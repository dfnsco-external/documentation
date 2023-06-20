# List Public Keys

`GET /public-keys/`

Retrieves all `PublicKeys` in the org.

{% hint style="info" %}
* Request headers required. See [Request Headers](../../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name              | Conditions      |
| ----------------- | --------------- |
| `PublicKeys:Read` | Always Required |

## Response <a href="#response" id="response"></a>

### Response example <a href="#response-example" id="response-example"></a>

```json
{
   "items": [
   {
     "isEddsa": false,
     "id": "pk-shade-wisconsin-c28c38b2e8",
     "publicKey": "03834ac098b2ce68f230940c3d30d85cb0a84623063f0fcba0e64dacf5a825e91c",
  },
  ...
  ]
}
```