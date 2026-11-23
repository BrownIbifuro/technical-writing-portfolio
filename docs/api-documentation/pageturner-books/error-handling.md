# Error Handling

This guide explains how the PageTurner Books API communicates errors and how developers can identify and resolve common request failures.

When an API request cannot be completed, the service returns an appropriate HTTP status code and a structured JSON error response.

Understanding these responses helps developers troubleshoot integration issues and build applications that handle unexpected situations gracefully.

> **Note:** PageTurner Books is a fictional API created for technical documentation practice. The error responses and behaviours described here represent a proposed API contract, not a live service.

## 1. Error Response Structure

The API uses a consistent JSON structure for error responses.

Each error response contains:

- An error code that identifies the type of failure.
- A human-readable message explaining what went wrong.

### Example

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication credentials are missing or invalid."
  }
}
```

### Error Fields

| Field | Type | Description |
|---|---|---|
| `error` | Object | Contains information about the request failure |
| `code` | String | Machine-readable identifier for the error |
| `message` | String | Human-readable explanation of the failure |

The HTTP status code is returned alongside the JSON response and communicates the outcome of the request.

## 2. HTTP Status Codes

The PageTurner Books API uses standard HTTP status codes to indicate whether a request succeeded or failed.

| Status Code | Name | Description |
|---|---|---|
| `200` | OK | Request completed successfully |
| `201` | Created | A new resource was created successfully |
| `400` | Bad Request | Request contains invalid or missing information |
| `401` | Unauthorized | Authentication credentials are missing or invalid |
| `403` | Forbidden | Client does not have permission to access the resource |
| `404` | Not Found | Requested resource does not exist |
| `409` | Conflict | Request conflicts with the current resource state |
| `429` | Too Many Requests | Client has exceeded the permitted request rate |
| `500` | Internal Server Error | An unexpected server-side error occurred |

The `200` and `201` status codes indicate successful operations. The remaining codes describe common request or server failures.

## 3. Common API Errors

### 400 Bad Request

This error occurs when a request contains invalid information or fails validation.

Possible causes include:

- Missing required request fields.
- Invalid parameter values.
- Incorrect data types.
- Invalid pagination values.

Example response:

```json
{
  "error": {
    "code": "BAD_REQUEST",
    "message": "The request contains invalid or missing parameters."
  }
}
```

**Recommended actions:**

1. Review the request URL and parameters.
2. Check that all required fields are provided.
3. Verify that values match the expected data types.
4. Submit the corrected request.

### 401 Unauthorized

This error indicates that the request does not contain valid authentication credentials.

Possible causes include:

- Missing `Authorization` header.
- Invalid API key.
- Incorrect bearer-token format.

Example response:

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication credentials are missing or invalid."
  }
}
```

**Recommended actions:**

1. Confirm that an API key has been provided.
2. Verify the key's validity.
3. Check that the `Authorization` header uses the `Bearer` scheme.
4. Retry the request with valid credentials.

See the [Authentication](authentication.md) guide for more information.

### 403 Forbidden

This error occurs when the client has been authenticated but does not have permission to access the requested resource or perform the requested operation.

Example response:

```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "You do not have permission to access this resource."
  }
}
```

**Recommended actions:**

1. Verify the permissions associated with the API key.
2. Confirm that the requested operation is permitted.
3. Contact the service provider if access should be granted.

### 404 Not Found

This error indicates that the requested resource could not be found.

Possible causes include:

- An incorrect book identifier.
- An invalid order identifier.
- A resource that does not exist.

Example response:

```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "The requested resource could not be found."
  }
}
```

**Recommended actions:**

1. Verify the resource identifier.
2. Check that the resource exists.
3. Confirm that the endpoint path is correct.

### 409 Conflict

This error occurs when a request conflicts with the current state of a resource.

For example, an order request may conflict with available inventory when the requested quantity exceeds the remaining stock.

Under the proposed API contract, an order is not created when the requested quantity exceeds available stock.

Example response:

```json
{
  "error": {
    "code": "CONFLICT",
    "message": "The requested quantity exceeds available stock."
  }
}
```

**Recommended actions:**

1. Check the current stock availability using the book availability endpoint.
2. Reduce the requested quantity if necessary.
3. Submit a new order request after verifying availability.

### 429 Too Many Requests

This error indicates that the client has exceeded the permitted request rate.

Example response:

```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please try again later."
  }
}
```

**Recommended actions:**

1. Reduce the frequency of API requests.
2. Avoid sending repeated requests in quick succession.
3. Wait before attempting another request.

The exact rate limits and retry policy are not specified in this sample API contract. A production implementation would need to document these details, including whether it provides a `Retry-After` response header.

### 500 Internal Server Error

This error indicates that the server encountered an unexpected problem while processing a request.

Example response:

```json
{
  "error": {
    "code": "INTERNAL_SERVER_ERROR",
    "message": "An unexpected error occurred while processing the request."
  }
}
```

**Recommended actions:**

1. Check whether the problem persists.
2. Retry the request cautiously where appropriate.
3. Contact the service provider if the error continues.

For requests that create resources, such as order creation, avoid automatically resubmitting the request without first checking its outcome. The sample API contract does not define idempotency or duplicate-request protection.

## 4. Error Handling Best Practices

Developers integrating with the PageTurner Books API should implement appropriate error-handling mechanisms.

### Check HTTP Status Codes

Always inspect the HTTP response status before assuming a request was successful.

For example, a `200 OK` response indicates success, while a `401 Unauthorized` response indicates an authentication problem.

### Parse Error Responses

Read the JSON error object to identify the specific problem.

Applications should use the error `code` for programmatic handling and the `message` to provide useful feedback to developers or users.

### Avoid Exposing Sensitive Information

Do not expose API keys, passwords, internal server details, or other sensitive information in application error messages or logs.

### Implement Appropriate Retries

Temporary server failures and rate-limit responses may require a retry strategy.

Use reasonable delays and follow any retry instructions explicitly documented by the API.

Do not blindly retry order-creation requests. A request may have been processed even if the client did not receive its response. Without an idempotency mechanism, resubmitting it could create a duplicate order.

### Validate Requests Before Submission

Check required fields, data types, and parameter values before sending requests.

This reduces avoidable validation errors and improves the integration experience.

## 5. Example: Handling API Errors with JavaScript

The following example demonstrates how a JavaScript application can check an API response and handle unsuccessful requests.

```javascript
async function getBooks() {
  try {
    const response = await fetch(
      "https://api.pageturnerbooks.example/v1/books",
      {
        headers: {
          "Authorization": "Bearer YOUR_API_KEY",
          "Accept": "application/json"
        }
      }
    );

    const result = await response.json();

    if (!response.ok) {
      const errorMessage =
        result.error?.message || "An unexpected error occurred.";

      throw new Error(
        `HTTP ${response.status}: ${errorMessage}`
      );
    }

    console.log("Books retrieved successfully:", result.data);

  } catch (error) {
    console.error("API request failed:", error.message);
  }
}

getBooks();
```

> **Security note:** This example illustrates error handling and request structure. In a real application, private API credentials should be stored on a secure backend rather than embedded in browser-delivered JavaScript.

### How the Example Works

1. The application sends a GET request to the books endpoint.
2. The response body is parsed as JSON.
3. The application checks the `response.ok` property.
4. If the request fails, it extracts the error message and throws an exception.
5. The `catch` block handles the failure.

This approach helps prevent unsuccessful responses from being treated as successful application data.

## 6. Troubleshooting Reference

The following table summarises common errors and recommended actions.

| HTTP Status | Error Code | Common Cause | Recommended Action |
|---|---|---|---|
| 400 | `BAD_REQUEST` | Invalid request parameters | Validate request fields |
| 401 | `UNAUTHORIZED` | Missing or invalid API key | Check authentication credentials |
| 403 | `FORBIDDEN` | Insufficient permissions | Review access permissions |
| 404 | `NOT_FOUND` | Resource does not exist | Verify the resource identifier |
| 409 | `CONFLICT` | Insufficient stock | Check inventory availability |
| 429 | `RATE_LIMIT_EXCEEDED` | Too many requests | Reduce request frequency |
| 500 | `INTERNAL_SERVER_ERROR` | Unexpected server failure | Retry cautiously or contact support |

## 7. Related Documentation

Continue exploring the PageTurner Books API:

- [Getting Started](getting-started.md) — Learn how to make your first request.
- [Authentication](authentication.md) — Understand API key authentication.
- [API Reference](reference.md) — Explore available endpoints.
- [Data Models](data-models.md) — Understand the API's data structures.

## Summary

Effective error handling is an important part of building reliable applications that integrate with APIs.

The PageTurner Books API uses standard HTTP status codes and structured JSON error responses to communicate request failures.

By validating requests, inspecting response codes, handling errors appropriately, and protecting sensitive information, developers can build more reliable integrations.

---

*This guide describes the proposed error-handling behaviour of a fictional bookstore API created for technical documentation portfolio purposes.*