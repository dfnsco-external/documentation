# Recovery

## User Recovery

Both `Employee` and `Enduser` user types can recover their Dfns accounts by creating recovery credentials.  `Employees` connected with your organization are provided a recovery kit when they register on the Dfns dashboard.  They should store this recovery kit securely offline.  If they need to recover their account credentials, they can do so at `app.dfns.<TLD>/recover` by providing the information from the kit and a verification code proving they still have access to their email.  Alternatively, an existing organization user with administrative permissions can deactivate and re-create their account using a different email address.&#x20;

It is possible to create as many recovery credentials as you want. Any recovery credential can be used to recover a User.&#x20;

Once a recovery credential is used, all the user credentials (recovery and regular) are invalidated for security reasons. The user needs to create at least one new recovery credential, this can be done during the recovery process (see [Recover User](createUserRecovery.md)).

`Enduser` recovery can be built by Dfns clients as described below.&#x20;

## End User Recovery

You can decide where in your UX flow to create recovery credentials for your end users.  Depending on the value of assets and the users' ability to create credentials from multiple devices (which is recommended), you may want to make this mandatory upfront or optional later in your flow.  In order to initiate recovery, however, an end-user must have credentials already registered with the system.

Here are the general steps required to perform an end-user recovery:&#x20;

* Verify the identity of your user via your existing authentication methods, KYC, etc.&#x20;
* Call the [delegated recovery](delegatedrecovery.md) endpoint from a service account (`DfnsApiClient.auth.createDelegatedUserRecovery` in the SDK).&#x20;
* Dfns generates a new registration context, so that you can create the user’s new credentials.  Forward this to your user.&#x20;
* The user creates a new credential (and optional new recovery credential)&#x20;
* The user signs the new credential(s) with their existing recovery credential, and sends the new credential(s) + signature to Dfns to [Recover User](createUserRecovery.md) endpoint (`DfnsDelegatedApiClient.auth.createUserRecovery` in the SDK)
* Dfns verifies the signature is valid&#x20;
* Dfns archives all of the user’s current credentials (regular + recovery)
* Dfns adds the user’s new credentials
