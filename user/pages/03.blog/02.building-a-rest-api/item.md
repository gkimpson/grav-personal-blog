---
title: Building a REST API with Grav
date: '2026-06-27'
tags:
  - grav
  - api
  - rest
  - php
  - development
---

# Building a REST API with Grav

Grav 2.0 includes built-in REST API capabilities, making it easy to build modern applications that consume your Grav content as a service.

## What is a REST API?

REST (Representational State Transfer) is an architectural style for building web services that use HTTP requests to perform CRUD (Create, Read, Update, Delete) operations on resources.

## Enabling the API

The REST API is built into Grav 2.0. You can configure it in your system configuration:

```yaml
# system/config/system.yaml
api:
  enabled: true
  authentication: bearer
  rate_limit: 0
```

## API Endpoints

Grav provides several built-in endpoints:

### Get All Pages

```bash
curl -X GET \
  http://localhost:8000/api/pages \
  -H "Authorization: Bearer YOUR_TOKEN"
```

Response:

```json
{
  "status": 200,
  "data": [
    {
      "title": "Home",
      "path": "/home",
      "slug": "home",
      "date": "2026-06-28"
    },
    {
      "title": "Blog",
      "path": "/blog",
      "slug": "blog",
      "date": "2026-06-28"
    }
  ]
}
```

### Get a Specific Page

```bash
curl -X GET \
  http://localhost:8000/api/pages/blog \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## JavaScript Example

Here's how to fetch Grav content in a JavaScript frontend:

```javascript
const API_URL = 'http://localhost:8000/api';
const TOKEN = 'your-api-token';

async function fetchPages() {
  try {
    const response = await fetch(`${API_URL}/pages`, {
      headers: {
        'Authorization': `Bearer ${TOKEN}`,
        'Content-Type': 'application/json'
      }
    });
    
    const data = await response.json();
    console.log('Pages:', data.data);
    return data.data;
  } catch (error) {
    console.error('Error fetching pages:', error);
  }
}

// Use it
fetchPages();
```

## PHP Example

You can also consume the API from PHP:

```php
<?php
$url = 'http://localhost:8000/api/pages';
$token = 'your-api-token';

$context = stream_context_create([
    'http' => [
        'method' => 'GET',
        'header' => [
            'Authorization: Bearer ' . $token,
            'Content-Type: application/json'
        ]
    ]
]);

$response = file_get_contents($url, false, $context);
$pages = json_decode($response, true);

foreach ($pages['data'] as $page) {
    echo $page['title'] . "\n";
}
?>
```

## Authentication

For production use, configure API authentication:

```yaml
# system/config/system.yaml
api:
  enabled: true
  authentication: bearer
  rate_limit: 100
  routes: 
    - /api
```

Generate tokens for different applications and users to maintain security.

## Best Practices

- Always use authentication in production
- Implement rate limiting to prevent abuse
- Cache responses when possible
- Document your API endpoints
- Use meaningful HTTP status codes
- Version your API endpoints

## Learn More

- [Grav API Documentation](https://learn.getgrav.org/api)
- [REST API Best Practices](https://restfulapi.net)
- [Grav Security Guide](https://learn.getgrav.org/security)

Build amazing things with Grav's powerful API!
