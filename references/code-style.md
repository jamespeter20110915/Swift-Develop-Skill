# Code Style

Swift conventions for clarity, consistency, and maintainability. These apply to all Swift code, with emphasis on SwiftUI-specific patterns.

## Naming

**Types** (structs, classes, enums, protocols) use `UpperCamelCase`:

```swift
struct FeedView: View { ... }
enum PostStatus { ... }
protocol ContentLoadable { ... }
```

**Values, functions, and properties** use `lowerCamelCase`:

```swift
let postCount: Int
func loadPosts() async throws -> [Post]
var isLoading: Bool
```

**Methods read as English sentences** when called with their arguments. This is why Swift parameter labels are part of the API:

```swift
// Reads: "view dot add subview colon postView"
view.addSubview(postView)
// Reads: "array dot append colon newItem"
array.append(newItem)
// Reads: "index dot advanced by colon 3"
index.advanced(by: 3)
```

**Boolean properties** use `is`, `has`, or `should`:

```swift
var isLoading: Bool
var hasUnreadMessages: Bool
var shouldShowOnboarding: Bool
```

**Avoid abbreviations.** `postCount` not `pCnt`. `errorDescription` not `errDesc`. The only acceptable abbreviations are universally understood ones like `id`, `url`, `min`, `max`.

## Access Control

Default to `private`. Widen only when a caller genuinely needs access.

```swift
struct PostRow: View {
    @State private var isExpanded = false   // internal state: private
    let post: Post                          // injected: implicit internal

    var body: some View { ... }

    private var formattedDate: String { ... }
}
```

Reserve `public` for the API surface of a module (Swift Package, framework). `internal` is the default and doesn't need to be written — it says "available within this module, not part of the API contract."

## `let` Over `var`

Prefer immutability. SwiftUI views are value types (structs), so this is natural — properties that don't mutate are `let`:

```swift
struct PostRow: View {
    let post: Post         // never changes for this instance
    let onTap: () -> Void  // closure reference is constant

    @State private var isExpanded = false  // only this mutates
}
```

Use computed properties with `let`-based storage rather than mutable state whenever possible:

```swift
let posts: [Post]
var recentPosts: [Post] { Array(posts.prefix(5)) }  // derived, not stored
```

## Optionals

Prefer `if let` and `guard let` over force-unwrap:

```swift
// Preferred
if let user = currentUser {
    ProfileView(user: user)
}

// For early exits
guard let user = currentUser else {
    return LoginView()
}
```

Use `??` for concise defaults:

```swift
Text(post.author?.name ?? "Unknown Author")
```

Force-unwrap (`!`) only when the invariant is truly guaranteed by construction — e.g., a resource bundled with the app:

```swift
let url = Bundle.main.url(forResource: "config", withExtension: "json")!
```

## Error Handling

`throws` over optional returns when failure carries meaning:

```swift
// Right: caller knows what went wrong
func loadPosts() async throws -> [Post]

// Wrong: silent failure
func loadPosts() async -> [Post]?  // was it a network error? empty response?
```

`Result` is rarely needed in modern Swift. `async throws` covers async error handling. `do`/`catch` covers sync cases. Reach for `Result` only when storing a success/failure value as data (e.g., in a completion handler that will be called later).

```swift
// Modern: async throws
func refresh() async {
    do {
        posts = try await loadPosts()
    } catch {
        self.error = error
    }
}
```

## Trailing Closures

Use trailing closure syntax when the last parameter is a closure:

```swift
Button("Save") {
    save()
}

List(posts) { post in
    PostRow(post: post)
}
```

For multiple trailing closures (Swift 5.3+), label the non-first closures. This is idiomatic for SwiftUI modifiers like `.alert()`, `.confirmationDialog()`, `.sheet()`:

```swift
.alert("Delete Post?", isPresented: $showDeleteAlert) {
    Button("Delete", role: .destructive) { deletePost() }
    Button("Cancel", role: .cancel) {}
} message: {
    Text("This action cannot be undone.")
}
```

## Doc Comments

`///` for public API documentation. Keep it concise — describe purpose, not implementation:

```swift
/// A scrollable list of posts with pull-to-refresh and infinite scroll.
struct PostList: View { ... }

/// Loads the next page of posts if the given post is the last visible one.
func loadMoreIfNeeded(current post: Post) async { ... }
```

## File Organization with `// MARK: -`

Group related members with MARK comments. A consistent section order:

```swift
// MARK: - Properties
// MARK: - Body
// MARK: - Actions
// MARK: - Subviews
// MARK: - Previews
```

```swift
struct PostRow: View {
    // MARK: - Properties
    let post: Post
    @State private var isExpanded = false

    // MARK: - Body
    var body: some View {
        VStack { ... }
    }

    // MARK: - Actions
    private func toggleExpanded() { ... }

    // MARK: - Subviews
    private var expandedContent: some View { ... }
}
```

## File Length

Keep files under ~300 lines. If longer, the file likely has multiple responsibilities and should be split. A screen with subviews can keep them in the same file if they're small (< 30 lines each) and specific to that screen. Once a subview is reusable across features, extract it to its own file.

## Avoid These

- **`try!`** — crashes on error. Use `try` with `do`/`catch` or `try?` when failure is acceptable.
- **Implicitly unwrapped optionals (`String!`)** — same risk as force-unwrap, but hidden. Use regular optionals.
- **`fatalError` outside unreachable code** — crashes the app. Use `assertionFailure` for developer-time checks that shouldn't terminate production builds.
- **Empty closures `{ }` on buttons** — buttons that do nothing are confusing. If a button is a placeholder, mark it with a `// TODO:` comment.
- **`.padding()` with no arguments on text** — the default padding varies. Be explicit: `.padding(.horizontal, 16)` is self-documenting.
- **Mixing `@StateObject` and `@ObservedObject` in the same view** — if a view creates an `ObservableObject`, it owns it (`@StateObject`). If it receives one, it observes (`@ObservedObject`). Never `@ObservedObject` for an object the view itself creates — it will be re-created on every render.
