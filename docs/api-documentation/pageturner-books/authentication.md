# Authentication

The PageTurner Books API uses bearer-token authentication to verify the identity of clients making API requests.

An API key must be included in the `Authorization` header of every authenticated request.

Requests without valid credentials may be rejected with an HTTP `401 Unauthorized` or `403 Forbidden` response.

> **Note:** PageTurner Books is a fictional API created for documentation practice. The authentication behaviour and examples in this guide describe a proposed API contract, not a live authentication service. No real API keys can currently be obtained.

## 1. Authentication Method

The API uses the HTTP Bearer authentication scheme.

Clients must provide their API key using the following header format:

```http
Authorization: Bearer YOUR_API_KEY
```

Replace `YOUR_API_KEY` with a valid credential issued by the service provider in a real implementation.

The API key identifies the client making the request and is used to determine whether the request is authorised.

The process for issuing, managing, and revoking API keys is outside the scope of this sample API contract.

## 2. Including the API Key in Requests

The API key must be included in the `Authorization` header when making an authenticated request.

### Example: cURL

```bash
curl -X GET "https://api.pageturnerbooks.example/v1/books" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Accept: application/json"
```

### Example: JavaScript

The following example uses the Fetch API to retrieve the book catalogue.

```javascript
const API_KEY = "YOUR_API_KEY";

async function getBooks() {
  const response = await fetch(
    "https://api.pageturnerbooks.example/v1/books",
    {
      method: "GET",
      headers: {
        "Authorization": `Bearer ${API_KEY}`,
        "Accept": "application/json"
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  const data = await response.json();

  console.log(data);
}

getBooks();
```

> **Security warning:** This JavaScript example illustrates the request format only. Do not embed a private API key in frontend JavaScript delivered to browsers. In a real application, make authenticated requests through a secure backend that stores and manages the credential.

## 3. Authentication Requirements

When making authenticated requests, observe the following requirements:

| Requirement | Description |
|---|---|
| Header name | `Authorization` |
| Authentication scheme | `Bearer` |
| Credential | A valid API key |
| Header format | `Bearer YOUR_API_KEY` |
| Credential placement | HTTP request header |

The API key should not be included in query parameters or request URLs.

## 4. Authentication Errors

The API uses HTTP status codes to communicate authentication and authorisation failures.

### 401 Unauthorized

This response indicates that the request does not contain valid authentication credentials.

Possible causes include:

- The `Authorization` header is missing.
- The API key is invalid.
- The bearer-token format is incorrect.

Example response:

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication credentials are missing or invalid."
  }
}
```

**Recommended action:** Verify that the API key is valid and that the `Authorization` header follows the required format.

### 403 Forbidden

This response indicates that the client has been authenticated but does not have permission to perform the requested operation.

Example response:

```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "You do not have permission to access this resource."
  }
}
```

**Recommended action:** Check the permissions associated with the API key or contact the service provider.

## 5. API Key Security Best Practices

API keys provide access to protected resources and should be handled securely.

Follow these recommendations when integrating with the PageTurner Books API:

1. **Keep API keys private.** Never publish credentials in public repositories or documentation examples.
2. **Use environment variables.** Store credentials outside application source code.
3. **Avoid frontend exposure.** Do not place private API keys in browser-delivered JavaScript.
4. **Use HTTPS.** Send authenticated requests over encrypted connections.
5. **Rotate compromised credentials.** Replace exposed API keys as soon as possible.
6. **Limit access.** Grant credentials only the permissions required by the application.

### Example: Using an environment variable in Python

Instead of writing an API key directly into your application, retrieve it from an environment variable.

```python
import os

api_key = os.getenv("PAGETURNER_API_KEY")

if not api_key:
    raise ValueError("API key is not configured.")

headers = {
    "Authorization": f"Bearer {api_key}",
    "Accept": "application/json"
}
```

This approach keeps the credential separate from the application source code. The `headers` dictionary can then be supplied to an HTTP client when making an API request.

## 6. Troubleshooting Authentication Issues

| Problem | Possible cause | Recommended solution |
|---|---|---|
| 401 Unauthorized | Missing API key | Include the `Authorization` header |
| 401 Unauthorized | Invalid credential | Verify the API key |
| 401 Unauthorized | Incorrect header format | Use `Bearer YOUR_API_KEY` |
| 403 Forbidden | Insufficient permissions | Check the key's access permissions |
| Requests fail unexpectedly | Incorrect API configuration | Verify the base URL and request headers |

## 7. Related Documentation

Continue exploring the PageTurner Books API:

- [Getting Started](getting-started.md) — Learn how to make your first request.
- [API Reference](reference.md) — Explore available endpoints.
- [Data Models](data-models.md) — Understand the API's data structures.
- [Error Handling](error-handling.md) — Review error responses and troubleshooting guidance.

## Summary

The PageTurner Books API uses bearer-token authentication to protect its resources.

Developers must include a valid API key in the `Authorization` header when making authenticated requests.

Following the authentication requirements and security recommendations in this guide helps developers integrate with the proposed API while protecting their credentials.