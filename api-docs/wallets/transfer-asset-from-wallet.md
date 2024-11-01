# Transfer Asset from Wallet

`POST /wallets/{walletId}/transfers`

Transfer an asset out of the specified wallet to a destination address. For all fungible token transfers, the transfer amount must be specified in the minimum denomination of that token. For example, use the amount in `Satoshi` for a Bitcoin transfer, or the amount in `Wei` for an Ethereum transfer etc.

{% hint style="info" %}
* User action signature required. See [User Action Signing](../authentication/user-action-signing/) for more information.
* Request headers required. See [Request Headers](../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name                    | Conditions      |
| ----------------------- | --------------- |
| `Wallets:TransferAsset` | Always Required |

## Parameters <a href="#parameters" id="parameters"></a>

### Path parameters <a href="#path-parameters" id="path-parameters"></a>

| Path parameter | Description                                                              |
| -------------- | ------------------------------------------------------------------------ |
| `walletId`     | Unique identifier of the `Wallet`. ex. `wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx` |

## Native Token <a href="#native-token" id="native-token"></a>

Transfer the native token of the network. All networks support the native token type.

### Request body <a href="#native-token-request-body" id="native-token-request-body"></a>

<table><thead><tr><th width="174">Property</th><th width="186">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td><code>Native</code></td></tr><tr><td><code>to</code><mark style="color:red;">*</mark></td><td>String</td><td>The destination address</td></tr><tr><td><code>amount</code><mark style="color:red;">*</mark></td><td>String</td><td>The amount of native tokens to transfer in minimum denomination</td></tr><tr><td><code>priority</code></td><td>(Optional) String</td><td>The priority that determines the fees paid for the transfer [1]</td></tr><tr><td><code>memo</code></td><td>(Optional) String</td><td>The memo or destination tag [2]</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>



1. All EVM compatible networks and Bitcoin support `priority`. Not supported for other networks. The accepted values are `Slow`, `Standard` and `Fast`. When specified, it uses the [estimate fees](../networks/estimate-fees.md) API to calculate the transfer fees. When not specified, the transfer will use the fees returned from the blockchain node providers.
2. Stellar and XrpLedger support `memo`. Not valid for other networks.

### Sample request body <a href="#sample-native-token-request" id="sample-native-token-request"></a>

```json
{
  "kind": "Native",
  "to": "0xb282dc7cde21717f18337a596e91ded00b79b25f",
  "amount": "1000000000"
}
```

### 200 response example <a href="#native-token-response-example" id="native-token-response-example"></a>

```json
{
  "id": "xfr-1vs8g-c1ub1-xxxxxxxxxxxxxxxx",
  "walletId": "wa-39abb-e9kpk-xxxxxxxxxxxxxxxx",
  "network": "Ethereum",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Native",
    "to": "0xb282dc7cde21717f18337a596e91ded00b79b25f",
    "amount": "1000000000"
  },
  "metadata": {
    "asset": {
      "symbol": "ETH",
      "decimals": 18,
      "verified": true,
      "quotes": {
        "EUR": 2802.867647101728,
        "USD": 3020.82462287215
      }
    }
  },
  "dateRequested": "2023-05-08T19:14:25.568Z",
  "status": "Pending"
}
```

## Algorand <a href="#algorand" id="algorand"></a>

### Algorand Standard Assets <a href="#alogrand-asa" id="alogrand-asa"></a>

Transfer Algorand standard assets, or [ASAs](https://developer.algorand.org/docs/get-details/asa/).

#### Request body <a href="#asa-request-body" id="asa-request-body"></a>

| Request body fields | Required/Optional | Description                                              | Type   |
| ------------------- | ----------------- | -------------------------------------------------------- | ------ |
| `kind`              | Required          | `Asa`                                                    | String |
| `assetId`           | Required          | The asset ID of the token                                | String |
| `to`                | Required          | The destination address                                  | String |
| `amount`            | Required          | The amount of tokens to transfer in minimum denomination | String |

#### Sample request body <a href="#asa-token-request" id="asa-token-request"></a>

```json
{
  "kind": "Asa",
  "assetId": "31566704",
  "to": "FRZP423Y7MNMTG4OOLESESTPCFGGHZMY7QN462YEQAJK5H6EOMFHZG73UA",
  "amount": "1000000"
}
```

#### 200 response example <a href="#asa-response-example" id="asa-response-example"></a>

```json
{
  "id": "xfr-22e36-7p55v-xxxxxxxxxxxxxxxx",
  "walletId": "wa-39abb-e9kpk-xxxxxxxxxxxxxxxx",
  "network": "Algorand",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Asa",
    "assetId": "31566704",
    "to": "FRZP423Y7MNMTG4OOLESESTPCFGGHZMY7QN462YEQAJK5H6EOMFHZG73UA",
    "amount": "1000000"
  },
  "metadata": {
    "asset": {
      "symbol": "USDC",
      "decimals": 6,
      "verified": true
    }
  },
  "dateRequested": "2024-05-10T14:35:55.768Z",
  "status": "Pending"
}
```

## EVM Compatible Networks <a href="#evm" id="evm"></a>

### ERC-20 <a href="#evm-erc20" id="evm-erc20"></a>

Transfer fungible tokens that implement the [ERC-20 specification](https://eips.ethereum.org/EIPS/eip-20).

#### Request body <a href="#erc20-request-body" id="erc20-request-body"></a>

| Request body fields | Required/Optional | Description                                                 | Type   |
| ------------------- | ----------------- | ----------------------------------------------------------- | ------ |
| `kind`              | Required          | `Erc20`                                                     | String |
| `contract`          | Required          | The ERC20 contract address                                  | String |
| `to`                | Required          | The destination address                                     | String |
| `amount`            | Required          | The amount of tokens to transfer in minimum denomination    | String |
| `priority`          | Optional          | The priority that determines the fees paid for the transfer | String |

#### Sample request body <a href="#sample-erc20-request" id="sample-erc20-request"></a>

```json
{
  "kind": "Erc20",
  "contract": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
  "to": "0xb282dc7cde21717f18337a596e91ded00b79b25f",
  "amount": "1000000"
}
```

#### 200 response example <a href="#erc20-response-example" id="erc20-response-example"></a>

```json
{
  "id": "xfr-6ulmv-sa183-xxxxxxxxxxxxxxxx",
  "walletId": "wa-40f4f-51gpm-xxxxxxxxxxxxxxxx",
  "network": "Ethereum",
  "requester": {
    "userId": "us-4vu4v-kud3l-xxxxxxxxxxxxxxxx",
    "appId": "ap-7c2pm-avfsr-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Erc20",
    "contract": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "amount": "1000000",
    "to": "0xb282dc7cde21717f18337a596e91ded00b79b25f"
  },
  "metadata": {
    "asset": {
      "symbol": "USDC",
      "decimals": 6,
      "verified": true,
      "quotes": {
        "USD": 1.000804849917271,
        "EUR": 0.9201529894769885
      }
    }
  },
  "status": "Confirmed",
  "fee": "1542993669053672",
  "txHash": "0x8e88793607610a83798eb5ec6dde861f3e459c7e4a22e78b0d2e675b86d0d1e7",
  "dateRequested": "2024-01-18T23:03:53.739Z",
  "dateBroadcasted": "2024-01-18T23:03:55.685Z",
  "dateConfirmed": "2024-01-18T23:03:59.000Z"
}
```

### ERC-721 <a href="#evm-erc721" id="evm-erc721"></a>

Transfer non-fungible tokens that implement the [ERC-721 specification](https://eips.ethereum.org/EIPS/eip-721)

#### Request body <a href="#erc721-request-body" id="erc721-request-body"></a>

| Request body fields | Required/Optional | Description                                                 | Type   |
| ------------------- | ----------------- | ----------------------------------------------------------- | ------ |
| `kind`              | Required          | `Erc721`                                                    | String |
| `contract`          | Required          | The ERC721 contract address                                 | String |
| `to`                | Required          | The destination address                                     | String |
| `tokenId`           | Required          | The token to transfer                                       | String |
| `priority`          | Optional          | The priority that determines the fees paid for the transfer | String |

#### Sample request body <a href="#sample-erc721-request" id="sample-erc721-request"></a>

```shell
{
  "kind": "Erc721",
  "contract": "0x00fb58432ef9d418bf6688bcf0a226d2fcaa18e2",
  "to": "0xb282dc7cde21717f18337a596e91ded00b79b25f",
  "tokenId": "1"
}
```

#### 200 response example <a href="#erc721-response-example" id="erc721-response-example"></a>

```json
{
  "id": "xfr-4n0dm-fqju5-xxxxxxxxxxxxxxxx",
  "walletId": "wa-39abb-e9kpk-xxxxxxxxxxxxxxxx",
  "network": "EthereumSepolia",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Erc721",
    "contract": "0x00fb58432ef9d418bf6688bcf0a226d2fcaa18e2",
    "to": "0xb282dc7cde21717f18337a596e91ded00b79b25f",
    "tokenId": "1"
  },
  "dateRequested": "2023-05-08T18:10:43.521Z",
  "status": "Pending"
}
```

## Solana <a href="#solana" id="solana"></a>

### Solana Program Library Token (SPL) <a href="#solana-spl" id="solana-spl"></a>

Transfer [SPL tokens](https://spl.solana.com/token).

#### Request body <a href="#spl-request-body" id="spl-request-body"></a>

<table><thead><tr><th width="292">Request body fields</th><th width="132">Required/Optional</th><th width="202">Description</th><th>Type</th></tr></thead><tbody><tr><td><code>kind</code></td><td>Required</td><td><code>Spl</code></td><td>String</td></tr><tr><td><code>mint</code></td><td>Required</td><td>The mint account address</td><td>String</td></tr><tr><td><code>to</code></td><td>Required</td><td>The destination address</td><td>String</td></tr><tr><td><code>amount</code></td><td>Required</td><td>The amount of tokens to transfer in minimum denomination</td><td>String</td></tr><tr><td><code>createDestinationAccount</code></td><td>Optional</td><td>If True, pay to create the associated token account of the recipient if it doesn't exist. Defaults to False.</td><td>Boolean</td></tr></tbody></table>

#### Sample request body <a href="#sample-spl-request" id="sample-spl-request"></a>

```json
{
  "kind": "Spl",
  "mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
  "to": "3U6stgsD1FmA7o3omUguritCU8iWmUM7Rs6KqAHHxHVZ",
  "amount": "1000000"
}
```

#### 200 response example <a href="#spl-response-example" id="spl-response-example"></a>

```json
{
  "id": "xfr-4qc73-2kr4s-xxxxxxxxxxxxxxxx",
  "walletId": "wa-3i0nv-fa4e7-xxxxxxxxxxxxxxxx",
  "network": "Solana",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Spl",
    "mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
    "to": "3U6stgsD1FmA7o3omUguritCU8iWmUM7Rs6KqAHHxHVZ",
    "amount": "1000000"
  },
  "metadata": {
    "asset": {
      "symbol": "USDC",
      "decimals": 6,
      "verified": true
    }
  },
  "dateRequested": "2024-06-11T13:46:55.175Z",
  "status": "Pending"
}
```

### Solana Program Library Token 2022 (SPL-2022) <a href="#solana-spl2022" id="solana-spl2022"></a>

Transfer [SPL 2022 tokens](https://spl.solana.com/token-2022).

#### Request body <a href="#spl2022-request-body" id="spl2022-request-body"></a>

| Request body fields        | Required/Optional | Description                                                                                                  | Type    |
| -------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------ | ------- |
| `kind`                     | Required          | `Spl2022`                                                                                                    | String  |
| `mint`                     | Required          | The mint account address                                                                                     | String  |
| `to`                       | Required          | The destination address                                                                                      | String  |
| `amount`                   | Required          | The amount of tokens to transfer in minimum denomination                                                     | String  |
| `createDestinationAccount` | Optional          | If True, pay to create the associated token account of the recipient if it doesn't exist. Defaults to False. | Boolean |

#### Sample request body <a href="#sample-spl2022-request" id="sample-spl2022-request"></a>

```json
{
  "kind": "Spl2022",
  "mint": "DnECowYZoUEyqfntQmeLPPH5s1TLR7P5v4iyToZxFjDZ",
  "to": "3U6stgsD1FmA7o3omUguritCU8iWmUM7Rs6KqAHHxHVZ",
  "amount": "1000000"
}
```

#### 200 response example <a href="#spl2022-response-example" id="spl2022-response-example"></a>

```json
{
  "id": "xfr-2kmot-fk31j-xxxxxxxxxxxxxxxx",
  "walletId": "wa-3i0nv-fa4e7-xxxxxxxxxxxxxxxx",
  "network": "Solana",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Spl2022",
    "mint": "DnECowYZoUEyqfntQmeLPPH5s1TLR7P5v4iyToZxFjDZ",
    "to": "3U6stgsD1FmA7o3omUguritCU8iWmUM7Rs6KqAHHxHVZ",
    "amount": "1000000"
  },
  "metadata": {
    "asset": {
      "symbol": "Dfns22",
      "decimals": 8
    }
  },
  "dateRequested": "2024-06-05T17:02:43.295Z",
  "status": "Pending"
}
```

## Stellar <a href="#stellar" id="stellar"></a>

### Classic Stellar Assets (SEP-41) <a href="#stellar-sep41" id="stellar-sep41"></a>

Transfer classic [Stellar Assets](https://developers.stellar.org/docs/issuing-assets/anatomy-of-an-asset). They all implement the [SEP-41 token interface](https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0041.md).

#### Request body <a href="#sep41-request-body" id="sep41-request-body"></a>

| Request body fields | Required/Optional | Description                                              | Type   |
| ------------------- | ----------------- | -------------------------------------------------------- | ------ |
| `kind`              | Required          | `Sep41`                                                  | String |
| `issuer`            | Required          | The asset issuer address                                 | String |
| `assetCode`         | Required          | The asset code                                           | String |
| `to`                | Required          | The destination address                                  | String |
| `amount`            | Required          | The amount of tokens to transfer in minimum denomination | String |
| `memo`              | Optional          | The memo                                                 | String |

#### Sample request body <a href="#sample-sep41-request" id="sample-sep41-request"></a>

```json
{
  "kind": "Sep41",
  "issuer": "GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN",
  "assetCode": "USDC",
  "to": "GAZWLHTNAOJWW52GZCUJAS5MSXK7LAWCUC5TFOFFVDQ7CDTNFODJ37GB",
  "amount": "1000000"
}
```

#### 200 response example <a href="#sep41-response-example" id="sep41-response-example"></a>

```json
{
  "id": "xfr-gbasv-hssu9-xxxxxxxxxxxxxxxx",
  "walletId": "wa-46sdf-a9stj-xxxxxxxxxxxxxxxx",
  "network": "Stellar",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Sep41",
    "issuer": "GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN",
    "assetCode": "USDC",
    "to": "GAZWLHTNAOJWW52GZCUJAS5MSXK7LAWCUC5TFOFFVDQ7CDTNFODJ37GB",
    "amount": "1000000"
  },
  "metadata": {
    "asset": {
      "symbol": "USDC",
      "decimals": 7,
      "verified": true
    }
  },
  "dateRequested": "2024-05-08T14:34:04.446Z",
  "status": "Pending"
}
```

## TON <a href="#solana" id="solana"></a>

### TON TEP74 Jetton <a href="#solana-spl" id="solana-spl"></a>

Transfer [Jetton](https://github.com/ton-blockchain/TEPs/blob/master/text/0074-jettons-standard.md).

#### Request body <a href="#spl-request-body" id="spl-request-body"></a>

| Request body fields | Required/Optional | Description                                              | Type   |
| ------------------- | ----------------- | -------------------------------------------------------- | ------ |
| `kind`              | Required          | `Tep74`                                                  | String |
| `master`            | Required          | The jetton master address                                | String |
| `to`                | Required          | The destination address                                  | String |
| `amount`            | Required          | The amount of tokens to transfer in minimum denomination | String |

#### Sample request body <a href="#sample-spl-request" id="sample-spl-request"></a>

```json
{
  "kind": "Tep74",
  "master": "EQAIZUJZxUgjovq8C6P5tRGwSsydiCtKiwRnycPnN1k4WpFo",
  "to": "EQBfYLuQwjbBd-LAZ6eNC26XmVVxEl86MQPKG981hdTSicL_",
  "amount": "1000000"
}
```

#### 200 response example <a href="#spl-response-example" id="spl-response-example"></a>

<pre class="language-json"><code class="lang-json">{
  "id": "xfr-4qc73-2kr4s-xxxxxxxxxxxxxxxx",
  "walletId": "wa-3i0nv-fa4e7-xxxxxxxxxxxxxxxx",
  "network": "Solana",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Tep74",
    "master": "EQAIZUJZxUgjovq8C6P5tRGwSsydiCtKiwRnycPnN1k4WpFo",
    "to": "EQBfYLuQwjbBd-LAZ6eNC26XmVVxEl86MQPKG981hdTSicL_",
    "amount": "1000000"
<strong>  }
</strong>  "metadata": {
    "asset": {
      "symbol": "jUSDC",
      "decimals": 6,
      "verified": true
    }
  },
  "dateRequested": "2024-06-11T13:46:55.175Z",
  "status": "Pending"
}
</code></pre>

## TRON <a href="#tron" id="tron"></a>

### TRC-10 <a href="#tron-trc10" id="tron-trc10"></a>

Transfer TRON's TRC-10 fungible tokens

#### Request body <a href="#trc10-request-body" id="trc10-request-body"></a>

| Request body fields | Required/Optional | Description                                              | Type   |
| ------------------- | ----------------- | -------------------------------------------------------- | ------ |
| `kind`              | Required          | `Trc10`                                                  | String |
| `tokenId`           | Required          | The token ID                                             | String |
| `to`                | Required          | The destination address                                  | String |
| `amount`            | Required          | The amount of tokens to transfer in minimum denomination | String |

#### Sample request body <a href="#trc10-request-body" id="trc10-request-body"></a>

```json
{
  "kind": "Trc10",
  "tokenId": "1005273",
  "to": "TADDx31pdCFfp3XrYxp6fQGbRxriYFLTrx",
  "amount": "10000"
}
```

### TRC-20 <a href="#tron-trc20" id="tron-trc20"></a>

Transfer fungible tokens that implement the TRC-20 smart contract specification.

#### Request body <a href="#trc20-request-body" id="trc20-request-body"></a>

| Request body fields | Required/Optional | Description                                              | Type   |
| ------------------- | ----------------- | -------------------------------------------------------- | ------ |
| `kind`              | Required          | `Trc20`                                                  | String |
| `contract`          | Required          | The smart contract address                               | String |
| `to`                | Required          | The destination address                                  | String |
| `amount`            | Required          | The amount of tokens to transfer in minimum denomination | String |

#### Sample request body <a href="#trc20-request-body" id="trc20-request-body"></a>

```json
{
  "kind": "Trc20",
  "contract": "TXLAQ63Xg1NAzckPwKHvzw7CSEmLMEqcdj",
  "to": "TQJNezrbfJ3akrGgR7eM2fWyFpsKeM8wzN",
  "amount": "1000000"
}
```

### TRC-721 <a href="#tron-trc721" id="tron-trc721"></a>

Transfer non-fungible tokens that implement the TRC-721 smart contract specification.

#### Request body <a href="#trc721-request-body" id="trc721-request-body"></a>

| Request body fields | Required/Optional | Description                | Type   |
| ------------------- | ----------------- | -------------------------- | ------ |
| `kind`              | Required          | `Trc721`                   | String |
| `contract`          | Required          | The smart contract address | String |
| `to`                | Required          | The destination address    | String |
| `tokenId`           | Required          | The token to transfer      | String |

#### Sample request body <a href="#trc721-request-body" id="trc721-request-body"></a>

```json
{
  "kind": "Trc721",
  "contract": "TKgnDMWHYmwH24REe9XnrnwcNCvtb53n8Q",
  "to": "TQJNezrbfJ3akrGgR7eM2fWyFpsKeM8wzN",
  "tokenId": "1"
}
```

## Response Statuses

<table><thead><tr><th width="167">Status</th><th>Definition</th></tr></thead><tbody><tr><td><code>Pending</code></td><td>The request is pending approval due to a <a href="https://docs.dfns.co/d/api-docs/policy-engine/policies#wallets-sign-activity">policy applied</a> to the wallet</td></tr><tr><td><code>Executing</code></td><td>The request is approved and is in the process of being executed (note this status is only set for a short time between pending and broadcasted)</td></tr><tr><td><code>Broadcasted</code></td><td>The transaction has been successfully written to the mempool</td></tr><tr><td><code>Confirmed</code></td><td>The transaction has been confirmed on-chain by our indexing pipeline</td></tr><tr><td><code>Failed</code></td><td>Indicates a system failure to complete the request</td></tr><tr><td><code>Rejected</code></td><td>The request has been rejected by a policy approval action</td></tr></tbody></table>
