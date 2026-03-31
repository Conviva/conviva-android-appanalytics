# Conviva Android App Analytics - AI Integration Contract

Single source of truth. Governs: Cursor, Claude Code, Codex, ChatGPT, Gemini CLI, Copilot, Android Studio AI, and all other agents. This file always wins over any conflicting documentation.

## Workflow

1. State: "I have read AGENTS.md and will follow its contract."
2. Ask developer for all inputs in Section 3 before writing any code.
3. Seed task list from Section 16 before writing any code.
4. Execute Sections 4-15 in order. Every Section 16 row must appear in your response.
5. If you cannot proceed without violating a rule, stop and ask.

---

## 1. Non-Negotiable Rules

1. Use only APIs, imports, dependencies, and procedures defined in this file.
2. Do not invent, guess, or hallucinate Conviva APIs, class names, imports, packages, Gradle coordinates, or configuration.
3. Do not mix instructions from other Conviva SDKs (VSI, JS, iOS, legacy Android SDKs).
4. Prompt the developer for all required inputs. Never assume or hardcode them.
5. Apply minimal, localized changes only - no refactors, no comment cleanup, no unrelated edits.
6. PII Protection: Never pass email, phone, full name, IMEI, or IP address to Conviva. Use only non-PII identifiers (opaque provider UID, account ID, stored UUID). If none found, do not implement userId tracking - ask developer (see Sections 3c, 3d, 10).
7. If you cannot proceed without violating a rule, stop and ask.

---

## 2. SDK Identity

| Field | Value |
|---|---|
| Product | Conviva DPI |
| Platform | Android |
| SDK Name | Conviva Android App Analytics SDK |
| GitHub Repo | `Conviva/conviva-android-appanalytics` |
| Tracker Artifact | `com.conviva.sdk:conviva-android-tracker` |
| Plugin Artifact | `com.conviva.sdk:android-plugin` |
| Plugin ID | `com.conviva.sdk.android-plugin` |

---

## 3. Required Inputs - Ask Before Writing Any Code

| Input | Description |
|---|---|
| `CUSTOMER_KEY` | Conviva Customer Key - never guess or hardcode |
| `APP_NAME` | App name string passed to tracker initialization |
| `TRACKER_VERSION` | Exact tracker version from GitHub Releases |
| `PLUGIN_VERSION` | Exact Android plugin version from GitHub Releases |

Do not proceed without all four values. Also detect AGP version before writing Gradle code (see Section 5).

---

## 3a. Project Scanning - Read Each File Once

Do not re-read a file already in context. If a prior tool call or subagent has already returned a file's content, use it from context - do not open it again.

| File | Extract | How |
|---|---|---|
| `settings.gradle` / `settings.gradle.kts` | All `include` lines | Partial - `include` lines only; read this first |
| Root `build.gradle` / `build.gradle.kts` | `buildscript` block presence; AGP version; existing classpath | Full (typically < 200 lines) |
| Each module's `build.gradle` (first 10 lines) | `plugins {}` or `apply plugin:` block | Partial - first 10 lines per module; stop once app module found |
| App module `build.gradle` / `build.gradle.kts` | Top `plugins {}` block; `apply plugin:` lines; `proguardFiles` line; last ~25 lines of `dependencies {}` | Partial - first 30 lines + search for `proguardFiles` + last 25 lines |
| App module `src/main/AndroidManifest.xml` | `android:name` on `<application>` tag | Partial - first 20 lines |
| Application class (resolved from Manifest) | imports + full `onCreate()` | Partial - imports + `onCreate()` only |
| Auth hooks (login / logout) | Every function triggering login or logout at every layer; lowest-layer convergence point per operation | Targeted - grep first, then single partial read. Auth hooks may live in library modules - do not skip. |
| ProGuard file (from `proguardFiles` reference) | Confirm exists; end of file for appending | Partial - last 10 lines |

**Do NOT read:** `MainActivity`, activities, fragments, library module source files, test source sets, the full Application class file, beyond first 10 lines of any library module `build.gradle`, any file not in the table above. Exception: auth hooks must be found regardless of which module they are in.

**Subagent scope:** Return only: all `include` lines from settings; full root build file; first 10 lines of each module build file; app module build file (first 30 lines + last 25 lines of `dependencies {}`); `proguardFiles` line; `android:name` from app manifest; `onCreate()` body. Do not read library sources, activities, fragments, or non-app manifests.

---

## 3b. Multi-Module Detection

Read `settings.gradle` first. Project is multi-module if it contains more than one `include`.

App module: the module declaring `com.android.application` in its `plugins {}` block. Do not assume it is in `app/` - confirm by reading.

| Change type | Target |
|---|---|
| Conviva plugin classpath / plugins DSL | Root `build.gradle` or `settings.gradle` only |
| Conviva plugin application | App module `build.gradle` only - never library modules |
| Tracker `implementation` dependency | App module `build.gradle`. Also add to a library module `build.gradle` if auth hooks live there. |
| ProGuard / R8 rules | File referenced by `proguardFiles` in app module only |
| `createTracker(...)` call | Application class or MAIN/LAUNCHER Activity in app module only |

Library module `proguard-rules.pro` files must be ignored entirely - append Conviva rules only to the file referenced by `proguardFiles` in the app module `build.gradle`. If `proguardFiles` appears in a shared file (e.g., `apply from: '../extensions.gradle'`), search that file.

App manifest `android:name` may be relative (e.g., `".App"`). Resolve using `namespace` in the app module `build.gradle` or `package` in the app manifest. Application class is always in the app module source set.

---

## 3c. Auth Hook Scan - Discovery Only, No Implementation Yet

Grep all `.kt` and `.java` files for:
```
(login|logout|signIn|signOut|authenticate|signup|signUp|register|createUser|createAccount)
```
Record: file path, function name, line number.

For each login / registration method: trace all callers down the call chain to the single lowest-layer function where the actual auth provider call is made. This is the **convergence point** - place `userId = <non-PII id>` here only. If callers share no convergence point, place the call at each independent path.

For logout: trace all callers to the single lowest-layer function where the actual provider sign-out is called. This is the convergence point - place `userId = null` here only.

Registration / signup creates a new authenticated session - treat identically to login.

Do not read files yet - just identify convergence points. Proceed to Section 10 for implementation.

**If only PII identifiers found:** do not implement userId tracking. Report in Section 16: which methods were found, why each is unsafe, and instruct the developer: "No non-PII identifier available. Implement one of: (1) opaque provider UID, (2) custom UUID stored at first launch, (3) backend account ID. Do not use email, phone, or full name."

**Non-PII vs PII:**

| Identifier | PII? | Safe? |
|---|---|---|
| Opaque UID (Firebase UID, Auth0 ID, custom backend ID) | No | YES |
| Stored UUID (deterministic per user) | No | YES |
| Backend account ID (opaque numeric/alphanumeric) | No | YES |
| Email address | Yes | NO |
| Phone number | Yes | NO |
| Full name / display name | Yes | NO |
| IP address | Yes | NO |
| Device IMEI / IMSI / serial | Yes | NO |

---

## 3d. PII Rules

Never pass to Conviva: email, phone, full name, display name, IMEI, IMSI, serial number, IP address, or any personally identifiable information. If the only available identifier is PII, stop - do not implement userId tracking. Safe alternatives: opaque provider UID, UUID stored at first launch, backend account ID.

---

## 4. Core Rules

- Initialize exactly once: `ConvivaAppAnalytics.createTracker(context, key, name)` - no settings maps, config objects, builders, or extra arguments.
- Entry point: existing `Application` class from Manifest, or MAIN/LAUNCHER Activity if none. Never create a new `Application` class or modify `AndroidManifest.xml`.
- Insert Conviva call at end of `onCreate()`. If `super.onCreate()` is last, insert above it. No other changes inside `onCreate()`.
- Gradle changes are append-only - never modify, remove, or reorder existing lines. `repositories {}` blocks are read-only.
- Apply Conviva plugin in app module only. Library module `build.gradle` may receive tracker `implementation` if auth hooks live there, but never the plugin application.
- Import only from `com.conviva.apptracker.*` - never `com.conviva.sdk.*`. If something does not compile, stop and ask.
- Set `userId` immediately after `createTracker(...)` if a non-PII identifier is available. Update on login, registration, logout, and account switch. Never use PII. If no guest identifier exists, ask developer.
- Append ProGuard rules to existing file(s) only - never modify existing rules. Also append to `multidex-config.pro` if multidex is in use. If no ProGuard file exists, ask developer.

---

## 5. Gradle Integration

**AGP compatibility:**

| AGP Version | Supported |
|---|---|
| < 7.0 | Yes |
| 7.0 to < 7.2 | No |
| >= 7.2 | Yes |

**Plugin version constraint:**

| AGP Version | Min Conviva Plugin Version |
|---|---|
| < 9.0 | any |
| >= 9.0 | 0.3.7 |

If AGP >= 9.0 and `PLUGIN_VERSION` < 0.3.7, stop and inform the developer. Find AGP version via `com.android.tools.build:gradle:<version>` or `id("com.android.application") version "<version>"` in the root build file.

**Gradle style:**
- buildscript style: root `build.gradle` has `buildscript { dependencies { classpath ... } }` -> add classpath there + `apply plugin:` in app module. Never add Conviva to a `plugins {}` block in this style.
- plugins DSL style: versions declared in `settings.gradle` / `settings.gradle.kts` -> add `id ... apply false` in settings + `id(...)` in app `plugins {}`.

**Root / settings lines to append:**

| Style | File | Location | Line |
|---|---|---|---|
| buildscript / Groovy | root `build.gradle` | `buildscript > dependencies {}` | `classpath 'com.conviva.sdk:android-plugin:<PLUGIN_VERSION>'` |
| buildscript / Kotlin | root `build.gradle.kts` | `buildscript > dependencies {}` | `classpath("com.conviva.sdk:android-plugin:<PLUGIN_VERSION>")` |
| plugins DSL / Groovy | `settings.gradle` | `pluginManagement > plugins {}` | `id 'com.conviva.sdk.android-plugin' version '<PLUGIN_VERSION>' apply false` |
| plugins DSL / Kotlin | `settings.gradle.kts` | `pluginManagement > plugins {}` | `id("com.conviva.sdk.android-plugin") version "<PLUGIN_VERSION>" apply false` |

**App module lines to append (`<app-module>` = confirmed app module folder, not assumed):**

| DSL | File | Location | Line |
|---|---|---|---|
| Groovy (buildscript) | `<app-module>/build.gradle` | after existing `apply plugin:` lines | `apply plugin: 'com.conviva.sdk.android-plugin'` |
| Kotlin (plugins DSL) | `<app-module>/build.gradle.kts` | inside `plugins {}` | `id("com.conviva.sdk.android-plugin")` |
| Groovy | `<app-module>/build.gradle` | inside `dependencies {}` | `implementation 'com.conviva.sdk:conviva-android-tracker:<TRACKER_VERSION>'` |
| Kotlin | `<app-module>/build.gradle.kts` | inside `dependencies {}` | `implementation("com.conviva.sdk:conviva-android-tracker:<TRACKER_VERSION>")` |

---

## 6. ProGuard / R8

Append to the file referenced by `proguardFiles` in the app module `build.gradle`. Also append to `multidex-config.pro` if present in the app module. Never modify existing rules. Never append to library module ProGuard files.

```proguard
-keepnames class * extends android.view.View
-keep,allowshrinking class com.conviva.** { *; }
```

---

## 7. Initialization Point

1. Read app module `AndroidManifest.xml`.
2. If `<application android:name="...">` points to a custom class, use that class.
3. Otherwise use the `<activity>` with `android.intent.action.MAIN` + `android.intent.category.LAUNCHER`.
4. Never create a new `Application` class or modify `AndroidManifest.xml`.

---

## 8. Initialization Code

- Insert at end of `onCreate()`. If `super.onCreate()` is last, insert above it.
- Only the inserted Conviva lines may change inside `onCreate()`.
- Forbidden: `createTracker(context, key, name, settings)`, `createTracker(context, key, name, HashMap(...))`, any config/builder/extra-argument variant, `ConvivaSdkConstants`.
- Kotlin: see "Initialization" in `AGENTS-kotlin.md`.
- Java: see "Initialization" in `AGENTS-java.md`.

---

## 9. Allowed API Surface

**Dependencies:**
```
com.conviva.sdk:conviva-android-tracker
com.conviva.sdk:android-plugin
```

**Allowed imports (only these two):**
- `com.conviva.apptracker.ConvivaAppAnalytics`
- `com.conviva.apptracker.controller.TrackerController`

**Forbidden:** `com.conviva.sdk.*`, `com.conviva.*`, `ConvivaSdkConstants`, `settings`, `gateway`, `config`, `builder`, `HashMap` in initialization.

**Cronet fix import:**
- Kotlin: `import com.conviva.instrumentation.tracker.OkHttp3Instrumentation`
- Java: `import com.conviva.instrumentation.tracker.OkHttp3Instrumentation;`

**Allowed tracker methods:**

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

If a symbol does not compile using allowed imports, stop and report the error. Do not try alternate packages.

---

## 10. User ID

Complete Section 3c scan first. If only PII identifiers found, do not implement - report in Section 16 and stop.

**Safety checklist (per login / registration method):**
- [ ] Non-PII identifier confirmed (opaque provider UID, account ID, or stored UUID)
- [ ] Not email, phone, name, IMEI, IP address, or any PII
- [ ] Available immediately after successful login or registration
- [ ] Consistent across sessions (not fresh per session)

**Implementation:**
1. Trace all callers for each login / registration method to the lowest-layer convergence point.
2. At that point, obtain the non-PII identifier immediately after the auth call succeeds.
3. Use language-specific snippet: `AGENTS-kotlin.md` -> "User ID" or `AGENTS-java.md` -> "User ID".
4. Place `setUserId` once at the convergence point - not at each individual caller.
5. At the logout convergence point, set `userId = null`.

If auth hooks are in a library module: add tracker `implementation` to that module's `build.gradle` and place userId calls directly in the login / logout functions. Do not use auth state observers or global callbacks in the Application class as a substitute.

**Do not implement if:**
- Only email available -> PII
- Only phone available -> PII
- Only full name available -> PII
- No identifier available -> ask developer to define one

---

## 11. Custom Events and Custom Tags (Optional)

`HashMap` and `JSONObject` allowed only in event / tag payloads - never in initialization. See `AGENTS-kotlin.md` or `AGENTS-java.md` for snippets.

---

## 12. Cronet Compatibility Check

After Gradle and initialization changes, search for `CronetInterceptor`, `CronetEngine`, or `cronet` in Gradle dependencies.

**If not found:** record "No Cronet usage detected" and proceed to Section 13.

**If found - apply fix automatically, do not ask developer:**
1. Search all Kotlin and Java source files for `CronetInterceptor`. Record each file path and line number.
2. For each match, confirm it is inside an OkHttpClient builder chain.
3. Insert `builder.addInterceptor(OkHttp3Instrumentation.ConvivaNetworkInterceptor())` immediately before the Cronet interceptor line. Match surrounding indentation. Do not remove the Cronet interceptor line. Add the OkHttp3Instrumentation import if missing.
4. Record every file path and line number in your checklist.

See `AGENTS-kotlin.md` or `AGENTS-java.md` for language-specific Cronet snippets.

---

## 13. Build Verification

Ask developer to run a debug build. If it fails, share the error and fix using only Section 9 allow-listed symbols.

---

## 14. Product Validation

Ask developer to validate: Pulse App -> Activation Module -> Live Lens. Confirm: tracking events live, identity attribution correct, user journeys not split.

---

## 15. Troubleshooting

If build fails or runtime issues occur, read `AGENTS-troubleshooting.md`.

---

## 16. Mandatory Checklist

Seed your task list from this table before writing any code. Every row must appear in your final response.

| Row | Required Content |
|---|---|
| Multi-module detection | Multi-module or single; all modules from `settings.gradle`; confirmed app module; changes applied to correct module only |
| Summary of changes | Exact files changed and why |
| Gradle changes | Exact appended lines and target files |
| ProGuard / R8 | Exact rules added; which file; confirm no library module ProGuard files modified |
| Initialization placement | Entry point chosen and why |
| User ID setup | Login, registration, and logout implementation; or stop instructions if PII-only |
| Custom events and tags | One code snippet each |
| Cronet check | "No Cronet usage detected" or each file/line where fix was applied |
| Build verification | Outcome |
| AGP compatibility check | Detected AGP version; if >= 9.0, confirm plugin >= 0.3.7 |
| Product validation | Ask developer to validate in Pulse App -> Activation Module -> Live Lens |
