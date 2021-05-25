---
title: Google IAM Quick Reference
weight: 10
---
Concise reference documentation about Google IAM.

Where's jamescaja?

## The Authorization Pipeline model

### Base model

![base-user-nestedgroup-role-permission.png](/images/base-user-nestedgroup-role-permission.png)

### Expanded model

![Expanded model](/images/exploded-user-nestedgroup-role-permission-2.png)

### The overall landscape and functional domains

![Overall landscape](/images/auth-pipeline-with-domains-and-functions-3.png)

## Group naming conventions

|Group Type|Naming Convention|
| ----------- | ----------- |
|Member|[*Application*]-[*User Role*]-[*Environment*]|
|Component|[*Project Name*]-[*Component Label*]-[*Group Access Level*]|

## Member group-related

### Membership User Roles

|User Role|Description|
| ----------- | ----------- |
|admin|Performs admin tasks in any environment|
|developer|Software developer.  May have restricted access in production, but more elevated access in development or UAT environments|
|user|An end user.  Could be internal (IT, any Express business unit) or external (contractor, vendor).  Typically granted only the required permission to perform their job tasks|

### Applications

|Label|Description|
| ------ | ----------- |
|cdh|Customer Data Hub|
|dsa|Data Science & Analytics|
|fsr|Free Shipping & Returns|
|stylist|Stylist|
## Component group-related

### Access name mappings

|Component Access Class|Group Access Level|Description|
| ----------- | ----------- | ----------- |
|Admin|Admin|Mostly unlimited access (within a project)|
|Editor|Poweruser|Read-Write access, some create object also|
|Viewer|User|Read-only access|

### Components

|Label|Description|
| ------ | ----------- |
|appeng|App Engine|
|bq|Big Query|
|cloudfn|Cloud Functions|
|cloudsql|Cloud SQL|
|compute|Compute|
|dataflow|Cloud Dataflow|
|storage|Cloud Storage|

## See Also

[Google IAM Guide]({{< relref "Google-IAM-Guide.md" >}})
