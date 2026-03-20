# Conviva Android App Analytics — Java Snippets

Companion file to AGENTS.md. Only read this file if the target project uses Java.

---

## Initialization — Application class (super is not last)

```java
import com.conviva.apptracker.ConvivaAppAnalytics;

public class MyApp extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        // ... existing startup code ...
        ConvivaAppAnalytics.createTracker(this, "YOUR_CUSTOMER_KEY", "YOUR_APP_NAME");
    }
}
```

## Initialization — Application class (super is last line — insert above it)

```java
import com.conviva.apptracker.ConvivaAppAnalytics;

public class MyApp extends Application {
    @Override
    public void onCreate() {
        // ... existing startup code ...
        ConvivaAppAnalytics.createTracker(this, "YOUR_CUSTOMER_KEY", "YOUR_APP_NAME");
        super.onCreate();
    }
}
```

Replace `YOUR_CUSTOMER_KEY` and `YOUR_APP_NAME` with the actual values from Section 3 of AGENTS.md.

---

## User ID

```java
import com.conviva.apptracker.ConvivaAppAnalytics;
import com.conviva.apptracker.controller.TrackerController;

TrackerController tracker = ConvivaAppAnalytics.getTracker();
tracker.getSubject().setUserId(userId);
```

---

## Custom Events

```java
TrackerController tracker = ConvivaAppAnalytics.getTracker();
JSONObject eventData = new JSONObject();
eventData.put("identifier1", intValue);
eventData.put("identifier2", boolValue);
eventData.put("identifier3", "stringValue");
tracker.trackCustomEvent("your-event-name", eventData);
```

## Custom Tags

```java
TrackerController tracker = ConvivaAppAnalytics.getTracker();
HashMap<String, Object> tags = new HashMap<>();
tags.put("key1", intValue);
tags.put("key2", boolValue);
tags.put("key3", "stringValue");
tracker.setCustomTags(tags);

tracker.clearCustomTags(Arrays.asList("key1", "key2"));
tracker.clearAllCustomTags();
```
