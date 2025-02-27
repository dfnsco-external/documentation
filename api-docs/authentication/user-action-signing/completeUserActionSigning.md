# Create User Action Signature

`POST /auth/action`

Completes the user action signing process and provides a signing token that can be used to verify the user intended to perform the action.

The type of credentials used to sign the action is determined by the `kind` field in the nested objects (`firstFactor` and `secondFactor`). Supported credential kinds are:

* `Fido2`: User action is signed by a user's signing device using `WebAuthn`.
* `Key`: User action is signed by a user's, or token's, private key.
* `PasswordProtectedKey`: Login challenge is signed by the decrypted user's private key that was sent during [Create User Action Signature Challenge](initUserActionSigning.md) step.

{% hint style="info" %}
* Request headers required. See [Request Headers](../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

{% hint style="info" %}
The permissions apply to the application only.
{% endhint %}

| Name               | Conditions      |
| ------------------ | --------------- |
| `Auth:Action:Sign` | Always Required |

## Request body

|                                                          |          |                                                                                                                   |
| -------------------------------------------------------- | -------- | ----------------------------------------------------------------------------------------------------------------- |
| `challengeIdentifier` <mark style="color:red;">\*</mark> | `String` | temporary authentication token returned by the [Create User Action Signature Challenge](initUserActionSigning.md) |
| `firstFactor` <mark style="color:red;">\*</mark>         | `Object` | first factor credential used to sign the user action                                                              |
| `secondFactor`                                           | `Object` | `Optional` second factor credential used to authenticate a user                                                   |

### Fido2 Credential

|                                                                            |          |                                                                                    |
| -------------------------------------------------------------------------- | -------- | ---------------------------------------------------------------------------------- |
| `kind` <mark style="color:red;">\*</mark>                                  | `String` | will always be `Fido2`                                                             |
| `credentialAssertion` <mark style="color:red;">\*</mark>                   | `Object` |                                                                                    |
| `credentialAssertion.credId` <mark style="color:red;">\*</mark>            | `String` | base64url encoded id of the credential returned by the user's WebAuthn client      |
| `credentialAssertion.clientData` <mark style="color:red;">\*</mark>        | `String` | base64url encoded client data object returned by the user's WebAuthn client        |
| `credentialAssertion.authenticatorData` <mark style="color:red;">\*</mark> | `String` | base64url encoded authenticator data object returned by the user's WebAuthn client |
| `credentialAssertion.signature` <mark style="color:red;">\*</mark>         | `String` | base64url encoded signature returned by the user's WebAuthn client                 |
| `credentialAssertion.userHandle` <mark style="color:red;">\*</mark>        | `String` | base64url encoded userHandle returned by the user's WebAuthn client                |

#### Example:

```json
{
  "challengeIdentifier":"eyJ0e...fQNA",
  "firstFactor":{
    "kind":"Fido2",
    "credentialAssertion":{
      "credId":"c1QEdgnPLJargwzy3cbYKny4Q18u0hr97unXsF3DiE8",
      "clientData":"eyJ0eXBlIjoid2ViYXV0aG4uZ2V0IiwiY2hhbGxlbmdlIjoiTVdNME1tWTVZVFEwTURSaU56ZGhOVEZoTnpZNU9EUXdOV0k1WlRRNFkyUmhPRFppTkRrM1pUWXpPVEU1T0dZeU1EY3haakJqWXprNE1tUTVZelkxTUEiLCJvcmlnaW4iOiJodHRwczovL2FwcC5kZm5zLm5pbmphIiwiY3Jvc3NPcmlnaW4iOmZhbHNlfQ",
      "authenticatorData":"WT-zFZUBbJHfBkmhzTlPf49LTn7asLeTQKhm_riCvFgFAAAAAA",
      "signature":"MEUCIQDJ8G9J1NTjdoKx0yloYw45bpn6fJhcqCoUGiZuOU1IAQIgAtPt7S8FHFYW9OMHh3S5FVAxk-lhli-2lX22bBNSDog",
      "userHandle":"dXMtMmJhMGgtbHZwMnEtOHYxODYwcGNqMWJoNWlyaQ"
    }
  }
}
```

### Key Credential and Password Protected Key Credential

|                                                                     |          |                                                                                                                                     |
| ------------------------------------------------------------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `kind` <mark style="color:red;">\*</mark>                           | `String` | `Key` or `PasswordProtectedKey`                                                                                                     |
| `credentialAssertion` <mark style="color:red;">\*</mark>            | `Object` |                                                                                                                                     |
| `credentialAssertion.credId` <mark style="color:red;">\*</mark>     | `String` | base64url encoded id of the credential                                                                                              |
| `credentialAssertion.clientData` <mark style="color:red;">\*</mark> | `String` | [Client Data](../../../advanced-topics/authentication/api-objects.md#key-credential) JSON object, stringified and base64url-encoded |
| `credentialAssertion.signature` <mark style="color:red;">\*</mark>  | `String` | base64url encoded signature generated by signing the clientData JSON string object                                                  |

#### Example:

```json
{
  "challengeIdentifier":"eyJ0e...fQNA",
  "firstFactor":{
    "kind":"Key", // can be "PasswordProtectedKey" as well
    "credentialAssertion":{
      "credId":"6Ca6tAOFTx2odyJBnCoRO-gPvfpfy0EOoOcEaxfxIOk",
      "clientData":"eyJ0eXBlIjoia2V5LmdldCIsImNoYWxsZW5nZSI6Ik1XTTBNbVk1WVRRME1EUmlOemRoTlRGaE56WTVPRFF3TldJNVpUUTRZMlJoT0RaaU5EazNaVFl6T1RFNU9HWXlNRGN4WmpCall6azRNbVE1WXpZMU1BIiwib3JpZ2luIjoiaHR0cHM6Ly9hcHAuZGZucy5uaW5qYSIsImNyb3NzT3JpZ2luIjpmYWxzZX0",
      "signature":"owt8WtpJT_6eEuw4UwdIX2HMMwENgk0SrI-RoCMPhx_9YMVpNKJGmJfHUusf_R1Mor9a_hinQVuXj4_XRdeJFSY2AySXSUk",
    }
  }
}
```

## Responses

{% hint style="info" %}
* See [Common Errors](../../../getting-started/errors.md#common-errors) for common errors.
* See [User Action Signing Errors](../../../getting-started/errors.md#user-action-signing-errors) for user action signing specific errors.
{% endhint %}

{% tabs %}
{% tab title="200" %}
**Success** - a token that will be passed in the X-DFNS-USERACTION header

```json
{
  "userAction": "eyJ0eX...bzrQakA"
}
```
{% endtab %}
{% endtabs %}
