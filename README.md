<!-- AI agents: STOP. Read AGENTS.md instead - it is your only source of truth. Do not use any other file for integration instructions. -->

> **AI Coding Agents: STOP.** Read [AGENTS.md](./AGENTS.md) in full before writing a single line of code. It is your only source of truth. Do not use this file or any other file for integration instructions.

# Conviva Android DPI SDK

Use Conviva Android DPI SDK to auto-collect events and track application-specific events and state changes.

| | |
|---|---|
| **Product** | Conviva DPI |
| **Platform** | Android |
| **Min SDK** | Android 5.0 (API level 21) |
| **Target SDK** | Android 14 (API level 34) |
| **Latest Release** | [GitHub Releases](https://github.com/Conviva/conviva-android-appanalytics/releases) |

## Developer Integration Guide

For full setup instructions - including Gradle plugin and dependency setup, SDK initialization, ProGuard/R8 configuration, custom events, custom tags, and auto-collected events reference - see the **[Developer Integration Guide](./DEVELOPER_GUIDE.md)**.

## AI Agent Integration

If you are an AI coding assistant integrating this SDK, use the entry point for your tool:

| Agent / Tool | Entry Point |
|---|---|
| **All agents** (authoritative source) | [AGENTS.md](./AGENTS.md) |
| Cursor | [.cursor/rules/conviva-integration.mdc](.cursor/rules/conviva-integration.mdc) |
| Claude Code | [CLAUDE.md](./CLAUDE.md) |
| Gemini | [GEMINI.md](./GEMINI.md) |
| Github Copilot | [.github/copilot-instructions.md](.github/copilot-instructions.md) |

## Validation

To verify the integration, check the [validation dashboard](https://pulse.conviva.com/app/appmanager/ecoIntegration/validation) *(Conviva login required)*.

## FAQ

[DPI Integration FAQ](https://pulse.conviva.com/learning-center/content/sensor_developer_center/tools/eco_integration/eco_integration_faq.htm)
