# Conviva Android ECO SDK

Use Conviva Android ECO SDK to auto-collect events and track application-specific events and state changes.

**Table of Contents**
- [Quick Start](#quick-start)
- [More Features](#more-features)
- [Auto-collected Events](#auto-collected-events)
- [FAQ](#faq)

## Quick Start

##### Supported Android Version

- Target SDK version: Android 14 (API level 34)
- Minimum SDK version: Android 5.0 (API level 21)

<details>
<summary><b>Diagram</b></summary>

  ```mermaid
graph TD
    build[Build Process] --> plugin;
    plugin[Conviva ECO Gradle Plugin] -->|Injects code| app;
    app[UI Layer & Business Logic] --> sdk@{ label: "Conviva ECO SDK" };
    events[App Events] --> sdk;
    app --> events;
    sdk --> backend[Conviva Backend Server];

    subgraph "Android Application Runtime"
        app;
        sdk;
        events;
    end

    subgraph "Compilation Phase"
        build;
        plugin;
    end
	style plugin fill:#004AAD,color:#FFFFFF
	style sdk fill:#004AAD,color:#FFFFFF
	style backend fill:#004AAD,color:#FFFFFF
  ```

</details>

### 1. Download

- Add the plugin to your project's root `build.gradle` file, replacing `<version>` with the latest from Conviva [Conviva Android ECO Plugin](https://github.com/Conviva/conviva-android-plugin).

```groovy
// Groovy
plugins {
  // ...
  id 'com.conviva.sdk.android-plugin' version '<version>' apply false
}

```

```kotlin
// Kotlin
plugins {
  // ...
  id("com.conviva.sdk.android-plugin") version "<version>" apply false
}

```

- Apply the Gradle plugin and add the dependency in `app/build.gradle` file, replacing `<version>` with the latest SDK version available [here](https://github.com/Conviva/conviva-android-appanalytics/releases).

```groovy
// Groovy
plugins {
    // ...
    id 'com.conviva.sdk.android-plugin'
}

android {
  // ...
}

dependencies {
    // ...
    implementation 'com.conviva.sdk:conviva-android-tracker:<version>'
}
```

```kotlin
// Kotlin 
plugins {
    // ...
    id("com.conviva.sdk.android-plugin")
}

android {
  // ...
}

dependencies {
    // ...
    implementation("com.conviva.sdk:conviva-android-tracker:<version>")
}

```

<details>
    <summary>Using Offline Library for Dependencies</summary>
    
Download the `.aar` from GitHub's [releases page](https://github.com/Conviva/conviva-android-appanalytics/releases) and add it manually instead of using Gradle.
    
```groovy
dependencies {
    // ...
    implementation fileTree(dir: 'libs',include:['*.aar'])
}
```

</details>


**Proguard / R8 / Multidex Config**

Add the following ProGuard/R8 rule to the `proguard-rules.pro` file to prevent Conviva SDK obfuscation. If using multidex with the `multidex-config.pro` file, add the same rule there as well.

```plaintext
-keep class com.conviva.** { *; }
```

### 2. Initialization

#### Note: It is recommended to Initialize the tracker at app startup before the first activity.

An example of Conviva Android ECO SDK initialization: 
```java
import com.conviva.apptracker.ConvivaAppAnalytics;

public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        // ...
        //  Initialize the Conviva Android ECO SDK
        TrackerController tracker = ConvivaAppAnalytics.createTracker(this, customerKey, appName);
        
    }
}
```

**customerKey** - A string to identify a specific customer account. Use different keys for dev and prod. Find them in [Pulse](https://pulse.conviva.com/app/profile/applications) under My Profile (_Conviva login required_).

**appName** - A string value that uniquely identifies your app across platforms.

```java
// The tracker object can be retrieved using the following API in other classes after initialization.
TrackerController tracker = ConvivaAppAnalytics.getTracker();
```

### 3. Set the User ID
User ID is a unique string identifier to distinguish individual viewers. If using [Conviva Video Sensor](https://github.com/Conviva/conviva-android-coresdk), match it with the **Viewer ID**.

```java
tracker.getSubject().setUserId(userId);
```

After steps 1–3, verify [auto-collected events](#auto-collected-events) in the [validation dashboard](https://pulse.conviva.com/app/appmanager/ecoIntegration/validation). (_Conviva login required_)

## More Features

<details>

<summary><b>Track Custom Event</b></summary>


Use the **trackCustomEvent()** API to track all kinds of events. This API provides 2 fields to describe the tracked events:

**eventName** - Name of the custom event

**eventData** - Data in a `JSONObject` or a JSON-formatted `String`

```java
// Set up the event properties JSONObject
JSONObject eventDataJSON = new JSONObject();
eventDataJSON.put("identifier1", intValue);
eventDataJSON.put("identifier2", boolValue);
eventDataJSON.put("identifier3", "stringValue");

String eventName = "your-event-name";

tracker.trackCustomEvent(eventName, eventDataJSON);
```

</details>

<details>

<summary><b>Set Custom Tags</b></summary>

Custom Tags are global tags applied to all events and persist throughout the application lifespan, or until they are cleared.

Set custom tags:
```java
// Adds the custom tags
HashMap<String, Object> tags = new HashMap<>();
tags.put("key1", intValue);
tags.put("key2", boolValue);
tags.put("key3", "stringValue");
tracker.setCustomTags(tags);
```

Clear a few of the previously set custom tags:
```java
// Clears custom tags key1 & key2
Set<String> clearTagKeysSet = new HashSet<>();
clearTagKeysSet.add("key1");
clearTagKeysSet.add("key2");
tracker.clearCustomTags(clearTagKeysSet);
```

Clear all the previously set custom tags:
```java
// Clears all the custom tags
tracker.clearAllCustomTags();
```

</details>

<details>

<summary><b>Override Activity Name</b></summary>

Override the default Activity Name in the Screen View Event by adding the `convivaScreenName` variable in the desired activity.

```java
public class ExampleActivity extends Activity {
    // ...
    public String convivaScreenName = "HomeScreen";
    // ...
}
```

</details>


## Auto-collected Events

Conviva automatically collects rich set of app performance metrics through app events after completing the [Quick Start](#quick-start).

<details>

<summary><b>Auto-collected events table</b></summary>

| Event | Occurrence |
| --- | --- |
| network\_request | After receiving the network request response. [Refer limitations](#limitations). _Collected by plugin._ |
| screen\_view | When the screen is interacted with on either first launch or relaunch. [Refer limitations](#limitations). _Collected by plugin._ |
| application\_error | When an error occurs in the application |
| button\_click | On the button click callback (works with both Clickable Views and Clickable Modifiers in compose). _Collected by plugin._ |
| application\_background | When the application is taken to the background |
| application\_foreground | When the application is taken to the foreground |
| application\_install | When the application is launched for the first time after it's installed. (It's not the exact installed time.) [Refer limitations](#limitations). |
| deep\_link\_received | On opening an application using the UTM URL. _Collected by plugin._ |
| anr\_start | Timer starts for the response from the main thread. If it takes more than 4 seconds, _anr\_start_ event is triggered. |
| anr\_end | If the SDK gets a response after triggering _anr\_start_, then _anr\_end_ is dispatched. |
| conviva\_fragment\_view | Whenever a fragment transaction commits. _Collected by plugin._ |
| conviva\_compose\_view | Whenever a destination change occurs in the NavController of the ComposeNavigation. _Collected by plugin._ |

To learn about the default metrics for analyzing the native and web applications performance, such as App Crashes, Avg Screen Load Time, and Page Loads, refer to the [App Experience Metrics](https://pulse.conviva.com/learning-center/content/eco/eco_metrics.html) page in the Learning Center.

</details>

### Limitations

<details>
  <summary><b>screen_view, application_install</b></summary>

  Auto-collection of **screen_view** and **application_install** events is temporarily affected due to controlled ingestion by Conviva.
  This impact occurs only during the first fresh launch after an app install or clear-data. It is valid only until the Conviva Remote Config becomes available and will no longer persist in subsequent launches.

</details>

<details>
  <summary><b>network_request</b></summary>
  This feature supports OkHttp, Retrofit, HTTPSUrlConnection, HTTPUrlConnection (tracking URL.getContent() and URL.getStream() are not supported).

  **Request and Response Body Collection:**

  Collected only when:
  - Size is < 10KB and content-length is available.
  - Content-type is `"json"` or `"text/plain"`.
  - Data is a `JSONObject`, nested `JSONObject`, or `JSONArray`.

 **Request and Response Header Collection:**

 Collected only when:
 - Data is a `JSONObject` (Nested `JSONObject` and `JSONArray` are not yet supported).
 - The server is provisioned with `"Access-Control-Expose-Headers:"`.

</details>

## FAQ

[ECO Integration FAQ](https://pulse.conviva.com/learning-center/content/sensor_developer_center/tools/eco_integration/eco_integration_faq.htm)
