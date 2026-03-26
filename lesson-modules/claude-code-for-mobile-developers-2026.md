# Claude Code for Mobile Developers (2026)

> Deep research report for company course: iOS, Android, and Cross-platform developers

---

## Table of Contents

1. [Current Tech Stack in 2026](#1-current-tech-stack-in-2026)
2. [Claude Code for iOS Development](#2-claude-code-for-ios-development)
3. [Claude Code for Android Development](#3-claude-code-for-android-development)
4. [Claude Code for Cross-Platform Development](#4-claude-code-for-cross-platform-development)
5. [MCP Servers for Mobile](#5-mcp-servers-for-mobile)
6. [Hooks for Mobile Development](#6-hooks-for-mobile-development)
7. [Custom Skills/Commands for Mobile](#7-custom-skillscommands-for-mobile)
8. [Headless Automation (`claude -p`)](#8-headless-automation-claude--p)
9. [GitHub Actions for Mobile](#9-github-actions-for-mobile)
10. [Mobile-Specific Challenges](#10-mobile-specific-challenges)
11. [Real-World Examples & Case Studies](#11-real-world-examples--case-studies)
12. [Sources](#sources)

---

## 1. Current Tech Stack in 2026

### iOS Development

| Component | Current Version (March 2026) | Notes |
|-----------|------------------------------|-------|
| **Swift** | 6.3 (swiftlang-6.3.0.123.5) | Swift 6 strict concurrency is the standard |
| **Xcode** | 26.4 (Build 17E192) | Native Claude agent integration since 26.3 |
| **SwiftUI** | Mature, dominant framework | UIKit considered legacy; SwiftUI is the floor |
| **SwiftData** | Stable | Preferred over Core Data for new projects |
| **Swift Testing** | Stable, preferred | Replaces XCTest for new test suites |
| **SPM** | Default package manager | CocoaPods declining; SPM is standard |
| **Concurrency** | async/await + structured concurrency | Swift 6.2 Inline Arrays, improved checking |
| **iOS SDK** | iOS 26 | Xcode 26 SDK required for App Store submissions from April 2026 |
| **Foundation Models** | New in iOS 26 | On-device AI processing framework |

Key developments:
- Xcode 26.3 introduced native agentic coding with Claude and Codex as agent runtimes
- Xcode 17 (beta) has instant previews that behave like the real app
- New SwiftUI instrument for visualizing how data changes affect view updates
- Opt-in compilation caching for faster build/test cycles
- Apple requires all App Store submissions to use Xcode 26 and iOS 26 SDK from April 2026

### Android Development

| Component | Current Version (March 2026) | Notes |
|-----------|------------------------------|-------|
| **Kotlin** | 2.3.0 (in Gradle) / 2.2.x (app-level) | K2 compiler stable and default |
| **Jetpack Compose** | 1.9+ (1.10/1.11 expected at I/O) | Powers 60% of top Play Store apps |
| **Android Studio** | Latest with Gemini integration | |
| **Gradle** | 9.4.1 | Kotlin DSL standard; Java 26 support |
| **AGP** | 9.0.0 | Android Gradle Plugin |
| **Compose Multiplatform** | 1.10.0 | Unified @Preview, Navigation 3, stable Hot Reload |
| **Min SDK** | 24 typical | Target SDK 36 |
| **Material Design** | Material 3 with dynamic colors | Adaptive UI scaffolds |
| **Navigation** | Navigation 3 (type-safe) | Replaces older navigation patterns |

Key developments:
- K2 compiler gives faster builds and better type analysis for Compose
- Kotlin 2.2's strong-skip mode is reshaping how recomposition works
- Experimental APIs in Compose reduced from 172 to 70 (v1.8) then settled further in v1.9
- XML layouts are effectively dead for new projects
- Compose Multiplatform for web is ready for early adopters

### Cross-Platform Landscape

| Framework | Market Share | Strengths | AI-Readiness |
|-----------|-------------|-----------|--------------|
| **Flutter** | ~46% | Near-native performance, consistent UI | Good Dart support in Claude |
| **React Native / Expo** | 35-42% | Massive JS ecosystem, web talent reuse | Excellent Claude Code support with Expo MCP |
| **Kotlin Multiplatform** | ~23% (up from 7% in 18 months) | True native performance, shared logic | Good, growing skill ecosystem |
| **Compose Multiplatform** | Growing with KMP | 80-90% UI code sharing, Skia rendering on iOS | Strong Kotlin skills apply |

Key trends:
- Companies increasingly use hybrid approaches (e.g., KMP for business logic + platform-native UI)
- React Native developers command highest salaries ($145K average) but are easiest to find
- Kotlin Multiplatform has Google's backing and fastest adoption growth
- Expo SDK is at version 55 with mature EAS (Expo Application Services)

### Build Systems & CI/CD

| Tool | Platform | Status in 2026 |
|------|----------|----------------|
| **SPM** | iOS | Default, integrated with Xcode |
| **Gradle (Kotlin DSL)** | Android | 9.4.1 with convention plugins |
| **Tuist / XcodeGen** | iOS | Popular for project generation (avoids pbxproj) |
| **Fastlane** | Both | Still widely used; MCP server available |
| **GitHub Actions** | Both | First-class Claude Code integration |
| **Bitrise** | Both | Mobile-focused CI/CD |
| **EAS Build** | React Native | Expo's cloud build service with MCP tools |

### Testing Frameworks

| Framework | Platform | Purpose |
|-----------|----------|---------|
| **Swift Testing** | iOS | Modern replacement for XCTest |
| **XCTest / XCUITest** | iOS | UI testing, still standard |
| **JUnit 5** | Android | Unit testing |
| **Espresso** | Android | UI testing |
| **Compose Testing** | Android | Compose-specific UI tests |
| **Maestro** | Both | YAML-based E2E, low flakiness, AI-integrated |
| **Detox** | React Native | Gray-box testing |
| **Appium** | Both | Cross-platform E2E |
| **Turbine** | Android | Kotlin Flow testing |
| **swift-snapshot-testing** | iOS | Snapshot/visual regression |

---

## 2. Claude Code for iOS Development

### 2.1 Initial Setup

**Install Claude Code:**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Install XcodeBuildMCP (the essential iOS MCP server):**
```bash
brew tap getsentry/xcodebuildmcp && brew install xcodebuildmcp
```

Or add to `.mcp.json`:
```json
{
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "npx",
      "args": ["-y", "xcodebuildmcp@latest", "mcp"]
    }
  }
}
```

**Install ios-simulator-mcp (for UI interaction):**
```bash
claude mcp add ios-simulator npx ios-simulator-mcp
```

**Install Swift Agent Skills:**
```bash
npx skills add https://github.com/twostraws/swiftui-agent-skill --skill swiftui-pro
npx skills add https://github.com/twostraws/Swift-Testing-Agent-Skill
npx skills add https://github.com/twostraws/SwiftData-Agent-Skill
npx skills add https://github.com/twostraws/Swift-Concurrency-Agent-Skill
```

**Install Apple Platform Build Tools Plugin:**
```
/plugin marketplace add kylehughes/apple-platform-build-tools-claude-code-plugin
/plugin install apple-platform-build-tools@apple-platform-build-tools-claude-code-plugin
```

### 2.2 CLAUDE.md for iOS Projects

```markdown
# Project: MyApp (iOS)

## Quick Reference
- Platform: iOS 18+ / SwiftUI
- Language: Swift 6.3 (strict concurrency)
- Architecture: MVVM-C (Model-View-ViewModel-Coordinator)
- Package Manager: SPM
- Persistence: SwiftData
- Networking: async/await + URLSession
- DI: Factory pattern
- Testing: Swift Testing + swift-snapshot-testing
- Minimum deployment: iOS 18.0

## Build & Run
- Build: Use XcodeBuildMCP tools (never raw xcodebuild)
- Test: Use XcodeBuildMCP test tools
- Scheme: MyApp
- Simulator: iPhone 16 Pro

## Project Structure
```
Sources/
  App/           # App entry point, AppDelegate
  Features/      # Feature modules (one folder per feature)
    Auth/
      AuthView.swift
      AuthViewModel.swift
      AuthCoordinator.swift
      AuthRepository.swift
  Core/
    Networking/   # API client, URLSession extensions
    Persistence/  # SwiftData models and stores
    UI/           # Shared SwiftUI components
    Extensions/   # Swift extensions
Resources/        # Assets, Localizable.xcstrings
Tests/
  UnitTests/
  SnapshotTests/
```

## Coding Standards
- Use Swift 6 strict concurrency (@Sendable, actor isolation)
- Prefer @Observable over ObservableObject (Swift 5.9+)
- Extract views exceeding 100 lines into subviews
- Use typed errors conforming to LocalizedError
- No force unwraps without justification
- No UIKit unless wrapping legacy components
- Use NavigationStack with type-safe routing
- Access control: explicit `internal`/`private`/`public`
- Use #Preview macro for all views

## SwiftUI Patterns
- @State for local view state
- @Environment for dependency injection
- @Bindable for @Observable model binding
- ViewModifier for reusable view modifications
- Prefer GeometryReader alternatives when possible

## NEVER
- Edit .pbxproj files directly (use Xcode GUI)
- Edit .xcodeproj or .xcworkspace files
- Use deprecated APIs (foregroundColor(), ObservableObject, etc.)
- Write UITests during scaffolding phase
- Force unwrap optionals without comment
- Commit code that doesn't compile (build first)
```

**Feature-level CLAUDE.md** (e.g., `Sources/Features/Auth/CLAUDE.md`):
```markdown
# Auth Feature
- Uses Apple Sign In + email/password
- AuthViewModel handles all auth state
- Token stored in Keychain via KeychainService
- Session refresh handled by AuthInterceptor
```

### 2.3 Settings Configuration

`.claude/settings.json`:
```json
{
  "permissions": {
    "allow": [
      "Bash(swift build *)",
      "Bash(swift test *)",
      "Bash(swiftlint *)",
      "Bash(swift-format *)",
      "Bash(xcodebuild -scheme * -destination 'platform=iOS Simulator,*' build)",
      "Bash(xcrun simctl *)",
      "Bash(cat *)",
      "Bash(find *)",
      "Bash(grep *)",
      "Bash(git status *)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "mcp__XcodeBuildMCP__*",
      "mcp__ios-simulator__*",
      "Read",
      "Write",
      "Edit",
      "Glob",
      "Grep"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push *)",
      "Bash(git rebase *)",
      "Bash(pod install)",
      "Edit(.*.pbxproj)"
    ]
  }
}
```

### 2.4 XcodeBuildMCP Configuration

`.xcodebuildmcp/config.yaml`:
```yaml
schemaVersion: 1
enabledWorkflows:
  - simulator
  - ui-automation
  - debugging
sessionDefaults:
  scheme: MyApp
  projectPath: ./MyApp.xcodeproj
  simulatorName: iPhone 16 Pro
```

XcodeBuildMCP provides **59 tools** including:
- `simulator/build` - Build for simulator
- `simulator/build-and-run` - Build and launch on simulator
- `simulator/test` - Run test suite
- `simulator/screenshot` - Capture simulator screenshot
- `debugging/attach` - Attach LLDB debugger
- `debugging/breakpoint` - Set breakpoints
- `ui-automation/tap` - Tap UI elements
- `ui-automation/swipe` - Swipe gestures

### 2.5 What Claude Code Can Automate for iOS

| Task | How | Tool/Command |
|------|-----|-------------|
| **New screen scaffolding** | Custom `/feature` skill | Generates View, ViewModel, Coordinator, tests |
| **Build & run** | XcodeBuildMCP | `mcp__XcodeBuildMCP__simulator_build_and_run` |
| **Run tests** | XcodeBuildMCP | `mcp__XcodeBuildMCP__simulator_test` |
| **Take screenshots** | XcodeBuildMCP or ios-simulator-mcp | `mcp__XcodeBuildMCP__simulator_screenshot` |
| **UI interaction** | ios-simulator-mcp | `ui_tap`, `ui_swipe`, `ui_type` |
| **SwiftUI previews** | Xcode MCP integration | Visual preview capture from CLI |
| **Code linting** | Bash hook | `swiftlint --fix` |
| **Code formatting** | Bash hook | `swift-format format -i` |
| **Localization** | Read/Write Localizable.xcstrings | Extract and organize strings |
| **API client generation** | From OpenAPI spec | Generate Swift Codable models + URLSession calls |
| **Dependency management** | SPM | Edit Package.swift |
| **Version bumping** | Bash + agvtool | `agvtool new-marketing-version`, `agvtool new-version` |
| **Asset catalog** | Read/Write | Generate/modify Contents.json |
| **Snapshot tests** | Generate test files | swift-snapshot-testing patterns |
| **Crash analysis** | Crashlytics MCP | Fetch crashes, analyze stacktraces |
| **Design-to-code** | Figma MCP | Convert Figma frames to SwiftUI |

---

## 3. Claude Code for Android Development

### 3.1 Initial Setup

**Install Claude Code:**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Install the Android Ninja skill (comprehensive):**
```bash
# Option 1: Manual
git clone https://github.com/Drjacky/claude-android-ninja.git ~/.claude/skills/claude-android-ninja/

# Option 2: OpenSkills CLI
npx openskills install drjacky/claude-android-ninja --global
npx openskills sync
```

**Or install the simpler Android skill:**
```bash
git clone https://github.com/dpconde/claude-android-skill.git ~/.claude/skills/claude-android-skill/
```

**Install Firebase MCP (if using Firebase):**
```bash
# Recommended: Use the official Firebase plugin
claude /plugin install firebase
```

Or add to `.mcp.json`:
```json
{
  "mcpServers": {
    "firebase": {
      "command": "npx",
      "args": ["-y", "firebase-tools@latest", "experimental:mcp"]
    }
  }
}
```

**Install mobile-mcp (cross-platform device interaction):**
```json
{
  "mcpServers": {
    "mobile-mcp": {
      "command": "npx",
      "args": ["-y", "@mobilenext/mobile-mcp@latest"]
    }
  }
}
```

### 3.2 CLAUDE.md for Android Projects

```markdown
# Project: MyApp (Android)

## Quick Reference
- Language: Kotlin 2.2+ (K2 compiler)
- UI: Jetpack Compose + Material 3
- Architecture: MVVM with Unidirectional Data Flow (UDF)
- DI: Hilt
- Persistence: Room 3
- Networking: Retrofit + Kotlinx Serialization
- Concurrency: Kotlin Coroutines + Flow
- Navigation: Navigation 3 (type-safe)
- Build: Gradle 9.x with Kotlin DSL + Convention Plugins
- Testing: JUnit 5, Turbine, Compose Testing
- Min SDK: 24, Target SDK: 36

## Build & Run
```bash
# Build debug
./gradlew assembleDebug

# Run tests
./gradlew test

# Run connected tests
./gradlew connectedAndroidTest

# Lint check
./gradlew detekt

# Format
./gradlew ktlintFormat
```

## Module Structure
```
app/                        # Application entry point, Hilt setup
feature/
  featurename/
    api/                    # Navigation contracts (public)
    impl/                   # Implementation (internal)
      ui/                   # Composables
      viewmodel/            # ViewModels
core/
  data/                     # Repository implementations
  database/                 # Room entities, DAOs, migrations
  network/                  # Retrofit API definitions
  model/                    # Domain models
  ui/                       # Shared Compose components
  designsystem/             # Theme, colors, typography
  testing/                  # Test utilities, fakes
build-logic/
  convention/               # Gradle convention plugins
```

## Coding Standards
- Offline-first: Room is the single source of truth
- UDF: Events flow down, state flows up
- All data exposed as Flow<T> from repositories
- ViewModels use StateFlow with SharingStarted.WhileSubscribed(5_000)
- Composables: stateless screens, state hoisting to Route level
- Use collectAsStateWithLifecycle() (not collectAsState)
- Feature modules are self-contained with api/impl split
- Use @Immutable and @Stable for compose stability
- No mocking frameworks -- use fakes and interfaces

## Compose Patterns
- Route pattern: @Composable FeatureRoute wraps FeatureScreen
- Route calls hiltViewModel() and collects state
- Screen is a pure function taking UiState + event lambdas
- 8dp spacing tokens from design system
- Dynamic colors from Material 3

## NEVER
- Use XML layouts (Compose only)
- Use LiveData (use StateFlow)
- Use RxJava (use Coroutines + Flow)
- Put business logic in Composables
- Use GlobalScope
- Use mocking frameworks (use fakes)
- Hardcode strings (use string resources)
```

### 3.3 Settings Configuration

`.claude/settings.json`:
```json
{
  "permissions": {
    "allow": [
      "Bash(./gradlew *)",
      "Bash(gradle *)",
      "Bash(adb *)",
      "Bash(ktlint *)",
      "Bash(detekt *)",
      "Bash(cat *)",
      "Bash(find *)",
      "Bash(grep *)",
      "Bash(git status *)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "mcp__firebase__*",
      "mcp__mobile-mcp__*",
      "Read",
      "Write",
      "Edit",
      "Glob",
      "Grep"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push *)",
      "Bash(git rebase *)"
    ]
  }
}
```

### 3.4 Android Ninja Skill Coverage

The `claude-android-ninja` skill (by Drjacky) is the most comprehensive Android skill available. It covers:

- **Architecture**: Feature-first modular design, domain/data/UI layering
- **Jetpack Compose**: Material 3 theming, adaptive UI, state management, animations
- **Navigation 3**: Type-safe routing, adaptive navigation for phones/tablets/foldables
- **Testing**: Fakes + Hilt DI testing, Room 3 testing, Compose Preview Screenshot Testing, macrobenchmarks
- **Gradle**: Convention plugins, version catalogs, KSP, build performance
- **Security**: Play Integrity, Credential Manager, certificate pinning
- **Performance**: Baseline Profiles, StrictMode, Google Play Vitals thresholds
- **Accessibility**: TalkBack, semantic properties, WCAG alignment
- **Migration paths**: XML to Compose, LiveData to StateFlow, RxJava to Coroutines

Tech specs: Kotlin 2.2.21, AGP 9.0.0, Min SDK 24, Target SDK 36.

### 3.5 What Claude Code Can Automate for Android

| Task | How | Tool/Command |
|------|-----|-------------|
| **New feature module** | Custom skill or android-ninja | Generates api/, impl/, ViewModel, Screen, tests |
| **Build** | Bash | `./gradlew assembleDebug` |
| **Run tests** | Bash | `./gradlew test` or `./gradlew connectedAndroidTest` |
| **Lint/format** | Bash hook | `./gradlew detekt` / `./gradlew ktlintFormat` |
| **Room migration** | Edit migration files | Generate migration SQL + entity updates |
| **API client** | From OpenAPI spec | Generate Retrofit interfaces + Kotlinx Serialization models |
| **Navigation setup** | Edit nav graph | Add type-safe routes and screens |
| **Dependency management** | Edit `libs.versions.toml` | Update version catalogs |
| **Version bump** | Edit build.gradle.kts | Update versionCode/versionName |
| **String resources** | Read/Write XML | Extract hardcoded strings to `strings.xml` |
| **Compose themes** | Generate theme files | Material 3 color schemes, typography |
| **Firebase setup** | Firebase MCP | Auth, Firestore, Storage config |
| **Crash analysis** | Crashlytics MCP | Fetch and analyze crash reports |
| **Play Store deploy** | play-store-mcp | Deploy APK/AAB, promote releases |
| **Screenshot tests** | Compose Preview Screenshot Testing | Generate and verify visual snapshots |

### 3.6 Standard ViewModel Pattern (for Skills)

```kotlin
@HiltViewModel
class FeatureViewModel @Inject constructor(
    private val repository: FeatureRepository,
) : ViewModel() {
    val uiState: StateFlow<FeatureUiState> = repository
        .getData()
        .map { FeatureUiState.Success(it) }
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5_000),
            initialValue = FeatureUiState.Loading,
        )
}

sealed interface FeatureUiState {
    data object Loading : FeatureUiState
    data class Success(val data: List<Model>) : FeatureUiState
    data class Error(val message: String) : FeatureUiState
}
```

---

## 4. Claude Code for Cross-Platform Development

### 4.1 React Native / Expo

**This is the best-supported cross-platform option for Claude Code in 2026.**

**Install Expo MCP Server:**
```bash
# Requires EAS paid plan
# Run /mcp in Claude Code session to authenticate
```

Add to `.mcp.json`:
```json
{
  "mcpServers": {
    "expo": {
      "command": "npx",
      "args": ["-y", "@expo/mcp@latest"]
    }
  }
}
```

**Install the Expo toolkit plugin:**
```bash
# From: github.com/rahulkeerthi/expo-toolkit
# Covers project init through App Store submission
```

**Install React Native best practices skill:**
```bash
# From Callstack (official React Native partner)
# Provides patterns that Claude auto-applies to Expo codebases
```

**Agent system for Expo (7 agents):**

The `claude-code-reactnative-expo-agent-system` provides:
1. **Grand Architect** - Feature planning and delegation
2. **Design Token Guardian** - Design system compliance
3. **A11y Compliance Enforcer** - WCAG 2.2 validation
4. **Smart Test Generator** - Auto-generates test suites
5. **Performance Budget Enforcer** - Performance metrics
6. **Performance Prophet** - Predictive analysis
7. **Security Penetration Specialist** - OWASP Mobile Top 10

Slash commands: `/feature`, `/review`, `/test`

### 4.2 Kotlin Multiplatform / Compose Multiplatform

Claude Code works well with KMP since it is fundamentally Kotlin-based. The android-ninja skill applies to the shared Kotlin logic. For the iOS-specific parts, the SwiftUI skills apply to the native iOS layer.

Key setup for KMP:
```markdown
# CLAUDE.md for KMP project

## Structure
```
shared/
  commonMain/    # Shared Kotlin code
  androidMain/   # Android-specific
  iosMain/       # iOS-specific
androidApp/      # Android app module
iosApp/          # iOS app (Xcode project)
```

## Build Commands
```bash
# Shared module
./gradlew :shared:build

# Android
./gradlew :androidApp:assembleDebug

# iOS (requires XcodeBuildMCP)
# Use XcodeBuildMCP for iosApp builds
```

## Key Libraries
- Ktor for networking
- Room/SQLDelight for persistence
- Koin for DI
- Compose Multiplatform for shared UI (80-90% code sharing)
```

### 4.3 Flutter

Flutter has strong Claude Code support through standard Dart tooling. No specialized MCP server exists yet, but Claude can run:
```bash
flutter build ios
flutter build apk
flutter test
flutter analyze
dart format .
```

---

## 5. MCP Servers for Mobile

### Essential MCP Servers

| MCP Server | Platform | What It Does | Install Command |
|------------|----------|-------------|-----------------|
| **XcodeBuildMCP** | iOS/macOS | Build, test, debug, UI automation, screenshots (59 tools) | `brew install getsentry/xcodebuildmcp/xcodebuildmcp` |
| **ios-simulator-mcp** | iOS | Simulator screenshots, UI tap/swipe/type, accessibility tree | `claude mcp add ios-simulator npx ios-simulator-mcp` |
| **mobile-mcp** | iOS + Android | Platform-agnostic device automation, accessibility snapshots | `npx -y @mobilenext/mobile-mcp@latest` |
| **Firebase MCP** | Both | Auth, Firestore, Storage, Crashlytics (30+ tools) | `npx -y firebase-tools@latest experimental:mcp` |
| **Figma MCP** | Both | Design-to-code, pull variables/components/layouts | Built into Figma Dev Mode |
| **Expo MCP** | React Native | EAS Build, Workflows, simulator interaction, SDK docs | `npx -y @expo/mcp@latest` |
| **Play Store MCP** | Android | Deploy APK/AAB, promote releases, get status | Java JAR with service account |
| **Fastlane MCP** | Both | Build, test, deploy, certificates, metadata | `github.com/lyderdev/fastlane-mcp-server` |
| **Sentry MCP** | Both | Crash analysis, issue management, debugging | Official Sentry MCP server |
| **Crashlytics MCP** | Both | Crash reports via BigQuery, AI-powered analysis | `github.com/tjdam007/mcp-crashlytics-server` |
| **Maestro MCP** | Both | YAML-based E2E test automation, AI-assisted | Built into Maestro |

### MCP Configuration Example (`.mcp.json` for iOS project)

```json
{
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "npx",
      "args": ["-y", "xcodebuildmcp@latest", "mcp"]
    },
    "ios-simulator": {
      "command": "npx",
      "args": ["-y", "ios-simulator-mcp@latest"]
    },
    "firebase": {
      "command": "npx",
      "args": ["-y", "firebase-tools@latest", "experimental:mcp"]
    },
    "figma": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/figma-mcp-server@latest"]
    }
  }
}
```

### MCP Configuration Example (`.mcp.json` for Android project)

```json
{
  "mcpServers": {
    "mobile-mcp": {
      "command": "npx",
      "args": ["-y", "@mobilenext/mobile-mcp@latest"]
    },
    "firebase": {
      "command": "npx",
      "args": ["-y", "firebase-tools@latest", "experimental:mcp"]
    },
    "play-store-mcp": {
      "command": "java",
      "args": ["-jar", "/path/to/play-store-mcp-all.jar"],
      "env": {
        "PLAY_STORE_SERVICE_ACCOUNT_KEY_PATH": "/path/to/service-account-key.json",
        "PLAY_STORE_DEFAULT_TRACK": "internal"
      }
    },
    "figma": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/figma-mcp-server@latest"]
    }
  }
}
```

### Xcode MCP Integration (Xcode 26.3+)

Apple now exposes Xcode capabilities through MCP. Developers using Claude Code can integrate with Xcode over MCP and capture visual SwiftUI Previews without leaving the CLI. Configuration lives at:
```
~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig/.claude.json
```

---

## 6. Hooks for Mobile Development

### iOS Hooks

`.claude/settings.json` (hooks section):
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/swiftlint-fix.sh",
            "timeout": 30
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Edit",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/block-pbxproj.sh",
            "timeout": 5
          }
        ]
      },
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/auto-approve-safe-ios.sh",
            "timeout": 5
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/verify-build.sh",
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

**`.claude/hooks/swiftlint-fix.sh`** (auto-lint after edits):
```bash
#!/bin/bash
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // .tool_input.file_path // ""')

if [[ "$FILE_PATH" == *.swift ]]; then
  swiftlint lint --fix --path "$FILE_PATH" 2>/dev/null
  if swiftlint lint --path "$FILE_PATH" --quiet 2>/dev/null | grep -q "warning\|error"; then
    ISSUES=$(swiftlint lint --path "$FILE_PATH" --quiet 2>/dev/null)
    jq -n --arg ctx "SwiftLint issues found:\n$ISSUES" '{
      hookSpecificOutput: {
        hookEventName: "PostToolUse",
        additionalContext: $ctx
      }
    }'
  fi
fi
exit 0
```

**`.claude/hooks/block-pbxproj.sh`** (prevent pbxproj edits):
```bash
#!/bin/bash
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // ""')

if [[ "$FILE_PATH" == *.pbxproj ]] || [[ "$FILE_PATH" == *.xcodeproj/* ]] || [[ "$FILE_PATH" == *.xcworkspace/* ]]; then
  jq -n '{
    hookSpecificOutput: {
      hookEventName: "PreToolUse",
      permissionDecision: "deny",
      permissionDecisionReason: "BLOCKED: Never edit Xcode project files directly. Create the source file and add it to the target via Xcode GUI."
    }
  }'
  exit 0
fi
exit 0
```

**`.claude/hooks/auto-approve-safe-ios.sh`** (auto-approve safe commands):
```bash
#!/bin/bash
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // ""')

if echo "$COMMAND" | grep -qE '^(swift build|swift test|swiftlint|swift-format|xcrun simctl list|xcodebuild -showBuildSettings)'; then
  jq -n '{
    hookSpecificOutput: {
      hookEventName: "PreToolUse",
      permissionDecision: "allow",
      permissionDecisionReason: "Safe iOS build/test command"
    }
  }'
  exit 0
fi
exit 0
```

**`.claude/hooks/verify-build.sh`** (verify build before stopping):
```bash
#!/bin/bash
# Build the project before allowing Claude to stop
BUILD_OUTPUT=$(xcodebuild -scheme MyApp -destination 'platform=iOS Simulator,name=iPhone 16 Pro' build 2>&1)
if [ $? -ne 0 ]; then
  echo "Build failed. Fix errors before stopping." >&2
  exit 2  # Block stopping
fi
exit 0
```

### Android Hooks

`.claude/settings.json` (hooks section):
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/ktlint-fix.sh",
            "timeout": 30
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/auto-approve-safe-android.sh",
            "timeout": 5
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/verify-android-build.sh",
            "timeout": 300
          }
        ]
      }
    ]
  }
}
```

**`.claude/hooks/ktlint-fix.sh`**:
```bash
#!/bin/bash
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // ""')

if [[ "$FILE_PATH" == *.kt ]] || [[ "$FILE_PATH" == *.kts ]]; then
  ktlint --format "$FILE_PATH" 2>/dev/null
  ISSUES=$(ktlint "$FILE_PATH" 2>/dev/null)
  if [ -n "$ISSUES" ]; then
    jq -n --arg ctx "ktlint issues:\n$ISSUES" '{
      hookSpecificOutput: {
        hookEventName: "PostToolUse",
        additionalContext: $ctx
      }
    }'
  fi
fi
exit 0
```

**`.claude/hooks/auto-approve-safe-android.sh`**:
```bash
#!/bin/bash
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // ""')

if echo "$COMMAND" | grep -qE '^(\./gradlew (assembleDebug|test|lint|detekt|ktlint)|adb (devices|logcat|shell dumpsys)|gradle --version)'; then
  jq -n '{
    hookSpecificOutput: {
      hookEventName: "PreToolUse",
      permissionDecision: "allow",
      permissionDecisionReason: "Safe Android build/test command"
    }
  }'
  exit 0
fi
exit 0
```

**`.claude/hooks/verify-android-build.sh`**:
```bash
#!/bin/bash
BUILD_OUTPUT=$(./gradlew assembleDebug 2>&1)
if [ $? -ne 0 ]; then
  echo "Android build failed. Fix errors before stopping." >&2
  exit 2
fi
exit 0
```

---

## 7. Custom Skills/Commands for Mobile

### iOS Skills

**`.claude/skills/new-screen/SKILL.md`**:
```yaml
---
name: new-screen
description: Scaffold a new SwiftUI screen with ViewModel, tests, and preview
disable-model-invocation: true
argument-hint: <ScreenName>
allowed-tools: Read, Write, Edit, Bash(swift build *)
---

Create a new screen module for "$ARGUMENTS":

1. Create `Sources/Features/$ARGUMENTS/$ARGUMENTS` + `View.swift`:
   - SwiftUI View with #Preview
   - Inject ViewModel via @Environment
   - Include loading, error, and content states
   - Follow the 100-line view extraction rule

2. Create `Sources/Features/$ARGUMENTS/$ARGUMENTS` + `ViewModel.swift`:
   - @Observable class
   - async/await for data loading
   - Published state with typed error handling

3. Create `Sources/Features/$ARGUMENTS/$ARGUMENTS` + `Coordinator.swift`:
   - NavigationStack-based routing
   - Register in AppCoordinator

4. Create `Tests/UnitTests/$ARGUMENTS/$ARGUMENTS` + `ViewModelTests.swift`:
   - Use Swift Testing framework (@Test, #expect)
   - Test loading, success, and error states

5. Verify compilation: run `swift build`
```

**`.claude/skills/ios-review/SKILL.md`**:
```yaml
---
name: ios-review
description: Pre-PR code review for iOS-specific issues
disable-model-invocation: true
context: fork
agent: Explore
---

Review all recently changed Swift files for:

1. **SwiftUI Issues**:
   - Deprecated API usage (foregroundColor, ObservableObject, etc.)
   - Views exceeding 100 lines
   - Missing #Preview declarations
   - Incorrect state management (@State vs @Environment vs @Bindable)

2. **Concurrency Issues**:
   - Missing @Sendable annotations
   - Actor isolation violations
   - Task cancellation handling
   - Main actor usage for UI code

3. **Safety Issues**:
   - Force unwraps without justification
   - Force casts
   - Retain cycles in closures (missing [weak self])
   - Missing error handling

4. **Localization**:
   - Hardcoded user-facing strings
   - Missing LocalizedStringKey usage

5. **Accessibility**:
   - Missing accessibility labels on interactive elements
   - VoiceOver navigation issues
   - Dynamic Type support

Report findings with file paths, line numbers, and suggested fixes.
```

**`.claude/skills/ios-localize/SKILL.md`**:
```yaml
---
name: ios-localize
description: Extract hardcoded strings and organize localization
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Grep, Glob
---

Scan all SwiftUI views for hardcoded user-facing strings:

1. Search for Text("..."), Button("..."), Label("...") with literal strings
2. Search for .navigationTitle("..."), .alert("...") with literals
3. For each found string:
   - Generate a localization key following the pattern: `feature.screen.element`
   - Add to Localizable.xcstrings
   - Replace literal with String(localized:) or LocalizedStringKey
4. Generate a summary of all extracted strings
5. Verify compilation: run `swift build`
```

**`.claude/skills/ios-release/SKILL.md`**:
```yaml
---
name: ios-release
description: Bump version, create changelog, prepare for release
disable-model-invocation: true
argument-hint: <version-number>
allowed-tools: Read, Write, Edit, Bash(agvtool *), Bash(git *)
---

Prepare release for version $ARGUMENTS:

1. Update marketing version: `agvtool new-marketing-version $ARGUMENTS`
2. Increment build number: `agvtool next-version -all`
3. Read git log since last tag to generate CHANGELOG entry
4. Update CHANGELOG.md with new version section
5. Create git commit: "chore: bump version to $ARGUMENTS"
6. Create git tag: "v$ARGUMENTS"
7. Print summary of changes included in this release
```

### Android Skills

**`.claude/skills/new-feature/SKILL.md`**:
```yaml
---
name: new-feature
description: Scaffold a new Android feature module with Compose, ViewModel, and tests
disable-model-invocation: true
argument-hint: <featureName>
allowed-tools: Read, Write, Edit, Bash(./gradlew *)
---

Create a new feature module for "$ARGUMENTS":

1. Create module directories:
   - `feature/$ARGUMENTS/api/` with navigation contract
   - `feature/$ARGUMENTS/impl/ui/` with Compose screen
   - `feature/$ARGUMENTS/impl/viewmodel/` with ViewModel

2. Generate `feature/$ARGUMENTS/api/build.gradle.kts`:
   ```kotlin
   plugins {
       id("myapp.android.library")
   }
   ```

3. Generate navigation contract in api/:
   - Interface with navigation route definition
   - Type-safe arguments using Navigation 3

4. Generate ViewModel in impl/:
   - @HiltViewModel with @Inject constructor
   - StateFlow<UiState> with WhileSubscribed(5_000)
   - Sealed interface for UiState (Loading, Success, Error)

5. Generate Compose screen in impl/:
   - @Composable FeatureRoute (stateful, calls hiltViewModel)
   - @Composable FeatureScreen (stateless, takes UiState + lambdas)
   - Material 3 components, 8dp spacing tokens

6. Generate test in impl/:
   - ViewModel test with JUnit 5 + Turbine
   - Fake repository implementation

7. Register module in settings.gradle.kts
8. Verify: `./gradlew :feature:$ARGUMENTS:impl:compileDebugKotlin`
```

**`.claude/skills/android-review/SKILL.md`**:
```yaml
---
name: android-review
description: Pre-PR review for Android-specific patterns
disable-model-invocation: true
context: fork
agent: Explore
---

Review recently changed Kotlin files for:

1. **Compose Issues**:
   - Missing @Stable/@Immutable annotations on data classes used in Compose
   - Using collectAsState instead of collectAsStateWithLifecycle
   - Business logic in Composables (should be in ViewModel)
   - Missing state hoisting

2. **Architecture Issues**:
   - Direct API calls without repository layer
   - Room not used as single source of truth
   - GlobalScope usage (should use viewModelScope)
   - Missing error handling in coroutines

3. **Performance**:
   - Unnecessary recompositions
   - Heavy operations on main thread
   - Missing Baseline Profile for new screens

4. **Resources**:
   - Hardcoded strings (should use R.string)
   - Missing RTL support
   - Missing night mode resources

Report findings with file paths, line numbers, and fixes.
```

**`.claude/skills/android-release/SKILL.md`**:
```yaml
---
name: android-release
description: Bump version, create changelog, build release AAB
disable-model-invocation: true
argument-hint: <version-name>
allowed-tools: Read, Write, Edit, Bash(./gradlew *), Bash(git *)
---

Prepare Android release for version $ARGUMENTS:

1. Update versionName to "$ARGUMENTS" in app/build.gradle.kts
2. Increment versionCode by 1
3. Read git log since last tag for changelog
4. Update CHANGELOG.md
5. Run `./gradlew test` to verify tests pass
6. Run `./gradlew assembleRelease` to verify release build
7. Create git commit: "chore: bump version to $ARGUMENTS"
8. Create git tag: "v$ARGUMENTS"
9. Print build artifact location and release summary
```

---

## 8. Headless Automation (`claude -p`)

### Nightly iOS Build Verification

```bash
#!/bin/bash
# .scripts/nightly-ios-build.sh
claude -p "Build the iOS project for all targets (Debug and Release) on iPhone 16 Pro simulator. \
Run the full test suite. Report any compilation errors, test failures, or SwiftLint warnings. \
If tests fail, analyze the failure and suggest a fix." \
  --allowedTools "Bash(swift *),Bash(xcodebuild *),Bash(xcrun *),Read,Glob,Grep,mcp__XcodeBuildMCP__*" \
  --output-format json \
  --max-turns 15 \
  | jq -r '.result' > build-report.txt

# Send notification if failures
if grep -q "FAIL\|ERROR\|error:" build-report.txt; then
  # Send to Slack/Teams
  curl -X POST "$SLACK_WEBHOOK" -d "{\"text\": \"iOS nightly build failed. See report.\"}"
fi
```

### Nightly Android Build Verification

```bash
#!/bin/bash
# .scripts/nightly-android-build.sh
claude -p "Run the full Android test suite with ./gradlew test. \
Then run ./gradlew detekt for code quality. \
Report any test failures, lint errors, or detekt violations. \
If tests fail, analyze the root cause." \
  --allowedTools "Bash(./gradlew *),Bash(adb *),Read,Glob,Grep" \
  --output-format json \
  --max-turns 15 \
  | jq -r '.result' > android-build-report.txt
```

### Automated Crash Analysis

```bash
#!/bin/bash
# .scripts/crash-analysis.sh
claude -p "Using the Crashlytics MCP server, fetch the top 5 unresolved crashes \
from the last 7 days. For each crash: \
1. Analyze the stacktrace \
2. Find the relevant source code \
3. Suggest a fix with code changes \
4. Estimate severity (critical/high/medium/low)" \
  --allowedTools "Read,Grep,Glob,mcp__firebase__*" \
  --output-format json \
  --max-turns 20
```

### Store Metadata Update

```bash
#!/bin/bash
# .scripts/update-store-metadata.sh
VERSION=$1
claude -p "Update the App Store and Play Store metadata for version $VERSION: \
1. Read CHANGELOG.md for the latest release notes \
2. Generate release notes in en, de, fr, es, ja \
3. Write iOS release notes to fastlane/metadata/[locale]/release_notes.txt \
4. Write Android release notes to fastlane/metadata/android/[locale]/changelogs/$VERSION.txt \
5. Verify all locale files are complete" \
  --allowedTools "Read,Write,Glob,Grep" \
  --output-format json
```

### Automated Screenshot Generation (iOS)

```bash
#!/bin/bash
# .scripts/generate-screenshots.sh
claude -p "Using XcodeBuildMCP and ios-simulator-mcp: \
1. Build and run the app on iPhone 16 Pro Max simulator \
2. Navigate to each of these screens: Home, Search, Profile, Settings \
3. Take a screenshot of each screen in both light and dark mode \
4. Save screenshots to ./screenshots/ with descriptive names \
5. Repeat for iPad Pro 13-inch for tablet screenshots" \
  --allowedTools "mcp__XcodeBuildMCP__*,mcp__ios-simulator__*,Bash(mkdir *),Write" \
  --output-format json \
  --max-turns 30
```

### PR Diff Analysis for Mobile Patterns

```bash
#!/bin/bash
# .scripts/mobile-pr-review.sh
PR_NUMBER=$1
claude -p "Review PR #$PR_NUMBER for mobile-specific issues: \
1. Check for new Swift files not added to Xcode project \
2. Verify all new strings are localized \
3. Check for accessibility label coverage on new UI \
4. Verify new screens have #Preview declarations \
5. Check for proper error handling in async code \
6. Verify snapshot tests for new/modified views" \
  --allowedTools "Bash(gh *),Bash(git *),Read,Grep,Glob" \
  --output-format json
```

---

## 9. GitHub Actions for Mobile

### iOS PR Review Workflow

`.github/workflows/claude-ios-review.yml`:
```yaml
name: Claude iOS PR Review

on:
  pull_request:
    types: [opened, synchronize]
    paths:
      - '**/*.swift'
      - '*.xcodeproj/**'
      - 'Package.swift'

jobs:
  review:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4

      - name: Select Xcode
        run: sudo xcode-select -s /Applications/Xcode_26.app

      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Review this iOS PR for:
            1. SwiftUI best practices (no deprecated APIs, proper state management)
            2. Swift 6 concurrency safety (@Sendable, actor isolation)
            3. Missing accessibility labels
            4. Hardcoded strings that should be localized
            5. Views over 100 lines that should be extracted
            6. Missing #Preview declarations
            7. Force unwraps without justification
            Post findings as review comments on specific lines.
          claude_args: "--max-turns 10 --model claude-sonnet-4-6"
```

### Android PR Review Workflow

`.github/workflows/claude-android-review.yml`:
```yaml
name: Claude Android PR Review

on:
  pull_request:
    types: [opened, synchronize]
    paths:
      - '**/*.kt'
      - '**/*.kts'
      - '**/build.gradle*'
      - 'gradle/**'

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK
        uses: actions/setup-java@v4
        with:
          java-version: '25'
          distribution: 'temurin'

      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Review this Android PR for:
            1. Compose best practices (state hoisting, stability annotations)
            2. collectAsStateWithLifecycle usage (not collectAsState)
            3. Repository pattern (offline-first, Room as source of truth)
            4. Coroutine scope usage (no GlobalScope)
            5. Hardcoded strings (should use R.string)
            6. Missing Hilt annotations
            7. Architecture violations (business logic in Composables)
            Post findings as review comments on specific lines.
          claude_args: "--max-turns 10 --model claude-sonnet-4-6"
```

### Automated Build Verification

`.github/workflows/claude-build-verify.yml`:
```yaml
name: Claude Build Verification

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ios-build:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Build the iOS project and run tests.
            If the build fails, analyze the error and suggest a fix.
            If tests fail, identify the root cause.
            Post a summary as a PR comment.
          claude_args: >
            --max-turns 15
            --allowedTools "Bash(xcodebuild *),Bash(swift *),Bash(xcrun *),Read,Grep,Glob"

  android-build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: '25'
          distribution: 'temurin'
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Run ./gradlew assembleDebug and ./gradlew test.
            If the build fails, analyze the error and suggest a fix.
            If tests fail, identify the root cause.
            Post a summary as a PR comment.
          claude_args: >
            --max-turns 15
            --allowedTools "Bash(./gradlew *),Read,Grep,Glob"
```

### Nightly QA Sweep

`.github/workflows/nightly-qa.yml`:
```yaml
name: Nightly QA Sweep

on:
  schedule:
    - cron: "0 6 * * *"  # 6 AM UTC daily

jobs:
  qa-sweep:
    runs-on: macos-latest
    steps:
      - uses: actions/checkout@v4
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Perform a nightly QA sweep:
            1. Build the iOS app for iPhone 16 Pro simulator
            2. Launch the app and navigate through all main screens
            3. Take screenshots of each screen
            4. Check for layout issues, missing content, or errors
            5. Run the full test suite
            6. Create a GitHub issue if any problems are found
          claude_args: >
            --max-turns 30
            --model claude-opus-4-6
            --allowedTools "Bash(*),Read,Write,Grep,Glob,mcp__XcodeBuildMCP__*,mcp__ios-simulator__*"
```

---

## 10. Mobile-Specific Challenges

### 10.1 Xcode Project Files (.pbxproj)

**The Problem**: `.pbxproj` files use a custom XML-like format that is 5000+ lines even for small projects. LLMs frequently introduce syntax errors when editing them.

**The Solution**: Never let Claude edit .pbxproj files.

Implementation:
1. Add a `PreToolUse` hook that blocks edits (see hooks section above)
2. Add to CLAUDE.md: "NEVER edit .pbxproj, .xcodeproj, or .xcworkspace files"
3. Add to `.claude/settings.json` deny rules: `"Edit(.*.pbxproj)"`
4. Use Tuist or XcodeGen to generate project files from YAML/Swift definitions instead

**Workflow**: Claude creates the `.swift` file, you add it to the Xcode target via the GUI (File > Add Files to Project).

### 10.2 Binary Assets

**The Problem**: Claude Code cannot meaningfully read or create binary files (images, fonts, app icons).

**The Solution**:
- For asset catalogs, Claude can read/write the `Contents.json` files that describe assets
- For image references, Claude can set up the code that references assets by name
- Actual images must be added manually or via asset pipeline scripts
- Consider SF Symbols for icons (text-based, Claude-friendly)

### 10.3 Interface Builder / Storyboards

**The Problem**: XIBs and Storyboards are XML-based but effectively opaque to Claude -- the XML is complex and fragile.

**The Solution**: Use SwiftUI exclusively for new projects. For legacy projects with Storyboards, productivity gains are reduced to 35-40% vs 60% for pure SwiftUI projects.

### 10.4 Simulator Interaction from Terminal

**This works well.** Claude Code can:

```bash
# List simulators
xcrun simctl list devices

# Boot a simulator
xcrun simctl boot "iPhone 16 Pro"

# Install an app
xcrun simctl install booted path/to/MyApp.app

# Launch an app
xcrun simctl launch booted com.mycompany.MyApp

# Take a screenshot
xcrun simctl io booted screenshot screenshot.png

# Record video
xcrun simctl io booted recordVideo video.mp4

# Open a URL
xcrun simctl openurl booted "myapp://deeplink"

# Send push notification
xcrun simctl push booted com.mycompany.MyApp payload.json
```

For Android:
```bash
# List emulators
emulator -list-avds

# Start emulator
emulator -avd Pixel_7_API_36 -no-window &

# Install APK
adb install app/build/outputs/apk/debug/app-debug.apk

# Launch app
adb shell am start -n com.mycompany.myapp/.MainActivity

# Take screenshot
adb shell screencap /sdcard/screenshot.png && adb pull /sdcard/screenshot.png

# Get UI hierarchy
adb shell uiautomator dump && adb pull /sdcard/window_dump.xml
```

### 10.5 Code Signing & Provisioning

**iOS signing is complex**. Claude Code can help with:
- Reading and analyzing provisioning profiles
- Running `security find-identity` to list certificates
- Configuring xcodebuild signing flags
- Troubleshooting signing errors from build logs

But should NOT:
- Modify signing configurations in Xcode project settings
- Handle certificate creation (use Apple Developer Portal or Fastlane match)
- Access Keychain programmatically for production signing

**Android signing**: Claude can read/write `signingConfigs` in `build.gradle.kts`, but keep keystore passwords in environment variables or `local.properties` (gitignored).

### 10.6 Gradle Build Times

**The Problem**: Gradle builds can take 5-10+ minutes, which risks Claude Code timeout.

**Solutions**:
- Set higher timeouts in hooks: `"timeout": 600` (10 minutes)
- Use `--max-turns` to give Claude enough time
- Configure Gradle build cache and configuration cache
- Use `./gradlew assembleDebug` (not release) during development
- Add to CLAUDE.md: "For Gradle builds, expect 2-5 minutes. Always use assembleDebug unless specifically asked for release."

### 10.7 Core Data Migrations

Claude can generate migration code, but verifying edge cases in production data requires human oversight. Add to CLAUDE.md: "Core Data/Room migrations require human review before merge."

---

## 11. Real-World Examples & Case Studies

### Case Study 1: Automated Mobile QA (Zabriskie App)

Christopher Meiklejohn built a daily automated QA system for his app Zabriskie that sweeps 25 screens across iOS and Android, files bug reports autonomously.

**Android**: Used Chrome DevTools Protocol via `adb reverse` to control the WebView, injecting JWT auth via WebSocket. 90-minute setup, 90-second sweep per run.

**iOS**: More challenging (6+ hours setup). Key challenges:
- Email input fields with `type="email"` prevented `@` typing in simulator
- Native UIKit dialogs required writing directly to Simulator's TCC.db privacy database
- Coordinate-based taps required using `ios-simulator-mcp`'s `ui_describe_point` for accessibility-based discovery

Outcome: Both platforms run automated morning QA sweeps with visual regression reports.

### Case Study 2: macOS App Built Entirely by Claude Code

Indragie Karunaratne shipped a complete macOS app built entirely by Claude Code, demonstrating that Claude can handle the full Apple platform development cycle from project setup through App Store submission.

### Case Study 3: Android App in 4 Days (Zero Experience)

A developer with zero Android experience built and shipped an Android app in 4 days using Claude Code with a "two-layer AI protocol." Builds were passing on both emulator and physical device by end of Day 1.

### Case Study 4: iOS Development Time Reduced by 60%

Osman Demiroz documented specific productivity gains:
- Module scaffolding: 15-20 minutes to ~30 seconds
- Pre-PR review catches issues before human reviewer sees them
- Custom /snapshot command generates comprehensive visual regression tests
- Tiered CLAUDE.md strategy (root > source > feature) provides precise context

### Case Study 5: Cars24 React Native Workflows

Cars24 engineering team documented production Claude Code workflows for React Native that "actually move the needle," covering real-world patterns for a large-scale mobile commerce app.

### Case Study 6: Expo SDK 54 to 55 Migration

A developer used Claude Code to upgrade from Expo SDK 54 to 55, noting that Claude handled the mechanical, repetitive migration work that usually takes hours, though some manual intervention was still needed for edge cases.

---

## Sources

### Claude Code Documentation
- [Claude Code README](https://github.com/anthropics/claude-code)
- [Claude Code Hooks Reference](https://code.claude.com/docs/en/hooks)
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills)
- [Claude Code Headless/Programmatic Mode](https://code.claude.com/docs/en/headless)
- [Claude Code GitHub Actions](https://code.claude.com/docs/en/github-actions)
- [Claude Code Best Practices](https://code.claude.com/docs/en/best-practices)

### iOS Development
- [Claude Code iOS Dev Guide (keskinonur)](https://github.com/keskinonur/claude-code-ios-dev-guide)
- [Reduce iOS Development Time by 60% with Claude Code](https://medium.com/@osmandemiroz/reduce-ios-development-time-by-60-with-claude-code-86a4e9d864ca)
- [I Shipped a macOS App Built Entirely by Claude Code](https://www.indragie.com/blog/i-shipped-a-macos-app-built-entirely-by-claude-code)
- [Xcode 26.3 Agentic Coding with Claude & Codex](https://www.paperclipped.de/en/blog/xcode-agentic-coding-claude-codex/)
- [Apple's Xcode now supports the Claude Agent SDK](https://www.anthropic.com/news/apple-xcode-claude-agent-sdk)
- [Claude is now generally available in Xcode](https://www.anthropic.com/news/claude-in-xcode)
- [How to Automate iOS Development Without Breaking .pbxproj Files](https://dev.to/anicca_301094325e/how-to-automate-ios-development-without-breaking-pbxproj-files-2mpk)
- [SwiftUI Agent Skill (Hacking with Swift)](https://www.hackingwithswift.com/articles/282/swiftui-agent-skill-claude-codex-ai)
- [Swift Agent Skills Collection (twostraws)](https://github.com/twostraws/swift-agent-skills)
- [Apple Platform Build Tools Plugin](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin)
- [Swift.org: What's new in Swift February 2026](https://www.swift.org/blog/whats-new-in-swift-february-2026/)
- [Xcode What's New (Apple)](https://developer.apple.com/xcode/whats-new/)
- [Giving External Agentic Coding Tools Access to Xcode (Apple)](https://developer.apple.com/documentation/xcode/giving-agentic-coding-tools-access-to-xcode)

### Android Development
- [Claude Android Skill (dpconde)](https://github.com/dpconde/claude-android-skill)
- [Claude Android Ninja (Drjacky)](https://github.com/Drjacky/claude-android-ninja)
- [I Built an Android App in 4 Days With Zero Android Experience](https://dev.to/raio/i-built-an-android-app-in-4-days-with-zero-android-experience-using-claude-code-and-a-two-layer-2p44)
- [Claude Code for Android Development: Best Practices](https://www.myandroidsolutions.com/2026/02/28/claude-code-android-development-best-practices/)
- [Jetpack Compose in 2026: Everything You Need to Know](https://medium.com/@androidlab/jetpack-compose-in-2026-everything-you-need-to-know-8975d48ad2a0)
- [Compose Multiplatform 1.10.0 Release (JetBrains)](https://blog.jetbrains.com/kotlin/2026/01/compose-multiplatform-1-10-0/)
- [Android Development in 2026: Tools, Libraries, and Predictions](https://medium.com/@androidlab/android-development-in-2026-tools-libraries-and-predictions-cb6981c6d084)

### Cross-Platform
- [KMP vs Flutter vs React Native: 2026 Reality](https://www.javacodegeeks.com/2026/02/kotlin-multiplatform-vs-flutter-vs-react-native-the-2026-cross-platform-reality.html)
- [React Native Expo Agent System](https://github.com/senaiverse/claude-code-reactnative-expo-agent-system)
- [Claude Code for React & React Native (Cars24)](https://medium.com/cars24/claude-code-for-react-react-native-workflows-that-actually-move-the-needle-33b8bb410b14)
- [Expo MCP Documentation](https://docs.expo.dev/eas/ai/mcp/)
- [React Native Best Practices for AI Agents (Callstack)](https://www.callstack.com/blog/announcing-react-native-best-practices-for-ai-agents)
- [Expo Toolkit Plugin](https://github.com/rahulkeerthi/expo-toolkit)
- [Compose Multiplatform: Sharing UI (2026)](https://www.myandroidsolutions.com/2026/03/23/compose-multiplatform-shared-ui-android-ios/)

### MCP Servers
- [XcodeBuildMCP (Sentry)](https://github.com/getsentry/XcodeBuildMCP)
- [XcodeBuildMCP Website](https://www.xcodebuildmcp.com/)
- [ios-simulator-mcp (Whitesmith)](https://github.com/whitesmith/ios-simulator-mcp)
- [ios-simulator-mcp (joshuayoes)](https://github.com/joshuayoes/ios-simulator-mcp)
- [mobile-mcp (mobile-next)](https://github.com/mobile-next/mobile-mcp)
- [Firebase MCP Server](https://firebase.google.com/docs/ai-assistance/mcp-server)
- [Crashlytics MCP](https://firebase.google.com/docs/crashlytics/ai-assistance-mcp)
- [Figma MCP Server](https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Figma-MCP-server)
- [Play Store MCP](https://github.com/devexpert-io/play-store-mcp)
- [Fastlane MCP Server](https://github.com/lyderdev/fastlane-mcp-server)
- [Maestro MCP (Mobile Testing)](https://maestro.dev/blog/how-maestro-is-reinventing-mobile-test-automation)
- [Mobile Development MCP Servers Directory](https://mcpmarket.com/categories/mobile-development)

### QA & Testing
- [Teaching Claude to QA a Mobile App](https://christophermeiklejohn.com/ai/zabriskie/development/android/ios/2026/03/22/teaching-claude-to-qa-a-mobile-app.html)
- [iOS Simulator Skill (conorluddy)](https://github.com/conorluddy/ios-simulator-skill)
- [Mobile App Test Automation Skills](https://mcpmarket.com/tools/skills/mobile-app-test-automation)
- [Best Mobile App Testing Frameworks 2026 (Maestro)](https://maestro.dev/insights/best-mobile-app-testing-frameworks)

### GitHub Actions
- [Claude Code Action (Official)](https://github.com/anthropics/claude-code-action)
- [Claude Code GitHub Actions Documentation](https://code.claude.com/docs/en/github-actions)
- [Claude Code Action on GitHub Marketplace](https://github.com/marketplace/actions/claude-code-action-official)
