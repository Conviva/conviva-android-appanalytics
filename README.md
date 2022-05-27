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

    // If 'lifecycleAutotracking' is enabled
    implementation 'androidx.lifecycle:lifecycle-extensions:2.2.0'

    // If 'installAutotracking' is enabled
    implementation 'com.android.installreferrer:installreferrer:2.2'

    // Dependency package required for Conviva Android tracker
    implementation 'com.squareup.okhttp3:okhttp:4.9.1'
    ...
}
```

## Offline library
Place the Conviva App Sensor in app's 'libs' folder and add the following line to app's <strong>build.gradle</strong> file:

```
dependencies {
    ...
    implementation fileTree(dir: 'libs',include:['*.aar'])
    ...
}
```

## Support Android Version

Target sdk version : Android 12L (API level 32)<br> 
Minimum sdk version : Android 4.0.4 (API level 15)

## Initialize the tracker by enabling autocollection

```
TrackerController tracker = ConvivaAppAnalytics.createTracker(context,
    <YOUR_CUSTOMER_KEY_ADVISED_BY_Conviva>,
    <YOUR_APP_NAME_ADVISED_BY_Conviva>
);
```

## Initialize the tracker to enable or disable specific autocollection

```
TrackerConfiguration trackerConfiguration = new TrackerConfiguration(<YOUR_APP_NAME_ADVISED_BY_Conviva>)
    .bundleInfoAutotracking(false);
    
TrackerController tracker = ConvivaAppAnalytics.createTracker(getApplicationContext(),
    <YOUR_CUSTOMER_KEY_ADVISED_BY_Conviva>,
    trackerConfiguration
);
```

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

## Note:  

* Refer https://pulse.conviva.com/learning-center/content/sensor_developer_center/sensor_integration/android/application_analytics/android_application_analytics.htm for integration guidelines.

