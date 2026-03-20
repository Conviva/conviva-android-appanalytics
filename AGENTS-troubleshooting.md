# Conviva Android App Analytics - Troubleshooting

Companion file to AGENTS.md. Only fetch this file if a build failure or runtime issue occurs.

---

## Known Limitations

### Supported HTTP Clients

| Client | Supported |
|---|---|
| OkHttp | Yes |
| Retrofit (via OkHttp) | Yes |
| `HttpsURLConnection` | Yes |
| `HttpURLConnection` | Yes |
| `URL.getContent()` | No |
| `URL.openStream()` | No |

### Request and Response Body Collection

Body data is collected only when all of the following are true:

| Condition | Detail |
|---|---|
| Size | Less than 10 KB and `Content-Length` is present |
| Content-Type | `application/json` or `text/plain` |
| Data format | `JSONObject`, nested `JSONObject`, or `JSONArray` |

### Request and Response Header Collection

Header data is collected only when all of the following are true:

| Condition | Detail |
|---|---|
| Data format | Flat `JSONObject` only |
| Server config | Server is provisioned with `Access-Control-Expose-Headers` |

Nested `JSONObject` and `JSONArray` header payloads are not yet supported.

---

## Troubleshooting

### Issue 1 - Gradle sync fails to resolve Conviva artifacts

**Symptoms:**
- `Could not find com.conviva.sdk:conviva-android-tracker:<version>`
- `Could not find com.conviva.sdk:android-plugin:<version>`

**Fixes:**
1. Verify the exact tracker and plugin versions from GitHub Releases.
2. Verify that required repositories already exist in the project. If not, ask the developer to add them - the AI must not edit repositories blocks.
3. Ask the developer to verify network or proxy configuration.

### Issue 2 - Wrong TrackerController import

Use exactly: `com.conviva.apptracker.controller.TrackerController`

### Issue 3 - Wrong ConvivaAppAnalytics import

Use exactly: `com.conviva.apptracker.ConvivaAppAnalytics`

### Issue 4 - Initialization inserted in the wrong place in `onCreate()`

- The only change inside `onCreate()` must be the inserted Conviva line(s).
- Place at end of method; if `super.onCreate(...)` is last, place Conviva above it.
- Revert any unrelated edits in `onCreate()`.

### Issue 5 - Conviva SDK obfuscated by ProGuard / R8

Append if missing:

```proguard
-keepnames class * extends android.view.View
-keep,allowshrinking class com.conviva.** { *; }
```

### Issue 6 - Cronet plus OkHttp instrumentation conflicts

> **This is now a pre-build check.** See Section 12 of AGENTS.md. The scan for Cronet is always performed; remediation is mandatory only when Cronet is detected in the project.

If Cronet-related issues persist after following Section 12 (e.g. partial request capture or duplicated events), ask the developer to confirm which OkHttpClient instances route traffic through Cronet and verify the interceptor ordering on each one.
