---
title: Google IAM Basics
weight: 10
---

{{% pageinfo %}}
**Summary** - Basics about IAM security.
{{% /pageinfo %}}

## Overview

Making Google Cloud (GCP) computing resources and applications available through the Cloud console allows people to do meaningful work.

Unlimited access grants to all users is a quick and easy way to do this. However, this approach will never pass a security audit. And Finance will frown on higher costs resulting from unnecessary resource use.

How can Express employees access GCP resources in a secure and cost-effective way?

In this guide, we introduce you to the concepts behind **Google Cloud Identity** and **GCP Identity and Access Management (IAM)**. Carefully designed IAM policies grant secure resource access to only the people who need it.

> See [Google Cloud Identity](https://cloud.google.com/identity/docs/overview) and [GCP Identity and Access Management (IAM)](https://cloud.google.com/iam) in the Google documentation for more detail.

After the policies are put in place, changes to the environment - new applications, existing application changes, or adding/removing user access - require ongoing maintenance.

The guide will describe an authorization model designed to simplify policy maintenance and resource access management.  It enables easy IAM policy changes to accomodate new applications and other resource access changes.

After describing the model, we'll show an [example](#a-real-world-example) of how it's been implemented in the development environment of an actual system.

> What this guide DOES NOT cover:
> - GCP Service Account IAM authorization
> - IAM Folder Inheritance

Let’s start with the fundamentals. Then you’ll learn how to create flexible and scalable IAM policies.

## Basics

### The Landscape

![the-neighborhood.png](/images/the-neighborhood.png)

At Express, working with the [GCP Cloud Console](https://cloud.google.com/) involves three distinct areas:

- Active Directory (authentication)
- Google Cloud Identity (authentication and authorization)
- Google Cloud Platform (authorization)

Because this guide focuses on *authorization* (what resources can I access) and not *authentication* (are you who you claim to be), we’ll talk about **Users** (Cloud Identity) and how they gain resource **Permissions**.

We’ll also discuss how using **Groups** and **Roles** make designing authorization policies a lot easier.

A few things to note:

#### GCP Doesn't Have Users, Google Does

Technically, **GCP Users** do not exist.

If you're familiar with Gmail, you know how to sign in to your account and  click on the Google Apps button to open other applications (Docs, Maps, Calendar, etc...).  When these apps open, you are already signed in and can do whatever your permissions allow.

Google uses a single user account that can be granted access to multiple Google applications, including GCP.

> Google user accounts are not environment-specific - meaning a person does not have a separate development and production account.  Access to resources in different environments (dev, uat, prod) is governed by the group membership assignments.

Even though GCP users don't exist, if you use the term we'll know what you mean.

#### Users and Groups in Google and AD are Independent, but Related

Although the names may look the same, these users and groups are separate entities.

However, they relate in these ways:

- Because Active Directory is used to authenticate Google users, they must have a valid AD network account.
- The Google user name must match the User Principal Name (UPN) of the AD network account.
- The associated AD network account must be a member of the AD **dlg-mfa-gcp** group for authentication to succeed.  It requires this for multi-factor authentication (MFA).

With that background, let's see how we can enable access to GCP resources.

> For the best education, we encourage you to read the full guide.  But if you're pressed for time or truly impatient, see the [Summarized Process](#the-summarized-process).

### Gaining Access to GCP Resources

By default, Google users have little to no access to anything in GCP.

> The concept of users, groups, roles, and permissions below are similar to other computing systems.  If this is already familiar to you, skip down to [Beyond Basics](#beyond-basics).

#### User -> Permission

To do useful work, users must have permission to use a resource.

![base-user-permission.png](/images/base-user-permission.png)

A user account can have multiple granted permissions.  Here's what that might look like:

![example-user-permission.png](/images/example-user-permission.png)

Pretty ugly!  Think how bad it would look with hundreds of users having access to over three thousand available permissions.

This quickly becomes unmanageable. And Google agrees because they don’t allow direct permission grants to user accounts.

#### User -> Role -> Permission

Their solution involves grouping commonly used permissions together in a **role**.

![base-user-role-permission.png](/images/base-user-role-permission.png)

The user is granted a single role instead of multiple permissions.   This simplifies our example:

![example-user-role-permission.png](/images/example-user-role-permission.png)

This looks better, but what if you have multiple users needing the same permissions? You can grant each user the same roles, but that can get tedious if you have many users.

Is there a way to simplify this task?  Of course there is!  

#### User -> Group -> Role -> Permission

If a set of users need the same permissions, wouldn’t it be nice to do a mass role assignment to grant the same role to each user in a single step?

Google thought so. They provide a **group** construct to combine multiple user accounts into a single logical unit. Similar to how permissions are assigned to a role, we can grant a user account membership in a group.

Then granting a role to each group member is simply a matter of granting that role to the group.

![base-user-group-role-permission.png](/images/base-user-group-role-permission.png)

Our diagram has become a set of relationships between user, group, role, and permission.

![example-user-group-role-permission.png](/images/example-user-group-role-permission.png)

> Groups may have multiple roles.  But in this concepts guide, we'll keep it simple by only showing a one-to-one relationship.

A best practice for granting permissions to users is to use groups and roles to abstract away the details. This simplifies security administration.

Roles can be granted directly to user accounts, but this is highly discouraged. [SAY WHY...]

![summary-user-group-role-permission.png](/images/summary-user-group-role-permission.png)

Next, let's extend this concept to create configurations that simplify access management administration.

## Beyond Basics

### Nested Groups

![advanced-nested-groups.png](/images/advanced-nested-groups.png)

Many other systems use the term **user group** to describe a container to group user accounts together.  Google also supports this, but uses the generic term **group** because groups can be members of another group in the same way user accounts can.

In fact, anything having membership in a group is technically called a **member**.  In this guide, we'll stick with using users and groups.

The ability to grant group membership to other groups has some powerful benefits.

### Building an Authorization Pipeline

Think of the path from user to permission as a pipeline.  The flow of authorization passes through group and role assignments.  

Using nested groups, we can design this pipeline to simplify administration and allow user access to new GCP resources with minimal work.

![base-user-nestedgroup-role-permission.png](/images/base-user-nestedgroup-role-permission.png)

Our authorization pipeline strings together two group types - Member and Component - to permit simplified and flexible access management.

![exploded-user-nestedgroup-role-permission-2.png](/images/exploded-user-nestedgroup-role-permission-2.png)

Let's learn more about these group types, working backwards from the **Permissions** toward the **Users**.  Later we'll talk about how they fit together to enable the authorization flow.

> We preface our actual group names with **dlg-gcp-** to mirror Active Directory groups.
> 
> Here we'll omit this prefix for clarity.

#### Component Groups

![component-group-2.png](/images/component-group-2.png)

**Components** are categories of related GCP resources.  *Compute*, *Storage*, *Big Query*, *App Engine*, and *Cloud Functions* are a few examples.

A component has three general classes of access: Admin, Editor, and Viewer

|Access Class|Description|
| ----------- | ----------- |
|Admin|Mostly unlimited access|
|Editor|Read-Write access, some create object also|
|Viewer|Read-only access|

GCP predefined **Roles** generally follow the naming convention "**Component Access-Class**".  For example, the **Bigquery Viewer** role contains permissions for Big Query read-only access.

For Component group naming purposes, we've mapped these component classes to corresponding group levels.

|Component Access Class|Group Access Level|
| ----------- | ----------- |
|Admin|Admin|
|Editor|Poweruser|
|Viewer|User|

So the *User* group level has read-only (Viewer) permissions, *Poweruser* has read-write (Editor), etc.

The component group name uses these level names in the naming convention [*Project Name*]-[*Component*]-[*Group Access Level*].

A few examples:

|Project Name|Component|Group Access Level|Group Name|
| ----------- | ----------- | ----------- | ----------- |
|fsr-dev|Storage|poweruser|fsr-dev-storage-poweruser|
|stylist-prod|Cloud SQL|admin|stylist-prod-cloudsql-admin|
|cdh-dev|BigQuery|user|cdh-dev-bq-user|

> For the standard component labels used in the group name, see the [Google IAM Quick Reference]({{< relref "Google-IAM-Quick-Reference.md#components" >}})

#### Member Groups

![member-group-2.png](/images/member-group-2.png)

Most of the day-to-day user access management work happens at the Member group level.

Where work with Component groups is considered permission configuration, adding a user account to a Member group really grants the user access to a GCP resource (together with [IAM Mapping](#iam-mapping), which we'll discuss below.)

These groups contain **Users** of a certain *Application*, having a defined *User Role*, in a specific *Environment*.

The **Member** group naming convention is [*Application*]-[*User Role*]-[*Environment*].

Valid category values might include:

|Application|User Role|Environment|
| ----------- | ----------- | ----------- |
|cdh|admin|prod|
|stylist|developer|uat|
|fsr|user|dev|
|*others...*|*others...*|*others...*|

> For example, we assign all Google users with production administration responsibilities in the CDH application to the cdh-admin-prod group.

## The Summarized Process

In [Gaining Access to GCP Resources](#gaining-access-to-gcp-resources), we went into detail about the authorization process.  Here we summarize that discussion:

Granting permissions directly to users becomes unmanageable very quickly.  

![example-user-permission.png](/images/example-user-permission.png)

For this reason, Google prohibits direct user permission assignments.  They support granting IAM **roles**, not **permissions**, to users.

![base-user-role-permission.png](/images/base-user-role-permission.png)

Assigning roles to groups instead of directly to users further simplifies security administration.

Only a single role grant is necessary instead of granting the role multiple times to individual users.

![base-user-group-role-permission.png](/images/base-user-group-role-permission.png)

The ability to use [nested groups](#nested-groups) results in a base model for GCP authorization.

![base-user-nestedgroup-role-permission.png](/images/base-user-nestedgroup-role-permission.png)

Expanding this model using **roles** and nested **groups** ([Member](#member-groups), [Component](#component-groups)) allows us to build [**Authorization Pipelines**](#building-an-authorization-pipeline) that grant GCP resource permissions to users indirectly.

![exploded-user-nestedgroup-role-permission-2.png](/images/exploded-user-nestedgroup-role-permission-2.png)

> Abstracting permission assignments in this way simplifies security administration.

[Earlier](#the-landscape) we mentioned parts of the pipeline reside in Cloud Identity (**users** and **groups**) and others in GCP (**roles** and **permissions**).

The overlap of the two Google platforms shows how Component groups live in Cloud Identity but are referenced in GCP IAM.

![auth-pipeline-with-domains.png](/images/auth-pipeline-with-domains.png)

## Pipeline Administration

The Authorization Pipeline model has three main administrative functions:

- [User Management](#user-management) (Cloud Identity)
- [Access Configuration](#access-configuration) (Cloud Identity)
- [IAM Mapping](#iam-mapping) (GCP IAM)

![auth-pipeline-with-domains-and-functions-3.png](/images/auth-pipeline-with-domains-and-functions-3.png)

### User Management

**User Management** involves assigning users to one or more [Member groups](#member-groups).  Most day-to-day administration happens here.

Without this step, a user could successfully login to the [GCP Cloud Console](https://cloud.google.com/) but would lack the authorization to access a GCP resource.  

### Access Configuration

The design and maintenance of nested groups comprise **Access Configuration**.

If an application needs to use a new GCP component (Cloud IoT, for example), we would create new [Component groups](#component-groups). The appropriate Member groups are then added as members of these additional groups.

### IAM Mapping

**IAM Mapping** is the process of granting roles to Component groups. The mapping process binds one or more roles to a Component group.  

This step, along with the Member group assignments, enables the authorization flow between users and permissions.

In our Cloud IoT example, we would assign the required Cloud IoT-related IAM roles to the new Component groups to complete the mapping.

> The scope of IAM Mapping is at the project level.

## A Real-world Example

Now that you know how the model works, let’s look at some actual Authorization Pipelines.

The users will remain generic, and the role permissions are just too many to list. But group and role relationships are the key to understanding authorization flow.

### Application and Environment

We'll be looking at authorization for the Development environment of the CDH application.

**cdh-dev** is one of the GCP projects used for CDH development.  

### Pipelines

The major user categories in development work are **Administrators** and **Developers**.

#### Administrator Pipeline

![example-admin-1.png](/images/example-admin-1.png)

**cdh-admin-dev** is the Member group for this pipeline.  

Because Big Query, Compute, and Storage components are used in this project, the Component groups used by the Administrator pipeline are:

- **cdh-dev-bq-admin**
- **cdh-dev-compute-admin**
- **cdh-dev-storage-admin**

Each Component group is bound through [IAM Mapping](#iam-mapping) to the appropriate role(s) for administrative functions.

Notice the **cdh-admin-dev** group has only a single member.  As a best practice, we minimize the number of people with Administrator access.

#### Developer Pipeline

![example-developer-1.png](/images/example-developer-1.png)

The Member group of the Developer pipeline is **cdh-developer-dev**.  

It is built on the same model, but has some key differences:

- There are currently 19 accounts doing development work compared to a single admin user.  As developers join or exit the CDH project, the **cdh-developer-dev** group membership is updated to reflect who requires access to the **cdh-dev** project resources.
- While the Administrator pipeline contained only admin-level Component groups, this pipeline has a mixture of group access levels (**user**, **poweruser**, **admin**).  The default access level for non-admin users is **user**, but the model allows for the flexibility to grant more powerful roles.

In this development project, Developers needed to do more than just Big Query read-only access.  Adding **cdh-developer-dev** to the **cdh-dev-bq-poweruser** component group gives them the permissions of the **Bigquery Data Editor** (read/write) and **Bigquery Job User** roles.

Because they needed to do privileged **Storage** actions, we granted **cdh-dev-storage-admin** membership to **cdh-developer-dev**. This is the same access level as a project administrator.

We allow this elevated access in **development** environments.  For **production** environments, Developer access is restricted by default to the **user** level.

## CONCLUSION

Conclusion goes here.

## See Also

[Google IAM Quick Reference]({{< relref "Google-IAM-Quick-Reference.md" >}})

jim - jamescaja
