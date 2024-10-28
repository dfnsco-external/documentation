# Get Wallet Assets

`GET /wallets/{walletId}/assets`

Retrieves a list of assets owned by the specified Wallet.

{% hint style="info" %}
* Request headers required. See [Request Headers](../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name           | Conditions      |
| -------------- | --------------- |
| `Wallets:Read` | Always Required |

## Parameters <a href="#request-example.1" id="request-example.1"></a>

### Path parameters <a href="#path-parameters" id="path-parameters"></a>

| Path parameter | Description                                                              |
| -------------- | ------------------------------------------------------------------------ |
| `walletId`     | Unique identifier of the `Wallet`. ex. `wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx` |

## Response <a href="#response" id="response"></a>

### 200 Response example <a href="#response-example" id="response-example"></a>

`verified` contracts have been certified authentic by CoinMarketCap.

```json
{
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "Ethereum",
  "assets": [
    {
      "symbol": "ETH",
      "decimals": 18,
      "balance": "1000000000000000000",
      "verified": true
    },
    {
      "contract": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
      "name": "USD Coin",
      "symbol": "USDC",
      "decimals": 6,
      "balance": "100000000000000000000",
      "verified": true
    },
    {
      "contract": "0x514910771af9ca656af840dff83e8264ecf986ca",
      "name": "Chainlink Token",
      "symbol": "LINK",
      "decimals": 18,
      "balance": "20000000000000000000",
      "verified": true
    }
  ]
}
```

## Algorand <a href="#algorand" id="algorand"></a>

### Algorand Standard Assets <a href="#alogrand-asa" id="alogrand-asa"></a>

Get Algorand standard assets, or [ASAs](https://developer.algorand.org/docs/get-details/asa/).

#### 200 response example <a href="#asa-request-body" id="asa-request-body"></a>

```json
{
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "Algorand",
  "assets": [
    {
       "kind": "Asa",
       "assetId": "xxxxxxxxx",
       "symbol": "USDC",
       "decimals": 6,
       "verified": true,
       "balance": "200000000"
    },
    ...
}
```

## Solana <a href="#evm" id="evm"></a>

### Solana Program Library Token (SPL) <a href="#solana-spl" id="solana-spl"></a>

Get [SPL tokens](https://spl.solana.com/token).

#### 200 response example <a href="#spl-request-body" id="spl-request-body"></a>

```json
{
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "Solana",
  "assets": [
    {
      "kind": "Spl",
      "mint": "4zMMC9srt5RixxxxxxxxxxxxxEERYPJgZJDncDU",
      "symbol": "USDC",
      "decimals": 6,
      "balance": "10000000"
    },
    ...
}
```

### Solana Program Library Token 2022 (SPL-2022) <a href="#solana-spl2022" id="solana-spl2022"></a>

Get [SPL 2022 tokens](https://spl.solana.com/token-2022).

#### 200 response example <a href="#spl2022-request-body" id="spl2022-request-body"></a>

```json
{
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "Solana",
  "assets": [
    {
        "kind": "Spl2022",
        "mint": "DnECowYZoUEyqfxxxxxxxxxxxxx5v4iyToZxFjDZ",
        "symbol": "Dfns22",
        "decimals": 8,
        "balance": "99999999999"
    },
    ...
}
```

## Stellar <a href="#solana-spl2022" id="solana-spl2022"></a>

### Classic Stellar Assets (SEP-41) <a href="#stellar-sep41" id="stellar-sep41"></a>

Get classic [Stellar Assets](https://developers.stellar.org/docs/issuing-assets/anatomy-of-an-asset). They all implement the [SEP-41 token interface](https://github.com/stellar/stellar-protocol/blob/master/ecosystem/sep-0041.md).

#### 200 response example <a href="#sep41-request-body" id="sep41-request-body"></a>

```json
{
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "Stellar",
  "assets": [
    {
        "kind": "Sep41",
        "mint": "GBBD47IF6LWK7P7xxxxxxxxxxxxT4AQH3ZLLFLA5",
        "symbol": "USDC",
        "decimals": 8,
        "balance": "99999999999"
    },
    ...
}
```

## TON <a href="#solana" id="solana"></a>

### TON TEP74 Jetton <a href="#solana-spl" id="solana-spl"></a>

Get [Jetton](https://github.com/ton-blockchain/TEPs/blob/master/text/0074-jettons-standard.md).

#### 200 response example <a href="#spl-request-body" id="spl-request-body"></a>

```json
{
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "Ton",
  "assets": [
    {
        "kind": "Tep74",
        "mint": "kQBFAGo2Af6xxxxxxxxxxxW_cWLyFz",
        "symbol": "Dfns",
        "decimals": 9,
        "balance": "99999999999999"
    },
    ...
}
```

## TRON <a href="#tron" id="tron"></a>

### TRC-10 <a href="#tron-trc10" id="tron-trc10"></a>

Get TRON's TRC-10 fungible tokens

#### 200 response example <a href="#sep41-request-body" id="sep41-request-body"></a>

```json
{
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "Tron",
  "assets": [
    {
        "kind": "Trc10",
        "tokenId": "1005273",
        "symbol": "DFNS",
        "decimals": 6,
        "balance": "10000000997"
    },
    ...
}
```



#### TRC-20 <a href="#trc10-request-body" id="trc10-request-body"></a>

Get fungible tokens that implement the TRC-20 smart contract specification.

#### 200 response example <a href="#sep41-request-body" id="sep41-request-body"></a>

```json
{
  "walletId": "wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx",
  "network": "Tron",
  "assets": [
    {
        "kind": "Trc20",
        "contract": "TXLAQ63XgxxxxxxxxxxEmLMEqcdj",
        "symbol": "USDT",
        "decimals": 6,
        "balance": "99999000000"
    },
    ...
}
```
