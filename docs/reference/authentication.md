# Authentication

All endpoints in the Stripe Mock API are secured via bearer token authentication. This ensures that only authorized clients can interact with the mock system, simulating real-world API security behavior.

---

## 🔐 Overview

Authentication is handled through a static, simulated **Secret API Key** included in the `Authorization` header of each request.

### Header Format

````http
Authorization: Bearer <your_api_key>
````

The API key should **never be exposed in frontend code**, just as in real production environments.

---

## 🗝️ Obtaining Your API Key

In the mock environment, predefined API keys are used:

| Environment | Key                    | Scope           |
| ----------- | ---------------------- | --------------- |
| Test        | `sk_test_51H8JFAKE...` | Full API access |
| Invalid     | `sk_invalid_key`       | Triggers 401    |

Use the test key for all documentation scenarios.

---

## 🧪 Sample Authenticated Request

```bash
curl https://api.stripe-mock.local/v1/customers \
  -H "Authorization: Bearer sk_test_51H8JFAKE..."
```

---

## ❌ Authentication Errors

Incorrect or missing credentials will result in an HTTP `401 Unauthorized` response:

```json
{
  "error": {
    "type": "authentication_error",
    "message": "Invalid API Key provided: sk_invalid_key"
  }
}
```

Refer to the [Error Codes](../reference/errors.md#authentication_error) for all possible authentication-related failures.

---

## 🔄 Token Rotation

Although tokens are static in this mock setup, production-grade APIs require token rotation and secrets management:

* Store API keys securely (e.g., in environment variables or secret managers).
* Never log or hard-code them.
* Rotate keys periodically.

---

## 🛡️ Authentication vs Authorization

This mock API models **authentication only**. For real-world scenarios, **authorization** (e.g., permission scopes, roles) may apply based on the token's context.

---

## 📘 Related Sections

* [Creating a Charge](../api/creating-a-charge.md)
* [Handling Events](../webhooks/handling-events.md)
* [Error Codes](../reference/errors.md)
* [OpenAPI Spec](../reference/openapi.md)

!!! note "Security Reminder"
The mock API uses static keys and does not simulate rate-limiting or fraud detection. For production-grade security practices, consult [Stripe's real API docs](https://stripe.com/docs/api/authentication).


