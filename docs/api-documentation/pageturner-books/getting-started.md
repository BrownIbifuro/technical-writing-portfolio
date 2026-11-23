# Getting Started

This guide explains how to prepare for integration with the PageTurner Books API and make your first API request.

By the end of this guide, you should understand the API's basic requirements, authentication format, request structure, and response format.

> **Note:** PageTurner Books is a fictional API created for documentation practice. The URL, credentials, and responses below are illustrative. They cannot be used to access a live service.

## Prerequisites

Before working with the API, ensure you have the following:

- A basic understanding of HTTP requests and REST APIs.
- Familiarity with JSON.
- An API client such as cURL, Postman, or a programming language capable of making HTTP requests.
- An understanding of bearer-token authentication.

**API credential note:** A real implementation would issue API keys through a developer portal or an authorised account representative. This sample API has no live authentication service, so no real API key can be obtained.

For documentation examples, use the placeholder:

```text
YOUR_API_KEY
```

## 1. Understand the Base URL

All requests to the PageTurner Books API use the following base URL:

```text
https://api.pageturnerbooks.example/v1
```

The `/v1` path identifies the API version.

Every endpoint documented in this reference is appended to this base URL.

For example, the endpoint for retrieving books is:

```http
GET /books
```

Its complete URL would be:

```text
https://api.pageturnerbooks.example/v1/books
```

The `.example` domain is reserved for illustrative documentation. It does not identify an operational API server.

## 2. Understand Authentication

The API uses bearer-token authentication.

In a real integration, developers would obtain an API key through the service provider's developer portal or an authorised account representative.

For this sample documentation, use the placeholder:

```text
YOUR_API_KEY
```

Include the credential in the `Authorization` header:

```http
Authorization: Bearer YOUR_API_KEY
```

For more information, see the [Authentication](authentication.md) guide.

## 3. Make Your First API Request

Let's retrieve the available books from the catalogue.

You can use cURL to construct the request from your terminal.

### Request

```bash
curl -X GET "https://api.pageturnerbooks.example/v1/books" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Accept: application/json"
```

> **Illustrative example:** This command demonstrates the expected request structure. It will not return a live response because the sample API has no backend.

### Understanding the request

| Component | Description |
|---|---|
| `GET` | HTTP method used to retrieve information |
| `/books` | Endpoint that provides the book catalogue |
| `Authorization` | Header containing the bearer API key |
| `Accept` | Indicates that the client expects a JSON response |

## 4. Understand the Response

A successful request returns a JSON object containing the requested book information and pagination metadata.

### Example Response

```json
{
  "data": [
    {
      "id": "book_001",
      "title": "The Pragmatic Programmer",
      "author": "David Thomas and Andrew Hunt",
      "isbn": "9780135957059",
      "price": 35.00,
      "currency": "USD",
      "genre": "Software Development"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "totalItems": 1,
    "totalPages": 1
  }
}
```

### Response Fields

| Field | Type | Description |
|---|---|---|
| `data` | Array | Contains the returned book records |
| `id` | String | Unique identifier for a book |
| `title` | String | Book title |
| `author` | String | Book author or authors |
| `isbn` | String | International Standard Book Number |
| `price` | Number | Book price |
| `currency` | String | Currency used for the price |
| `genre` | String | Book category |
| `pagination` | Object | Contains pagination information |
| `page` | Integer | Current page number |
| `limit` | Integer | Maximum number of records requested per page |
| `totalItems` | Integer | Total number of matching records |
| `totalPages` | Integer | Total number of available pages |

The `description` field is optional and may be included when retrieving an individual book through the `GET /books/{bookId}` endpoint.

## 5. Use Query Parameters

The books endpoint supports pagination and basic catalogue filtering.

For example, developers can request the second page of results:

```bash
curl -X GET "https://api.pageturnerbooks.example/v1/books?page=2&limit=10" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Accept: application/json"
```

The available query parameters are:

| Parameter | Type | Description | Default |
|---|---|---|---|
| `page` | Integer | Page of results to retrieve. Must be a positive integer. | `1` |
| `limit` | Integer | Maximum number of records per page. Must be a positive integer. | `20` |
| `genre` | String | Filter books by genre | None |
| `search` | String | Search book titles and author names | None |

For example, to retrieve books in the Software Development genre:

```http
GET /books?genre=Software%20Development
```

To search for books with a particular title or author:

```http
GET /books?search=programmer
```

The precise matching and case-sensitivity rules for these filters are implementation-defined.

## 6. Handle Common Responses

The API uses standard HTTP status codes to communicate the outcome of a request.

| Status Code | Meaning |
|---|---|
| `200 OK` | Request completed successfully |
| `201 Created` | A new resource was created successfully |
| `400 Bad Request` | Request contains invalid parameters or data |
| `401 Unauthorized` | Authentication credentials are missing or invalid |
| `403 Forbidden` | The authenticated client does not have permission |
| `404 Not Found` | Requested resource does not exist |
| `409 Conflict` | Request conflicts with the current resource state |
| `429 Too Many Requests` | API request limit has been exceeded |
| `500 Internal Server Error` | Unexpected server-side error |

These codes describe the broader API contract. Not every code applies to every endpoint.

For detailed explanations and error response examples, see [Error Handling](error-handling.md).

## 7. What's Next?

Now that you understand the basic request structure, you can explore the other sections of the API documentation:

- [Authentication](authentication.md) — Learn how API credentials are used.
- [API Reference](reference.md) — Explore available endpoints and their parameters.
- [Data Models](data-models.md) — Understand the structure of books, inventory records, and orders.
- [Error Handling](error-handling.md) — Learn how to interpret and troubleshoot API errors.

## Summary

You have now explored the basic integration workflow for the PageTurner Books API:

1. Understand the API base URL.
2. Understand the authentication requirements.
3. Include the authentication header in requests.
4. Construct an HTTP request.
5. Interpret the JSON response.

These fundamentals provide the starting point for understanding the remaining API endpoints.

---

*This guide documents an illustrative integration workflow for a fictional API. It does not describe a live service or provide working credentials.*