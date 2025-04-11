# Infuser API Reference

The ChronoWeave Infuser is responsible for enhancing prompts with time-aware context. It retrieves relevant information from the TLKG and formats it for different LLM providers.

## Table of Contents

- [ContextInfuser Class](#contextinfuser-class)
- [Prompt Infusion](#prompt-infusion)
- [System Prompt Infusion](#system-prompt-infusion)
- [Context Formatting](#context-formatting)
- [Model Adapters](#model-adapters)
- [Token Management](#token-management)
- [Examples](#examples)

## ContextInfuser Class

### Initialization

```python
from chronoweave.core.retriever import ChronoSyncRetriever
from chronoweave.core.infuser import ContextInfuser

# Initialize infuser with retriever
infuser = ContextInfuser(
    retriever=retriever,
    max_context_length=2000
)
```

### Constructor Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| retriever | ChronoSyncRetriever | Retriever instance |
| max_context_length | int | Maximum context length in characters (default: 2000) |

## Prompt Infusion

### Infuse Prompt

Infuse a prompt with time-aware context.

```python
# Infuse a prompt
infused_prompt = infuser.infuse_prompt(
    prompt="What issues has the customer reported?",
    query={
        "text": "customer issues",
        "reference_time": "2023-06-15T14:30:00Z",
        "entity_ids": ["customer_123"]
    },
    k=5,
    model_type="openai"
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| prompt | str | The original prompt |
| query | Dict[str, Any] | Query parameters for retrieval |
| user_id | str | Optional user ID for personalization |
| reference_time | Union[str, datetime] | Optional reference time |
| k | int | Maximum number of context items (default: 5) |
| model_type | str | Model type for formatting (default: "default") |

#### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| text | str | Text query for semantic search |
| reference_time | str | Reference time for temporal relevance |
| entity_ids | List[str] | Optional entity IDs to filter by |

#### Returns

| Type | Description |
|------|-------------|
| str | The infused prompt |

## System Prompt Infusion

### Infuse System Prompt

Infuse a system prompt with user-specific context.

```python
# Infuse a system prompt
infused_system_prompt = infuser.infuse_system_prompt(
    system_prompt="You are a helpful assistant.",
    user_id="user_123",
    reference_time="2023-06-15T14:30:00Z",
    k=3,
    model_type="anthropic"
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| system_prompt | str | The original system prompt |
| user_id | str | User ID for personalization |
| reference_time | Union[str, datetime] | Optional reference time |
| k | int | Maximum number of context items (default: 3) |
| model_type | str | Model type for formatting (default: "default") |

#### Returns

| Type | Description |
|------|-------------|
| str | The infused system prompt |

## Context Formatting

### Format Context

Format retrieved nodes into a context string.

```python
# Format context
context_str = infuser._format_context(
    results=retrieval_results,
    max_length=1500
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| results | List[Dict[str, Any]] | Retrieval results |
| max_length | int | Maximum length of context string |

#### Returns

| Type | Description |
|------|-------------|
| str | Formatted context string |

## Model Adapters

The ContextInfuser includes adapters for different LLM providers:

### OpenAI Adapter

```python
# Format for OpenAI
openai_prompt = infuser._format_openai(
    prompt="What issues has the customer reported?",
    context="Customer reported issues with account access on 2023-06-15."
)
```

### Anthropic Adapter

```python
from chronoweave.core.adapters import AnthropicAdapter

# Format for Anthropic
anthropic_prompt = AnthropicAdapter.infuse_prompt(
    prompt="What issues has the customer reported?",
    context="Customer reported issues with account access on 2023-06-15."
)
```

### Llama Adapter

```python
from chronoweave.core.adapters import LlamaAdapter

# Format for Llama
llama_prompt = LlamaAdapter.infuse_prompt(
    prompt="What issues has the customer reported?",
    context="Customer reported issues with account access on 2023-06-15."
)
```

## Token Management

The ContextInfuser includes adaptive context length management based on token limits:

### Token Estimation

```python
# Estimate tokens
token_count = infuser._estimate_tokens("This is a test prompt.")
```

### Available Tokens Calculation

```python
# Calculate available tokens
available_tokens = infuser._get_available_tokens(
    prompt="What issues has the customer reported?",
    model_type="openai-gpt4"
)
```

### Model Token Limits

The infuser includes predefined token limits for different models:

| Model | Token Limit |
|-------|-------------|
| openai-gpt3 | 4096 |
| openai-gpt4 | 8192 |
| anthropic-claude | 100000 |
| llama-7b | 2048 |
| llama-13b | 4096 |
| default | 4096 |

## Examples

### Basic Prompt Infusion

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.core.retriever import ChronoSyncRetriever
from chronoweave.core.infuser import ContextInfuser
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore
from datetime import datetime

# Initialize components
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")
tlkg = TLKG(database, vector_store)
retriever = ChronoSyncRetriever(tlkg, vector_store)
infuser = ContextInfuser(retriever)

# Create a prompt
prompt = "What issues has the customer reported recently?"

# Create query
query = {
    "text": "customer issues",
    "reference_time": datetime.now().isoformat(),
    "entity_ids": ["customer_123"]
}

# Infuse prompt
infused_prompt = infuser.infuse_prompt(
    prompt=prompt,
    query=query,
    k=5,
    model_type="openai"
)

print("Original Prompt:")
print(prompt)
print("\nInfused Prompt:")
print(infused_prompt)
```

### User-Specific System Prompt

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.core.retriever import ChronoSyncRetriever
from chronoweave.core.infuser import ContextInfuser
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore
from datetime import datetime

# Initialize components
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")
tlkg = TLKG(database, vector_store)
retriever = ChronoSyncRetriever(tlkg, vector_store)
infuser = ContextInfuser(retriever)

# Create a system prompt
system_prompt = "You are a helpful assistant that provides personalized support."

# Infuse system prompt
infused_system_prompt = infuser.infuse_system_prompt(
    system_prompt=system_prompt,
    user_id="user_123",
    reference_time=datetime.now().isoformat(),
    k=3,
    model_type="anthropic"
)

print("Original System Prompt:")
print(system_prompt)
print("\nInfused System Prompt:")
print(infused_system_prompt)
```

### Adaptive Context Length

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.core.retriever import ChronoSyncRetriever
from chronoweave.core.infuser import ContextInfuser
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore
from datetime import datetime

# Initialize components
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")
tlkg = TLKG(database, vector_store)
retriever = ChronoSyncRetriever(tlkg, vector_store)
infuser = ContextInfuser(retriever)

# Create a short prompt
short_prompt = "What issues has the customer reported?"

# Create a long prompt
long_prompt = "I need a detailed analysis of all customer issues reported in the last six months, including the frequency of each issue type, the average resolution time, the customer satisfaction scores for each issue category, and any patterns or trends that might indicate systemic problems that need to be addressed at a product or service level."

# Create query
query = {
    "text": "customer issues",
    "reference_time": datetime.now().isoformat(),
    "entity_ids": ["customer_123"]
}

# Infuse both prompts
short_infused = infuser.infuse_prompt(
    prompt=short_prompt,
    query=query,
    model_type="openai-gpt4"
)

long_infused = infuser.infuse_prompt(
    prompt=long_prompt,
    query=query,
    model_type="openai-gpt4"
)

# Compare token usage
short_tokens = infuser._estimate_tokens(short_infused)
long_tokens = infuser._estimate_tokens(long_infused)

print(f"Short prompt tokens: {short_tokens}")
print(f"Long prompt tokens: {long_tokens}")
```
