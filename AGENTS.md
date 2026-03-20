# Conviva Android App Analytics  - AI Integration Contract

> **Single source of truth for every AI coding assistant.**
> This file governs: Cursor, Claude Code, OpenAI Codex, ChatGPT, Gemini CLI, GitHub Copilot, Android Studio AI, and any other agent.
> If any other file or documentation in this repository conflicts with what is written here, **this file always wins**.

---

## How to use this file

When a developer asks you to integrate the Conviva Android App Analytics SDK:

1. Read this entire file before writing a single line of code.
2. **State the following before proceeding:** "I have read AGENTS.md for conviva-android-appanalytics and will follow its contract."
3. Ask the developer for all required inputs listed in Section 3.
4. **Before writing any code**, read Section 15 and create one task in your todo/task list for every row in that table. These rows are your required deliverables - do not skip any row.
5. Execute Sections 4 through 14 in order to fulfil each task you created in step 4.
6. Deliver a final response that covers every task from your Section 15 list - no row may be omitted.
7. If at any point you cannot proceed without violating a rule in this file, **stop and ask** the developer  - do not guess.

---

## 1. Non-Negotiable Rules

Every AI assistant MUST accept these rules before starting:

1. Use **only** the APIs, imports, dependencies, and procedures defined in this file.
2. Do **not** invent, guess, or hallucinate Conviva APIs, class names, imports, packages, Gradle coordinates, repositories, or configuration.
3. Do **not** mix instructions from other Conviva SDKs (VSI, JS, iOS, legacy Android SDKs).
4. **Prompt the developer** for all required inputs (customer key, app name, tracker version, plugin version). Never assume or hardcode them.
5. Apply **minimal, localized changes only**  - no refactors, no comment cleanup, no unrelated edits.
6. If you cannot proceed without violating these rules, **stop and ask**.

---

## 2. SDK Identity

| Field | Value |
|---|---|
| Product | Conviva DPI (Digital Product Intelligence) |
| Platform | Android |
| SDK Name | Conviva Android App Analytics SDK |
| GitHub Repo | `Conviva/conviva-android-appanalytics` |
| Tracker Artifact | `com.conviva.sdk:conviva-android-tracker` |
| Plugin Artifact | `com.conviva.sdk:android-plugin` |
| Plugin ID | `com.conviva.sdk.android-plugin` |

---

## 3. Required Inputs  - Ask the Developer Before Writing Any Code

| Input | Description |
|---|---|
| `CUSTOMER_KEY` | Conviva Customer Key  - never guess or hardcode |
| `APP_NAME` | App name string passed to tracker initialization |
| `TRACKER_VERSION` | Exact tracker version from GitHub Releases |
| `PLUGIN_VERSION` | Exact Android plugin version from GitHub Releases |

**Do not proceed without all four values.**

> **Before writing Gradle code:** Also detect the AGP version from the project's build files. If AGP >= 9.0, enforce the plugin version constraint in Section 5.

---

## 4. Do's and Don'ts

### Initialization

| Do | Don't |
|---|---|
| Initialize exactly once | Initialize multiple times |
| Read `AndroidManifest.xml` to find the entry point | Guess where to initialize |
| Use the existing `Application` class if manifest defines one | Create a new `Application` class |
| Use MAIN/LAUNCHER Activity if no Application class exists | Modify `AndroidManifest.xml` |
| Insert init at the end of `onCreate()` | Insert in the middle of startup logic |
| Insert above `super.onCreate()` if `super` is the last line | Place Conviva call after `super.onCreate()` when it is last |
| Change only the inserted Conviva line(s) inside `onCreate()` | Modify any other `onCreate()` lines or comments |
| Use only `ConvivaAppAnalytics.createTracker(...)` | Use settings maps, config objects, or builder patterns |

### Gradle Changes

| Do | Don't |
|---|---|
| Append Conviva lines only | Modify any existing lines |
| Replace a Conviva version string only if the exact same coordinate already exists | Replace or reorder unrelated dependencies |
| Leave `repositories {}` blocks completely untouched | Add or modify `mavenCentral()`, `google()`, `gradlePluginPortal()`, etc. |
| Apply Conviva plugin only in the app module | Apply plugin in root or library modules |

### Imports and APIs

| Do | Don't |
|---|---|
| Import `ConvivaAppAnalytics` from `com.conviva.apptracker.ConvivaAppAnalytics` | Import from `com.conviva.sdk.*` |
| Import `TrackerController` from `com.conviva.apptracker.controller.TrackerController` | Import `TrackerController` from `com.conviva.sdk.*` |
| Use only allow-listed APIs (Section 9) | Guess method names or try alternate packages |
| Stop and ask if something does not compile | Try alternate packages to force compilation |

### User ID

| Do | Don't |
|---|---|
| Set `userId` immediately after `createTracker(...)` if available | Delay userId unnecessarily |
| Update `userId` on login, logout, and account switch | Assume it never changes after first set |
| Use existing non-PII identifiers already in the app | Use PII (email, phone, full name) |
| If no guest identifier exists, recommend the developer define a policy | Implement guest-id generation in this integration |

### ProGuard / R8

| Do | Don't |
|---|---|
| Append Conviva rules to the existing ProGuard file(s) only | Modify or remove any existing ProGuard rules |
| Also append the same rules to `multidex-config.pro` if multidex is in use | Skip the multidex file when multidex is configured |
| Append only  - never reorder or reformat existing rules | Create a new ProGuard file if none exists  - ask the developer instead |

---

## 5. Gradle Integration

### AGP Compatibility

| AGP Version Range | Supported |
|---|---|
| Less than 7.0 | Yes |
| 7.0 up to (not including) 7.2 | No |
| 7.2 and above | Yes |

### Plugin Version Constraint by AGP Version

| AGP Version Range | Minimum Conviva Android Plugin Version |
|---|---|
| Less than 9.0 | Any supported version |
| 9.0 and above | 0.3.7 |

> **Rule:** If the application uses AGP >= 9.0, the Conviva Android Plugin version (`PLUGIN_VERSION`) **must be >= 0.3.7**.
> Before writing any Gradle code, check the AGP version in the project-level `build.gradle` / `build.gradle.kts` (look for `com.android.tools.build:gradle:<version>` or `id("com.android.application") version "<version>"`).
> If the developer-supplied `PLUGIN_VERSION` is below `0.3.7` and the detected AGP is >= 9.0, **stop and inform the developer**  - do not proceed with an incompatible version.

### Project-level `build.gradle`  - Groovy DSL

Append inside `buildscript > dependencies`:

```groovy
classpath 'com.conviva.sdk:android-plugin:<PLUGIN_VERSION>'
```

### Project-level `build.gradle.kts`  - Kotlin DSL

Append inside `buildscript > dependencies`:

```kotlin
classpath("com.conviva.sdk:android-plugin:<PLUGIN_VERSION>")
```

### `settings.gradle` / `settings.gradle.kts`  - plugins DSL projects

If the project uses the plugins DSL in settings, append the plugin declaration there and keep all existing repository configuration untouched.

**Groovy DSL:**
```groovy
id 'com.conviva.sdk.android-plugin' version '<PLUGIN_VERSION>' apply false
```

**Kotlin DSL:**
```kotlin
id("com.conviva.sdk.android-plugin") version "<PLUGIN_VERSION>" apply false
```

### App `build.gradle`  - Groovy DSL

Append the plugin apply at the top of the app module file:

```groovy
apply plugin: 'com.conviva.sdk.android-plugin'
```

Append inside `dependencies {}`:

```groovy
implementation 'com.conviva.sdk:conviva-android-tracker:<TRACKER_VERSION>'
```

### App `build.gradle.kts`  - Kotlin DSL

Append the plugin inside `plugins {}`:

```kotlin
id("com.conviva.sdk.android-plugin")
```

Append inside `dependencies {}`:

```kotlin
implementation("com.conviva.sdk:conviva-android-tracker:<TRACKER_VERSION>")
```

### Common Gradle Anti-Patterns  - Never Do These

The most frequent AI mistake is applying the wrong Gradle style. Before writing any Gradle code, check whether the root build file uses `buildscript { dependencies { classpath ... } }` (buildscript style) or declares plugin versions inside `settings.gradle` / `settings.gradle.kts` (plugins DSL style). Each style requires a different approach.

**buildscript style  - wrong vs correct**

WRONG: **WRONG**  - adding to the `plugins {}` block in root `build.gradle`:
```groovy
// root build.gradle  - WRONG
plugins {
    id 'com.conviva.sdk.android-plugin' version '0.3.7' apply false  // <-- never do this
}
```

CORRECT: **CORRECT**  - appending inside `buildscript > dependencies {}`:
```groovy
// root build.gradle  - CORRECT
buildscript {
    dependencies {
        classpath 'com.conviva.sdk:android-plugin:<PLUGIN_VERSION>'  // <-- append here
    }
}
```

WRONG: **WRONG**  - adding inside the `plugins {}` block in `app/build.gradle`:
```groovy
// app/build.gradle  - WRONG
plugins {
    id 'com.conviva.sdk.android-plugin'  // <-- never do this for buildscript-style projects
}
```

CORRECT: **CORRECT**  - using the legacy apply statement in `app/build.gradle`:
```groovy
// app/build.gradle  - CORRECT
apply plugin: 'com.conviva.sdk.android-plugin'  // <-- append after existing apply lines
```

**plugins DSL style  - `settings.gradle.kts` projects**

For projects that declare plugin versions in `settings.gradle` / `settings.gradle.kts`, add the `apply false` declaration there and apply inside the app `plugins {}` block  - this is the only case where the `plugins {}` block approach is correct. See Section 5 for the exact syntax.

### Gradle Policy  - Enforced

- `repositories {}` blocks are **read-only**  - never add or change them.
- All Conviva dependency edits are **append-only**.
- Replace a version string only if the exact same Conviva coordinate already exists.
- Do not reformat or reorder any existing lines.
- Apply the Conviva plugin only in the app module, except for the `apply false` declaration in plugins-DSL settings when that project style is already in use.

---

## 6. ProGuard / R8 Rules

### How to Find the Correct File(s)

1. Open the app module `build.gradle` / `build.gradle.kts`.
2. Look for `proguardFiles`  - add rules to the referenced file, typically `proguard-rules.pro`.
3. Check whether `multidex-config.pro` exists. If it does, add the same rules there too.

### Rules to Add

Append the following rules to `proguard-rules.pro` (and to `multidex-config.pro` if present):

```proguard
-keepnames class * extends android.view.View
-keep,allowshrinking class com.conviva.** { *; }
```

### ProGuard Policy  - Enforced

- Append only  - do not modify or remove any existing ProGuard rules.
- Only add these rules to the existing ProGuard files already used by the project.
- If neither file exists, ask the developer whether ProGuard / R8 is enabled before proceeding.

---

## 7. Finding the Right Initialization Point

1. Open `AndroidManifest.xml`.
2. Look at the `<application>` tag  - if `android:name` points to a custom `Application` class, **use that class**.
3. If there is no custom `Application` class, find the `<activity>` that has both `android.intent.action.MAIN` and `android.intent.category.LAUNCHER` intent filters  - **use that Activity**.
4. Do **not** create a new `Application` class.
5. Do **not** modify `AndroidManifest.xml`.

---

## 8. Initialization Code

### Insertion Rule

- Place the Conviva call at the **end of `onCreate()`**.
- Exception: if `super.onCreate(...)` is the very last line, insert the Conviva call **above** it.
- The **only** changes inside `onCreate()` are the inserted Conviva line(s). No other lines may change.

### Kotlin  - Application class (super is not last)

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

### Kotlin  - Application class (super is last line  - insert above it)

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

### Java  - Application class (super is not last)

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

### Java  - Application class (super is last line  - insert above it)

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

### Forbidden Initialization Patterns

The following patterns are forbidden  - never generate them:

```
ConvivaAppAnalytics.createTracker(context, key, name, settings)
ConvivaAppAnalytics.createTracker(context, key, name, HashMap(...))
ConvivaAppAnalytics.createTracker(context, key, name, config)
ConvivaAppAnalytics.createTracker(context, key, name, builder)
ConvivaSdkConstants.*
```

Replace `YOUR_CUSTOMER_KEY` and `YOUR_APP_NAME` with the actual values from Section 3.

---

## 9. Allowed API Surface

### Allowed Dependencies

```
com.conviva.sdk:conviva-android-tracker
com.conviva.sdk:android-plugin
```

### Allowed Imports

```java
import com.conviva.apptracker.ConvivaAppAnalytics;
import com.conviva.apptracker.controller.TrackerController;
```

```kotlin
import com.conviva.apptracker.ConvivaAppAnalytics
import com.conviva.apptracker.controller.TrackerController
```

### Forbidden Imports  - Never Use

```
com.conviva.sdk.*
com.conviva.sdk.ConvivaSdkConstants
com.conviva.sdk.controller.TrackerController
com.conviva.*
```

### Forbidden Patterns in Initialization Code

```
ConvivaSdkConstants
settings
gateway
config
builder
HashMap      <-- forbidden in init/config; allowed only in custom event/tag payloads
```

### Allowed Tracker Methods

| Method | Purpose |
|---|---|
| `ConvivaAppAnalytics.createTracker(...)` | Initialize (once) |
| `ConvivaAppAnalytics.getTracker()` | Get tracker instance |
| `tracker.subject.userId` (Kotlin) | Set user ID |
| `tracker.getSubject().setUserId(...)` (Java) | Set user ID |
| `tracker.trackCustomEvent(...)` | Custom event |
| `tracker.setCustomTags(...)` | Set custom tags |
| `tracker.clearCustomTags(...)` | Clear specific tags |
| `tracker.clearAllCustomTags()` | Clear all tags |

If a symbol does not compile using the allow-listed imports, **stop and report the error**. Do not try alternate packages.

---

## 10. User ID

Set `userId` immediately after `createTracker(...)` if a non-PII identifier is already available at that point. Update on every login, logout, and account switch.

### Kotlin

```kotlin
import com.conviva.apptracker.ConvivaAppAnalytics
import com.conviva.apptracker.controller.TrackerController

val tracker = ConvivaAppAnalytics.getTracker()
tracker?.subject?.userId = userId
```

### Java

```java
import com.conviva.apptracker.ConvivaAppAnalytics;
import com.conviva.apptracker.controller.TrackerController;

TrackerController tracker = ConvivaAppAnalytics.getTracker();
tracker.getSubject().setUserId(userId);
```

### Rules

- `userId` must be **non-PII**  - no email addresses, phone numbers, or full names.
- Set `userId` for both **guest** and **logged-in** users.
- Update `userId` on guest-to-logged-in transitions and on account switches.
- If the app has no guest identifier, tell the developer to define a non-PII guest identifier policy  - **do not implement guest-id generation yourself**.

### If You Cannot Safely Find Auth Hooks

Tell the developer explicitly:

> You must add userId reporting yourself. Set userId immediately after tracker initialization and update it on login, logout, and account switches using the snippets above.
>
> Conviva recommendation: set userId for both guest and logged-in users using non-PII identifiers. If the app does not have a guest identifier, define a non-PII guest identifier policy  - do not implement guest-id generation in this integration.

---

## 11. Post-Integration  - Custom Events and Custom Tags (Optional)

After core integration is complete, the developer may optionally enrich analytics with custom events and custom tags.

> `HashMap` and `JSONObject` are allowed **only** in the payloads below  - never in initialization or configuration.

### Custom Events  - Kotlin

```kotlin
val tracker = ConvivaAppAnalytics.getTracker()
val eventData = JSONObject().apply {
    put("identifier1", intValue)
    put("identifier2", boolValue)
    put("identifier3", "stringValue")
}
tracker?.trackCustomEvent("your-event-name", eventData)
```

### Custom Events  - Java

```java
TrackerController tracker = ConvivaAppAnalytics.getTracker();
JSONObject eventData = new JSONObject();
eventData.put("identifier1", intValue);
eventData.put("identifier2", boolValue);
eventData.put("identifier3", "stringValue");
tracker.trackCustomEvent("your-event-name", eventData);
```

### Custom Tags  - Kotlin

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

### Custom Tags  - Java

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

## 12. Build Verification

After applying all changes, prompt the developer to verify the build themselves by opening the target Android project and running a debug build to confirm the integration compiles successfully.

> If compilation fails, share the error output. Fix failures using only the allow-listed symbols in Section 9. Do not try alternate packages.

---

## 13. Product Validation

After a successful build, ask the developer to validate the integration:

**Navigate to: Pulse App -> Activation Module -> Live Lens**

Confirm that:
- Tracking events are live.
- Identity attribution is correct (guest vs logged-in).
- User journeys are not split across multiple anonymous users.

---

## 14. Known Limitations and Troubleshooting

### Known Limitations

#### Supported HTTP Clients

| Client | Supported |
|---|---|
| OkHttp | Yes |
| Retrofit (via OkHttp) | Yes |
| `HttpsURLConnection` | Yes |
| `HttpURLConnection` | Yes |
| `URL.getContent()` | No |
| `URL.openStream()` | No |

Network requests made via `URL.getContent()` and `URL.openStream()` are not instrumented.

#### Request and Response Body Collection

Body data is collected only when all of the following are true:

| Condition | Detail |
|---|---|
| Size | Less than 10 KB and `Content-Length` is present |
| Content-Type | `application/json` or `text/plain` |
| Data format | `JSONObject`, nested `JSONObject`, or `JSONArray` |

#### Request and Response Header Collection

Header data is collected only when all of the following are true:

| Condition | Detail |
|---|---|
| Data format | Flat `JSONObject` only |
| Server config | Server is provisioned with `Access-Control-Expose-Headers` |

Nested `JSONObject` and `JSONArray` header payloads are not yet supported.

### Troubleshooting

#### Issue 1  - Gradle sync fails to resolve Conviva artifacts

**Symptoms:**
- `Could not find com.conviva.sdk:conviva-android-tracker:<version>`
- `Could not find com.conviva.sdk:android-plugin:<version>`
- Gradle sync fails after adding Conviva lines

**Fixes:**
1. Verify the exact tracker and plugin versions from GitHub Releases.
2. Verify that required repositories already exist in the project. If not, ask the developer to add them  - the AI must not edit repositories blocks.
3. Ask the developer to verify network or proxy configuration.

#### Issue 2  - Wrong TrackerController import

Use exactly:

```java
import com.conviva.apptracker.controller.TrackerController;
```

```kotlin
import com.conviva.apptracker.controller.TrackerController
```

#### Issue 3  - Wrong ConvivaAppAnalytics import

Use exactly:

```java
import com.conviva.apptracker.ConvivaAppAnalytics;
```

```kotlin
import com.conviva.apptracker.ConvivaAppAnalytics
```

#### Issue 4  - Initialization inserted in the wrong place in `onCreate()`

- The only change inside `onCreate()` must be the inserted Conviva line(s).
- Place at end of method; if `super.onCreate(...)` is last, place Conviva above it.
- Revert any unrelated edits in `onCreate()`.

#### Issue 5  - Conviva SDK obfuscated by ProGuard / R8

Append if missing:

```proguard
-keepnames class * extends android.view.View
-keep,allowshrinking class com.conviva.** { *; }
```

#### Issue 6  - Cronet plus OkHttp instrumentation conflicts

If the app uses both Cronet and OkHttp, verify the interceptor chain and ensure Conviva instrumentation is attached to the actual client handling the request path. If request capture appears partial or duplicated, ask the developer to confirm which stack is authoritative for production traffic before making further edits.

---

## 15. Mandatory Checklist - Seed Your Task List From This Table Before Writing Any Code

**Before writing any code**, create one task in your todo/task list for every row below. Execute Sections 4-14 to complete each task. Every row must appear in your final response - no row may be omitted.

| Section | Required Content |
|---|---|
| Summary of changes | Exact files changed and why |
| Gradle changes | Exact appended lines and which files received them |
| ProGuard / R8 | Exact rules added and where |
| Initialization placement | Why this entry point was chosen from `AndroidManifest.xml` |
| User ID setup | What was implemented, or exact instructions if auth hooks were unclear |
| Custom events and tags | Brief explanation plus at least one code snippet each |
| Build verification | Commands run and outcomes |
| AGP compatibility check | Confirm detected AGP version; if >= 9.0, confirm plugin version is >= 0.3.7 |
| Product validation | Explicit ask to validate in Pulse App -> Activation Module -> Live Lens |

---

## 16. AI Agent System Prompt Block

Use this block when configuring any AI agent for this repository:

```
You are an AI coding assistant working inside the Conviva/conviva-android-appanalytics repository.

AUTHORITATIVE SOURCE OF TRUTH:
- Read AGENTS.md at the repository root in full before writing any code.

NON-NEGOTIABLE BEHAVIOR:
- Do not guess or invent APIs, dependencies, imports, repositories, or Gradle changes.
- Do not modify repositories blocks anywhere.
- Gradle changes are append-only for Conviva lines; do not change anything else.
- Code changes must be minimal and localized; no refactors or comment cleanup.
- If blocked, stop and ask for the missing required inputs.

WORKFLOW:
1. Ask for required inputs first: customer key, app name, exact tracker version, exact plugin version.
2. Read Section 15 of AGENTS.md and create one todo task for every row in that table before writing any code.
3. Implement the integration strictly per AGENTS.md (Sections 4-14) to fulfil each task from step 2.
4. Prompt the developer to verify the build in their target Android project. Fix any reported failures using only allow-listed symbols.
5. Ask the developer to validate: Pulse App -> Activation Module -> Live Lens.
6. Final response must cover every task from the Section 15 list - no row may be omitted.
```
