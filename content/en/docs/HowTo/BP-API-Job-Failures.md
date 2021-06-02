---
title: BP API Monitoring
weight: 10
---
There are currently 2 control-m jobs that monitor the API located at Brierley for loyalty lookup of members:

These jobs from edg11 and from the ecomm machine:

> cmhlpdlakedg11.**expressco.com** as sa-martech-feeds
>
> cmhlpdlakapp01.**ecomm.local** as sa-cdh-ecomm

The scripts run from the same location on both machines and the installs are mirrors of each other.  The commands are:

 ```sh
/apps/monitoring/brierley_api/executeTest.sh \
-j /apps/monitoring/brierley_api/brierley.jar \
-p /apps/monitoring/brierley_api/brierley_prod.properties \
 -l /apps/logs/monitoring/brierley_api
```

These jobs connect to the BP API using a jar we built, it does a lookup by loyalty id on their API and just parses the first name and last name to ensure we are getting data back.  The information is stored in:

`/apps/monitoring/brierley_api/brierley_prod.properties`

They run every 10 minutes in a loop.  If a job failes, it will retry up to 2 times before failing the job.  If this job fails, you can try loading the brierley_url from /apps/monitoring/brierley_api/brierley_prod.properties on your Windows VM to see if it will load.  Loading from your laptop may timeout due to firewall issues.  The output of the control-m job will supply the error messages that should give you an idea of what the issue is. 

If you pull up the url, you should get a page like below, if not then there is an issue with the API and it should be escalated to BP that the API is not responding.

Here is what the page should look like on a success, this just tests that we are able to get to the API, it does not test any functionality like the control-m jobs do.

![LWIntegrationService_Service.png](/images/LWIntegrationService_Service.png)

Here is an example of what a failure will look like in control-m, the error will change as to why it is failing, either cannot connect or received the wrong data:

![LWIntegrationService_Service-failure.png](/images/LWIntegrationService_Service-failure.png)
