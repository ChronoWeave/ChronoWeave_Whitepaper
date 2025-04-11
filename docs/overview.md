# ChronoWeave API Overview

The ChronoWeave API provides a comprehensive set of endpoints for interacting with the ChronoWeave temporal cognition framework. This document provides an overview of the API structure, authentication, and common patterns.

## API Structure

The ChronoWeave API is organized into three main sections:

1. **Core API**: Basic operations for storing and retrieving temporal information
2. **TIF API**: Operations related to the Temporal Integrity Framework
3. **Compliance API**: Operations related to regulatory compliance features

### Base URL

All API endpoints are relative to the base URL:

```
https://api.chronoweave.ai/v1
```

For beta environments, use:

```
https://beta-api.chronoweave.ai/v1
```

## Authentication

ChronoWeave uses API keys for authentication. Include your API key in the `Authorization` header of all requests:

```
Authorization: Bearer your-api-key
```

### API Key Management

You can manage your API keys through the ChronoWeave dashboard or using the API key management endpoints:

- `POST /api-keys`: Create a new API key
- `GET /api-keys`: List all API keys
- `DELETE /api-keys/{key_id}`: Delete an API key

## Request Format

Most API endpoints accept JSON in the request body. For example:

```json
{
  "user_id": "user_123",
  "content": {
    "observation_type": "CustomerPreference",
    "text": "Customer prefers email over phone calls"
  },
  "reference_time": "2023-07-01T12:00:00Z"
}
```

## Response Format

All API responses are in JSON format and include a standard structure:

```json
{
  "status": "success",
  "data": {
    // Response data specific to the endpoint
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

For error responses:

```json
{
  "status": "error",
  "error": {
    "code": "invalid_request",
    "message": "Invalid request parameters",
    "details": {
      // Additional error details
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 7
  }
}
```

## Rate Limits

ChronoWeave implements rate limiting to ensure fair usage of the API:

- **Standard Tier**: 100 requests per minute, 10,000 requests per day
- **Professional Tier**: 500 requests per minute, 50,000 requests per day
- **Enterprise Tier**: Custom limits based on your needs

Rate limit headers are included in all responses:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1633089600
```

## Pagination

For endpoints that return lists of items, ChronoWeave uses cursor-based pagination:

```json
{
  "status": "success",
  "data": {
    "items": [
      // List of items
    ],
    "pagination": {
      "next_cursor": "cursor_abc123",
      "has_more": true
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

To get the next page, include the `cursor` parameter in your request:

```
GET /nodes?cursor=cursor_abc123
```

## Error Handling

ChronoWeave uses standard HTTP status codes to indicate the success or failure of an API request:

- **2xx**: Success
- **4xx**: Client error (invalid request, authentication error, etc.)
- **5xx**: Server error

Common error codes include:

- `400 Bad Request`: Invalid request parameters
- `401 Unauthorized`: Missing or invalid API key
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `429 Too Many Requests`: Rate limit exceeded
- `500 Internal Server Error`: Server error

## Versioning

The ChronoWeave API is versioned to ensure backward compatibility. The current version is `v1`.

When we make backward-incompatible changes, we'll release a new API version. You can specify the API version in the URL:

```
https://api.chronoweave.ai/v1/...
https://api.chronoweave.ai/v2/...
```

## SDK Support

ChronoWeave provides official SDKs for several programming languages:

- [Python SDK](https://github.com/chronoweave/chronoweave-python)
- [JavaScript SDK](https://github.com/chronoweave/chronoweave-js)
- [Java SDK](https://github.com/chronoweave/chronoweave-java)
- [C# SDK](https://github.com/chronoweave/chronoweave-dotnet)

## Next Steps

Explore the specific API sections:

- [Core API](core.md): Store and retrieve temporal information
- [TIF API](tif.md): Work with the Temporal Integrity Framework
- [Compliance API](compliance.md): Access compliance features
