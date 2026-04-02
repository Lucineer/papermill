> *Experiment: DeepSeek Chat*

# ModelRouter: BYOK-Aware Model Routing for Repository Agents

## Core Architecture

The ModelRouter is a middleware component that sits between the repository agent's task dispatcher and the LLM provider interface. It makes intelligent routing decisions based on task characteristics, user preferences, and performance metrics.

```python
from dataclasses import dataclass
from enum import Enum
from typing import Dict, List, Optional, Tuple, Any
from datetime import datetime
import hashlib

class TaskType(Enum):
    CREATIVE_WRITING = "creative_writing"
    CODE_GENERATION = "code_generation"
    CODE_REVIEW = "code_review"
    RESEARCH = "research"
    IMAGE_GENERATION = "image_generation"
    DATA_ANALYSIS = "data_analysis"
    DOCUMENTATION = "documentation"
    GENERAL = "general"

class Provider(Enum):
    OPENAI = "openai"
    ANTHROPIC = "anthropic"
    GOOGLE = "google"
    DEEPSEEK = "deepseek"
    MISTRAL = "mistral"
    COHERE = "cohere"

@dataclass
class ModelConfig:
    """BYOK configuration for a specific provider"""
    provider: Provider
    model_name: str
    api_key: str
    endpoint: Optional[str] = None
    max_tokens: int = 4096
    temperature: float = 0.7
    cost_per_1k_tokens: Dict[str, float] = None  # {"input": 0.001, "output": 0.002}
    
    def __post_init__(self):
        if self.cost_per_1k_tokens is None:
            self.cost_per_1k_tokens = {"input": 0.001, "output": 0.002}

@dataclass
class RouteRule:
    """Defines routing for a specific task type"""
    task_type: TaskType
    primary: ModelConfig
    fallback_chain: List[ModelConfig]
    min_confidence: float = 0.8  # Confidence threshold to use primary
    weight: float = 1.0  # For A/B testing
    
    def get_route(self, confidence: float) -> List[ModelConfig]:
        """Returns ordered list of models to try"""
        if confidence >= self.min_confidence:
            return [self.primary] + self.fallback_chain
        return self.fallback_chain + [self.primary]

@dataclass
class RoutingTable:
    """User-configured routing preferences"""
    default_rules: Dict[TaskType, RouteRule]
    conversation_overrides: Dict[str, Dict[TaskType, ModelConfig]]  # conversation_id -> overrides
    user_preferences: Dict[Tuple[str, TaskType], float]  # (user_id, task_type) -> preference_score
    
    def get_rule(self, task_type: TaskType, conversation_id: Optional[str] = None) -> RouteRule:
        """Get routing rule with conversation-specific overrides"""
        if conversation_id and conversation_id in self.conversation_overrides:
            overrides = self.conversation_overrides[conversation_id]
            if task_type in overrides:
                # Create temporary route rule with override as primary
                base_rule = self.default_rules.get(task_type, self.default_rules[TaskType.GENERAL])
                return RouteRule(
                    task_type=task_type,
                    primary=overrides[task_type],
                    fallback_chain=base_rule.fallback_chain
                )
        return self.default_rules.get(task_type, self.default_rules[TaskType.GENERAL])
```

## Intelligent Task Classification

The router must first classify the task type before routing:

```python
class TaskClassifier:
    def __init__(self):
        self.keyword_patterns = {
            TaskType.CREATIVE_WRITING: ["story", "poem", "creative", "narrative", "imagine"],
            TaskType.CODE_GENERATION: ["implement", "function", "class", "algorithm", "write code"],
            TaskType.CODE_REVIEW: ["review", "refactor", "optimize", "bug", "vulnerability"],
            TaskType.RESEARCH: ["research", "summarize", "explain", "compare", "analysis"],
            TaskType.IMAGE_GENERATION: ["image", "generate", "draw", "visualize", "picture"],
            TaskType.DATA_ANALYSIS: ["analyze", "data", "statistics", "trend", "pattern"],
            TaskType.DOCUMENTATION: ["document", "comment", "explain", "tutorial", "guide"]
        }
        
    def classify(self, prompt: str, context: Optional[str] = None) -> Tuple[TaskType, float]:
        """Classify task type with confidence score"""
        prompt_lower = prompt.lower()
        scores = {}
        
        for task_type, keywords in self.keyword_patterns.items():
            score = sum(1 for kw in keywords if kw in prompt_lower)
            scores[task_type] = score / len(keywords)
        
        # Use ML model for complex classification (simplified here)
        if context and "code" in context.lower():
            scores[TaskType.CODE_GENERATION] *= 1.5
            scores[TaskType.CODE_REVIEW] *= 1.3
            
        # Return best match
        best_type = max(scores.items(), key=lambda x: x[1])
        return best_type[0], min(best_type[1], 1.0)
```

## ModelRouter Implementation

```python
class ModelRouter:
    def __init__(self, routing_table: RoutingTable):
        self.routing_table = routing_table
        self.task_classifier = TaskClassifier()
        self.performance_tracker = PerformanceTracker()
        self.cost_tracker = CostTracker()
        
    async def route(
        self,
        prompt: str,
        context: Optional[str] = None,
        conversation_id: Optional[str] = None,
        user_id: Optional[str] = None,
        force_model: Optional[ModelConfig] = None,
        enable_ab_testing: bool = False
    ) -> Dict[str, Any]:
        """
        Main routing method
        Returns: {
            'selected_model': ModelConfig,
            'task_type': TaskType,
            'confidence': float,
            'ab_test_id': Optional[str],
            'estimated_cost': float
        }
        """
        # 1. Classify task
        task_type, confidence = self.task_classifier.classify(prompt, context)
        
        # 2. Get routing rule
        if force_model:
            route_rule = RouteRule(
                task_type=task_type,
                primary=force_model,
                fallback_chain=[]
            )
        else:
            route_rule = self.routing_table.get_rule(task_type, conversation_id)
        
        # 3. Apply user preferences if available
        if user_id:
            pref_key = (user_id, task_type)
            if pref_key in self.routing_table.user_preferences:
                # Adjust confidence based on historical user preference
                user_pref = self.routing_table.user_preferences[pref_key]
                confidence = (confidence + user_pref) / 2
        
        # 4. Get model sequence to try
        models_to_try = route_rule.get_route(confidence)
        
        # 5. Check performance history
        models_to_try = self.performance_tracker.sort_by_performance(
            models_to_try, task_type
        )
        
        # 6. Estimate cost
        estimated_tokens = self._estimate_tokens(prompt, context)
        estimated_cost = self.cost_tracker.estimate_cost(
            models_to_try[0], estimated_tokens
        )
        
        # 7. Handle A/B testing if enabled
        ab_test_id = None
        if enable_ab_testing and len(models_to_try) >= 2:
            ab_test_id = self._setup_ab_test(models_to_try[:2], prompt, task_type)
        
        return {
            'selected_model': models_to_try[0],
            'task_type': task_type,
            'confidence': confidence,
            'ab_test_id': ab_test_id,
            'estimated_cost': estimated_cost,
            'fallback_chain': models_to_try[1:]
        }
    
    def _estimate_tokens(self, prompt: str, context: Optional[str]) -> Dict[str, int]:
        """Simple token estimation (replace with actual tokenizer)"""
        total_text = prompt + (context or "")
        return {
            'input': len(total_text) // 4,  # Rough approximation
            'output': 500  # Default estimate
        }
    
    def _setup_ab_test(self, models: List[ModelConfig], prompt: str, task_type: TaskType) -> str:
        """Create A/B test session"""
        test_id = hashlib.md5(f"{datetime.now().isoformat()}{prompt}".encode()).hexdigest()[:8]
        # Store test configuration for later comparison
        return test_id
```

## Performance Tracking and Learning

```python
@dataclass
class PerformanceMetrics:
    success_count: int = 0
    failure_count: