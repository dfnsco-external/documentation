# API objects

## Client Data

The Client Data object is used during [user registration](../../api-docs/authentication/registration/completeUserRegistration.md), [new credential creation](../../api-docs/authentication/credential-management/api-reference/createusercredential-1.md), [login](../../api-docs/authentication/login/completeLogin.md) and [action signing](../../api-docs/authentication/user-action-signing/completeUserActionSigning.md)

### Fido2 credential

When using Fido2, the `client data` object is built inside the authenticator and returned to the browser `base64url` encoded. There is no need to modify it.&#x20;

More information can be found in the [W3C webauthn offical specification](https://w3c.github.io/webauthn/#dictdef-collectedclientdata)

| field       | type      | description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ----------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| type        | `string`  | <p> <code>webauthn.create</code> for <a href="../../api-docs/authentication/registration/completeUserRegistration.md#fido2-credential">registration</a> and <a href="../../api-docs/authentication/credential-management/api-reference/createusercredential-1.md#fido2-credential">new credential</a></p><p><code>webauthn.get</code> for <a href="../../api-docs/authentication/login/completeLogin.md#fido2-credential">login</a> and <a href="../../api-docs/authentication/user-action-signing/completeUserActionSigning.md#fido2-credential">action signing</a></p> |
| challenge   | `string`  | The challenge returned from the init call                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| origin      | `string`  | The origin in which the app is being executed                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| crossOrigin | `boolean` | A flag indicating if the current call is running cross origin; in most cases this should be `false`                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

#### Example

```typescript
// Client data as returned by the authenticator during registration
eyJ0eXBlIjoid2ViYXV0aG4uY3JlYXRlIiwiY2hhbGxlbmdlIjoiWTJndE16bGxORFl0YUdKdGRtMHRPR3gwY1hFemMybzBPRGczWlRkd09BIiwib3JpZ2luIjoiaHR0cHM6Ly9hcHAuZGZucy5uaW5qYSIsImNyb3NzT3JpZ2luIjpmYWxzZX0

// Client data object
{
  "type":"webauthn.create",
  "challenge":"Y2gtMzllNDYtaGJtdm0tOGx0cXEzc2o0ODg3ZTdwOA",
  "origin":"https://app.dfns.ninja",
  "crossOrigin":false
}
```

### Key, Password Protected Key and Recovery credential

Unlike when using `Fido2 credential`, the client data object needs to be created manually for `Key credential`. Once created the object needs to be "stringified" and base64url encoded.

| field     | type     | description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| --------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type      | `string` | <p> <code>key.create</code> for <a href="../../api-docs/authentication/registration/completeUserRegistration.md#key-credential">registration</a> and <a href="../../api-docs/authentication/credential-management/api-reference/createusercredential-1.md#fido2-credential">new credential</a><br><code>key.get</code> for <a href="../../api-docs/authentication/login/completeLogin.md#key-credential">login</a> and <a href="../../api-docs/authentication/user-action-signing/completeUserActionSigning.md#key-credential">action signing</a></p> |
| challenge | `string` | The challenge returned from the init call. The challenge is already base64url encoded, there is no need to encode it                                                                                                                                                                                                                                                                                                                                                                                                                                  |

#### Example

```typescript
// Client data object
{
  "challenge":"Y2gtNzloaHQtbXJlb2stOGFwOHFtMmVpZWZ0amxhZw",
  "type":"key.create"
}

// Stringify
'{"challenge":"Y2gtNzloaHQtbXJlb2stOGFwOHFtMmVpZWZ0amxhZw","type":"key.create"}'

// Base64url
eyJjaGFsbGVuZ2UiOiJZMmd0Tnpsb2FIUXRiWEpsYjJzdE9HRndPSEZ0TW1WcFpXWjBhbXhoWnciLCJ0eXBlIjoia2V5LmNyZWF0ZSJ9
```

## Attestation Data

This attestation data object is used during [registration](../../api-docs/authentication/registration/completeUserRegistration.md) and [new credential](../../api-docs/authentication/credential-management/api-reference/createusercredential-1.md).

### Fido2 credential

When using Fido2, the `attestation data` object is built inside the authenticator and returned to the browser. It is encoded using [CBOR specification](https://cbor.io/). There is no need to modify it.&#x20;

More information can be found in the [W3C webauthn offical specification](https://w3c.github.io/webauthn/#dom-authenticatorattestationresponse-attestationobject).&#x20;

It is an opaque object and there is no need to describe it here.

#### Example

```typescript
// Attestation Data as returned by the authenticator during registration
o2NmbXRmcGFja2VkZ2F0dFN0bXSjY2FsZyZjc2lnWEcwRQIgVHg5PQ_mEyPi_FRZdkgT-SXmspljVaOWJBcN3M0iDxoCIQC8dJkvMWREoJrEdgECSRWzUxXG0WbrpCiajYEJ8mNF5mN4NWOBWQLdMIIC2TCCAcGgAwIBAgIJANVbnGiXosqIMA0GCSqGSIb3DQEBCwUAMC4xLDAqBgNVBAMTI1l1YmljbyBVMkYgUm9vdCBDQSBTZXJpYWwgNDU3MjAwNjMxMCAXDTE0MDgwMTAwMDAwMFoYDzIwNTAwOTA0MDAwMDAwWjBvMQswCQYDVQQGEwJTRTESMBAGA1UECgwJWXViaWNvIEFCMSIwIAYDVQQLDBlBdXRoZW50aWNhdG9yIEF0dGVzdGF0aW9uMSgwJgYDVQQDDB9ZdWJpY28gVTJGIEVFIFNlcmlhbCAxNzU1MDc3NTg5MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEAQap0H_qWf7Lo9-qH8Xj2q-EtN-D_jO0JWxfoyafAdQRcIHIhUMixqtqB9fd5M95L0F4zS7Pvbe5EIA_tns7naOBgTB_MBMGCisGAQQBgsQKDQEEBQQDBQQDMCIGCSsGAQQBgsQKAgQVMS4zLjYuMS40LjEuNDE0ODIuMS43MBMGCysGAQQBguUcAgEBBAQDAgUgMCEGCysGAQQBguUcAQEEBBIEEO6IKHlyHEkTl3U9_M6XByowDAYDVR0TAQH_BAIwADANBgkqhkiG9w0BAQsFAAOCAQEAhDTK-uoXyNUKvzPk-mTjRykakGfJx6CXWJHJAR_zdkHQHaNA-SB8z3a2lmn9sBKI2_-9T3Pasj4gaaXiQxqOXbifp8Iv5nz7rKtmmMuur_u4-XMkOo-wLdZvcjwj-jWdX0daFGmRU0Yck4tYw6-Y_hJ_L8mNT_Odu2jqY3--WlZ8T9H-c9BYhz3dG1MCiQpYH_tw5sz0LXuSFrM3tF_0yEehgtwDwANby9OG7KqUf7O0ArvpBcFFPj8lJf_1_6qXkwFSYxZZzKXHwNsumEdpB7is-X6M4sWG_dcl6msj-hQdtWpxokCWzymdlUG5mk541vtzqpMjM6UvREg1wWjoXmhhdXRoRGF0YVjCtP0s4DAIslfywtetyFg4YfWUmc3GcqHTevk0O6OswibFAAAAAe6IKHlyHEkTl3U9_M6XByoAMEmPqgPRjx312Ywh4gex7TgWLwE2kWRNFGsBomlOLuGIceqoSXgbyAXJgksJs_8_nqUBAgMmIAEhWCBJj6oD0Y8d9dmMIeIHCSUgwKr6FKqaytOhNxEZnGoOYiJYIPGzFTyHoPed-ysej7WwkaaHydkatYmz0rInky2TzyiKoWtjcmVkUHJvdGVjdAI

// CBOR decoded Attestation Data
{
  "authData" : {
    "rpIdHash" : "tP0s4DAIslfywtetyFg4YfWUmc3GcqHTevk0O6OswiY=",
    "flags" : -59,
    "signCount" : 1,
    "attestedCredentialData" : {
      "aaguid" : {
        "value" : "ee882879-721c-4913-9775-3dfcce97072a",
        "bytes" : "7ogoeXIcSROXdT38zpcHKg=="
      },
      "credentialId" : "SY+qA9GPHfXZjCHiB7HtOBYvATaRZE0UawGiaU4u4Yhx6qhJeBvIBcmCSwmz/z+e",
      "cosekey" : {
        "1" : "2",
        "3" : -7,
        "-1" : 1,
        "-2" : "SY+qA9GPHfXZjCHiBwklIMCq+hSqmsrToTcRGZxqDmI=",
        "-3" : "8bMVPIeg9537Kx6PtbCRpofJ2Rq1ibPSsieTLZPPKIo=",
        "1" : 2
      }
    },
    "extensions" : {
      "credProtect" : 2
    },
    "flagUP" : true,
    "flagUV" : true,
    "flagAT" : true,
    "flagED" : true
  },
  "attStmt" : {
    "alg" : -7,
    "sig" : "MEUCIFR4OT0P5hMj4vxUWXZIE/kl5rKZY1WjliQXDdzNIg8aAiEAvHSZLzFkRKCaxHYBAkkVs1MVxtFm66Qomo2BCfJjReY=",
    "x5c" : [ "MIIC2TCCAcGgAwIBAgIJANVbnGiXosqIMA0GCSqGSIb3DQEBCwUAMC4xLDAqBgNVBAMTI1l1YmljbyBVMkYgUm9vdCBDQSBTZXJpYWwgNDU3MjAwNjMxMCAXDTE0MDgwMTAwMDAwMFoYDzIwNTAwOTA0MDAwMDAwWjBvMQswCQYDVQQGEwJTRTESMBAGA1UECgwJWXViaWNvIEFCMSIwIAYDVQQLDBlBdXRoZW50aWNhdG9yIEF0dGVzdGF0aW9uMSgwJgYDVQQDDB9ZdWJpY28gVTJGIEVFIFNlcmlhbCAxNzU1MDc3NTg5MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEAQap0H/qWf7Lo9+qH8Xj2q+EtN+D/jO0JWxfoyafAdQRcIHIhUMixqtqB9fd5M95L0F4zS7Pvbe5EIA/tns7naOBgTB/MBMGCisGAQQBgsQKDQEEBQQDBQQDMCIGCSsGAQQBgsQKAgQVMS4zLjYuMS40LjEuNDE0ODIuMS43MBMGCysGAQQBguUcAgEBBAQDAgUgMCEGCysGAQQBguUcAQEEBBIEEO6IKHlyHEkTl3U9/M6XByowDAYDVR0TAQH/BAIwADANBgkqhkiG9w0BAQsFAAOCAQEAhDTK+uoXyNUKvzPk+mTjRykakGfJx6CXWJHJAR/zdkHQHaNA+SB8z3a2lmn9sBKI2/+9T3Pasj4gaaXiQxqOXbifp8Iv5nz7rKtmmMuur/u4+XMkOo+wLdZvcjwj+jWdX0daFGmRU0Yck4tYw6+Y/hJ/L8mNT/Odu2jqY3++WlZ8T9H+c9BYhz3dG1MCiQpYH/tw5sz0LXuSFrM3tF/0yEehgtwDwANby9OG7KqUf7O0ArvpBcFFPj8lJf/1/6qXkwFSYxZZzKXHwNsumEdpB7is+X6M4sWG/dcl6msj+hQdtWpxokCWzymdlUG5mk541vtzqpMjM6UvREg1wWjoXg" ]
  },
  "fmt" : "packed"
}
```



### Key, Password Protected Key and Recovery credential

Unlike when using `Fido2 credential`, the attestation data object needs to be created manually for `Key credential`. Once created the object needs to be "stringified" and base64url encoded.

Before building the `attestation data` object, the `credential info fingerprint` object needs to be created.

#### Credential Info Fingerprint

The attestation data object contains a `signature`. This section explains how to construct the credential info fingerprint object that is then signed and included in the attestation data object.

| Field            | Type   | Description                                                                                            |
| ---------------- | ------ | ------------------------------------------------------------------------------------------------------ |
| `clientDataHash` | string | The hex encoded SHA-256 hash of the "stringified" [client data object ](api-objects.md#key-credential) |
| `publicKey`      | string | PEM encoded public key that can be used to verify the signature for the credential                     |

{% hint style="danger" %}
In order for the server to properly verify the signature, the `clientDataHash` needs to be computed in a reproducible way. That means the "stringified" operation of the client data object needs to be done with the following requirements:

* Keys need to be sorted in alphabetical order: `challenge` first, and then `type`
* Separators need to be `:` and `,` without any space before and after

For example given the following client data object

<pre class="language-typescript"><code class="lang-typescript"><strong>{
</strong>  "challenge":"Y2gtNzloaHQtbXJlb2stOGFwOHFtMmVpZWZ0amxhZw",
  "type":"key.create"
}
</code></pre>

The "stringified" version needs to be

```
{"challenge":"Y2gtNzloaHQtbXJlb2stOGFwOHFtMmVpZWZ0amxhZw","type":"key.create"}
```
{% endhint %}

**Example**

```typescript
// Client data object
{
  "challenge":"Y2gtNzloaHQtbXJlb2stOGFwOHFtMmVpZWZ0amxhZw",
  "type":"key.create"
}

// "Stringified" client data
{"challenge":"Y2gtNzloaHQtbXJlb2stOGFwOHFtMmVpZWZ0amxhZw","type":"key.create"}

// SHA256 hex encoded
cba00cc2224e76aa12e42cd0e30a1a73e5525ed0dccb7e29e709fee3a1e98dec

// Credential info fingerprint object
{
  "clientDataHash": "cba00cc2224e76aa12e42cd0e30a1a73e5525ed0dccb7e29e709fee3a1e98dec",
  "publicKey": "-----BEGIN PUBLIC KEY-----\nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE9cG2mE4DWHbwwlLRSKBLZ9m6+QsC\neOqWJh1x5VvRHZMaPLQlRrhhgbHm8una4h8S+L5o8sV8Hvujbl3MrATj3Q==\n-----END PUBLIC KEY-----\n"
}
```

So the `attestation data` object is built with the following fields

| Field       | Type   | Description                                                                                                                                                                                                                                                                              |
| ----------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `publicKey` | string | PEM encoded public key that can be used to verify the signature for the credential. This the same public key than the one in the [Credential Info Fingerprint ](api-objects.md#credential-info-fingerprint)object                                                                        |
| `signature` | string | The signature produced by signing the "stringified" [Credential Info Fingerprint](api-objects.md#credential-info-fingerprint) object with the credentials private key, using the algorithm specified in `algorithm`. Needs to be encoded as a hex string                                 |
| `algorithm` | string | <p><code>Optional</code> The algorithm/digest that the credential will use to sign data. If the algoritm is not specified the algorithm will be determined by the key. Can be one of the following choices:<br><code>RSA-SHA256</code><br><code>SHA256</code><br><code>SHA512</code></p> |

{% hint style="danger" %}
In order for the server to properly verify the signature, the "stringified" operation of the Credential Info Fingerprint object needs to be done with the following requirements:

* Keys need to be sorted in alphabetical order: `clientDataHash` first, then `publicKey`
* Separators need to be `:` and `,` without any space before and after

For example given the following client data object

```typescript
{
  "clientDataHash": "cba00cc2224e76aa12e42cd0e30a1a73e5525ed0dccb7e29e709fee3a1e98dec",
  "publicKey": "-----BEGIN PUBLIC KEY-----\nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE9cG2mE4DWHbwwlLRSKBLZ9m6+QsC\neOqWJh1x5VvRHZMaPLQlRrhhgbHm8una4h8S+L5o8sV8Hvujbl3MrATj3Q==\n-----END PUBLIC KEY-----\n"
}
```

The "stringified" version needs to be

```
{"clientDataHash":"cba00cc2224e76aa12e42cd0e30a1a73e5525ed0dccb7e29e709fee3a1e98dec","publicKey":"-----BEGIN PUBLIC KEY-----\nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE9cG2mE4DWHbwwlLRSKBLZ9m6+QsC\neOqWJh1x5VvRHZMaPLQlRrhhgbHm8una4h8S+L5o8sV8Hvujbl3MrATj3Q==\n-----END PUBLIC KEY-----\n"}
```
{% endhint %}

{% hint style="danger" %}
The signature also needs to be generated with pre-defined hash algorithm and encoding:

* Hash algorithm: `SHA256`
* Encoding: `DER`. See [Signature Format](credentials/generate-a-key-pair.md#signature-format) for more information

#### Signing Example

```typescript
keyOrPasswordClientData: {
  type: 'key.create',
  challenge: challenge,
}

const clientData = Buffer.from(JSON.stringify(supportedCredentials.credentialData.keyOrPasswordClientData))
const signaturePayload = Buffer.from(JSON.stringify({
  clientDataHash: crypto.createHash('sha256').update(clientData).digest('hex'),
  publicKey: newKey.publicKey,
}))
const signature = crypto.sign(undefined, signaturePayload, newKey.privateKey)
```
{% endhint %}

#### Example

<pre class="language-typescript"><code class="lang-typescript">// Hex encoded signature of the string '{"clientDataHash":"cba00cc2224e76aa12e42cd0e30a1a73e5525ed0dccb7e29e709fee3a1e98dec","publicKey":"-----BEGIN PUBLIC KEY-----\nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE9cG2mE4DWHbwwlLRSKBLZ9m6+QsC\neOqWJh1x5VvRHZMaPLQlRrhhgbHm8una4h8S+L5o8sV8Hvujbl3MrATj3Q==\n-----END PUBLIC KEY-----\n"}'
30460221008e0109848c6fc83004d0e6c7fdac71dae8524fc5a29081d012f865416986ce29022100f47e1bee6c5175c4488b143c936c68fac1ae7e7931e76c677d46331ed149d17d

// Attestation Data object
{
  "publicKey": "-----BEGIN PUBLIC KEY-----\nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE9cG2mE4DWHbwwlLRSKBLZ9m6+QsC\neOqWJh1x5VvRHZMaPLQlRrhhgbHm8una4h8S+L5o8sV8Hvujbl3MrATj3Q==\n-----END PUBLIC KEY-----\n",
  "signature": "30460221008e0109848c6fc83004d0e6c7fdac71dae8524fc5a29081d012f865416986ce29022100f47e1bee6c5175c4488b143c936c68fac1ae7e7931e76c677d46331ed149d17d"
}

// Stringify
<strong>'{"publicKey": "-----BEGIN PUBLIC KEY-----\nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE9cG2mE4DWHbwwlLRSKBLZ9m6+QsC\neOqWJh1x5VvRHZMaPLQlRrhhgbHm8una4h8S+L5o8sV8Hvujbl3MrATj3Q==\n-----END PUBLIC KEY-----\n","signature":"30460221008e0109848c6fc83004d0e6c7fdac71dae8524fc5a29081d012f865416986ce29022100f47e1bee6c5175c4488b143c936c68fac1ae7e7931e76c677d46331ed149d17d"}'
</strong>
// Base64url
eyJwdWJsaWNLZXkiOiAiLS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS1cbk1Ga3dFd1lIS29aSXpqMENBUVlJS29aSXpqMERBUWNEUWdBRTljRzJtRTREV0hid3dsTFJTS0JMWjltNitRc0NcbmVPcVdKaDF4NVZ2UkhaTWFQTFFsUnJoaGdiSG04dW5hNGg4UytMNW84c1Y4SHZ1amJsM01yQVRqM1E9PVxuLS0tLS1FTkQgUFVCTElDIEtFWS0tLS0tXG4iLCJzaWduYXR1cmUiOiIzMDQ2MDIyMTAwOGUwMTA5ODQ4YzZmYzgzMDA0ZDBlNmM3ZmRhYzcxZGFlODUyNGZjNWEyOTA4MWQwMTJmODY1NDE2OTg2Y2UyOTAyMjEwMGY0N2UxYmVlNmM1MTc1YzQ0ODhiMTQzYzkzNmM2OGZhYzFhZTdlNzkzMWU3NmM2NzdkNDYzMzFlZDE0OWQxN2QifQ
</code></pre>

