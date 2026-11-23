# API Reference

This reference documents the endpoints available in the PageTurner Books API.

It describes each endpoint's purpose, HTTP method, parameters, authentication requirements, request structure, response format, and possible error codes.

> **Important:** PageTurner Books is a fictional API developed as a technical documentation sample. All endpoints, payloads, credentials, and responses are illustrative. They do not connect to a live backend.

## Base URL

All API requests use the following base URL:

```text
https://api.pageturnerbooks.example/v1
```

## Authentication

All endpoints require bearer-token authentication.

Include your API key in the `Authorization` header:

```http
Authorization: Bearer YOUR_API_KEY
```

For details, see the [Authentication](authentication.md) guide.

## Common Request Headers

| Header | Required | Description |
|---|---|---|
| `Authorization` | Yes | Bearer API key |
| `Accept` | Recommended | Expected response format (`application/json`) |
| `Content-Type` | For requests with a body | Request format (`application/json`) |

---

# 1. Retrieve Books

Returns a paginated list of books available in the catalogue.

**Endpoint**

```http
GET /books
```

### Query Parameters

| Parameter | Type | Required | Default | Description |
|---|---|---|---|---|
| `page` | Integer | No | `1` | Page number to retrieve. Must be a positive integer. |
| `limit` | Integer | No | `20` | Maximum number of records to return per page. Must be a positive integer. |
| `genre` | String | No | None | Filters books by genre. |
| `search` | String | No | None | Searches book titles and author names. |

The `genre` parameter filters catalogue entries by their genre value. The `search` parameter matches text in a book's title or author name. The precise matching and case-sensitivity rules are implementation-defined.

### Example Request

```bash
curl -X GET "https://api.pageturnerbooks.example/v1/books?page=1&limit=20" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Accept: application/json"
```

### Successful Response

**Status:** `200 OK`

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

### Possible Errors

| Status | Description |
|---|---|
| `400 Bad Request` | Invalid pagination or query parameter |
| `401 Unauthorized` | Missing or invalid API key |
| `500 Internal Server Error` | Unexpected server error |

---

# 2. Retrieve a Single Book

Returns detailed information about a specific book using its unique identifier.

**Endpoint**

```http
GET /books/{bookId}
```

### Path Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `bookId` | String | Yes | Unique identifier of the requested book |

### Example Request

```bash
curl -X GET "https://api.pageturnerbooks.example/v1/books/book_001" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Accept: application/json"
```

### Successful Response

**Status:** `200 OK`

```json
{
  "id": "book_001",
  "title": "The Pragmatic Programmer",
  "author": "David Thomas and Andrew Hunt",
  "isbn": "9780135957059",
  "price": 35.00,
  "currency": "USD",
  "genre": "Software Development",
  "description": "A practical guide to software development principles and techniques."
}
```

### Possible Errors

| Status | Description |
|---|---|
| `401 Unauthorized` | Missing or invalid API key |
| `404 Not Found` | Book does not exist |
| `500 Internal Server Error` | Unexpected server error |

---

# 3. Check Book Availability

Returns the current stock availability of a specific book.

**Endpoint**

```http
GET /books/{bookId}/availability
```

### Path Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `bookId` | String | Yes | Unique identifier of the book |

### Example Request

```bash
curl -X GET "https://api.pageturnerbooks.example/v1/books/book_001/availability" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Accept: application/json"
```

### Successful Response

**Status:** `200 OK`

```json
{
  "bookId": "book_001",
  "available": true,
  "quantityAvailable": 15,
  "stockStatus": "in_stock"
}
```

### Response Fields

| Field | Type | Description |
|---|---|---|
| `bookId` | String | Identifier of the book |
| `available` | Boolean | Indicates whether the book is available |
| `quantityAvailable` | Integer | Number of units currently available |
| `stockStatus` | String | Current inventory status |

The `stockStatus` field can return the following illustrative values:

- `in_stock` — The book has available stock.
- `low_stock` — The remaining quantity is below the configured stock threshold.
- `out_of_stock` — No units are currently available.

The exact threshold for `low_stock` is implementation-defined and is not specified by this sample contract.

### Possible Errors

| Status | Description |
|---|---|
| `401 Unauthorized` | Missing or invalid API key |
| `404 Not Found` | Book does not exist |
| `500 Internal Server Error` | Unexpected server error |

---

# 4. Create an Order

Creates a new customer order containing one or more books.

**Endpoint**

```http
POST /orders
```

### Request Headers

```http
Authorization: Bearer YOUR_API_KEY
Content-Type: application/json
Accept: application/json
```

### Request Body

The request body must contain the customer's email address and at least one book item.

| Field | Type | Required | Description |
|---|---|---|---|
| `customerEmail` | String | Yes | Customer's email address |
| `items` | Array | Yes | Books included in the order |
| `items[].bookId` | String | Yes | Identifier of the requested book |
| `items[].quantity` | Integer | Yes | Number of units requested. Must be a positive integer. |

### Example Request

```bash
curl -X POST "https://api.pageturnerbooks.example/v1/orders" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "customerEmail": "customer@example.com",
    "items": [
      {
        "bookId": "book_001",
        "quantity": 2
      }
    ]
  }'
```

### Successful Response

**Status:** `201 Created`

```json
{
  "id": "order_001",
  "customerEmail": "customer@example.com",
  "items": [
    {
      "bookId": "book_001",
      "title": "The Pragmatic Programmer",
      "quantity": 2,
      "unitPrice": 35.00
    }
  ],
  "totalAmount": 70.00,
  "currency": "USD",
  "status": "pending",
  "createdAt": "2026-11-20T10:30:00Z"
}
```

### Possible Errors

| Status | Description |
|---|---|
| `400 Bad Request` | Missing or invalid request fields |
| `401 Unauthorized` | Missing or invalid API key |
| `404 Not Found` | One or more requested books do not exist |
| `409 Conflict` | Requested quantity exceeds available stock |
| `500 Internal Server Error` | Unexpected server error |

### Important Behaviour

The proposed API contract assumes that an order can only be created when all requested books exist and sufficient stock is available.

If any requested quantity exceeds available inventory, the API returns `409 Conflict` and does not create the order.

The order starts with a `pending` status. Payment processing is outside the scope of this API.

---

# 5. Retrieve an Order

Returns the details of an existing order using its unique identifier.

**Endpoint**

```http
GET /orders/{orderId}
```

### Path Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `orderId` | String | Yes | Unique identifier of the order |

### Example Request

```bash
curl -X GET "https://api.pageturnerbooks.example/v1/orders/order_001" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Accept: application/json"
```

### Successful Response

**Status:** `200 OK`

```json
{
  "id": "order_001",
  "customerEmail": "customer@example.com",
  "items": [
    {
      "bookId": "book_001",
      "title": "The Pragmatic Programmer",
      "quantity": 2,
      "unitPrice": 35.00
    }
  ],
  "totalAmount": 70.00,
  "currency": "USD",
  "status": "pending",
  "createdAt": "2026-11-20T10:30:00Z"
}
```

### Possible Errors

| Status | Description |
|---|---|
| `401 Unauthorized` | Missing or invalid API key |
| `404 Not Found` | Order does not exist |
| `500 Internal Server Error` | Unexpected server error |

---

# HTTP Status Code Summary

The following status codes are defined in the PageTurner Books API contract.

| Status Code | Name | Meaning |
|---|---|---|
| `200` | OK | Request completed successfully |
| `201` | Created | A new resource was created |
| `400` | Bad Request | Request contains invalid data |
| `401` | Unauthorized | Authentication is missing or invalid |
| `403` | Forbidden | Client is authenticated but lacks permission |
| `404` | Not Found | Requested resource does not exist |
| `409` | Conflict | Request conflicts with the current resource state |
| `429` | Too Many Requests | Request limit exceeded |
| `500` | Internal Server Error | Unexpected server-side failure |

The `403` and `429` responses represent general API-level error conditions. Their specific triggering rules and endpoint-level application are not defined in this sample contract.

For error response structures and troubleshooting instructions, see [Error Handling](error-handling.md).

## Related Documentation

- [Getting Started](getting-started.md)
- [Authentication](authentication.md)
- [Data Models](data-models.md)
- [Error Handling](error-handling.md)

---

*This reference describes a fictional API contract for demonstration and technical documentation portfolio purposes. It should not be interpreted as documentation for an operational service.*