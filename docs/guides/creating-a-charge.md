# Creating a Charge

Creating a charge is the core operation of any payment system. This guide explains how to initiate a payment using the Stripe Mock API by issuing a POST request to the `/v1/charges` endpoint.

---

## 🔐 Authentication

All charge creation requests require a valid API key passed via the `Authorization` header:

````http
Authorization: Bearer sk_test_51H8J...
````

Refer to the [Authentication Reference](../reference/authentication.md) for details on token scopes and expiry.

---

## 📤 Endpoint: `POST /v1/charges`

### Description

Creates a new payment charge against a customer's payment method.

### HTTP Request

````http
POST /v1/charges
Content-Type: application/x-www-form-urlencoded
Authorization: Bearer <API_KEY>
````

### Request Parameters

| Name          | Type    | Required | Description                                                        |
| ------------- | ------- | -------- | ------------------------------------------------------------------ |
| `amount`      | integer | Yes      | Charge amount in the smallest currency unit (e.g., cents for USD). |
| `currency`    | string  | Yes      | ISO currency code (e.g., `usd`, `eur`).                            |
| `source`      | string  | Yes      | A valid payment source (e.g., a test card token).                  |
| `description` | string  | No       | Optional charge description for internal use.                      |

---

## 🧪 Example Request

````bash
curl https://api.stripe-mock.local/v1/charges \
  -u sk_test_51H8J...: \
  -d amount=2000 \
  -d currency=usd \
  -d source=tok_visa \
  -d description="Test charge for user onboarding"
````

---

## ✅ Example Response

````json
{
  "id": "ch_mock_1HX9sz2eZvKYlo2CxYqz",
  "object": "charge",
  "amount": 2000,
  "currency": "usd",
  "paid": true,
  "status": "succeeded",
  "created": 1628709103,
  "description": "Test charge for user onboarding",
  "source": {
    "id": "tok_visa",
    "object": "card",
    "brand": "Visa",
    "last4": "4242",
    "exp_month": 12,
    "exp_year": 2025
  }
}
````

---

## ⚠️ Error Handling

Expect `4xx` responses for malformed requests or invalid tokens.

* **400 Bad Request**: Missing or invalid parameters
* **401 Unauthorized**: Invalid or missing API key
* **402 Payment Required**: Simulated card decline

For full error semantics, refer to the [Error Codes Reference](../reference/errors.md).

---

## 📩 Webhook Integration

Upon successful charge creation, a `charge.succeeded` event will be dispatched if you have configured a webhook endpoint.

````json
{
  "type": "charge.succeeded",
  "data": {
    "object": { ...charge object... }
  }
}
````

See [Handling Events](../webhooks/handling-events.md) for event formats and testing instructions.

---

## 📘 Related Topics

* [Authentication](../reference/authentication.md)
* [Handling Events](../webhooks/handling-events.md)
* [Error Codes](../reference/errors.md)
* [OpenAPI Spec](../reference/openapi.md)

> This mock charge flow is ideal for UI prototyping, webhook testing, and demonstrating end-to-end payment logic without actual transactions.


