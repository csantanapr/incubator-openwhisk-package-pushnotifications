

# Using the Push package
[![Build Status](https://travis-ci.org/openwhisk/openwhisk-package-pushnotifications?branch=master)](https://travis-ci.org/openwhisk/openwhisk-package-pushnotifications)

The `/whisk.system/pushnotifications` package enables you to work with a push service.

It includes the following action and feed:

| Entity | Type | Parameters | Description |
| --- | --- | --- | --- |
| `/whisk.system/pushnotifications` | package | appId, appSecret  | Work with the Push Service |
| `/whisk.system/pushnotifications/sendMessage` | action | text, url, deviceIds, platforms, tagNames, gcmPayload, gcmSound, gcmCollapseKey, gcmDelayWhileIdle, gcmPriority, gcmTimeToLive, gcmSync, gcmVisibility, gcmStyleType, gcmStyleTitle, gcmStyleUrl, gcmStyleText, gcmStyleLines, apnsBadge, apnsCategory, apnsIosActionKey, apnsPayload, apnsType, apnsSound, fireFoxTitle, fireFoxIconUrl, fireFoxTimeToLive, fireFoxPayload, chromeTitle, chromeIconUrl, chromeTimeToLive, chromePayload, chromeAppExtTitle, chromeAppExtCollapseKey, chromeAppExtDelayWhileIdle, chromeAppExtIconUrl, chromeAppExtTimeToLive, chromeAppExtPayload | Send push notification to one or more specified devices |
| `/whisk.system/pushnotifications/webhook` | feed | events | Fire trigger events on device activities (device registration, unregistration, subscription, or unsubscription) and delivery status (Notification Sent and Notification Failure) on the Push service |
Creating a package binding with the `appId` and `appSecret` values is suggested. This way, you don't need to specify these credentials every time you invoke the actions in the package.

### Creating a Push package binding

While creating a Push Notifications package binding, you must specify the following parameters,

-  `appId`: The Bluemix app GUID.
-  `appSecret`: The Bluemix push notification service appSecret.

The following is an example of creating a package binding.

1. Create a Bluemix application in [Bluemix Dashboard](http://console.ng.bluemix.net).

2. Initialize the Push Notification Service and bind the service to the Bluemix application

3. Configure the [Push Notification application](https://console.ng.bluemix.net/docs/services/mobilepush/index.html).

  Be sure to remember the `App GUID` and the `App Secret` of the Bluemix app you created.


4. Create a package binding with the `/whisk.system/pushnotifications`.

  ```
  $ wsk package bind /whisk.system/pushnotifications myPush -p appId myAppID -p appSecret myAppSecret
  ```

5. Verify that the package binding exists.

  ```
  $ wsk package list
  ```

  ```
  packages
  /myNamespace/myPush private binding
  ```

### Sending push notifications

The `/whisk.system/pushnotifications/sendMessage` action sends push notifications to registered devices. The parameters are as follows:
- `text`: The notification message to be shown to the user. For example: `-p text "Hi ,OpenWhisk send a notification"`.
- `url`: An optional URL that can be sent along with the alert. For example: `-p url "https:\\www.w3.ibm.com"`.
- `deviceIds` The list of specified devices. For example: `-p deviceIds ["deviceID1"]`.
- `platforms` Send notification to the devices of the specified platforms. 'A' for apple (iOS) devices and 'G' for google (Android) devices. For example `-p platforms ["A"]`.
- `tagNames` Send notification to the devices that have subscribed to any of these tags. For example `-p tagNames "["tag1"]" `.
- `gcmPayload`: Custom JSON payload that will be sent as part of the notification message. For example: `-p gcmPayload "{"hi":"hello"}"`
- `gcmSound`: The sound file (on device) that will be attempted to play when the notification arrives on the device.
- `gcmCollapseKey`: This parameter identifies a group of messages
- `gcmDelayWhileIdle`: When this parameter is set to true, it indicates that the message will not be sent until the device becomes active.
- `gcmPriority`: Sets the priority of the message.
- `gcmTimeToLive`: This parameter specifies how long (in seconds) the message will be kept in GCM storage if the device is offline.
- `gcmSync`: Device group messaging makes it possible for every app instance in a group to reflect the latest messaging state.
- `gcmVisibility`: private/public - Visibility of this notification, which affects how and when the notifications are revealed on a secure locked screen.
- `gcmStyleType`: Specifies the type of expandable notifications. The possible values are `bigtext_notification`, `picture_notification`, `inbox_notification`.
- `gcmStyleTitle`: Specifies the title of the notification. The title is displayed when the notification is expanded. Title must be specified for all three expandable notification.
- `gcmStyleUrl`: An URL from which the picture has to be obtained for the notification. Must be specified for picture_notification.
- `gcmStyleText`: The big text that needs to be displayed on expanding a bigtext_notification. Must be specified for bigtext_notification.
- `gcmStyleLines`: An array of strings that is to be displayed in inbox style for inbox_notification. Must be specified for inbox_notification.
- `apnsBadge`: The number to display as the badge of the application icon.
- `apnsCategory`: The category identifier to be used for the interactive push notifications.
- `apnsIosActionKey`: The title for the Action key .
- `apnsPayload`: Custom JSON payload that will be sent as part of the notification message.
- `apnsType`: ['DEFAULT', 'MIXED', 'SILENT'].
- `apnsSound`: The name of the sound file in the application bundle. The sound of this file is played as an alert.
- `fireFoxTitle`: Specifies the title to be set for the WebPush Notification.
- `fireFoxIconUrl`: The URL of the icon to be set for the WebPush Notification.
- `fireFoxTimeToLive`: This parameter specifies how long (in seconds) the message should be kept in GCM storage if the device is offline.
- `fireFoxPayload`: Custom JSON payload that will be sent as part of the notification message.
- `chromeTitle`: Specifies the title to be set for the WebPush Notification.
- `chromeIconUrl`: The URL of the icon to be set for the WebPush Notification.
- `chromeTimeToLive`: This parameter specifies how long (in seconds) the message should be kept in GCM storage if the device is offline.
- `chromePayload`: Custom JSON payload that will be sent as part of the notification message.
- `chromeAppExtTitle`: Specifies the title to be set for the WebPush Notification.
- `chromeAppExtCollapseKey`: This parameter identifies a group of messages.
- `chromeAppExtDelayWhileIdle`: When this parameter is set to true, it indicates that the message should not be sent until the device becomes active.
- `chromeAppExtIconUrl`: The URL of the icon to be set for the WebPush Notification.
- `chromeAppExtTimeToLive`: This parameter specifies how long (in seconds) the message should be kept in GCM storage if the device is offline.
- `chromeAppExtPayload`: Custom JSON payload that will be sent as part of the notification message.

Here is an example of sending push notification from the *pushnotification* package.

1. Send push notification by using the `sendMessage` action in the package binding that you created previously. Be sure to replace `/myNamespace/myPush` with your package name.

```
wsk action invoke /myNamespace/myPush/sendMessage --blocking --result  -p url https://example.com -p text "this is my message"  -p sound soundFileName -p deviceIds "[\"T1\",\"T2\"]"
```

```
{
"result": {
"pushResponse": "{"messageId":"11111H","message":{"message":{"alert":"this is my message","url":"http.google.com"},"settings":{"apns":{"sound":"default"},"gcm":{"sound":"default"},"target":{"deviceIds":["T1","T2"]}}}"
},
"status": "success",
"success": true
}
```

### Firing a trigger event on Push activity

The `/whisk.system/pushnotifications/webhook` configures the Push service to fire a trigger when there is a device activity such as device registration / unregistration or subscription / unsubscription in a specified application

The parameters are as follows:

- `appId:` The Bluemix app GUID.
- `appSecret:` The Bluemix push notification service appSecret.
- `events:` Supported events are `onDeviceRegister`, `onDeviceUnregister`, `onDeviceUpdate`, `onSubscribe`, `onUnsubscribe`, `onNotificationSent`, `onNotificationFailure`.To get notified for all events use the wildcard character `*`.

The following is an example of creating a trigger that will be fired each time there is a new device registered with the Push Notifications service application.

1. Create a package binding configured for your Push Notifications service with your appId and appSecret.

  ```
  $ wsk package bind /whisk.system/pushnotifications myNewDeviceFeed --param appID myapp --param appSecret myAppSecret --param events onDeviceRegister
  ```

2. Create a trigger for the Push Notifications service `onDeviceRegister` event type using your `myPush/webhook` feed.

  ```
  $ wsk trigger create myPushTrigger --feed myPush/webhook --param events onDeviceRegister
  ```

### Using Push Package Locally.

You can use the push package actions and feed in your own openWhisk packages. For using it you have to download the Push package form the  [openwhisk-package-pushnotifications](https://github.com/openwhisk/openwhisk-package-pushnotifications) repository.

To create your own package follow the below steps,

1. Point to the `openwhisk-package-pushnotifications` location.
2. Create the package using `wsk package create package-name` command
3. Add action  using the following command,

  ```
  wsk action create actionName sendMessage.js -p appId your_AppId -p appSecret application_Secret -p text "message"
  ```

  You can add multiple parameters to sendMessage action.
  Check for the available parameters [here](#action parameters)

4. Create feed using the following command,

   ```
   wsk action create /myNamespace/yourPackageName/webhook webhook.js -a feed true
   ```

5. Create a trigger using the feed created above,

   ```
   wsk trigger create triggerName --feed /myNamespace/yourPackageName/webhook -p appId your_AppId -p appSecret application_Secret -p events onDeviceUnregister
   ```

  Output will be like this:

    ```
    {
    "response": {
                "name":"triggerName",
                "eventTypes":"onDeviceRegister",
                "url":"https://openWhiskAuth@openwhisk.ng.bluemix.net/api/v1/namespaces/myNamespace/triggers/triggerName"
                }
    }
    ```

6. We need to create a rule that will combine the trigger and the action created in previous steps.Create the rule using ,

   `wsk rule create --enable yourRule triggerName actionName `

7. Check the results in the `wsk activation poll`.
8. Register a device in your Bluemix application , you can see the `rule`,`trigger` and  `action` getting executed in the openWhisk [dashboard] (https://new-console.ng.bluemix.net/openwhisk/dashboard).
9. The action will send a push notification.


## Deploying Push Package using `installCatalog.sh`

1. git clone https://github.com/openwhisk/openwhisk-package-pushnotifications
2. cd openwhisk-package-pushnotifications/packages
3. ./installCatalog.sh AUTH APIHOST NAMESPACE WSK_CLI
   AUTH is your auth key.  APIHOST is the OpenWhisk hostname.  NAMESPACE is the namespace to install the package in.  WSK_CLI is location of the Openwhisk CLI binary.
