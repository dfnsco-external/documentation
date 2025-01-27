# Tezos: Broadcast Transaction

## Request body <a href="#transaction-request-body" id="transaction-request-body"></a>

<table><thead><tr><th width="190">Property</th><th width="175">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td>For Tezos, always <code>Transaction</code></td></tr><tr><td><code>transaction</code><mark style="color:red;">*</mark></td><td>Hex String</td><td>The unsigned hex encoded transaction as shown below</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

### Sample request body <a href="#sample-transaction-request" id="sample-transaction-request"></a>

```json
{
  "kind": "Transaction",
  "transaction": "0x04c4b1966777a5d83f3f61e17bf64aa6090ddd3413ede4e24316d3334a7836486c0060f4b0700cb1b73bff168a6221c6a033de12953ebc029d82d50a8d02000100008017ed86b1bbb1c6a9399fc47b83fb8a919e013400"
}
```

### 200 response example <a href="#transaction-response-example" id="transaction-response-example"></a>

```json
{
  "id": "tx-5fnhg-8u56n-xxxxxxxxxxxxxxxx",
  "walletId": "wa-5kqa8-4leor-xxxxxxxxxxxxxxxx",
  "network": "TezosGhostnet",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-341e6-12nj6-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Transaction",
    "transaction": "0x04c4b1966777a5d83f3f61e17bf64aa6090ddd3413ede4e24316d3334a7836486c0060f4b0700cb1b73bff168a6221c6a033de12953ebc029d82d50a8d02000100008017ed86b1bbb1c6a9399fc47b83fb8a919e013400"
  },
  "status": "Broadcasted",
  "txHash": "ooESkzFG4oKQueVWX9PX1tSrBES8hWJ7N9NRtz2gK6AwVZpZqGX",
  "dateRequested": "2024-01-10T20:06:52.915Z",
  "dateBroadcasted": "2024-01-10T20:06:53.103Z"
}
```

## Typescript Example with Taquito

First install the Taquito SDK. You can find the full documentation here: [https://taquito.io/docs/quick\_start/](https://taquito.io/docs/quick\_start/)

Taquito is a little special in that it requires a `signer` to forge an operation. In fact, we only need the signer to return the wallet address and the encoded public key. We'll initialize a Taquito RPC instance using our fake signer and a local forger (see below). After forging the operation, we can distribute it via [the Dfns TypeScript SDK](https://github.com/dfns/dfns-sdk-ts).

```typescript
import { RpcClient } from '@taquito/rpc'
import { Signer, TezosToolkit } from '@taquito/taquito'
import { LocalForger } from '@taquito/local-forging'
import { Prefix, b58cencode, prefix } from '@taquito/utils'

const walletId = 'wa-6lbfv-9esgj-xxxxxxxxxxxxxxxx'
const wallet = await dfnsClient.wallets.getWallet({ walletId })

// Tezos requires a signer to forge a transaction. When forging, we only
// need 'publicKeyHash' (address) and 'publicKey' (encoded)
class TezosToolkitSigner implements Signer {
  sign(): Promise<{
    bytes: string
    sig: string
    prefixSig: string
    sbytes: string
  }> {
    throw new Error('Method not implemented.')
  }
  async publicKey(): Promise<string> {
    const prefix = wallet.signingKey.scheme === 'EdDSA' ? Prefix.EDPK : Prefix.SPPK
    return b58cencode(wallet.signingKey.publicKey, prefix)
  }

  async publicKeyHash(): Promise<string> {
    return wallet.address
  }

  secretKey(): Promise<string | undefined> {
    throw new Error('Method not implemented.')
  }
}

const client = new RpcClient('TEZOS_NODE_URL')
const Tezos = new TezosToolkit(client)
const forger = new LocalForger()

Tezos.setForgerProvider(forger)
Tezos.setSignerProvider(new TezosToolkitSigner())

const receiver = 'tz1XKKrD4NLRhBK4PFxQ4XvH2KXZ6J389N1Z'

// estimate fees
const estimate = await Tezos.estimate.transfer({
  source: wallet.address,
  to: receiver,
  amount: 1,
})

const prepared = await Tezos.prepare.transaction({
  source: wallet.address,
  to: receiver,
  amount: 1,
  fee: estimate.suggestedFeeMutez,
  gasLimit: estimate.gasLimit,
  storageLimit: estimate.storageLimit,
})

const forgeable = await Tezos.prepare.toForge(prepared)
const forgedBytes = await forger.forge(forgeable)

const res = await dfnsClient.wallets.broadcastTransaction({
  walletId,
  body: {
    kind: 'Transaction',
    transaction: `0x${forgedBytes}`,
  },
})
```
