---
title: dhmesh-gcp
linktitle: dhmesh
weight: 20
---
Follow these instructions to deploy a **dhmesh-gcp** software change to production.

## Pre-Deployment

At this point, pull requests for all code changes scheduled for release have been submitted and approved.

Execute these steps to prepare the release for deployment.

### Create Release Branch

|Action (in BITBUCKET)|
| ----------- |
|Merge pull request(s) into DEVELOPMENT branch​​​​​​​|
|Create next RELEASE branch from DEVELOPMENT branch|

### Build Release Artifact and Deploy for DEV testing

#### Access the Dev Menu

|Action|Note|
| ----------- | ----------- |
|ssh to edge server|`ssh adm-jcarlisle@cmhlddlkeedg11.expdev.local`|
|Become the sa-devcustdatahub user|`sudo su - sa-devcustdatahub`|
|Run the menu|`/apps/devtools/env-control/run_controller.sh`|

#### Build/Deploy the release

|Action|Menu|Option|Note|
| ----------- | ----------- | ----------- | ----------- |
|Create release build|Administrative|build release||
|Deploy release build to DEV|Code|deploy|Use environment **dev**|
|Stage release build|Administrative|promote|Makes artifact available for prod deployment|

## Deployment

After the release has been tested, follow these steps to install into production on the day of deployment.

### Hold Batch

Log into control-m client from your local machine and select **Hold** for the following job in the current running schedule:

- **MT_END_OF_DAILY_PROCESSING**

This is a common job across the projects and can be held to stop processing for installs.

### Confirm Batch Job Completion

Once the schedule reaches MT_END_OF_DAILY_PROCESSING, confirm all its predecessor jobs are complete.

No batch processes should be running at this point and installs can begin.

### Deploy Release to Production

> **Before deploying**, run command `ps -ef | grep dhmesh | grep "-j"` to check for running feeds.
> 
> If it returns any result, **do not** run the deployment command until the dhmesh process(es) are finished.

> The commands below contain version number placeholders (`x.x`).
>
> Don't forget to replace them with the actual version number before running the command.

|Action|Command - from PROD (cmhlpdlakedg11)|
| ----------- | ----------- |
|Deploy release|`/apps/admin/run_deploy_prod.sh -r dhmesh -b release/x.x -v x.x`|

### Verify deployment information

Review output of `/apps/admin/run_deploy_prod.sh` command

### Release Batch

Log into control-m client from your local machine and select **Free** for the following job in the current running schedule:

- **MT_END_OF_DAILY_PROCESSING**

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
|Deploy PREVIOUS release|`/apps/admin/run_deploy_prod.sh -r dhmesh -b release/x.x -v x.x`|

where `x.x` is the previous release number.

### Verify deployment information

Review output of `/apps/admin/run_deploy_prod.sh` command

### Release Batch

Log into control-m client from your local machine and select **Free** for the following job in the current running schedule:

- **MT_END_OF_DAILY_PROCESSING**
