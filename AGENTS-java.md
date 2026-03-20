# Conviva Android App Analytics - Java Snippets

Only read this file if the target project uses Java. Imports are in AGENTS.md Section 9.

---

## Initialization

Append at the end of `onCreate()`. If `super.onCreate()` is the last line, insert above it. Add the import if not already present.

```java
import com.conviva.apptracker.ConvivaAppAnalytics;

ConvivaAppAnalytics.createTracker(this, "YOUR_CUSTOMER_KEY", "YOUR_APP_NAME");
```

---

## User ID

```java
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

---

## Cronet

Add the Conviva interceptor **before** the Cronet interceptor in the OkHttpClient builder:

```java
builder.addInterceptor(new OkHttp3Instrumentation.ConvivaNetworkInterceptor());
// existing Cronet interceptor follows here
```
