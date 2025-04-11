# Client SDK Reference

The ChronoWeave Client SDK provides a simple interface for interacting with the ChronoWeave API. It abstracts away the details of the HTTP requests and provides a convenient way to use ChronoWeave's functionality.

## Table of Contents

- [ChronoWeaveClient Class](#chronoweaveclient-class)
- [Observation Operations](#observation-operations)
- [Recall Operations](#recall-operations)
- [Infusion Operations](#infusion-operations)
- [Consent Operations](#consent-operations)
- [Node Operations](#node-operations)
- [Examples](#examples)

## ChronoWeaveClient Class

### Initialization

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client with API key
client = ChronoWeaveClient(
    api_key="your_api_key",
    base_url="https://api.chronoweave.ai"
)
```

### Constructor Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| api_key | str | ChronoWeave API key |
| base_url | str | API base URL (default: "https://api.chronoweave.ai") |

## Observation Operations

### Observe

Store an observation in ChronoWeave.

```python
# Store an observation
result = client.observe({
    "node_type": "Observation",
    "content": {
        "observation_type": "CustomerFeedback",
        "text": "Customer reported issues with account access",
        "subject_id": "customer_123",
        "observer_id": "agent_456"
    },
    "temporal": {
        "start_time": "2023-06-15T14:30:00Z"
    },
    "metadata": {
        "importance": 0.8,
        "source": "support_ticket"
    }
})
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| data | Dict[str, Any] | Observation data |

#### Returns

| Type | Description |
|------|-------------|
| Dict[str, Any] | Created node data |

## Recall Operations

### Recall

Recall information from ChronoWeave.

```python
# Recall information
results = client.recall({
    "text": "account access issues",
    "reference_time": "2023-06-15T14:30:00Z",
    "entity_ids": ["customer_123"],
    "k": 5,
    "min_score": 0.5
})
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| query | Dict[str, Any] | Query parameters |

#### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| text | str | Text query for semantic search |
| reference_time | str | Reference time for temporal relevance |
| entity_ids | List[str] | Optional entity IDs to filter by |
| k | int | Maximum number of results (default: 10) |
| min_score | float | Minimum relevance score (default: 0.5) |
| filters | Dict[str, Any] | Optional filters |

#### Returns

| Type | Description |
|------|-------------|
| Dict[str, Any] | Query results |

### Recall by ID

Recall a specific node by ID.

```python
# Recall node by ID
node = client.recall_by_id("node_123")
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID to retrieve |

#### Returns

| Type | Description |
|------|-------------|
| Dict[str, Any] | Node data or None if not found |

## Infusion Operations

### Infuse Prompt

Infuse a prompt with time-aware context.

```python
# Infuse a prompt
infused_prompt = client.infuse_prompt(
    prompt="What issues has the customer reported?",
    query={
        "text": "customer issues",
        "reference_time": "2023-06-15T14:30:00Z",
        "entity_ids": ["customer_123"]
    },
    user_id="agent_789",
    model_type="openai"
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| prompt | str | The original prompt |
| query | Dict[str, Any] | Optional query parameters |
| user_id | str | Optional user ID for personalization |
| reference_time | str | Optional reference time |
| model_type | str | Model type for formatting (default: "default") |

#### Returns

| Type | Description |
|------|-------------|
| str | The infused prompt |

### Infuse System Prompt

Infuse a system prompt with user-specific context.

```python
# Infuse a system prompt
infused_system_prompt = client.infuse_system_prompt(
    system_prompt="You are a helpful assistant.",
    user_id="user_123",
    reference_time="2023-06-15T14:30:00Z",
    model_type="anthropic"
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| system_prompt | str | The original system prompt |
| user_id | str | User ID for personalization |
| reference_time | str | Optional reference time |
| model_type | str | Model type for formatting (default: "default") |

#### Returns

| Type | Description |
|------|-------------|
| str | The infused system prompt |

## Consent Operations

### Register Consent

Register user consent for data usage.

```python
# Register consent
consent_id = client.register_consent(
    user_id="user_123",
    data_types=["Entity", "Observation", "Event"],
    purposes=["storage", "retrieval", "context_infusion"],
    expiration_days=365
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| user_id | str | User ID |
| data_types | List[str] | Data types to consent for |
| purposes | List[str] | Purposes to consent for |
| expiration_days | int | Consent expiration in days |

#### Returns

| Type | Description |
|------|-------------|
| str | Consent ID |

### Check Consent

Check if user has given consent for a specific purpose.

```python
# Check consent
has_consent = client.check_consent(
    user_id="user_123",
    data_type="Observation",
    purpose="context_infusion"
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| user_id | str | User ID |
| data_type | str | Data type to check |
| purpose | str | Purpose to check |

#### Returns

| Type | Description |
|------|-------------|
| bool | True if consent exists, False otherwise |

### Revoke Consent

Revoke user consent.

```python
# Revoke consent
success = client.revoke_consent(
    user_id="user_123",
    data_types=["Observation"],
    purposes=["context_infusion"]
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| user_id | str | User ID |
| data_types | List[str] | Data types to revoke |
| purposes | List[str] | Purposes to revoke |

#### Returns

| Type | Description |
|------|-------------|
| bool | True if successful, False otherwise |

## Node Operations

### Get Node

Get a node by ID.

```python
# Get node
node = client.get_node("node_123")
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID |

#### Returns

| Type | Description |
|------|-------------|
| Dict[str, Any] | Node data |

### Update Node

Update a node.

```python
# Update node
updated_node = client.update_node(
    node_id="node_123",
    updates={
        "metadata": {
            "importance": 0.9,
            "verified": True
        }
    }
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID |
| updates | Dict[str, Any] | Fields to update |

#### Returns

| Type | Description |
|------|-------------|
| Dict[str, Any] | Updated node data |

### Delete Node

Delete a node.

```python
# Delete node
success = client.delete_node("node_123")
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID |

#### Returns

| Type | Description |
|------|-------------|
| bool | True if successful, False otherwise |

## Examples

### Customer Support Example

```python
from chronoweave.client import ChronoWeaveClient
from datetime import datetime

# Initialize client
client = ChronoWeaveClient(api_key="your_api_key")

# Register user consent
client.register_consent(
    user_id="customer_123",
    data_types=["Entity", "Observation", "Event"],
    purposes=["storage", "retrieval", "context_infusion"],
    expiration_days=365
)

# Store customer entity
client.observe({
    "node_type": "Entity",
    "content": {
        "entity_id": "customer_123",
        "entity_type": "Customer",
        "name": "John Doe",
        "attributes": {
            "email": "john@example.com",
            "plan": "premium"
        }
    }
})

# Store support interaction
client.observe({
    "node_type": "Observation",
    "content": {
        "observation_type": "SupportInteraction",
        "text": "Customer reported issues with account access",
        "subject_id": "customer_123",
        "observer_id": "agent_456"
    },
    "temporal": {
        "start_time": datetime.now().isoformat()
    },
    "metadata": {
        "importance": 0.8,
        "source": "support_ticket",
        "ticket_id": "T-12345"
    }
})

# Recall customer information
results = client.recall({
    "text": "account access issues",
    "reference_time": datetime.now().isoformat(),
    "entity_ids": ["customer_123"]
})

# Process results
print(f"Found {len(results['results'])} relevant items:")
for result in results["results"]:
    node = result["node"]
    score = result["score"]
    
    print(f"Node Type: {node['node_type']}")
    if node["node_type"] == "Observation":
        print(f"Text: {node['content']['text']}")
    print(f"Score: {score}")
    print()

# Infuse prompt for support agent
prompt = "How can I help the customer with their current issue?"
infused_prompt = client.infuse_prompt(
    prompt=prompt,
    query={
        "text": "customer issues",
        "entity_ids": ["customer_123"]
    },
    model_type="openai"
)

print("Infused Prompt:")
print(infused_prompt)
```

### Personal Memory Example

```python
from chronoweave.client import ChronoWeaveClient
from datetime import datetime

# Initialize client
client = ChronoWeaveClient(api_key="your_api_key")

# Register user
user_id = "user_123"
client.register_consent(
    user_id=user_id,
    data_types=["Entity", "Observation", "Event"],
    purposes=["storage", "retrieval", "context_infusion"],
    expiration_days=365
)

# Store a memory
memory = client.observe({
    "node_type": "Observation",
    "content": {
        "observation_type": "Memory",
        "text": "Had lunch with Sarah at the Italian restaurant downtown. She mentioned her new job at Google.",
        "subject_id": user_id,
        "observer_id": user_id,
        "category": "experience"
    },
    "temporal": {
        "start_time": datetime.now().isoformat()
    },
    "metadata": {
        "importance": 0.7,
        "location": "Downtown Italian Restaurant",
        "tags": ["lunch", "Sarah", "meeting", "food"],
        "is_private": False
    }
})

# Recall memory about Sarah
results = client.recall({
    "text": "Sarah job",
    "reference_time": datetime.now().isoformat(),
    "entity_ids": [user_id]
})

# Process results
print("Memories about Sarah:")
for result in results["results"]:
    node = result["node"]
    if node["node_type"] == "Observation" and node["content"]["observation_type"] == "Memory":
        print(f"- {node['content']['text']}")
        print(f"  Date: {node['temporal']['start_time']}")
        print(f"  Score: {result['score']}")
        print()

# Ask a question about the memory
prompt = "What did Sarah tell me about her job?"
infused_prompt = client.infuse_prompt(
    prompt=prompt,
    user_id=user_id
)

print("Infused Prompt:")
print(infused_prompt)
```
