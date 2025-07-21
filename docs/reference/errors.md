# Error Codes

The Stripe Mock API follows a consistent, JSON-based error response format to help developers gracefully handle problems during integration. This section outlines the standard error structure, supported error types, and examples of failure responses.

---

## 🧩 Error Object Structure

All errors are returned with HTTP status codes and a top-level `error` object:

````json
{
  "error": {
    "type": "invalid_request_error",
    "message": "Missing required parameter: amount",
    "param": "amount"
  }
}
````

### Fields

| Field     | Type   | Description                                         |
| --------- | ------ | --------------------------------------------------- |
| `type`    | string | Category of error (`api_error`, `card_error`, etc.) |
| `message` | string | Human-readable explanation of the error             |
| `param`   | string | Parameter causing the issue, if applicable          |

---

## 🧱 Error Types

| Type                    | HTTP Status | Description                             |
| ----------------------- | ----------- | --------------------------------------- |
| `invalid_request_error` | 400         | Missing or malformed parameters         |
| `authentication_error`  | 401         | Invalid or missing API key              |
| `permission_error`      | 403         | Attempted access to forbidden resources |
| `not_found_error`       | 404         | Resource does not exist                 |
| `rate_limit_error`      | 429         | Too many requests in a short time       |
| `api_error`             | 500         | Internal API error                      |
| `card_error`            | 402         | Failed card-related operations (mocked) |

---

## 🔁 Sample Responses

### Missing Parameter

````json
{
  "error": {
    "type": "invalid_request_error",
    "message": "Missing required parameter: currency",
    "param": "currency"
  }
}
````

### Invalid API Key

````json
{
  "error": {
    "type": "authentication_error",
    "message": "Invalid API Key provided: sk_invalid_key"
  }
}
````

### Card Declined (Mock Scenario)

````json
{
  "error": {
    "type": "card_error",
    "message": "Your card was declined.",
    "code": "card_declined"
  }
}
````

---

## 🧠 Handling Strategies

!!! tip "Graceful Degradation"
Always display user-friendly messages on client-side apps and log detailed errors server-side.

!!! important "Retry Logic"
Only retry for `api_error` or `rate_limit_error` types. Never retry authentication or validation failures.

---

## 🧪 See Also

* [Authentication](../getting-started/authentication.md)
* [Creating a Charge](../api/creating-a-charge.md)
* [Handling Events](../api/handling-events.md)
* [OpenAPI Reference](../reference/openapi.md)

---

## 🛠️ Developer Notes

* All error responses are deterministic and mockable.
* Use HTTP status codes programmatically; reserve the `message` field for debugging and UX copy.


