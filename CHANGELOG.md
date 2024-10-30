
# Changelog

## 0.9.7 (30/OCT/2024)
* Supports Controlled Ingestion Feature which is disabled by default. Upgrading to this version will not change the collection behavior unless it is enabled. To enable this feature, please contact Conviva support team.
* Supports sending Diagnostic Info Events which is disabled by default.

## 0.9.6.1 (25/SEP/2024)
* Fixes the issue in capturing the response body when content-length is unknown.

## 0.9.6 (10/SEP/2024)
* <b>Remote config enhancements:</b>
  * Implements exponential backoff for remote config fetch attempts in case of failures, with a maximum of 3 attempts, each separated by a 5-second interval
  * Enhances version checking for remote config
  * Sets the default timeout of Read and Connect to 30 seconds, for the remote config and CTP requests
  * Enhances the remote config fetch timestamp based on server responses
  * Optimizes the cacheRefreshInterval delta to 3%
* <b>Retry Logic enhancements:</b>
  * Enhances the retry logic to delete the db entries in response to avoid piling up events on failure cases
* <b>Conviva cleanup enhancements:</b>
  * Introduces a new cleanup() API to remove Conviva from the application's footprint
  * Enhances the internal cleanup process to improve efficiency
* <b>Conviva CTP requests dispatching enhancements:</b>
  * Enhances the CTP requests dispatching by changing the queue from LIFO to FIFO
* <b>Logging enhancements:</b>
  * Replaces printStackTrace in catch blocks with proper log messages and all regex log messages with strings to prevent issues
  * Ensures diagnosticAutotracking operates independently, without relying on logging
  * Disables the diagnostic_error events
* <b>Other enhancements:</b>
  * Defaults timeout of read and connect to 30 seconds for CTP requests
  * Enhances cache refresh interval for non-positive numbers
  * Enhances the setting of the Conviva identifier
  * Updates file directories and shared preferences of Conviva SDK
  * Restricts tracker instance creation from remote config unless triggered by the application
  * Removes debug context and debug event implementations
  * Removes the isDataSaverEnabled() check for the periodic heartbeat events
  * Removes explicit HTTP 1.2 setting for the CTP requests

## 0.9.5 (06/SEP/2024)
* Introduces API for setting the conviva identifier

## 0.9.4 (05/AUG/2024)
* Enables and disables the auto detection of the compose and fragment navigation auto detection dynamically based on the remote config.

## 0.9.3 (19/JUL/2024)
* Supports the auto detection of the compose navigation and compose click tracking.
* Supports the fragments navigation.
* Supports the traceparent header generation and collection.
* Requires conviva gradle plugin version >= [0.3.5](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.3.5) to support auto detection of compose navigation, click and fragments navigation. Please refer to [compatibility table](https://github.com/Conviva/conviva-android-appanalytics/blob/main/README.md#android-sensor-and-conviva-plugin-versions-compatibility)

## 0.9.2 (20/MAY/2024)
* Fixes the build issue of the [react native sensor](https://github.com/Conviva/conviva-react-native-appanalytics) introduced in 0.9.0 version of sensor

## 0.9.1 (13/MAY/2024)
* Fixes the issue of redundant deeplink events
* Fixes the issue of monitoring of network request events which are in blocklist during the remote config application
* Fixes the issue of network requests tracked before the corresponding feature enabled
* Fixes the error due to deserialization of remote config which improves the remote config functionality
* Improves the application error event reporting by increasing timeout to 2 seconds
* Requires conviva gradle plugin version >= [0.2.4](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.2.4) to support deeplink feature. Please refer to [compatibility table](https://github.com/Conviva/conviva-android-appanalytics/blob/main/README.md#android-sensor-and-conviva-plugin-versions-compatibility)

## 0.9.0 (29/APR/2024)
* Supports Android 14 version
* Requires conviva gradle plugin version >= [0.2.3](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.2.3) and >= [0.3.3](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.3.3). Please refer to [compatibility table](https://github.com/Conviva/conviva-android-appanalytics/blob/main/README.md#android-sensor-and-conviva-plugin-versions-compatibility)
* Optimizes the payload size by applying *gzip encryption* and *schema optimization*
* Supports custom gateway URL by using the unique `CUSTOMER_KEY` associated with each customer
* Renames the remote configuration URL domain to `*.conviva.com` (`http://conviva.com/`)
* Reduces the payload size of the *Network Request Event* by limiting the default attributes to `targetUrl`, `method`, `responseStatusCode`, and `duration`. However, the request/response body/header attributes remain unchanged
* Updates the default block list of the *Network Request Tracking* feature
* Updates the remote config attribute from `cacheRefreshInterval` in seconds to `cri` in minutes to support backward compatibility
* Introduces API to report the *Screen Name* from the application instead of the default *Activity Name*. To know more, refer to this [section](https://github.com/Conviva/conviva-android-appanalytics/blob/main/README.md#api-to-override-the-default-activity-name-in-the-screen-view-event)
* Enhances the remote configuration to fetch the application background state when the application remains active
* Enhances the *Network Request Blocking* and *Custom Event Blocking* logic for the case insensitive support
* Enhances the *Substring Matching* technique by removing the `%` regex. Earlier the `""` string was treated as `"*"`
* Enhances the auto-detection of `application_background` and `application_foreground` events by making them independent of *Session Context*
* Includes the Conviva debug event and context by applying remote config, which remain disabled, by default
* Includes the *Retry* logic instead of the *Fallback* logic to fetch remote config
* Fixes the issue of `application_foreground` events that are not sent during the first launch, as well as kill and relaunch of the application
* Fixes the issue of fetching remote config after the kill and relaunch of the application
* Fixes the issue of the duplicate `application_error` events that are sent during application crashes
* Fixes the issue of not sending the `network_request` events during the network failure use cases
* Fixes the issue of Video Sensor Events not broadcasted to ECO Sensors in Android 14 devices

## 0.8.3 (04/APR/2024)
* Fixes the ClassCastException for the applications overriding the getApplicationContext with ContextImpl.
  
## 0.8.2 (16/FEB/2024)
* Enhances network request & response collection feature to capture JSONArray type data. <br/>
  **Note: Please refer to [Network Request Feature](https://github.com/Conviva/conviva-android-appanalytics?tab=readme-ov-file#collection-of-the-okhttpretrofithttpsurlconnectionhttpurlconnection-networkrequest-tracking-via-instrumentation) for more details** 

## 0.8.1 (21/DEC/2023)
* Fixes the Network Request Feature issue of sending Request/Response Body/Headers as String instead of JSONObject. <br/>
  **Note: The 0.8.1 version needs to be used for extracting the Request/Response Body/Headers for Network Request Feature, please refer to [Network Request Feature](https://github.com/Conviva/conviva-android-appanalytics?tab=readme-ov-file#collection-of-the-okhttpretrofithttpsurlconnectionhttpurlconnection-networkrequest-tracking-via-instrumentation) for more details** 

## 0.8.0 (12/DEC/2023)
* Enhances Custom Event blocking logic by matching the substring instead of regex match.
* Enhances the User Click event to have atleast only one of the attributes as mandatory.
* Enhances handling of the unwanted diagnostic error log message on the first launch of the application.
* Enhances the logic of the timer tasks for applying the remote configuration.
* Enhances the default timeout of sending Heartbeat to 30 seconds(from 15 seconds).
* Deprecates few of the unused API's of createTracker()
* Fixes the issue of App Load Time(onCreate and onResume) dependency on the initialisation of Conviva SDK and the first activity invocation.
* Fixes the issue preventing heartbeats from being sent in a specific corner case.


## 0.7.5.1 (14/NOV/2023)
* Enhances Conviva SDK initialisation for lightweight execution on the application thread, relocating internal SDK API calls to Conviva Worker Threads.
* Enhances Network Request Feature to parse Request Body and Response Body only when the fields are available in remote config.
* Enhances Network Request Feature by passing data from instrumentation to tracker via callback function instead of Broadcast.
* Fixes the issue of the Screen Load Time(missing onCreate and onResume) of the first activity invocation.


## 0.7.5 (27/OCT/2023)
* Enhances network request & response collection feature to capture limited (json only, size limit 10kb) and controlled set of information from headers and body.
* Fixes the issue of implicit Broadcast of the Network Requests with in the application package.
* Enhances setCustomTags() to support passing any Map<String, Object> argument instead of HashMap.


## 0.7.4 (03/OCT/2023)
* Fixed an OutOfMemory bug in the Network-Request-Tracking feature, when a request with large response is tracked.
* Default block list of the Network-Request-Tracking feature is updated to cover more urls with large responses(Ex: MP4).

## 0.7.3 (05/SEP/2023)
* Supports the auto collection of the click events by instrumenting the **onClick(View v)** function of the **android.view.View.OnClickListener** interface, when **com.conviva.sdk.android-plugin** applied in the project level build.gradle.

## 0.7.1 (11/AUG/2023)
* Supports auto collection of network request(http/https) performance metrics using the instrumentation logic for the okhttp and HTTPUrlConnection using conviva-instrumentation-tracker and conviva-android-plugin packages.

## 0.7.0 (01/JUL/2023)
* Supports collection of network request(http/https) performance metrics using the ConvivaOkHttpInterceptor(which is disabled by default)

## 0.6.0 (21/JUN/2023)

* Enhances App Sensor by handling Exceptions, due to which the return value of createTracker can be Nullable
* Fixes the crash issue of the SecurityException due to Settings key: <psm_switch>, which is found in Specific Device
* Fixes the ANR issue due to registerReceiver by moving it to Worker Thread, which is found in Smart TV's
* Fixes the issue of tracker creation while applying remote config in some cases 
* Enhances applying of the remote config while enabling and disabling feature

## 0.5.6 (23/MAY/2023)

* Fixes the issue of the default config not applied till the remote config is downloaded on first time using Conviva SDK

## 0.5.5 (16/MAY/2023)

* Enhances the applying of the remote configuration at an interval of cacheRefreshInterval which is set to 30 mins by default
* Enhances the auto collection of screen_view event, by excluding the Fragment tracking
* Fixes the Strict Mode issues

## 0.5.3 (01/MAY/2023)

* Fixes the issue of duplicate event sequence number(client id based) while taking application to foreground after killing the application during background state
* Fixes the issue of recurring suffix to the tracker version while pause and un pause of the application

## 0.5.2 (23/APR/2023)

* Implements feature of the client id based event sequence number
* Fixes the issue of custom events not getting blocked for the camel case event names
* Fixes unwanted diagnostic error on the missing Android IDFA in React Native and Flutter Sensors

##  0.5.1 (06/APR/2023)

* Fixes the issue of new session creation while applying remote config and local cache, due to which duplicate heartbeats are sent
* Fixes the reporting of Periodic Heartbeat event being independent on the remote configuration
* Fixes the issue of inaccurate collection of 'Low Power Mode' device setting

## 0.5.0 (09/MAR/2023)
*	Supports auto detection of the Application Not Responding(ANR), which is set to default as false("anrTracking" remote config)
*	Adds trackCustomEvent(eventName, JSONObject) to pass the JSONObject instead of String conversion
*	Adds Client Id of the device with "X-Client-ID" HTTP Header while sending HB
*	Enhances the auto detection of Application Background and Foreground events, which earlier failed when the application choose to remove the InitializationProvider in the Manifest
*	Enhances reporting error log message in console for trackCustomEvent(eventName, String), if the Object is non JSON
*	Downgrades the okhttp dependency package to 4.9.3 as 4.10.0 in causes issues with using App and Video Sensors

## 0.4.4 (19/JAN/2023)
*	Fixes the Null Pointer exceptions in receiving the Video Sensor Events + Extra null checks added for the feature
*	Limits support for the devices api level >= 21 in the Manifest file

## 0.4.3 (12/JAN/2023)
* Fixes the VideoSensorReceiver zero argument issue
* Fixes the regression issue of userclicktracking default value to true by disabling in this version(introduced in 0.4.0)

## 0.4.2 (05/JAN/2023)
* Fixes the NullPointerException thrown due to Objects.requireNonNull check for the SetUserId which can be null.

## 0.4.0 (29/DEC/2022)
* Supports Android 13 (API level 33)
* Supports feature to receive the broadcasted video events from Conviva Android Video Sensor SDK(supports Video Sensor SDK 4.0.29 onwards)
* Adds the API's setCustomTags(), clearCustomTags() and clearAllCustomTags() for reporting/remove custom tags similar to video sensor
* Adds eventIndex and previousEventTimestamp to event_info context
* Enhances the performance, while sending offline data with default settings of:
   * The maximum entries to be read from db to 5
   * The maximum concurrent threads to 2
* Updates the application_error schema version to the latest one 1_0_2
* Fixes the issue of generating duplicate events in some scenarios
* Internal enhancements:
   * Adds interface to subscribe for events tracked in Web views
   * Adds anonymous tracking features
   * Adds pause and resume to EmitterController
   * Adds session callback
   * Adds eventIndex and firstEventTimestamp properties to client sessions
   * Adds a customisable set of failure HTTP status codes for which collector requests should not be retried
   * Adds support for storing cookies in NetworkConnection
   * Sets log level without trackerDiagnostic 
   * Attaches LifecycleEntity before the first Background event
   * Enables updating collector endpoint while Emitter is running
   * Checks session for background event using foreground timeout
   * Ensures english numerals are used in date-times
   * Fixes multiple ScreenContexts on ScreenView events
   * Fixes updating IDFA during app runtime
   * Does not allow multiple state machines with the same ID

## 0.3.4.4 (07/NOV/2022)

* Hot fix on top of 0.3.4
* Fixes the intermittent StackOverflowError Exception during retry of failed Heartbeats
* Fixes the potential db syncronisation issues


## 0.3.4 (17/OCT/2022)

* Supports createTracker() with a callback function for the TrackerController with readcache from worker thread
* Fixes the Strict Mode issues

## 0.3.3 (12/OCT/2022)

* Option to disableEventCaching() set to false by default, to avoid storing events in db
* Fixes the issue of the iid getting changed on new session
* Fixes the issue of non closing of Response body for unsuccessful Requests
* Fixes issue of missing events when the application is launched in offline mode

## 0.3.2 (21/SEP/2022)

* Fixes the issue of cached customer key used for the gateway endpoint and app name
* Replaces the external dependency of NamedThreadFactory with internal one
* Enhances the logging by removing unwanted error messages, especially on the missing Advertising Id

## 0.3.1 (07/SEP/2022)

* Supports remote config feature to configure enabling/disabling of features and the time intervals from backend portal
* Supports adding the required dependency packages lifecycle and installreferrer in Conviva App Sensor SDK
* Supports collecting the Android IDFA automatically
* Deprecates the API createTracker(context, customerKey, trackerConfiguration) and mandates to use createTracker(context, customerKey, appName)
* Internal improvements in multi threaded application environments
* Fixes the Conviva data enrichment issues related to referrer, batteryLevel, systemAvailableMemory and empty body warnings
* Fixes the issue of the incorrect session id timeout logic for background and foreground events
* Fixes the issue of raw event being sent with empty string eventName attribute

## 0.3.0 (27/MAY/2022)

* Option to enable or disable bundleInfoAutotracking() set to true by default

## 0.2.2 (12/MAY/2022)

* Fixed the ClassCastException introduced in 0.2.0v of Conviva SDK

## 0.2.1 (10/MAY/2022)

* Added feature of Auto Detection of Low Power Mode in Platform Contexts
* Added feature to auto track deepLink set to true by default with option to enable or disable deepLinkAutotracking()
* Option to enable or disable 


Autotracking() set to false by default
* Fixed the issue of auto collection of first Fragment onCreate and onResume timestamps

## 0.2.0 (20/APR/2022)

* Published the binaries to maven
* Added the tracker.reportCustomEvent() for sending raw data
* Changed the default post path to ctp
* Added feature to auto track the Fragment and its load time
* Added feature to auto track user clicks inside Activity and Fragment
* Added feature to enable or disable activityTrackingPeriodicHB() set to true by default, with default interval of 40 seconds and delay of 2 seconds
* Simplified the integration instructions by absorbing internally the:
    * default gateway endpoint to "https://appgw.conviva.com"
    * HTTPMethod POST
    * default namespace "CAT"
* Added feature of measuring Application and Activity Load time
* Added feature of auto collecting Bundle Info from the getIntent()
* Fixed the duplicate entries in the heartbeats while navigating through the screens

## 0.1.1 (30/MAR/2022)

* Modified the min sdk version supported to 15 for supporting older versions

## 0.1.0 (10/FEB/2022)

* Published the first version of the Conviva App Sensor for the App Analytics
* Add the customerKey("ck") to createrTracker()
* Add Conviva specific parameters ck, clid and iid under clid_schema schema
* Rename the default package to "com.conviva.apptracker"
* Rename the default class to ConvivaAppAnalytics
