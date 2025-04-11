# TLKG API Reference

The Time-Layered Knowledge Graph (TLKG) is the core component of ChronoWeave. It provides functionality for storing, retrieving, and managing temporal information.

## Table of Contents

- [TLKG Class](#tlkg-class)
- [Node Operations](#node-operations)
- [Relationship Operations](#relationship-operations)
- [Temporal Operations](#temporal-operations)
- [Query Operations](#query-operations)
- [Schema Utilities](#schema-utilities)
- [Examples](#examples)

## TLKG Class

### Initialization

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore

# Initialize storage components
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")

# Initialize TLKG
tlkg = TLKG(database, vector_store)
```

### Constructor Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| database | Database | Database instance for storing graph structure |
| vector_store | VectorStore | Vector store instance for semantic search |
| config | Dict[str, Any] | Optional configuration parameters |

## Node Operations

### Add Node

Add a node to the TLKG.

```python
from chronoweave.core.schema import create_entity_node

# Create an entity node
entity = create_entity_node(
    entity_id="user_123",
    entity_type="Person",
    name="John Doe",
    attributes={"email": "john@example.com"}
)

# Add to TLKG
node_id = tlkg.add_node(entity)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node | Dict[str, Any] | Node data conforming to ChronoNode schema |

#### Returns

| Type | Description |
|------|-------------|
| str | Unique node ID |

### Get Node

Retrieve a node by ID.

```python
# Get node by ID
node = tlkg.get_node("node_123")
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID to retrieve |

#### Returns

| Type | Description |
|------|-------------|
| Dict[str, Any] | Node data or None if not found |

### Update Node

Update an existing node.

```python
# Update node
tlkg.update_node(
    node_id="node_123",
    updates={
        "content": {"name": "John Smith"},
        "metadata": {"importance": 0.8}
    }
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID to update |
| updates | Dict[str, Any] | Fields to update |

#### Returns

| Type | Description |
|------|-------------|
| bool | True if successful, False otherwise |

### Delete Node

Delete a node and its relationships.

```python
# Delete node
tlkg.delete_node("node_123")
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID to delete |

#### Returns

| Type | Description |
|------|-------------|
| bool | True if successful, False otherwise |

## Relationship Operations

### Add Relationship

Add a relationship between nodes.

```python
from chronoweave.core.schema import Relationship, RelationshipType

# Create relationship
relationship = Relationship(
    type=RelationshipType.KNOWS,
    source_id="node_123",
    target_id="node_456"
)

# Add to TLKG
relationship_id = tlkg.add_relationship(relationship)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| relationship | Relationship | Relationship object |

#### Returns

| Type | Description |
|------|-------------|
| str | Unique relationship ID |

### Get Relationship

Retrieve a relationship by ID.

```python
# Get relationship
relationship = tlkg.get_relationship("rel_123")
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| relationship_id | str | Relationship ID |

#### Returns

| Type | Description |
|------|-------------|
| Dict[str, Any] | Relationship data or None if not found |

### Delete Relationship

Delete a relationship.

```python
# Delete relationship
tlkg.delete_relationship("rel_123")
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| relationship_id | str | Relationship ID |

#### Returns

| Type | Description |
|------|-------------|
| bool | True if successful, False otherwise |

## Temporal Operations

### Get Temporal Context

Get nodes within a temporal window around a reference time.

```python
from datetime import datetime

# Get temporal context
context = tlkg.get_temporal_context(
    reference_time=datetime.now(),
    window_size=7  # 7 days
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| reference_time | Union[str, datetime] | Reference time |
| window_size | int | Window size in days |

#### Returns

| Type | Description |
|------|-------------|
| List[Dict[str, Any]] | List of nodes in temporal context |

### Get Temporal Distance

Calculate temporal distance between two times.

```python
from chronoweave.core.time import time_distance

# Calculate distance
distance = time_distance(
    time1="2023-06-01T12:00:00Z",
    time2="2023-06-05T12:00:00Z"
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| time1 | Union[str, datetime] | First time |
| time2 | Union[str, datetime] | Second time |

#### Returns

| Type | Description |
|------|-------------|
| float | Distance in days |

## Query Operations

### Query Nodes

Query nodes based on text, temporal relevance, and filters.

```python
# Query nodes
results = tlkg.query_nodes(
    query_text="account access issues",
    reference_time="2023-06-15T14:30:00Z",
    filters={"node_types": ["Observation"]},
    limit=10
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| query_text | str | Text query |
| reference_time | Union[str, datetime] | Reference time for temporal relevance |
| filters | Dict[str, Any] | Optional filters |
| limit | int | Maximum number of results |

#### Returns

| Type | Description |
|------|-------------|
| List[Dict[str, Any]] | Matching nodes with scores |

### Get Related Nodes

Get nodes related to a specific node.

```python
# Get related nodes
related = tlkg.get_related_nodes(
    node_id="node_123",
    relationship_types=["KNOWS", "PARTICIPATES_IN"],
    direction="outgoing",
    limit=10
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID |
| relationship_types | List[str] | Optional relationship types to filter |
| direction | str | "incoming", "outgoing", or "both" |
| limit | int | Maximum number of results |

#### Returns

| Type | Description |
|------|-------------|
| List[Dict[str, Any]] | Related nodes with relationship info |

## Schema Utilities

ChronoWeave provides utilities for creating nodes that conform to the schema.

### Create Entity Node

```python
from chronoweave.core.schema import create_entity_node

entity = create_entity_node(
    entity_id="user_123",
    entity_type="Person",
    name="John Doe",
    attributes={"email": "john@example.com"}
)
```

### Create Event Node

```python
from chronoweave.core.schema import create_event_node

event = create_event_node(
    event_type="Login",
    title="User Login",
    description="User logged into the system",
    participants=[{"entity_id": "user_123", "role": "user"}],
    start_time="2023-06-15T14:30:00Z"
)
```

### Create Observation Node

```python
from chronoweave.core.schema import create_observation_node

observation = create_observation_node(
    observer_id="agent_456",
    observation_type="CustomerFeedback",
    text="Customer reported issues with account access",
    subject_id="user_123",
    start_time="2023-06-15T14:30:00Z"
)
```

### Create Pattern Node

```python
from chronoweave.core.schema import create_pattern_node

pattern = create_pattern_node(
    pattern_type="BehavioralPattern",
    description="User typically logs in during evening hours",
    evidence_ids=["event_123", "event_456"],
    confidence=0.75
)
```

## Examples

### Building a User Knowledge Graph

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.core.schema import (
    create_entity_node,
    create_event_node,
    create_observation_node,
    Relationship,
    RelationshipType
)
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore
from datetime import datetime

# Initialize TLKG
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")
tlkg = TLKG(database, vector_store)

# Create user entity
user = create_entity_node(
    entity_id="user_123",
    entity_type="Person",
    name="John Doe",
    attributes={"email": "john@example.com"}
)
user_id = tlkg.add_node(user)

# Create product entity
product = create_entity_node(
    entity_id="product_456",
    entity_type="Product",
    name="Premium Subscription",
    attributes={"price": 99.99}
)
product_id = tlkg.add_node(product)

# Create purchase event
purchase = create_event_node(
    event_type="Purchase",
    title="Subscription Purchase",
    description="User purchased premium subscription",
    participants=[
        {"entity_id": "user_123", "role": "buyer"},
        {"entity_id": "product_456", "role": "purchased_item"}
    ],
    start_time=datetime.now().isoformat()
)
purchase_id = tlkg.add_node(purchase)

# Create support observation
support = create_observation_node(
    observer_id="agent_789",
    observation_type="SupportInteraction",
    text="User reported issues with account access",
    subject_id="user_123",
    start_time=datetime.now().isoformat()
)
support_id = tlkg.add_node(support)

# Add relationships
tlkg.add_relationship(Relationship(
    type=RelationshipType.PARTICIPATES_IN,
    source_id=user_id,
    target_id=purchase_id
))

tlkg.add_relationship(Relationship(
    type=RelationshipType.REFERS_TO,
    source_id=support_id,
    target_id=user_id
))

# Query for user context
results = tlkg.query_nodes(
    query_text="account issues",
    reference_time=datetime.now(),
    filters={"entity_ids": ["user_123"]}
)

for result in results:
    print(f"Node: {result['node']['node_type']}")
    print(f"Content: {result['node']['content']}")
    print(f"Score: {result['score']}")
    print()
```

### Temporal Analysis

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore
from datetime import datetime, timedelta

# Initialize TLKG
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")
tlkg = TLKG(database, vector_store)

# Get temporal context for different time periods
now = datetime.now()
yesterday = now - timedelta(days=1)
last_week = now - timedelta(days=7)
last_month = now - timedelta(days=30)

# Get context for each time period
context_now = tlkg.get_temporal_context(now, window_size=1)
context_yesterday = tlkg.get_temporal_context(yesterday, window_size=1)
context_last_week = tlkg.get_temporal_context(last_week, window_size=1)
context_last_month = tlkg.get_temporal_context(last_month, window_size=1)

# Analyze temporal distribution
print(f"Events today: {len(context_now)}")
print(f"Events yesterday: {len(context_yesterday)}")
print(f"Events last week: {len(context_last_week)}")
print(f"Events last month: {len(context_last_month)}")
```
