# Health Check and Monitoring in ChronoWeave API

This document describes the health check and monitoring system in the ChronoWeave API, including health check endpoints, component health checks, and system metrics.

## Table of Contents

- [Overview](#overview)
- [Health Check Endpoints](#health-check-endpoints)
- [Component Health Checks](#component-health-checks)
- [System Metrics](#system-metrics)
- [Health Status](#health-status)
- [Configuration](#configuration)
- [Best Practices](#best-practices)

## Overview

The ChronoWeave API includes a comprehensive health check and monitoring system that provides detailed information about the health and performance of the API and its dependencies. The system includes:

- **Health Check Endpoints**: Endpoints for checking the health of the API and its components.
- **Component Health Checks**: Health checks for individual components, such as the database and storage system.
- **System Metrics**: Metrics about the system, such as CPU usage, memory usage, and disk usage.
- **Health Status**: Status information about the API and its components.

## Health Check Endpoints

The ChronoWeave API provides the following health check endpoints:

### Basic Health Check

```
GET /health
```

Returns a simple health check response indicating whether the API is running.

**Response:**

```json
{
  "status": "ok"
}
```

### Full Health Check

```
GET /health?full=true
```

Returns a detailed health check response with information about the API and its components.

**Response:**

```json
{
  "status": "ok",
  "version": "1.0.0",
  "environment": "production",
  "timestamp": "2023-06-15T14:30:00Z",
  "uptime": 3600,
  "components": [
    {
      "name": "api",
      "type": "api",
      "status": "ok",
      "message": "API is running",
      "details": {
        "response_time": 1.23
      },
      "last_checked": "2023-06-15T14:30:00Z"
    },
    {
      "name": "database",
      "type": "database",
      "status": "ok",
      "message": "Database is connected",
      "details": {
        "connection_pool": {
          "active": 1,
          "idle": 4,
          "max": 10
        },
        "response_time": 5.67
      },
      "last_checked": "2023-06-15T14:30:00Z"
    },
    {
      "name": "storage",
      "type": "storage",
      "status": "ok",
      "message": "Storage is accessible",
      "details": {
        "disk_usage": {
          "total": 1000000000,
          "used": 500000000,
          "free": 500000000,
          "percent": 50.0
        },
        "response_time": 3.45
      },
      "last_checked": "2023-06-15T14:30:00Z"
    }
  ],
  "metrics": {
    "cpu_usage": 10.0,
    "memory_usage": 20.0,
    "disk_usage": 30.0,
    "uptime": 3600,
    "load_average": [0.1, 0.2, 0.3],
    "process_count": 100,
    "thread_count": 10,
    "open_file_count": 20,
    "network_connections": 5
  }
}
```

### System Metrics

```
GET /health/metrics
```

Returns detailed system metrics.

**Response:**

```json
{
  "cpu_usage": 10.0,
  "memory_usage": 20.0,
  "disk_usage": 30.0,
  "uptime": 3600,
  "load_average": [0.1, 0.2, 0.3],
  "process_count": 100,
  "thread_count": 10,
  "open_file_count": 20,
  "network_connections": 5
}
```

## Component Health Checks

The ChronoWeave API performs health checks on the following components:

### API

Checks whether the API is running.

**Status:**
- `ok`: The API is running.
- `critical`: The API is not running.

### Database

Checks whether the database is connected and accessible.

**Status:**
- `ok`: The database is connected and accessible.
- `degraded`: The database is connected but experiencing issues.
- `critical`: The database is not connected or not accessible.

### Storage

Checks whether the storage system is accessible.

**Status:**
- `ok`: The storage system is accessible.
- `degraded`: The storage system is accessible but experiencing issues.
- `critical`: The storage system is not accessible.

## System Metrics

The ChronoWeave API provides the following system metrics:

- **CPU Usage**: The percentage of CPU usage.
- **Memory Usage**: The percentage of memory usage.
- **Disk Usage**: The percentage of disk usage.
- **Uptime**: The number of seconds the API has been running.
- **Load Average**: The system load average for the last 1, 5, and 15 minutes.
- **Process Count**: The number of processes running on the system.
- **Thread Count**: The number of threads in the current process.
- **Open File Count**: The number of open files in the current process.
- **Network Connections**: The number of network connections in the current process.

## Health Status

The ChronoWeave API uses the following health status values:

- **OK**: The component is healthy and functioning normally.
- **Degraded**: The component is functioning but experiencing issues.
- **Critical**: The component is not functioning or experiencing severe issues.
- **Unknown**: The component's health status is unknown.

The overall health status of the API is determined by the health status of its components:

- If any critical component has a status of `critical`, the overall status is `critical`.
- If any component has a status of `degraded` and no critical component has a status of `critical`, the overall status is `degraded`.
- Otherwise, the overall status is `ok`.

## Configuration

The health check system can be configured using the following environment variables:

- `CHRONOWEAVE_VERSION`: The version of the API (default: "unknown").
- `CHRONOWEAVE_ENV`: The environment the API is running in (default: "development").
- `HEALTH_INCLUDE_METRICS`: Whether to include system metrics in health check responses (default: "true").
- `HEALTH_CHECK_INTERVAL`: The interval in seconds between health checks (default: "60").
- `HEALTH_COMPONENT_TIMEOUT`: The timeout in seconds for component health checks (default: "5").

## Best Practices

Here are some best practices for using the health check and monitoring system:

### For API Operators

- **Monitor Health Endpoints**: Regularly monitor the health check endpoints to ensure the API is functioning properly.
- **Set Up Alerts**: Set up alerts for when the health status changes to `degraded` or `critical`.
- **Track Metrics**: Track system metrics over time to identify trends and potential issues.
- **Check Component Health**: Regularly check the health of individual components to identify issues early.
- **Configure Appropriately**: Configure the health check system appropriately for your environment.

### For API Clients

- **Check Health Before Use**: Check the health of the API before using it to ensure it's functioning properly.
- **Handle Degraded Status**: Handle degraded status appropriately, such as by retrying requests or using fallback mechanisms.
- **Respect Rate Limits**: Respect rate limits when checking health to avoid overloading the API.
- **Use Basic Health Check**: Use the basic health check endpoint for simple health checks.
- **Use Full Health Check Sparingly**: Use the full health check endpoint sparingly, as it's more resource-intensive.
