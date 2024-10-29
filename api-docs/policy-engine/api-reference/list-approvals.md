# Get Approval

`GET /v2/policy-approvals/{approvalId}`

Get an [Approval](../policies.md#approval) object (created if a policy triggered requiring an Approval process action).

{% hint style="info" %}
* Request headers required. See [Request Headers](../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name                      | Conditions      |
| ------------------------- | --------------- |
| `Policies:Approvals:Read` | Always Required |

## Responses <a href="#response" id="response"></a>

### 200 - Approval Objects <a href="#response-example" id="response-example"></a>

Note: `TriggerStatus` of `Skipped` means the rule did not trigger for the given activity.

```json
{
  "id":"ap-...",
  "initiatorId":"us-...",
  "status":"Pending",
  "expirationDate":"2023-12-22T21:16:16.659Z",
  "dateCreated":"2023-12-22T20:56:16.662Z",
  "dateUpdated":"2023-12-22T20:56:16.662Z",
  "activity":{
    "kind": "Wallets:Sign",
    "transferRequest": { // the transfer request object from transfer endpoint
      "id": "xfr-...",
      ...
    },
  },
  "evaluatedPolicies":[
    {
      "policyId":"plc-...",
      "triggerStatus":"Triggered",
      "reason":"Number of transactions (2) is above limit (2)."
    },
    {
      "policyId":"plc-...",
      "triggerStatus":"Triggered",
      "reason":"Cumulative transfer amount (USD 20) is above limit (USD 2)."
    }
  ],
  "decisions":[
    {
      "userId":"us-...",
      "dateActioned":"2023-12-22T20:56:16.662Z",
      "value":"Approved"
    }
  ],
}
```
