# Conviva Android App Analytics - Java Snippets

Only read this file if the target project uses Java. Imports are in AGENTS.md Section 9.

---

## Initialization

Insert at end of `onCreate()`. If `super.onCreate()` is last, insert above it. Add the import if not already present.

```java
import com.conviva.apptracker.ConvivaAppAnalytics;

ConvivaAppAnalytics.createTracker(this, "YOUR_CUSTOMER_KEY", "YOUR_APP_NAME");
```

---

## User ID

Place immediately after successful login or registration at the convergence point.

```java
TrackerController tracker = ConvivaAppAnalytics.getTracker();
tracker.getSubject().setUserId(userId);
```

Clear on logout:

```java
TrackerController tracker = ConvivaAppAnalytics.getTracker();
tracker.getSubject().setUserId(null);
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

---

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

Only apply after AGENTS.md Section 12 detection confirms Cronet is present. Insert immediately before the Cronet interceptor line, matching surrounding indentation.

```java
builder.addInterceptor(new OkHttp3Instrumentation.ConvivaNetworkInterceptor());
```

Add import if not present:

```java
import com.conviva.instrumentation.tracker.OkHttp3Instrumentation;
```
