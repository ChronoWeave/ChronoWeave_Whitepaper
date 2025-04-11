# API Key Management in ChronoWeave

This document describes the API key management system in ChronoWeave, including key generation, validation, role-based access control, and tiered rate limiting.

## Table of Contents

- [Overview](#overview)
- [API Key Format](#api-key-format)
- [Roles and Permissions](#roles-and-permissions)
- [Service Tiers](#service-tiers)
- [Key Lifecycle](#key-lifecycle)
- [API Endpoints](#api-endpoints)
- [Security Considerations](#security-considerations)
- [Best Practices](#best-practices)

## Overview

The ChronoWeave API uses API keys for authentication and authorization. Each API key has:

- A unique identifier
- A role that determines what actions the key can perform
- A service tier that determines rate limits
- An optional expiration date
- Metadata for additional information

API keys are managed through a set of API endpoints that allow creating, listing, revoking, and deleting keys.

## API Key Format

ChronoWeave API keys have the following format:

```
{tier}-{role}-{key_id}.{key_secret}
```

For example:

```
premium-write-550e8400e29b41d4a716446655440000.dGhpcyBpcyBhIHNlY3JldCBrZXk=
```

Where:

- `tier` is the service tier (basic, premium, unlimited)
- `role` is the key role (admin, write, read)
- `key_id` is a unique identifier for the key
- `key_secret` is a secret value used for authentication

The API key format is designed to be self-describing, making it easy to identify the tier and role of a key without looking it up in a database.

## Roles and Permissions

ChronoWeave API keys can have one of the following roles:

- **Admin**: Full access to all API endpoints, including key management
- **Write**: Access to read and write operations, but not administrative functions
- **Read**: Access to read-only operations

The role determines what actions the key can perform:

| Endpoint | Admin | Write | Read |
|----------|-------|-------|------|
| GET /api/v1/tif/status | ✓ | ✓ | ✓ |
| POST /api/v1/tif/identity | ✓ | ✓ | ✗ |
| GET /api/v1/tif/identity/{agent_id} | ✓ | ✓ | ✓ |
| POST /api/v1/tif/dormancy/exit | ✓ | ✗ | ✗ |
| POST /api/v1/keys | ✓ | ✗ | ✗ |
| GET /api/v1/keys | ✓ | ✗ | ✗ |
| POST /api/v1/keys/{key_id}/revoke | ✓ | ✗ | ✗ |
| DELETE /api/v1/keys/{key_id} | ✓ | ✗ | ✗ |

## Service Tiers

ChronoWeave API keys can have one of the following service tiers:

- **Basic**: Limited rate limits
- **Premium**: Higher rate limits
- **Unlimited**: Very high rate limits

The service tier determines the rate limits for the key:

| Tier | Rate Limit | Burst Limit |
|------|------------|-------------|
| Basic | 100 requests per minute | 120 requests |
| Premium | 500 requests per minute | 600 requests |
| Unlimited | 10,000 requests per minute | 12,000 requests |

## Key Lifecycle

API keys go through the following lifecycle:

1. **Creation**: A key is created with a specific role, tier, and optional expiration date
2. **Active**: The key is active and can be used to authenticate API requests
3. **Revocation**: The key is revoked and can no longer be used
4. **Deletion**: The key is permanently deleted from the system

Keys can also expire if they have an expiration date. Expired keys cannot be used to authenticate API requests.

## API Endpoints

The following API endpoints are available for managing API keys:

### Create API Key

```
POST /api/v1/keys
```

Creates a new API key with the specified role, tier, and expiration.

**Request Body:**

```json
{
  "name": "My API Key",
  "role": "write",
  "tier": "premium",
  "expires_in_days": 90,
  "metadata": {
    "owner": "John Doe",
    "department": "Engineering"
  }
}
```

**Response:**

```json
{
  "success": true,
  "data": {
    "key": "premium-write-550e8400e29b41d4a716446655440000.dGhpcyBpcyBhIHNlY3JldCBrZXk=",
    "key_id": "550e8400e29b41d4a716446655440000",
    "name": "My API Key",
    "role": "write",
    "tier": "premium",
    "created_at": "2023-06-15T14:30:00Z",
    "expires_at": "2023-09-15T14:30:00Z",
    "metadata": {
      "owner": "John Doe",
      "department": "Engineering"
    },
    "is_active": true
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

### List API Keys

```
GET /api/v1/keys
```

Lists all API keys.

**Query Parameters:**

- `include_inactive` (boolean, optional): Whether to include inactive keys (default: false)

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "key_id": "550e8400e29b41d4a716446655440000",
      "name": "My API Key",
      "role": "write",
      "tier": "premium",
      "created_at": "2023-06-15T14:30:00Z",
      "expires_at": "2023-09-15T14:30:00Z",
      "metadata": {
        "owner": "John Doe",
        "department": "Engineering"
      },
      "is_active": true
    }
  ],
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z",
    "total_items": 1,
    "include_inactive": false
  }
}
```

### Get API Key

```
GET /api/v1/keys/{key_id}
```

Gets an API key by ID.

**Response:**

```json
{
  "success": true,
  "data": {
    "key_id": "550e8400e29b41d4a716446655440000",
    "name": "My API Key",
    "role": "write",
    "tier": "premium",
    "created_at": "2023-06-15T14:30:00Z",
    "expires_at": "2023-09-15T14:30:00Z",
    "metadata": {
      "owner": "John Doe",
      "department": "Engineering"
    },
    "is_active": true
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

### Revoke API Key

```
POST /api/v1/keys/{key_id}/revoke
```

Revokes an API key by ID.

**Response:**

```json
{
  "success": true,
  "data": {
    "key_id": "550e8400e29b41d4a716446655440000",
    "revoked": true
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

### Delete API Key

```
DELETE /api/v1/keys/{key_id}
```

Deletes an API key by ID.

**Response:**

```json
{
  "success": true,
  "data": {
    "key_id": "550e8400e29b41d4a716446655440000",
    "deleted": true
  },
  "metadata": {
    "request_id": "req_123",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

## Security Considerations

The ChronoWeave API key management system includes several security features:

- **Key Hashing**: API keys are hashed before storage to prevent exposure in case of a database breach
- **HMAC Validation**: API keys are validated using HMAC-SHA256 to prevent tampering
- **Role-Based Access Control**: API keys have roles that determine what actions they can perform
- **Expiration**: API keys can have expiration dates to limit their lifetime
- **Revocation**: API keys can be revoked to immediately disable them
- **Audit Logging**: All API key operations are logged for audit purposes

## Best Practices

Here are some best practices for using API keys in ChronoWeave:

- **Use the Principle of Least Privilege**: Give API keys the minimum role required for their intended use
- **Set Expiration Dates**: Use expiration dates to limit the lifetime of API keys
- **Rotate Keys Regularly**: Create new API keys and revoke old ones on a regular schedule
- **Use Different Keys for Different Applications**: Use separate API keys for different applications or services
- **Store Keys Securely**: Store API keys securely and never expose them in client-side code
- **Monitor Key Usage**: Monitor API key usage for suspicious activity
- **Revoke Compromised Keys**: Immediately revoke any API keys that may have been compromised
