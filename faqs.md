---
description: >-
  Generally asked questions about RudderStack - including setup, sources,
  destinations, and other features.
---

# FAQs

This section aims to address the queries and issues you might encounter while using RudderStack.

{% hint style="success" %}
**To quickly find the solution you are looking for, we recommend using the Contents panel on the right hand side of this guide to quickly navigate to the section you are looking for.** 

**Alternatively, you can use the search feature at the top-right hand side of this page with the relevant keywords to look for solutions on a specific topic.**
{% endhint %}

{% hint style="info" %}
If you come across any issue that is not listed in this guide, feel free to start a conversation on our [**Slack**](https://resources.rudderstack.com/join-rudderstack-slack) channel.
{% endhint %}

## Installation and Setup

#### 1. How do I quickly get started with RudderStack?

To quickly get started, you can [**sign up for RudderStack**](https://app.rudderstack.com/). Here, you can configure your sources and destinations and start building your data pipelines in no time.

#### 2. What is a Data Plane URL? Where do I get it?

Simply put, the Data Plane URL is used to connect to the RudderStack backend for routing and processing your events.

{% hint style="info" %}
 To get the **Data Plane URL**: ****

* If you're using the **open-source** version of RudderStack, you are required to set up your own data plane by [**installing and setting up RudderStack**](get-started/installing-and-setting-up-rudderstack/) in your preferred environment. 
* If you're using the **enterprise** version of RudderStack, please contact us for the data plane URL with the email ID used to sign up for RudderStack.
{% endhint %}

#### 3. To get started with RudderStack on my local machine, is it mandatory to get the workspace token from RudderStack dashboard?

The workspace token allows you to use the RudderStack-hosted control plane. It is a unique identifier for your configuration settings which the RudderStack can fetch to track your instrumentations.

#### 4. Can I self-host RudderStack?

Yes, you can use the [**RudderStack Config Generator**](get-started/config-generator.md) to self-host the control plane and configure your sources and destinations. Refer to the Config Generator section below for more information/FAQs.

#### 5. While running `git submodule update`, I get the following error:

```c
"Please make sure you have the correct access rights and the repository exists.
fatal: clone of 'git@github.com:rudderlabs/rudder-transformer.git' into submodule path '/home/ubuntu/rudder-server/rudder-transformer' failed
Failed to clone 'rudder-transformer'. Retry scheduled.
Cloning into '/home/ubuntu/rudder-server/rudder-transformer'...
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository."
```

Verify if the SSH keys are correctly set in your GitHub account as they are used when cloning using the git protocol. For more information, refer to [**this thread**](https://stackoverflow.com/questions/25957125/git-submodule-permission-denied)**.**

### Docker

#### 1. Is there any recommended size for the EC2 instance? I am running a self-hosted Docker setup.

A **c4.xlarge** or **c4.2xlarge** machine should work just fine for your setup.

#### 2. I'm running RudderStack in Docker on a GCP VM instance. I upgraded the instance to have more CPU and now the RudderStack container is stuck on this message: 

```c
sh -c '/wait-for db:5432 -- /rudder-server'
```

This message indicates that the RudderStack server is waiting on the PostgreSQL database dependency to be up and running. Please verify if your PostgreSQL container is up.

## RudderStack Server

#### 1. How do I check the status of the RudderStack data plane?

To check the status of the data plane, run the following command:

```bash
CURL <DATA_PLANE_URL>/health
```

A sample command would look something like:

```c
CURL https://hosted.rudderlabs.com/health
```

#### 2. How many events can a single RudderStack node handle?

The number of events that a single RudderStack node can handle will depend on the destinations that you are sending the event data to. It also depends on the transformations that you are running. 

However, here are some ballpark figures:

* **Dumping to S3** - Approximately 1.5K events/sec
* **Dumping to Warehouse** - Approximately 1K events/sec 
* **Dumping to Warehouse + a couple of cloud destinations** - Approximately 750 events/sec 

{% hint style="info" %}
Please note that these are conservative numbers. A single RudderStack node can handle **5x**+ event load at the peak; just that those events will get cached locally and then drained as per the regular throughput.
{% endhint %}

#### 3. How can I speed up the number of events sent to a destination?

There is a [**config variable**](https://github.com/rudderlabs/rudder-server/blob/403adb3576eb57b65dcab3111d1e40caa99ef2af/config/config.toml#L123) to configure the number of workers that send data to destinations. The default value is 64, which itself is an aggressive number. You can increase the number of workers, but some destinations generally throttle the number of requests per account.

#### 4. How I can know the number of events that are being sent to a destination?

You can login to your PostgreSQL database and check the tables and the number of rows in each table. This should give you a rough idea of the number of events that are being sent.

#### 5. My rudder-server setup keeps creating a new database automatically. What could be the reason?

This is a possibility if you have changed your workspace token. Also, ensure that the RudderStack server is running on the latest version.

#### 6. Do I need to change the data plane URL associated with the cloud-hosted RudderStack to my self-hosted data plane?

No, you need not change the URL. As long as your self-hosted data plane has the same workspace token, the RudderStack-hosted control plane will use your data plane for processing events.

#### 7. While trying to start rudder-server, I get the following error:

```text
backend_1 | 2021/06/09 08:12:14 notifying bugsnag: During db.vlog.open: Value log truncate required to run DB. This might result in data loss 
backend_1 | 2021/06/09 08:12:14 bugsnag.Notify: bugsnag/payload.deliver: invalid api key: '' 
backend_1 | 2021/06/09 08:12:14 bugsnag/sessions/publisher.publish invalid API key: '' 
backend_1 | panic: During db.vlog.open: Value log truncate required to run DB. This might result in data loss [recovered] 
backend_1 | panic: During db.vlog.open: Value log truncate required to run DB. This might result in data loss [recovered] 
backend_1 | panic: During db.vlog.open: Value log truncate required to run DB. This might result in data loss
```

Check for the folder `/tmp/badgerdbv2` and delete it. This should resolve the issue and you should be able to start rudder-server.

## Config Generator

#### 1. How do I self-host the UI configuration?

For self-hosting the UI, you can use the [**RudderStack Config Generator**](https://github.com/rudderlabs/config-generator). 

Note that the open-source config-generator will only generate the source-destination configurations which are required by RudderStack. 

{% hint style="warning" %}
The self-hosted control plane \(UI\) does not support features like **Transformations** and **Live Events Debugger**, which are included for free in the [**RudderStack-hosted control plane**](https://app.rudderstack.com).
{% endhint %}

#### 2. I am using the RudderStack Config Generator to generate the `workspaceConfig.json` file. But when I import this file, I get an error: 

#### "**TypeError: Cannot read property 'name' of undefined"**. What should I do?

This issue can occur when you have some old data left in your browser's local storage. Use the latest version of the Config Generator after clearing your browser cache and local storage. In case it still does not work, feel free to contact us.

## Transformations

#### 1. How do I add custom user transformations in RudderStack?

RudderStack lets you implement your own custom transformation functions that leverage the event data to implement specific use-cases based on your business requirements. To add custom transformations in RudderStack, follow this [**guide**](adding-a-new-user-transformation-in-rudderstack/).

#### 2. How do transformations handle batching? The transformation functions are given a list of events, but the events are also pushed out in real-time. What's the logic behind that?

The batching is done on a per end-user level. All the events from a given end-user are batched and then sent to the transformation function. The batching process is controlled via the following three parameters in [**`config.yaml`**](https://github.com/rudderlabs/rudder-server/blob/master/config/config.yaml)\( or **`config.toml`** in case of older RudderStack deployments\):

* `processSessions = False` \(make it `True` for batching\) 
* `sessionThresholdEvents = 100` 
* `sessionInactivityThresholdInS = 120` 

The events from an end-user are batched till we have 100 events or 120 seconds of inactivity since the last event. This list is then passed to the transformation function.

#### 3. How do transformations affect the pipeline? If RudderStack is connected to the database and transformation is used to enrich the data, will it slow everything down?

There is parallelism in calling the transformation function, so ideally it should not slow the system. However, if you have a really slow transformation, you can increase the number of transformation workers by tweaking `numTransformWorker` in [**`config.yaml`**](https://github.com/rudderlabs/rudder-server/blob/master/config/config.yaml).

#### 4. Is there a configuration or setting to group together events to minimize the number of database calls?

The number of user events that are batched together can be configured with `sessionThresholdEvents` and `sessionInactivityThresholdInS`in [**`config.yaml`**](https://github.com/rudderlabs/rudder-server/blob/master/config/config.yaml). The higher the number, the longer the events are grouped into a session. Note that that these will increase the memory footprint proportionally.

#### 5. Can I apply a transformation on a source configured in RudderStack?

Currently, transformations can only be configured and used for destinations. If you want to write some custom logic specific to the source, you can get the source ID in the transformation function and use it to include the logic. For more information, refer to this [**doc**](https://docs.rudderstack.com/adding-a-new-user-transformation-in-rudderstack#accessing-metadata).

## RudderStack Cloud

#### 1. Can I change my workspace name?

Unfortunately, your workspace name is not changeable currently. We are planning to include this feature in our future releases.

#### 2.1 How do I see the logs or data that is sent to my destination?

#### 2.2 I see a few events that show up in the live stream but do not reach the destination. What is the best way to debug this?

To view the data or events that are sent to your destination, you can use the **Live Events** tab on your destination page.

#### 3. Do I need to change the data plane URL associated with the cloud-hosted RudderStack to my self-hosted data plane?

No, you need not change the URL. As long as your self-hosted data plane has the same workspace token, the RudderStack-hosted control plane will use your data plane for processing events.

#### 4. How can I switch from open-source RudderStack to RudderStack Cloud and vice-versa? Will there be a lot of work involved?

Switching between open-source RudderStack and RudderStack Cloud is quite straightforward. Replace the URL of your self-hosted data plane to the RudderStack-hosted data plane URL. You can use the same sources and destinations as before - all you need to do is just change the URL to where the events are sent.

#### 5. Does RudderStack have any settings for consent management?

Currently, we do not have any solutions for consent management. We are in the process of implementing some key GDPR features. In the meantime, you can filter your events using our [**Transformations**](adding-a-new-user-transformation-in-rudderstack/) feature or stop sending events to the users who do not give their consent.

## Integrations

### SDKs

#### 1. I want to use the RudderStack JavaScript SDK to track impressions in an eCommerce site, such as product impression. How can I send the impression data in batches? I could not find the `batch` method in the SDK.

You should use the `track` method in the SDK instead. For JavaScript SDK's `track` method parameters specific to eCommerce, you can refer our Google Analytics [**Enhanced eCommerce**](https://docs.rudderstack.com/destinations/google-analytics-ga#enhanced-e-commerce) guide.

#### 2. Is Shopify compatible as a data source for Rudderstack?

Currently we do not have an integration for Shopify as an event stream source. However, we do have users that integrate the JavaScript SDK into their Shopify sites. In some cases, they even do it through Google Tag Manager.

### Cloud Extract Sources

#### 1. While trying to set up the Zendesk Chat Cloud Extract source, I get the following error:

```c
""Unauthorized"",""description"": The server could not verify that you are authorized to access the requested resource.""
```

Try editing the Zendesk Chat source and reauthorizing it again. If it still is unsuccessful, please contact us.

#### 2. When syncing data from Cloud Extract sources, how does RudderStack determine the new events? Is this data kept in RudderStack or S3?

RudderStack does not persist any data on its own. Rather, it fetches the data from the source based on the last timestamp till it was extracted.

### Destinations

#### 1. Would a destination connected with a source work if it is connected to a new source?

Yes, you can use the same destination without any problem.

#### 2. How do I see the logs or the data that is sent to my destination?

To view the data or events that are sent to your destination, you can use the **Live Events** tab on your destination page.

#### 3. I would like to send events to Mixpanel via RudderStack. However, I would like to set a filtering condition on the source events before routing them to Mixpanel. How do I go about doing this?

You can use [**RudderStack Transformations**](adding-a-new-user-transformation-in-rudderstack/) to set custom logic on your events before sending them to Mixpanel.

#### 4. I am seeing a `Message type not supported` error. What does this mean?

This error is being returned from the RudderStack Transformer. It means that for a particular destination, the event type that is trying to be sent, is not supported. For example, Salesforce only supports `identify` events. Therefore, if a `track` call is sent to Salesforce, the `Message type not supported` error will be returned. This error does not affect any other events and is harmless. However, a simple user transformation can be written to filter out these events so you will no longer see this error. Read [these docs](https://docs.rudderstack.com/adding-a-new-user-transformation-in-rudderstack) on how to create a user transformation for a destination.

### Warehouse Destinations

#### 1. How to force RudderStack to push all the data to a data warehouse in real-time with no delay? During the implementation, it would be better to see how the data is collected in real-time, rather than 30 minutes later.

You can override the UI set sync frequency by setting `warehouseSyncFreqIgnore` to true in [**`config.yaml`**](https://github.com/rudderlabs/rudder-server/blob/master/config/config.yaml) \(or `config.toml`, in case you have an older RudderStack deployment\). You can set your desired frequency by changing the `uploadFreqInS` parameter.

#### 2. I am using Rudderstack to mirror my source tables to PostgreSQL. I have all of the data in the S3 staging folders. But RudderStack doesn't create the corresponding PostgreSQL tables when I press on 'sync'. What do I do?

* Firstly, make sure you have set up the [**required user permissions**](https://docs.rudderstack.com/data-warehouse-integrations/postgresql#setting-postgresql-user-permissions) ****for PostgreSQL.
* Then, check the status of the sync in the [**RudderStack dashboard**](https://app.rudderstack.com/syncs).
* Check if the database is accessible by whitelisting all the RudderStack IPs listed [**here**](https://docs.rudderstack.com/data-warehouse-integrations/warehouse-faqs#which-ips-should-be-whitelisted).
* Ensure that all the security group policies for S3 are set as specified [**here**](https://docs.rudderstack.com/destinations/storage-platforms/amazon-s3#permissions).

#### 3. When sending data into a data warehouse, how can I change the table where this data is sent?

By default, RudderStack sends the data to the table / dataset based on the source it is connected to. For example, if the source is Google Tag Manager, RudderStack sets the schema name as **`gtm_*`**.

You can override this by setting namespace in the destination settings as shown:

![](.gitbook/assets/mssqlconnection%20%281%29.png)

#### 4. I am trying to set `warehouseSyncFreqIgnore = true` to have a real-time sync with BigQuery but I can't find the `config.yaml` file. How can I do this using the Docker setup?

You can do so by setting this value via the following ENV variables:

* `RSERVER_WAREHOUSE_WAREHOUSE_SYNC_FREQ_IGNORE`
* `RSERVER_WAREHOUSE_UPLOAD_FREQ_IN_S`

#### 5. I'm looking to send data to my data warehouse through RudderStack and I'm trying to understand what data is populated in each column. How do I go about this?

You can refer to the [**RudderStack Warehouse Schema**](data-warehouse-integrations/warehouse-schemas.md) documentation for details on how RudderStack generates the schema in the warehouse.

#### 6. I am trying to load data into my BigQuery destination and I get this error:

```c
backend_1 | {Location: “”; Message: “Cannot read and write in different locations: 
source: US, destination: us-central1""; Reason: “invalid”}"
```

Make sure that both your BigQuery dataset and the bucket have the same region. For more information, refer to the [**BigQuery documentation**](https://cloud.google.com/bigquery/docs/locations#data-locations).

#### 7. When piping data to a BigQuery destination, I can set the bucket but not a folder within the bucket. Is there a way to put Rudderstack data in a specific bucket folder?

Yes, you can set the desired folder name in the **Prefix** input field while configuring your BigQuery destination.

## RudderStack Pro / Enterprise

#### 1. I don't want to configure my API keys and secrets with RudderStack's control plane. But I want to use its features like Transformations. How can I do this?

RudderStack lets you fill in the values with variable names. These variables should be prepended with `env.`. You can populate these secrets as environment variables and run the data plane.

Suppose you are configuring Amazon S3 as a destination but you don't want to enter the AWS access key credentials in the destination settings. Fill the value with a placeholder that starts with `env.`  It should look like this `env.MY_AWS_ACCESS_KEY`. Then set the value of the environment variable `MY_AWS_ACCESS_KEY`while running the data plane.

## RudderStack Failover, Hardening and Security

#### 1. What cloud infrastructure is the RudderStack hosted solution running on? Do you have failover to alternate availability zones?

RudderStack's hosted solution is running on AWS EKS with the cluster spanning 3 availability zones \(east-1a, east-1b, east-1c\).

#### 2. What sort of hardening is in place to ensure up-times with a single RudderStack node?

* At the infrastructure layer, RudderStack runs on a multi-availability zone EKS cluster. So hardware failures, if any, are handled by Kubernetes by relocating pods.
* At an application level, RudderStack operates in either of the following 3 modes:
  * **Normal** mode, where everything is normal and there are no issues.
  * If for some reason the system fails \(e.g. because of a bug\), it enters the **Degraded** mode, where RudderStack processes incoming requests but doesn't send it to destinations. 
  * If the system continues to fail to process the data \(e.g. internal database corruption\), it enters the **Maintenance** mode where we save the previous state \(which can be debugged and processed\) and start from scratch - still receiving requests. 
* All of RudderStack's SDKs also have failure handling. They can store events in local storage and retry on failure.
* RudderStack provides isolation between the data and control planes. For example, if the control plane \(where you manage the source and destination configurations\) goes offline, the data plane continues to operate.

All this is done to ensure that RudderStack can always receive events, and no events are lost.

#### 3. Would adding an additional node to RudderStack cause an outage, and if so what is the expected downtime? How long would it take to recover from backup?

Adding a new node requires a bit of downtime. However, we have built RudderStack it to minimize this downtime as much as possible. When a new node is added, we need to re-balance users across nodes \(to keep event ordering\). While the re-balancing is happening \(can take a few minutes\), RudderStack does not send events to downstream destinations, but continues to receive events so that your SDKs won't see any failures \(ignoring the small ELB switch over time\). Also, the SDKs have local caching capability & retries built-in. So even if they see a failure, no events are lost.

## Retry Behavior

#### 1. How does RudderStack handle retries for failed events in case of destination failure?

Sometimes, the downstream destination can be unavailable or send a failure code for a variety of reasons. RudderStack retries sending the user events depending on the type of failure:

| Failure Code | Retry Behavior |
| :--- | :--- |
| 5XX | Retry for a time window of 3 hours with exponential backoff and a minimum of 3 times. |
| 429 | Retry for a time window of 3 hours with exponential backoff and a minimum of 3 times. |
| 4XX | Retry for a minimum of 3 times without any backoff . |

The above behavior is configurable via config variables in [**`config.yaml`**](https://github.com/rudderlabs/rudder-server/blob/master/config/config.yaml).

```c
[Router]
retryTimeWindowInMins = 180
minRetryBackoffInS = 10
maxRetryBackoffInS = 300
maxFailedCountForJob = 8
```

{% hint style="warning" %}
Note that if a user event fails, the other events are not sent until the failed event is successfully sent or aborted \(as per above behavior\). This is to ensure event ordering for all events of a single user.
{% endhint %}

## Throttling Behavior

#### 1. Some destinations have limits on the number of events they accept. How does RudderStack handle this?

Some downstream destinations have limits on the number of events they accept at an account or user/device level. RudderStack tries to throttle the API requests as per the destination's limits.

Some examples are:

* [**Customer.io**](https://customer.io/docs/api/#api-documentationlimits): 
* \*\*\*\*[**Amplitude Upload Limit** ](https://help.amplitude.com/hc/en-us/articles/360032842391-HTTP-API-V2#upload-limit)

These limits can also be configured using config variables in [**`config.yaml`**](https://github.com/rudderlabs/rudder-server/blob/master/config/config.yaml) or using environment variables as described in comments [**here**](https://github.com/rudderlabs/rudder-server/blob/master/config/config.yaml#L1-L32).

```c
# The following configuration throttles request to Amplitude at 1000 req/s for the account 
# and 10 req/s for individual user/device  

[Router.throttler.AM]
limit = 1000
timeWindowInS = 1
userLevelThrottling = true
userLevelLimit = 10
userLevelTimeWindowInS = 1
```

## Contact Us

If you come across any issue that is not listed in this guide, feel free to start a conversation on our [**Slack**](https://resources.rudderstack.com/join-rudderstack-slack) channel.

