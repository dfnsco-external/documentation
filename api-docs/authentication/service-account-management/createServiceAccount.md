# Create Service Account

`POST /auth/service-accounts`

Create a new service account in the caller's org

{% hint style="info" %}
* User action signature required. See [User Action Signing](../user-action-signing/) for more information.
* Request headers required. See [Request Headers](../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name                        | Conditions      |
| --------------------------- | --------------- |
| `Auth:Apps:Create`          | Always Required |
| `Auth:Types:ServiceAccount` | Always Required |

## Request Body

|                                                |         |                                                                                                                                                                                     |
| ---------------------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name` <mark style="color:red;">\*</mark>      | String  | the name of the service account, must be unique for the caller's org                                                                                                                |
| `publicKey` <mark style="color:red;">\*</mark> | String  | public key that can be used to validate signatures generated by the service account                                                                                                 |
| `daysValid`                                    | Integer | `Optional` number of days the service account will be valid. Max is `730`                                                                                                           |
| `permissionId`                                 | String  | `Optional` ID of the permission that will be assigned to the service account. If no permission ID is given, the service account will be assigned the same permissions as the caller |
| `externalId`                                   | String  | `Optional` user defined value that can be used to correlate the entity with an external system                                                                                      |

Example:

```JSON
{
  "name": "My Service Account name",
  "publicKey": "-----BEGIN PUBLIC KEY-----\nMFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEZQt0YI2hdsFNmKJesSkAHldyPLIV\nFLI/AhQ5eGasA7jU8tEXOb6nGvxRaTIXrgZ2NPdk78O8zMqz5u9AekH8jA==\n-----END PUBLIC KEY-----",
  "daysValid": 365,
  "permissionId": "pm-delaw-avoca-v16r37fpp8koqebc"
}
```

## Responses

{% hint style="info" %}
* See [Common Errors](../../../getting-started/errors.md#common-errors) for common errors.
* See [Service Account Management Errors](../../../getting-started/errors.md#service-account-management-errors) for service account management specific errors.
{% endhint %}

{% tabs %}
{% tab title="200" %}
**Success** - The created service account

```JSON
{
  "userInfo": {
    "username": "My new service account",
    "userId": "us-2q55i-g68v6-9etoie66crbdsh7k",
    "kind": "CustomerEmployee",
    "credentialUuid": "cr-4uc9u-12ij1-9s08327e73jqqcnr",
    "orgId": "or-yanke-mars-6ulofamogg84s87v",
    "permissions": [],
    "scopes": [],
    "isActive": true,
    "isServiceAccount": true,
    "isRegistered": true,
    "permissionAssignments": [
      {
        "permissionId": "pm-paris-lithi-17bf4kf01h8ajph4",
        "permissionName": "DfnsFullAdminAccess",
        "assignmentId": "",
        "operations": [
          "ApiKeys:Create",
          "ApiKeys:Read",
          "ApiKeys:Revoke",
          "AssetAccounts:Archive",
          "AssetAccounts:Create",
          "AssetAccounts:Read",
          "Auth:Action:Sign",
          "Auth:Apps:Create",
          "Auth:Apps:Read",
          "Auth:Apps:Update",
          "Auth:Creds:Create",
          "Auth:Creds:Read",
          "Auth:Creds:Update",
          "Auth:Types:Application",
          "Auth:Types:Employee",
          "Auth:Types:EndUser",
          "Auth:Types:Pat",
          "Auth:Types:ServiceAccount",
          "Auth:Users:Create",
          "Auth:Users:Delegate",
          "Auth:Users:Read",
          "Auth:Users:Update",
          "Balances:Read",
          "CallbackEvents:Read",
          "CallbackSubscriptions:Archive",
          "CallbackSubscriptions:Create",
          "CallbackSubscriptions:Read",
          "Employees:Read",
          "Payments:Create",
          "Payments:Read",
          "PermissionAssignments:Create",
          "PermissionAssignments:Read",
          "PermissionAssignments:Revoke",
          "PermissionPredicates:Archive",
          "PermissionPredicates:Create",
          "PermissionPredicates:Read",
          "PermissionPredicates:Update",
          "Permissions:Archive",
          "Permissions:Create",
          "Permissions:Read",
          "Permissions:Update",
          "Policies:Archive",
          "Policies:Create",
          "Policies:Read",
          "Policies:Update",
          "PolicyControlExecutions:Read",
          "PolicyControlExecutions:Update",
          "PolicyControls:Archive",
          "PolicyControls:Create",
          "PolicyControls:Read",
          "PolicyControls:Update",
          "PolicyRules:Archive",
          "PolicyRules:Create",
          "PolicyRules:Read",
          "PolicyRules:Update",
          "PublicKeyAddresses:Read",
          "PublicKeys:Create",
          "PublicKeys:Read",
          "Signatures:Create",
          "Signatures:Read",
          "Transactions:Create",
          "Transactions:Read",
          "Wallets:Create",
          "Wallets:Read",
          "Wallets:Update"
        ]
      }
    ]
  },
  "accessTokens": [
    {
      "accessToken": "eyJ0eXAiO...shGAkjbXAQsR9YJn3g",
      "dateCreated": "2023-04-12T23:49:33.767Z",
      "credId": "",
      "isActive": true,
      "kind": "CustomerEmployee",
      "linkedUserId": "us-2q55i-g68v6-9etoie66crbdsh7k",
      "linkedAppId": "",
      "name": "My new service account",
      "orgId": "or-yanke-mars-6ulofamogg84s87v",
      "permissionAssignments": [
        {
          "permissionId": "pm-paris-lithi-17bf4kf01h8ajph4",
          "permissionName": "DfnsFullAdminAccess",
          "assignmentId": "",
          "operations": [
            "ApiKeys:Create",
            "ApiKeys:Read",
            "ApiKeys:Revoke",
            "AssetAccounts:Archive",
            "AssetAccounts:Create",
            "AssetAccounts:Read",
            "Auth:Action:Sign",
            "Auth:Apps:Create",
            "Auth:Apps:Read",
            "Auth:Apps:Update",
            "Auth:Creds:Create",
            "Auth:Creds:Read",
            "Auth:Creds:Update",
            "Auth:Types:Application",
            "Auth:Types:Employee",
            "Auth:Types:EndUser",
            "Auth:Types:Pat",
            "Auth:Types:ServiceAccount",
            "Auth:Users:Create",
            "Auth:Users:Delegate",
            "Auth:Users:Read",
            "Auth:Users:Update",
            "Balances:Read",
            "CallbackEvents:Read",
            "CallbackSubscriptions:Archive",
            "CallbackSubscriptions:Create",
            "CallbackSubscriptions:Read",
            "Employees:Read",
            "Payments:Create",
            "Payments:Read",
            "PermissionAssignments:Create",
            "PermissionAssignments:Read",
            "PermissionAssignments:Revoke",
            "PermissionPredicates:Archive",
            "PermissionPredicates:Create",
            "PermissionPredicates:Read",
            "PermissionPredicates:Update",
            "Permissions:Archive",
            "Permissions:Create",
            "Permissions:Read",
            "Permissions:Update",
            "Policies:Archive",
            "Policies:Create",
            "Policies:Read",
            "Policies:Update",
            "PolicyControlExecutions:Read",
            "PolicyControlExecutions:Update",
            "PolicyControls:Archive",
            "PolicyControls:Create",
            "PolicyControls:Read",
            "PolicyControls:Update",
            "PolicyRules:Archive",
            "PolicyRules:Create",
            "PolicyRules:Read",
            "PolicyRules:Update",
            "PublicKeyAddresses:Read",
            "PublicKeys:Create",
            "PublicKeys:Read",
            "Signatures:Create",
            "Signatures:Read",
            "Transactions:Create",
            "Transactions:Read",
            "Wallets:Create",
            "Wallets:Read",
            "Wallets:Update"
          ]
        }
      ],
      "publicKey": "",
      "tokenId": "to-79hoo-ohdi6-9rnomnmhiavfb335"
    }
  ]
}
```
{% endtab %}
{% endtabs %}

## Examples

{% embed url="https://github.com/dfnsext/dfns-api-docs/blob/canary/examples/typescript/src/api/authentication/service-account-management/create-service-account.ts" %} Typescript Example {% endembed %}