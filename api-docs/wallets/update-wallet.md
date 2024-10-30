# Update Wallet

`PUT /wallets/{walletId}`

Updates the name or externalID of an existing `Wallet.`&#x20;

{% hint style="info" %}
* User action signature required. See [User Action Signing](../authentication/user-action-signing/) for more information.
* Request headers required. See [Request Headers](../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name                                               | Conditions      |
| -------------------------------------------------- | --------------- |
| `Wallets:Update`<mark style="color:red;">\*</mark> | Always Required |

## Path parameters <a href="#path-parameters" id="path-parameters"></a>

| Path parameter | Description                                                              |
| -------------- | ------------------------------------------------------------------------ |
| `walletId`     | Unique identifier of the `Wallet`. ex. `wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx` |

## Request <a href="#request-body" id="request-body"></a>

<table><thead><tr><th width="203">Property</th><th width="184">Type - Optional</th><th>Description</th></tr></thead><tbody><tr><td><code>name</code></td><td>String - Optional</td><td>Name given to the wallet</td></tr><tr><td><code>externalid</code></td><td>String - Optional</td><td>External id usually used as a foreign key to an external database. </td></tr></tbody></table>

#### Example

```shell
{
  "name": "my-wallet-updated"
}
```

## Response <a href="#response" id="response"></a>

#### 200 Response example <a href="#response-example" id="response-example"></a>

```json
{
  "id": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "EthereumSepolia",
  "status": "Active",
  "name": "My awesome wallet",
  "address": "0x00e3495cf6af59008f22ffaf32d4c92ac33dac47",
  "dateCreated": "2023-04-14T20:41:28.715Z",
  "signingKey": {
    "curve": "secp256k1",
    "scheme": "ECDSA",
    "publicKey": "e2375c8c9e87bfcd0be8f29d76c818cabacd51584f72cb2222d49a13b036d84d3d"
  }
}
```
