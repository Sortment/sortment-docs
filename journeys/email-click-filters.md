# Refine journey email-click filters

Use **Specify Email** and **Specify Link** to narrow an email-click event in a journey. They control different restrictions: the email identifies the message, while the link identifies the clicked URL.

## Choose the restrictions you need

1. Open the journey's email-click event filter in edit mode.
2. To restrict the event to one message, choose **Specify Email** and select the email.
3. To restrict the clicked URL, choose **Specify Link**. You can add this without first selecting an email.
4. Choose the URL comparison and enter or select the value. When no campaign links are available to choose from, enter the URL directly.
5. Review every remaining rule before saving the journey settings.

For example, you can match clicks on a particular offer URL across emails, or combine that URL with one selected email. Combined rules must both match.

## Remove a restriction

Remove the link rule to keep the selected email while allowing its other links to match. Remove the email rule to keep the URL condition across matching emails. Removing either rule preserves the other rule and the remaining event filters.

Removing a restriction broadens what can match. Check the channel, URL comparison, email selection, and any other conditions after the edit.

For general engagement segmentation, see [Campaign Event Filters](../engage/audiences/campaign-event-filters.md). For conversion criteria, see [Journey Settings](journey-settings.md).
