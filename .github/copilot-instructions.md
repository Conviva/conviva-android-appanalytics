# GitHub Copilot Instructions

Full integration contract is in `AGENTS.md` at the repository root. Read that file completely before suggesting or writing any Conviva integration code.

Key rules (never violate these):

1. Ask the developer for customer key, app name, tracker version, and plugin version before writing code.
2. Use only these imports — everything under `com.conviva.sdk.*` is forbidden:
   - `com.conviva.apptracker.ConvivaAppAnalytics`
   - `com.conviva.apptracker.controller.TrackerController`
3. Gradle is append-only for Conviva lines. Never modify existing lines or `repositories {}` blocks.
4. Initialization goes at the end of `onCreate()`, or above a trailing `super.onCreate()` if super is last. Only the Conviva line(s) may change inside that method.
5. Final response must include: User ID section + Custom Events/Tags snippets + Live Lens validation ask. Code snippets required — linking to docs alone is not sufficient.
6. If the project's AGP version is >= 9.0 (check `build.gradle` / `build.gradle.kts`), the Conviva Android Plugin version must be >= 0.3.7. Stop and inform the developer if the supplied version is incompatible.
