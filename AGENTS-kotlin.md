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

> Only reach this section after following the automatic remediation steps in AGENTS.md Section 12. Do not ask the developer - apply the fix directly.

### What to detect

Search all Kotlin source files for any `addInterceptor(...)` call whose argument constructs a Cronet interceptor (e.g. `CronetInterceptor`, `MixinCronetInterceptor`, or any class whose name contains `Cronet` and implements `Interceptor`).

### What to insert

On the line **immediately before** every such `addInterceptor(...)` call, insert the following line, matching the surrounding indentation exactly:

```kotlin
builder.addInterceptor(OkHttp3Instrumentation.ConvivaNetworkInterceptor())
```

Also add the following import to the file's import block if it is not already present:

```kotlin
import com.conviva.instrumentation.tracker.OkHttp3Instrumentation
```

### Example - before

```kotlin
if (engine != null) {
    builder.addInterceptor(MixinCronetInterceptor.newBuilder(engine).build())
}
```

### Example - after

```kotlin
if (engine != null) {
    builder.addInterceptor(OkHttp3Instrumentation.ConvivaNetworkInterceptor())
    builder.addInterceptor(MixinCronetInterceptor.newBuilder(engine).build())
}
```
