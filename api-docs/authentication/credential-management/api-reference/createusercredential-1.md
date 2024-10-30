# Create Credential

`POST /auth/credentials`

Part of the flow [Create Credential Regular flow](../../../../advanced-topics/authentication/credentials/#regular-flow).

Adds a new credential to a user's account. See [Credential Kinds](../../../../advanced-topics/authentication/credentials/#credential-kinds) for all supported credential types.

{% hint style="info" %}
* User action signature required. See [User Action Signing](../../user-action-signing/) for more information.
* Request headers required. See [Request Headers](../../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

{% hint style="info" %}
The permissions apply to the application only.
{% endhint %}

| Name                | Conditions      |
| ------------------- | --------------- |
| `Auth:Creds:Create` | Always Required |

## Request body

<table><thead><tr><th width="254">Field</th><th width="110.33333333333331">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>challengeIdentifier</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>Challenge identifier returned by the <a href="createusercredentialchallenge.md">Create User Credential Challenge</a> call</td></tr><tr><td><code>credentialName</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>Name the user is assigning to this credential</td></tr><tr><td><code>credentialKind</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>Kind of credential being registered (see <a href="../#credential-kinds">Credential Kind</a>)</td></tr><tr><td><code>credentialInfo</code> <mark style="color:red;">*</mark></td><td><code>Object</code></td><td>An object containing information about the credential being registered</td></tr><tr><td><code>encryptedPrivateKey</code></td><td><code>String</code></td><td>Only for Password Protected Key and Recovery Key</td></tr></tbody></table>

### Fido2 Credential

<table><thead><tr><th></th><th width="109.33333333333331"></th><th></th></tr></thead><tbody><tr><td><code>credentialKind</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>will always be <code>Fido2</code></td></tr><tr><td><code>credentialInfo</code> <mark style="color:red;">*</mark></td><td><code>Object</code></td><td>See fields below</td></tr><tr><td><code>credentialInfo.credId</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded id of the credential</td></tr><tr><td><code>credentialInfo.clientData</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded client data object. The underlying object is the clientData object returned by the user's WebAuthn client</td></tr><tr><td><code>credentialInfo.attestationData</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded attestation data object. The underlying object is the attestationData object returned by the user's WebAuthn client</td></tr></tbody></table>

#### Example

```json
{
  "challengeIdentifier":"eyJ0e...fQNA",
  "credentialName": "My Credential",
  "credentialKind":"Fido2",
  "credentialInfo":{
    "credId":"c1QEdgnPLJargwzy3cbYKny4Q18u0hr97unXsF3DiE8",
    "clientData":"eyJ0eXBlIjoid2ViYXV0aG4uY3JlYXRlIiwiY2hhbGxlbmdlIjoiTVdNME1tWTVZVFEwTURSaU56ZGhOVEZoTnpZNU9EUXdOV0k1WlRRNFkyUmhPRFppTkRrM1pUWXpPVEU1T0dZeU1EY3haakJqWXprNE1tUTVZelkxTUEiLCJvcmlnaW4iOiJodHRwczovL2FwcC5kZm5zLm5pbmphIiwiY3Jvc3NPcmlnaW4iOmZhbHNlfQ",
    "attestationData":"WT-zFZUBbJHfBkmhzTlPf49LTn7asLeTQKhm_riCvFgFAAAAAA"
  }
}
```

### Key Credential

<table><thead><tr><th></th><th width="120.33333333333331"></th><th></th></tr></thead><tbody><tr><td><code>credentialKind</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>will always be <code>Key</code></td></tr><tr><td><code>credentialInfo</code> <mark style="color:red;">*</mark></td><td><code>Object</code></td><td>See fields below</td></tr><tr><td><code>credentialInfo.credId</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded id of the credential.  Note: This can be any unique value that identifies the credential (eg. account+key ID on AWS, the key's database ID, or the path to the key on disk)</td></tr><tr><td><code>credentialInfo.clientData</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded <a href="../../../../advanced-topics/authentication/api-objects.md#key-credential">Client Data</a> JSON string object that was signed with the user's private key</td></tr><tr><td><code>credentialInfo.attestationData</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded <a href="../../../../advanced-topics/authentication/api-objects.md#key-credential-1">Attestation Data</a> JSON string object with the users signature and public key</td></tr></tbody></table>

#### Example

```json
{
  "challengeIdentifier":"eyJ0e...fQNA",
  "credentialName": "My Credential",
  "credentialKind":"Key",
  "credentialInfo":{
    "credId":"6Ca6tAOFTx2odyJBnCoRO-gPvfpfy0EOoOcEaxfxIOk",
    "clientData":"eyJ0eXBlIjoia2V5LmNyZWF0ZSIsImNoYWxsZW5nZSI6Ik1XTTBNbVk1WVRRME1EUmlOemRoTlRGaE56WTVPRFF3TldJNVpUUTRZMlJoT0RaaU5EazNaVFl6T1RFNU9HWXlNRGN4WmpCall6azRNbVE1WXpZMU1BIiwib3JpZ2luIjoiaHR0cHM6Ly9hcHAuZGZucy5uaW5qYSIsImNyb3NzT3JpZ2luIjpmYWxzZX0",
    "attestationData":"eyJjaGFsbGVuZ2UiOiJZMmd0Tm1Oc2RHTXRiV05sWTNZdE9XRTRPV2QxYnpKd1lqYzBOVEp4Y2ciLCJjcm9zc09yaWdpbiI6ZmFsc2UsIm9yaWdpbiI6Imh0dHBzOi8vYXBwLmRmbnMud3RmIiwidHlwZSI6ImtleS5jcmVhdGUifQ"
  }
}
```

### Password Protected Key Credential

<table><thead><tr><th></th><th width="120.33333333333331"></th><th></th></tr></thead><tbody><tr><td><code>credentialKind</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>will always be <code>PasswordProtectedKey</code></td></tr><tr><td><code>credentialInfo</code> <mark style="color:red;">*</mark></td><td><code>Object</code></td><td>See fields below</td></tr><tr><td><code>credentialInfo.credId</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded id of the credential.  Note: This can be any unique value that identifies the credential (eg. account+key ID on AWS, the key's database ID, or the path to the key on disk)</td></tr><tr><td><code>credentialInfo.clientData</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded <a href="../../../../advanced-topics/authentication/api-objects.md#key-credential">Client Data</a> JSON string object that was signed with the user's private key</td></tr><tr><td><code>credentialInfo.attestationData</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded <a href="../../../../advanced-topics/authentication/api-objects.md#key-credential-1">Attestation Data</a> JSON string object with the users signature and public key</td></tr><tr><td><code>encryptedPrivateKey</code></td><td><code>String</code></td><td>Encrypted private key. The user should hold the secret to decrypting this value, and that secret should never be transmitted to Dfns</td></tr></tbody></table>

#### Example

```json
{
  "challengeIdentifier":"eyJ0e...fQNA",
  "credentialName": "My Recovery Credential",
  "credentialKind":"PasswordProtectedKey",
  "credentialInfo":{
    "credId":"GMkW0zlmcoMxI1OX0Z96LL_Mz7dgeu6vOH5_TOeGyNk",
    "clientData":"eyJ0eXBlIjoia2V5LmNyZWF0ZSIsImNoYWxsZW5nZSI6Ik1XTTBNbVk1WVRRME1EUmlOemRoTlRGaE56WTVPRFF3TldJNVpUUTRZMlJoT0RaaU5EazNaVFl6T1RFNU9HWXlNRGN4WmpCall6azRNbVE1WXpZMU1BIiwib3JpZ2luIjoiaHR0cHM6Ly9hcHAuZGZucy5uaW5qYSIsImNyb3NzT3JpZ2luIjpmYWxzZX0",
    "attestationData":"eyJjaGFsbGVuZ2UiOiJZMmd0Tm1Oc2RHTXRiV05sWTNZdE9XRTRPV2QxYnpKd1lqYzBOVEp4Y2ciLCJjcm9zc09yaWdpbiI6ZmFsc2UsIm9yaWdpbiI6Imh0dHBzOi8vYXBwLmRmbnMud3RmIiwidHlwZSI6ImtleS5jcmVhdGUifQ"
  },
  "encryptedPrivateKey":"LsXVskHYqqrKKxBC9KvqStLEmxak5Y7NaboDDlRSIW7evUJpQTT1AYvx0EsFskmriaVb3AjTCGEv7gqUKokml1USL7+dVmrUVhV+cNWtS5AorvRuZr1FMGVKFkW1pKJhFNH2e2O661UhpyXsRXzcmksA7ZN/V37ZK7ITue0gs6I="
}
```

### Recovery Credential

<table><thead><tr><th></th><th width="106.33333333333331"></th><th></th></tr></thead><tbody><tr><td><code>credentialKind</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>will always be <code>RecoveryKey</code></td></tr><tr><td><code>credentialInfo</code> <mark style="color:red;">*</mark></td><td><code>Object</code></td><td>See fields below</td></tr><tr><td><code>credentialInfo.credId</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded id of the credential</td></tr><tr><td><code>credentialInfo.clientData</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded <a href="../../../../advanced-topics/authentication/api-objects.md#key-credential">Client Data</a> JSON string object that was signed with the user's private key</td></tr><tr><td><code>credentialInfo.attestationData</code> <mark style="color:red;">*</mark></td><td><code>String</code></td><td>base64url encoded <a href="../../../../advanced-topics/authentication/api-objects.md#key-credential-1">Attestation Data</a> JSON string object with the users signature and public key</td></tr><tr><td><code>encryptedPrivateKey</code></td><td><code>String</code></td><td>Encrypted private key. The user should hold the secret to decrypting this value, and that secret should never be transmitted to Dfns</td></tr></tbody></table>

#### Example

```json
{
  "challengeIdentifier":"eyJ0e...fQNA",
  "credentialName": "My Recovery Credential",
  "credentialKind":"RecoveryKey",
  "credentialInfo":{
    "credId":"GMkW0zlmcoMxI1OX0Z96LL_Mz7dgeu6vOH5_TOeGyNk",
    "clientData":"eyJ0eXBlIjoia2V5LmNyZWF0ZSIsImNoYWxsZW5nZSI6Ik1XTTBNbVk1WVRRME1EUmlOemRoTlRGaE56WTVPRFF3TldJNVpUUTRZMlJoT0RaaU5EazNaVFl6T1RFNU9HWXlNRGN4WmpCall6azRNbVE1WXpZMU1BIiwib3JpZ2luIjoiaHR0cHM6Ly9hcHAuZGZucy5uaW5qYSIsImNyb3NzT3JpZ2luIjpmYWxzZX0",
    "attestationData":"eyJjaGFsbGVuZ2UiOiJZMmd0Tm1Oc2RHTXRiV05sWTNZdE9XRTRPV2QxYnpKd1lqYzBOVEp4Y2ciLCJjcm9zc09yaWdpbiI6ZmFsc2UsIm9yaWdpbiI6Imh0dHBzOi8vYXBwLmRmbnMud3RmIiwidHlwZSI6ImtleS5jcmVhdGUifQ"
  },
  "encryptedPrivateKey":"LsXVskHYqqrKKxBC9KvqStLEmxak5Y7NaboDDlRSIW7evUJpQTT1AYvx0EsFskmriaVb3AjTCGEv7gqUKokml1USL7+dVmrUVhV+cNWtS5AorvRuZr1FMGVKFkW1pKJhFNH2e2O661UhpyXsRXzcmksA7ZN/V37ZK7ITue0gs6I="
}
```

## Responses

{% hint style="info" %}
* See [Common Errors](../../../../getting-started/errors.md#common-errors) for common errors.
* See [Credential Management Errors](../../../../getting-started/errors.md#credential-management-errors) for credential management specific errors.
{% endhint %}

{% tabs %}
{% tab title="200" %}
**Success** - an object describing the new credential

```json
{
  "credentialId": "c1QEdgnPLJargwzy3cbYKny4Q18u0hr97unXsF3DiE8",
  "credentialUuid": "cr-34514-nip9c-8bppvgqgj28dbodrc",
  "dateCreated": "2023-01-11T19:05:06.773Z",
  "isActive": true,
  "kind": "Fido2",
  "name": "My Yubikey",
  "publicKey": "SHA256:E2a3ZQEb4...rPqc",
  "relyingPartyId": "dfns.ninja",
  "origin": "https://app.dfns.ninja"
}
```
{% endtab %}
{% endtabs %}
