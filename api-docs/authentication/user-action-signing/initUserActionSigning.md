# Create User Action Signature Challenge

`POST /auth/action/init`

Starts a user action signing session, returning a challenge that will be used to verify the user's intent to perform an action.

{% hint style="info" %}
* Request headers required. See [Request Headers](../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

### Required Permissions

{% hint style="info" %}
The permissions apply to the application only.
{% endhint %}

| Name               | Conditions      |
| ------------------ | --------------- |
| `Auth:Action:Sign` | Always Required |

## Request body

|                                                           |          |                                                                                                                                                                                                     |
| --------------------------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `userActionPayload` <mark style="color:red;">\*</mark>    | `String` | The JSON encoded body of the request that is being signed                                                                                                                                           |
| `userActionHttpMethod` <mark style="color:red;">\*</mark> | `String` | <p>The HTTP method that will be used to make the request that is being signed. Can be one of the following:<br><code>POST</code><br><code>PUT</code><br><code>DELETE</code><br><code>GET</code></p> |
| `userActionHttpPath` <mark style="color:red;">\*</mark>   | `String` | The path of the request that is being signed                                                                                                                                                        |
| `userActionServerKind`                                    | `String` | `Optional` indicator of which Dfns service being called. Currently, this can only be `Api`                                                                                                          |

### Example:

```json
{
  "userActionPayload": "{\"name\": \"My PAT\",\"publicKey\": \"-----BEGIN PUBLIC KEY-----\\nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEZQt0YI2hdsFNmKJesSkAHldyPLIV\\nFLI/AhQ5eGasA7jU8tEXOb6nGvxRaTIXrgZ2NPdk78O8zMqz5u9AekH8jA==\\n-----END PUBLIC KEY-----\",\"daysValid\": 365,\"permissionId\": \"pm-delaw-avoca-v16r37fpp8koqebc\"}",
  "userActionHttpMethod": "POST",
  "userActionHttpPath": "/auth/pats"
}
```

## Responses

{% hint style="info" %}
* See [Common Errors](../../../getting-started/errors.md#common-errors) for common errors.
* See [User Action Signing Errors](../../../getting-started/errors.md#user-action-signing-errors) for user action signing specific errors.
{% endhint %}

{% tabs %}
{% tab title="200" %}
**Success** - an object containing the user's authentication options

Format:

```json
{
  // identifies the kind of credentials that can be used to sign the user action
  "supportedCredentialKinds": [
    {
      // the kind of credental; can be `Fido2` or `Key`
      "kind": "string",
      // indicates if the credential can be used as a first factor, second factor, or either; can be `first`, `second`, or `either`
      "factor": "string",
      // when true indicates a second factor credential is required if the credential is used as a first factor
      "requiresSecondFactor": "boolean"
    }
  ],
  // random value used to uniquely identify the request. This value will be included in the data that is signed and sent to the matching /signing call
  "challenge": "string",
  // temporary authentication token that is used to identify this signing session with the matching call to CreateUserActionSignature
  "challengeIdentifier": "string",
  // optional url containing a secret value that can be used to enable cross device/origin signing
  "externalAuthenticationUrl": "string",
  // list of credentials that the user can use to sign the user action
  "allowCredentials": {
    // list of keys that the user can use to sign the user action
    "key":[
      {
        // is always `public-key`
        "type": "string",
        // ID that identifies the credential
        "id": "string",
      }
    ],
    // list of password protected keys that the user can use to sign the login challenge.
    "passwordProtectedKey": [
      {
        // is always `public-key`
        "type": "string",
        // ID that identifies the credential
        "id": "string",
        // Encrypted Private Key. Only the user knows the password to decrypt it and have access to the private key
        "encryptedPrivateKey": "string"
      }
    ],
    // list of WebAuthn credentials that the user can use to sign the user action
    "webauthn": [
      {
        // is always `public-key`
        "type": "string",
        // ID that identifies the credential
        "id": "string",
        // optional list of transports that are supported by the credential (used only for WebAuthn)
        "transports": "string"
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}

### Examples

```json
{
  "supportedCredentialKinds": [
    {
      "kind": "Fido2",
      "factor": "first",
      "requiresSecondFactor": true
    }
  ],
  "challenge": "MWM0MmY5YTQ0MDRiNzdhNTFhNzY5ODQwNWI5ZTQ4Y2RhODZiNDk3ZTYzOTE5OGYyMDcxZjBjYzk4MmQ5YzY1MA",
  "challengeIdentifier": "eyJ0e...fQNA",
  "allowCredentials": {
    "key":[],
    "passwordProtectedKey": [
      {
        "type": "public-key",
        "id": "hIjkx5PqVxz8wbtuvOh2UYHEY1QXS8mMfKeEDGt-0Fo=",
        "encryptedPrivateKey": "LsXVskHYqqrKKxBC9KvqStLEmxak5Y7NaboDDlRSIW7evUJpQTT1AYvx0EsFskmriaVb3AjTCGEv7gqUKokml1USL7+dVmrUVhV+cNWtS5AorvRuZr1FMGVKFkW1pKJhFNH2e2O661UhpyXsRXzcmksA7ZN/V37ZK7ITue0gs6I="
      }
    ],
    "webauthn": [
      {
        "type": "public-key",
        "id": "c1QEdgnPLJargwzy3cbYKny4Q18u0hr97unXsF3DiE8"
      }
    ]
  }
}
```
