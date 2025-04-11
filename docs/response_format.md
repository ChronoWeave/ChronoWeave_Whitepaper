# Response Format in ChronoWeave API

This document describes the standardized response format in the ChronoWeave API, including success responses, error responses, and examples.

## Table of Contents

- [Overview](#overview)
- [Success Response Format](#success-response-format)
- [Error Response Format](#error-response-format)
- [Paginated Response Format](#paginated-response-format)
- [Examples](#examples)

## Overview

The ChronoWeave API uses a standardized approach to response formatting to provide consistent and informative responses. This approach includes:

1. **Standardized Success Response Format**: All successful responses follow a consistent format, making it easier for clients to handle responses.
2. **Standardized Error Response Format**: All error responses follow a consistent format, making it easier for clients to handle errors.
3. **Standardized Paginated Response Format**: All paginated responses follow a consistent format, making it easier for clients to handle paginated data.

## Success Response Format

All successful responses follow this format:

```json
{
  "success": true,
  "data": {
    // Response data
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

- `success`: Always `true` for successful responses.
- `data`: Contains the response data.
- `metadata`: Contains additional metadata about the response.
  - `request_id`: A unique identifier for the request.
  - `timestamp`: The time the response was generated.

## Error Response Format

All error responses follow this format:

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Error message",
    "field": "field_name",
    "details": {
      "additional": "details"
    }
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

- `success`: Always `false` for error responses.
- `error`: Contains detailed information about the error.
  - `code`: A string code identifying the type of error.
  - `message`: A human-readable error message.
  - `field`: The field that caused the error (for validation errors).
  - `details`: Additional details about the error.
- `metadata`: Contains additional metadata about the response.
  - `request_id`: A unique identifier for the request.
  - `timestamp`: The time the response was generated.

## Paginated Response Format

All paginated responses follow this format:

```json
{
  "success": true,
  "data": [
    // List of items
  ],
  "metadata": {
    "page": 1,
    "page_size": 10,
    "total_items": 100,
    "total_pages": 10,
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

- `success`: Always `true` for successful responses.
- `data`: Contains the list of items for the current page.
- `metadata`: Contains pagination metadata and additional metadata about the response.
  - `page`: The current page number.
  - `page_size`: The number of items per page.
  - `total_items`: The total number of items across all pages.
  - `total_pages`: The total number of pages.
  - `request_id`: A unique identifier for the request.
  - `timestamp`: The time the response was generated.

## Examples

### Success Response Example

```json
{
  "success": true,
  "data": {
    "agent_id": "agent_456",
    "core_values": ["transparency", "privacy", "helpfulness"],
    "traits": {
      "openness": 0.8,
      "conscientiousness": 0.9,
      "extraversion": 0.6,
      "agreeableness": 0.7,
      "neuroticism": 0.3
    },
    "capabilities": ["text_generation", "information_retrieval", "summarization"],
    "created_at": "2023-06-15T14:30:00Z",
    "updated_at": "2023-06-15T14:30:00Z",
    "expiry": "2023-09-15T14:30:00Z",
    "expires_at": "2023-09-15T14:30:00Z",
    "metadata": {},
    "is_valid": true
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

### Error Response Example

```json
{
  "success": false,
  "error": {
    "code": "IDENTITY_NOT_FOUND",
    "message": "Agent identity not found: agent_123",
    "details": {
      "resource_type": "identity",
      "resource_id": "agent_123"
    }
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

### Paginated Response Example

```json
{
  "success": true,
  "data": [
    {
      "axiom_id": "A1",
      "axiom": "consent",
      "severity": "high",
      "message": "Missing or expired consent",
      "timestamp": "2023-06-15T14:30:00Z",
      "context": {
        "user_id": "user_123",
        "operation": "store",
        "data_category": "personal"
      }
    }
  ],
  "metadata": {
    "page": 1,
    "page_size": 10,
    "total_items": 100,
    "total_pages": 10,
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```
