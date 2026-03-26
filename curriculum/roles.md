# Role Registry

Single source of truth for all learner roles. **All other files must reference this registry** instead of hardcoding role lists.

## Available Roles

| Role | Description | Examples | Skills Focus | Hooks | Workflows |
|------|-------------|----------|-------------|-------|-----------|
| `frontend` | React, Next.js, TypeScript, CSS/Tailwind | React components, Next.js pages/API routes, Storybook stories | Component creation, page routing, SSR/SSG patterns | Prettier, ESLint, bundle size checks, Next.js lint | Visual regression, accessibility audits, Lighthouse CI |
| `backend` | NestJS, TypeScript, REST/GraphQL APIs, databases | NestJS modules/controllers/services, DTOs, Drizzle models | Endpoint creation, migrations, guards/interceptors, error handling | Type checking, API spec validation, DTO validation | Contract testing, performance benchmarks, OpenAPI sync |
| `QA` | Test automation, E2E, integration testing | Test suites, fixtures, page objects, Playwright/Jest specs | Test creation, coverage reporting, E2E scenario design | Auto-run tests, coverage thresholds | Regression detection, flaky test identification |
| `DevOps` | Infrastructure, CI/CD, containers, cloud | Terraform modules, Dockerfiles, K8s manifests, GitHub Actions | Infrastructure patterns, deployment procedures | Config validation, security scanning | Infrastructure validation, deployment automation |
| `marketing` | Performance marketing: ad campaigns, funnels, A/B tests, creative production | Ad copy variations, UTM links, landing pages, funnel reports, creative briefs | Campaign automation, performance reporting, creative asset management | Brand voice enforcement, platform spec validation, budget safety gates | Scheduled performance reports, bleed detection, experiment monitoring |
| `mobile` | iOS (Swift/SwiftUI) and Android (Kotlin/Compose) native app development | SwiftUI views, Compose screens, ViewModels, navigation, platform APIs | Screen scaffolding, feature modules, localization, release automation | SwiftLint/ktlint auto-fix, build verification, pbxproj protection | Nightly builds, store submission, crash analysis, screenshot generation |

## Mobile Sub-Specializations

When a student selects the `mobile` role, ask which platform they work on. This is stored in `progress.json` → `student.mobile_platform` and used to further tailor examples.

| Sub-specialization | Focus | Tech Stack | Typical Tasks |
|--------------------|-------|------------|---------------|
| `ios` | Native iOS development | Swift 6.3, SwiftUI, SwiftData, SPM, Xcode 26, Swift Testing | New screens/views, navigation setup, SwiftData models, localization, TestFlight releases |
| `android` | Native Android development | Kotlin 2.3, Jetpack Compose, Navigation 3, Gradle/Kotlin DSL, Material 3 | Compose screens, ViewModels, Room/SQLDelight models, Play Store releases |

### Mobile MCP Servers

| MCP Server | Platform | Purpose |
|------------|----------|---------|
| XcodeBuildMCP (Sentry) | iOS | 59 tools — build, test, debug, UI automation, screenshots |
| ios-simulator-mcp | iOS | Tap/swipe/type/screenshot on iOS simulator |
| mobile-mcp | iOS + Android | Platform-agnostic device automation |
| Firebase MCP | iOS + Android | Auth, Firestore, Crashlytics, Remote Config |
| Play Store MCP | Android | Deployment, release management, store listing |
| Expo MCP | Cross-platform | EAS Build, Workflows, simulator interaction |
| Figma MCP (Official) | iOS + Android | Design-to-code for mobile screens |

### Mobile Skills by Sub-Specialization

| Sub-spec | Example Skills |
|----------|---------------|
| ios | `/new-screen` (SwiftUI view + ViewModel), `/ios-release` (version bump + TestFlight), `/localize` (string extraction + translation), `/ios-review` (Swift-specific PR review) |
| android | `/new-screen` (Compose screen + ViewModel), `/android-release` (version bump + Play Store), `/localize` (strings.xml management), `/android-review` (Kotlin-specific PR review) |

### Mobile Hooks by Sub-Specialization

| Sub-spec | Hook | Type | What It Does |
|----------|------|------|-------------|
| ios | SwiftLint auto-fix | PostToolUse | Runs `swiftlint lint --fix` on saved `.swift` files |
| ios | Block pbxproj edits | PreToolUse | Prevents direct edits to `.pbxproj` (use Xcode/SPM instead) |
| ios | Build verification | Stop | Runs `xcodebuild build` to verify changes compile |
| android | ktlint auto-format | PostToolUse | Runs `ktlint -F` on saved `.kt` files |
| android | Gradle lint check | PostToolUse | Runs `./gradlew lint` after code changes |
| android | Build verification | Stop | Runs `./gradlew assembleDebug` to verify changes compile |

> **Detailed reference**: See `lesson-modules/claude-code-for-mobile-developers-2026.md` for full examples, headless scripts, GitHub Actions, and MCP configurations for each platform.

## Marketing Sub-Specializations

When a student selects the `marketing` role, ask which sub-specialization fits best. This is stored in `progress.json` → `student.marketing_specialization` and used to further tailor examples.

| Sub-specialization | Focus | Tools & Platforms | Typical Tasks |
|--------------------|-------|-------------------|---------------|
| `creative` | Ad creative concepts, copywriting, creative strategy | Meta Ad Library, Google Ads, Notion, Slack | Ad copy generation, competitor intelligence, creative fatigue detection, creative briefs |
| `uam` | User acquisition, campaign buying, ROAS optimization | Meta Ads Manager, Google Ads, TikTok Ads, AppsFlyer, BigQuery | Budget reallocation, bleed detection, cross-platform reporting, UTM generation, bid optimization |
| `creative_producer` | Visual design, ad asset production, landing pages | Figma, Adobe Suite, Canva, Puppeteer | Bulk creative variations, image resizing, landing page generation, asset QC |
| `pmm` | Funnels, A/B testing, user behavior, product positioning | Amplitude, Mixpanel, Optimizely, BigQuery, HubSpot | Funnel analysis, experiment tracking, cohort retention, CRO, user research synthesis |

### Marketing MCP Servers

| MCP Server | Used By | Purpose |
|------------|---------|---------|
| Meta Ads MCP (Pipeboard) | creative, uam | Campaign data, creative performance, budget management |
| Google Ads MCP | creative, uam | Campaign performance, keyword analytics |
| TikTok Ads MCP (AdsMCP) | uam | Creative performance, campaign management |
| Figma MCP (Official) | creative_producer | Two-way design-code sync, component access |
| Amplitude MCP | pmm | Funnels, retention cohorts, experiments |
| Mixpanel MCP | pmm | Product analytics, user flows |
| BigQuery MCP | uam, pmm | Data warehouse queries on marketing data |
| Slack MCP | all | Alerts, report distribution |
| Notion MCP | creative, pmm | Briefs, experiment docs, status boards |
| Google Sheets MCP | uam, pmm | UTM tracking, report output |

### Marketing Skills by Sub-Specialization

| Sub-spec | Example Skills |
|----------|---------------|
| creative | `/creative-brief`, `/copy-matrix`, `/fatigue-scan` |
| uam | `/ua-daily`, `/campaign-launch`, `/bleed-check`, `/rebalance` |
| creative_producer | `/resize-batch`, `/creative-qc`, `/bulk-creative` |
| pmm | `/funnel-health`, `/experiment-tracker`, `/user-research-synthesis` |

### Marketing Hooks by Sub-Specialization

| Sub-spec | Hook | Type | What It Does |
|----------|------|------|-------------|
| creative | Brand voice enforcement | PreToolUse | Validates copy follows brand guidelines before writing |
| uam | Budget safety gate | PreToolUse | Blocks budget changes >30% or campaigns in learning phase |
| uam | Ad platform audit log | PostToolUse | Logs every MCP ad platform action to audit trail |
| creative_producer | Auto-optimize images | PostToolUse | Runs optipng on generated PNG files |
| creative_producer | Platform spec verification | Stop | Verifies all assets meet platform dimension/size requirements |
| pmm | Analysis completeness check | Stop | Verifies statistical significance, confidence intervals, sample size |

> **Detailed reference**: See `lesson-modules/claude-code-for-performance-marketing-2026.md` for full examples, headless scripts, GitHub Actions, and MCP configurations for each sub-specialization.

## How Roles Are Used

1. **On first `/cc-course:start`** — student selects their role
2. **If `marketing`** — additionally asked for sub-specialization (creative / uam / creative_producer / pmm)
3. **If `mobile`** — additionally asked for platform (ios / android)
4. **Stored in** `progress.json` → `student.role` and optionally `student.marketing_specialization` or `student.mobile_platform`
5. **Teaching adapts** — examples, skills, hooks, and workflows match the role (and sub-spec)
6. **Hints adapt** — hint examples use role-specific technologies

## How to Add a New Role

1. Add a row to the **Available Roles** table above
2. That's it — all other files read from this registry

## Role-Specific Hint Examples

When giving hints, use technologies familiar to the learner:

- **frontend**: React components, Next.js pages/API routes, Tailwind, Storybook
- **backend**: NestJS modules/controllers/services, Drizzle, DTOs, guards
- **QA**: Test suites, Playwright specs, fixtures, page objects
- **DevOps**: Terraform, Docker, K8s, GitHub Actions
- **marketing (creative)**: Ad copy frameworks (PAS, AIDA), creative briefs, Meta Ad Library
- **marketing (uam)**: Campaign metrics (CPI, ROAS, CTR), budget pacing, UTM tracking
- **marketing (creative_producer)**: Figma, asset dimensions, bulk variations, landing pages
- **marketing (pmm)**: Funnels, A/B test significance, cohort retention, ICE framework
- **mobile (ios)**: SwiftUI views, Swift concurrency, SPM packages, Xcode build settings, TestFlight
- **mobile (android)**: Jetpack Compose screens, Kotlin coroutines, Gradle modules, Play Store
