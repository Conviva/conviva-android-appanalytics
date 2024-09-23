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

    // Conviva video sensor dependency(recommended from 4.0.30 and above excluding 4.0.31)
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

## Proguard rules
Please add the following proguard rules to keep conviva sdk classes from obfuscation.
```
-keep class com.conviva.** { *; }
```

## Multidex Config
If multidex is enabled and a multidex-config.pro is being used by the application, please add the following rule to the config.pro file.
```
-keep class com.conviva.** { *; }
```

## Support Android Version

Target sdk version : Android 14 (API level 34)<br> 
Minimum sdk version : Android 5.0 (API level 21)

## Android Sensor and Conviva Plugin Versions compatibility
Android Gradle Version | Conviva Sensor Version | Conviva Plugin Version |
------|------------ | ------------ |
 Any Android Gradle Version | [>=0.9.3](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.9.3) | [0.3.5](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.3.5) |
 7.2 and above | [>=0.9.0](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.9.0) | [0.3.3](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.3.3) |
 below 7.0  | [>=0.9.1](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.9.1) | [0.2.4](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.2.4) |
 below 7.0  | [0.9.0](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.9.0) | [0.2.3](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.2.3) |

 #### Note : Conviva Gradle Plugin do not support for the Android Gradle Plugin versions >= 7.0 and < 7.2

## Initialize the tracker by enabling autocollection

```
TrackerController tracker = ConvivaAppAnalytics.createTracker(context,
    customerKey,
    appName
);
// The tracker object can be fetched using the following API in the other classes
// than the place where createTracker is invoked using following API:
TrackerController tracker = ConvivaAppAnalytics.getDefaultTracker();
```
<strong>customerKey</strong> - a string to identify specific customer account. Different keys shall be used for development / debug versus production environment. Find your keys on the account info page in Pulse.

<strong>appName</strong> - a string value used to distinguish your applications. Simple values that are unique across all of your integrated platforms work best here.

#### Note : It is recommended to initialize the tracker at the start of the application before the first activity class.

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

## Features Auto detected using Conviva plugin

Conviva Plugin supports Auto detection below features. Please refer to the [Conviva Plugin](https://github.com/Conviva/conviva-android-plugin) for more details.

  ### User Clicks detection
  This feature supports auto detection of click events where View.OnClickListener is used.<br>
  <b>Prerequisites:</b> Conviva Gradle Plugin version [>=0.2.3](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.2.3) and tracker version [>=0.7.1](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.7.1)
      
  ### Network request detection
  This feature supports for OkHttp, Retrofit, HTTPSUrlConnection, HTTPUrlConnection<br>
  <b>Prerequisites:</b> Conviva Gradle Plugin version [>=0.2.3](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.2.3) and tracker version [>=0.7.3](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.7.3)

  #### Granular Details & Limitations:
- **Request and Response Body Collection**:
  - Collected only when:
    - Size is < 10KB and content-length is available.
    - Content-type is `"json"` or `"text/plain"`.
    - Data is a `JSONObject`, nested `JSONObject`, or `JSONArray`.
  
- **Request and Response Header Collection**:
  - Collected only when:
    - Data is a `JSONObject` (nested `JSONObject` and `JSONArray` are not yet supported).
    - The server is provisioned with `"Access-Control-Expose-Headers:"`.

### Fragments auto detection
  This feature supports auto detection of the fragment transactions such as navigation with fragments using a NavGraph or a simple fragmentTransaction to load next fragment etc.<br>
  <b>Prerequisites:</b> Conviva Gradle Plugin version [0.3.5](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.3.5) and tracker version [>=0.9.3](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.9.3)

  ### Compose Navigation auto detection
  This feature supports auto detection of the navigation events when [ComposeNavigation](https://developer.android.com/develop/ui/compose/navigation) is used in the app to define a navigation flow.<br>
  <b>Prerequisites:</b> Conviva Gradle Plugin version [0.3.5](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.3.5) and tracker version [>=0.9.3](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.9.3)

  ### Compose click auto detection
  This feature supports the auto detection of click events of composables when used as shown below.<br>
  <b>Prerequisites:</b> Conviva Gradle Plugin version [0.3.5](https://github.com/Conviva/conviva-android-plugin/releases/tag/v0.3.5) and tracker version [>=0.9.3](https://github.com/Conviva/conviva-android-appanalytics/releases/tag/v0.9.3)

  ```kotlin
val submitLabel = "Submit"
Text(
    text = submitLabel,
    style = MaterialTheme.typography.button,
    modifier = Modifier.clickable(
        onClick = onClickSeeAll,
        onClickLabel = submitLabel
    )
)
```

  ### Traceparent Header generation and collection
  Please contact conviva for enabling this feature.


## API to override the default Activity Name in the Screen View Event
This feature supports overriding the default Activity Name in the Screen View Event. Add the public variable *convivaScreenName* in the corresponding activity which you want to set the screen name supported from 0.9.0 version onwards

The following example shows how to include the plugin:
```
public class ExampleActivity extends Activity {
    ...
    public String convivaScreenName = "HomeScreen";
    ...
```

<details>
    <summary><b>Auto-collected Events</b></summary>
    
##### Conviva provides a rich set of application performance metrics with the help of auto collected app events. Below are the events which are auto collected once above initialisation is done.

Event | Occurrence
------|------------
network_request | after receiving the network request response
screen_view | when the screen is interacted on either first launch or relaunch
application_error | when an error occurrs in the application
button_click | on the button click callback (works both Clickable Views and Clickable Modifiers in compose)
application_background | when the application is taken to the background
application_foreground | when the application is taken to the foreground
application_install | when the application is launched for the first time after it's installed. (It's not the exact installed time.)
deep_link_received | on opening an application using the UTM URL
anr_start | Timer starts for the response from the main thread. If it takes more than 4 seconds, _anr_start_ event is triggered.
anr_end | If the SDK gets response after triggering _anr_start_, then _anr_end_ is dispatched.
conviva_fragment_view | Whenever a fragment transaction commits
conviva_compose_view | Whenever a destination change occurs in the NavController  of the ComposeNavigation

To learn about the default metrics for analyzing the native and web applications performance, such as App Crashes, Avg Screen Load Time, and Page Loads, refer to the [App Experience Metrics](https://pulse.conviva.com/learning-center/content/eco/eco_metrics.html) page in the Learning Center.
</details>
