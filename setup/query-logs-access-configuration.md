# Query Logs Access Configuration

To read query history from Sortment, the connected warehouse role or service account needs additional permissions beyond standard read access, depending on the warehouse.

## **BigQuery**

To read query history from `INFORMATION_SCHEMA.JOBS*` views, you'll additionally need one of:

* BigQuery Resource Viewer (`roles/bigquery.resourceViewer`) — can view metadata about jobs.
* A custom role containing:
  * `bigquery.jobs.listAll`
  * `bigquery.jobs.get`
  * `bigquery.jobs.list`

The key permission is usually `bigquery.jobs.listAll`, which allows viewing jobs run by other users in the project.

## **Snowflake**

To access query history, the role additionally needs access to:

* `SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY` or `INFORMATION_SCHEMA.QUERY_HISTORY`

This is typically granted through:

* `MONITOR` privilege on the account or warehouse
* Or one of the built-in roles:
  * `ACCOUNTADMIN`
  * `SECURITYADMIN` (depending on scope)
  * `MONITOR USAGE`

### Which scheduled job issued this Snowflake query?

In Snowflake query history, inspect the query tag for the statement you are investigating. A Sortment query issued in a scheduled job can include these fields:

| Field | What it tells you |
| --- | --- |
| `scheduler_task` | The type of scheduled work that issued the query. |
| `scheduler_job_id` | The job ID. Use it to group statements from the same job. |

For example, two statements with the same job ID can belong to one run even if their SQL differs. A statement with no scheduler fields is not evidence that a run failed: queries issued outside a scheduled job may have no job context. When asking support about a run, include its time and timezone, the Snowflake query ID and the scheduler fields if present.

## **Athena**

To access query history, the principal needs the following Athena permissions:

* `athena:ListQueryExecutions`
* `athena:GetQueryExecution`
* `athena:GetQueryResults`
* `athena:BatchGetQueryExecution`

Access to the Athena workgroups whose history you want to inspect is also required.

Example IAM policy:

json

```json
{
  "Action": [
    "athena:ListQueryExecutions",
    "athena:GetQueryExecution",
    "athena:GetQueryResults",
    "athena:BatchGetQueryExecution"
  ],
  "Effect": "Allow",
  "Resource": "*"
}
```
