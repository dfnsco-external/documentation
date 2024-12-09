# Sign and Broadcast Transaction from Wallet

`POST /wallets/{walletId}/transactions`

Sign & Broadcast transaction enables communication with any arbitrary smart contract by replicating the native transaction protocol fields in the body of the request.  It executes a signature, constructs the transaction for the target chain, and then broadcasts the transaction.  It can be used to make native payments, call smart contract functions, and even deploy new smart contracts. Note for reading from a "view" function on EVM chains, please use [Call Read Function](broken-reference).

{% hint style="info" %}
* User action signature required. See [User Action Signing](../../authentication/user-action-signing/) for more information.
* Request headers required. See [Request Headers](../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name                           | Conditions      |
| ------------------------------ | --------------- |
| `Wallets:BroadcastTransaction` | Always Required |

## Parameters <a href="#request-example.1" id="request-example.1"></a>

### Path parameters <a href="#path-parameters" id="path-parameters"></a>

<table><thead><tr><th width="250">Path parameter</th><th>Description</th></tr></thead><tbody><tr><td><code>walletId</code></td><td>Unique identifier of the <code>Wallet</code>. ex. <code>wa-1f04s-lqc9q-xxxxxxxxxxxxxxxx</code></td></tr></tbody></table>

## Request Body

The body of the request will depend on the chain you are targeting.   Please find the chain in question by expanding this section in the left hand navigation:

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

## Response Statuses

<table><thead><tr><th width="167">Status</th><th>Definition</th></tr></thead><tbody><tr><td><code>Pending</code></td><td>The request is pending approval due to a <a href="https://docs.dfns.co/d/api-docs/policy-engine/policies#wallets-sign-activity">policy applied</a> to the wallet</td></tr><tr><td><code>Executing</code></td><td>The request is approved and is in the process of being executed (note this status is only set for a short time between pending and broadcasted)</td></tr><tr><td><code>Broadcasted</code></td><td>The transaction has been successfully written to the mempool</td></tr><tr><td><code>Confirmed</code></td><td>The transaction has been confirmed on-chain by our indexing pipeline</td></tr><tr><td><code>Failed</code></td><td>Indicates a system failure to complete the request</td></tr><tr><td><code>Rejected</code></td><td>The request has been rejected by a policy approval action</td></tr></tbody></table>

