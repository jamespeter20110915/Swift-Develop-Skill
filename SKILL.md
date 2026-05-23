---
name: apple-design-swift
description: Use this skill whenever the user wants to design or build an app for any Apple platform — iOS, iPadOS, macOS, watchOS, or visionOS — using SwiftUI. Trigger on phrases like "design an iOS app", "build a Mac app", "make a SwiftUI view", "Apple Watch app", "visionOS experience", or any request involving Swift code for Apple platforms, even casual ones like "help me make an iPhone app for X". This skill enforces Apple's official Human Interface Guidelines (HIG), idiomatic SwiftUI patterns, and maintainable Swift code structure. Use it BEFORE writing any Swift/SwiftUI code so the design language and architecture are correct from the start, not retrofitted afterward.
---

# Apple Design & Swift

Use Apple's Human Interface Guidelines (HIG) as the design authority and idiomatic SwiftUI as the implementation toolkit. This skill is a navigation map — for design decisions, fetch the relevant HIG page rather than relying on memory.

## Core Philosophy

Apple's design language rests on three principles. Apply them as questions you ask yourself while designing:

**Clarity.** Is every element legible? Is the hierarchy obvious at a glance? Use negative space ruthlessly — if removing something doesn't hurt comprehension, remove it. Text must be sharp, icons unambiguous, and interactions predictable.

**Deference.** Does the UI serve the content or compete with it? Chromeless is the default. The interface recedes so the user's task — reading, drawing, watching, playing — takes center stage. Use system fonts, system colors, and translucent materials that adapt to the content behind them.

**Depth.** Does the spatial model match the user's mental model? Motion should feel causal: a view slides in from the edge that summoned it, a sheet rises from its trigger, a transition zooms from the tapped element. Layers are real — use z-ordering, vibrancy, and parallax to communicate what's in front of what.

## Workflow

Follow these steps in order when designing a new app, screen, or feature. Skip steps only when the brief is trivial (a single-row bug fix, a color tweak).

### 1. Identify Target Platforms

For each platform the app targets, `web_fetch` the corresponding HIG page:

| Platform | URL |
|----------|-----|
| iOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-ios` |
| iPadOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-ipados` |
| macOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-macos` |
| watchOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-watchos` |
| visionOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-visionos` |
| Games | `https://developer.apple.com/design/human-interface-guidelines/designing-for-games` |

These pages cover platform-specific screen sizes, input methods, and capabilities. Read them before making layout decisions — iPhone and iPad have different navigation patterns, Mac has a menu bar and pointer precision, watchOS is glanceable, and visionOS is spatial.

### 2. Apply Foundational Design Rules

Consult `references/hig-navigation.md`. Identify which foundation topics are relevant to the current task (typography, color, layout, icons, accessibility, etc.), then `web_fetch` those pages. The navigation file is the map; the HIG pages are the territory. Do not guess font sizes, tap target minimums, or color contrast rules — fetch the current values.

### 3. Plan Information Architecture

Choose one of these top-level structures. If none fits cleanly, reconsider whether the IA is clear enough:

- **Tab bar** (iOS, iPadOS): 3–5 flat top-level destinations. Use `TabView` on iPhone; prefer sidebar on iPad.
- **Sidebar / split view** (iPadOS, macOS, visionOS): Hierarchical or flattened navigation with detail pane. `NavigationSplitView`.
- **Hierarchical stack** (iOS, watchOS): Drill-down lists with back navigation. `NavigationStack`.
- **Document-based** (iOS, macOS): Each document is an independent window/scene. `DocumentGroup`.

Custom navigation chrome (custom tab bars, non-standard back buttons, gesture-based nav with no affordance) almost always signals an information architecture problem. Users already know how the system controls work — leverage that fluency.

### 4. Implement in SwiftUI

Read `references/swiftui-patterns.md`. It covers view composition (extract small views, use `@ViewBuilder`, avoid `AnyView`), state management (`@Observable`, `@State`, `@Binding`, `@Environment`), modifier order, side effects (`.task`, `.onAppear`), navigation APIs, and list performance.

Read `references/code-style.md`. It covers naming conventions, access control, `let` over `var`, optional handling, error handling, trailing closures, doc comments, and file length limits.

Read `references/localization.md`. It covers `Text` localization behavior, `FormatStyle` formatters, plurals, RTL layout, String Catalogs, and pseudo-localization testing. Even single-language apps benefit from getting the formatting and text infrastructure right early.

### 5. Structure the Project

Read `references/project-structure.md`. Organize by feature, not by type. Every screen or cohesive flow gets its own folder containing its view, its `@Observable` model, and its subviews. Centralize reusable design components in a `DesignSystem/` folder. Domain types shared across features go in `Models/`. Stateful non-UI code (networking, persistence) goes in `Services/`.

### 6. Verify System Behaviors

Before declaring the design complete, verify every item on this checklist:

- [ ] **Dark Mode**: All colors adapt. No fixed black or white anywhere.
- [ ] **Dynamic Type**: Test at the largest accessibility size. No text truncation, no broken layouts.
- [ ] **VoiceOver**: Every interactive element has an accessibility label. Decorative elements are hidden from VoiceOver.
- [ ] **Empty / Loading / Error**: Each of these states is designed, not just the ideal-data happy path.
- [ ] **Keyboard shortcuts** (Mac, iPad): Common actions have `keyboardShortcut(_:)` modifiers.
- [ ] **Hover effects** (Mac, iPad): Interactive elements respond to pointer hover with `.onHover` or `ButtonStyle`.
- [ ] **Haptics**: Meaningful state transitions (success, failure, selection, confirmation) trigger appropriate haptic feedback via `sensoryFeedback(_:_:)`.
- [ ] **Safe areas**: Content respects safe area insets. No content hidden behind the notch, Dynamic Island, home indicator, or camera housing.
- [ ] **Localization**: No hard-coded formatted strings (`String(format:)` with dates/numbers). Dates and numbers use `FormatStyle`. Pseudo-localization enabled and tested. RTL layout verified (Arabic or Hebrew).

## Anti-patterns

Never do these:

- **Hard-coded colors or font sizes.** Use `Color(.systemBackground)`, `.primary`, `.secondary`, `Font.body`, etc. Hard-coding breaks Dark Mode, Dynamic Type, and platform consistency.
- **`AnyView` in regular code.** Use `@ViewBuilder` or `Group` instead. `AnyView` destroys SwiftUI's diffing optimizations and obscures intent.
- **Reimplementing system UI.** No custom tab bars, custom back buttons, or custom toggle styles. Users have muscle memory for the system controls — leverage it.
- **MVVM ViewModel per view by default.** With `@Observable` (Swift 5.9+), a single `@Observable` model shared across a feature's views is usually cleaner than one ViewModel per screen. Don't add a layer of indirection without a specific reason.
- **Splash screens, welcome modals, tutorial overlays.** Get to content immediately. If onboarding education is essential, show it contextually in empty states. A splash screen before every launch is an anti-pattern.
- **Translating Material/Android patterns.** No floating action buttons, no hamburger menus, no drag-handle bottom sheets as primary navigation, no snackbars. Each platform has its own navigation vocabulary — respect it.
- **`GeometryReader` everywhere.** Needing `GeometryReader` in most views is a signal you're missing layout primitives (`HStack`, `VStack`, `.frame`, `.layoutPriority`, `ViewThatFits`). Use it only as a last resort.
- **Hard-coded formatted strings.** `String(format: "%.2f", value)` ignores locale (decimal separator, digit grouping). Use `value.formatted()` or `Text(value, format: .number)`. Same for dates — `Text(date, format: .dateTime)` adapts to the user's calendar.

## When the Brief Is Ambiguous

Ask 1–2 focused questions (which platform, what's the single most important screen), make reasonable assumptions for the rest, and proceed. For quick prototypes, don't over-interrogate — just pick sensible defaults and note what you assumed. For production designs, confirm the platform and core navigation pattern before writing code.

## Reference Files

Read these when the workflow directs you to:

- `references/hig-navigation.md` — HIG URL inventory. Read before fetching any design page.
- `references/swiftui-patterns.md` — View composition, state management, navigation, list performance. Read before writing SwiftUI code.
- `references/code-style.md` — Naming, idioms, file conventions. Read before structuring code.
- `references/project-structure.md` — File/folder layout and when to use Swift Packages. Read before scaffolding a project.
- `references/localization.md` — `Text` API, `FormatStyle`, plurals, RTL, String Catalogs, pseudo-localization testing. Read before writing any user-facing text.
