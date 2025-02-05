# Dfns Environments

Dfns supports a production environment (api.dfns.io) and a staging environment (api.dfns.ninja). Production interacts with both mainnet and testnet chains, while staging only interacts with testnet chains. _Staging is for use only with test funds_ - the environment does not have the same availability guarantees as production.

{% hint style="danger" %}
_Staging environment (api.dfns.ninja) is for use only with test funds_
{% endhint %}

This constraint exists to prevent customers from accidentally moving real funds into the staging environment. Note Dfns can not prevent customers from using [CreateSignature](broken-reference) in a staging environment and sending funds to a mainnet chain. This is however against our terms of use, so please don't do it - thanks!

Throughout our documentation, assume that you should prepend the environment you want to interact with in front of the API paths given. For example:

`POST /assets/asset-accounts/{AssetAccountId}/payments`

For the production environment should be interpreted as a post to:

`https://api.dfns.io/assets/asset-accounts/{AssetAccountId}/payments`

If you have any questions or concerns about our environments, don't hesitate to reach out to us: [docs@dfns.co](https://email:docs@dfns.co).

### IPs

You can use the following values to whitelist Dfns IPs for webhook delivery:

* Staging: `52.47.197.63`
* Production: `35.181.116.68`

