---
hidden: true
---

# Sync Schedule

Audiences are created and managed on the warehouse. To maintain data freshness, Sortment allows setting up a sync for the audience at regular intervals. You can also trigger a run manually from the audience page.

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

### Setting Audience Refresh Schedule

From audience manager, click on action menu for any audience and click **Change Run Schedule**.

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

3. The scheduler has different run options:
   * Generate once now: This will create the audience now and store the static list of the users in the warehouse for your use
   * Generate every: It takes a start time input, frequency of run, and end time input to maintain data freshness in your warehouse.
4. Once saved, the audience run will get scheduled and executed into the warehouse. It could take some time depending on how big the query is and how much computing power is available to Sortment.
5. Once run is successfully completed, the status will be updated in the audience manager for you to use the audience in campaigns.

#### Frequency of run

1.  Hourly\
    Choose after how many hours you want the audience to be synced back into the warehouse<br>

    <figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>
2.  Daily\
    Choose after how many days you want the audience to be synced back into the warehouse<br>

    <figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>
3.  Weekly\
    Choose after how many weeks you want the audience to be synced back into the warehouse. You can choose specific days of the week to schedule multiple runs during a week.<br>

    <figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>
4. Monthly\
   There are two options to set a sync frequency for monthly runs. You can choose specific dates (for example, 10th of every month) or choose day of the month (for example, second Wednesday). Both examples are added below

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

### Does refreshing an audience also update its Customer.io segment?

An audience refresh and a scheduled Customer.io audience sync are separate runs. The refresh updates the audience in your warehouse. The destination sync runs the audience query against the warehouse and uses the mapped identifier to update the Customer.io segment. A successful warehouse refresh alone does not mean the segment was updated.

If the segment looks stale, check when the source tables finished loading, then check the destination sync’s most recent run. Schedule the destination run after the source data is available. If the run failed, inspect the reported error and check that the identifier field is mapped. Rows without an identifier cannot contribute members to the segment.

Use **Sync now** only when you intend to update Customer.io. On an existing segment, a successful sync can add newly matching members **and remove members who no longer match**. A synced row count is not necessarily the segment’s member count: several rows can refer to the same identifier.
