# API Authentication

To use Dfns API endpoints, you will generally need to:

1. Get an authentication token (referred to as "authentication token" or "token").
2. Sign a User Action Challenge using a cryptographic key that you own (referred to "Credential Key" or just "Credentials"). This is only required for actions which mutate state (non-readonly api calls).

{% hint style="info" %}
Check [request-headers.md](request-headers.md "mention") for more details about which headers are required
{% endhint %}

## Get an authentication token

There are several ways to get an authentication token, depending on what kind of "identity" you'll be using. You can authenticate as:

* A User (human :man:)
* A Service Account (machine :robot:)

As a User, there are two ways to get an authentication token:

* Follow the [Login](../api-docs/authentication/login/) flow. You'll get a authentication token at the end of this flow, which expires after a relatively short period of time.
* Or create a [Personal Access Token](../api-docs/authentication/personal-access-token-management/) (PAT), which basically is a long-lived authentication token which is tied to the User and represents them. This PAT will never have more permissions than the User itself. Once you have created a PAT, you can use it as an authentication token directly.

A [Service Account Token](../api-docs/authentication/service-account-management/) (or "Service Account") is not tied to one User, it's existence is visible across all your organisation. It is a long-lived token, and any permission can be granted to it, beyond the scope of one particular user. Once created, this Service Account can be used as an authentication token directly.

{% hint style="info" %}
Once generated, Dfns system do not keep a trace of your long-lived authentication tokens (Personal Access Token, or Service Account Token), only you will hold on to those. If you lose them, you'll just need to create a new one.
{% endhint %}

Once you have an authentication token, you can add it in the headers of your API requests as an Bearer token: `Authorization: Bearer {auth_token}`.

## Sign API requests (User Action Signing)

Most endpoints which induce some state change in Dfns (everything that is not a GET essentially), will require you to sign the actual request, before being able to execute it. We call that "User Action Signing".

We provide a couple of [User Action Signing](../api-docs/authentication/user-action-signing/) endpoints which handle this flow:

* You tell Dfns "I want to perform this exact request"
* Dfns sends you back a challenge to be signed with your Credential .
* You sign the challenge with your Credentials (essentially a cryptographic key you registered), and send it to Dfns.
* Dfns gives you back a "user action signature", which you'll need include in the headers when you perform the actual request (`X-DFNS-USERACTION` header)

The credential -- essentially being a cryptographic key -- you'll need to use to sign the challenge will depend on who is calling the api (User / Service Account), see more about that below.

## Credentials

In order to complete the [login](../api-docs/authentication/login/ "mention") flow, or to sign User Action Challenges, you'll need to sign challenges using Credentials.

A Credential is essentially a Public/Private keypair. The Private key is only known by you, while the Public key is provided to Dfns, so that Dfns system can be aware that you own this key, and can register it as being yours.

The first time you registered with Dfns, you had to create a Credential (if you used Dfns Dashboard, it prompted you to with WebAuthn prompts). You can also register different credentials later on using our API.

Different kind of Credentials will be created, depending on your use case, and how you prefer to manage those credentials:

* WebAuthn Credentials -> Use WebAuthn standard to create/manage those keys for you on your device (see more about that below). You can use WebAuthn Credentials if you need a User signature in-browser for example.
* Key Credentials -> "manually" generate keypairs yourself, and store them however you see fit (see [How to generate a keypair](../advanced-topics/authentication/credentials/generate-a-key-pair.md)). You can use Key Credential if you need a Service Account sitting in your server to also be the signer for example.

Depending on the Identity you are using, the Credentials supported are such:

<table><thead><tr><th width="273.3333333333333">Identity</th><th>WebAuthn Credentials</th><th>Key Credentials</th></tr></thead><tbody><tr><td>User</td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr><tr><td>PAT (Personal Access Token)</td><td><span data-gb-custom-inline data-tag="emoji" data-code="1f6d1">🛑</span></td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr><tr><td>Service Account</td><td><span data-gb-custom-inline data-tag="emoji" data-code="1f6d1">🛑</span></td><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span></td></tr></tbody></table>

#### WebAuthn

WebAuthn is a web authentication standard supported by most modern browsers, which leverages your devices key-management features (like touch ID on a mac, a phone authenticator, a yubikey, etc).

Essentially, WebAuthn allows you to create cryptographic keys stored on one of your device (your device enclave), and use them to sign payloads when needed using touch ID (or else). Here's some screenshots with some examples of WebAuthn prompts shown in your browser during Credential creation, or during Signing using those Credentials.

![](<../.gitbook/assets/image (1) (1) (1) (1) (1).png>) ![](<../.gitbook/assets/image (2) (1).png>)

{% hint style="info" %}
You can read more about WebAuthn on [webauthn.guide](https://webauthn.guide/), and if you want you can test a WebAuthn demo on [webauthn.io](https://webauthn.io/)
{% endhint %}

