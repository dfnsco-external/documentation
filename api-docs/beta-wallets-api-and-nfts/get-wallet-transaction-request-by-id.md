# Get Wallet Transaction Request by ID

`GET /wallets/{walletId}/transactions/{transactionId}`

Retrieves a Wallet Transaction Request by its ID.

{% hint style="info" %}
* Request headers required. See [Request Headers](../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name                      | Conditions      |
| ------------------------- | --------------- |
| `Wallets:ReadTransaction` | Always Required |

## Parameters <a href="#request-example.1" id="request-example.1"></a>

### Path parameters <a href="#path-parameters" id="path-parameters"></a>

| Path parameter  | Description                                                                         |
| --------------- | ----------------------------------------------------------------------------------- |
| `walletId`      | Unique identifier of the `Wallet`. ex.`wa-6lbvd-hjdu1-9rtppq7p4c87cns7`             |
| `transactionId` | Unique identifier of the transaction request. ex. `tx-1jbko-fmk8d-9iebm4la5m3c4j8v` |

## Response <a href="#response" id="response"></a>

### 200 Response example <a href="#response-example" id="response-example"></a>

```json
{
  "id": "tx-1jbko-fmk8d-9iebm4la5m3c4j8v",
  "walletId": "wa-6lbvd-hjdu1-9rtppq7p4c87cns7",
  "network": "ETH_SEPOLIA",
  "requester": {
    "kind": "CustomerEmployee",
    "userId": "us-3v1ag-v6b36-9r0pjasaiqt1d3q7",
    "tokenId": "to-7mkkj-c831n-9a7oj3kp8j5i5o9q",
    "appId": "ap-C3H2-H7-3c762njr9t96l9qto6snl5ca4r"
  },
  "requestBody": {
    "kind": "Evm",
    "to": "0xb282dc7cde21717f18337a596e91ded00b79b25f",
    "value": "1000000000"
  },
  "status": "Confirmed",
  "txHash": "0x1e62ce5cf14b026d8fe3b5fa6195857049ec22e55fe932c74598c21866c07f14",
  "fee": "31500000147000",
  "dateRequested": "2023-05-09T19:51:33.628Z",
  "dateBroadcasted": "2023-05-09T19:51:39.983Z",
  "dateConfirmed": "2023-05-09T19:51:48.000Z"
}
```