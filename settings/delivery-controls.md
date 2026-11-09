# Delivery Controls

Delivery Controls in Sortment allow businesses to regulate when and how often users receive messages, ensuring that users are not overwhelmed by communications or disturbed at inappropriate times. There are four critical functionalities to enhance user experience and manage message flow, which are covered here.

### **Quiet Hours** <a href="#quiet-hours" id="quiet-hours"></a>

Use Quiet Hours to set the hours when your workspace should avoid sending campaign messages. Enter the start and end times in the [workspace timezone](workspace-timezone.md).

<figure><img src="../.gitbook/assets/image (19).png" alt="Quiet Hours from 8:00 PM to 8:00 AM in Asia/Kolkata"><figcaption><p>The timezone appears beside the start and end times.</p></figcaption></figure>

**Will a blocked message go out after Quiet Hours end?**

* **Scheduled campaign:** If you see **Campaign cannot be scheduled within quiet hours**, choose a time outside the window and schedule the campaign again. Dismissing the error does not move the start time.
* **Single-send campaign:** Sortment checks Quiet Hours when it receives the send request. If Quiet Hours block the request, Sortment skips that send. It does not queue it for the morning. For example, with Quiet Hours set to 21:00–07:00, a request blocked at 23:00 will not go out at 07:00.

**Setup Steps**

1. In Workspace Settings, go to the **Delivery Controls.**
2. In the **Quiet Hours** section, click on the **Edit** button.
3. Set the start and end time for quiet hours by selecting the desired time range (e.g., from 21:00 to 05:30).
4. Save the configuration.

If the workspace timezone changes, or its daylight saving offset changes, reopen **Delivery Controls** and check the displayed start and end times. If they no longer match the local hours you want, adjust them and save.

***

### **Frequency Capping** <a href="#frequency-capping" id="frequency-capping"></a>

Frequency Capping allows limiting the number of messages that are sent to each user within a specified timeframe in a workspace. This ensures that users are not bombarded with too many communications in a short span, improving engagement rates and preventing unsubscribes due to communication overload. The limit can be set at omnichannel communications or channel specific.

**How it Works**

* The system tracks how many messages of the defined type have been sent to a user within the specified time period.
* Once the cap is reached (e.g., 3 emails in 7 days), no further messages of that type will be sent to the user until the time period resets.

<figure><img src="../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

**Setup Steps**

1. In Workspace Settings, go to the **Delivery Controls.**
2. In the **Frequency Capping** section, click on the **Edit** button.
3. Specify the maximum number of messages (e.g., "Send no more than 3 emails").
4. Select the type of messages to cap (e.g., emails, push notifications, SMS, any type).
5. Define the time period (e.g., every 7 days).
6. Save the configuration.

{% hint style="info" %}
**Note:** When selecting a timeframe such as _1 day, 1 week, or 1 month_, these follow the **calendar day, calendar week, and calendar month.**
{% endhint %}

###

### Channel-Specific Rules

* **SMS, MMS, and WhatsApp**: Frequency capping is applied at the **phone number** level. If multiple users share the same phone number, they are treated as the same recipient under frequency capping.
* **Email**: Frequency capping is applied at the **email address** level. If multiple users share the same email, they are treated as the same recipient under frequency capping.
* **Any Delivery**: Frequency capping is applied at the **user ID** level. Even if two users share the same phone number or email, they are treated as separate entities under this setting.

***

### Global Holdout Group

The **Global Holdout Group** allows you to assign a percentage of your users to be excluded from all messaging. This is useful for understanding absolute impact of your experiments and marketing communications – the global holdout group will not receive any communications but some percentage of these users would still convert or do relevant actions.&#x20;

You can compare campaign statistics against these users to learn about absolute impact your marketing strategies have on business metrics like conversion or revenue.

**How it Works:**

* A percentage of your total users (e.g., 5%) will be randomly selected and added to the global holdout group.
* These users will not receive any messages, allowing you to compare against the treatment group.

<figure><img src="../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
How are audience fluctuations handled?

In case the user base increases, the holdout group will readjust accordingly among the new users and sample the required percentage to ensure the global holdout group size is same ratio of all users.

In case user base decreases, the holdout group will not be updated.
{% endhint %}

**Setup Steps:**

1. In **Workspace Settings** → **Delivery Controls**, find the **Global Holdout Group** section.
2. Click **Set up** or **Edit**.
3. Enter the desired percentage of users to assign.
4. Save the configuration.

Once saved, this will also show you the size of the global holdout group with total number of users.

***

### Suppression Rules

**Suppression Rules** prevent communications from being sent to specific user segments or audiences. These audiences will always be excluded before sending out a campaign. For example, an audience of internal users could be set as a suppression rule to prevent your team from receiving customer communications.

**How it Works:**

* You define rules to exclude users from messaging based on audience criteria (e.g., “Active Platform Users”).
* Rules apply across all types of messages or can be customised by message type (Email, SMS, or Whatsapp).

<figure><img src="../.gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>

**Setup Steps:**

1. In the **Suppression Rules** section under **Delivery Controls**, click **Add Rule**.
2. Choose the message type to suppress (e.g., any type, email, SMS).
3. Select the user segment to suppress (e.g., “Beta Testers”).
4. Save the rule.

***

### Best Practices <a href="#best-practices" id="best-practices"></a>

* **Quiet Hours:** Always align quiet hours with your audience’s behavior and preferences to avoid disturbing them during sensitive times (e.g., early mornings or late nights).
* **Frequency Capping:** Use frequency capping to manage communications effectively. For example, segment users by engagement levels to send fewer messages to less engaged users and more to active ones.
* **Global Holdout Group**: Use for campaign impact analysis.
* **Suppression Rules**: Apply exclusions to protect high-risk or sensitive user groups from over-messaging.

[<br>](https://app.gitbook.com/o/CdalBLUjYzosXMRl8crP/s/mbOMKRXus6KHDbeQALkO/~/changes/111/~/revisions/ZBj2tB0WMqe00TC24AVe/engage/campaigns/subscription-groups)
