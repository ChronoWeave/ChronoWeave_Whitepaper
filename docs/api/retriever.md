# Retriever API Reference

The ChronoWeave Retriever is responsible for retrieving relevant time-anchored information from the TLKG based on queries. It combines semantic search with temporal relevance to provide context-aware results.

## Table of Contents

- [ChronoSyncRetriever Class](#chronosyncretriever-class)
- [Retrieval Operations](#retrieval-operations)
- [Filtering and Ranking](#filtering-and-ranking)
- [Caching](#caching)
- [Examples](#examples)

## ChronoSyncRetriever Class

### Initialization

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.core.retriever import ChronoSyncRetriever
from chronoweave.storage.vector_store import VectorStore

# Initialize TLKG and vector store
tlkg = TLKG(database, vector_store)

# Initialize retriever
retriever = ChronoSyncRetriever(tlkg, vector_store, cache_size=1000)
```

### Constructor Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| tlkg | TLKG | TLKG instance |
| vector_store | VectorStore | Vector store instance |
| cache_size | int | Maximum cache size (default: 1000) |

## Retrieval Operations

### Retrieve

Retrieve relevant ChronoNodes based on a query.

```python
# Create query
query = {
    "text": "account access issues",
    "reference_time": "2023-06-15T14:30:00Z",
    "entity_ids": ["user_123"]
}

# Retrieve results
results = retriever.retrieve(
    query=query,
    k=10,
    min_score=0.5,
    filters={"node_types": ["Observation"]}
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| query | Dict[str, Any] | Query parameters |
| k | int | Maximum number of results (default: 10) |
| min_score | float | Minimum relevance score (default: 0.5) |
| filters | Dict[str, Any] | Optional filters |

#### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| text | str | Text query for semantic search |
| reference_time | str | Reference time for temporal relevance |
| entity_ids | List[str] | Optional entity IDs to filter by |

#### Returns

| Type | Description |
|------|-------------|
| List[Dict[str, Any]] | List of relevant nodes with scores |

### Retrieve by ID

Retrieve a specific node by ID.

```python
# Retrieve node by ID
node = retriever.retrieve_by_id("node_123")
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| node_id | str | Node ID to retrieve |

#### Returns

| Type | Description |
|------|-------------|
| Dict[str, Any] | Node data or None if not found |

### Retrieve by User

Retrieve nodes associated with a user.

```python
# Retrieve user-related nodes
nodes = retriever.retrieve_by_user(
    user_id="user_123",
    k=10,
    include_relationships=True
)
```

#### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| user_id | str | User ID |
| k | int | Maximum number of results (default: 10) |
| include_relationships | bool | Whether to include relationships (default: True) |

#### Returns

| Type | Description |
|------|-------------|
| List[Dict[str, Any]] | List of nodes related to the user |

## Filtering and Ranking

### Filtering Options

The retriever supports various filtering options:

```python
# Filter by node types
filters = {
    "node_types": ["Entity", "Observation"]
}

# Filter by time window
filters = {
    "time_window": {
        "start": "2023-06-01T00:00:00Z",
        "end": "2023-06-30T23:59:59Z"
    }
}

# Filter by metadata
filters = {
    "metadata": {
        "importance": {"min": 0.7}
    }
}

# Combine filters
filters = {
    "node_types": ["Observation"],
    "time_window": {
        "start": "2023-06-01T00:00:00Z",
        "end": "2023-06-30T23:59:59Z"
    },
    "metadata": {
        "importance": {"min": 0.7}
    }
}
```

### Scoring Components

The retriever uses multiple scoring components to rank results:

1. **Semantic Score**: Relevance based on semantic similarity to the query text
2. **Temporal Score**: Relevance based on temporal proximity to the reference time
3. **Entity Score**: Relevance based on connection to specified entities
4. **Combined Score**: Weighted combination of the above scores

## Caching

The ChronoSyncRetriever includes caching to improve performance for repeated queries.

### Cache Management

```python
# Clear the cache
retriever.clear_cache()
```

### Cache Configuration

The cache size can be configured during initialization:

```python
# Initialize with custom cache size
retriever = ChronoSyncRetriever(tlkg, vector_store, cache_size=2000)
```

## Examples

### Basic Retrieval

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.core.retriever import ChronoSyncRetriever
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore
from datetime import datetime

# Initialize components
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")
tlkg = TLKG(database, vector_store)
retriever = ChronoSyncRetriever(tlkg, vector_store)

# Create query
query = {
    "text": "account access issues",
    "reference_time": datetime.now().isoformat()
}

# Retrieve results
results = retriever.retrieve(query, k=5)

# Process results
for result in results:
    node = result["node"]
    score = result["score"]
    
    print(f"Node ID: {node['node_id']}")
    print(f"Node Type: {node['node_type']}")
    print(f"Content: {node['content']}")
    print(f"Score: {score}")
    print(f"Semantic Score: {result['semantic_score']}")
    print(f"Temporal Score: {result['temporal_score']}")
    print(f"Entity Score: {result['entity_score']}")
    print()
```

### User Context Retrieval

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.core.retriever import ChronoSyncRetriever
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore
from datetime import datetime

# Initialize components
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")
tlkg = TLKG(database, vector_store)
retriever = ChronoSyncRetriever(tlkg, vector_store)

# Get user entity
user_id = "user_123"
user_nodes = retriever.retrieve_by_user(user_id, k=1)
user_entity = None

for node in user_nodes:
    if node["node_type"] == "Entity" and node["content"]["entity_id"] == user_id:
        user_entity = node
        break

if user_entity:
    print(f"User: {user_entity['content']['name']}")
    
    # Get user-related observations
    query = {
        "text": "feedback",
        "reference_time": datetime.now().isoformat(),
        "entity_ids": [user_id]
    }
    
    results = retriever.retrieve(query, k=5)
    
    print(f"\nUser Feedback:")
    for result in results:
        node = result["node"]
        if node["node_type"] == "Observation":
            print(f"- {node['content']['text']}")
            print(f"  Date: {node['temporal']['start_time']}")
            print(f"  Score: {result['score']}")
            print()
```

### Time-Window Filtering

```python
from chronoweave.core.tlkg import TLKG
from chronoweave.core.retriever import ChronoSyncRetriever
from chronoweave.storage.database import Database
from chronoweave.storage.vector_store import VectorStore
from datetime import datetime, timedelta

# Initialize components
database = Database(database_url="sqlite:///chronoweave.db")
vector_store = VectorStore(persist_directory="./vector_store")
tlkg = TLKG(database, vector_store)
retriever = ChronoSyncRetriever(tlkg, vector_store)

# Define time window
now = datetime.now()
one_month_ago = now - timedelta(days=30)

# Create query with time window filter
query = {
    "text": "customer feedback",
    "reference_time": now.isoformat(),
    "entity_ids": ["user_123"]
}

filters = {
    "time_window": {
        "start": one_month_ago.isoformat(),
        "end": now.isoformat()
    },
    "node_types": ["Observation"]
}

# Retrieve results
results = retriever.retrieve(query, k=10, filters=filters)

# Process results
print(f"Feedback from the last 30 days:")
for result in results:
    node = result["node"]
    date = datetime.fromisoformat(node["temporal"]["start_time"].replace("Z", "+00:00"))
    print(f"- {date.strftime('%Y-%m-%d')}: {node['content']['text']}")
```
