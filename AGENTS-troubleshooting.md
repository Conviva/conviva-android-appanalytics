# Conviva Android App Analytics - Troubleshooting

Companion to AGENTS.md. Read only when a build failure or runtime issue occurs.

---

## Supported HTTP Clients

| Client | Supported |
|---|---|
| OkHttp | Yes |
| Retrofit (via OkHttp) | Yes |
| `HttpsURLConnection` | Yes |
| `HttpURLConnection` | Yes |
| `URL.getContent()` | No |
| `URL.openStream()` | No |

---

## Request / Response Body Collection

Collected only when all conditions are true:

| Condition | Detail |
|---|---|
| Size | < 10 KB and `Content-Length` present |
| Content-Type | `application/json` or `text/plain` |
| Format | `JSONObject`, nested `JSONObject`, or `JSONArray` |

---

## Request / Response Header Collection

Collected only when all conditions are true:

| Condition | Detail |
|---|---|
| Format | Flat `JSONObject` only (nested `JSONObject` / `JSONArray` not supported) |
| Server config | Server provisioned with `Access-Control-Expose-Headers` |

---

## Common Issues

**Issue 1 - Gradle sync fails to resolve Conviva artifacts**
Symptoms: `Could not find com.conviva.sdk:conviva-android-tracker:<version>` or `Could not find com.conviva.sdk:android-plugin:<version>`
Fix: Verify exact versions from GitHub Releases. Verify required repositories exist - do not edit repositories blocks. Ask developer to verify network / proxy configuration.

**Issue 2 - Wrong TrackerController import**
Use exactly: `com.conviva.apptracker.controller.TrackerController`

**Issue 3 - Wrong ConvivaAppAnalytics import**
Use exactly: `com.conviva.apptracker.ConvivaAppAnalytics`

**Issue 4 - Initialization in wrong place**
Place at end of `onCreate()`. If `super.onCreate()` is last, place above it. Revert any unrelated edits in `onCreate()`.

**Issue 5 - SDK obfuscated by ProGuard / R8**
Append if missing:
```proguard
-keepnames class * extends android.view.View
-keep,allowshrinking class com.conviva.** { *; }
```

**Issue 6 - Cronet / OkHttp instrumentation conflict**
Follow AGENTS.md Section 12. If issues persist after applying the fix, ask developer to confirm which OkHttpClient instances route through Cronet and verify interceptor ordering on each.
