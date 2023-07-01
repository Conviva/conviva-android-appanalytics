
# Changelog

## 0.7.0 (01/JUL/2023)
* Supports collection of network request(http/https) performance metrics using the ConvivaOkHttpInterceptor

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
