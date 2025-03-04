---
description: Step-by-step guide to send event data from RudderStack to Google Ads
---

# Google Ads

[Google Ads](https://ads.google.com/) is Google's premier online advertising platform. It can be used for efficient marketing strategies such as product listing, service offerings, as well as activities such as conversion tracking. 

RudderStack's Google Ads SDK uses a global site tag \(`gtag.js`\) that is a JavaScript tagging framework, and an API that allows you to send event data to Google Ads.

## Getting Started

To enable sending data to Google Ads, you will first need to add it as a destination to the source from which you are sending the event data. Once the destination is enabled, events from our JavaScript SDK will start to flow to Google Ads. 

{% hint style="info" %}
You can use our SDK to send **Page Load Conversions** as well as **Click Conversions**. However, you can send this data only through our JavaScript SDK.
{% endhint %}

Before configuring your source and destination in RudderStack, please check whether the platform you are working on is supported by Google Ads by referring to the table below:

| **Connection Mode** | **Web** | **Mobile** | **Server** |
| :--- | :--- | :--- | :--- |
| **Device mode** | **Supported** | - | - |
| **Cloud mode** | - | - | - |

{% hint style="info" %}
To know more about the difference between Cloud mode and Device mode in RudderStack, read the [RudderStack connection modes](https://docs.rudderstack.com/get-started/rudderstack-connection-modes) guide.
{% endhint %}

Once you have confirmed that the platform supports sending events to Google Ads, perform the steps below:

* From your [RudderStack dashboard](https://app.rudderlabs.com/), add the source and select **Google Ads** as a destination.

{% hint style="info" %}
Please follow our guide on [How to Add a Source and Destination in RudderStack](https://docs.rudderstack.com/how-to-guides/adding-source-and-destination-rudderstack) to add a source and destination in RudderStack.
{% endhint %}

* Give a name to the destination and click on **Next**. You should then see the following screen:

![Connection Settings for Google Ads in RudderStack](../../.gitbook/assets/image%20%2840%29%20%281%29%20%281%29.png)

* Please enter the **Conversion ID** of your Google Ads account. 
* For `page` calls, you can also configure **Page Load Conversions** for multiple instances. In the **Conversion Label** input, provide the conversion label from Google Ads. Then, for the **Name** input provide the name of the `page` event that will be sent.
* For `track` calls, you can configure **Click Event Conversion**. ****In the **Conversion Label** input, provide the conversion label from Google Ads. Then, for the **Name** input provide the name of the `track` event that will be sent.
* Click on **Next** to finish the configuration. Google Ads will now be added and enabled as a destination in RudderStack.

## Page

You can make a `page` call with the conversion name to our RudderStack SDK for a **Page Load Conversion**. The SDK will send this data to Google Ads.

A sample `page` call is as shown: 

```text
rudderanalytics.page('page view');
```

## Track

You can make a track call with the conversion name to our RudderStack for a Click Event Conversion, which will in turn send the data to Google Ads.

RudderStack maps the `properties.value`, `properties.currency` or `properties.order_id` to the supported `value`, `currency`, and `transaction_id` fields of Google Ads, respectively. RudderStack also maps `properties.revenue` to `value`. 

A sample `track` call is as shown:

```text
rudderanalytics.track('track conversion', {
    value: 125,
    currency: 'INR',
    order_id: 'order_1'
});
```

## FAQs

### How do I get the Conversion ID?

You can get the conversion ID from your global site tag snippet. It should look something like `AW-123456789`.

### How do I get the Conversion Label for Google Ads?

You can find the value of the **Conversion Label** from your event snippet. The provided event snippet should look something like`send_to: 'AW-123456789/bpg3CMiIjLMBELXBp8wC'`. Enter the part after the `'/'`

## Contact Us

If you come across any issues while configuring Google Ads with RudderStack, please feel free to [contact us](mailto:%20docs@rudderstack.com). You can also start a conversation on our [Slack](https://resources.rudderstack.com/join-rudderstack-slack) channel; we will be happy to talk to you!

