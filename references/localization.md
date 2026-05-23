# Localization

Apple platforms have global reach. Every app should be localizable from day one, even if you only ship one language initially. Retrofitting localization is painful — building it in from the start is cheap.

## Foundation

### `Text` and Localization

SwiftUI `Text` has three initializers with different localization behavior:

```swift
// Localized — looks up the key in the app's string catalog
Text("Welcome to MyApp")

// Localized with string interpolation (table lookup, not concatenation)
Text("Hello, \(username)")

// NOT localized — renders the string as-is, bypassing the catalog
Text(verbatim: "https://example.com")

// Explicit localization via String(localized:)
Text(String(localized: "Welcome to MyApp"))
```

The rule: use a string literal for user-facing text. It becomes the key for localization lookups. For URLs, code values, debug strings, and anything that should never be translated, use `Text(verbatim:)`.

### `Label` and SF Symbols

`Label` with an SF Symbol doesn't need the symbol name localized — only the title:

```swift
// The symbol name "house" is not localized; "Home" is
Label("Home", systemImage: "house")
```

### `LocalizedStringKey` vs. `String`

`Text` accepts both, but they behave differently:

```swift
let key: LocalizedStringKey = "Welcome"   // looks up "Welcome" in catalog
let string: String = "Welcome"             // renders "Welcome" literally (unless you use String(localized:))
```

Be careful with computed properties that return `String` — they bypass localization:

```swift
// Wrong: returns String, no localization
var title: String { isLoggedIn ? "Dashboard" : "Welcome" }

// Right: returns LocalizedStringKey or uses Text directly
@ViewBuilder
var title: some View {
    if isLoggedIn {
        Text("Dashboard")
    } else {
        Text("Welcome")
    }
}
```

## Formatting

Never build formatted strings by hand. Use Foundation's format styles — they adapt to the user's locale, calendar, and measurement system.

```swift
// Dates
Text(post.date, format: .dateTime.month(.wide).day().year())
Text(event.start, format: .dateTime.hour().minute())
Text(date, format: .relative(presentation: .named))  // "Yesterday", "2 hours ago"

// Numbers
Text(count, format: .number)                    // "1,234" or "1.234" depending on locale
Text(percentage, format: .percent)              // "42%" or "42 %" (different locale spaces)
Text(price, format: .currency(code: "EUR"))     // "€12,34" or "12,34€" depending on locale

// Measurements
Text(distance, format: .measurement(width: .narrow))  // "5mi" or "8km"
Text(temperature, format: .measurement(width: .abbreviated))
```

`Text(_:format:)` accepts any `FormatStyle`. Foundation provides `Date.FormatStyle`, `Number.FormatStyle`, `Measurement.FormatStyle`, `PersonNameComponents.FormatStyle`, `ByteCountFormatStyle`, and others. Use them — never `String(format:)` with hard-coded format specifiers.

## Plurals

Different languages have different plural rules. English has "1 post" / "2 posts". Arabic has forms for zero, one, two, few, many. Russian has different forms for numbers ending in 1 vs. 2–4 vs. 5+. Hard-coding "item(s)" is a telltale sign the developer didn't think about localization.

Use Xcode's **String Catalog** (`.xcstrings`), which handles plurals natively. Add a key with plural variants:

```
"post_count" → {count} posts   // English plural rule automatically applies
```

The String Catalog handles the plural rules for every language you localize into. In code, reference it with string interpolation:

```swift
Text("post_count \(postCount)")
```

For legacy `.stringsdict` files, the format is more verbose but functionally equivalent. String Catalogs are the recommended format for Xcode 15+.

## Right-to-Left

SwiftUI handles layout direction automatically. `HStack` becomes right-aligned, `NavigationStack` slides from the right, and text flows RTL. Most views need no special work.

Exceptions — elements that should NOT mirror:

```swift
// Icons that represent physical direction (e.g., a forward arrow in a media player
// should always point right regardless of language direction)
Image(systemName: "forward.fill")
    .flipsForRightToLeftLayoutDirection(false)

// Time-based timelines should always flow left-to-right
TimelineView(.animation)
    .flipsForRightToLeftLayoutDirection(false)
```

Test RTL by setting the language to Arabic or Hebrew in the simulator, or with the environment override:

```swift
ContentView()
    .environment(\.layoutDirection, .rightToLeft)
```

## String Catalogs

Use `.xcstrings` (Xcode's String Catalog format). One catalog per language, stored in `Resources/`. Xcode 15+ manages the catalog file, and extraction of new keys is automatic.

```swift
// This string literal is automatically extracted into the String Catalog
Text("Share Post")
```

The catalog tracks translation state per string. Untranslated strings fall back to English (or the development language). Use `String(localized:)` with a comment to give translators context:

```swift
Text(String(localized: "Following \(friendCount) accounts", comment: "Count text on profile showing follower badge"))
```

## Testing

Test localization early, even before translations exist.

**Pseudo-localization** doubles string lengths and inserts accent characters without needing real translations. Enable it in the scheme editor: Run → Options → App Language → "Pseudo Language". This catches:
- Hard-coded widths that truncate with longer text
- English-only assumptions in string concatenation
- Missing plural forms

**Double-length strings** stress-test layout. Enable in the same scheme editor. Many languages (German, Finnish, Russian) produce text 30–50% longer than English, so if your app works with double-length, it likely works with those translations.

**Accessibility & localization** — test the largest Dynamic Type size with pseudo-localization. The combination reveals truncation issues that neither test alone would catch.

## HIG Guidance

For tone, terminology, and copywriting, `web_fetch` the HIG Writing page:

`https://developer.apple.com/design/human-interface-guidelines/writing`

Use inclusive, globally-aware language — avoid idioms and culture-specific metaphors that don't translate. Refer to the HIG Inclusion page for bias and representation guidance.
