---
title: fsr
linktitle: fsr
weight: 30
---
Follow these instructions to deploy an **FSR** software change to production.

There are 2 types of FSR deployments:

- Application
- Integration

## Pre-Deployment

At this point, pull requests for all code changes scheduled for release have been submitted and approved.

Execute these steps to prepare the release for deployment.

### Create Release Branch

|Action (in BITBUCKET)|
| ----------- |
|Create next RELEASE branch from DEVELOPMENT branch|

## Deployment

After the release has been tested, follow these steps to install into production on the day of deployment.

### Deploy Release to Production

> **Note**
>
> The commands below contain version number placeholders (`x.x`).
>
> Don't forget to replace them with the actual version number before running the command.

|Action|Command - from PROD (cmhlpdlakedg11)|
| ----------- | ----------- |
|Deploy release|`/apps/admin/run_deploy_prod.sh -r tools -b release/x.x -v x.x`|

where `x.x` is the previous release number.

### Verify deployment information

## Post-Deployment

The deployment is complete.  Now time to tie up loose ends.

|Action (in BITBUCKET)|
| ----------- |
|Create pull request to merge RELEASE branch into MASTER branch|
|Tag commit with release number in MASTER branch|

## Rollback

After a new release is deployed to production, problems may arise that require the release to be rolled back to the previous version.

The rollback process mirrors the deployment process.  The only change is you must use the version number from the previous release.

To roll back a release:

### Hold Batch

Log into control-m client from your local machine and select **Hold** for the following job in the current running schedule:

- **MT_END_OF_DAILY_PROCESSING**

This is a common job across the projects and can be held to stop processing for installs.

### Confirm Batch Job Completion

Once the schedule reaches MT_END_OF_DAILY_PROCESSING, confirm all its predecessor jobs are complete.

No batch processes should be running at this point and installs can begin.

### Deploy Release to Production

|Action|Command - from PROD (cmhlpdlakedg11)|
| ----------- | ----------- |
|Deploy PREVIOUS release|`/apps/admin/run_deploy_prod.sh -r tools -b release/x.x -v x.x`|

where `x.x` is the previous release number.

### Verify deployment information

Review output of `/apps/admin/run_deploy_prod.sh` command


### Release Batch

Log into control-m client from your local machine and select **Free** for the following job in the current running schedule:

- **MT_END_OF_DAILY_PROCESSING**
