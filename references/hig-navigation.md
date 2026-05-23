# HIG Navigation Map

When designing any aspect of an Apple platform app, identify the relevant topic in this file and `web_fetch` the corresponding URL. This file is a navigation map; Apple's HIG is the authoritative source. Do not paraphrase from memory — fetch the current page.

## Get Started (Per-Platform Design)

Fetch the page for each target platform before making layout or navigation decisions. Each page covers screen sizes, input methods, and platform-specific capabilities.

| Platform | URL | Consult when |
|----------|-----|-------------|
| iOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-ios` | Designing for iPhone — touch-first, compact screens, Dynamic Island, reachability |
| iPadOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-ipados` | Designing for iPad — multitasking, Split View, Stage Manager, Apple Pencil, pointer |
| macOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-macos` | Designing for Mac — menu bar, window management, keyboard-heavy, precise pointer |
| watchOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-watchos` | Designing for Apple Watch — glanceable, Digital Crown, haptic-first, actionable notifications |
| visionOS | `https://developer.apple.com/design/human-interface-guidelines/designing-for-visionos` | Designing for Apple Vision Pro — spatial windows, eye/hand input, immersive spaces |
| Games | `https://developer.apple.com/design/human-interface-guidelines/designing-for-games` | Designing games across platforms — Metal, Game Center, controllers |

## Foundations

These are the universal design rules. Consult the relevant topic whenever it affects your current task.

| Topic | URL | Consult when |
|-------|-----|-------------|
| Accessibility | `https://developer.apple.com/design/human-interface-guidelines/accessibility` | Any UI involving text, interaction, color, or motion. Not optional — foundational. |
| App icons | `https://developer.apple.com/design/human-interface-guidelines/app-icons` | Designing the app icon. Covers sizes, corner radii, and platform variants. |
| Branding | `https://developer.apple.com/design/human-interface-guidelines/branding` | Incorporating brand identity without breaking platform conventions. |
| Color | `https://developer.apple.com/design/human-interface-guidelines/color` | Choosing colors. Covers system colors, semantic colors, contrast, and accessibility. |
| Dark Mode | `https://developer.apple.com/design/human-interface-guidelines/dark-mode` | Ensuring the UI works in both light and dark appearances. |
| Icons | `https://developer.apple.com/design/human-interface-guidelines/icons` | Using SF Symbols and custom glyphs. Covers sizing, weight, and metaphor. |
| Images | `https://developer.apple.com/design/human-interface-guidelines/images` | Displaying photos and illustrations. Covers aspect ratios, resolution, and placeholders. |
| Immersive experiences | `https://developer.apple.com/design/human-interface-guidelines/immersive-experiences` | visionOS immersive spaces and augmented reality across platforms. |
| Inclusion | `https://developer.apple.com/design/human-interface-guidelines/inclusion` | Avoiding bias in language, imagery, and defaults. Write for everyone. |
| Layout | `https://developer.apple.com/design/human-interface-guidelines/layout` | Safe areas, margins, grids, and spacing. Consult before any layout work. |
| Materials | `https://developer.apple.com/design/human-interface-guidelines/materials` | Translucency effects (sidebar, tab bar, toolbar, popover materials). |
| Motion | `https://developer.apple.com/design/human-interface-guidelines/motion` | Animations and transitions. Covers timing curves, purpose-driven motion, Reduce Motion. |
| Privacy | `https://developer.apple.com/design/human-interface-guidelines/privacy` | Permission requests, data disclosure UI, privacy nutrition labels. |
| Right to left | `https://developer.apple.com/design/human-interface-guidelines/right-to-left` | Supporting RTL languages. Layout should mirror; not everything should flip. |
| SF Symbols | `https://developer.apple.com/design/human-interface-guidelines/sf-symbols` | Choosing and configuring SF Symbols. Covers weight, scale, and rendering modes. |
| Spatial layout | `https://developer.apple.com/design/human-interface-guidelines/spatial-layout` | visionOS-specific layout rules for windows, volumes, and spaces. |
| Typography | `https://developer.apple.com/design/human-interface-guidelines/typography` | Font choices, sizes, weights, and Dynamic Type scaling. Consult before picking any font. |
| Writing | `https://developer.apple.com/design/human-interface-guidelines/writing` | Tone, terminology, and copywriting conventions for Apple platforms. |

## Patterns

Common UX patterns. When designing a screen that fits one of these, fetch the page.

| Pattern | URL | Consult when |
|---------|-----|-------------|
| Patterns overview | `https://developer.apple.com/design/human-interface-guidelines/patterns` | Understanding how Apple organizes common UX flows. Start here for unfamiliar patterns. |
| Onboarding | `https://developer.apple.com/design/human-interface-guidelines/onboarding` | Designing first-launch experience. Covers setup, sign-in, and permission priming. |
| Navigation and search | `https://developer.apple.com/design/human-interface-guidelines/navigation-and-search` | Structuring how users move through the app and find content. |
| Settings | `https://developer.apple.com/design/human-interface-guidelines/settings` | Designing in-app settings and preferences. |
| Undo and redo | `https://developer.apple.com/design/human-interface-guidelines/undo-and-redo` | Supporting undo/redo gestures (shake, three-finger, Cmd+Z) and UI affordances. |
| Feedback | `https://developer.apple.com/design/human-interface-guidelines/feedback` | Communicating results — confirmation, error, progress — to the user. |
| File management | `https://developer.apple.com/design/human-interface-guidelines/file-management` | Opening, saving, browsing, and managing documents and files. |
| Notifications | `https://developer.apple.com/design/human-interface-guidelines/notifications` | Push notifications, notification center, and in-app notification UI. |

| Entering data | `https://developer.apple.com/design/human-interface-guidelines/entering-data` | Forms, data entry, validation, and input efficiency. |
| Game Center | `https://developer.apple.com/design/human-interface-guidelines/game-center` | Game Center UI: leaderboards, achievements, challenges, and player profiles. |
| Multitasking | `https://developer.apple.com/design/human-interface-guidelines/multitasking` | Split View, Slide Over, Stage Manager, and multiple window support on iPad. |
| Printing | `https://developer.apple.com/design/human-interface-guidelines/printing` | Print UI and print preview integration. |

## Components

Reusable UI elements. When choosing a control, fetch its page for usage guidelines, not just the API docs.

| Component | URL | Consult when |
|-----------|-----|-------------|
| Components overview | `https://developer.apple.com/design/human-interface-guidelines/components` | Understanding the component taxonomy and choosing the right control. |
| Activity rings | `https://developer.apple.com/design/human-interface-guidelines/activity-rings` | Displaying progress toward movement, exercise, or standing goals. |
| Alerts | `https://developer.apple.com/design/human-interface-guidelines/alerts` | Modal confirmation dialogs. Use sparingly — alert fatigue is real. |
| Buttons | `https://developer.apple.com/design/human-interface-guidelines/buttons` | Styling and labeling buttons. Covers filled, bordered, plain, and action buttons. |
| Charts | `https://developer.apple.com/design/human-interface-guidelines/charts` | Data visualization using Swift Charts. |
| Color wells | `https://developer.apple.com/design/human-interface-guidelines/color-wells` | Color picker controls (primarily macOS). |
| Context menus | `https://developer.apple.com/design/human-interface-guidelines/context-menus` | Long-press / right-click contextual actions. |
| Date and time pickers | `https://developer.apple.com/design/human-interface-guidelines/date-and-time-pickers` | Date, time, and calendar selection controls. |
| Gauges | `https://developer.apple.com/design/human-interface-guidelines/gauges` | Progress display (circular, linear). Distinct from progress indicators. |
| Group boxes | `https://developer.apple.com/design/human-interface-guidelines/group-boxes` | Visual grouping of related controls (primarily macOS). |
| Images and video | `https://developer.apple.com/design/human-interface-guidelines/images-and-video` | Media display components, including `AsyncImage` patterns. |
| Labels | `https://developer.apple.com/design/human-interface-guidelines/labels` | Text display with system styling. Covers `Label` and `LabeledContent`. |
| Lists and tables | `https://developer.apple.com/design/human-interface-guidelines/lists-and-tables` | `List`, `Table`, `Form`, and `OutlineGroup`. |
| Menus | `https://developer.apple.com/design/human-interface-guidelines/menus` | Dropdown menus, `Menu`, `ContextMenu`, and menu bar menus (macOS). |
| Pickers | `https://developer.apple.com/design/human-interface-guidelines/pickers` | Selection from a set of options (wheel, segmented, pop-up). |
| Popovers | `https://developer.apple.com/design/human-interface-guidelines/popovers` | Lightweight transient dialogs anchored to a source element. |
| Progress indicators | `https://developer.apple.com/design/human-interface-guidelines/progress-indicators` | Determinate and indeterminate progress. `ProgressView`. |
| Scroll views | `https://developer.apple.com/design/human-interface-guidelines/scroll-views` | Scrollable content, pagination, and scroll transitions. |
| Search fields | `https://developer.apple.com/design/human-interface-guidelines/search-fields` | Search bar placement, behavior, and result display. |
| Sheets | `https://developer.apple.com/design/human-interface-guidelines/sheets` | Modal and non-modal sheets, detents, and dismissal patterns. |
| Sliders | `https://developer.apple.com/design/human-interface-guidelines/sliders` | Continuous value selection from a range. |
| Steppers | `https://developer.apple.com/design/human-interface-guidelines/steppers` | Incremental value controls. |
| Tab views | `https://developer.apple.com/design/human-interface-guidelines/tab-views` | Tab bar and segmented tab navigation. |
| Text fields | `https://developer.apple.com/design/human-interface-guidelines/text-fields` | Single and multi-line text input. Covers `TextField` and `TextEditor`. |
| Toggles | `https://developer.apple.com/design/human-interface-guidelines/toggles` | On/off switches and checkboxes. |
| Toolbars | `https://developer.apple.com/design/human-interface-guidelines/toolbars` | Toolbar placement, item types, and customization. |

For components not listed here, fetch the components overview page for the full index.

## Inputs

Input methods and hardware interaction. Consult when designing for specific input modalities.

| Input | URL | Consult when |
|-------|-----|-------------|
| Inputs overview | `https://developer.apple.com/design/human-interface-guidelines/inputs` | Understanding the input taxonomy for each platform. |
| Apple Pencil and Scribble | `https://developer.apple.com/design/human-interface-guidelines/apple-pencil-and-scribble` | iPadOS apps with drawing, markup, or handwriting input. |
| Game controllers | `https://developer.apple.com/design/human-interface-guidelines/game-controllers` | Supporting physical game controllers. |
| Keyboards | `https://developer.apple.com/design/human-interface-guidelines/keyboards` | Keyboard input, shortcuts, and custom keyboard layouts. |




For inputs not listed here, fetch the inputs overview page for the full index.
