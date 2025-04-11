# Request Logging Configuration Guide

This document provides detailed information about configuring the request logging middleware in the ChronoWeave API.

## Table of Contents

- [Overview](#overview)
- [Environment Variables](#environment-variables)
- [Code Configuration](#code-configuration)
- [Log Output Configuration](#log-output-configuration)
- [Performance Considerations](#performance-considerations)
- [Security Considerations](#security-considerations)
- [Best Practices](#best-practices)

## Overview

The request logging middleware in the ChronoWeave API is designed to be highly configurable to meet different logging needs. This guide explains how to configure the middleware for different environments and use cases.

## Environment Variables

The request logging middleware can be configured using the following environment variables:

| Variable | Description | Default | Options |
|----------|-------------|---------|---------|
| `LOG_LEVEL` | The logging level | `INFO` | `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL` |
| `LOG_FORMAT` | The logging format | `json` | `json`, `text` |
| `LOG_REQUEST_BODY` | Whether to log request bodies | `false` | `true`, `false` |
| `LOG_RESPONSE_BODY` | Whether to log response bodies | `false` | `true`, `false` |

Example:

```bash
# Enable request and response body logging
export LOG_REQUEST_BODY=true
export LOG_RESPONSE_BODY=true

# Set logging level to DEBUG
export LOG_LEVEL=DEBUG

# Use text format instead of JSON
export LOG_FORMAT=text
```

## Code Configuration

The request logging middleware can also be configured in code when adding it to the FastAPI app:

```python
from chronoweave.api.logging_middleware import add_request_logging_middleware

# Add request logging middleware with custom configuration
add_request_logging_middleware(
    app,
    log_request_body=True,
    log_response_body=True,
    exclude_paths=["/health", "/metrics", "/docs", "/redoc", "/openapi.json"],
    exclude_headers=["authorization", "x-api-key", "cookie"],
    log_level=logging.DEBUG,
    log_format="json"
)
```

### Configuration Options

| Option | Description | Default |
|--------|-------------|---------|
| `log_request_body` | Whether to log request bodies | `False` |
| `log_response_body` | Whether to log response bodies | `False` |
| `exclude_paths` | Paths to exclude from logging | `["/health", "/metrics", "/docs", "/redoc", "/openapi.json"]` |
| `exclude_headers` | Headers to exclude from logging | `["authorization", "x-api-key", "cookie"]` |
| `log_level` | Logging level | `logging.INFO` |
| `log_format` | Logging format | `"json"` |

## Log Output Configuration

The request logging middleware uses Python's standard logging module, which can be configured to output logs to different destinations:

### Console Output

```python
import logging

# Configure logging to output to console
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)
```

### File Output

```python
import logging

# Configure logging to output to file
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s",
    filename="chronoweave.log",
    filemode="a"
)
```

### Rotating File Output

```python
import logging
from logging.handlers import RotatingFileHandler

# Configure logging to output to rotating file
logger = logging.getLogger()
logger.setLevel(logging.INFO)

handler = RotatingFileHandler(
    "chronoweave.log",
    maxBytes=10485760,  # 10 MB
    backupCount=5
)
handler.setFormatter(logging.Formatter("%(asctime)s - %(name)s - %(levelname)s - %(message)s"))
logger.addHandler(handler)
```

## Performance Considerations

The request logging middleware can impact performance, especially when logging request and response bodies. Here are some considerations:

- **Body Logging**: Enabling `log_request_body` and `log_response_body` can significantly impact performance, especially for large requests and responses. Consider disabling these options in production environments.

- **Excluded Paths**: Use the `exclude_paths` option to exclude high-volume paths from logging, such as health check endpoints.

- **Logging Level**: Use a higher logging level (e.g., `INFO` or `WARNING`) in production environments to reduce the volume of logs.

- **Asynchronous Logging**: Consider using an asynchronous logging library like `aiologger` to reduce the impact of logging on request processing.

## Security Considerations

The request logging middleware includes several security features:

- **Header Redaction**: Sensitive headers like `authorization`, `x-api-key`, and `cookie` are redacted by default.

- **Body Redaction**: Consider implementing custom body redaction for sensitive fields in request and response bodies.

- **Path Exclusion**: Exclude sensitive paths from logging using the `exclude_paths` option.

- **Log Storage**: Ensure that logs are stored securely and access is restricted to authorized personnel.

## Best Practices

Here are some best practices for configuring the request logging middleware:

- **Development Environment**: In development environments, enable request and response body logging to help with debugging.

- **Production Environment**: In production environments, disable request and response body logging to improve performance and reduce log volume.

- **Sensitive Data**: Be careful about logging sensitive data. Use the `exclude_headers` option to redact sensitive headers and consider implementing custom body redaction for sensitive fields.

- **Log Rotation**: Use log rotation to prevent log files from growing too large.

- **Log Aggregation**: Consider using a log aggregation service like ELK (Elasticsearch, Logstash, Kibana) or Graylog to centralize and analyze logs.

- **Request ID**: Use the request ID for correlation across different services and log messages.

- **Monitoring**: Set up monitoring and alerting based on log messages, especially for error responses.
