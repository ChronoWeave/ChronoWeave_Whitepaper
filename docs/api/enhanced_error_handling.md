# Enhanced Error Handling in ChronoWeave API

This document describes the enhanced error handling system in the ChronoWeave API, including error response format, error types, and examples.

## Table of Contents

- [Overview](#overview)
- [Error Response Format](#error-response-format)
- [Error Types](#error-types)
- [Error Codes](#error-codes)
- [Exception Handlers](#exception-handlers)
- [Examples](#examples)
- [Best Practices](#best-practices)

## Overview

The ChronoWeave API includes a comprehensive error handling system that provides consistent, informative error responses across all endpoints. The system includes:

- **Standardized Error Response Format**: All error responses follow a consistent format, making it easier for clients to handle errors.
- **Detailed Error Information**: Error responses include detailed information about the error, including error code, message, field, and details.
- **Comprehensive Metadata**: Error responses include metadata such as request ID, timestamp, path, and method.
- **Extensive Logging**: All errors are logged with detailed information for debugging and monitoring.

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
    "timestamp": "2023-06-15T14:30:00Z",
    "path": "/api/v1/tif/status",
    "method": "GET"
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
  - `path`: The path of the request.
  - `method`: The HTTP method of the request.

## Error Types

The ChronoWeave API defines several error types:

### Validation Errors

Validation errors occur when the request data does not meet the validation requirements. These errors include:

- **Field Validation Errors**: Errors in specific fields, such as invalid format, length, or value.
- **Missing Field Errors**: Errors when required fields are missing.
- **Type Errors**: Errors when field values have the wrong type.

### HTTP Errors

HTTP errors occur when there is a problem with the HTTP request or response. These errors include:

- **Not Found Errors**: Errors when the requested resource is not found.
- **Method Not Allowed Errors**: Errors when the HTTP method is not allowed for the endpoint.
- **Unauthorized Errors**: Errors when authentication is required but not provided.
- **Forbidden Errors**: Errors when the authenticated user does not have permission to access the resource.

### Application Errors

Application errors occur when there is a problem with the application logic. These errors include:

- **Business Logic Errors**: Errors when the request violates business rules.
- **Data Errors**: Errors when there is a problem with the data.
- **System Errors**: Errors when there is a problem with the system.

### System Errors

System errors occur when there is a problem with the system infrastructure. These errors include:

- **Internal Server Errors**: Errors when there is an unexpected problem with the server.
- **Service Unavailable Errors**: Errors when the service is temporarily unavailable.
- **Gateway Timeout Errors**: Errors when a dependent service times out.

## Error Codes

The ChronoWeave API defines a comprehensive set of error codes:

### General Errors

- `INTERNAL_ERROR`: An unexpected internal error occurred.
- `VALIDATION_ERROR`: The request data did not pass validation.
- `NOT_FOUND`: The requested resource was not found.
- `UNAUTHORIZED`: Authentication is required.
- `FORBIDDEN`: The authenticated user does not have permission.
- `RATE_LIMIT_EXCEEDED`: The rate limit has been exceeded.
- `BAD_REQUEST`: The request is invalid.
- `METHOD_NOT_ALLOWED`: The HTTP method is not allowed.
- `CONFLICT`: The request conflicts with the current state.
- `GONE`: The resource is no longer available.
- `UNPROCESSABLE_ENTITY`: The request is well-formed but cannot be processed.
- `SERVICE_UNAVAILABLE`: The service is temporarily unavailable.
- `GATEWAY_TIMEOUT`: A dependent service timed out.

### TIF-Specific Errors

- `DORMANCY_ACTIVE`: The system is in dormancy mode.
- `DORMANCY_INACTIVE`: The system is not in dormancy mode.
- `IDENTITY_NOT_FOUND`: The agent identity was not found.
- `IDENTITY_INVALID`: The agent identity is invalid.
- `IDENTITY_EXPIRED`: The agent identity has expired.
- `CONSENT_NOT_FOUND`: The consent record was not found.
- `CONSENT_EXPIRED`: The consent record has expired.
- `CONSENT_INVALID`: The consent record is invalid.
- `AXIOM_VIOLATION`: An axiom violation occurred.

### API Key Errors

- `API_KEY_INVALID`: The API key is invalid.
- `API_KEY_EXPIRED`: The API key has expired.
- `API_KEY_REVOKED`: The API key has been revoked.
- `API_KEY_NOT_FOUND`: The API key was not found.
- `INSUFFICIENT_PERMISSIONS`: The API key does not have sufficient permissions.

### Data Errors

- `DATA_NOT_FOUND`: The requested data was not found.
- `DATA_INVALID`: The data is invalid.
- `DATA_CONFLICT`: The data conflicts with existing data.
- `DATA_EXPIRED`: The data has expired.
- `DATA_LOCKED`: The data is locked.

### Storage Errors

- `STORAGE_ERROR`: An error occurred with the storage system.
- `STORAGE_FULL`: The storage system is full.
- `STORAGE_UNAVAILABLE`: The storage system is unavailable.

### Network Errors

- `NETWORK_ERROR`: A network error occurred.
- `TIMEOUT`: A timeout occurred.
- `CONNECTION_ERROR`: A connection error occurred.

## Exception Handlers

The ChronoWeave API includes several exception handlers:

- `enhanced_validation_exception_handler`: Handles validation exceptions from FastAPI.
- `enhanced_http_exception_handler`: Handles HTTP exceptions from FastAPI.
- `enhanced_general_exception_handler`: Handles all other exceptions.

These handlers ensure that all errors are handled consistently and return standardized error responses.

## Examples

### Validation Error

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation error",
    "field": "core_values",
    "details": {
      "errors": [
        {
          "field": "core_values",
          "message": "core_values cannot be empty",
          "type": "value_error"
        }
      ]
    }
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z",
    "path": "/api/v1/tif/identity",
    "method": "POST"
  }
}
```

### Not Found Error

```json
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "Agent identity not found: agent_123",
    "details": {
      "resource_type": "identity",
      "resource_id": "agent_123"
    }
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z",
    "path": "/api/v1/tif/identity/agent_123",
    "method": "GET"
  }
}
```

### Rate Limit Exceeded Error

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded. Please try again later.",
    "details": {
      "limit": 100,
      "reset_after": 30
    }
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z",
    "path": "/api/v1/tif/status",
    "method": "GET"
  }
}
```

### Internal Server Error

```json
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "Internal server error: An unexpected error occurred."
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z",
    "path": "/api/v1/tif/status",
    "method": "GET"
  }
}
```

## Best Practices

Here are some best practices for handling errors in the ChronoWeave API:

### For API Developers

- **Use Specific Error Codes**: Use the most specific error code for each error.
- **Provide Detailed Error Messages**: Include detailed error messages that help clients understand and fix the error.
- **Include Field Information**: For validation errors, include the field that caused the error.
- **Add Detailed Metadata**: Include detailed metadata such as request ID, timestamp, path, and method.
- **Log Errors**: Log all errors with detailed information for debugging and monitoring.

### For API Clients

- **Check Success Field**: Always check the `success` field to determine if the request was successful.
- **Handle Error Codes**: Handle specific error codes appropriately.
- **Display Error Messages**: Display error messages to users in a user-friendly way.
- **Retry on Transient Errors**: Retry requests on transient errors such as rate limit exceeded or service unavailable.
- **Log Error Details**: Log error details for debugging and monitoring.
