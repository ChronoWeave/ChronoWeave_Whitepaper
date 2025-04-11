# ChronoWeave Profiling Endpoints

## Overview

ChronoWeave provides a set of API endpoints for profiling and performance monitoring. These endpoints allow you to collect, analyze, and manage profiling data to identify performance bottlenecks and optimize your application.

## Prerequisites

To use the profiling endpoints, you need to:

1. Enable the profiling middleware by setting `ENABLE_PROFILING=true`
2. Have admin access to the API (requires an API key with admin privileges)

## Endpoints

### Get Profiling Data

Retrieves profiling data for functions and endpoints.

```
GET /api/v1/profiling/data
```

#### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| function_name | string | (Optional) Filter by function name |

#### Response

```json
{
  "data": {
    "GET /api/v1/recall": {
      "calls": 10,
      "total_time": 2.5,
      "min_time": 0.1,
      "max_time": 0.5,
      "avg_time": 0.25
    },
    "POST /api/v1/observe": {
      "calls": 5,
      "total_time": 1.0,
      "min_time": 0.15,
      "max_time": 0.3,
      "avg_time": 0.2
    }
  }
}
```

#### Example

```bash
curl -X GET "http://localhost:8000/api/v1/profiling/data" \
  -H "X-API-Key: your-api-key"
```

### Reset Profiling Data

Resets all collected profiling data.

```
POST /api/v1/profiling/reset
```

#### Response

No content (204)

#### Example

```bash
curl -X POST "http://localhost:8000/api/v1/profiling/reset" \
  -H "X-API-Key: your-api-key"
```

### Save Profiling Data

Saves profiling data to a file.

```
POST /api/v1/profiling/save
```

#### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| file_path | string | Path to save the profiling data |

#### Response

```json
{
  "message": "Profiling data saved to profiling_data.json"
}
```

#### Example

```bash
curl -X POST "http://localhost:8000/api/v1/profiling/save?file_path=profiling_data.json" \
  -H "X-API-Key: your-api-key"
```

## Usage Examples

### Collecting Profiling Data

To collect profiling data:

1. Enable profiling:
   ```bash
   ENABLE_PROFILING=true python -m chronoweave.api.main
   ```

2. Generate some load on the API (e.g., using the `profile_api.py` script)

3. Retrieve the profiling data:
   ```bash
   curl -X GET "http://localhost:8000/api/v1/profiling/data" \
     -H "X-API-Key: your-api-key" > profiling_results.json
   ```

### Analyzing Profiling Data

To analyze profiling data:

```python
import json
import matplotlib.pyplot as plt

# Load profiling data
with open("profiling_results.json", "r") as f:
    data = json.load(f)["data"]

# Extract endpoint names and average times
endpoints = []
avg_times = []

for endpoint, stats in data.items():
    if endpoint.startswith("GET") or endpoint.startswith("POST"):
        endpoints.append(endpoint)
        avg_times.append(stats["avg_time"])

# Sort by average time
sorted_data = sorted(zip(endpoints, avg_times), key=lambda x: x[1], reverse=True)
endpoints = [x[0] for x in sorted_data]
avg_times = [x[1] for x in sorted_data]

# Plot the results
plt.figure(figsize=(12, 6))
plt.bar(endpoints[:10], avg_times[:10])
plt.xlabel("Endpoint")
plt.ylabel("Average Time (s)")
plt.title("Top 10 Slowest Endpoints")
plt.xticks(rotation=45, ha="right")
plt.tight_layout()
plt.savefig("endpoint_performance.png")
```

### Continuous Monitoring

For continuous monitoring:

1. Set up a cron job to periodically save profiling data:
   ```bash
   # Save profiling data every hour
   0 * * * * curl -X POST "http://localhost:8000/api/v1/profiling/save?file_path=profiling_$(date +\%Y\%m\%d\%H).json" -H "X-API-Key: your-api-key"
   ```

2. Reset profiling data periodically to avoid memory growth:
   ```bash
   # Reset profiling data every day
   0 0 * * * curl -X POST "http://localhost:8000/api/v1/profiling/reset" -H "X-API-Key: your-api-key"
   ```

3. Analyze the collected data to identify trends and bottlenecks

## Security Considerations

The profiling endpoints require admin privileges to access. Make sure to:

1. Use a secure API key with admin privileges
2. Enable HTTPS for all API communications
3. Restrict access to the profiling endpoints to trusted users
4. Be careful when sharing profiling data, as it may contain sensitive information

## Performance Impact

The profiling middleware has a small performance impact:

- ~0.5ms overhead per request
- Additional memory usage for storing profiling data

To minimize the impact:

1. Only enable profiling when needed
2. Reset profiling data periodically
3. Exclude frequently called endpoints from profiling

## Troubleshooting

### Common Issues

1. **Profiling data not available**
   - Make sure `ENABLE_PROFILING=true` is set
   - Check that you have admin privileges
   - Verify that the profiling middleware is loaded

2. **High memory usage**
   - Reset profiling data periodically
   - Exclude frequently called endpoints from profiling

3. **Slow API responses with profiling enabled**
   - Consider disabling profiling in production
   - Use sampling to reduce the number of profiled requests

### Getting Help

If you encounter issues with the profiling endpoints, you can:

1. Check the server logs for error messages
2. Consult the ChronoWeave documentation
3. Contact the ChronoWeave support team
