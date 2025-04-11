# Error Handling in ChronoWeave API

This document describes the error handling approach in the ChronoWeave API, including error response formats, exception classes, and error codes.

## Table of Contents

- [Overview](#overview)
- [Error Response Format](#error-response-format)
- [Error Codes](#error-codes)
- [Exception Classes](#exception-classes)
- [Exception Handlers](#exception-handlers)
- [Examples](#examples)

## Overview

The ChronoWeave API uses a standardized approach to error handling to provide consistent and informative error responses. This approach includes:

1. **Standardized Error Response Format**: All error responses follow a consistent format, making it easier for clients to handle errors.
2. **Comprehensive Error Codes**: Each error has a specific error code, making it easier to identify and handle different types of errors.
3. **Specialized Exception Classes**: The API uses specialized exception classes for different types of errors, making it easier to raise and handle specific error conditions.
4. **Global Exception Handlers**: The API includes global exception handlers that convert exceptions into standardized error responses.

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
- `metadata`: Additional metadata about the request.
  - `request_id`: A unique identifier for the request.
  - `timestamp`: The time the error occurred.

## Error Codes

The API uses the following error codes:

### General Errors

- `INTERNAL_ERROR`: An internal server error occurred.
- `VALIDATION_ERROR`: The request data failed validation.
- `NOT_FOUND`: The requested resource was not found.
- `UNAUTHORIZED`: The request lacks valid authentication credentials.
- `FORBIDDEN`: The request is understood, but it has been refused or access is not allowed.

### TIF-Specific Errors

- `DORMANCY_ACTIVE`: The system is in dormancy mode.
- `DORMANCY_INACTIVE`: The system is not in dormancy mode.
- `IDENTITY_NOT_FOUND`: The requested identity was not found.
- `IDENTITY_INVALID`: The identity data is invalid.
- `CONSENT_NOT_FOUND`: The requested consent record was not found.
- `CONSENT_EXPIRED`: The consent record has expired.
- `AXIOM_VIOLATION`: An axiom violation occurred.

## Exception Classes

The API includes the following exception classes:

### Base Exception Class

- `ChronoWeaveException`: Base exception class for all ChronoWeave API exceptions.

### General Exception Classes

- `ValidationError`: Exception for validation errors.
- `NotFoundError`: Exception for not found errors.

### TIF-Specific Exception Classes

- `DormancyActiveError`: Exception for when the system is in dormancy mode.
- `DormancyInactiveError`: Exception for when the system is not in dormancy mode.
- `IdentityNotFoundError`: Exception for when an identity is not found.
- `IdentityInvalidError`: Exception for when an identity is invalid.
- `ConsentNotFoundError`: Exception for when a consent record is not found.
- `ConsentExpiredError`: Exception for when a consent record has expired.
- `AxiomViolationError`: Exception for axiom violations.

## Exception Handlers

The API includes the following exception handlers:

- `validation_exception_handler`: Handles validation exceptions from FastAPI.
- `chronoweave_exception_handler`: Handles ChronoWeave exceptions.
- `http_exception_handler`: Handles HTTP exceptions from FastAPI.
- `general_exception_handler`: Handles all other exceptions.

## Examples

### Validation Error

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation error",
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
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

### Not Found Error

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

### Dormancy Error

```json
{
  "success": false,
  "error": {
    "code": "DORMANCY_ACTIVE",
    "message": "Cannot create identity while system is in dormancy mode",
    "details": {
      "dormancy_info": {
        "active": true,
        "reason": "Scheduled maintenance",
        "timestamp": "2023-06-15T14:30:00Z"
      }
    }
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```
