# Pagination in ChronoWeave API

This document describes the pagination approach in the ChronoWeave API, including pagination parameters, response format, and examples.

## Table of Contents

- [Overview](#overview)
- [Pagination Parameters](#pagination-parameters)
- [Response Format](#response-format)
- [Examples](#examples)

## Overview

The ChronoWeave API uses a standardized approach to pagination to provide consistent and efficient access to large collections of data. This approach includes:

1. **Standardized Pagination Parameters**: All paginated endpoints use the same pagination parameters.
2. **Consistent Response Format**: All paginated responses follow a consistent format, making it easier for clients to handle paginated data.
3. **Metadata**: Pagination metadata is included in the response, making it easier for clients to navigate through pages.

## Pagination Parameters

All paginated endpoints support the following parameters:

- `page` (query, optional): Page number (1-based, default: 1)
- `page_size` (query, optional): Number of items per page (default: 10, max: 100)

Example:

```
GET /api/v1/tif/violations?page=2&page_size=20
```

This request will return the second page of violations, with 20 items per page.

## Response Format

All paginated responses follow this format:

```json
{
  "success": true,
  "data": [
    // List of items
  ],
  "metadata": {
    "page": 1,
    "page_size": 10,
    "total_items": 100,
    "total_pages": 10
  }
}
```

- `success`: Indicates whether the operation was successful.
- `data`: Contains the list of items for the current page.
- `metadata`: Contains pagination metadata.
  - `page`: The current page number.
  - `page_size`: The number of items per page.
  - `total_items`: The total number of items across all pages.
  - `total_pages`: The total number of pages.

## Examples

### Paginated Violations Endpoint

Request:

```
GET /api/v1/tif/violations?page=1&page_size=5
```

Response:

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
    },
    // More violations...
  ],
  "metadata": {
    "page": 1,
    "page_size": 5,
    "total_items": 25,
    "total_pages": 5
  }
}
```

### Filtered Violations Endpoint

Request:

```
GET /api/v1/tif/violations?page=1&page_size=5&axiom_id=A1&severity=high
```

Response:

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
    },
    // More violations with axiom_id=A1 and severity=high...
  ],
  "metadata": {
    "page": 1,
    "page_size": 5,
    "total_items": 10,
    "total_pages": 2
  }
}
```
