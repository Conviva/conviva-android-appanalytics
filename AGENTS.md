# Conviva Android App Analytics  - AI Integration Contract

> **Single source of truth for every AI coding assistant.**
> This file governs: Cursor, Claude Code, OpenAI Codex, ChatGPT, Gemini CLI, GitHub Copilot, Android Studio AI, and any other agent.
> If any other file or documentation in this repository conflicts with what is written here, **this file always wins**.

---

## How to use this file

When a developer asks you to integrate the Conviva Android App Analytics SDK:

1. **State:** "I have read AGENTS.md for conviva-android-appanalytics and will follow its contract."
2. Collect all required inputs (Section 3). Gather project context per **Section 3a** - read each file once, partial reads only.
3. Seed your task list from Section 16 **before writing any code**.
4. Execute Sections 4-15 in order. Every Section 16 row must appear in your final response.
5. If you cannot proceed without violating a rule, **stop and ask** - do not guess.

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

## 3a. Efficient Project Scanning - Read Each File Once

Before writing any code, gather exactly the context listed below. These rules exist to avoid token waste from reading irrelevant files, reading full files when only a section is needed, and re-reading files that are already in context.

### No-Re-Read Rule

> If a subagent, exploration tool, or prior tool call has already returned the content of a file, **do not open that file again**. Use the content already in your context window. Every duplicate read is wasted context.

### What to Read and How Much

| File | What to extract | How to read |
|---|---|---|
| Root `build.gradle` / `build.gradle.kts` | Presence of `buildscript { dependencies { classpath ... } }` block; AGP version (`com.android.tools.build:gradle:<version>`); existing classpath lines | Full file - root build files are typically short (< 200 lines) |
| `app/build.gradle` / `app/build.gradle.kts` | Top `plugins {}` block; any `apply plugin:` lines; the `proguardFiles` line; last ~25 lines of `dependencies {}` | **Partial read** - first 30 lines + a targeted search for `proguardFiles` + last 25 lines. Do not read the full file. |
| `app/src/main/AndroidManifest.xml` | The `android:name` attribute on the `<application>` tag | **Partial read** - first 20 lines are sufficient in most projects |
| Application class (resolved from Manifest) | `import` block + full `onCreate()` method - specifically its first line and last line | **Partial read** - target only `onCreate()` and the imports at the top of the file |
| Auth hooks (login / logout) | The single function called on login (e.g. `storeAccount`), the single function called on logout (e.g. `clearAccount`) | **Single targeted read** - read lines covering both functions in one pass rather than separate reads |
| ProGuard file (from `proguardFiles` reference) | Confirm file exists; find the end of the file for appending | **Partial read** - last 10 lines only |

### What NOT to Read

- Do **not** read `MainActivity` or any Activity unless the Manifest shows no custom `Application` class.
- Do **not** read library modules, test source sets, or any file not listed in the table above.
- Do **not** open `settings.gradle` / `settings.gradle.kts` unless no `buildscript` block is found in the root `build.gradle`.
- Do **not** read the full Application class file. Target `onCreate()` only.

### Subagent / Exploration Tool Scope

If you use a subagent or codebase exploration tool to gather initial context, instruct it to return **only**:
- Root `build.gradle` contents
- `app/build.gradle` first 30 lines + last 25 lines of `dependencies {}`
- The `android:name` value from `AndroidManifest.xml`
- The `onCreate()` body from the Application class

Do **not** ask the subagent to read `MainActivity`, activity files, fragment files, or any file outside this list.

---

## 4. Rules

- Initialize exactly once using only `ConvivaAppAnalytics.createTracker(context, key, name)` - no settings maps, config objects, or builders.
- Read `AndroidManifest.xml` to find the entry point. Use the existing `Application` class, or the MAIN/LAUNCHER Activity if none exists. Never create a new `Application` class or modify `AndroidManifest.xml`.
- Insert the Conviva call at the end of `onCreate()`. If `super.onCreate()` is the last line, insert above it. Change only the inserted line(s) - no other modifications.
- Gradle changes are **append-only** - never modify, remove, or reorder existing lines. `repositories {}` blocks are read-only.
- Apply the Conviva plugin only in the app module (not root or library modules).
- Import only from `com.conviva.apptracker.*` - never from `com.conviva.sdk.*`. If something does not compile, stop and ask.
- Set `userId` immediately after `createTracker(...)` if a non-PII identifier is available. Update on login, logout, and account switch. Never use PII (email, phone, full name). If no guest identifier exists, ask the developer to define a policy.
- Append ProGuard rules to existing file(s) only - never modify existing rules. Also append to `multidex-config.pro` if multidex is in use. If no ProGuard file exists, ask the developer.

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

### Detecting the Gradle Style

- **buildscript style:** root `build.gradle` contains `buildscript { dependencies { classpath ... } }` -> add classpath to that block + `apply plugin:` in the app module. **Never** add Conviva to a `plugins {}` block in this style.
- **plugins DSL style:** plugin versions declared in `settings.gradle` / `settings.gradle.kts` -> add `id ... apply false` in settings + `id(...)` in app `plugins {}` block.

### Lines to Append - by Style and File

| Style | File | Location | Append |
|---|---|---|---|
| buildscript / Groovy | root `build.gradle` | `buildscript > dependencies {}` | `classpath 'com.conviva.sdk:android-plugin:<PLUGIN_VERSION>'` |
| buildscript / Kotlin | root `build.gradle.kts` | `buildscript > dependencies {}` | `classpath("com.conviva.sdk:android-plugin:<PLUGIN_VERSION>")` |
| plugins DSL / Groovy | `settings.gradle` | `pluginManagement > plugins {}` | `id 'com.conviva.sdk.android-plugin' version '<PLUGIN_VERSION>' apply false` |
| plugins DSL / Kotlin | `settings.gradle.kts` | `pluginManagement > plugins {}` | `id("com.conviva.sdk.android-plugin") version "<PLUGIN_VERSION>" apply false` |

**App module - all styles:**

| DSL | File | Location | Append |
|---|---|---|---|
| Groovy (buildscript) | `app/build.gradle` | after existing `apply plugin:` lines | `apply plugin: 'com.conviva.sdk.android-plugin'` |
| Kotlin (plugins DSL) | `app/build.gradle.kts` | inside `plugins {}` | `id("com.conviva.sdk.android-plugin")` |
| Groovy | `app/build.gradle` | inside `dependencies {}` | `implementation 'com.conviva.sdk:conviva-android-tracker:<TRACKER_VERSION>'` |
| Kotlin | `app/build.gradle.kts` | inside `dependencies {}` | `implementation("com.conviva.sdk:conviva-android-tracker:<TRACKER_VERSION>")` |

---

## 6. ProGuard / R8 Rules

Find the ProGuard file via the `proguardFiles` line in `app/build.gradle` (typically `proguard-rules.pro`). Append these two rules to it. If `multidex-config.pro` also exists, append there too. Append-only - never modify existing rules. If no ProGuard file exists, ask the developer.

```proguard
-keepnames class * extends android.view.View
-keep,allowshrinking class com.conviva.** { *; }
```

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

### Language-Specific Snippets

Detect the project language, then fetch **only one**:
- Kotlin projects (`.kt` files in app/src) -> read the "Initialization" sections in `AGENTS-kotlin.md`
- Java projects (`.java` files in app/src) -> read the "Initialization" sections in `AGENTS-java.md`

### Forbidden Initialization Patterns

Never generate: `createTracker(context, key, name, settings)`, `createTracker(context, key, name, HashMap(...))`, `createTracker(context, key, name, config)`, `createTracker(context, key, name, builder)`, or any use of `ConvivaSdkConstants`.

---

## 9. Allowed API Surface

### Allowed Dependencies

```
com.conviva.sdk:conviva-android-tracker
com.conviva.sdk:android-plugin
```

### Allowed Imports

Only these two classes may be imported (use the Java or Kotlin syntax as appropriate):
- `com.conviva.apptracker.ConvivaAppAnalytics`
- `com.conviva.apptracker.controller.TrackerController`

### Forbidden Imports and Patterns

Never import from `com.conviva.sdk.*` or `com.conviva.*`. Never use `ConvivaSdkConstants`, `settings`, `gateway`, `config`, `builder`, or `HashMap` in initialization code (HashMap is allowed only in custom event/tag payloads).

### Cronet Interceptor Import

When applying the Cronet fix (Section 12), add the following import to the affected file if not already present:
- Kotlin: `import com.conviva.instrumentation.tracker.OkHttp3Instrumentation`
- Java: `import com.conviva.instrumentation.tracker.OkHttp3Instrumentation;`

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

Set `userId` immediately after `createTracker(...)` if a non-PII identifier is already available. Update on every login, logout, and account switch. For the language-specific snippet, see the "User ID" section in `AGENTS-kotlin.md` or `AGENTS-java.md`.

- `userId` must be **non-PII** - no email addresses, phone numbers, or full names.
- Set `userId` for both **guest** and **logged-in** users.
- If the app has no guest identifier, tell the developer to define a non-PII guest identifier policy - do not implement guest-id generation yourself.
- If you cannot safely find auth hooks, tell the developer they must add userId reporting themselves.

---

## 11. Post-Integration - Custom Events and Custom Tags (Optional)

After core integration is complete, the developer may optionally enrich analytics with custom events and custom tags. `HashMap` and `JSONObject` are allowed **only** in these payloads - never in initialization or configuration.

For language-specific snippets, see the "Custom Events" and "Custom Tags" sections in `AGENTS-kotlin.md` or `AGENTS-java.md`.

---

## 12. Cronet Compatibility Check

Conviva auto-instruments OkHttp clients via bytecode instrumentation at build time, automatically adding a Conviva interceptor to the OkHttpClient interceptor chain. When Google's Cronet Transport for OkHttp is also present, the `CronetInterceptor` short-circuits the chain - any interceptor added **after** it is silently skipped. This means Conviva's auto-instrumented interceptor may never execute, and **network request auto-detection will not work** for Cronet-routed traffic.

### Detection - Always Perform This Scan

After completing Gradle and initialization changes, **before build verification**, scan the project for Cronet usage:

1. Search the codebase for `CronetInterceptor`, `CronetEngine`, or `cronet` in Gradle dependency declarations.
2. If **none found** - record "No Cronet usage detected" in your checklist and proceed to Section 13. No further action is needed.
3. If **Cronet is found** - this section becomes mandatory. Execute the automatic remediation steps below immediately. Do not ask the developer - do not skip.

### Remediation - Automatic Fix When Cronet Is Detected

**Do not ask the developer.** Apply the fix automatically:

1. **Find every source file** that calls `addInterceptor(...)` with a Cronet interceptor. Search across all Kotlin and Java source files for `CronetInterceptor` usages (class names, constructor calls, builder calls). Record the exact file path and line number of each match.

2. **For each match**, do a targeted read of the surrounding lines to confirm it is inside an OkHttpClient builder chain.

3. **Insert** `builder.addInterceptor(OkHttp3Instrumentation.ConvivaNetworkInterceptor())` on the line **immediately before** the line that adds the Cronet interceptor. Use the language-specific snippet from the **Cronet** section in `AGENTS-kotlin.md` or `AGENTS-java.md`.
   - Preserve the exact indentation of the surrounding lines.
   - Do **not** remove or modify the existing Cronet interceptor line.
   - Add `import com.conviva.instrumentation.tracker.OkHttp3Instrumentation` to the import block of the affected file if it is not already present (use the correct syntax for the file's language - see `AGENTS-kotlin.md` or `AGENTS-java.md`).

4. **Record in your checklist** every file path and line number where the fix was applied.

---

## 13. Build Verification

After applying all changes, prompt the developer to verify the build themselves by opening the target Android project and running a debug build to confirm the integration compiles successfully.

> If compilation fails, share the error output. Fix failures using only the allow-listed symbols in Section 9. Do not try alternate packages.

---

## 14. Product Validation

After a successful build, ask the developer to validate the integration:

**Navigate to: Pulse App -> Activation Module -> Live Lens**

Confirm that:
- Tracking events are live.
- Identity attribution is correct (guest vs logged-in).
- User journeys are not split across multiple anonymous users.

---

## 15. Known Limitations and Troubleshooting

If the build fails or runtime issues occur, fetch and read `AGENTS-troubleshooting.md` in this repository for known limitations (HTTP client support, body/header collection constraints) and common fixes (wrong imports, Gradle resolution failures, ProGuard issues).

---

## 16. Mandatory Checklist - Seed Your Task List From This Table Before Writing Any Code

**Before writing any code**, create one task in your todo/task list for every row below. Execute Sections 4-15 to complete each task. Every row must appear in your final response - no row may be omitted.

| Section | Required Content |
|---|---|
| Summary of changes | Exact files changed and why |
| Gradle changes | Exact appended lines and which files received them |
| ProGuard / R8 | Exact rules added and where |
| Initialization placement | Why this entry point was chosen from `AndroidManifest.xml` |
| User ID setup | What was implemented, or exact instructions if auth hooks were unclear |
| Custom events and tags | Brief explanation plus at least one code snippet each |
| Cronet compatibility check | State whether Cronet was detected. If not found, record "No Cronet usage detected." If found, list every file path and line number where `OkHttp3Instrumentation.ConvivaNetworkInterceptor()` was automatically inserted before the Cronet interceptor |
| Build verification | Commands run and outcomes |
| AGP compatibility check | Confirm detected AGP version; if >= 9.0, confirm plugin version is >= 0.3.7 |
| Product validation | Explicit ask to validate in Pulse App -> Activation Module -> Live Lens |

