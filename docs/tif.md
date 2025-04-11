# Temporal Integrity Framework (TIF) API

The Temporal Integrity Framework (TIF) API provides operations for working with ChronoWeave's Temporal Integrity Framework, including the Temporal Integrity Enforcement Layer (TIEL) and the Regulatory-Aware Ethical Suspension System (RA-ESS).

## TIEL Operations

The Temporal Integrity Enforcement Layer (TIEL) ensures the consistency and reliability of temporal information.

### Validate Operation

```
POST /tif/validate
```

Validate an operation to ensure it maintains temporal integrity.

#### Request

```json
{
  "operation_type": "CREATE_NODE",
  "parameters": {
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
    }
  }
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "valid": true,
    "operation_type": "CREATE_NODE",
    "validation_details": {
      "temporal_consistency": true,
      "causal_consistency": true,
      "regulatory_compliance": true
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Get Temporal Lineage

```
GET /tif/lineage/{node_id}
```

Get the temporal lineage of a node, showing how it has changed over time.

#### Parameters

- `start_time` (optional): Filter lineage to changes after this time
- `end_time` (optional): Filter lineage to changes before this time

#### Response

```json
{
  "status": "success",
  "data": {
    "node_id": "node_abc123",
    "lineage": [
      {
        "version": 1,
        "operation_type": "CREATE_NODE",
        "timestamp": "2023-07-02T15:30:45Z",
        "content": {
          "observation_type": "CustomerPreference",
          "text": "Customer prefers email over phone calls",
          "observer_id": "agent_1"
        },
        "metadata": {
          "source": "customer_survey",
          "confidence": 0.9
        }
      },
      {
        "version": 2,
        "operation_type": "UPDATE_NODE",
        "timestamp": "2023-07-03T09:15:22Z",
        "content": {
          "observation_type": "CustomerPreference",
          "text": "Customer prefers email over phone calls, but is open to SMS for urgent matters",
          "observer_id": "agent_1"
        },
        "metadata": {
          "source": "customer_survey",
          "confidence": 0.95
        }
      }
    ]
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Get Temporal Snapshot

```
GET /tif/snapshot/{user_id}
```

Get a snapshot of a user's data at a specific point in time.

#### Parameters

- `reference_time` (required): The point in time to get the snapshot for
- `node_types` (optional): Filter by node types
- `content_types` (optional): Filter by content types

#### Response

```json
{
  "status": "success",
  "data": {
    "user_id": "user_123",
    "reference_time": "2023-07-01T12:00:00Z",
    "nodes": [
      {
        "node_id": "node_abc123",
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
        }
      },
      // More nodes...
    ],
    "relationships": [
      {
        "relationship_id": "rel_ghi789",
        "source_node_id": "node_abc123",
        "target_node_id": "node_def456",
        "relationship_type": "PRECEDES"
      },
      // More relationships...
    ]
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Get Temporal Diff

```
POST /tif/diff
```

Get the differences between two temporal snapshots.

#### Request

```json
{
  "user_id": "user_123",
  "start_time": "2023-07-01T12:00:00Z",
  "end_time": "2023-07-15T12:00:00Z",
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
    "user_id": "user_123",
    "start_time": "2023-07-01T12:00:00Z",
    "end_time": "2023-07-15T12:00:00Z",
    "added_nodes": [
      {
        "node_id": "node_pqr678",
        "node_type": "Observation",
        "content": {
          "observation_type": "CustomerPreference",
          "text": "Customer prefers SMS for appointment reminders",
          "observer_id": "agent_2"
        },
        "reference_time": "2023-07-10T14:22:33Z"
      }
    ],
    "modified_nodes": [
      {
        "node_id": "node_abc123",
        "old_content": {
          "observation_type": "CustomerPreference",
          "text": "Customer prefers email over phone calls",
          "observer_id": "agent_1"
        },
        "new_content": {
          "observation_type": "CustomerPreference",
          "text": "Customer prefers email over phone calls, but is open to SMS for urgent matters",
          "observer_id": "agent_1"
        },
        "modification_time": "2023-07-03T09:15:22Z"
      }
    ],
    "deleted_nodes": [],
    "added_relationships": [
      {
        "relationship_id": "rel_stu901",
        "source_node_id": "node_abc123",
        "target_node_id": "node_pqr678",
        "relationship_type": "RELATED_TO"
      }
    ],
    "deleted_relationships": []
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## RA-ESS Operations

The Regulatory-Aware Ethical Suspension System (RA-ESS) provides control over information accessibility.

### Suspend Node

```
POST /tif/suspend
```

Suspend a node, making it temporarily inaccessible.

#### Request

```json
{
  "node_id": "node_abc123",
  "suspension_type": "soft_pause",
  "reason": "User requested temporary removal",
  "expiration": "2023-08-01T00:00:00Z"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "suspension_id": "susp_vwx234",
    "node_id": "node_abc123",
    "suspension_type": "soft_pause",
    "reason": "User requested temporary removal",
    "creation_time": "2023-07-15T10:20:30Z",
    "expiration": "2023-08-01T00:00:00Z",
    "status": "active"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Get Suspension

```
GET /tif/suspensions/{suspension_id}
```

Get information about a specific suspension.

#### Response

```json
{
  "status": "success",
  "data": {
    "suspension_id": "susp_vwx234",
    "node_id": "node_abc123",
    "suspension_type": "soft_pause",
    "reason": "User requested temporary removal",
    "creation_time": "2023-07-15T10:20:30Z",
    "expiration": "2023-08-01T00:00:00Z",
    "status": "active"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### List Suspensions

```
GET /tif/suspensions
```

List suspensions with optional filtering.

#### Parameters

- `node_id` (optional): Filter by node ID
- `user_id` (optional): Filter by user ID
- `suspension_type` (optional): Filter by suspension type
- `status` (optional): Filter by status
- `cursor` (optional): Pagination cursor
- `limit` (optional): Number of suspensions to return (default: 20, max: 100)

#### Response

```json
{
  "status": "success",
  "data": {
    "items": [
      {
        "suspension_id": "susp_vwx234",
        "node_id": "node_abc123",
        "suspension_type": "soft_pause",
        "reason": "User requested temporary removal",
        "creation_time": "2023-07-15T10:20:30Z",
        "expiration": "2023-08-01T00:00:00Z",
        "status": "active"
      },
      // More suspensions...
    ],
    "pagination": {
      "next_cursor": "cursor_yz0123",
      "has_more": true
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Lift Suspension

```
DELETE /tif/suspensions/{suspension_id}
```

Lift a suspension, making the node accessible again.

#### Response

```json
{
  "status": "success",
  "data": {
    "lifted": true,
    "suspension_id": "susp_vwx234",
    "node_id": "node_abc123"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Apply Regulatory Rule

```
POST /tif/rules
```

Apply a regulatory rule to a user's data.

#### Request

```json
{
  "user_id": "user_123",
  "rule_type": "retention_policy",
  "parameters": {
    "data_category": "financial_transactions",
    "retention_period": "7y",
    "action": "archive"
  },
  "jurisdiction": "US-FINRA"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "rule_id": "rule_abc123",
    "user_id": "user_123",
    "rule_type": "retention_policy",
    "parameters": {
      "data_category": "financial_transactions",
      "retention_period": "7y",
      "action": "archive"
    },
    "jurisdiction": "US-FINRA",
    "creation_time": "2023-07-15T10:25:30Z",
    "status": "active"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Get Regulatory Compliance Status

```
GET /tif/compliance/{user_id}
```

Get the regulatory compliance status for a user.

#### Parameters

- `jurisdiction` (optional): Filter by jurisdiction
- `data_category` (optional): Filter by data category

#### Response

```json
{
  "status": "success",
  "data": {
    "user_id": "user_123",
    "compliance_status": {
      "US-HIPAA": {
        "compliant": true,
        "details": {
          "phi_protection": "compliant",
          "access_controls": "compliant",
          "audit_logging": "compliant"
        }
      },
      "US-FINRA": {
        "compliant": true,
        "details": {
          "retention_policies": "compliant",
          "data_lifecycle": "compliant",
          "regulatory_reporting": "compliant"
        }
      }
    },
    "active_rules": [
      {
        "rule_id": "rule_abc123",
        "rule_type": "retention_policy",
        "jurisdiction": "US-FINRA"
      },
      // More rules...
    ]
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Next Steps

Explore the other API sections:

- [Core API](core.md): Store and retrieve temporal information
- [Compliance API](compliance.md): Access compliance features
