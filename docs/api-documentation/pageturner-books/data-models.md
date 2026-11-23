# Data Models

This page describes the data structures used by the PageTurner Books API.

Each model defines the fields returned by the API, their data types, and their intended purpose.

Understanding these structures helps developers interpret API responses and integrate the returned data into their applications.

> **Note:** PageTurner Books is a fictional API created for technical documentation practice. All models and examples describe a proposed API contract, not a live backend.

## 1. Book Object

The Book object represents an individual book in the bookstore catalogue.

It contains information such as the book's title, author, ISBN, price, and genre.

### Example

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

### Field Definitions

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | String | Yes | Unique identifier assigned to the book |
| `title` | String | Yes | Title of the book |
| `author` | String | Yes | Name of the author or authors |
| `isbn` | String | Yes | International Standard Book Number |
| `price` | Number | Yes | Current selling price of the book |
| `currency` | String | Yes | Currency used for the book price |
| `genre` | String | Yes | Category or genre of the book |
| `description` | String | No | Short description of the book |

**Implementation note:** The `description` field is included in the individual book response but may be omitted from the book catalogue response.

The `price` field represents a monetary amount, while `currency` identifies the currency in which that amount is expressed.

## 2. Inventory Availability Object

The Inventory Availability object provides information about the current stock level of a specific book.

It helps applications determine whether a book can be ordered.

### Example

```json
{
  "bookId": "book_001",
  "available": true,
  "quantityAvailable": 15,
  "stockStatus": "in_stock"
}
```

### Field Definitions

| Field | Type | Required | Description |
|---|---|---|---|
| `bookId` | String | Yes | Identifier of the associated book |
| `available` | Boolean | Yes | Indicates whether the book can currently be ordered |
| `quantityAvailable` | Integer | Yes | Number of units currently available |
| `stockStatus` | String | Yes | Describes the current stock condition |

### Stock Status Values

The `stockStatus` field supports the following illustrative values:

| Value | Description |
|---|---|
| `in_stock` | The book has available stock |
| `low_stock` | The remaining quantity is below the configured stock threshold |
| `out_of_stock` | No units are currently available |

The following consistency rules apply to these fields:

- `available` should be `true` when `quantityAvailable` is greater than zero.
- `available` should be `false` when `quantityAvailable` is zero.
- `stockStatus` should reflect the reported quantity.
- `out_of_stock` indicates that no units are available.

The exact threshold for `low_stock` is implementation-defined and is not specified by this sample contract.

## 3. Order Object

The Order object represents a customer's order submitted through the API.

It contains customer information, the books included in the order, the total amount, and the order's current status.

### Example

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

### Field Definitions

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | String | Yes | Unique identifier assigned to the order |
| `customerEmail` | String | Yes | Email address associated with the order |
| `items` | Array | Yes | Collection of books included in the order |
| `totalAmount` | Number | Yes | Total monetary value of the order |
| `currency` | String | Yes | Currency used for the order total |
| `status` | String | Yes | Current state of the order |
| `createdAt` | String | Yes | Date and time the order was created |

The `items` array contains one or more Order Item objects.

### Order Status Values

| Value | Description |
|---|---|
| `pending` | Order has been created and is awaiting further processing |

The current sample API defines `pending` as its initial order status. Additional order states would require further specification.

### Date and Time Format

The `createdAt` field uses the ISO 8601 date-time format in UTC.

Example:

```text
2026-11-20T10:30:00Z
```

The trailing `Z` indicates that the timestamp is expressed in Coordinated Universal Time (UTC).

## 4. Order Item Object

The Order Item object represents an individual book included in an order.

An order can contain multiple Order Item objects.

### Example

```json
{
  "bookId": "book_001",
  "title": "The Pragmatic Programmer",
  "quantity": 2,
  "unitPrice": 35.00
}
```

### Field Definitions

| Field | Type | Required | Description |
|---|---|---|---|
| `bookId` | String | Yes | Identifier of the ordered book |
| `title` | String | Yes | Title of the ordered book |
| `quantity` | Integer | Yes | Number of units ordered; must be a positive integer |
| `unitPrice` | Number | Yes | Price per unit at the time the order was created |

The `unitPrice` field represents the price associated with the order item when the order is created. It is expressed in the currency specified by the parent Order object.

For example, if a customer orders two books at USD 35.00 each:

```text
Quantity: 2
Unit price: USD 35.00
Total: USD 70.00
```

## 5. Pagination Object

The Pagination object provides metadata about paginated catalogue responses.

It allows developers to understand how many records are available and which page they are currently viewing.

### Example

```json
{
  "page": 1,
  "limit": 20,
  "totalItems": 45,
  "totalPages": 3
}
```

### Field Definitions

| Field | Type | Description |
|---|---|---|
| `page` | Integer | Current page number |
| `limit` | Integer | Maximum number of records requested per page |
| `totalItems` | Integer | Total number of matching records |
| `totalPages` | Integer | Total number of pages available |

For example, if there are 45 matching books and the requested page size is 20, the API returns three pages of results.

Developers can request another page by changing the `page` query parameter.

The `page` and `limit` values must be positive integers. Their default values are `1` and `20`, respectively.

## 6. Error Response Object

The Error Response object describes the structure returned when an API request fails.

It provides an error code and a human-readable message to help developers understand what went wrong.

### Example

```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication credentials are missing or invalid."
  }
}
```

### Field Definitions

| Field | Type | Description |
|---|---|---|
| `error` | Object | Contains information about the error |
| `code` | String | Machine-readable error identifier |
| `message` | String | Human-readable explanation of the error |

### Example Error Codes

| Error Code | Associated HTTP Status | Description |
|---|---|---|
| `BAD_REQUEST` | 400 | Request contains invalid or missing data |
| `UNAUTHORIZED` | 401 | Authentication credentials are missing or invalid |
| `FORBIDDEN` | 403 | Client does not have permission to access the resource |
| `NOT_FOUND` | 404 | Requested resource does not exist |
| `CONFLICT` | 409 | Request conflicts with the current resource state |
| `RATE_LIMIT_EXCEEDED` | 429 | Request limit has been exceeded |
| `INTERNAL_SERVER_ERROR` | 500 | Unexpected server-side error |

The HTTP status code communicates the outcome of the request, while the error code provides additional context about the failure.

For additional examples and troubleshooting guidance, see [Error Handling](error-handling.md).

## 7. Relationships Between Data Models

The PageTurner Books API uses several related data structures.

The following relationships describe how the models work together:

- A Book represents an individual catalogue item.
- An Inventory Availability object references a Book through `bookId`.
- An Order contains one or more Order Item objects.
- Each Order Item references a Book through `bookId`.
- A Pagination object accompanies a paginated catalogue response.
- An Error Response provides structured information when a request fails.

These relationships help developers understand how catalogue, inventory, and order information fit together.

## 8. Related Documentation

- [Getting Started](getting-started.md)
- [Authentication](authentication.md)
- [API Reference](reference.md)
- [Error Handling](error-handling.md)

---

*This page documents proposed data structures for a fictional bookstore API. The models are illustrative and do not represent records from a live application.*