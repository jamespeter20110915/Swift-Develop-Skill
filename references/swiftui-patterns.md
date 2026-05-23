# SwiftUI Patterns

Idiomatic patterns for composing views, managing state, ordering modifiers, handling side effects, navigating, and optimizing lists. These conventions apply to all Apple platforms.

## View Composition

### One View, One Responsibility

Extract subviews when `body` exceeds ~30 lines. Many small `View` structs are better than one large one — the compiler diffs more efficiently and intent is clearer.

```swift
// Before: a 100-line body
struct FeedScreen: View {
    var body: some View {
        ScrollView {
            VStack {
                // 20 lines of header
                // 40 lines of post list
                // 20 lines of footer
            }
        }
    }
}

// After: composed
struct FeedScreen: View {
    var body: some View {
        ScrollView {
            VStack {
                FeedHeader()
                PostList()
                FeedFooter()
            }
        }
    }
}
```

### Avoid `AnyView`

`AnyView` erases type information, defeating SwiftUI's diffing optimizations. Use `@ViewBuilder` instead.

```swift
// Wrong
func detailView(for item: Item) -> some View {
    if item.isSpecial {
        return AnyView(SpecialDetail(item: item))
    } else {
        return AnyView(RegularDetail(item: item))
    }
}

// Right
@ViewBuilder
func detailView(for item: Item) -> some View {
    if item.isSpecial {
        SpecialDetail(item: item)
    } else {
        RegularDetail(item: item)
    }
}
```

### `Group` vs. `VStack` vs. Nothing

- **`Group`** — logical grouping with no layout effect. Use to work around the 10-view limit, share modifiers, or tag content for `toolbar`/`picker`.
- **`VStack`/`HStack`** — actual layout (spacing, alignment). Use when you need the arranged layout.
- **Nothing** — if a single view is already the root of `body`, wrapping it in a stack adds unnecessary layout work.

```swift
// Group to share modifiers across heterogeneous content
Group {
    Text("Title")
    Image(systemName: "star")
    Rectangle()
}
.font(.title)
.foregroundStyle(.primary)
```

## State Management

### `@State` — View-Local Value

Use for value-type state owned by a single view. Always `private`.

```swift
struct CounterView: View {
    @State private var count = 0

    var body: some View {
        Button("Count: \(count)") { count += 1 }
    }
}
```

### `@Binding` — Pass-Through Mutation

Use when a child view needs to read and write a value owned by its parent.

```swift
struct ToggleRow: View {
    @Binding var isOn: Bool
    let label: String

    var body: some View {
        Toggle(label, isOn: $isOn)
    }
}
```

### `@Observable` — Shared Reference State (Swift 5.9+)

The Observation framework is the preferred way to share state across views. Mark the class with `@Observable`, no `@Published` needed.

```swift
@Observable
final class FeedModel {
    var posts: [Post] = []
    var isLoading = false
    var error: Error?

    func load() async {
        isLoading = true
        defer { isLoading = false }
        do {
            posts = try await APIClient.fetchPosts()
        } catch {
            self.error = error
        }
    }
}

struct FeedScreen: View {
    @State private var model = FeedModel()

    var body: some View {
        List(model.posts) { post in
            PostRow(post: post)
        }
        .task { await model.load() }
    }
}
```

### `@Bindable` — Binding to `@Observable` Properties

When a child view needs two-way binding to a property on an `@Observable` instance passed in by a parent, use `@Bindable` (not `@Binding`, not `@State`):

```swift
@Observable
final class PostModel {
    var title = ""
    var body = ""
}

struct EditPostView: View {
    @Bindable var post: PostModel

    var body: some View {
        Form {
            TextField("Title", text: $post.title)
            TextEditor(text: $post.body)
        }
    }
}
```

The mental model:
- `@State` — own an `@Observable` instance.
- `@Bindable` — receive an `@Observable` instance and edit its properties.
- `@Binding` — receive a single value and write it back to the parent.

`@Bindable` lets you use `$` to derive a `Binding` for any property of the observable, without the parent having to expose individual bindings for each field.

### `@Environment` — Values from the View Tree

Use for system-provided values (`\.dismiss`, `\.colorScheme`) and custom environment values injected higher up.

```swift
struct SettingsView: View {
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        NavigationStack {
            Form {
                Text("Settings content here")
            }
            .toolbar {
                ToolbarItem(placement: .confirmationAction) {
                    Button("Done") { dismiss() }
                }
            }
        }
    }
}
```

`@Environment(\.colorScheme)` is also available for adapting to light/dark appearance:

```swift
struct AdaptiveIcon: View {
    @Environment(\.colorScheme) private var colorScheme

    var body: some View {
        Image(systemName: colorScheme == .dark ? "moon.fill" : "sun.max.fill")
    }
}
```

### When to Still Use `@StateObject` / `@ObservedObject`

For new code targeting iOS 17+ / macOS 14+, prefer `@Observable`. Use `@StateObject` and `@ObservedObject` only when:
- Back-deploying to iOS 16 or earlier
- Interfacing with existing `ObservableObject`-based code you cannot migrate yet

### Where State Should Live

Place state at the lowest common ancestor. If two sibling views need the same data, lift it to their parent. If only a subtree needs it, inject it there — don't pollute the root view. A deeply nested child that needs the value should get it via `@Environment` rather than prop-drilling through every intermediate view.

## Modifier Order

Modifiers apply from the outside in — each modifier wraps the result of all previous modifiers. The conceptual order is:

**Layout → Appearance → Behavior → Accessibility**

### The Classic Example

```swift
// Padding outside background: background fills the padded area
Text("Hello")
    .padding(16)
    .background(.blue)    // blue fills the 16pt padded area

// Background inside padding: background fills only the text
Text("Hello")
    .background(.blue)    // blue fills only the text bounds
    .padding(16)          // padding is transparent (default background)
```

### When Order Matters Most

- **`.padding()` before `.background()`** — background fills the padded area (the usual intent for buttons, rows, cards).
- **`.frame()` before `.clipShape()`** — clip the framed area, not the content.
- **`.background()` before `.clipShape()`** — clip both content and background together.
- **`.overlay()` position** — overlays always render on top regardless of preceding modifiers.
- **Accessibility last** — `.accessibilityLabel(_:)` and `.accessibilityHidden(_:)` should be the outermost modifiers so they accurately describe the final rendered element.

```swift
// Building a card: order matters
VStack(alignment: .leading) {
    Text(title).font(.headline)
    Text(subtitle).font(.subheadline).foregroundStyle(.secondary)
}
.padding(16)                  // 1. layout: internal spacing
.background(.regularMaterial) // 2. appearance: material fills padded area
.clipShape(RoundedRectangle(cornerRadius: 12))  // 3. appearance: rounded corners
.shadow(radius: 2)            // 4. appearance: shadow on the clipped shape
.accessibilityElement(children: .combine)       // 5. accessibility: describe as one unit
.accessibilityLabel("\(title), \(subtitle)")    // 6. accessibility: meaningful label
```

## Side Effects

### `.task { }` — Async Work Tied to View Lifetime

The primary mechanism for async work. Auto-cancels when the view disappears. Prefer over `.onAppear { Task { ... } }`.

```swift
struct PostScreen: View {
    @State private var post: Post?

    var body: some View {
        Group {
            if let post {
                PostDetail(post: post)
            } else {
                ProgressView()
            }
        }
        .task {
            // Cancelled automatically if view disappears before completion
            post = await loadPost()
        }
    }
}
```

### `.onAppear` / `.onDisappear` — Sync Setup

Use for fire-and-forget work: analytics, logging, starting a timer. Not for async — use `.task` instead.

### `.refreshable { }` — Pull-to-Refresh

```swift
List(model.items) { item in ... }
    .refreshable {
        await model.refresh()
    }
```

### Never Do Work in `body`

`body` is called on every render. It must be a pure, cheap function that returns a view tree. No networking, no heavy computation, no side effects.

## Navigation

### `NavigationStack` — Hierarchical Drill-Down (iOS 16+)

```swift
struct ContentView: View {
    @State private var path = NavigationPath()

    var body: some View {
        NavigationStack(path: $path) {
            List(categories) { category in
                NavigationLink(category.name, value: category)
            }
            .navigationDestination(for: Category.self) { category in
                CategoryDetail(category: category, path: $path)
            }
        }
    }
}
```

### `NavigationSplitView` — Sidebar Layout (iPadOS, macOS, visionOS)

```swift
NavigationSplitView {
    Sidebar()
} content: {
    ContentList()
} detail: {
    DetailView()
}
```

### `TabView` — Top-Level Tabs

Use on iPhone as primary navigation. On iPad and Mac, tabs should supplement a sidebar, not replace it — Apple's guidance recommends sidebar for iPadOS.

```swift
TabView(selection: $selection) {
    FeedScreen()
        .tabItem { Label("Feed", systemImage: "house") }
        .tag(Tab.feed)
    SearchScreen()
        .tabItem { Label("Search", systemImage: "magnifyingglass") }
        .tag(Tab.search)
    ProfileScreen()
        .tabItem { Label("Profile", systemImage: "person") }
        .tag(Tab.profile)
}
```

### Sheet vs. Full-Screen Cover vs. Inspector vs. Popover

| Modality | Use when |
|----------|----------|
| `.sheet` | Presenting a related sub-task (compose, filter, pick). Dismissible by drag. Use detents for partial-height sheets. |
| `.fullScreenCover` | Immersive task that demands full attention (camera, media playback). Avoid for forms or settings. |
| `.inspector` | Sidebar-adjacent detail pane (iPadOS, macOS). Good for inspectors and info panels. |
| `.popover` | Lightweight context anchored to a source element. On iPad/Mac renders as a true popover. On iPhone it adapts to a sheet by default; use `.presentationCompactAdaptation(.popover)` only when a popover-style presentation on iPhone is genuinely appropriate. |

## Lists and Performance

### `List` vs. `ScrollView { LazyVStack }`

- **`List`** — platform-native styling, swipe actions, selection, built-in cell reuse. Prefer for standard list content.
- **`ScrollView { LazyVStack }`** — custom layout, mixed content (text, images, custom shapes), non-standard spacing. Cells are lazily created but not reused, so avoid for very long lists (> a few hundred items).

### Stable `id` for `ForEach`

Always provide a stable, unique identifier. Never use indices (`\.self` on `Int`) for data that can change — it causes unnecessary view recreation and animation artifacts.

```swift
// Right: stable model ID
ForEach(items) { item in
    ItemRow(item: item)
}

// Wrong: index-based when items can reorder or change
ForEach(Array(items.enumerated()), id: \.offset) { index, item in
    ItemRow(item: item)
}
```

### `.listStyle()` Per Platform

Apply only one `listStyle` per `List` — they don't stack.

```swift
// iPhone settings or forms
List { ... }
    .listStyle(.insetGrouped)

// iPhone plain list (no inset, no grouping)
List { ... }
    .listStyle(.plain)

// iPad / Mac sidebar — only inside NavigationSplitView's first column
NavigationSplitView {
    List(selection: $selection) { ... }
        .listStyle(.sidebar)
} detail: { ... }

// macOS table-like list
List { ... }
    .listStyle(.bordered)

// watchOS rotating carousel
List { ... }
    .listStyle(.carousel)
```
