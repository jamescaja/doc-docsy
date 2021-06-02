---
title: "gcloud Cheatsheet"
linkTitle: "gcloud Cheatsheet"
date: 2021-05-27
weight: 10
description: >
  Common gcloud SDK command reference.
---

## Overview

Overview text.

## Commands

### SQL

#### Stop PRIMARY instance

    gcloud sql instances patch [INSTANCE_NAME] --activation-policy NEVER

#### Promote read REPLICA to PRIMARY instance

    gcloud sql instances promote-replica [REPLICA_NAME]

#### Create a read REPLICA

```sh
gcloud sql instances create [REPLICA_NAME] \
--master-instance-name=[PRIMARY_INSTANCE_NAME] \
--region=[OTHER_REGION] \
--no-assign-ip \
--replica-type=READ
```

## See Also

[Google IAM Quick Reference]({{< relref "Google-IAM-Quick-Reference.md" >}})
