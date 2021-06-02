---
title: Deployment Process
weight: 10
---
## Overview of Responsibilities, Deliverables, and Deadlines

The following describes the flow of the deployment process.  

In general, a code review is held to review potential production changes.  The following week after changes have been identified for production deployment, tasks are completed by the assigned individual or group by the specified deadline.

Groups with responsibilities / action items:

- Development Team
- Development Manager
- Scrum Master
- DevOps Team

## Process

### Weekly Code Review

This scheduled meeting serves as a placeholder to review any submitted pull requests.  The Scrum Master and DevOps Team are scheduled to attend.

The meeting is typically cancelled if no pull requests have been submitted.

### Adhoc Code Reviews

The Development Team(s) are free to hold their own code review sessions without Scrum Master / DevOps Team attendance.  This might be needed, for example, if a pull request was not ready for submission by the Weekly Code Review but still must be deployed to production with the next Code Install.

Regardless of which review forum is used, the process must meet these deliverables by the documented deadlines. [All times are US-Eastern time zone]:

|Responsible|Task / Deliverable|
|---|---|
|Dev Team|Review pull request code.  Team who owns code is responsible for reviewing it.|
|Dev Team/Dev Mgr/Scrum Master|Determine which changes are planned for deployment the following week.|
|Scrum Master|Send email communicating all planned deployments for the following week.|

### Due: **Tuesday at 2:00 pm**

|Responsible|Task / Deliverable|
|---|---|
|Dev Team|Fully approved Pull Requests for all planned code deployments.|
|Dev Team|Create CRQ for each installation.|
|Dev Team|Provide necessary documentation (business justification, test results).  This documentation **MUST** be attached directly to the CRQ (Mandatory as of 6/2/2021).|

### Due: **Tuesday after approved pull request(s)**

|Responsible|Task / Deliverable|
|---|---|
|DevOps|Merge in the new code to the development branch(s) and create new release branch, deploy to core dev.|

### Due: **Tuesday at EOD**

|Responsible|Task / Deliverable|
|---|---|
|Dev Mgr|Approve CRQ(s)|

### Thursday Code Install

Executes after batch completion -  normally around 2pm.

|Responsible|Task / Deliverable|
|---|---|
|DevOps|Deploy release build(s) to production.|
|Dev Team|After deployment, verify production code was installed as expected.|
|Scrum Master|Send email communicating the completion status of each installation.|
