# apple-design-swift

A Claude Skill that teaches AI agents to design and build Apple platform apps (iOS, iPadOS, macOS, watchOS, visionOS) using SwiftUI, following Apple's Human Interface Guidelines and idiomatic Swift conventions.

一个 Claude Skill，教会 AI 智能体如何使用 SwiftUI 设计和构建 Apple 平台应用（iOS、iPadOS、macOS、watchOS、visionOS），遵循 Apple 人机界面指南和地道的 Swift 实践。

---

## What it does / 它能做什么

- Navigates Apple's Human Interface Guidelines — fetches the right HIG page for the task at hand, so the agent always designs against the current spec rather than from memory.
- Enforces idiomatic SwiftUI patterns — view composition, state management (`@Observable`, `@State`, `@Bindable`, `@Environment`), modifier ordering, navigation APIs, and list performance.
- Provides project structure conventions — feature-based folder layout, DesignSystem centralization, and when to extract Swift Packages.
- Sets Swift code style rules — naming, access control, error handling, and file organization.

---

- 引导 Apple 人机界面指南 —— 根据当前任务抓取正确的 HIG 页面，让智能体始终基于最新规范进行设计，而非凭记忆。
- 强制执行地道的 SwiftUI 模式 —— 视图组合、状态管理（`@Observable`、`@State`、`@Bindable`、`@Environment`）、修饰符顺序、导航 API 和列表性能。
- 提供项目结构规范 —— 按功能组织的目录布局、DesignSystem 集中化、何时抽取 Swift Package。
- 设定 Swift 代码风格规则 —— 命名、访问控制、错误处理和文件组织。

## Skill structure / Skill 结构

```
apple-design-swift/
├── SKILL.md                         # Entry point: workflow, anti-patterns, reference index
│                                    # 入口：工作流、常见错误、参考索引
└── references/
    ├── hig-navigation.md            # HIG URL map: platforms, foundations, patterns, components, inputs
    │                                # HIG URL 地图：平台、基础、模式、组件、输入
    ├── swiftui-patterns.md          # View composition, state, modifiers, navigation, lists
    │                                # 视图组合、状态、修饰符、导航、列表
    ├── project-structure.md         # File/folder layout, feature-based organization
    │                                # 文件/目录布局、按功能组织
    └── code-style.md                # Naming, access control, optionals, error handling
                                     # 命名、访问控制、可选值、错误处理
```

## Trigger phrases / 触发短语

The skill activates when the user mentions any of these (English or Chinese):

该 skill 在用户提及以下任意内容时激活（英文或中文均可）：

| English | 中文 |
|---------|------|
| Design an iOS app | 设计一个 iOS 应用 |
| Build a Mac app | 构建一个 Mac 应用 |
| Make a SwiftUI view | 写一个 SwiftUI 视图 |
| Apple Watch app | Apple Watch 应用 |
| visionOS experience | visionOS 体验 |
| Help me make an iPhone app | 帮我做一个 iPhone 应用 |
| Swift code for Apple platforms | Apple 平台的 Swift 代码 |

## Design philosophy / 设计理念

1. **HIG is the authority.** This skill never caches Apple's design rules — it tells the agent *which page to fetch* so the guidance never goes stale.
2. **SwiftUI conventions are documented.** Stable coding patterns are written into the skill directly as reference material.
3. **Progressive disclosure.** Metadata triggers the skill; SKILL.md provides the workflow; `references/` is loaded on demand.

---

1. **HIG 是权威来源。** 本 Skill 从不缓存 Apple 的设计规则 —— 它告诉智能体*去哪个页面抓取*，确保指导永不过时。
2. **SwiftUI 惯例是写死的。** 稳定的编码模式直接写入 Skill 作为参考材料。
3. **渐进式披露。** Metadata 触发 Skill；SKILL.md 提供工作流；`references/` 按需加载。

## Requirements / 依赖

- A Claude Code environment that supports Claude Skills (custom or built-in skill directory).
- The agent must be able to `web_fetch` Apple Developer URLs (`developer.apple.com`).

---

- 支持 Claude Skill 的 Claude Code 环境（自定义或内置 skill 目录）。
- 智能体需要能够 `web_fetch` Apple Developer URL（`developer.apple.com`）。

## Installation / 安装方式

1. Copy the `apple-design-swift/` folder into your skills directory (e.g., `~/.claude/skills/`).
2. Restart Claude Code or reload skills.
3. Start a conversation with any of the trigger phrases above.

---

1. 将 `apple-design-swift/` 文件夹复制到你的 skills 目录（如 `~/.claude/skills/`）。
2. 重启 Claude Code 或重新加载 skills。
3. 使用上述任意触发短语开始对话。

## License / 许可证

MIT
