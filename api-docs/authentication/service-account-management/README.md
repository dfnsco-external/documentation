# Service Accounts

Service Accounts can be viewed as "Machine Users" in your Dfns organisation. They are often used authenticate and interact with Dfns API from your server, on behalf of your organisation.

As any user, they must sign their API requests (cf [User Action Signing](../user-action-signing/)). So before creating a Service Account, an asymmetric keypair must be created. It will be used as the Service Account [Credential](../credential-management/credentials-overview.md). The public part of this key is passed during the Service Account creation.

Service Accounts also have configurable TTL of anywhere from 1 to 730 days.

When created, the Service Account will return the Service Account access token. Make sure to keep both the signing secret (private part of the asymmetric keypair) and the Service Account access token secure.

{% hint style="info" %}
Dfns recommends using services like AWS Secrets Manager or comparable services on other public cloud platforms
{% endhint %}

As any user, Service Accounts can also be assigned [permissions](../../permissions/permissions/) in order to use API endpoints.
