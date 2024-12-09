# Binance Setup

Dfns support integrations with Binance exchange accounts.  Users must have a Binance account in order to generate API keys to input into the Dfns Dashboard to configure the integration.

## API Key Generation

Log into your Binance account and go to Account=>API Management at [https://www.binance.com/en/my/settings/api-management](https://www.binance.com/en/my/settings/api-management).   Click Create API, select System Generated, name the API, then sign with your passkey if requeste&#x64;**.**   You should see a screen that looks like this:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-10-23 at 11.52.01 AM.png" alt=""><figcaption></figcaption></figure>

Log into your Dfns dashboard and click **Settings=>Exchanges=>New**.  Select "Binance" and copy the API and Secret keys from above into the corresponding fields for the Read credentials. &#x20;

Go back to Binance and generate a second Write API key by following the same steps as outlined above.  Next click "Edit Restrictions" in the top right, then select the radio button at the bottom for "Restrict access to trusted IPs only (Recommended)".  Enter 35.181.116.68 and push return.  Finally check the box for "Enable Withdrawals" and click "Save". &#x20;

<figure><img src="../../../.gitbook/assets/Screenshot 2024-10-23 at 11.56.44 AM.png" alt=""><figcaption></figcaption></figure>

Copy the generated values into the Write API key fields in Dfns. Click **Create** and the integration setup is complete.&#x20;

