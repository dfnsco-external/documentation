# Delegated Signing

### What is Delegated Signing?

{% hint style="info" %}
Delegated Signing is an innovative approach to non-custodial wallet deployments leveraging Dfns' industry-leading authentication to create seamless user experiences enabled by familiar technologies [like biometrics](https://www.coindesk.com/tech/2023/05/09/crypto-security-firm-dfns-adds-biometric-support-to-wallet-development-toolkit/).  The goal of Delegated Signing is to optimize both user experience and security to onboard the next billion users to web3.&#x20;
{% endhint %}

As we have engaged with market segments such as DeFi, tokenization, and payments, we have seen significant demand for a solution which can delegate custodial responsibility to the end-user while preserving a seamless, Web2-like onboarding experience to ensure high conversion rates.  In response, we have designed a configuration we call Delegated Signing, which gives end-users all the benefits of a self-custodial wallet like Metamask without forcing them to securely persist a 24-word seed phrase (or any other mnemonic secret).

This page describes the architecture behind our Delegated Signing solution, how custody is delegated via an API signing secret, and how wallets can be recovered in the case of lost devices.&#x20;

### Delegated Signing Architecture

The following diagram illustrates the Delegated Signing architecture:&#x20;

<figure><img src="../.gitbook/assets/DelegatedSigningDiagram.png" alt=""><figcaption></figcaption></figure>

The key shares (labeled 1 through 5 above on the right side) are encrypted and stored in our decentralized signer network. Our signers implement leading peer-reviewed cryptographic protocols for distributed key generation and threshold signatures. They are spread across Tier 3+ data centers and different geographical regions to ensure high levels of service availability, fault-tolerance, and business continuity.&#x20;

Our decentralized custody network will be extended to include permissioned partners who are compensated for generating keys and signing transactions. No single entity will hold a threshold of key shares for any given wallet (i.e., the total number of key shares required to move assets on-chain). Hence, Dfns can guarantee high levels of governance neutrality, demonstrate that the private key does not exist in its full format, and ensure that no single party can move assets unilaterally.&#x20;

Instead, our design scheme suggests that control of the API is the new proof of custodianship.  This control is dictated by the combination of an access token and a signing secret as described above in [Authentication](authentication/). Using Dfns, platforms can decide whether they want to keep the API control on-premise (custodial mode) or delegate it to their end-users’ devices exempting themselves from any custodial responsibility (end-user-custodial mode, or self-custodial mode).

This architecture ensures that transactions can only be executed via the API. Therefore, API security becomes paramount. The Dfns security team has built a multi-level authentication, authorization, and governance architecture to ensure full accountability of API usage. At the heart of this architecture are the signing secrets (illustrated with the key icons on the left side of the diagram above).

Dfns’ [Authentication](../api-docs/authentication/) requires that all requests to the API capable of changing state in the system or on chain (POST, PUT, and DELETE requests specifically) must be signed by a secret known only to the custodian of the assets. The authentication service validates the signature and writes an immutable log of the transaction, cryptographically proving the source of the API call.&#x20;

### Delegated Signing User Experience

In a Delegated Signing configuration, our client delegates custodianship to their end-user by building an onboarding flow in which the user generates and persists a signing secret on their device. In order to enable frictionless user experiences, we have integrated the [WebAuthn 3.0](http://webauthn.guide) passwordless open protocol. WebAuthn is natively integrated into all major browsers and mobile operating systems, exposing seamless access to biometrics, pin codes, and Yubikeys. This enables an onboarding flow such as the following:

1. The user registers with your product using the credentials of their choice including social login.
2. They create a wallet, establishing signing credentials via biometrics. This is executed via a simple Dfns API call.&#x20;
3. They purchase or transfer assets into the wallet. When they want to execute an outbound transaction, they verify via biometrics again, silently signing the transaction on their device.

The Delegated Signing architecture relieves the Dfns client from the regulatory burden of financial custodianship by ensuring that they do not have the ability to manipulate the assets of their users on chain. This is cryptographically proven given that they never have access to the signing secret kept by their user which is required to execute transactions against the API.&#x20;

### Delegated Signing Wallet Recovery

Most self-custodial wallets like Metamask or Ledger require users to indefinitely safekeep a 24-word seed phrase to guarantee access to their assets. Even early adopters of cryptocurrencies who are familiar with the technical constraints of self-custodial wallets have proven time and again incapable of managing keys safely, as seen most recently in the unfortunate case of [a bitcoin core developer losing his funds](https://cointelegraph.com/news/bitcoin-core-developer-claims-to-have-lost-200-btc-in-hack).&#x20;

By leveraging an API signing secret as a proxy for the blockchain private key, the risks described above are substantially mitigated. Unlike a blockchain private key, the signing secret is not immutable. If it is stolen, it can be revoked. If it is lost, it can be re-established. See below a table comparing the properties between a blockchain-bound Private Key and an API Key.

<figure><img src="../.gitbook/assets/Screenshot 2023-07-05 at 3.17.57 PM.png" alt=""><figcaption></figcaption></figure>

Of course, there are trade-offs. This architecture creates new potential attack vectors for bad actors. For instance, a hacker may impersonate a legitimate user in order to gain access to their wallet via the recovery process. In order to mitigate this risk, Dfns provides guidance on recovery mechanisms which our clients can implement.

For example, Dfns clients can implement "biometric recovery" by encouraging users to [create secondary credentials](../api-docs/authentication/credential-management/api-reference/createusercredential-1.md) on alternate devices. In this way, if a user lose access to their primary device (for example, their phone), they can still access their wallet via a secondary device like their laptop.&#x20;

Additionally, similar to password manager solutions like 1Password, end-users can receive a recovery code which they are instructed to keep securely offline. Should the user lose their device, this code can be provided via the Dfns client’s application in order to re-establish access to their wallet.

Alternatively, some Dfns clients require their end-users to go through a KYC process. This process can be re-initiated for users who have lost access to their devices in order to validate their identities and re-establish credentials.  The KYC vendor in this case serves as a gate to a recovery secret the user can access once checks are passed that can be used to sign a recovery challenge.&#x20;

### Questions on Delegated Signing

Delegated Signing is an innovative approach to optimizing the security and user experience of noncustodial wallets. Dfns is committed to partnering with our customers to onboard the next billion users through familiar technologies like biometrics.

Have a follow up question on Delegated Signing?  Feel free to reach out to us in DfnsCare or at docs@dfns.co.&#x20;
