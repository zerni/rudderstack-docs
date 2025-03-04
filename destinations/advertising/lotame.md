---
description: Step-by-step guide to set up Lotame as a destination in RudderStack
---

# Lotame

[Lotame](https://www.lotame.com/) is a popular data management and enrichment platform, used mainly for digital advertising. Its unique data solutions allow you to find new customers, improve engagement with them, and grow your revenue. 

RudderStack supports syncing Lotame's **BCP Pixel** and **DSP Pixel** through our `page` and `identify` call.

{% hint style="info" %}
* **BCP:** The BCP, or **Behavior Collection Point**, is a JavaScript snippet that is placed on a web page. It is responsible for capturing the relevant user activity.
* **DSP:** The DSP, or **Demand-Side Platform**, allows digital advertising inventory buyers to manage their ad exchange as well as data exchange accounts through a single interface.
{% endhint %}

## Getting Started

To enable sending data to Lotame, you will first need to add it as a destination to the source from which you are sending your event data. Once the destination is enabled, events from our SDK will start flowing to Lotame.

Before configuring your source and destination on the RudderStack, please verify if the source platform is supported by Lotame, by referring to the table below:

| **Connection Mode** | **Web** | **Mobile** | **Server** |
| :--- | :--- | :--- | :--- |
| **Device mode** | **Supported** | **Supported** | - |
| **Cloud mode** | - | - | - |

{% hint style="info" %}
To know more about the difference between Cloud mode and Device mode in RudderStack, read the [RudderStack connection modes](https://docs.rudderstack.com/get-started/rudderstack-connection-modes) guide.
{% endhint %}

Once you have confirmed that the platform supports sending events to Lotame, perform the steps below:

* From your [RudderStack dashboard](https://app.rudderlabs.com/),  select Lotame as a destination.

{% hint style="info" %}
Please follow our guide on [How to Add a Source and Destination in RudderStack](https://docs.rudderstack.com/how-to-guides/adding-source-and-destination-rudderstack) to add a source and destination in RudderStack.
{% endhint %}

* Give a name to the destination and select the source to connect to this destination. Then, click on **Next**. You should see the following screen:

![Lotame Destination Settings on the RudderStack dashboard](../../.gitbook/assets/image%20%2825%29.png)

You can provide multiple BCP and DSP URL templates in the settings dashboard by clicking on the **ADD MORE** option as seen above. Once specified, RudderStack includes these sources of image pixels in the web app. To provide the URLs, please use the below template:

```javascript
https://bcp.crwdcntrl.net/1/c={{clientId}}/b={{behavioralId}}
```

{% hint style="info" %}
RudderStack supports simple Handlebar expressions for the URLs. It uses the values provided by you in the **Map all the fields** section to replace the expressions, and the final URL as `src`.
{% endhint %}

## Adding Lotame to your mobile project

{% tabs %}
{% tab title="Android" %}
Please follow the steps below to add Lotame to your Android Project:

* Add the following `repository` to your `app/build.gradle` file.

```groovy
repositories {
    mavenCentral()
}
```

* Then, add the following `dependencies` in the same file:

```groovy
implementation 'com.rudderstack.android.sdk:core:1.+'
implementation 'com.rudderstack.android.integration:lotame:1.+'
```

* Finally, change the initialization of your `RudderClient` in your `Application` class, as shown in the snippet below:

```kotlin
val rudderClient = RudderClient.getInstance(
    this,
    WRITE_KEY,
    RudderConfig.Builder()
        .withDataPlaneUrl(DATA_PLANE_URL)
        .withFactory(LotameIntegrationFactory.FACTORY)
        .build()
)
```

{% hint style="warning" %}
If your Lotame URLs follow the HTTP protocol, you need to allow **`ClearTextTraffic`** for your app.  To do so, add **`android:usesCleartextTraffic="true"`** in the **`<application>`** tag of your app's `Android Manifest` file. After adding this, the file should look like the following:

```markup
<application
  ...
  android:usesCleartextTraffic="true"
  ...>
    <activity>
      ...
    </activity>
</application>
```
{% endhint %}
{% endtab %}

{% tab title="iOS" %}
* Open the `Podfile` of your project and add the following line

```ruby
pod 'Rudder-Lotame'
```

followed by 

```text
$ pod install
```

* Finally change the SDK initialization with the following

```objectivec
RSConfigBuilder *builder = [[RSConfigBuilder alloc] init];
[builder withDataPlaneUrl:DATA_PLANE_URL];
[builder withFactory:[RudderLotameFactory instance]];
[builder withLoglevel:RSLogLevelDebug];
[RSClient getInstance:WRITE_KEY config:[builder build]];
```
{% endtab %}
{% endtabs %}

## Identify

For each `identify` call, RudderStack will sync the DSP Pixels provided by you in the **DSP URL Settings**, in the destination configuration settings**.** As DSP Pixels need to be synced for every user only once in 7 days, RudderStack will sync the DSP Pixels in every `identify` call and if the same user remains active after 7 days, it syncs the same DSP Pixels in subsequent `page` calls once in every 7 days.

A sample `identify` call is as shown in the code snippet below:

```javascript
rudderanalytics.identify("12345", {
    name: "my-name",
    email: "name@domain.com",
    country: "India"
    }
);
```

The above identify call will sync the ****DSP Pixel, modify the template to place the `userId` in the source URL where required, and use it as `{{userId}}` in the template, as shown: 

```text
http://ab.cdef.com/getapi?http://sync.crwdcntrl.net/map/c={{clientId}}/rand={{random}}/tpid={{userId}}/tp={{clientIdSpace}}
```

RudderStack will replace the `{{userId}}` expression with the `userId` provided in the [`identify`](https://docs.rudderstack.com/getting-started/rudderstack-api-spec#identifypayload) call.We will also replace `{{random}}` with a random value. The rest of the expression's values \(`{{clientId}}` , `{{clientIdSpace}}` \) are to be provided in the mapping fields section, i.e. **Map all the fields** section in the Destination Settings, as covered in the Getting Started section. 

#### Important Notes

* If you mention `userId` in the URL template in the Destination Settings, RudderStack replaces it the with the ID from the `identify` call.
* RudderStack also replaces the value of the  `{{random}}` expression with a random integer.

## Page

For each `page` call, RudderStack will load the BCP ****Pixels. A `page` call with a payload having `userId` triggers the syncing of DSP Pixels once in every **7 days** too. So, a[`page`](https://docs.rudderstack.com/getting-started/rudderstack-api-spec#page-payload) call serves the purpose of loading BCP Pixels and syncing DSP Pixels.

A sample `page` call is as shown: 

```javascript
rudderanalytics.page();
```

## Screen

For each `screen` call, RudderStack will send `GET` requests for BCP ****Pixels. A `screen` call with a payload having `userId` triggers the syncing of DSP Pixels once in every **7 days** too. So, a[`screen`](https://docs.rudderstack.com/getting-started/rudderstack-api-spec#screen-payload) call serves the purpose of sending requests for BCP Pixels and syncing DSP Pixels.

A sample `screen` call is as shown: 

```objectivec
[[RSClient sharedInstance] screen];
```

## Sync Pixel Callback

To get a track of every sync Pixel call \(once in every 7 days\), you may register a function that will be executed when the syncing of DSP Pixels takes place from the SDK. 

{% tabs %}
{% tab title="Web" %}
Use the web SDK [`ready` A](https://github.com/rudderlabs/rudder-sdk-js#step-4-check-ready-state)[PI](https://github.com/rudderlabs/rudder-sdk-js#step-4-check-ready-state) to register the callback.  We execute the function provided in `LOTAME_SYNCH_CALLBACK` ****window object after calling the sync pixels.

An example of the above is as shown:

```javascript
rudderanalytics.ready(() => {
    window.LOTAME_SYNCH_CALLBACK = () => {
        rudderanalytics.track("synch lotame", {}, {
            integrations:{"All" : false, "S3": true}
        });
    }
});
```

The above callback triggers a call to RudderStack SDK's [`track` ](https://docs.rudderstack.com/getting-started/rudderstack-api-spec#track-payload)API which dumps the `track` call payload to a configured S3 destination in your RudderStack dashboard, each time the syncing of the DSP Pixels happens.
{% endtab %}

{% tab title="Android" %}
Use the `onIntegrationReady` method to register the `onSync` callback \(which will be called every time the DSP URLs are synced\).

You'll get notified for all the sync pixels through this callback. You'll receive the type of the pixel and the final compiled URL through the callback. 

```kotlin
rudderClient!!.onIntegrationReady("Lotame Mobile") {
    (it as LotameIntegration).registerCallback { urlType, url ->
        // urlType => "bcp", "dsp"
        // url => complete url with all values replaced
        println("LotameSync: $urlType : $url")
    }
}
```
{% endtab %}

{% tab title="iOS" %}
Register your callback to get notified when the pixel has been synced

```objectivec
[LotameIntegration registerCallback:^{
    // your custom code
}];
```
{% endtab %}
{% endtabs %}

## FAQ

### How and when does the syncing of DSP Pixels take place?

Syncing of DSP Pixels happens in each `identify` call, and once in 7 days for `page` calls having `userId` in payload. RudderStack stores the sync timestamp in storage. So, in every `page` call for web and in every `screen` call for mobile platform, RudderStack checks for the identified user \(i.e. payload having `userId`\) and the last syncing timestamp. If it is before 7 days or more, RudderStack automatically triggers the syncing. 

### How do we validate the Pixels on Mobile devices?

You can validate the Pixels in two ways on Mobile devices. 

1. You can register a [Sync Callback](lotame.md#sync-pixel-callback) with `rudderClient` and you'll get the type of the pixel which is getting fired along with the compiled URL with values replaced. We replace the `userId` and `advertisingId` automatically. For other values, you can use the settings on the [dashboard](https://app.rudderstack.com).
2. You can turn on `DEBUG` log in your `rudderClient` initialization. It'll also set the `DEBUG` logging in Lotame implementation. And you can check logs generated by the SDK. It'll help you to understand the steps taken by the SDK.

## Contact Us

If you come across any issues while configuring Lotame with RudderStack, please feel free to [contact us](mailto:%20docs@rudderstack.com). You can also start a conversation on our [Slack](https://resources.rudderstack.com/join-rudderstack-slack) channel; we will be happy to talk to you!

