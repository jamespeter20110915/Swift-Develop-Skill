# Project Structure

Organizing files and folders in a SwiftUI project for long-term maintainability.

## Organize by Feature, Not by Type

The "split by type" layout (`Views/`, `Models/`, `ViewModels/`, `Helpers/`) scatters related code as the project grows. Changing a single feature means touching files across five folders, and understanding the app requires reconstructing features from scattered pieces.

Organize by feature instead. Each feature is a folder containing everything needed for one screen or one cohesive flow:

```
MyApp/
├── App/
│   ├── MyApp.swift
│   └── AppDelegate.swift          # only if needed (rare in modern SwiftUI)
├── Features/
│   ├── Feed/
│   │   ├── FeedView.swift
│   │   ├── FeedModel.swift        # @Observable class
│   │   ├── FeedRow.swift
│   │   ├── FeedFilterSheet.swift
│   │   └── EmptyFeedView.swift
│   ├── PostDetail/
│   │   ├── PostDetailView.swift
│   │   ├── PostDetailModel.swift
│   │   ├── CommentSection.swift
│   │   └── ShareSheet.swift
│   ├── Profile/
│   │   ├── ProfileView.swift
│   │   ├── ProfileModel.swift
│   │   └── EditProfileView.swift
│   └── Settings/
│       ├── SettingsView.swift
│       ├── SettingsModel.swift
│       ├── AccountSection.swift
│       ├── AppearanceSection.swift
│       └── NotificationSection.swift
├── DesignSystem/
│   ├── Colors.swift
│   ├── Typography.swift
│   ├── ButtonStyles.swift
│   ├── TextFieldStyles.swift
│   ├── BadgeView.swift
│   ├── AsyncButton.swift
│   └── Modifiers/
│       ├── CardModifier.swift
│       └── ShimmerModifier.swift
├── Models/
│   ├── Post.swift
│   ├── User.swift
│   ├── Comment.swift
│   └── APIError.swift
├── Services/
│   ├── APIClient.swift
│   ├── AuthService.swift
│   ├── PersistenceController.swift
│   ├── AnalyticsService.swift
│   └── ImageCache.swift
├── Extensions/
│   ├── Date+Formatting.swift
│   ├── String+Validation.swift
│   └── View+ConditionalModifier.swift
├── Resources/
│   ├── Assets.xcassets
│   ├── Colors.xcassets             # named colors for DesignSystem
│   ├── Localizable.xcstrings
│   └── Sounds/
└── Tests/
    ├── FeedFeatureTests/
    ├── ServicesTests/
    └── DesignSystemSnapshotTests/
```

## Folder Purposes

### `App/`

The entry point. Contains `@main App` struct and scene configuration. Keep it minimal — it bootstraps the app and delegates to features. Avoid placing business logic here.

### `Features/`

Each folder represents one screen or one cohesive flow. A feature folder contains:
- The **View** (the screen itself)
- The **`@Observable` model** (data + behavior for that screen)
- **Subviews** specific to this feature (not reused elsewhere)
- **Empty/error/loading states** for this screen

Subviews that are small and used only within the feature can live in the same file as their parent. Extract to a separate file when they exceed ~30 lines or are referenced from multiple places within the feature.

### `DesignSystem/`

The single source of truth for visual design. Every reusable styled component, custom modifier, and design token lives here:

- **Colors** — named colors, gradients, and semantic color aliases. Extend system colors rather than replacing them.
- **Typography** — custom font styles if the app's brand departs from system fonts. Most apps don't need this.
- **Styled components** — `BadgeView`, `AsyncButton` (a button that shows a spinner during async work), `ChipView`, etc.
- **Custom modifiers** — `.cardStyle()`, `.shimmer()`, `.sectionHeader()`. These let features apply design-system styling declaratively.

Features should never define ad-hoc reusable styles. If a style is used in more than one feature, it belongs in `DesignSystem/`.

### `Models/`

Domain types shared across features. These are plain structs/enums with no view dependencies. `Post`, `User`, `Comment`, `APIError`, `SearchFilter` — types that represent the app's data, not any specific screen's state.

Keep models thin. They define the shape of data. Business logic belongs in services or in the `@Observable` model for the feature that owns the operation.

### `Services/`

Stateful non-UI code. Networking, persistence, authentication, analytics, image caching — anything that talks to the outside world or manages shared mutable state.

Services are typically long-lived singletons or injected via `@Environment`. Avoid creating service instances inside view `body` or feature models — inject them.

```swift
@Observable
final class FeedModel {
    private let api: APIClient
    private let analytics: AnalyticsService

    init(api: APIClient, analytics: AnalyticsService) {
        self.api = api
        self.analytics = analytics
    }
}
```

### `Extensions/`

Extensions on system or third-party types. Group by the type being extended (`Date+Formatting.swift`, not `FormattingExtensions.swift`). Prefer composition over extensions when the added behavior is specific to one feature — only put truly shared extensions here.

### `Resources/`

Assets, localized strings, fonts, sounds. `Localizable.xcstrings` uses Xcode's String Catalog format (recommended over the legacy `.strings`/`.stringsdict` format).

## Naming Conventions

Files match their primary type name:

| File | Contains |
|------|----------|
| `FeedView.swift` | `struct FeedView: View` |
| `FeedModel.swift` | `@Observable final class FeedModel` |
| `Post.swift` | `struct Post` |
| `APIClient.swift` | `final class APIClient` |
| `CardModifier.swift` | `struct CardModifier: ViewModifier` |

When multiple related small types share a file, name the file after the primary type or the concept.

## When to Make a Swift Package

Extract into a local Swift Package when a subsystem:
- Exceeds ~5 files
- Has clear boundaries (it could be described in one sentence)
- Is independently testable
- Changes at a different cadence than the main app

Good candidates: a networking layer, an analytics framework, a custom design system for a multi-app brand, a persistence layer.

A local Swift Package is a folder in the repo with its own `Package.swift`. Add it to the Xcode project via `File > Add Package Dependencies > Add Local`. The package has its own `Sources/` and `Tests/`, and the main app imports it like any other dependency.

Tradeoffs: Swift Packages add build complexity (separate target, module boundary) and cannot reference app-level types directly. Don't extract prematurely — a 3-file feature is fine in `Features/`. Extract when the boundary pays for itself through independent compilation, testing, or reuse.
