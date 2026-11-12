# SendGrid

### Overview&#x20;

Sortment lets you send emails via your existing SendGrid account.&#x20;

{% hint style="info" %}
It is required to set up / have a SendGrid account before configuring the same on Sortment.&#x20;
{% endhint %}

### SendGrid Set-up&#x20;

1. Navigate to Channels
2. Under "Email" section, click on the "SendGrid" button.
3. In the screen that appears, fill in:
   1. **Custom name:** Provide a name that would help you identify the configured account in Sortment
   2. **API Key:** On the SendGrid app, find and click on the Settings tab on the left-hand navigation menu. In the sub-menu, click on "API Keys". Create a new API Key and copy the same. You may also use a previously generated API Key, in case you have it.
   3. **From Email:** In your SendGrid account, from the Settings tab on the left navigation menu, find "Sender Authentication" and copy the details of the Sender that you intend on using. Alternatively, you can add a domain or a single user on SendGrid from the same page.&#x20;
   4. **From Name:** Enter the name associated with the Sender that was entered in the "From Email" field.
   5. **Unsubscribe Group ID**: Enter the group ID associated with your SendGrid account

### What does publishing check for a native SendGrid campaign?

If native sending is enabled for your workspace or campaign, publish the email campaign in Sortment with your SendGrid integration selected. Publishing prepares the campaign without requiring you to create a separate notification event. Sortment checks native delivery eligibility when the campaign runs.

Use an email campaign with a warehouse audience, a SendGrid integration, and a subject and body that the native sender can render. If you configure throttling, keep the campaign rate within the rate limit saved on the integration.

#### What happens if the campaign becomes ineligible after publishing?

* **Native sending was enabled on this campaign.** The run stops if it can no longer send natively. For example, switching the campaign to a CSV audience or adding an A/B experiment can make it ineligible.
* **Native sending comes only from the workspace default.** A campaign that fails an eligibility rule can use standard delivery for that run. If Sortment cannot complete the eligibility check or set up the native delivery events, the run stops.

For a stopped run, read the reason in the error. Check the audience source, experiment settings, selected integration and template. If the error mentions throttling, compare the campaign’s requested rate with the integration limit. Check the campaign report before retrying.

### Message Delivery Status

To enable Sortment to provide and track status against your email campaigns via SendGrid, you will have to manually update the SendGrid account with the Sortment callback endpoint. To update the callback, please follow the steps below:&#x20;

1. Log in to your SendGrid account.
2. From the left navigation panel, find **Settings** and navigate to **Mail Settings**.
3. On the page that loads, find **Event Webhooks** and click on the edit icon.
4. On the pop-up that loads, select an **Authentication Method** and enter Sortment's POST URL (This will be shared on request).
5. Select the data you want to be sent by checking on the relevant boxes and save the configuration

### Test your connection

Once the steps detailed above have been completed, you can test your connection to see whether the email is being sent successfully.&#x20;

<figure><img src="../../../.gitbook/assets/Screenshot 2024-01-11 at 7.47.54 PM.png" alt=""><figcaption></figcaption></figure>

### Adding Un-subscription groups to all emails sent

Given the latest changes by email providers including Google and Yahoo, adding un-subscription option in each email being sent is a must.&#x20;

Sortment supports you to add the un-subscription group IDs created on SendGrid while sending out emails and any templates.&#x20;

{% code overflow="wrap" %}
```
Steps to activate unsub IDs 
1. Ensure you have created unsub ID / unsub group ID in your SendGrid account 
2. Go to Marketing → Unsubscribe groups to see the list of your unsub groups / IDs 
Share the ID that you want to use across all your templates and share with Sortment 
```
{% endcode %}

> For now Sortment only supports 1 unsub ID against all email templates. We will automatically attach the group ID value to your template variable.
