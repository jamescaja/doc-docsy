---
title: Create GCP Service Accounts
date: 2021-05-21
weight: 10
tags:
  - security
---
## Overview

intro text CHANGING THIS

## Commands

### Create Service Account and Key

```sh
gcloud iam service-accounts create sa-dsa-automation \ 
--display-name=sa-dsa-automation \
--description="SA account for DSA automation tasks"

gcloud iam service-accounts keys create \
--iam-account sa-dsa-automation@exp-dsa-dev.iam.gserviceaccount.com \
sa-dsa-automation.json
```

### Assign IAM roles to Service Account

```sh
gcloud projects add-iam-policy-binding exp-dsa-dev \
--member="serviceAccount:sa-dsa-automation@exp-dsa-dev.iam.gserviceaccount.com" \
--role="roles/bigquery.dataEditor"

gcloud projects add-iam-policy-binding exp-dsa-dev \
--member="serviceAccount:sa-dsa-automation@exp-dsa-dev.iam.gserviceaccount.com" \
--role="roles/compute.instanceAdmin.v1"

gcloud projects add-iam-policy-binding exp-dsa-dev \
--member="serviceAccount:sa-dsa-automation@exp-dsa-dev.iam.gserviceaccount.com" \
--role="roles/compute.storageAdmin"

gcloud projects add-iam-policy-binding exp-dsa-dev \
--member="serviceAccount:sa-dsa-automation@exp-dsa-dev.iam.gserviceaccount.com" \
--role="roles/compute.networkUser"

gcloud projects add-iam-policy-binding exp-dsa-dev \
--member="serviceAccount:sa-dsa-automation@exp-dsa-dev.iam.gserviceaccount.com" \
--role="roles/iam.serviceAccountUser"
```

## See Also

[Google IAM Quick Reference]({{< relref "Google-IAM-Quick-Reference.md" >}})
