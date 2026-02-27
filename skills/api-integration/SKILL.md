---
name: API Integration
description: Connect to external APIs with proper authentication and error handling
version: 1.0.0
author: LLM Tools Project
tags: [api, integration, rest, graphql, authentication]
---

# API Integration Skill

This skill provides comprehensive guidance for integrating with external APIs, handling authentication, managing rate limits, and implementing robust error handling.

## Overview

API integration involves making HTTP requests to external services, handling various authentication methods, parsing responses, and managing errors gracefully. This skill covers REST APIs, GraphQL, and common integration patterns.

## When to Use This Skill

Use this skill when:
- ✅ You need to fetch data from external services
- ✅ You're integrating third-party functionality
- ✅ You need to send data to external systems
- ✅ You're building microservice integrations
- ✅ An API is available (preferred over web scraping)

Don't use this skill when:
- ❌ No API is available (use web-scraping skill instead)
- ❌ You don't have proper API credentials
- ❌ The API doesn't meet your data needs

## Prerequisites

**Required Libraries**:

```bash
# For REST APIs
pip install requests httpx

# For async operations
pip install aiohttp

# For GraphQL
pip install gql[all]

# For API testing
pip install responses pytest
```

**Tools**:
- Python 3.7+
- API credentials (keys, tokens, etc.)
- API documentation

## Instructions

### Step 1: Understand the API

Before integrating, review the API documentation:

1. **Authentication method**: API key, OAuth, JWT, etc.
2. **Base URL**: The root endpoint for all requests
3. **Endpoints**: Available routes and their purposes
4. **Request format**: Required headers, parameters, body structure
5. **Response format**: JSON, XML, etc.
6. **Rate limits**: Requests per minute/hour/day
7. **Error codes**: How errors are communicated

### Step 2: Set Up Authentication

```python
import requests
from typing import Dict

class APIClient:
    """Base API client with authentication."""
    
    def __init__(self, base_url: str, api_key: str):
        self.base_url = base_url.rstrip('/')
        self.session = requests.Session()
        self.session.headers.update({
            'Authorization': f'Bearer {api_key}',
            'Content-Type': 'application/json',
            'User-Agent': 'MyApp/1.0'
        })
    
    def _make_request(self, method: str, endpoint: str, **kwargs) -> Dict:
        """Make an authenticated request."""
        url = f"{self.base_url}/{endpoint.lstrip('/')}"
        response = self.session.request(method, url, **kwargs)
        response.raise_for_status()
        return response.json()
    
    def get(self, endpoint: str, params: Dict = None) -> Dict:
        """GET request."""
        return self._make_request('GET', endpoint, params=params)
    
    def post(self, endpoint: str, data: Dict = None) -> Dict:
        """POST request."""
        return self._make_request('POST', endpoint, json=data)
```

### Step 3: Implement Error Handling

```python
import requests
import time
from typing import Optional, Dict
import logging

logger = logging.getLogger(__name__)

class APIError(Exception):
    """Base exception for API errors."""
    pass

class RateLimitError(APIError):
    """Raised when rate limit is exceeded."""
    pass

class APIClient:
    def __init__(self, base_url: str, api_key: str, max_retries: int = 3):
        self.base_url = base_url
        self.api_key = api_key
        self.max_retries = max_retries
        self.session = requests.Session()
    
    def request_with_retry(self, method: str, endpoint: str, **kwargs) -> Dict:
        """Make request with automatic retries."""
        url = f"{self.base_url}/{endpoint}"
        
        for attempt in range(self.max_retries):
            try:
                response = self.session.request(
                    method, url,
                    headers={'Authorization': f'Bearer {self.api_key}'},
                    timeout=30,
                    **kwargs
                )
                
                # Handle rate limiting
                if response.status_code == 429:
                    retry_after = int(response.headers.get('Retry-After', 60))
                    logger.warning(f"Rate limited. Waiting {retry_after}s")
                    time.sleep(retry_after)
                    continue
                
                # Handle server errors with retry
                if 500 <= response.status_code < 600:
                    if attempt < self.max_retries - 1:
                        wait_time = 2 ** attempt
                        logger.warning(f"Server error. Retrying in {wait_time}s")
                        time.sleep(wait_time)
                        continue
                
                response.raise_for_status()
                return response.json()
                
            except requests.Timeout:
                logger.error(f"Request timeout (attempt {attempt + 1})")
                if attempt == self.max_retries - 1:
                    raise APIError("Request timed out after retries")
                    
            except requests.RequestException as e:
                logger.error(f"Request failed: {e}")
                if attempt == self.max_retries - 1:
                    raise APIError(f"Request failed: {e}")
        
        raise APIError("Max retries exceeded")
```

### Step 4: Handle Pagination

```python
def fetch_all_pages(client: APIClient, endpoint: str) -> list:
    """Fetch all pages of paginated results."""
    all_items = []
    page = 1
    
    while True:
        response = client.get(endpoint, params={'page': page, 'per_page': 100})
        
        items = response.get('items', [])
        if not items:
            break
        
        all_items.extend(items)
        
        # Check if there are more pages
        if not response.get('has_next', False):
            break
        
        page += 1
        time.sleep(0.5)  # Be polite with rate limiting
    
    return all_items
```

### Step 5: Implement Rate Limiting

```python
import time
from collections import deque
from datetime import datetime, timedelta

class RateLimiter:
    """Token bucket rate limiter."""
    
    def __init__(self, max_requests: int, time_window: int):
        """
        Args:
            max_requests: Maximum requests allowed
            time_window: Time window in seconds
        """
        self.max_requests = max_requests
        self.time_window = time_window
        self.requests = deque()
    
    def wait_if_needed(self):
        """Wait if rate limit would be exceeded."""
        now = datetime.now()
        
        # Remove old requests outside the time window
        cutoff = now - timedelta(seconds=self.time_window)
        while self.requests and self.requests[0] < cutoff:
            self.requests.popleft()
        
        # Check if we need to wait
        if len(self.requests) >= self.max_requests:
            sleep_time = (self.requests[0] - cutoff).total_seconds()
            if sleep_time > 0:
                time.sleep(sleep_time)
                self.requests.popleft()
        
        self.requests.append(now)

# Usage
rate_limiter = RateLimiter(max_requests=100, time_window=60)

def api_call():
    rate_limiter.wait_if_needed()
    # Make API request
    pass
```

## Examples

### Example 1: REST API Integration

**Scenario**: Fetch user data from a REST API

**Implementation**:
```python
import os
import requests
from typing import List, Dict

class UserAPIClient:
    """Client for user management API."""
    
    def __init__(self):
        self.base_url = "https://api.example.com/v1"
        self.api_key = os.getenv('API_KEY')
        self.session = requests.Session()
        self.session.headers.update({
            'Authorization': f'Bearer {self.api_key}',
            'Content-Type': 'application/json'
        })
    
    def get_user(self, user_id: int) -> Dict:
        """Get a single user by ID."""
        response = self.session.get(f"{self.base_url}/users/{user_id}")
        response.raise_for_status()
        return response.json()
    
    def list_users(self, page: int = 1, per_page: int = 50) -> List[Dict]:
        """List users with pagination."""
        response = self.session.get(
            f"{self.base_url}/users",
            params={'page': page, 'per_page': per_page}
        )
        response.raise_for_status()
        return response.json()['users']
    
    def create_user(self, user_data: Dict) -> Dict:
        """Create a new user."""
        response = self.session.post(
            f"{self.base_url}/users",
            json=user_data
        )
        response.raise_for_status()
        return response.json()
    
    def update_user(self, user_id: int, updates: Dict) -> Dict:
        """Update an existing user."""
        response = self.session.patch(
            f"{self.base_url}/users/{user_id}",
            json=updates
        )
        response.raise_for_status()
        return response.json()

# Usage
client = UserAPIClient()
user = client.get_user(123)
print(f"User: {user['name']}")
```

### Example 2: GraphQL API Integration

**Scenario**: Query data from a GraphQL API

**Implementation**:
```python
from gql import gql, Client
from gql.transport.requests import RequestsHTTPTransport

class GraphQLClient:
    """Client for GraphQL API."""
    
    def __init__(self, url: str, api_key: str):
        transport = RequestsHTTPTransport(
            url=url,
            headers={'Authorization': f'Bearer {api_key}'},
            verify=True,
            retries=3
        )
        self.client = Client(transport=transport, fetch_schema_from_transport=True)
    
    def get_user_repos(self, username: str) -> list:
        """Get user's repositories."""
        query = gql("""
            query GetUserRepos($username: String!) {
                user(login: $username) {
                    repositories(first: 10) {
                        nodes {
                            name
                            description
                            stargazerCount
                            url
                        }
                    }
                }
            }
        """)
        
        result = self.client.execute(query, variable_values={'username': username})
        return result['user']['repositories']['nodes']

# Usage
client = GraphQLClient('https://api.github.com/graphql', api_key='your_token')
repos = client.get_user_repos('octocat')
```

## Best Practices

### Do's
- ✅ **Use environment variables** for API keys and secrets
- ✅ **Implement retries** for transient failures
- ✅ **Handle rate limits** proactively
- ✅ **Validate responses** before using data
- ✅ **Log requests and errors** for debugging
- ✅ **Use timeouts** to prevent hanging requests
- ✅ **Cache responses** when appropriate
- ✅ **Use connection pooling** with `requests.Session()`

### Don'ts
- ❌ **Don't hardcode credentials** in source code
- ❌ **Don't ignore error responses**
- ❌ **Don't make synchronous calls in loops** (use async or batch)
- ❌ **Don't exceed rate limits**
- ❌ **Don't trust API responses** without validation
- ❌ **Don't log sensitive data** (tokens, passwords)
- ❌ **Don't use default timeouts** (set explicit values)

## Common Pitfalls

### Pitfall 1: Not Handling Rate Limits

**Problem**: API returns 429 Too Many Requests errors

**Solution**: Implement rate limiting and respect Retry-After headers

```python
if response.status_code == 429:
    retry_after = int(response.headers.get('Retry-After', 60))
    time.sleep(retry_after)
    # Retry request
```

### Pitfall 2: Ignoring Pagination

**Problem**: Only getting first page of results

**Solution**: Implement proper pagination handling

```python
def get_all_results(endpoint):
    results = []
    next_url = endpoint
    
    while next_url:
        response = requests.get(next_url)
        data = response.json()
        results.extend(data['items'])
        next_url = data.get('next_page_url')
    
    return results
```

### Pitfall 3: Poor Error Handling

**Problem**: Crashes on API errors

**Solution**: Handle different error types appropriately

```python
try:
    response = requests.get(url, timeout=10)
    response.raise_for_status()
except requests.Timeout:
    logger.error("Request timed out")
except requests.HTTPError as e:
    if e.response.status_code == 404:
        logger.warning("Resource not found")
    elif e.response.status_code == 401:
        logger.error("Authentication failed")
    else:
        logger.error(f"HTTP error: {e}")
except requests.RequestException as e:
    logger.error(f"Request failed: {e}")
```

## Security Considerations

- **Store credentials securely**: Use environment variables or secret managers
- **Use HTTPS**: Never send credentials over HTTP
- **Validate SSL certificates**: Don't disable verification in production
- **Rotate API keys**: Regularly update credentials
- **Limit permissions**: Use least-privilege API keys
- **Sanitize inputs**: Validate data before sending to APIs

## Performance Considerations

- **Use async for multiple requests**: `aiohttp` or `httpx` for concurrent calls
- **Implement caching**: Cache responses to reduce API calls
- **Batch requests**: Use batch endpoints when available
- **Connection pooling**: Reuse connections with `Session()`
- **Compress requests**: Use gzip encoding for large payloads

## Related Skills

- **web-scraping** - Fallback when no API is available
- **data-validation** - Validate API responses
- **error-handling** - Robust error management patterns

## Additional Resources

- [Requests Documentation](https://requests.readthedocs.io/)
- [REST API Best Practices](https://restfulapi.net/)
- [GraphQL Best Practices](https://graphql.org/learn/best-practices/)
- [API Security Checklist](https://github.com/shieldfy/API-Security-Checklist)

## Version History

- **1.0.0** (2026-02-16): Initial version
