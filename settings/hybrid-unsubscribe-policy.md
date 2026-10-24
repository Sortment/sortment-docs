---
description: >-
  How Sortment decides who is suppressed when a contact unsubscribes, and why
  the Hybrid policy suppresses the whole user rather than a single address.
---

# Hybrid unsubscribe policy

{% hint style="info" %}
**In one sentence:** under the Hybrid policy, an unsubscribe on **any one** email or phone number silences the **whole user** that address belongs to, plus any other user that shares that exact address. Opt‑ins are never shared.
{% endhint %}

## Why this exists

Many businesses store more than one way to reach the same user. A company record might carry a primary email, a billing email and an owner's personal email. A household might carry two phone numbers.

That raises a question every time someone unsubscribes:

> They unsubscribed using **one** address. Should we stop messaging the **user**, or just stop using **that address**?

Sortment lets each workspace answer this with an **unsubscribe identifier** setting. Hybrid is the setting for teams who want the user treated as one person, while still catching shared mailboxes.

## The three policies at a glance

```mermaid
flowchart LR
    E["Unsubscribe event<br/>user = Acme Corp<br/>address = billing@acme.com"]
    E --> C{{"Unsubscribe<br/>identifier"}}
    C -->|"User ID"| U["Suppress <b>Acme Corp</b><br/>(all addresses)"]
    C -->|"Channel ID"| A["Suppress <b>billing@acme.com</b><br/>wherever it appears"]
    C -->|"Hybrid"| H["Suppress <b>Acme Corp</b><br/>+ every user that also has<br/><b>billing@acme.com</b>"]
    style H fill:#eef4ff,stroke:#3b6ef5,stroke-width:2px
```

| Policy | What the event is keyed on | Who gets suppressed | Best for |
| --- | --- | --- | --- |
| **User ID** | The user on the event | That user only, on every address | One address per user, or when consent is strictly personal |
| **Channel ID** | The address on the event | That exact address, on every user that has it | Address‑level consent, shared mailboxes, no reliable user ID |
| **Hybrid** | User **and** address | That user on every address, **and** every other user that has the same address | Multiple addresses per user **and** addresses that may be shared |

{% hint style="warning" %}
The policy is set **per workspace** and applies to every channel and every subscription group in it. Changing it after subscription groups exist requires regenerating the subscription materialisations. Ask your Sortment contact before switching.
{% endhint %}

## How Hybrid decides who to suppress

Hybrid runs a **one‑hop** expansion. Starting from the unsubscribe event, it takes exactly two steps and then stops.

```mermaid
flowchart TD
    S(["Opt‑out event arrives<br/>user ID + address"])
    S --> A["<b>Step 1</b><br/>Add the user on the event"]
    A --> B["<b>Step 2</b><br/>Find every user whose contact fields<br/>(email, email2, email3 …) contain that address.<br/>Add them too."]
    B --> X["<b>Stop.</b><br/>Do <u>not</u> follow those users' other addresses."]
    X --> R(["Result: a set of suppressed <b>user IDs</b>"])
    style X fill:#fff4e5,stroke:#f59e0b,stroke-width:2px
    style R fill:#eafaf0,stroke:#16a34a,stroke-width:2px
```

Three things follow from this:

1. **Suppression is by user, not by address.** Once a user is in the set, none of their addresses receive the message, including addresses that were never unsubscribed.
2. **Shared addresses pull in other users.** If two records list the same email, an unsubscribe on that email suppresses both.
3. **There is no chain reaction.** Users pulled in through a shared address do not drag in further users through *their* other addresses.

## Worked example

Four company records, each with up to three email fields:

```mermaid
flowchart LR
    subgraph users["Primary user table"]
        direction TB
        U1["<b>Acme Corp</b><br/>email: ceo@acme.com<br/>email2: shared@books.com<br/>email3: ops@acme.com"]
        U2["<b>Bolt LLC</b><br/>email: shared@books.com<br/>email2: hr@bolt.com<br/>email3: pay@bolt.com"]
        U3["<b>Cedar Inc</b><br/>email: hello@cedar.com"]
        U4["<b>Delta Co</b><br/>email: hr@bolt.com<br/>email2: ceo@delta.com"]
    end
    EV(["Opt‑out event<br/>Acme Corp · shared@books.com"])
    EV -->|"Step 1: user on the event"| U1
    EV -->|"Step 2: also has shared@books.com"| U2
    U2 -.->|"hr@bolt.com is NOT followed"| U4
    style U1 fill:#fee2e2,stroke:#dc2626,stroke-width:2px
    style U2 fill:#fee2e2,stroke:#dc2626,stroke-width:2px
    style U3 fill:#f1f5f9,stroke:#94a3b8
    style U4 fill:#f1f5f9,stroke:#94a3b8
```

| User | Suppressed? | Why |
| --- | --- | --- |
| Acme Corp | 🔴 Yes, on **all three** emails | They are the user on the event |
| Bolt LLC | 🔴 Yes, on **all three** emails | They share `shared@books.com` (one hop) |
| Cedar Inc | 🟢 No | No shared address |
| Delta Co | 🟢 No | Shares `hr@bolt.com` with Bolt, but that is a **second** hop |

{% hint style="success" %}
**Contrast with a personal address.** If Acme unsubscribed via `ceo@acme.com` instead, only Acme would be suppressed, because nobody else has that address. Acme would still be suppressed on all three of its emails.
{% endhint %}

## Opt‑in groups behave differently

The one‑hop expansion applies to **opt‑out** groups only. For **opt‑in** groups, Hybrid behaves exactly like User ID: consent is personal and is never inferred for another user just because they share an address.

```mermaid
flowchart LR
    I(["Opt‑in event<br/>Bolt LLC · shared@books.com"])
    I -->|"only this user"| B["Bolt LLC subscribed"]
    I -.-x|"no expansion"| A["Acme Corp<br/>(also has shared@books.com)"]
    style B fill:#eafaf0,stroke:#16a34a,stroke-width:2px
    style A fill:#f1f5f9,stroke:#94a3b8
```

## Where the policy is applied

The same suppression set is used everywhere Sortment checks whether someone can be messaged, so results are consistent.

```mermaid
flowchart TB
    SET[("Suppressed user IDs<br/>(materialised per subscription group)")]
    SET --> C["Campaign sends<br/>excluded before the audience is handed to the provider"]
    SET --> J["Journey & single sends<br/>eligibility check per user"]
    SET --> P["Preference centre<br/>what the user sees as their status"]
    SET --> R["Audience Insights<br/>'unreachable users' count"]
    SET --> K["Campaign analytics<br/>'Send skips → Unsubscribed users'"]
```

{% hint style="info" %}
In the campaign **Send skips** panel, the *Total / Global / Subscription group unsubscribed users* rows are computed with this same policy. A user suppressed through a shared address will appear there even though they never clicked unsubscribe themselves.
{% endhint %}

## Setup requirements

Hybrid depends on knowing which fields hold contact details. These come from **Contact Fields** in workspace settings. See [Subscription Groups and Contact Fields](subscription-groups-and-contact-fields.md).

| Requirement | Detail |
| --- | --- |
| Contact Fields configured per channel | Primary field plus any secondary fields, e.g. `email`, `email2`, `email3` for email |
| Fields live on the **primary user table** | Fields on joined tables are not supported for Hybrid |
| Exact matching | `Billing@Acme.com` and `billing@acme.com` are **different** addresses. Normalise case and whitespace in your warehouse |
| Bulk CSV uploads need **both** columns | `user_id` **and** `channel_id` are required when the workspace is on Hybrid. See [Bulk Unsubscribe](../how-to-guides/bulk-unsubscribe.md) |
| Regenerate after changing Contact Fields | Adding or removing a contact field requires rebuilding subscription materialisations |

{% hint style="warning" %}
The [Bulk Unsubscribe](../how-to-guides/bulk-unsubscribe.md) guide describes uploading **either** `channel_id` **or** `user_id`. That applies to the User ID and Channel ID policies. On Hybrid the CSV must contain **both** columns, and every row suppresses the named user on all of their addresses.
{% endhint %}

## Choosing the right tool for data hygiene

Because Hybrid suppresses the **whole user**, an unsubscribe is a blunt instrument for cleaning up a single bad address. Use the decision tree below.

```mermaid
flowchart TD
    Q{{"What are you trying to do?"}}
    Q -->|"The person asked<br/>not to be contacted"| U["Record an <b>unsubscribe</b><br/>(opt‑out event, CSV or API)"]
    Q -->|"One address bounced<br/>or failed validation"| V{{"Should the user still be<br/>reachable on other addresses?"}}
    V -->|"Yes"| N["<b>Clear the bad address</b> in the warehouse<br/>(set the column to NULL).<br/>Sends to that column are skipped by the null check;<br/>other addresses keep working."]
    V -->|"Yes, but I can't edit<br/>the warehouse"| X["Add an <b>exclusion audience</b><br/>of flagged addresses to the campaigns<br/>that target that column"]
    V -->|"No, drop the whole user"| U
    style U fill:#fee2e2,stroke:#dc2626
    style N fill:#eafaf0,stroke:#16a34a,stroke-width:2px
    style X fill:#eef4ff,stroke:#3b6ef5
```

{% hint style="danger" %}
**Do not upload email‑validation results (bounces, invalid, spam‑trap) as unsubscribes** unless you intend to stop contacting the entire user. Under Hybrid each row will suppress the named user on every address, and any other user sharing that address.
{% endhint %}

## Quick reference

| Scenario | User ID | Channel ID | Hybrid |
| --- | --- | --- | --- |
| Acme unsubscribes via its billing email | Acme suppressed on all emails | Only the billing email suppressed, on every user that has it | Acme on all emails **+** every user with that billing email |
| Two companies share one mailbox; one unsubscribes | Only the unsubscribing company | Both, but only on that mailbox | Both, on **all** their emails |
| A single address bounces | Whole user suppressed if recorded as opt‑out | Only that address | Whole user suppressed, so prefer clearing the column instead |
| Someone opts **in** via a shared address | Only that user | Only that address | Only that user (no expansion) |

## FAQ

<details>

<summary>Does Hybrid chain across several shared addresses?</summary>

No. It stops after one hop. A user pulled in through a shared address does not pull in further users through their other addresses.

</details>

<details>

<summary>We only have one email field. Does Hybrid change anything?</summary>

Only for shared addresses. With a single field and no duplicates, Hybrid behaves like User ID.

</details>

<details>

<summary>Can I switch from User ID to Hybrid?</summary>

Yes, but it requires Contact Fields to be configured for every channel you message on, and subscription materialisations to be regenerated. Contact Sortment support to schedule the switch.

</details>

<details>

<summary>How do I see who was suppressed and why?</summary>

Open the campaign, click **View reasons** next to *Send skips*. The *Unsubscribed users* rows reflect this policy. For a single user, the Preference centre shows their status per subscription group.

</details>

## Related

* [Subscription Groups and Contact Fields](subscription-groups-and-contact-fields.md)
* [Bulk Unsubscribe](../how-to-guides/bulk-unsubscribe.md)
* [Delivery Controls](delivery-controls.md)
