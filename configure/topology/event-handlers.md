---
description: Automate alerts and actions based on events
---

# Event handlers

## Overview

Event handlers can be attached to a StackState view to [send alerts](../../use/health-state-and-alerts/send-alerts.md) and trigger actions in response to health state change events generated within the view.

To trigger an alert or action, the event handler will run an [event handler function](event-handlers.md#event-handler-functions). This is set in the StackState UI **Events Settings** &gt; **ADD NEW EVENT HANDLER** dialogue as **Run event handler**.

![Add an event handler](../../.gitbook/assets/v42_event_handlers_tab.png)

## Event handler functions

Event handlers listen to events generated within a view. When the configured event type is generated, the configured event handler function is run to [send an alert](../../use/health-state-and-alerts/send-alerts.md) or trigger an action in a system outside of StackState. For example, an alert handler function could send an email or make a POST to a webhook URL. A number of default event handler functions are included out of the box with StackState, or you can [create your own custom event handler functions](event-handlers.md#create-a-custom-event-handler-function).

StackState ships with the following event handler functions:

| Event handler function | Description |
| :--- | :--- |
| **Email** | Sends details of a health state change event using the [configured SMTP server](configure-email-alerts.md).. |
| **HTTP webhook POST** | Sends an HTTP webhook POST request to the specified URL. |
| **Slack** | Sends an alert message to the configured Slack webhook URL. The message can contain detailed content on the trigger event and possible root cause. See [how to create a Slack webhook \(slack.com\)](https://api.slack.com/messaging/webhooks). |
| **SMS** | Sends details of a health state change event using MessageBird. |

{% hint style="info" %}
Some of the event handler functions above will be installed as part of a StackPack. A full list of the event handler functions available in your StackState instance can be found in the StackState UI, go to **Settings** &gt; **Functions** &gt; **Event Handler Functions**
{% endhint %}

## Create a custom event handler function

You can write your own custom event handler functions that react to state change events and use a plugin to send an alert to a system outside of StackState. To add a custom event handler function:

1. Go to **Settings** &gt; **Functions** &gt; **Event Handler Functions**.
2. Click **ADD EVENT HANDLER FUNCTION**. 
3. Enter the required settings:
   * **Name** - A name to identify the event handler function.
   * **Description** - Optional. A description of the event handler function.
   * **Parameters** - Parameters that are made available to the event handler function script. For details, see the section on [parameters](event-handlers.md#parameters) below.
   * **Supported Event Types** - The type of event\(s\) that the event handler can respond to. For details, see the section on [supported event types](event-handlers.md#supported-event-types) below.
   * **Async** - Select **On** for Slack event handlers, select **Off** for email, SMS or HTTP webhook event handlers. For details, see the section on [async on/off](event-handlers.md#async-on-off) below.
   * **Script** - The script run by the function. For details, see the sections below on the [available properties](event-handlers.md#available-properties) and [plugins](event-handlers.md#plugins) and how you can [add logging to a function](event-handlers.md#logging).
   * **Identifier** - Optional. A unique identifier \(URN\) for the event handler function.
4. Click **CREATE** to save the event handler function. 
   * The new event handler function will be listed on the **Event Handler Functions** page and available in the **Run event handler** drop-down when you [add an event handler](../../use/health-state-and-alerts/send-alerts.md#add-an-event-handler-to-a-view) that listens to one of the configured **Supported Event Types**.

![Add a custom event handler function](../../.gitbook/assets/v42_event_handler_functions.png)

### Parameters

An event handler function takes system and user defined parameters. System parameters are predefined parameters passed automatically to the script.

* The **view** system parameter is passed to every event handler function and provides details of the view the event handler is in. See [available properties](event-handlers.md#available-properties).
* An **event** user parameter is also required, this is the event stream that will be used to trigger the event handler function. See [available properties](event-handlers.md#available-properties).
* You can also add your own user parameters, these can then be entered in the **Add event handler** dialogue when you add an event handler to a view.

### Supported event types

One or more supported event types can be added for each event handler function. The supported event types are used to determine which event handler functions can be selected for each trigger event type when you [add an event handler to a view](../../use/health-state-and-alerts/send-alerts.md#add-an-event-handler-to-a-view). For example, an event handler function with no supported event types will not be included in the **Run event handler** list of the **Add event handler** dialogue for any trigger event type.

Up to three types of event can be chosen:

* **State change of entire view** - For functions that will react to a `ViewHealthStateChangedEvent`. These events are generated when the health state of the entire view changes.
* **State change of an element** - For functions that will react to a `HealthStateChangedEvent`. These events are generated when an element's own health state changes.
* **Propagated state change of an element** - For functions that will react to a `PropagatedHealthStateChangedEvent`. These events are generated when the propagated health state of an element changes. 

### Async on/off

Event handler functions can be written as async \(default\) or synchronous.

* With Async set to **On** the function will be run as [async](event-handlers.md#async-event-handler-functions-default). This gives the function access to the StackState script APIs.
* With Async set to **Off** the function will be run as [synchronous](event-handlers.md#synchronous-event-handler-functions-async-off).

#### Async event handler functions \(default\)

An async event handler function has access to the [StackState script APIs](../../develop/reference/scripting/script-apis/). This allows the function to make an HTTP request with a custom header using the [HTTP script API](../../develop/reference/scripting/script-apis/http.md) and gives access to the whole topology/telemetry. Currently only the **Slack** event handler function shipped with StackState will run as an async function. As a result, more extensive details around an event can be included in alerts sent to Slack, such as links to relevant data and a possible root cause.

Event handler functions use plugins to interact with external systems, not all plugins are available for use with async functions. See [plugins](event-handlers.md#plugins) below for further details. The [available properties](event-handlers.md#properties-for-async-functions) that can be retrieved from the default parameters are described below.

#### Synchronous event handler functions \(async Off\)

All event handler functions developed before StackState v4.2 and the email, SMS and HTTP webhook event handler functions shipped with StackState v4.2 run as synchronous functions. This places limitations on both the capability of what they can achieve and the number of functions that can be run in parallel.

Event handler functions use plugins to interact with external systems, synchronous event handler functions can use all available plugins. See [plugins](event-handlers.md#plugins) below for further details. The [available properties](event-handlers.md#properties-for-synchronous-functions) that can be retrieved from the default parameters are described below.

### Available properties

Different sets of properties are available for use in synchronous and async functions, these are described below.

#### Properties for synchronous functions

{% hint style="info" %}
The **view** and **event** properties described below can be used in **synchronous event handler functions**. If your function runs as async, see the [properties for use in async functions](event-handlers.md#properties-for-async-functions).
{% endhint %}

**View** properties return details of the view the event handler is in. Note that parameter name `view` or `scope` can be used, or an alias.

* `view.getName` - returns the name of the view.
* `view.getDescription` - returns the view description.
* `view.getQuery` - returns an STQL query of the view.
* `view.getIdentifier` - returns the globally unique URN value that identifies the view.

**Event** properties return details of a received event and vary for the different event types. Note that the default parameter name is`event`, this can be modified if you choose.

* `event.getCauseId` - returns the UUID of the event that triggered the health state change.
* `event.getTriggeredTimestamp` - returns the time \(epoch in ms\) at which the state change occurred. 
* `event.getNewStateRef` - returns an object representing the current state of the element. For HealthStateChangedEvents and  ViewHealthStateChangedEvents.
* `event.getOldStateRef` - returns an object representing the previous state of the element. For HealthStateChangedEvents and  ViewHealthStateChangedEvents.
* `event.getStateChanges` - returns the chain of elements through which the health state change propagated. For PropagatedHealthStateChangedEvents only.

#### Properties for async functions

{% hint style="info" %}
The **view** and **event** properties described below can be used in **async event handler functions**. If your function runs as synchronous, see the [properties for use in synchronous functions](event-handlers.md#properties-for-synchronous-functions).
{% endhint %}

**View** properties return details of the view the event handler is in. Note that parameter name `view` or `scope` can be used, or an alias.

* `view.name` - returns the view name.
* `view.description` - returns the view description.
* `view.query` -  returns an STQL query of the view.
* `view.identifier` - returns the globally unique URN value that identifies the view.

**Event** properties return details of a received event and vary for the different event types. Note that the default parameter name is`event`, this can be modified if you choose.

* `event.triggeredTimestamp` - returns the time  \(epoch in ms\) at which the state change occurred. 
* `event.causeId` - returns the UUID of the event that triggered the health state change.
* `event.newState` - returns the current state of the element. For HealthStateChangedEvents and  ViewHealthStateChangedEvents.
* `event.oldState` - returns the previous state of the element. For HealthStateChangedEvents and  ViewHealthStateChangedEvents.
* `event.stackElement` - returns the node ID of the element that has changed its state. For HealthStateChangedEvents only.
* `event.stateChanges` - returns the chain of elements through which the health state change propagated. For PropagatedHealthStateChangedEvents only.
* `event.viewHealthState` - returns the node ID of the health state object for the view that changed its state. For ViewHealthStateChangedEvents only

### Plugins

Event handler functions use plugins to send notifications to external systems. The plugins available for use in custom event handler functions are listed below. Note that not all plugins can be used in an async event handler function:

| Plugin | Async | Description |  |
| :--- | :--- | :--- | :--- |
| email | - | Sends an email using the [configured SMTP server](configure-email-alerts.md). `emailPlugin.sendEmail(to, subject, "body")` |  |
| HTTP webhook | - | Sends an HTTP POST request with the specified content to a URL. `webhookPlugin.sendMessage(url, "json")` |  |
| Slack | ✅ | Sends a notification message to a Slack webhook. The message can contain detailed content on the trigger event and possible root cause. `slackPlugin.sendSlackMessage(slackWebHookUrl, "message")` |  |
| SMS | - | Sends an SMS using MessageBird with the specified token. `smsPlugin.sendSMSMessage(token, "to", "message")` |  |

### Logging

You can add logging statements to an event handler function for debug purposes, for example, with `log.info("message")`. Logs will appear in `stackstate.log`. Read how to [enable logging for functions](../logging/enable-logging.md).

## See also

* [Enable logging for functions](../logging/enable-logging.md)
* [Send alerts when a health state changes](../../use/health-state-and-alerts/send-alerts.md)
* [StackState script APIs](../../develop/reference/scripting/script-apis/)
* [How to create a Slack webhook \(slack.com\)](https://api.slack.com/messaging/webhooks)
