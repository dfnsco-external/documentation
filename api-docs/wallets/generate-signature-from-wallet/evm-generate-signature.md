# EVM: Generate Signature

EVM chains like Ethereum, Polygon, BSC, Arbitrum, etc support the following signature `kinds`:

* `Transaction`: Generates a fully signed transaction ready for broadcasting yourself.
* `Message`: Generates a signature for an arbitrary message.
* `Eip712`: Generates the signature for typed structured data defined in [EIP-712](https://eips.ethereum.org/EIPS/eip-712).
* `Hash`: Generates the signature for the hash digest of the original message.

## Transaction Signature <a href="#transaction-request-body" id="transaction-request-body"></a>

Generates a fully signed transaction ready for broadcasting yourself.

<table><thead><tr><th width="183">Property</th><th width="197"> Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td><code>Transaction</code></td></tr><tr><td><code>transaction</code><mark style="color:red;">*</mark></td><td>Hex String</td><td>The unsigned hex encoded transaction as shown below</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

### Sample request body <a href="#sample-transaction-request" id="sample-transaction-request"></a>

```shell
{
  "kind": "Transaction",
  "message": "0x02e783aa36a71503850d40e49def82520894e5a2ebc128e262ab1e3bd02bffbe16911adfbffb0180c0"
}
```

### 200 response example <a href="#transaction-response-example" id="transaction-response-example"></a>

```json
{
  "id": "sig-39l22-xxxxx-xxxxxxxxxxxxxxxx",
  "walletId": "wa-19lns-xxxxx-xxxxxxxxxxxxxxxx",
  "network": "EthereumSepolia",
  "requester": {
    "userId": "us-3v1ag-xxxxx-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-xxxxx-xxxxxxxxxxxxxxxx",
    "appId": "ap-c831n-xxxxx-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Transaction",
    "transaction": "0x02e783aa36a71603850cbab1770382520894e5a2ebc128e262ab1e3bd02bffbe16911adfbffb0180c0"
  },
  "status": "Signed",
  "signature": {
    "r": "0x05e365d4304eaa78516eb309bff91f8c12e5445a431e3f2428239678d0150c6c",
    "s": "0x47e0765c439fb42d57767910865d240964b7b09f2b2f74d8f14a63da7ce5a1fe",
    "recid": 0,
    "encoded": "0x05e365d4304eaa78516eb309bff91f8c12e5445a431e3f2428239678d0150c6c47e0765c439fb42d57767910865d240964b7b09f2b2f74d8f10000000000000000"
  },
  "signedData": "0x02f86a83aa36a71603850cbab1770382520894e5a2ebc128e262ab1e3bd02bffbe16911adfbffb0180c080a005e365d4304eaa78516eb309bff91f8c12e5445a431e3f2428239678d0150c6ca047e0765c439fb42d57767910865d240964b7b09f2b2f74d80000000000000000",
  "dateRequested": "2024-01-10T19:07:39.277Z",
  "dateSigned": "2024-01-10T19:07:40.533Z"
}
```

### Typescript Example with Ethers

First install the Ethers JS. You can find the full documentation here: [https://docs.ethers.org/v6/](https://docs.ethers.org/v6/)

Here a code sample to generate a signature via [the Dfns TypeScript SDK](https://github.com/dfns/dfns-sdk-ts):

```typescript
import { parseUnits, Transaction } from 'ethers'

const walletId = 'wa-6lbfv-9esgj-88s80c0qsih0a393'

const transaction = Transaction.from({
  to: '0xa238b6008Bc2FBd9E386A5d4784511980cE504Cd',
  value: '1',
  gasLimit: '21000',
  maxPriorityFeePerGas: parseUnits('5', 'gwei'),
  maxFeePerGas: parseUnits('20', 'gwei'),
  nonce: 3,
  type: 2,
  chainId: 11155111,
})

const res = await dfnsClient.wallets.generateSignature({
  walletId,
  body: { kind: 'Transaction', transaction: transaction.unsignedSerialized },
})
```

## Message Signature <a href="#message-request-body" id="message-request-body"></a>

Generates a signature for an arbitrary message.

<table><thead><tr><th width="202">Property</th><th width="204">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td><code>Message</code></td></tr><tr><td><code>message</code><mark style="color:red;">*</mark></td><td>String</td><td>The original message hex encoded.</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

### Sample request body <a href="#sample-message-request" id="sample-message-request"></a>

```shell
{
  "kind": "Message",
  "message": "0x49206c6f76652044666e73"
}
```

### 200 response example <a href="#message-response-example" id="message-response-example"></a>

```json
{
  "id": "sig-2rv2t-u0cmd-xxxxxxxxxxxxxxxx",
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "KeyEdDSA",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Message",
    "message": "0x49206c6f76652044666e73"
  },
  "status": "Signed",
  "signature": {
    "r": "0x05e365d4304eaa78516eb309bff91f8c12e5445a431e3f2428239678d0150c6c",
    "s": "0x47e0765c439fb42d57767910865d240964b7b09f2b2f74d8f14a63da7ce5a1fe",
    "recid": 0,
    "encoded": "0x05e365d4304eaa78516eb309bff91f8c12e5445a431e3f2428239678d0150c6c47e0765c439fb42d57767910865d240964b7b09f2b2f74d8f10000000000000000"
  },
  "dateRequested": "2023-05-15T20:21:11.576Z",
  "dateSigned": "2024-01-10T19:07:40.533Z"
}
```

## EIP-712 TypedData Signature <a href="#eip712-request-body" id="eip712-request-body"></a>

Generates the signature for typed structured data defined in [EIP-712](https://eips.ethereum.org/EIPS/eip-712), only applicable for EVM compatible blockchain networks.

<table><thead><tr><th width="163">field</th><th width="227">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td><code>Eip712</code></td></tr><tr><td><code>types</code><mark style="color:red;">*</mark></td><td>Map&#x3C;String, TypedDataField[]></td><td>Type definitions.</td></tr><tr><td><code>domain</code><mark style="color:red;">*</mark></td><td>Eip712Domain</td><td>Domain separator.</td></tr><tr><td><code>message</code><mark style="color:red;">*</mark></td><td>Object</td><td>Structured message to sign.</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

**TypedDataField**

<table><thead><tr><th width="166">field</th><th width="214">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>name</code><mark style="color:red;">*</mark></td><td>String</td><td>Field name.</td></tr><tr><td><code>type</code><mark style="color:red;">*</mark></td><td>String</td><td>Field type.</td></tr></tbody></table>

**Eip712Domain**

<table><thead><tr><th width="222">field</th><th width="154">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>name</code></td><td>String</td><td>Name of the signing domain.</td></tr><tr><td><code>version</code></td><td>String</td><td>Current major version of the signing domain.</td></tr><tr><td><code>chainId</code></td><td>Integer</td><td>Chain ID.</td></tr><tr><td><code>verifyingContract</code></td><td>String</td><td>The address of the contract that will verify the signature.</td></tr><tr><td><code>salt</code></td><td>String</td><td>32-byte value as a last-resort domain separator.</td></tr></tbody></table>

### Sample request body <a href="#sample-eip712-request" id="sample-eip712-request"></a>

```shell
{
  "kind": "Eip712",
  "types": {
    "Person": [
      { "name": "name", "type": "string" },
      { "name": "wallet", "type": "address" }
    ],
    "Mail": [
      { "name": "from", "type": "Person" },
      { "name": "to", "type": "Person" },
      { "name": "contents", "type": "string" }
    ]
  },
  "domain": {
    "name": "Ether Mail",
    "version": "1",
    "chainId": 1,
    "verifyingContract": "0x1b352de7a926ebd1bf52194dab487c2cb0793a9b",
    "salt": "0xf2d857f4a3edcb9b78b4d503bfe733db1e3f6cdc2b7971ee739626c97e86a558"
  },
  "message": {
    "from": {
      "name": "Chris",
      "wallet": "0x00e3495cf6af59008f22ffaf32d4c92ac33dac47"
    },
    "to": {
      "name": "Bob",
      "wallet": "0xcc0ee1a1c5e788b61916c8f1c96c960f9a9d3db7"
    },
    "contents": "Hello, Bob!"
  }
}
```

### 200 response example <a href="#eip712-response-example" id="eip712-response-example"></a>

```json
{
  "id": "sig-4tcfd-enph7-xxxxxxxxxxxxxxxx",
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "EthereumSepolia",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Eip712",
    "types": {
      "Person": [
        {
          "name": "name",
          "type": "string"
        },
        {
          "name": "wallet",
          "type": "address"
        }
      ],
      "Mail": [
        {
          "name": "from",
          "type": "Person"
        },
        {
          "name": "to",
          "type": "Person"
        },
        {
          "name": "contents",
          "type": "string"
        }
      ]
    },
    "domain": {
      "name": "Ether Mail",
      "version": "1",
      "chainId": 1,
      "verifyingContract": "0x1b352de7a926ebd1bf52194dab487c2cb0793a9b",
      "salt": "0xf2d857f4a3edcb9b78b4d503bfe733db1e3f6cdc2b7971ee739626c97e86a558"
    },
    "message": {
      "from": {
        "name": "Chris",
        "wallet": "0x00e3495cf6af59008f22ffaf32d4c92ac33dac47"
      },
      "to": {
        "name": "Bob",
        "wallet": "0xcc0ee1a1c5e788b61916c8f1c96c960f9a9d3db7"
      },
      "contents": "Hello, Bob!"
    }
  },
  "status": "Signed",
  "signature": {
    "r": "0xb23c2cfb6d409f5a55ced08f89ae70f3fe89403a5ba907c367545499874f1c7f",
    "s": "0x49992f242a21ae0692c24b43393336744ddc08459d936b6a70542d79df4f66f0",
    "recid": 1,
    "encoded": "0xb23c2cfb6d409f5a55ced08f89ae70f3fe89403a5ba907c367545499874f1c7f49992f242a21ae0692c24b43393336744ddc08459d936b6a70542d79df4f66f01c"
  },
  "dateRequested": "2023-05-15T19:26:18.145Z"
}
```

## Hash Signature <a href="#hash-signature-request-body" id="hash-signature-request-body"></a>

Generates the signature for the hash digest of the original message.

### Request body <a href="#hash-signature-request-body" id="hash-signature-request-body"></a>

<table><thead><tr><th width="193">Property</th><th width="170">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td><code>Hash</code></td></tr><tr><td><code>hash</code><mark style="color:red;">*</mark></td><td>String</td><td>The hash digest in hex.</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

### Sample request body <a href="#sample-hash-request" id="sample-hash-request"></a>

```shell
{
  "kind": "Hash",
  "hash": "031edd7d41651593c5fe5c006fa5752b37fddff7bc4e843aa6af0c950f4b9406"
}
```

### 200 response example <a href="#hash-response-example" id="hash-response-example"></a>

```json
{
  "id": "sig-2ouaj-f4nq6-xxxxxxxxxxxxxxxx",
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "EthereumSepolia",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "Hash",
    "hash": "031edd7d41651593c5fe5c006fa5752b37fddff7bc4e843aa6af0c950f4b9406"
  },
  "status": "Signed",
  "signature": {
    "r": "0x05e365d4304eaa78516eb309bff91f8c12e5445a431e3f2428239678d0150c6c",
    "s": "0x47e0765c439fb42d57767910865d240964b7b09f2b2f74d8f14a63da7ce5a1fe",
    "recid": 0,
    "encoded": "0x05e365d4304eaa78516eb309bff91f8c12e5445a431e3f2428239678d0150c6c47e0765c439fb42d57767910865d240964b7b09f2b2f74d8f10000000000000000"
  },
  "dateRequested": "2023-05-15T20:21:11.576Z",
  "dateSigned": "2024-01-10T19:07:40.533Z"
}
```
