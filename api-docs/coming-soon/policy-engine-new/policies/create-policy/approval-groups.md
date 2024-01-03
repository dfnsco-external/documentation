# Approval Groups

Approval groups are a list of objects that specify the approvers whose decision is required for an activity to be approved.

### Approvers Object

<table><thead><tr><th width="199">Request body fields</th><th width="185">Required/Optional</th><th width="218">Description</th><th>Type</th></tr></thead><tbody><tr><td><code>userId</code></td><td>Required</td><td>The IDs of users who are allowed to make a decision.</td><td>Object</td></tr></tbody></table>

### Example Approval group <a href="#request-example.1" id="request-example.1"></a>

```json
{
  "name": "Admins",
  "quorum": 2,
  "approvers": {
    "userId": {
      "oneOf": ["us-...", "us-..."],
    },
  },
}
```