# List Permissions

`GET /permissions`

Retrieves a list of permissions (success) or gives a reason why it's not possible (failure).

{% hint style="info" %}
* Request headers required. See [Request Headers](../../../getting-started/request-headers.md) for more information.
* Authentication required. See [Authentication Headers](../../../getting-started/request-headers.md#authentication-headers) for more information.
{% endhint %}

## Required Permissions

| Name               | Conditions      |
| ------------------ | --------------- |
| `Permissions:Read` | Always Required |

## Response <a href="#response" id="response"></a>

### Response example <a href="#response-example" id="response-example"></a>

If successful, the response contains a list of permission objects:

<pre class="language-json"><code class="lang-json"><strong>{    
</strong><strong>  items: [
</strong>    {
      "id": "pm-orange-apple-2b17a80613",
      "name": "US",
      "operations": ["Wallets:Read"],
      "status": "Active",
      "isImmutable": false,
      "dateCreated": "2022-10-26T08:30:25.348Z",
      "dateUpdated": "2022-10-26T08:30:25.348Z",
      "isArchived": false
    }
  ]
}
</code></pre>
