# conviva-android-appanalytics
Use Application Analytics to autocollect events and track application specific events and state changes.

## Conviva Android App Analytics can be included in two ways in any Android app projects.

* Gradle dependency
* Offline library

## Gradle dependency
Add the following line to app's <strong>build.gradle</strong> file along with the dependencies:

```
dependencies {
    ...
    implementation 'com.conviva.sdk:conviva-android-tracker:<version>'

    // Conviva video sensor dependency(supported 4.0.19 onwards)
    implementation 'com.conviva.sdk:conviva-core-sdk:<version>'
    ...
}
```

<details>
  <summary><b> Offline library</b></summary>
    
## Offline library
Place the Conviva App Sensor in app's 'libs' folder and add the following line to app's <strong>build.gradle</strong> file:

```
dependencies {
    ...
    implementation fileTree(dir: 'libs',include:['*.aar'])
    ...
}
```
</details>

## Support Android Version

Target sdk version : Android 13 (API level 33)<br> 
Minimum sdk version : Android 5.0 (API level 21)

## Initialize the tracker by enabling autocollection

```
TrackerController tracker = ConvivaAppAnalytics.createTracker(context,
    customerKey,
    appName
);
```
<strong>customerKey</strong> - a string to identify specific customer account. Different keys shall be used for development / debug versus production environment. Find your keys on the account info page in Pulse.

<strong>appName</strong> - a string value used to distinguish your applications. Simple values that are unique across all of your integrated platforms work best here.


<details>
  <summary><b> Initialize the tracker to enable or disable specific autocollection</b></summary>

## Initialize the tracker to enable or disable specific autocollection

```
TrackerConfiguration trackerConfiguration = new TrackerConfiguration(appName)
    .bundleInfoAutotracking(false);
    
TrackerController tracker = ConvivaAppAnalytics.createTracker(getApplicationContext(),
    customerKey,
    trackerConfiguration
);
```
</details>

<details>
  <summary><b> Initialize the tracker to disable event caching</b></summary>

## Initialize the tracker to disable event caching

### To run the Conviva initialisation in the main thread
```
// Supported from 0.3.3 onwards
EmitterConfiguration emitterConfiguration = new EmitterConfiguration()
    .disableEventCaching(true);
    
TrackerController tracker = ConvivaAppAnalytics.createTracker(getApplicationContext(),
    customerKey,
    appName,
    emitterConfiguration
);
```
        
### To run the Conviva initialisation in the worker thread
```
// Supported from 0.3.4 onwards
EmitterConfiguration emitterConfiguration = new EmitterConfiguration()
    .disableEventCaching(true);

TrackerController tracker;
ConvivaAppAnalytics.createTracker(getApplicationContext(),
    customerKey,
    appName,
    new Consumer<TrackerController>() {
        @Override
        public void accept(TrackerController trackerController) {
            tracker = trackerController;
            // update the tracker to be used for setting the user id
        }
    },
    emitterConfiguration
);
```
</details>
        
        
## Set the user id (viewer id)

```
tracker.getSubject().setUserId(userId);
```

        
## Extend tracking to track your application specific events and state changes
Use <strong>trackCustomEvent()</strong> API to track all kinds of events. This API provides 2 fields to describe the tracked events:<br>
<strong>eventName</strong> - Name of the custom event<br>
<strong>eventData</strong> - Any type of data in JSONObject format

The following example shows the implementation of the 'onClick' event listener to any element.
```
// Supported from 0.5.0 onwards

JSONObject eventDataJSON = new JSONObject();
eventDataJSON.put("identifier1", intValue);
eventDataJSON.put("identifier2", boolValue);
eventDataJSON.put("identifier3", "stringValue");

String eventName = "your-event-name";

tracker.trackCustomEvent(eventName, eventDataJSON);
```
<details>
  <summary><b> track application with data in JSON String format </b></summary>

## trackCustomEvent() with data in JSON String format

Use <strong>trackCustomEvent()</strong> API to track all kinds of events. This API provides 2 fields to describe the tracked events:<br>
<strong>eventName</strong> - Name of the custom event<br>
<strong>eventData</strong> - Any type of data in JSON String format

The following example shows the implementation of the 'onClick' event listener to any element.
```
// ... send events 'onClick' of button
HashMap<String, Object> eventData = new HashMap<>(); 
eventData.put("identifier1", intValue); 
eventData.put("identifier2", boolValue); 
eventData.put("identifier3", "stringValue");

String eventName = "your-event-name";

tracker.trackCustomEvent(eventName, JSONValue.toJSONString(eventData));
```
</details>

## Extend tracking to set your application specific custom tags
Use <strong>setCustomTags()</strong> API to set custom tags<br>
Use <strong>clearCustomTags()</strong> API to clear few of the previously set custom tags<br>
Use <strong>clearAllCustomTags()</strong> API to clear all the previously set custom tags<br>

The following example shows the implementation of the application using these API's:
```
// Adds the custom tags
HashMap<String, Object> tags = new HashMap<>(); 
eventData.put("key1", intValue); 
eventData.put("key2", boolValue); 
eventData.put("key3", "stringValue");
tracker.setCustomTags(tags);

// clears few of the custom tags
Set<String> clearTagKeysSet = new HashSet<>();
clearTagKeysSet.add("key1"); 
clearTagKeysSet.add("key2"); 
tracker.clearCustomTags(clearTagKeysSet);

// clears all the custom tags
tracker.clearAllCustomTags();
```

## Collection of the user click events of any clickable views via instrumentation
This feature supports tracking the user click events of views when a View.OnClickListener is set in the application and is supported from 0.7.3 version onwards

The following example shows how to include the plugin:
```
// in the root or project-level build.gradle
dependencies {
  ...
//For Android Gradle Plugin version 8.0 and above, use
classpath 'com.conviva.sdk:android-plugin:0.3.2'

//For Android Gradle Plugin version 7.2 and below, use
classpath 'com.conviva.sdk:android-plugin:0.2.2'
  ...
}

// in the app, build.gradle at the end of plugins add the
...
apply plugin: 'com.conviva.sdk.android-plugin'



// in the app, build.gradle.kts at the end of plugins add the
plugins {
    id 'com.conviva.sdk.android-plugin'
}

```
    
## Collection of the OkHttp/Retrofit/HTTPSUrlConnection/HTTPUrlConnection NetworkRequest Tracking via instrumentation
This feature supports to track the Network Requests triggerred with in application and third party libraries scope as well supported from 0.7.1 version onwards

*Note: This collection is disabled by default, reach out to Conviva Team enabling the tracking.* <br>

The following example shows how to include the plugin:
```
// in root top-level build.gradle
dependencies {
  ...
  classpath 'com.conviva.sdk:android-plugin:<version>'
  ...
}

// in app module-level build.gradle
...
apply plugin: 'com.conviva.sdk.android-plugin'

// For Conviva Android tracker version <0.7.3 only
implementation "com.conviva.sdk:conviva-instrumentation-tracker:<version>"
//<version> starts from 0.1.0 version onwards

...
```
*<strong>Note:</strong>* <br>
*Please remove the interceptor integration while using the android plugin.</strong>* <br>
<br> *Here are some of the granular details/limitations of the feature:*
* *Response and Request Body atributes are collected only when the:*
    * *size is < 10kb and the content-length is available* 
    * *content-type is "json" or "text/plain"*
    * *data is a JSONObject or Nested JSONObject or JSONArray*
* *Response and Request Headers are collected only when the:*
    * *data is a JSONObject(Nested JSONObject and JSONArray are not yet supported)*
    * *server is provisioned with "Access-Control-Expose-Headers:*"* 
</details>

<details>
  <summary><b> Collection of the OkHttp/Retrofit NetworkRequest Tracking for the objects created with in the application scope via Okhttp Interceptor</b></summary>

### Collection of the OkHttp/Retrofit NetworkRequest Tracking for the objects created with in the application scope via Okhttp Interceptor
Deprecated feature
This feature supports to track the Network Requests triggerred with in the application scope supported from 0.7.0 version onwards.

*Note: This collection is disabled by default, reach out to Conviva Team enabling the tracking.* <br>

The following example shows how to set the Network Request Interceptor for okhttp/retrofit:
```
...
OkHttpClient client = new OkHttpClient.Builder()
        .addInterceptor(new ConvivaOkHttpInterceptor())
        .build();
...
```
</details>

<details>
    <summary><b>Auto-collected Events</b></summary>
    
##### Conviva provides a rich set of application performance metrics with the help of autocollected app events, such as _screen_view_ , _button_click_, and _network_request_.

Event | Occurrence |
------|------------ |
network_request | after receiving the network request response |
screen_view | when the screen is interacted on either first launch or relaunch |
application_error | when an error occurrs in the application |
button_click | on the button click callback |
application_background | when the application is taken to the background |
application_foreground | when the application is taken to the foreground |
application_install | when the application is launched for the first time after it's installed. (It's not the exact installed time.) |
deep_link_received | on opening an application using the UTM URL |
anr_start | Timer starts for the response from the main thread. If it takes more than 4 seconds, _anr_start_ event is triggered. |
anr_end | If the SDK gets response after triggering _anr_start_, then _anr_end_ is dispatched.   |


To learn about the default metrics for analyzing the native and web applications performance, such as App Crashes, Avg Screen Load Time, and Page Loads, refer to the [App Experience Metrics](https://pulse.conviva.com/learning-center/content/app_experience/app_experience_metrics.html?tocpath=App%20Experience%7C_____8) page in the Learning Center.
</details>
