# Get Policy

`GET /v2/policies/{policyId}`

{% hint style="info" %}
* Request headers required. See [Request Headers](../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name            | Conditions      |
| --------------- | --------------- |
| `Policies:Read` | Always Required |

## Response <a href="#response" id="response"></a>

### 200 - Policy object <a href="#response-example" id="response-example"></a>

```json
{
  "id": "plc-...",
  "name": "Transfer Limit",
  "status": "Active",
  "activityKind": "Wallets:Sign",
  "rule": {
    "kind": "TransactionAmountLimit",
    "configuration": {
      "currency": "USD",
      "limit": "1000"
    }
  },
  "action": {
    "kind": "RequestApproval",
    "autoRejectTimeout": 60,
    "approvalGroups": [
      {
        "name": "Admins",
        "quorum": 2,
        "approvers": {
          "userId": {
            "in": ["us-...1", "us-...2", "us-...3"]
          }
        }
      }
    ],
  },
  "filters": {
    "walletId": {
      "in": ["wa-...1", "wa-...2"]
    }
  },
}
```
