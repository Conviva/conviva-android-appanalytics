# Conviva Android App Analytics - Kotlin Snippets

Only read this file if the target project uses Kotlin. Imports are in AGENTS.md Section 9.

---

## Initialization

Insert at end of `onCreate()`. If `super.onCreate()` is last, insert above it. Add the import if not already present.

```kotlin
import com.conviva.apptracker.ConvivaAppAnalytics

ConvivaAppAnalytics.createTracker(this, "YOUR_CUSTOMER_KEY", "YOUR_APP_NAME")
```

---

## User ID

Place immediately after successful login or registration at the convergence point.

```kotlin
val tracker = ConvivaAppAnalytics.getTracker()
tracker?.subject?.userId = userId
```

Clear on logout:

```kotlin
val tracker = ConvivaAppAnalytics.getTracker()
tracker?.subject?.userId = null
```

---

## Custom Events

```kotlin
val tracker = ConvivaAppAnalytics.getTracker()
val eventData = JSONObject().apply {
    put("identifier1", intValue)
    put("identifier2", boolValue)
    put("identifier3", "stringValue")
}
tracker?.trackCustomEvent("your-event-name", eventData)
```

---

## Custom Tags

```kotlin
val tracker = ConvivaAppAnalytics.getTracker()
val tags = hashMapOf<String, Any>(
    "key1" to intValue,
    "key2" to boolValue,
    "key3" to "stringValue"
)
tracker?.setCustomTags(tags)

tracker?.clearCustomTags(listOf("key1", "key2"))
tracker?.clearAllCustomTags()
```

---

## Cronet

Only apply after AGENTS.md Section 12 detection confirms Cronet is present. Insert immediately before the Cronet interceptor line, matching surrounding indentation.

```kotlin
builder.addInterceptor(OkHttp3Instrumentation.ConvivaNetworkInterceptor())
```

Add import if not present:

```kotlin
import com.conviva.instrumentation.tracker.OkHttp3Instrumentation
```
