# API Endpoints Reference

ChronoWeave provides a RESTful API for interacting with the system. This document describes the available endpoints, request formats, and response structures.

## Table of Contents

- [Authentication](#authentication)
- [Base URL](#base-url)
- [Observation Endpoints](#observation-endpoints)
- [Recall Endpoints](#recall-endpoints)
- [Infusion Endpoints](#infusion-endpoints)
- [Consent Endpoints](#consent-endpoints)
- [Node Endpoints](#node-endpoints)
- [Health Endpoint](#health-endpoint)
- [Error Handling](#error-handling)
- [Examples](#examples)

## Authentication

All API requests require authentication using an API key. The API key should be included in the `X-API-Key` header.

```
X-API-Key: your_api_key
```

## Base URL

The base URL for all API endpoints is:

```
https://api.chronoweave.ai
```

## Observation Endpoints

### Store Observation

Store an observation in ChronoWeave.

**Endpoint:** `POST /api/v1/observe`

**Request Body:**

```json
{
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
}
```

**Response:**

```json
{
  "node_id": "node_789",
  "status": "success"
}
```

## Recall Endpoints

### Recall Information

Recall information from ChronoWeave.

**Endpoint:** `POST /api/v1/recall`

**Request Body:**

```json
{
  "text": "account access issues",
  "reference_time": "2023-06-15T14:30:00Z",
  "entity_ids": ["customer_123"],
  "k": 5,
  "min_score": 0.5,
  "filters": {
    "node_types": ["Observation"]
  }
}
```

**Response:**

```json
{
  "results": [
    {
      "node": {
        "node_id": "node_789",
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
      },
      "score": 0.92,
      "semantic_score": 0.95,
      "temporal_score": 0.85,
      "entity_score": 1.0
    }
  ],
  "status": "success"
}
```

### Recall by ID

Recall a specific node by ID.

**Endpoint:** `GET /api/v1/nodes/{node_id}`

**Response:**

```json
{
  "node_id": "node_789",
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
}
```

## Infusion Endpoints

### Infuse Prompt

Infuse a prompt with time-aware context.

**Endpoint:** `POST /api/v1/infuse`

**Request Body:**

```json
{
  "prompt": "What issues has the customer reported?",
  "query": {
    "text": "customer issues",
    "reference_time": "2023-06-15T14:30:00Z",
    "entity_ids": ["customer_123"]
  },
  "user_id": "agent_789",
  "model_type": "openai"
}
```

**Response:**

```json
{
  "infused_prompt": "What issues has the customer reported?\n\nCurrent time: 2023-06-15 14:30\n\nContext:\nObservation: Customer reported issues with account access (Date: 2023-06-15) (Relevance: 0.92)\nEntity: John Doe (Type: Customer) (Relevance: 0.85)\n",
  "status": "success"
}
```

### Infuse System Prompt

Infuse a system prompt with user-specific context.

**Endpoint:** `POST /api/v1/infuse/system`

**Request Body:**

```json
{
  "system_prompt": "You are a helpful assistant.",
  "user_id": "user_123",
  "reference_time": "2023-06-15T14:30:00Z",
  "model_type": "anthropic"
}
```

**Response:**

```json
{
  "infused_system_prompt": "You are a helpful assistant.\n\nUSER PROFILE:\nName: John Doe\nEmail: john@example.com\nPlan: Premium\n\nRecent interactions:\n- Customer reported issues with account access (2023-06-15)\n",
  "status": "success"
}
```

## Consent Endpoints

### Register Consent

Register user consent for data usage.

**Endpoint:** `POST /api/v1/consent`

**Request Body:**

```json
{
  "user_id": "user_123",
  "data_types": ["Entity", "Observation", "Event"],
  "purposes": ["storage", "retrieval", "context_infusion"],
  "expiration": "2024-06-15T00:00:00Z"
}
```

**Response:**

```json
{
  "consent_id": "consent_456",
  "status": "success"
}
```

### Check Consent

Check if user has given consent for a specific purpose.

**Endpoint:** `GET /api/v1/consent/{user_id}/{data_type}/{purpose}`

**Response:**

```json
{
  "has_consent": true,
  "expiration": "2024-06-15T00:00:00Z",
  "status": "success"
}
```

### Revoke Consent

Revoke user consent.

**Endpoint:** `DELETE /api/v1/consent`

**Request Body:**

```json
{
  "user_id": "user_123",
  "data_types": ["Observation"],
  "purposes": ["context_infusion"]
}
```

**Response:**

```json
{
  "status": "success"
}
```

## Node Endpoints

### Get Node

Get a node by ID.

**Endpoint:** `GET /api/v1/nodes/{node_id}`

**Response:**

```json
{
  "node_id": "node_789",
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
}
```

### Update Node

Update a node.

**Endpoint:** `PATCH /api/v1/nodes/{node_id}`

**Request Body:**

```json
{
  "metadata": {
    "importance": 0.9,
    "verified": true
  }
}
```

**Response:**

```json
{
  "node_id": "node_789",
  "status": "success"
}
```

### Delete Node

Delete a node.

**Endpoint:** `DELETE /api/v1/nodes/{node_id}`

**Response:**

```json
{
  "status": "success"
}
```

## Health Endpoint

### Check API Health

Check the health of the API.

**Endpoint:** `GET /health`

**Response:**

```json
{
  "status": "ok",
  "version": "1.0.0"
}
```

## Error Handling

ChronoWeave API uses standard HTTP status codes to indicate the success or failure of requests. In case of an error, the response body will contain an error message.

**Example Error Response:**

```json
{
  "status": "error",
  "error": "Invalid API key",
  "code": "unauthorized"
}
```

### Common Error Codes

| Status Code | Error Code | Description |
|-------------|------------|-------------|
| 400 | bad_request | The request was malformed or invalid |
| 401 | unauthorized | Invalid or missing API key |
| 403 | forbidden | The API key doesn't have permission to perform the request |
| 404 | not_found | The requested resource was not found |
| 429 | too_many_requests | Rate limit exceeded |
| 500 | server_error | An error occurred on the server |

## Examples

### cURL Examples

#### Store Observation

```bash
curl -X POST "https://api.chronoweave.ai/api/v1/observe" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your_api_key" \
  -d '{
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
  }'
```

#### Recall Information

```bash
curl -X POST "https://api.chronoweave.ai/api/v1/recall" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your_api_key" \
  -d '{
    "text": "account access issues",
    "reference_time": "2023-06-15T14:30:00Z",
    "entity_ids": ["customer_123"],
    "k": 5,
    "min_score": 0.5
  }'
```

#### Infuse Prompt

```bash
curl -X POST "https://api.chronoweave.ai/api/v1/infuse" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your_api_key" \
  -d '{
    "prompt": "What issues has the customer reported?",
    "query": {
      "text": "customer issues",
      "reference_time": "2023-06-15T14:30:00Z",
      "entity_ids": ["customer_123"]
    },
    "model_type": "openai"
  }'
```

#### Register Consent

```bash
curl -X POST "https://api.chronoweave.ai/api/v1/consent" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your_api_key" \
  -d '{
    "user_id": "user_123",
    "data_types": ["Entity", "Observation", "Event"],
    "purposes": ["storage", "retrieval", "context_infusion"],
    "expiration": "2024-06-15T00:00:00Z"
  }'
```
