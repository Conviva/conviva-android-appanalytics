# Conviva Android App Analytics - Kotlin Snippets

Only read this file if the target project uses Kotlin. Imports are in AGENTS.md Section 9.

---

## Initialization

Append at the end of `onCreate()`. If `super.onCreate()` is the last line, insert above it. Add the import if not already present.

```kotlin
import com.conviva.apptracker.ConvivaAppAnalytics

ConvivaAppAnalytics.createTracker(this, "YOUR_CUSTOMER_KEY", "YOUR_APP_NAME")
```

---

## User ID

```kotlin
val tracker = ConvivaAppAnalytics.getTracker()
tracker?.subject?.userId = userId
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

Add the Conviva interceptor **before** the Cronet interceptor in the OkHttpClient builder:

```kotlin
builder.addInterceptor(OkHttp3Instrumentation.ConvivaNetworkInterceptor())
// existing Cronet interceptor follows here
```
