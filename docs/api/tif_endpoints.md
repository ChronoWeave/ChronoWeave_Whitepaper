# Temporal Integrity Framework (TIF) API Endpoints

This document provides detailed information about the TIF API endpoints, including request formats, response structures, and examples.

## Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [Base URL](#base-url)
- [Status Endpoint](#status-endpoint)
- [Consent Endpoints](#consent-endpoints)
- [Identity Endpoints](#identity-endpoints)
- [Dormancy Endpoints](#dormancy-endpoints)
- [Violations Endpoint](#violations-endpoint)
- [Error Handling](#error-handling)

## Overview

The Temporal Integrity Framework (TIF) provides ethical governance, privacy controls, and human oversight for ChronoWeave's temporal cognition system. The TIF API endpoints allow you to interact with the TIF components, including the Moral Axiom System, ChronoSelf Identity Manager, and Ethical Dormancy Protocol.

## Authentication

All API requests require authentication using an API key. The API key should be included in the `X-API-Key` header.

```
X-API-Key: your_api_key
```

## Base URL

All TIF API endpoints are prefixed with `/api/v1/tif`.

## Status Endpoint

### Get TIF Status

```
GET /api/v1/tif/status
```

Returns the current status of the TIF, including dormancy status and recent axiom violations.

#### Response

```json
{
  "dormant": false,
  "dormancy_info": null,
  "axiom_violation_count": 0,
  "recent_violations": []
}
```

## Consent Endpoints

### Create Consent Record

```
POST /api/v1/tif/consent
```

Creates a new consent record for a user.

#### Request

```json
{
  "user_id": "user_123",
  "operations": ["store", "retrieve", "process"],
  "data_categories": ["personal", "behavioral", "preferences"],
  "duration_days": 90
}
```

#### Response

```json
{
  "consent_id": "consent_789",
  "user_id": "user_123",
  "operations": ["store", "retrieve", "process"],
  "data_categories": ["personal", "behavioral", "preferences"],
  "created_at": "2023-06-15T14:30:00Z",
  "expires_at": "2023-09-15T14:30:00Z",
  "active": true
}
```

### Get User Consent Records

```
GET /api/v1/tif/consent/{user_id}
```

Returns the consent records for a user.

#### Parameters

- `user_id` (path): ID of the user
- `include_revoked` (query, optional): Whether to include revoked consent records (default: false)

#### Response

```json
[
  {
    "consent_id": "consent_789",
    "user_id": "user_123",
    "operations": ["store", "retrieve", "process"],
    "data_categories": ["personal", "behavioral", "preferences"],
    "created_at": "2023-06-15T14:30:00Z",
    "expires_at": "2023-09-15T14:30:00Z",
    "active": true
  }
]
```

## Identity Endpoints

### Create Agent Identity

```
POST /api/v1/tif/identity
```

Creates a new agent identity.

#### Request

```json
{
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
  "duration_days": 90
}
```

#### Response

```json
{
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
}
```

Note: The response includes both `expiry` and `expires_at` fields, which contain the same value. The `expires_at` field is provided for API compatibility.

### Get Agent Identity

```
GET /api/v1/tif/identity/{agent_id}
```

Returns the identity of an agent.

#### Parameters

- `agent_id` (path): ID of the agent

#### Response

```json
{
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
}
```

## Dormancy Endpoints

### Get Dormancy Status

```
GET /api/v1/tif/dormancy
```

Returns the current dormancy status.

#### Response

```json
{
  "active": false,
  "reason": null,
  "timestamp": null,
  "approver": null,
  "exit_timestamp": null,
  "human_approved_exit": null,
  "dormancy_id": null
}
```

### Enter Dormancy

```
POST /api/v1/tif/dormancy
```

Puts the system into dormancy mode.

#### Request

```json
{
  "reason": "Scheduled maintenance"
}
```

#### Response

```json
{
  "active": true,
  "reason": "Scheduled maintenance",
  "timestamp": "2023-06-15T14:30:00Z",
  "approver": null,
  "exit_timestamp": null,
  "human_approved_exit": null,
  "dormancy_id": "dormancy_123"
}
```

### Exit Dormancy

```
POST /api/v1/tif/dormancy/exit
```

Exits dormancy mode.

#### Request

```json
{
  "approver": "admin_user",
  "reason": "Maintenance completed",
  "human_approved": true
}
```

#### Response

```json
{
  "active": false,
  "success": true,
  "message": "Dormancy exited. Approved by: admin_user"
}
```

Note: The response includes a `success` field that indicates whether the operation was successful. If the system was not in dormancy mode, the `success` field will be `false`.

## Violations Endpoint

### Get Axiom Violations

```
GET /api/v1/tif/violations
```

Returns recent axiom violations with pagination and filtering.

#### Parameters

- `page` (query, optional): Page number (1-based, default: 1)
- `page_size` (query, optional): Number of items per page (default: 10, max: 100)
- `axiom_id` (query, optional): Filter by axiom ID
- `severity` (query, optional): Filter by severity (low, medium, high)

#### Response

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
    "total_pages": 10
  }
}
```

## Error Handling

The TIF API uses standard HTTP status codes to indicate the success or failure of a request. In case of an error, the response body will contain a JSON object with a `detail` field that provides more information about the error.

### Common Error Codes

- `400 Bad Request`: The request was invalid or cannot be served. The exact error is specified in the response body.
- `401 Unauthorized`: Authentication failed or user doesn't have permissions for the requested operation.
- `403 Forbidden`: The request is understood, but it has been refused or access is not allowed.
- `404 Not Found`: The requested resource could not be found.
- `500 Internal Server Error`: An error occurred on the server.

### Error Response Example

```json
{
  "detail": "Invalid operation: modify. Must be one of: store, retrieve, process, share, delete"
}
```
