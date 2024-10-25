# TON: Generate Signature

TON chains support the following signature `kinds`:

- `Message`: Generates a signature for a signing message.
- `Hash`: Generates the signature for the hash digest of the original message.

## Message Signature <a href="#transaction-request-body" id="transaction-request-body"></a>

Generates a signature from a serialized signing message BoC.

<table><thead><tr><th width="180">Property</th><th width="180">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td><code>Message</code></td></tr><tr><td><code>message</code><mark style="color:red;">*</mark></td><td>Hex String</td><td>The serialized signing message BoC</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

### Sample request body <a href="#sample-transaction-request" id="sample-transaction-request"></a>

```json
{
  "kind": "Message",
  "message": "0xb5ee9c7241010201004400011c29a9a317671ba91000000004000301006242000b3fa8f8f4c8abf5f1086d9d8ca76c23ac7186a03cd4142b77428237abb8bfb7880800000000000000000000000000bc0d3854"
}
```

### 200 response example <a href="#transaction-response-example" id="transaction-response-example"></a>

```json
{
  "id": "sig-19u01-g60tf-xxxxxxxxxxxxxxxx",
  "walletId": "wa-174tk-m918i-xxxxxxxxxxxxxxxx",
  "network": "TonTestnet",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-341e6-12nj6-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Message",
    "transaction": "0xb5ee9c7241010201004400011c29a9a317671ba91000000004000301006242000b3fa8f8f4c8abf5f1086d9d8ca76c23ac7186a03cd4142b77428237abb8bfb7880800000000000000000000000000bc0d3854"
  },
  "status": "Signed",
  "signature": {
    "r": "0xb1cbcdcfefce82ba35c3b7ed352b77876c917528e2f1b668599847860f2f02c2",
    "s": "0xc26389dca075e706973526195a70282f7c0dc63b94ac1aed8fe85f2116eb3202",
    "encoded": "0xb1cbcdcfefce82ba35c3b7ed352b77876c917528e2f1b668599847860f2f02c2c26389dca075e706973526195a70282f7c0dc63b94ac1aed8fe85f2116eb3202"
  },
  "dateRequested": "2024-01-10T21:13:38.186Z",
  "dateSigned": "2024-01-10T21:13:38.348Z"
}
```

### Typescript Example with ton-core

First install ton-core. You can find the full documentation here: [https://github.com/ton-org/ton](https://github.com/ton-org/ton)

Ton requires a specific message format when interacting with the network. We need to create a signing message payload BoC. Then, we can sign the BoC via [the Dfns TypeScript SDK](https://github.com/dfns/dfns-sdk-ts):

```typescript
import { beginCell, internal, SendMode, storeMessageRelaxed, TonClient, WalletContractV4 } from '@ton/ton'

const walletId = 'wa-6lbfv-9esgj-xxxxxxxxxxxxxxxx'
const wallet = await dfnsClient.wallets.getWallet({ walletId })

const client = new TonClient({ endpoint })

const contract = client.open(
  WalletContractV4.create({
    workchain: 0,
    publicKey: Buffer.from(wallet.signingKey.publicKey, 'hex'),
  })
)

const seqno = await opened.getSeqno()

const builder = beginCell().storeUint(contract.walletId, 32)
if (seqno === 0) {
  for (let i = 0; i < 32; i++) {
    builder.storeBit(1)
  }
} else {
  builder.storeUint(Math.floor(Date.now() / 1e3) + 60, 32)
}

builder.storeUint(seqno, 32)
builder.storeUint(0, 8)
builder.storeUint(SendMode.PAY_GAS_SEPARATELY, 8)

const message = internal({
  value: '1',
  to: '0QDXET3_xkWbJwXv16hU402QY3hS3cCelF5Ax3cvUpEbzG5A',
  body: 'Dfns SDK Example',
})

const cell = builder.storeRef(beginCell().store(storeMessageRelaxed(message))).endCell()

const res = await dfnsClient.wallets.generateSignature({
  walletId,
  body: {
    kind: 'Message',
    message: `0x${cell.toBoc().toString('hex')}`,
  },
})
```

## Hash Signature <a href="#hash-signature-request-body" id="hash-signature-request-body"></a>

Generates the signature for the hash digest of the original message.

### Request body <a href="#hash-signature-request-body" id="hash-signature-request-body"></a>

<table><thead><tr><th width="193">Property</th><th width="170">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td><code>Hash</code></td></tr><tr><td><code>hash</code><mark style="color:red;">*</mark></td><td>String</td><td>The hash digest in hex.</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

### Sample request body <a href="#sample-hash-request" id="sample-hash-request"></a>

```shell
{
  "kind": "Hash",
  "hash": "0x031edd7d41651593c5fe5c006fa5752b37fddff7bc4e843aa6af0c950f4b9406"
}
```

### 200 response example <a href="#hash-response-example" id="hash-response-example"></a>

```json
{
  "id": "sig-19u01-g60tf-xxxxxxxxxxxxxxxx",
  "walletId": "wa-174tk-m918i-xxxxxxxxxxxxxxxx",
  "network": "TonTestnet",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-341e6-12nj6-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Hash",
    "hash": "031edd7d41651593c5fe5c006fa5752b37fddff7bc4e843aa6af0c950f4b9406"
  },
  "status": "Signed",
  "signature": {
    "r": "0xb1cbcdcfefce82ba35c3b7ed352b77876c917528e2f1b668599847860f2f02c2",
    "s": "0xc26389dca075e706973526195a70282f7c0dc63b94ac1aed8fe85f2116eb3202",
    "encoded": "0xb1cbcdcfefce82ba35c3b7ed352b77876c917528e2f1b668599847860f2f02c2c26389dca075e706973526195a70282f7c0dc63b94ac1aed8fe85f2116eb3202"
  },
  "dateRequested": "2024-01-10T21:13:38.186Z",
  "dateSigned": "2024-01-10T21:13:38.348Z"
}
```
