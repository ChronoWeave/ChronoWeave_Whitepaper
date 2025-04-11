# Rate Limiting Configuration Guide

This document provides detailed information about configuring the rate limiting middleware in the ChronoWeave API.

## Table of Contents

- [Overview](#overview)
- [Environment Variables](#environment-variables)
- [Code Configuration](#code-configuration)
- [Rate Limiting Strategies](#rate-limiting-strategies)
- [Performance Considerations](#performance-considerations)
- [Security Considerations](#security-considerations)
- [Best Practices](#best-practices)

## Overview

The rate limiting middleware in the ChronoWeave API is designed to be highly configurable to meet different rate limiting needs. This guide explains how to configure the middleware for different environments and use cases.

## Environment Variables

The rate limiting middleware can be configured using the following environment variables:

| Variable | Description | Default | Options |
|----------|-------------|---------|---------|
| `ENABLE_RATE_LIMITING` | Whether to enable rate limiting | `true` | `true`, `false` |
| `ENABLE_TIERED_RATE_LIMITING` | Whether to enable tiered rate limiting | `false` | `true`, `false` |
| `RATE_LIMIT_RATE` | Number of requests allowed per time period for basic rate limiting | `100` | Any positive integer |
| `RATE_LIMIT_PER` | Time period in seconds for basic rate limiting | `60` | Any positive integer |
| `RATE_LIMIT_BURST` | Maximum burst size for basic rate limiting | `120` | Any positive integer |
| `RATE_LIMIT_BASIC_RATE` | Number of requests allowed per time period for basic tier | `100` | Any positive integer |
| `RATE_LIMIT_BASIC_PER` | Time period in seconds for basic tier | `60` | Any positive integer |
| `RATE_LIMIT_BASIC_BURST` | Maximum burst size for basic tier | `120` | Any positive integer |
| `RATE_LIMIT_PREMIUM_RATE` | Number of requests allowed per time period for premium tier | `500` | Any positive integer |
| `RATE_LIMIT_PREMIUM_PER` | Time period in seconds for premium tier | `60` | Any positive integer |
| `RATE_LIMIT_PREMIUM_BURST` | Maximum burst size for premium tier | `600` | Any positive integer |
| `RATE_LIMIT_UNLIMITED_RATE` | Number of requests allowed per time period for unlimited tier | `10000` | Any positive integer |
| `RATE_LIMIT_UNLIMITED_PER` | Time period in seconds for unlimited tier | `60` | Any positive integer |
| `RATE_LIMIT_UNLIMITED_BURST` | Maximum burst size for unlimited tier | `12000` | Any positive integer |

Example:

```bash
# Enable basic rate limiting
export ENABLE_RATE_LIMITING=true
export ENABLE_TIERED_RATE_LIMITING=false
export RATE_LIMIT_RATE=100
export RATE_LIMIT_PER=60
export RATE_LIMIT_BURST=120

# Enable tiered rate limiting
export ENABLE_RATE_LIMITING=true
export ENABLE_TIERED_RATE_LIMITING=true
export RATE_LIMIT_BASIC_RATE=100
export RATE_LIMIT_BASIC_PER=60
export RATE_LIMIT_BASIC_BURST=120
export RATE_LIMIT_PREMIUM_RATE=500
export RATE_LIMIT_PREMIUM_PER=60
export RATE_LIMIT_PREMIUM_BURST=600
export RATE_LIMIT_UNLIMITED_RATE=10000
export RATE_LIMIT_UNLIMITED_PER=60
export RATE_LIMIT_UNLIMITED_BURST=12000
```

## Code Configuration

The rate limiting middleware can also be configured in code when adding it to the FastAPI app:

### Basic Rate Limiting

```python
from chronoweave.api.rate_limiting import add_rate_limiting_middleware

# Add basic rate limiting middleware
add_rate_limiting_middleware(
    app,
    rate=100,
    per=60,
    burst=120,
    exclude_paths=["/health", "/metrics", "/docs", "/redoc", "/openapi.json"],
    headers_enabled=True,
    error_message="Rate limit exceeded. Please try again later."
)
```

### Tiered Rate Limiting

```python
from chronoweave.api.rate_limiting import add_tiered_rate_limiting_middleware

# Define tiers
tiers = {
    "basic": {
        "rate": 100,
        "per": 60,
        "burst": 120
    },
    "premium": {
        "rate": 500,
        "per": 60,
        "burst": 600
    },
    "unlimited": {
        "rate": 10000,
        "per": 60,
        "burst": 12000
    }
}

# Define key function to extract tier and API key
async def tiered_key_func(request: Request) -> str:
    # Get API key from header
    api_key = request.headers.get("X-API-Key", "")
    
    # Determine tier based on API key
    if api_key.startswith("premium-"):
        tier = "premium"
    elif api_key.startswith("unlimited-"):
        tier = "unlimited"
    else:
        tier = "basic"
    
    # Get client IP
    forwarded_for = request.headers.get("X-Forwarded-For")
    if forwarded_for:
        client_ip = forwarded_for.split(",")[0].strip()
    else:
        client_ip = request.client.host if request.client else "unknown"
    
    # Return tier and client IP as key
    return f"tier:{tier}:{client_ip}"

# Add tiered rate limiting middleware
add_tiered_rate_limiting_middleware(
    app,
    tiers=tiers,
    default_tier="basic",
    key_func=tiered_key_func,
    exclude_paths=["/health", "/metrics", "/docs", "/redoc", "/openapi.json"],
    headers_enabled=True,
    error_message="Rate limit exceeded. Please try again later or upgrade your plan."
)
```

### Configuration Options

| Option | Description | Default |
|--------|-------------|---------|
| `rate` | Number of requests allowed per time period | `100` |
| `per` | Time period in seconds | `60` |
| `burst` | Maximum burst size | `None` (same as rate) |
| `key_func` | Function to extract the rate limit key from a request | IP-based function |
| `exclude_paths` | Paths to exclude from rate limiting | `[]` |
| `headers_enabled` | Whether to include rate limit headers in responses | `True` |
| `error_message` | Error message to return when rate limit is exceeded | `"Rate limit exceeded. Please try again later."` |

## Rate Limiting Strategies

The ChronoWeave API supports two rate limiting strategies:

### Token Bucket Algorithm

The token bucket algorithm is a flexible and efficient rate limiting algorithm that allows for bursts of traffic while still enforcing a long-term rate limit. It works as follows:

1. Each client has a bucket that can hold a maximum number of tokens (the burst size).
2. Tokens are added to the bucket at a constant rate (the refill rate).
3. When a client makes a request, a token is removed from the bucket.
4. If the bucket is empty, the request is rejected.

This algorithm allows clients to make bursts of requests up to the burst size, but still enforces a long-term rate limit based on the refill rate.

### Custom Key Functions

The rate limiting middleware allows you to define custom key functions to determine how requests are grouped for rate limiting. The default key function uses the client IP address, but you can define custom key functions to use other identifiers, such as API keys or user IDs.

Example:

```python
async def api_key_func(request: Request) -> str:
    """Rate limit based on API key."""
    api_key = request.headers.get("X-API-Key", "")
    return f"api_key:{api_key}"
```

## Performance Considerations

The rate limiting middleware can impact performance, especially with large numbers of clients. Here are some considerations:

- **In-Memory Storage**: The current implementation uses in-memory storage, which is fast but does not scale across multiple instances. For production deployments with multiple instances, consider using a distributed rate limiting solution with a shared storage backend like Redis.

- **Cleanup Interval**: The in-memory rate limiter periodically cleans up expired buckets to prevent memory leaks. The cleanup interval can be configured to balance memory usage and performance.

- **Excluded Paths**: Use the `exclude_paths` option to exclude high-volume paths from rate limiting, such as health check endpoints.

## Security Considerations

The rate limiting middleware includes several security features:

- **IP-Based Rate Limiting**: The default key function uses the client IP address, which helps prevent abuse from individual clients.

- **X-Forwarded-For Support**: The middleware supports the X-Forwarded-For header, which is important when running behind a proxy or load balancer.

- **Tiered Rate Limiting**: Tiered rate limiting allows you to provide different levels of service to different clients, which can help prevent abuse while still allowing legitimate high-volume users.

## Best Practices

Here are some best practices for configuring the rate limiting middleware:

- **Start Conservative**: Start with conservative rate limits and adjust as needed based on actual usage patterns.

- **Monitor Rate Limit Exceeded Events**: Monitor the number of rate limit exceeded events to identify potential abuse or legitimate high-volume users who might need higher limits.

- **Use Tiered Rate Limiting**: Use tiered rate limiting to provide different levels of service to different clients, based on their needs and your business model.

- **Include Rate Limit Headers**: Include rate limit headers in responses to help clients understand and respect your rate limits.

- **Provide Clear Error Messages**: Provide clear error messages when rate limits are exceeded, including information about when the client can try again and how to request higher limits if needed.

- **Exclude Health Check Endpoints**: Exclude health check endpoints from rate limiting to prevent false positives in monitoring systems.

- **Consider Distributed Rate Limiting**: For production deployments with multiple instances, consider using a distributed rate limiting solution with a shared storage backend like Redis.
