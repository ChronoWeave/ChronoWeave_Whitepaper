# Core API

The Core API provides the fundamental operations for storing and retrieving temporal information in ChronoWeave.

## Nodes

Nodes are the basic units of information in ChronoWeave's Time-Layered Knowledge Graph (TLKG).

### Create a Node

```
POST /nodes
```

Create a new node in the TLKG.

#### Request

```json
{
  "user_id": "user_123",
  "node_type": "Observation",
  "content": {
    "observation_type": "CustomerPreference",
    "text": "Customer prefers email over phone calls",
    "observer_id": "agent_1"
  },
  "reference_time": "2023-07-01T12:00:00Z",
  "validity_period": {
    "start": "2023-07-01T00:00:00Z",
    "end": "2024-07-01T00:00:00Z"
  },
  "metadata": {
    "source": "customer_survey",
    "confidence": 0.9
  }
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "node_id": "node_abc123",
    "user_id": "user_123",
    "node_type": "Observation",
    "content": {
      "observation_type": "CustomerPreference",
      "text": "Customer prefers email over phone calls",
      "observer_id": "agent_1"
    },
    "reference_time": "2023-07-01T12:00:00Z",
    "creation_time": "2023-07-02T15:30:45Z",
    "modification_time": "2023-07-02T15:30:45Z",
    "validity_period": {
      "start": "2023-07-01T00:00:00Z",
      "end": "2024-07-01T00:00:00Z"
    },
    "metadata": {
      "source": "customer_survey",
      "confidence": 0.9
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Get a Node

```
GET /nodes/{node_id}
```

Retrieve a specific node by ID.

#### Parameters

- `reference_time` (optional): The point in time to retrieve the node. Defaults to the current time.

#### Response

```json
{
  "status": "success",
  "data": {
    "node_id": "node_abc123",
    "user_id": "user_123",
    "node_type": "Observation",
    "content": {
      "observation_type": "CustomerPreference",
      "text": "Customer prefers email over phone calls",
      "observer_id": "agent_1"
    },
    "reference_time": "2023-07-01T12:00:00Z",
    "creation_time": "2023-07-02T15:30:45Z",
    "modification_time": "2023-07-02T15:30:45Z",
    "validity_period": {
      "start": "2023-07-01T00:00:00Z",
      "end": "2024-07-01T00:00:00Z"
    },
    "metadata": {
      "source": "customer_survey",
      "confidence": 0.9
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Update a Node

```
PATCH /nodes/{node_id}
```

Update an existing node.

#### Request

```json
{
  "content": {
    "observation_type": "CustomerPreference",
    "text": "Customer prefers email over phone calls, but is open to SMS for urgent matters",
    "observer_id": "agent_1"
  },
  "metadata": {
    "confidence": 0.95
  }
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "node_id": "node_abc123",
    "user_id": "user_123",
    "node_type": "Observation",
    "content": {
      "observation_type": "CustomerPreference",
      "text": "Customer prefers email over phone calls, but is open to SMS for urgent matters",
      "observer_id": "agent_1"
    },
    "reference_time": "2023-07-01T12:00:00Z",
    "creation_time": "2023-07-02T15:30:45Z",
    "modification_time": "2023-07-03T09:15:22Z",
    "validity_period": {
      "start": "2023-07-01T00:00:00Z",
      "end": "2024-07-01T00:00:00Z"
    },
    "metadata": {
      "source": "customer_survey",
      "confidence": 0.95
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Delete a Node

```
DELETE /nodes/{node_id}
```

Delete a node.

#### Response

```json
{
  "status": "success",
  "data": {
    "deleted": true,
    "node_id": "node_abc123"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### List Nodes

```
GET /nodes
```

List nodes with optional filtering.

#### Parameters

- `user_id` (required): The user ID to filter nodes by
- `node_type` (optional): Filter by node type
- `reference_time` (optional): The point in time to retrieve nodes. Defaults to the current time.
- `start_time` (optional): Filter nodes with reference time after this time
- `end_time` (optional): Filter nodes with reference time before this time
- `cursor` (optional): Pagination cursor
- `limit` (optional): Number of nodes to return (default: 20, max: 100)

#### Response

```json
{
  "status": "success",
  "data": {
    "items": [
      {
        "node_id": "node_abc123",
        "user_id": "user_123",
        "node_type": "Observation",
        "content": {
          "observation_type": "CustomerPreference",
          "text": "Customer prefers email over phone calls",
          "observer_id": "agent_1"
        },
        "reference_time": "2023-07-01T12:00:00Z",
        "creation_time": "2023-07-02T15:30:45Z",
        "modification_time": "2023-07-02T15:30:45Z",
        "validity_period": {
          "start": "2023-07-01T00:00:00Z",
          "end": "2024-07-01T00:00:00Z"
        },
        "metadata": {
          "source": "customer_survey",
          "confidence": 0.9
        }
      },
      // More nodes...
    ],
    "pagination": {
      "next_cursor": "cursor_def456",
      "has_more": true
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Relationships

Relationships connect nodes in the TLKG.

### Create a Relationship

```
POST /relationships
```

Create a new relationship between nodes.

#### Request

```json
{
  "source_node_id": "node_abc123",
  "target_node_id": "node_def456",
  "relationship_type": "PRECEDES",
  "metadata": {
    "confidence": 0.9,
    "source": "automated_analysis"
  }
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "relationship_id": "rel_ghi789",
    "source_node_id": "node_abc123",
    "target_node_id": "node_def456",
    "relationship_type": "PRECEDES",
    "creation_time": "2023-07-02T15:35:12Z",
    "metadata": {
      "confidence": 0.9,
      "source": "automated_analysis"
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Get a Relationship

```
GET /relationships/{relationship_id}
```

Retrieve a specific relationship by ID.

#### Response

```json
{
  "status": "success",
  "data": {
    "relationship_id": "rel_ghi789",
    "source_node_id": "node_abc123",
    "target_node_id": "node_def456",
    "relationship_type": "PRECEDES",
    "creation_time": "2023-07-02T15:35:12Z",
    "metadata": {
      "confidence": 0.9,
      "source": "automated_analysis"
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Delete a Relationship

```
DELETE /relationships/{relationship_id}
```

Delete a relationship.

#### Response

```json
{
  "status": "success",
  "data": {
    "deleted": true,
    "relationship_id": "rel_ghi789"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### List Relationships

```
GET /relationships
```

List relationships with optional filtering.

#### Parameters

- `source_node_id` (optional): Filter by source node ID
- `target_node_id` (optional): Filter by target node ID
- `relationship_type` (optional): Filter by relationship type
- `cursor` (optional): Pagination cursor
- `limit` (optional): Number of relationships to return (default: 20, max: 100)

#### Response

```json
{
  "status": "success",
  "data": {
    "items": [
      {
        "relationship_id": "rel_ghi789",
        "source_node_id": "node_abc123",
        "target_node_id": "node_def456",
        "relationship_type": "PRECEDES",
        "creation_time": "2023-07-02T15:35:12Z",
        "metadata": {
          "confidence": 0.9,
          "source": "automated_analysis"
        }
      },
      // More relationships...
    ],
    "pagination": {
      "next_cursor": "cursor_jkl012",
      "has_more": true
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Observe

The Observe endpoint provides a simplified way to store observations in the TLKG.

```
POST /observe
```

Store an observation.

#### Request

```json
{
  "user_id": "user_123",
  "content": {
    "observation_type": "CustomerPreference",
    "text": "Customer prefers email over phone calls",
    "observer_id": "agent_1"
  },
  "reference_time": "2023-07-01T12:00:00Z",
  "metadata": {
    "source": "customer_survey",
    "confidence": 0.9
  }
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "node_id": "node_abc123",
    "user_id": "user_123",
    "node_type": "Observation",
    "content": {
      "observation_type": "CustomerPreference",
      "text": "Customer prefers email over phone calls",
      "observer_id": "agent_1"
    },
    "reference_time": "2023-07-01T12:00:00Z",
    "creation_time": "2023-07-02T15:30:45Z",
    "modification_time": "2023-07-02T15:30:45Z",
    "validity_period": {
      "start": "2023-07-01T00:00:00Z",
      "end": null
    },
    "metadata": {
      "source": "customer_survey",
      "confidence": 0.9
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Recall

The Recall endpoint provides a way to retrieve relevant information from the TLKG based on a query.

```
POST /recall
```

Retrieve relevant information.

#### Request

```json
{
  "user_id": "user_123",
  "text": "What are the customer's contact preferences?",
  "reference_time": "2023-07-15T12:00:00Z",
  "limit": 10,
  "filters": {
    "node_types": ["Observation"],
    "content_types": ["CustomerPreference"]
  }
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "items": [
      {
        "node_id": "node_abc123",
        "score": 0.95,
        "user_id": "user_123",
        "node_type": "Observation",
        "content": {
          "observation_type": "CustomerPreference",
          "text": "Customer prefers email over phone calls",
          "observer_id": "agent_1"
        },
        "reference_time": "2023-07-01T12:00:00Z",
        "creation_time": "2023-07-02T15:30:45Z",
        "modification_time": "2023-07-02T15:30:45Z",
        "validity_period": {
          "start": "2023-07-01T00:00:00Z",
          "end": "2024-07-01T00:00:00Z"
        },
        "metadata": {
          "source": "customer_survey",
          "confidence": 0.9
        }
      },
      // More items...
    ],
    "total_items": 3
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Infuse

The Infuse endpoint provides a way to enhance prompts with time-aware context.

```
POST /infuse
```

Infuse a prompt with context.

#### Request

```json
{
  "user_id": "user_123",
  "prompt": "How should I contact the customer?",
  "reference_time": "2023-07-15T12:00:00Z",
  "max_context_items": 5,
  "filters": {
    "node_types": ["Observation"],
    "content_types": ["CustomerPreference"]
  }
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "infused_prompt": "How should I contact the customer?\n\nContext:\n- Customer prefers email over phone calls (observed on 2023-07-01)\n- Customer's email is customer@example.com (observed on 2023-06-15)\n- Customer is available on weekdays between 9 AM and 5 PM (observed on 2023-06-20)",
    "context_items": [
      {
        "node_id": "node_abc123",
        "score": 0.95,
        "content": {
          "observation_type": "CustomerPreference",
          "text": "Customer prefers email over phone calls",
          "observer_id": "agent_1"
        },
        "reference_time": "2023-07-01T12:00:00Z"
      },
      // More items...
    ]
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Consent

The Consent endpoints provide ways to manage user consent.

### Update Consent

```
POST /consent
```

Update a user's consent settings.

#### Request

```json
{
  "user_id": "user_123",
  "data_categories": ["preferences", "history"],
  "purposes": ["service_improvement", "personalization"],
  "expiration": "2024-12-31T23:59:59Z"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "consent_id": "consent_mno345",
    "user_id": "user_123",
    "data_categories": ["preferences", "history"],
    "purposes": ["service_improvement", "personalization"],
    "creation_time": "2023-07-02T15:40:22Z",
    "expiration": "2024-12-31T23:59:59Z"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Check Consent

```
GET /consent/check
```

Check if a user has consented to a specific data category and purpose.

#### Parameters

- `user_id` (required): The user ID to check consent for
- `data_category` (required): The data category to check
- `purpose` (required): The purpose to check

#### Response

```json
{
  "status": "success",
  "data": {
    "user_id": "user_123",
    "data_category": "preferences",
    "purpose": "personalization",
    "granted": true,
    "expiration": "2024-12-31T23:59:59Z"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Revoke Consent

```
DELETE /consent
```

Revoke a user's consent.

#### Request

```json
{
  "user_id": "user_123",
  "data_categories": ["preferences"],
  "purposes": ["personalization"]
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "revoked": true,
    "user_id": "user_123",
    "data_categories": ["preferences"],
    "purposes": ["personalization"]
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Next Steps

Explore the other API sections:

- [TIF API](tif.md): Work with the Temporal Integrity Framework
- [Compliance API](compliance.md): Access compliance features
