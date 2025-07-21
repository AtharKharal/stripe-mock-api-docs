# Handling Events

The Stripe Mock API supports webhook-like event simulations, allowing developers to test asynchronous flows such as payment confirmations, failures, and refunds. This guide explains how to subscribe to events and validate payloads.

---

## 🧠 What Are Events?

Events are JSON objects that notify your application about state changes in the system. They are delivered to your configured webhook endpoint via HTTP POST requests.

Typical use cases:

- Listening for `charge.succeeded` to trigger order fulfillment.
- Reacting to `charge.failed` to notify users of payment issues.
- Logging `customer.deleted` for data retention compliance.

---

## 📡 Configuring a Webhook Endpoint

To receive events:

1. Run a local server or public endpoint.
2. Register it via the mock admin interface or by setting an environment variable.
3. Handle incoming JSON POST payloads.

```bash
export MOCK_WEBHOOK_URL=http://localhost:3000/webhook
````

---

## 🔔 Event Structure

Each event payload includes metadata and the full resource object that triggered it.

```json
{
  "id": "evt_001",
  "type": "charge.succeeded",
  "created": 1721505600,
  "data": {
    "object": {
      "id": "ch_001",
      "amount": 5000,
      "currency": "usd",
      "status": "succeeded"
    }
  }
}
```

### Fields

| Field     | Type    | Description                                    |
| --------- | ------- | ---------------------------------------------- |
| `id`      | string  | Unique ID for the event                        |
| `type`    | string  | Event type (e.g. `charge.failed`)              |
| `created` | integer | UNIX timestamp of event creation               |
| `data`    | object  | Contains the resource that triggered the event |

---

## 🎯 Supported Event Types

| Event Name         | Trigger                                 |
| ------------------ | --------------------------------------- |
| `charge.created`   | When a charge is initiated              |
| `charge.succeeded` | When a charge is successfully completed |
| `charge.failed`    | When a charge is declined               |
| `refund.created`   | When a refund is issued                 |
| `customer.deleted` | When a customer object is deleted       |

---

## 🔒 Verifying Event Signatures

To ensure the event was sent by the Stripe Mock API:

1. Each event includes a `Mock-Signature` header.
2. Compute HMAC-SHA256 using your webhook secret.
3. Compare the computed hash to the one in the header.

```python
import hmac
import hashlib

payload = request.body
secret = b'secret123'
signature = request.headers['Mock-Signature']

computed = hmac.new(secret, payload, hashlib.sha256).hexdigest()
assert computed == signature
```

---

## 🔁 Retry Logic

The Stripe Mock API simulates real-world reliability behavior:

* If your endpoint returns a non-2xx status code, the event will be retried up to 3 times with exponential backoff.
* Events are delivered in the order they occur, but delivery is not guaranteed to be strictly sequential under failure conditions.

---

## 🧪 Example: Listening to `charge.succeeded`

```javascript
app.post('/webhook', express.json(), (req, res) => {
  const event = req.body;

  if (event.type === 'charge.succeeded') {
    const charge = event.data.object;
    fulfillOrder(charge);
  }

  res.status(200).send();
});
```

---

## 🔗 Related Docs

* [Creating a Charge](../api/creating-a-charge.md)
* [Errors](../reference/errors.md)
* [Authentication](../getting-started/authentication.md)
* [OpenAPI Spec](../reference/openapi.md)

---

## 📌 Notes

* For webhook testing, tools like [ngrok](https://ngrok.com/) or [Webhook.site](https://webhook.site/) are recommended.
* Payloads conform to Stripe’s canonical structure, enabling easy migration to real Stripe APIs.
