# Rate Limiting in ChronoWeave API

This document describes the rate limiting approach in the ChronoWeave API, including configuration options, rate limit strategies, and examples.

## Table of Contents

- [Overview](#overview)
- [Configuration](#configuration)
- [Rate Limit Headers](#rate-limit-headers)
- [Rate Limiting Strategies](#rate-limiting-strategies)
- [Tiered Rate Limiting](#tiered-rate-limiting)
- [Examples](#examples)

## Overview

The ChronoWeave API includes comprehensive rate limiting to protect the API from abuse and ensure fair resource allocation. The rate limiting middleware limits the number of requests that clients can make to the API within a specified time period, based on client IP address, API key, or other identifiers.

Key features of the rate limiting system include:

- **Token Bucket Algorithm**: Uses the token bucket algorithm for flexible and efficient rate limiting
- **Tiered Rate Limiting**: Supports different rate limits for different API keys or clients
- **Rate Limit Headers**: Includes standard rate limit headers in responses
- **Path Exclusion**: Allows excluding specific paths from rate limiting
- **Configurable Error Messages**: Customizable error messages for rate limit exceeded responses

## Configuration

The rate limiting middleware can be configured using environment variables:

- `ENABLE_RATE_LIMITING`: Whether to enable rate limiting (true or false, default: true)
- `ENABLE_TIERED_RATE_LIMITING`: Whether to enable tiered rate limiting (true or false, default: false)
- `RATE_LIMIT_RATE`: Number of requests allowed per time period for basic rate limiting (default: 100)
- `RATE_LIMIT_PER`: Time period in seconds for basic rate limiting (default: 60)
- `RATE_LIMIT_BURST`: Maximum burst size for basic rate limiting (default: 120)

For tiered rate limiting, additional environment variables are available:

- `RATE_LIMIT_BASIC_RATE`: Number of requests allowed per time period for basic tier (default: 100)
- `RATE_LIMIT_BASIC_PER`: Time period in seconds for basic tier (default: 60)
- `RATE_LIMIT_BASIC_BURST`: Maximum burst size for basic tier (default: 120)
- `RATE_LIMIT_PREMIUM_RATE`: Number of requests allowed per time period for premium tier (default: 500)
- `RATE_LIMIT_PREMIUM_PER`: Time period in seconds for premium tier (default: 60)
- `RATE_LIMIT_PREMIUM_BURST`: Maximum burst size for premium tier (default: 600)
- `RATE_LIMIT_UNLIMITED_RATE`: Number of requests allowed per time period for unlimited tier (default: 10000)
- `RATE_LIMIT_UNLIMITED_PER`: Time period in seconds for unlimited tier (default: 60)
- `RATE_LIMIT_UNLIMITED_BURST`: Maximum burst size for unlimited tier (default: 12000)

## Rate Limit Headers

The rate limiting middleware includes the following headers in responses:

- `X-RateLimit-Limit`: The maximum number of requests that the client is allowed to make in the current time window
- `X-RateLimit-Remaining`: The number of requests remaining in the current time window
- `X-RateLimit-Reset`: The time at which the current rate limit window resets, in Unix time
- `Retry-After`: The number of seconds to wait before making another request

Example:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1623766200
Retry-After: 0
```

## Rate Limiting Strategies

The ChronoWeave API supports two rate limiting strategies:

### Token Bucket Algorithm

The token bucket algorithm is a flexible and efficient rate limiting algorithm that allows for bursts of traffic while still enforcing a long-term rate limit. It works as follows:

1. Each client has a bucket that can hold a maximum number of tokens (the burst size).
2. Tokens are added to the bucket at a constant rate (the refill rate).
3. When a client makes a request, a token is removed from the bucket.
4. If the bucket is empty, the request is rejected.

This algorithm allows clients to make bursts of requests up to the burst size, but still enforces a long-term rate limit based on the refill rate.

### In-Memory Storage

The ChronoWeave API uses in-memory storage for rate limiting, which is fast and efficient but does not persist across server restarts or scale across multiple instances. For production deployments with multiple instances, consider using a distributed rate limiting solution with a shared storage backend like Redis.

## Tiered Rate Limiting

The ChronoWeave API supports tiered rate limiting, which allows different rate limits for different API keys or clients. This is useful for implementing different service tiers, such as basic, premium, and unlimited.

Tiered rate limiting works as follows:

1. Each API key is assigned to a tier (basic, premium, or unlimited).
2. Each tier has its own rate limit parameters (rate, per, and burst).
3. The rate limit is applied based on the tier of the API key.

The tier is determined based on the API key prefix:

- API keys starting with `premium-` are assigned to the premium tier.
- API keys starting with `unlimited-` are assigned to the unlimited tier.
- All other API keys are assigned to the basic tier.

In a real implementation, the tier would be looked up in a database or other storage system.

## Examples

### Basic Rate Limiting

With basic rate limiting, all clients are subject to the same rate limit:

```
# Allow 100 requests per minute with a burst of 120
ENABLE_RATE_LIMITING=true
ENABLE_TIERED_RATE_LIMITING=false
RATE_LIMIT_RATE=100
RATE_LIMIT_PER=60
RATE_LIMIT_BURST=120
```

### Tiered Rate Limiting

With tiered rate limiting, different clients can have different rate limits based on their tier:

```
# Enable tiered rate limiting
ENABLE_RATE_LIMITING=true
ENABLE_TIERED_RATE_LIMITING=true

# Basic tier: 100 requests per minute with a burst of 120
RATE_LIMIT_BASIC_RATE=100
RATE_LIMIT_BASIC_PER=60
RATE_LIMIT_BASIC_BURST=120

# Premium tier: 500 requests per minute with a burst of 600
RATE_LIMIT_PREMIUM_RATE=500
RATE_LIMIT_PREMIUM_PER=60
RATE_LIMIT_PREMIUM_BURST=600

# Unlimited tier: 10000 requests per minute with a burst of 12000
RATE_LIMIT_UNLIMITED_RATE=10000
RATE_LIMIT_UNLIMITED_PER=60
RATE_LIMIT_UNLIMITED_BURST=12000
```

### Rate Limit Exceeded Response

When a client exceeds the rate limit, they receive a 429 Too Many Requests response with a JSON error message:

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Rate limit exceeded. Please try again later or upgrade your plan."
  },
  "metadata": {
    "request_id": "550e8400-e29b-41d4-a716-446655440000",
    "timestamp": "2023-06-15T14:30:00Z"
  }
}
```

The response also includes rate limit headers that indicate when the client can make another request:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1623766200
Retry-After: 30
```
