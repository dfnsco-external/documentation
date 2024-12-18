# Aptos: Generate Signature

## Request body <a href="#transaction-request-body" id="transaction-request-body"></a>

<table><thead><tr><th width="182">Property</th><th width="189">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td>For Aptos, always <code>Transaction</code></td></tr><tr><td><code>transaction</code><mark style="color:red;">*</mark></td><td>Hex String</td><td>The unsigned hex bcs encoded transaction. More details bellow.</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

### Transaction formating <a href="#sample-transaction-request" id="sample-transaction-request"></a>

We accept two transaction kinds to be signed:

* [RawTransaction](https://aptos.web3doc.top/guides/creating-a-signed-transaction/#raw-transaction): This is the simplest transaction (no external fee payer, no secondarySigners)
* [SignedTransaction](https://aptos.web3doc.top/guides/creating-a-signed-transaction/#signed-transaction): For more complex transaction, you can pass a SignedTransaction that will contains the RawTransaction and an authenticator depending on the transaction. Accepted authenticator are:
  * &#x20;TransactionAuthenticatorFeePayer for sponsored transactions (single/multi sig)
  * TransactionAuthenticatorMultiAgent for multi signature transactions (not sponsored)

All the passed transactions should be [BCS](https://aptos.dev/en/build/sdks/ts-sdk/building-transactions/bcs-format) serialized and in hex format

You can find advanced transaction constructs (sponsored/multisig) in our  [Dfns TypeScript SDK](https://github.com/dfns/dfns-sdk-ts)

### Sample request body <a href="#sample-transaction-request" id="sample-transaction-request"></a>

```json
{
  "kind": "Transaction",
  "transaction": "0x01000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000008001000103b25c8c464080ab2835a166d2b3f13195c2ff3c8f281c7ebe492f0d45d830ff4824a8b38a94b73d2756f2be68655a49706be9b1dc900978984d6eeaf65ab62e900000000000000000000000000000000000000000000000000000000000000000ed589eed2559d935c834cd6d6cbee12970423ad37853618d39e632032aa4c51201020200010c02000000010000000000000000"
}
```

### 200 response example <a href="#transaction-response-example" id="transaction-response-example"></a>

```json
{
  "id": "sig-2rv2t-u0cmd-xxxxxxxxxxxxxxxx",
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "AptosTestnet",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Transaction",
    "transaction": "0x01000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000008001000103b25c8c464080ab2835a166d2b3f13195c2ff3c8f281c7ebe492f0d45d830ff4824a8b38a94b73d2756f2be68655a49706be9b1dc900978984d6eeaf65ab62e900000000000000000000000000000000000000000000000000000000000000000ed589eed2559d935c834cd6d6cbee12970423ad37853618d39e632032aa4c51201020200010c02000000010000000000000000"
  },
  "status": "Signed",
  "signature": {
    "r": "0x87e2fafa916877495baed6adb66283fcecd6166c73b1bb5812441045fc52b5ac",
    "s": "0x52f5216e39945db8a1fc7a19778813ed30e4cb84787ff9fe7a1e4f1d7976d70f",
    "encoded": "0x87e2fafa916877495baed6adb66283fcecd6166c73b1bb5812441045fc52b5ac52f5216e39945db8a1fc7a19778813ed30e4cb84787ff9fe7a1e4f1d7976d70f"
  },
  "signedData": "0x0187e2fafa916877495baed6adb66283fcecd6166c73b1bb5812441045fc52b5ac52f5216e39945db8a1fc7a19778813ed30e4cb84787ff9fe7a1e4f1d7976d70f8001000103b25c8c464080ab2835a166d2b3f13195c2ff3c8f281c7ebe492f0d45d830ff4824a8b38a94b73d2756f2be68655a49706be9b1dc900978984d6eeaf65ab62e900000000000000000000000000000000000000000000000000000000000000000ed589eed2559d935c834cd6d6cbee12970423ad37853618d39e632032aa4c51201020200010c02000000010000000000000000",
  "dateRequested": "2024-01-10T19:45:08.059Z",
  "dateSigned": "2024-01-10T19:45:08.285Z"
}
```

## Typescript Example with Aptos sdk

First install the aptos SDK. You can find the source code here: [https://github.com/aptos-labs/aptos-ts-sdk](https://github.com/aptos-labs/aptos-ts-sdk)

Here a code sample to generate a signature for a basic transaction via [the Dfns TypeScript SDK](https://github.com/dfns/dfns-sdk-ts):

```typescript

import {
  Aptos, 
  APTOS_COIN, 
  AptosConfig, 
  MimeType, 
  Network, 
  PendingTransactionResponse, 
  postAptosFullNode
} from '@aptos-labs/ts-sdk'


const walletId = 'wa-6lbfv-9esgj-88s80c0qsih0a393'
const wallet = await dfnsClient.wallets.getWallet({ walletId })

const myAddress = new PublicKey(wallet.address)
const toAddress = new PublicKey('0x5bdc24cb9033286ffe19f436145b9e2267dd03b0fd0d422459d381a6431d39ba')

const aptosConfig = new AptosConfig({
    network: Network.TESTNET,
  })
const client = new Aptos(aptosConfig)

const tx = await client.transaction.build.simple({
    sender: wallet.address,
    data: {
      function: '0x1::coin::transfer',
      typeArguments: [APTOS_COIN],
      functionArguments: [
        "0x5bdc24cb9033286ffe19f436145b9e2267dd03b0fd0d422459d381a6431d39ba", // Receiver
        "1", // Amount
      ],
    },
  })

const signed = (await wallet.signTransaction(tx))

const { data } = await postAptosFullNode<Uint8Array, PendingTransactionResponse>({
  aptosConfig: client.config,
  body: signed.bcsToBytes(),
  path: 'transactions',
  originMethod: 'submitTransaction',
  contentType: MimeType.BCS_SIGNED_TRANSACTION,
})

// Wait for the transaction to be included
await client.waitForTransaction({transactionHash: data.hash})

```
