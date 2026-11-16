# Journey Components

Journeys in Sortment are made up of **blocks**, which represent individual steps within the automation flow. Each block defines a specific type of behavior, decision, or action that should be taken when a user progresses through the journey.

## Available block types

Blocks are categorised based on their purpose:

### Triggers

Triggers are the starting point of any journey. They define the condition that initiates the flow for a user. Without a trigger, no user will enter the journey. This is compulsory

* **User action**: Starts the journey when a user performs a specific action (e.g., clicks a button, completes a transaction).
* **Business event**: Begins the journey based on a business-related event (e.g., purchase completed, signup).
* **Scheduled**: Initiates the journey at a specific date and time, or on a recurring schedule.
* **Audience entry or exit**: Starts the journey when a user enters or exits a defined audience.
* **Other journey**: Triggers a journey when a user is added to this journey from another journey.

For a detailed breakdown, see [Triggers](trigger.md#available-trigger-types).

### 1. Delays

Delay blocks are used to pause a user’s progress through the journey. This is helpful when you want to space out communication or wait for user behavior before continuing.

* **Delay** – Wait for a fixed amount of time before the next step.
* **Wait for action** – Pause until a specific event occurs (e.g. user opens an email or makes a purchase).
* **Wait till date** – Resume the journey at a specific date and time, coming from user or event property.

For a detailed breakdown, see [Delays](delays.md#id-1.-delay).

***

### 2. Flow Control

These blocks let you dynamically route users down different paths based on their attributes, behaviours, or randomisation logic.

* **Filter** – Branch logic based on conditions (e.g. user’s location, subscription status).
* **Split** – Route users by the value of a property.
* **Experiment** – Setup an A/B test in the journey and measure performance across content or flow logic ideas.
* [**AI Classify**](flow-control.md#ai-classify) – Route users by the meaning of text, such as a reply.

For a detailed breakdown, see [Flow Control blocks](flow-control.md).

***

### 3. Actions

Action blocks are used to communicate with users or trigger downstream updates.

* **Send Email** – Deliver an email campaign using one of your saved templates.
* **Send SMS** – Send a transactional or promotional SMS.
* **Send WhatsApp** – Send a WhatsApp message using a connected provider.
* **Call API** – Trigger an external API call (e.g. update CRM, notify external service).
* **Update trait** – Modify a user’s profile attribute (e.g. mark “onboarded” as true).
* **Add to journey** – Dynamically enroll the user into another journey.

For a detailed breakdown, see [Action Blocks](action-blocks.md#id-1.-send-email).
