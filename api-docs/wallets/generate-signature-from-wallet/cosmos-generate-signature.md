# Cosmos Appchain: Generate Signature

## SIGN_MODE_DIRECT Signature <a href="#signdocdirect-request-body" id="signdocdirect-request-body"></a>

Generates the signature using [SIGN_MODE_DIRECT](https://docs.cosmos.network/main/learn/advanced/transactions#sign_mode_direct-preferred).

<table><thead><tr><th width="173">Property</th><th width="187">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>kind</code><mark style="color:red;">*</mark></td><td>String</td><td><code>SignDocDirect</code></td></tr><tr><td><code>signDoc</code><mark style="color:red;">*</mark></td><td>Hex String</td><td>The hex encoded <code>SignDoc</code> protobuf as shown below</td></tr><tr><td><code>externalId</code></td><td>(Optional) String</td><td>A unique ID from your system. It can be leveraged to be used as an idempotency key (read more <a href="../../../advanced-topics/api-idempotency.md">here</a>)</td></tr></tbody></table>

### Sample request body <a href="#sample-signdocdirect-request" id="sample-signdocdirect-request"></a>

```json
{
  "kind": "SignDocDirect",
  "signDoc": "0x0a89010a86010a1c2f636f736d6f732e62616e6b2e763162657461312e4d736753656e6412660a2b6f736d6f313366796d6d61797430727a72366d6130716439686a6d71747433706b37787737736871666b70122b6f736d6f313378396b70343573366e6539686a6738356b333867346d7a687067337435766a666c30356b6c1a0a0a05756f736d6f12013112640a4e0a460a1f2f636f736d6f732e63727970746f2e736563703235366b312e5075624b657912230a210286a1b7ce6ae2b5b33c47d0b6c91bb28e4cdb786d15f4497adcdaa69640ca0db512040a02080112120a0c0a05756f736d6f120337353010e0a7121a0b6f736d6f2d746573742d3520e39f06"
}
```

### 200 response example <a href="#signdocdirect-response-example" id="signdocdirect-response-example"></a>

```json
{
  "id": "sig-7jb9u-37kf5-xxxxxxxxxxxxxxxx",
  "walletId": "wa-4a2t4-fjvpo-xxxxxxxxxxxxxxxx",
  "network": "SeiAtlantic2",
  "requester": {
    "userId": "us-3v1ag-v6b36-xxxxxxxxxxxxxxxx",
    "tokenId": "to-7mkkj-c831n-xxxxxxxxxxxxxxxx",
    "appId": "ap-24vva-92s32-xxxxxxxxxxxxxxxx"
  },
  "requestBody": {
    "kind": "SignDocDirect",
    "transaction": "0x0a89010a86010a1c2f636f736d6f732e62616e6b2e763162657461312e4d736753656e6412660a2b6f736d6f313366796d6d61797430727a72366d6130716439686a6d71747433706b37787737736871666b70122b6f736d6f313378396b70343573366e6539686a6738356b333867346d7a687067337435766a666c30356b6c1a0a0a05756f736d6f12013112640a4e0a460a1f2f636f736d6f732e63727970746f2e736563703235366b312e5075624b657912230a210286a1b7ce6ae2b5b33c47d0b6c91bb28e4cdb786d15f4497adcdaa69640ca0db512040a02080112120a0c0a05756f736d6f120337353010e0a7121a0b6f736d6f2d746573742d3520e39f06"
  },
  "status": "Signed",
  "signature": {
    "r": "0x96cfe730ef5edc727c29ace118aac11cf70f031d05be4b9a7cb24ebe7caa7cf5",
    "s": "0x3646b908c79eb310b4d56c2e23daf64ee14ceed8155779b83aebd3aef7c8fb4a",
    "recid": 1,
    "encoded": "0x96cfe730ef5edc727c29ace118aac11cf70f031d05be4b9a7cb24ebe7caa7cf53646b908c79eb310b4d56c2e23daf64ee14ceed8155779b83aebd3aef7c8fb4a"
  },
  "dateRequested": "2024-10-18T21:29:40.779Z",
  "dateSigned": "2024-10-18T21:29:41.299Z"
}
```

## Typescript Example with CosmJS SDK

First install the CosmJS SDK. You can find the full documentation here: [https://cosmos.github.io/cosmjs/](https://cosmos.github.io/cosmjs/)

Here a code sample to generate a signature via [the Dfns TypeScript SDK](https://github.com/dfns/dfns-sdk-ts):

```typescript
const client = await Comet38Client.create(new HttpClient(process.env.OSMOSIS_NODE_URL!))
const queryClient = QueryClient.withExtensions(cometClient, setupAuthExtension)

const body: TxBodyEncodeObject = {
  typeUrl: '/cosmos.tx.v1beta1.TxBody',
  value: {
    messages: [
      {
        typeUrl: '/cosmos.bank.v1beta1.MsgSend',
        value: {
          fromAddress: address,
          toAddress: to,
          amount: coins(1, 'uosmo'),
        },
      },
    ],
    memo,
  },
}

const account = await context.queryClient.auth.account(address)
const { sequence, accountNumber } = accountFromAny(account)

const authInfoBytes = makeAuthInfoBytes(
  [
    {
      pubkey: encodePubkey(encodeSecp256k1Pubkey(Buffer.from(publicKey, 'hex'))),
      sequence,
    },
  ],
  '100',
  Int53.fromString(fee.gas).toNumber(),
  undefined,
  undefined
)

const signDoc = makeSignDoc(context.registry.encode(body), authInfoBytes, 'osmo-test-5', accountNumber)

const res = await dfnsClient.wallets.generateSignature({
  walletId,
  body: {
    kind: 'SignDocDirect',
    signDoc: `0x${Buffer.from(makeSignBytes(doc)).toString('hex')}`,
  },
})
```
