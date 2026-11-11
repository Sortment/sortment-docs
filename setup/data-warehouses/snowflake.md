# Snowflake

## Overview

Sortment lets you access data stored in your Snowflake and use it to create audiences.

> 📘 Connecting Sortment to Snowflake requires setup in both platforms. It's recommended to set up a specific account with the correct permissions in Snowflake before configuring the connection in Sortment.

## Connection configuration

To get started, select Snowflake on Data connections page and and follow the steps below.

### Snowflake configuration

Following required fields into Sortment:

* **Account identifier**: This is always found at the beginning of your Snowflake URL which might look like this: `https://ACCOUNT_IDENTIFIER.snowflakecomputing.com`.
* > ℹ️ Account identifier format may differ based on Snowflake account age.
  >
  > For example, older Snowflake accounts often have identifiers that look like ACCOUNT\_LOCATOR.CLOUD\_REGION\_ID.CLOUD, whereas newer Snowflake accounts have identifiers that look like ORGNAME-ACCOUNT\_NAME.
  >
  > For more details, visit [Snowflake's account identifier docs](https://docs.snowflake.com/en/user-guide/admin-account-identifier).
* **Warehouse** is the primary Snowflake warehouse. Sortment uses it for all queries unless you configure a secondary warehouse for the web app.
* **Database**: The database that Sortment can access to write data in Snowflake. Follow the [Snowflake query reference](snowflake.md#snowflake-query-reference) to share with Sortment access to a database with write access to respective schema.

### Can dashboards use a different warehouse from scheduled jobs?

Yes. In **Workspace Settings → Data connection**, enter the second virtual warehouse’s name in **Secondary warehouse (frontend queries only)**. For example, you could keep scheduled deliveries on `MARKETING_JOBS` and run dashboards, charts, explores and analytics pages on `MARKETING_UI`. API calls and AI queries continue to use the primary warehouse. Leave the optional field empty to use one warehouse for both workloads.

Ask your Snowflake administrator to confirm that the connection’s role has **USAGE** on the secondary warehouse. Test the connection. If the test reports that it could not run on that warehouse, check the name and the role’s access. An interactive query can fall back to the primary warehouse when the secondary is unavailable, so a dashboard loading successfully does not by itself confirm the configuration. Connections using external-browser authentication use the primary warehouse.

### Service Account Credentials

* **Username:** This should be a dedicated account for Sortment. At minimum, this user must have read access to your data relevant for Sortment. This user should have read/write access to a dedicated schema where Sortment can write campaigns and analytics data. Follow the [Snowflake query reference](snowflake.md#snowflake-query-reference) to create a user with relevant access.
* **Role:** This role will be used when executing queries in Snowflake. If left blank, Sortment will use the user’s default role. Follow the [Snowflake query reference](snowflake.md#snowflake-query-reference) to create a role with relevant access.
* **Authentication:** Choose to authenticate via a JSON Private Key. You can generate a Private Key for a Snowflake user following the guide [here](https://docs.snowflake.com/en/user-guide/key-pair-auth#generate-the-private-key). Once generated, add the Private Key File that is generated into Sortment. If you chose to encrypt your private key you will also need to supply the Private Key Passphrase.

## Snowflake query reference

Sortment recommends creating a new:

* **Role** for Sortment user with the required permissions for better access control on data and actions available to Sortment.
* **Warehouse** to avoid warehouse sharing between workloads, and easy access to all billing and credit usage information on Snowflake
* **Schema** for Sortment to write data into Snowflake to follow best data management practices.

**Prerequisites**

{% hint style="warning" %}
To complete all steps on Snowflake, the user should have at least ACCOUNTADMIN role.

Also, execute the steps in same worksheet as subsequent steps reference variables set in Step 1.
{% endhint %}

**Step 1: Create a new Snowflake User and Sortment Role**

You need to create a user account and assign the created Sortment role to the new user.

<pre class="language-sql"><code class="lang-sql">-- Set role for grants
USE ROLE ACCOUNTADMIN;
<strong>
</strong>set smt_username='SORTMENT_USER45';
set smt_default_warehouse='&#x3C;warehouse>';
set smt_default_namespace='&#x3C;database.schema>'; 
set smt_default_role='SORTMENT_ROLE';
set smt_comment='Used for Sortment integrations';

-- Run this only if you want to create a new warehouse for Sortment
-- We recommend keeping a MEDIUM warehouse. You may tweak this basis your usage estimate
CREATE WAREHOUSE IF NOT EXISTS identifier($smt_default_warehouse)
  WAREHOUSE_SIZE = 'MEDIUM';

-- Create a role for Sortment
CREATE ROLE IF NOT EXISTS identifier($smt_default_role)
COMMENT = $smt_comment;

-- Create Sortment's user
CREATE USER IF NOT EXISTS identifier($smt_username)
TYPE=service
rsa_public_key='&#x3C;public_key>' 
DEFAULT_WAREHOUSE=$smt_default_warehouse
DEFAULT_NAMESPACE=$smt_default_namespace
DEFAULT_ROLE=$smt_default_role
COMMENT=$smt_comment;

-- Grant usage permissions to the role
GRANT ROLE identifier($smt_default_role) TO ROLE SYSADMIN;
GRANT USAGE ON WAREHOUSE identifier($smt_default_warehouse) TO ROLE identifier($smt_default_role);

-- Grant role to the Sortment user
GRANT ROLE identifier($smt_default_role) TO USER identifier($smt_username);
</code></pre>

Once you've created a Snowflake user account, you're ready to add read and write access to the Sortment User.

**Step 2: Assign write access to a dedicated schema to Sortment role**

Sortment needs write access to write back Audiences and Campaigns data back to your Snowflake warehouse.

The snippet includes creation of a new database for Sortment to use. If you already have database you intend to use, omit the creation steps. These steps grant access to Sortment role to a write schema and permission to access the tabels created by Sortment in the database.

```sql
set smt_write_db='SORTMENT_WRITE_DB';

-- Create a new database for Sortment to use
CREATE DATABASE IF NOT EXISTS identifier($smt_write_db);

-- Grant write access to a sortment schema where we would write audience, campaigns, audit logs, analytics
USE identifier($smt_write_db);
CREATE SCHEMA IF NOT EXISTS sortment;

-- Grant access to Sortment role to access the schema
GRANT OWNERSHIP ON SCHEMA sortment TO ROLE identifier($smt_default_role);

-- Grant access permissions on write database created for Sortment
GRANT USAGE ON DATABASE identifier($smt_write_db) TO ROLE identifier($smt_default_role);
GRANT USAGE ON ALL SCHEMAS IN DATABASE identifier($smt_write_db) TO ROLE identifier($smt_default_role);
GRANT SELECT ON ALL TABLES IN DATABASE identifier($smt_write_db) TO ROLE identifier($smt_default_role);
GRANT SELECT ON FUTURE TABLES IN DATABASE identifier($smt_write_db) TO ROLE identifier($smt_default_role);
GRANT SELECT ON ALL VIEWS IN DATABASE identifier($smt_write_db) TO ROLE identifier($smt_default_role);
GRANT SELECT ON FUTURE VIEWS IN DATABASE identifier($smt_write_db) TO ROLE identifier($smt_default_role);
```

**Step 3: Assign read access to relevant tables to Sortment role**

If you want Sortment to access multiple databases, run this step multiple times, changing `smt_read_db` each time.

```sql
-- Follow the following steps to give Sortment read access to your data. 
-- These tables will be used to setup schema and run queries for marketing use cases.
-- This step can be run multiple times, if you have tables distributed across different databases.
set smt_read_db='<read_database>';

GRANT USAGE ON DATABASE identifier($smt_read_db) TO ROLE identifier($smt_default_role);
GRANT USAGE ON ALL SCHEMAS IN DATABASE identifier($smt_read_db) TO ROLE identifier($smt_default_role);
GRANT SELECT ON ALL TABLES IN DATABASE identifier($smt_read_db) TO ROLE identifier($smt_default_role);
GRANT SELECT ON FUTURE TABLES IN DATABASE identifier($smt_read_db) TO ROLE identifier($smt_default_role);
GRANT SELECT ON ALL VIEWS IN DATABASE identifier($smt_read_db) TO ROLE identifier($smt_default_role);
GRANT SELECT ON FUTURE VIEWS IN DATABASE identifier($smt_read_db) TO ROLE identifier($smt_default_role);
```

### Test connection

When setting up a source for the first time, Sortment validates the following:

* Network connectivity
* Snowflake credentials
* Permission to list schemas and tables
* Permission to write to the schema

All configurations must pass them for uninterrupted access to Sortment. Some sources may initially fail connection tests due to timeouts. Once a connection is established, subsequent API requests should happen more quickly, so it's best to retry tests if they first fail. You can do this by clicking Continue again.

If you've retried the tests and verified your credentials are correct but it is still failing, reach out to [support@sortment.com](mailto:support@sortment.com)

## Tips and troubleshooting

If you encounter an error or question not listed below and need assistance, don't hesitate to [reach out](mailto:support@sortment.com) . We're here to help.

### Connection timeout

When initially testing your connection, you may receive a connection timeout error. Once a connection is established, subsequent API requests should happen more quickly, so it's best to retry the tests if they first fail. You can do this by clicking Continue again.

### Network error: Could not reach Snowflake

You may receive this error if the input for Account identifier is invalid. Instead of using the complete Snowflake URL, for example, `https://ACCOUNT_IDENTIFIER.snowflakecomputing.com` ensure that you're only using the ACCOUNT\_IDENTIFIER part of the URL, for example, companyname-abc123 or companyname-abc123.us-east-1.
