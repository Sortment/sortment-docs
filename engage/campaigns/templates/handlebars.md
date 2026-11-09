# Handlebars

Handlebars is a JavaScript library used for creating dynamic templates.

It allows you to define templates with placeholders, and these placeholders are replaced with data when the template is triggered with the information sent in the payload.

Handlebars within Sortment support features like conditional operations, loops, date time formatting, partial searches, and custom helpers, making it a very cool tool to use when creating your templates.

### **Handlebars in Sortment's Templates**

#### Why does my email say “Invalid template variables” when the preview looks right?

The error lists the variable names the email editor could not save. A name can appear in the template code even when it is invisible in the preview.

1. Look for the name in the email body and preview text. In the MJML editor, open the template’s **⋯** menu and choose **View code** to inspect the markup. Check HTML comments, too: a note like `<!-- Example: {{first_name}} -->` can cause the same error as visible text.
2. If the name is only in a comment, remove the placeholder from the source. If you need it for personalization, give it a name that starts with a letter and uses only letters and numbers, such as `{{firstName}}`. For an AI-generated template, you can ask Sortment AI to make that correction.
3. Save again. If you renamed a variable, check its mapping and preview the email with a sample value before using the template in a campaign.

This naming rule applies to simple placeholders you create in the email editor. You do not need to rename your warehouse column to match the placeholder.

#### Example payload

To understand how handlebars work, we first need to establish a sample payload, based off which the Handlebars function and applicability can be demonstrated.

```
{
  "members": [
    {
      "first_name": "John",
      "last_name": "Doe",
      "city": ""
    }
  ],
  "investments": [
    {
      "amount": 10,
      "company": "L&T"
    },
    {
      "amount": 20
    }
  ],
  "companies": {
    "L&T": "India",
    "Apple": "USA"
  },
  "user_type": "pro",
  "period": {
    "start": "2023-10-01",
    "end": "2023-10-03"
  },
  "classification": ["active", "pro"]
}

```

Sortment has default as well as custom **handlebars** that you can use in your templates. Let’s have a look at what these are and how to use them using the above payload and data as example responses.

{% hint style="warning" %}
**Double Quotes within Handlebars \{{" "\}} may break your templates!**

Double quote may break your syntax in HBS, especially in emails! And nobody wants that!

**For example:** Use `{{math number1 '+' number2}}` instead of `{{math number1 "+" number2}}`.

Use single quotes `{{' '}}` instead!
{% endhint %}

<details>

<summary>To access placeholder values</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{key}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{user_type}}
```
{% endcode %}

{% code title="Output" %}
```
pro
```
{% endcode %}

</details>

<details>

<summary>To access values of an object</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{key.subkey}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{period.start}} to {{period.end}}
```
{% endcode %}

{% code title="Output" %}
```
{{period.start}} to {{period.end}}
```
{% endcode %}

</details>

<details>

<summary>To access object values from an array</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{key.index.subkey}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{members.0.first_name}}
```
{% endcode %}

{% code title="Output" %}
```
John
```
{% endcode %}

</details>

<details>

<summary>To access values from an array</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{key.index}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{classification.0}}
```
{% endcode %}

{% code title="Output" %}
```
active
```
{% endcode %}

</details>

<details>

<summary>To get length of an array</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{key.length}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
User made {{investments.length}} investments
```
{% endcode %}

{% code title="Output" %}
```
User made 2 investments
```
{% endcode %}

</details>

<details>

<summary>To check if variable exists</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#if variable}}
  Content Here
{{else if variable}}
  Content Here
{{else}}
  Content Here
{{/if}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#if members.0.city}}
  City available for member 1 and may be member 2
{{else if members.1.city}}
  City available for member 2 only
{{else}}
  City not available for member 1 and 2
{{/if}}
```
{% endcode %}

{% code title="Output" %}
```
City available for member 1 and may be member 2
```
{% endcode %}

</details>

<details>

<summary>Approach 1: To check if variable equals a certain value</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#if (eq variable 'value')}}
  Content Here
{{else if (eq variable 'value')}}
  Content Here
{{/if}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#if (eq investments.0.company 'L&T')}}
  First investment was L&T
{{else if (eq investments.0.company 'Apple')}}
  First investment was Apple
{{else}}
  First investment was neither Apple nor L&T
{{/if}}
```
{% endcode %}

{% code title="Output" %}
```
First investment was L&T
```
{% endcode %}

</details>

<details>

<summary>Approach 2: To check if variable equals a certain value</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#ifEquals variable 'value'}}
  Content Here
{{else ifEquals variable 'value'}}
  Content Here
{{else}}
  Content Here
{{/ifEquals}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#ifEquals investments.0.company 'L&T'}}
  First investment was L&T
{{else ifEquals investments.0.company 'Apple'}}
  First investment was Apple
{{else}}
  First investment was neither Apple nor L&T
{{/ifEquals}}
```
{% endcode %}

{% code title="Output" %}
```
First investment was L&T
```
{% endcode %}

</details>

<details>

<summary>To check if multiple variables equal a certain value (AND and OR options)</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#if (and/or (eq/ne/lt/gt/lte/gte variable 'value') (eq/ne/lt/gt/lte/gte variable 'value'))}}
  Content Here
{{else}}
  Content Here
{{/if}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#if (or (eq investments.0.company 'L&T') (eq investments.0.company 'Apple'))}}
  First investment is either L&T OR Apple
{{/if}}

{{#if (and (eq investments.0.company 'L&T') (gt investments.0.amount 5))}}
  First investment is L&T and investment amount is greater than 5
{{/if}}

{{#if (and (ne user_type 'pro') (gte investments.0.amount 10))}}
  User is not pro but first investment is greater than or equal to 10
{{else if (and (eq user_type 'pro') (lt investments.0.amount 10))}}
  User type is pro but first investment is less than 10
{{else if (and (eq user_type 'pro') (eq investments.0.amount 10))}}
  User type is pro and first investment is equal to 10
{{/if}}

```
{% endcode %}

{% code title="Output" %}
```
First investment is either L&T OR Apple
First investment is L&T and investment amount is greater than 5
User type is pro and first investment is equal to 10
```
{% endcode %}

</details>

<details>

<summary>To check if variable equals a regular expression</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#ifMatches variable 'regex'}}
  Content Here
{{else ifMatches variable 'regex'}}
  Content Here
{{else}}
  Content Here
{{/ifMatches}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#ifMatches members.0.last_name 'Aga\*'}}
  Matched
{{else}}
  Not matched
{{/ifMatches}}
```
{% endcode %}

{% code title="Output" %}
```
Matched
```
{% endcode %}

</details>

<details>

<summary>To check if string starts with certain characters</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#ifStartsWith variable 'value'}}
  Content Here
{{else ifStartsWith variable 'value'}}
  Content Here
{{else}}
  Content Here
{{/ifStartsWith}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#ifStartsWith members.0.first_name 'A'}}
  Name starts with A
{{else}}
  Name does not start with A
{{/ifStartsWith}}
```
{% endcode %}

{% code title="Output" %}
```
Name starts with A
```
{% endcode %}

</details>

<details>

<summary>To use OR operator between two variables</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#compare var1 'or' var2}}
  Content Here
{{else compare var1 'or' var2}}
  Content Here
{{/compare}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#compare investments.0.amount 'or' investments.1.amount}}
  Invested at least once
{{else}}
  No investment made
{{/compare}}
```
{% endcode %}

{% code title="Output" %}
```
Invested at least once
```
{% endcode %}

</details>

<details>

<summary>To use AND operator between two variables</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#compare var1 'and' var2}}
  Content Here
{{else compare var1 'and' var2}}
  Content Here
{{/compare}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#compare investments.0.amount 'and' investments.1.amount}}
  Invested at least twice
{{else}}
  One or fewer investments made
{{/compare}}
```
{% endcode %}

{% code title="Output" %}
```
Invested at least twice
```
{% endcode %}

</details>

<details>

<summary>To use a specified default value if the actual value does not exist (or is null)</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{default variable 'value'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{default members.0.city 'Bangalore'}}
```
{% endcode %}

{% code title="Output" %}
```
Bangalore
```
{% endcode %}

</details>

<details>

<summary>To sum two numbers</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{math number1 '+' number2}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{math investments.0.amount '+' investments.1.amount}}
```
{% endcode %}

{% code title="Output" %}
```
30
```
{% endcode %}

</details>

<details>

<summary>To subtract two numbers</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{math number1 '-' number2}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{math investments.1.amount '-' investments.0.amount}}
```
{% endcode %}

{% code title="Output" %}
```
10
```
{% endcode %}

</details>

<details>

<summary>To multiply two numbers</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{math number1 '*' number2}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{math investments.0.amount '*' investments.1.amount}}
```
{% endcode %}

{% code title="Output" %}
```
200
```
{% endcode %}

</details>

<details>

<summary>To divide numbers</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{math number1 '/' number2}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{math investments.0.amount '/' investments.1.amount}}
```
{% endcode %}

{% code title="Output" %}
```
0.5
```
{% endcode %}

</details>

<details>

<summary>To perform modulo</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{math number1 '%' number2}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{math investments.0.amount '%' investments.1.amount}}
```
{% endcode %}

{% code title="Output" %}
```
10
```
{% endcode %}

</details>

<details>

<summary>To round numbers</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{math number 'round' decimal_places}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{math investments.0.amount 'round' 2}}
{{math investments.0.amount 'round'}}
```
{% endcode %}

{% code title="Output" %}
```
10.00
10
```
{% endcode %}

</details>

<details>

<summary>To check for negative condition (show something only if key doesn't exist)</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#unless key}}
  Content Here
{{/unless}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#unless investments}}
  Investments were not made!
{{else}}
  Investments were made!
{{/unless}}
```
{% endcode %}

{% code title="Output" %}
```
Investments were made!
```
{% endcode %}

</details>

<details>

<summary>To iterate over a list/array</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#each key}}
  {{this.subkey}}
{{/each}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#each investments}}
  {{math @index '+' 1}}.
  {{{default this.company 'Not known'}}}
  - ${{this.amount}}
{{else}}
  No investments were made!
{{/each}}
```
{% endcode %}

{% code title="Output" %}
```
1. L&T - $10
2. Not known - $20
```
{% endcode %}

</details>

<details>

<summary>To use combination of conditions and nesting (advanced)</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#if variable}}
  Content Here
{{else}}
  {{#ifEquals variable 'value'}}
    Content Here
  {{else}}
    {{#ifMatches variable 'regex'}}
      Content Here
    {{else}}
      {{#ifStartsWith variable 'value'}}
        Content Here
      {{else}}
        {{#if (eq var1 var2)}}
          Content Here
        {{/if}}
      {{/ifStartsWith}}
    {{/ifMatches}}
  {{/ifEquals}}
{{/if}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#if investments}}
  {{#ifEquals investments.0.company 'Apple'}}
    Invested in Apple Inc!
  {{else}}{{#ifMatches investments.0.company 'Amazon\*'}}
      Invested in Amazon ventures!
    {{else}}{{#ifStartsWith investments.0.company 'Microsoft'}}
        Invested in Microsoft!
      {{else}}{{#if (eq investments.0.company 'L&T')}}
          Invested in Larsen & Toubro!
        {{/if}}
      {{/ifStartsWith}}
    {{/ifMatches}}
  {{/ifEquals}}
{{else}}
  No investments were made!
{{/if}}
```
{% endcode %}

{% code title="Output" %}
```
Invested in Larsen & Toubro!
```
{% endcode %}

</details>

<details>

<summary>To lookup values and use them in placeholders (advanced)</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#each iterate_key}}
    {{subkey}}
    {{lookup ../key [subkey]}}
{{/each}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#each investments as | investment |}}
    {{#if company}}
        {{{company}}} is in {{lookup ../companies [company]}}
    {{/if}}
{{/each}}
```
{% endcode %}

{% code title="Output" %}
```
L&T is in India
```
{% endcode %}

</details>

<details>

<summary>To split variables and get value at index</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{split variable_name 'delimiter' index}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{split period.start '-' -1}}/{{split period.start '-' 1}}/{{split period.start '-' 0}} {{split period.end '-' -1}}/{{split period.end '-' 1}}/{{split period.end '-' 0}}
```
{% endcode %}

{% code title="Output" %}
```
01/10/2023
03/10/2023
```
{% endcode %}

</details>

<details>

<summary>To split variables and loop on it</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{#each (split variable_name 'delimiter')}}
  {{this}}
{{/each}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{#each (split period.start '-')}}
  {{this}}
{{/each}}
```
{% endcode %}

{% code title="Output" %}
```
2023
10
01
```
{% endcode %}

</details>

<details>

<summary>To check if timestamp equals to today or yesterday or tomorrow</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{relativeDay timestamp_with_timezone}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{relativeDay '2023-10-25T14:30:00+05:30'}}
{{relativeDay '2023-10-26T14:30:00+05:30'}}
{{relativeDay '2023-10-24T14:30:00+05:30'}}
{{relativeDay period.start}}
{{#ifEquals (relativeDay '2023-12-19T14:30:00+05:30') 'today'}}
  The date is today!
{{else}}
  The date is not today!
{{/ifEquals}}
```
{% endcode %}

{% code title="Output" %}
```
today
tomorrow
yesterday
before
The date is today!
```
{% endcode %}

</details>

<details>

<summary>To check difference between two dates (in seconds, minutes, days, hours, month, hours, etc)<br><br>The date and time must be in RFC-3339, ISO_8601 or the helper will return NaN</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{dateDiff timestamp1_with_timezone timestamp2_with_timezone 'ms / seconds / minutes / hours / days / weeks / months / years'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{dateDiff '2023-12-01T14:30:00+05:30' '2023-12-19T14:30:00+05:30' 'ms'}}
ms
{{dateDiff '2023-12-01T14:30:00+05:30' '2023-12-19T14:30:00+05:30' 'seconds'}}
secs
{{dateDiff '2023-12-01T14:30:00+05:30' '2023-12-19T14:30:00+05:30' 'minutes'}}
mins
{{dateDiff '2023-12-01T14:30:00+05:30' '2023-12-19T14:30:00+05:30' 'hours'}}
hours
{{dateDiff '2023-12-01T14:30:00+05:30' '2023-12-19T14:30:00+05:30' 'days'}}
days
{{dateDiff '2023-12-01T14:30:00+05:30' '2023-12-19T14:30:00+05:30' 'weeks'}}
weeks
{{dateDiff '2023-12-01T14:30:00+05:30' '2023-12-19T14:30:00+05:30' 'months'}}
months
{{dateDiff '2023-12-01T14:30:00+05:30' '2023-12-19T14:30:00+05:30' 'year'}}
years Since
{{dateDiff '2023-12-01T14:30:00+05:30' 'NOW' 'days'}}
days

{{#if (gt (dateDiff '2023-12-01T14:30:00+05:30' 'NOW' 'days') 10)}}
  More than 10 days!
{{else}}
  Less than 10 days!
{{/if}}
```
{% endcode %}

{% code title="Output" %}
```
1555200000 ms
1555200 secs
25920 mins
432 hours
18 days
2 weeks
0 months
0 years
Since 18 days
More than 10 days!
```
{% endcode %}

</details>

<details>

<summary>To format date value</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{formatDate timestamp_with_timezone 'short'}}
{{formatDate timestamp_with_timezone 'short' 'en-in'}}
{{formatDate timestamp_with_timezone 'short' 'en-in' '+05:30'}}
{{formatDate timestamp_with_timezone 'medium'}}
{{formatDate timestamp_with_timezone 'medium' 'en-in'}}
{{formatDate timestamp_with_timezone 'medium' 'en-in' '+05:30'}}
{{formatDate timestamp_with_timezone 'long'}}
{{formatDate timestamp_with_timezone 'long' 'en-in'}}
{{formatDate timestamp_with_timezone 'long' 'en-in' '+05:30'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{formatDate '2023-10-25T18:30:00Z' 'short'}}
{{formatDate '2023-10-25T18:30:00Z' 'short' 'en-in'}}
{{formatDate '2023-10-25T18:30:00Z' 'short' 'en-in' '+05:30'}}
{{formatDate '2023-10-25T18:30:00Z' 'medium'}}
{{formatDate '2023-10-25T18:30:00Z' 'medium' 'en-in'}}
{{formatDate '2023-10-25T18:30:00Z' 'medium' 'en-in' '+05:30'}}
{{formatDate '2023-10-25T18:30:00Z' 'long'}}
{{formatDate '2023-10-25T18:30:00Z' 'long' 'en-in'}}
{{formatDate '2023-10-25T18:30:00Z' 'long' 'en-in' '+05:30'}}
```
{% endcode %}

{% code title="Output" %}
```
10/25/2023
25/10/2023
26/10/2023
Oct 25, 2023
25 Oct 2023
26 Oct 2023
October 25, 2023
25 October 2023
26 October 2023
```
{% endcode %}

</details>

<details>

<summary>To format datetime value</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{formatDateTime timestamp_with_timezone 'short'}}
{{formatDateTime timestamp_with_timezone 'short' 'en-in'}}
{{formatDateTime timestamp_with_timezone 'short' 'en-in' '+05:30'}}
{{formatDateTime timestamp_with_timezone 'medium'}}
{{formatDateTime timestamp_with_timezone 'medium' 'en-in'}}
{{formatDateTime timestamp_with_timezone 'medium' 'en-in' '+05:30'}}
{{formatDateTime timestamp_with_timezone 'long'}}
{{formatDateTime timestamp_with_timezone 'long' 'en-in'}}
{{formatDateTime timestamp_with_timezone 'long' 'en-in' '+05:30'}}
{{formatDateTime timestamp_with_timezone 'custom' 'en-in' '+05:30'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{formatDateTime '2023-10-25T18:30:00Z' 'short'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'short' 'en-in'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'short' 'en-in' '+05:30'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'medium'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'medium' 'en-in'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'medium' 'en-in' '+05:30'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'long'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'long' 'en-in'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'long' 'en-in' '+05:30'}}
{{formatDateTime '2023-10-25T18:30:00Z' 'custom' 'en-in' '+05:30'}}
```
{% endcode %}

{% code title="Output" %}
```
25/10/2023, 18:30
25/10/2023, 06:30 pm
26/10/2023, 12:00 am
25 Oct 2023, 18:30
25 Oct 2023, 06:30 pm
26 Oct 2023, 12:00 am
25 October 2023 at 18:30
25 October 2023 at 06:30 pm
26 October 2023 at 12:00 am
26 October 2023 at 12:00:00 am
```
{% endcode %}

</details>

<details>

<summary>To format numeric value</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{formatNumber number}}
{{formatNumber number 'en-in'}}
{{formatNumber (math number 'round') 'en-in'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{formatNumber 4000000.112}}
{{formatNumber 4000000.112 'en-in'}}
{{formatNumber (math 4000000.11 'round') 'en-in'}}
{{formatNumber 4000000.112 'en-in' 'notation=compact'}}
{{formatNumber 4000000.112 'en-US' 'notation=compact'}}
{{formatNumber 42456235.112 'en-US' 'notation=compact, minimumFractionDigits=2, maximumFractionDigits=2, style=currency, currency=USD'}}
{{formatNumber 42456235.112 'en-in' 'notation=compact, minimumFractionDigits=2, maximumFractionDigits=2, style=currency, currency=INR'}}
```
{% endcode %}

{% code title="Output" %}
```
4,000,000.112
40,00,000.112
40,00,000
40L
4M
$42.46M
₹4.25Cr
```
{% endcode %}

</details>

<details>

<summary>To trim spaces from value</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{trim variable_name}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{trim ' Test Name '}}
```
{% endcode %}

{% code title="Output" %}
```
Test Name
```
{% endcode %}

</details>

<details>

<summary>To trim values to a particular length</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{trim variable_name max_length}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{trim 'First Middle Last Name' 20}}
```
{% endcode %}

{% code title="Output" %}
```
First Middle Last Na
```
{% endcode %}

</details>

<details>

<summary>To trim value to a particular length and then truncate until the last occurrence of the specified delimiter</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{trim variable_name max_length delimiter}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{trim 'First Middle Last Name' 20 ' '}}
```
{% endcode %}

{% code title="Output" %}
```
First Middle Last
```
{% endcode %}

</details>

<details>

<summary>To remove a string</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{remove variable_name 'remove_char/string'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{remove 'Sortment™' '™'}}
{{remove 'Sortment®' '®'}}
{{remove 'Sortment®™' '®|™'}}
```
{% endcode %}

{% code title="Output" %}
```
Sortment
Sortment
Sortment
```
{% endcode %}

</details>

<details>

<summary>To sum multiple numbers</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{sumAll start_key 'number_key'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{sumAll @root 'investments.amount'}}
{{formatNumber (sumAll @root 'investments.amount') 'en-US' 'style=currency, currency=USD'}}
```
{% endcode %}

{% code title="Output" %}
```
30
$30.00
```
{% endcode %}

</details>

<details>

<summary>Current time with date format</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{moment format='date_format'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{moment format='D/M/Y'}}
{{moment format='D/M/Y hh:mm A'}}
{{moment format='dddd, D MMMM'}}
{{moment format='dddd, Do MMMM Y HH:mm Z'}}
```
{% endcode %}

{% code title="Output" %}
```
26/11/2024
26/11/2024 02:51 PM
Tuesday, 26 November
Tuesday, 26th November 2024 14:54 +05:30
```
{% endcode %}

</details>

<details>

<summary>Specific time with formatting and timezone</summary>

{% code title="Format" fullWidth="false" %}
```handlebars
{{moment date format='date_format' tz='timezone'}}
```
{% endcode %}

{% code title="Example" %}
```handlebars
{{moment '1722605334190' format='D/M/Y'}}
{{moment '1722605334190' format='DD/MM/YYYY hh:mm A'}}
{{moment '1722605334190' format='dddd, D MMMM'}}
{{moment '1722605334190' format='dddd, Do MMMM Y HH:mm Z'}}
{{moment '1722605334190' format='dddd, Do MMMM Y HH:mm Z' tz='Europe/Andorra'}}
```
{% endcode %}

{% code title="Output" %}
```
2/8/2024
02/08/2024 06:58 PM
Friday, 2 August
Friday, 2nd August 2024 18:58 +05:30
Friday, 2nd August 2024 15:28 +02:00
```
{% endcode %}

</details>

