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

    // Dependency package required for Conviva Android tracker
    implementation 'com.squareup.okhttp3:okhttp:4.9.1'
    
    // Need to add this dependency explicitly prior to 0.3.1 
    implementation 'androidx.lifecycle:lifecycle-extensions:2.2.0'

    // Need to add this dependency explicitly prior to 0.3.1
    implementation 'com.android.installreferrer:installreferrer:2.2'
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
Minimum sdk version : Android 4.0.4 (API level 15)

## Initialize the tracker by enabling autocollection

```
TrackerController tracker = ConvivaAppAnalytics.createTracker(context,
    <YOUR_CUSTOMER_KEY_ADVISED_BY_Conviva>,
    <YOUR_APP_NAME_ADVISED_BY_Conviva>
);
```

<details>
  <summary><b> Initialize the tracker to enable or disable specific autocollection</b></summary>

## Initialize the tracker to enable or disable specific autocollection

```
TrackerConfiguration trackerConfiguration = new TrackerConfiguration(<YOUR_APP_NAME_ADVISED_BY_Conviva>)
    .bundleInfoAutotracking(false);
    
TrackerController tracker = ConvivaAppAnalytics.createTracker(getApplicationContext(),
    <YOUR_CUSTOMER_KEY_ADVISED_BY_Conviva>,
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
    <YOUR_CUSTOMER_KEY_ADVISED_BY_Conviva>,
    <YOUR_APP_NAME_ADVISED_BY_Conviva>
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
    <YOUR_CUSTOMER_KEY_ADVISED_BY_Conviva>,
    <YOUR_APP_NAME_ADVISED_BY_Conviva>,
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
<strong>eventData</strong> - Any type of data in string format

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
