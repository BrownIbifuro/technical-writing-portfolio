# PageTurner Books API

## Overview

The PageTurner Books API is a RESTful API designed to help developers integrate bookstore catalogue, inventory, and ordering functionality into their applications.

It provides programmatic access to book information, stock availability, and customer orders through predictable HTTP endpoints and JSON responses.

Developers can use the API to build bookstore websites, mobile applications, inventory dashboards, and other applications that require access to bookstore data.

**Important:** PageTurner Books is a fictional sample service created for technical documentation purposes. The endpoints and responses in this documentation illustrate a proposed API contract. They do not connect to a live backend.

## API Specification

The complete API contract is available in the
[OpenAPI specification](openapi.yaml).

## Base URL

All API requests use the following base URL:

```text
https://api.pageturnerbooks.example/v1
```

The `/v1` path identifies the first version of the API.

## Key Features

The API provides the following capabilities:

| Feature | Description |
|---|---|
| Book catalogue | Retrieve, search, and view book information |
| Inventory | Check the available stock of individual books |
| Order management | Place orders and retrieve order information |
| Authentication | Authenticate requests using an API key |
| Error handling | Receive structured error responses |

## API Conventions

The following conventions apply throughout the API:

- **Architecture:** REST
- **Data format:** JSON
- **HTTP methods:** GET and POST
- **Authentication:** Bearer API key
- **API version:** v1
- **Character encoding:** UTF-8

Requests that contain a JSON body must include the following header:

```http
Content-Type: application/json
```

Successful responses and error responses use standard HTTP status codes.

## Authentication

The API uses bearer-token authentication.

Developers must include a valid API key in the `Authorization` header when making authenticated requests.

Example:

```http
Authorization: Bearer YOUR_API_KEY
```

Replace `YOUR_API_KEY` with the API key issued by the service.

**Security note:** Never expose API keys in public repositories, client-side application code, or publicly accessible documentation examples containing real credentials.

## Available Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/books` | Retrieve a list of books |
| GET | `/books/{bookId}` | Retrieve a specific book |
| GET | `/books/{bookId}/availability` | Check a book's stock availability |
| POST | `/orders` | Create a customer order |
| GET | `/orders/{orderId}` | Retrieve a specific order |

See the [API Reference](reference.md) for detailed endpoint documentation.

## Example Request

The following example retrieves a list of books:

```bash
curl -X GET "https://api.pageturnerbooks.example/v1/books" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Accept: application/json"
```

## Example Response

A successful request returns a JSON response containing a list of books and pagination information.

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

*The response above is illustrative sample data.*

## Scope and Limitations

This documentation describes the proposed API contract and its expected behaviour.

The sample API covers catalogue retrieval, stock availability, and order management.

The following capabilities are outside its current scope:

- Customer registration and account management.
- Payment processing and refunds.
- Shipment tracking.
- Persistent backend implementation.
- Live API credentials and production access.

## Intended Audience

This documentation is intended for:

- Frontend developers integrating bookstore data into web applications.
- Mobile developers building bookstore applications.
- Backend developers integrating inventory and order functionality.
- Technical teams evaluating the API's capabilities and integration requirements.

## Next Steps

- Read [Getting Started](getting-started.md) to understand how to make your first request.
- Review the [API Reference](reference.md) for endpoint details.
- Explore [Authentication](authentication.md) to understand how requests are secured.
