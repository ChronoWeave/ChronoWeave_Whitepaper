# Request Logging in ChronoWeave API

This document describes the request logging approach in the ChronoWeave API, including configuration options, log format, and examples.

## Table of Contents

- [Overview](#overview)
- [Configuration](#configuration)
- [Log Format](#log-format)
- [Request ID Tracking](#request-id-tracking)
- [Examples](#examples)

## Overview

The ChronoWeave API includes comprehensive request logging to help with debugging, monitoring, and auditing. The request logging middleware captures detailed information about each request and response, including:

- Request method, path, and query parameters
- Request headers (with sensitive information redacted)
- Request body (optional)
- Response status code
- Response headers
- Response body (optional)
- Timing information
- Client IP address
- Request ID for correlation

This information is logged in a structured format (JSON by default) to make it easier to parse and analyze.

## Configuration

The request logging middleware can be configured using environment variables:

- `LOG_LEVEL`: The logging level (default: INFO)
- `LOG_FORMAT`: The logging format (json or text, default: json)
- `LOG_REQUEST_BODY`: Whether to log request bodies (true or false, default: false)
- `LOG_RESPONSE_BODY`: Whether to log response bodies (true or false, default: false)

Additional configuration options are set in the code:

- `exclude_paths`: Paths to exclude from logging (default: ["/health", "/metrics", "/docs", "/redoc", "/openapi.json"])
- `exclude_headers`: Headers to exclude from logging (default: ["authorization", "x-api-key", "cookie"])

## Log Format

The request logging middleware logs two messages for each request:

1. **Request Received**: Logged when the request is received, before processing
2. **Request Completed**: Logged when the response is sent, after processing

Each log message includes the following information:

```json
{
  "message": "Request received",
  "request_id": "550e8400-e29b-41d4-a716-446655440000",
  "method": "GET",
  "path": "/api/v1/tif/status",
  "query_params": {},
  "headers": {
    "host": "localhost:8000",
    "user-agent": "Mozilla/5.0",
    "accept": "application/json",
    "authorization": "[REDACTED]"
  },
  "client_ip": "127.0.0.1",
  "timestamp": "2023-06-15 14:30:00"
}
```

```json
{
  "message": "Request completed",
  "request_id": "550e8400-e29b-41d4-a716-446655440000",
  "method": "GET",
  "path": "/api/v1/tif/status",
  "query_params": {},
  "headers": {
    "host": "localhost:8000",
    "user-agent": "Mozilla/5.0",
    "accept": "application/json",
    "authorization": "[REDACTED]"
  },
  "client_ip": "127.0.0.1",
  "timestamp": "2023-06-15 14:30:00",
  "status_code": 200,
  "duration_ms": 12.34,
  "response_headers": {
    "content-type": "application/json",
    "content-length": "123"
  }
}
```

If request and response body logging is enabled, the log messages will also include:

```json
{
  "request_body": {
    "key": "value"
  },
  "response_body": {
    "success": true,
    "data": {
      "message": "Hello, world!"
    }
  }
}
```

## Request ID Tracking

The request logging middleware generates a unique request ID for each request if one is not already provided in the `X-Request-ID` header. This request ID is:

1. Added to the request state as `request.state.request_id`
2. Included in all log messages for the request
3. Added to the response headers as `X-Request-ID`

This makes it easier to correlate requests, responses, and log messages, especially in a distributed system.

## Examples

### Basic Request Logging

```
2023-06-15 14:30:00 - chronoweave.api.logging_middleware - INFO - {"message": "Request received", "request_id": "550e8400-e29b-41d4-a716-446655440000", "method": "GET", "path": "/api/v1/tif/status", "query_params": {}, "headers": {"host": "localhost:8000", "user-agent": "Mozilla/5.0", "accept": "application/json", "authorization": "[REDACTED]"}, "client_ip": "127.0.0.1", "timestamp": "2023-06-15 14:30:00"}
2023-06-15 14:30:00 - chronoweave.api.logging_middleware - INFO - {"message": "Request completed", "request_id": "550e8400-e29b-41d4-a716-446655440000", "method": "GET", "path": "/api/v1/tif/status", "query_params": {}, "headers": {"host": "localhost:8000", "user-agent": "Mozilla/5.0", "accept": "application/json", "authorization": "[REDACTED]"}, "client_ip": "127.0.0.1", "timestamp": "2023-06-15 14:30:00", "status_code": 200, "duration_ms": 12.34, "response_headers": {"content-type": "application/json", "content-length": "123"}}
```

### Request with Body Logging

```
2023-06-15 14:30:00 - chronoweave.api.logging_middleware - INFO - {"message": "Request received", "request_id": "550e8400-e29b-41d4-a716-446655440000", "method": "POST", "path": "/api/v1/tif/identity", "query_params": {}, "headers": {"host": "localhost:8000", "user-agent": "Mozilla/5.0", "accept": "application/json", "content-type": "application/json", "authorization": "[REDACTED]"}, "client_ip": "127.0.0.1", "timestamp": "2023-06-15 14:30:00", "request_body": {"agent_id": "agent_456", "core_values": ["transparency", "privacy", "helpfulness"], "traits": {"openness": 0.8, "conscientiousness": 0.9}, "capabilities": ["text_generation", "information_retrieval"], "duration_days": 90}}
2023-06-15 14:30:00 - chronoweave.api.logging_middleware - INFO - {"message": "Request completed", "request_id": "550e8400-e29b-41d4-a716-446655440000", "method": "POST", "path": "/api/v1/tif/identity", "query_params": {}, "headers": {"host": "localhost:8000", "user-agent": "Mozilla/5.0", "accept": "application/json", "content-type": "application/json", "authorization": "[REDACTED]"}, "client_ip": "127.0.0.1", "timestamp": "2023-06-15 14:30:00", "status_code": 201, "duration_ms": 45.67, "response_headers": {"content-type": "application/json", "content-length": "456"}, "response_body": {"success": true, "data": {"agent_id": "agent_456", "core_values": ["transparency", "privacy", "helpfulness"], "traits": {"openness": 0.8, "conscientiousness": 0.9}, "capabilities": ["text_generation", "information_retrieval"], "created_at": "2023-06-15T14:30:00Z", "updated_at": "2023-06-15T14:30:00Z", "expiry": "2023-09-15T14:30:00Z", "expires_at": "2023-09-15T14:30:00Z", "metadata": {}, "is_valid": true}, "metadata": {"request_id": "550e8400-e29b-41d4-a716-446655440000", "timestamp": "2023-06-15T14:30:00Z"}}}
```

### Error Response Logging

```
2023-06-15 14:30:00 - chronoweave.api.logging_middleware - INFO - {"message": "Request received", "request_id": "550e8400-e29b-41d4-a716-446655440000", "method": "GET", "path": "/api/v1/tif/identity/nonexistent_agent", "query_params": {}, "headers": {"host": "localhost:8000", "user-agent": "Mozilla/5.0", "accept": "application/json", "authorization": "[REDACTED]"}, "client_ip": "127.0.0.1", "timestamp": "2023-06-15 14:30:00"}
2023-06-15 14:30:00 - chronoweave.api.logging_middleware - INFO - {"message": "Request completed", "request_id": "550e8400-e29b-41d4-a716-446655440000", "method": "GET", "path": "/api/v1/tif/identity/nonexistent_agent", "query_params": {}, "headers": {"host": "localhost:8000", "user-agent": "Mozilla/5.0", "accept": "application/json", "authorization": "[REDACTED]"}, "client_ip": "127.0.0.1", "timestamp": "2023-06-15 14:30:00", "status_code": 404, "duration_ms": 5.67, "response_headers": {"content-type": "application/json", "content-length": "234"}, "response_body": {"success": false, "error": {"code": "IDENTITY_NOT_FOUND", "message": "Agent identity not found: nonexistent_agent", "details": {"resource_type": "identity", "resource_id": "nonexistent_agent"}}, "metadata": {"request_id": "550e8400-e29b-41d4-a716-446655440000", "timestamp": "2023-06-15T14:30:00Z"}}}
```
