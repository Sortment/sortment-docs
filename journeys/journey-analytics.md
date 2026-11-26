# Read journey entry and conversion trends

Open **Journey analytics** to see how many entry and conversion events your journey records over time. Use it to compare periods and investigate changes after editing a journey.

## Open the chart

1. Open an existing journey from **Engage → Journeys**.
2. For an active or deactivated journey, select **Journey analytics** in the canvas header.
3. Choose a date range. The initial view uses the last seven days.
4. Choose **Hourly**, **Daily**, **Weekly**, or **Monthly** to group the events.
5. Switch between **Aggregate** and **Cumulative** to read individual periods or the running total within the selected range.

The chart and the **Entered**, **Converted**, and **Conversion rate** totals reflect the selected date range.

## What do the numbers mean?

| Metric | Meaning |
| --- | --- |
| **Entered** | The number of journey entry events recorded in the period. |
| **Converted** | The number of journey conversion events recorded in the period. |
| **Conversion rate** | Converted divided by Entered, displayed as a percentage. If there are no entry events, the rate is shown as zero. |

These are event counts across the journey's versions. A person who enters more than once can contribute more than one entry. The chart is not a unique-person report.

Entries and conversions are grouped by their own event timestamps. A conversion recorded today may belong to someone who entered earlier. The rate therefore does not necessarily describe the percentage of today's entrants who later converted.

For example, 100 entry events and 20 conversion events in a period produce a 20% rate. This alone does not establish that those 20 conversions came from those same 100 entries. See [Journey Settings](journey-settings.md) for the journey's conversion criteria.

## Aggregate or Cumulative?

**Aggregate** shows the events in each time bucket. Use it to compare one day or week with another.

**Cumulative** adds events from the start of the selected range through each bucket. Its conversion rate uses the running conversion total divided by the running entry total. Changing the date range changes where that running total starts.

## What does Version periods show?

Turn **Version periods** on to display approximate version bands behind the chart. These bands use version creation times; they are not an exact record of when each version was published.

The bands do not filter the metrics to a version and do not establish that a version caused a change in performance.

The separate message analytics view has a version selector for its **Journey Conversion** reporting. It initially selects the current version when available. Check the selected versions and time range before comparing that view with the all-version journey chart.

## Why is the chart empty?

Check the selected journey and date range. **No journey entries or conversions in this range** means the chart has no matching events to display. Widen the range if the journey ran earlier.

If expected events are still missing, [contact support](../getting-started/contact-us.md) with the journey name, date range, and the metric you expected. An empty chart does not by itself prove that a message was not sent.
