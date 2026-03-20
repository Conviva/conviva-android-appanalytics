# Conviva Android App Analytics — Kotlin Snippets

Companion file to AGENTS.md. Only read this file if the target project uses Kotlin.

---

## Initialization — Application class (super is not last)

```kotlin
import com.conviva.apptracker.ConvivaAppAnalytics

class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()
        // ... existing startup code ...
        ConvivaAppAnalytics.createTracker(this, "YOUR_CUSTOMER_KEY", "YOUR_APP_NAME")
    }
}
```

## Initialization — Application class (super is last line — insert above it)

```kotlin
import com.conviva.apptracker.ConvivaAppAnalytics

class MyApp : Application() {
    override fun onCreate() {
        // ... existing startup code ...
        ConvivaAppAnalytics.createTracker(this, "YOUR_CUSTOMER_KEY", "YOUR_APP_NAME")
        super.onCreate()
    }
}
```

Replace `YOUR_CUSTOMER_KEY` and `YOUR_APP_NAME` with the actual values from Section 3 of AGENTS.md.

---

## User ID

```kotlin
import com.conviva.apptracker.ConvivaAppAnalytics
import com.conviva.apptracker.controller.TrackerController

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
