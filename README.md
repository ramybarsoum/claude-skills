# Claude Skills

97 shareable skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Built and maintained by the [AllCare](https://allcare.ai) team.

We use Claude Code across product, engineering, and design. These skills capture workflows we've battle-tested internally, plus community skills we've found useful.

## Install

One command. Installs all skills, configures hooks, done.

```bash
curl -fsSL https://raw.githubusercontent.com/AllCare-ai/claude-skills/main/install.sh | bash
```

Install a single skill:

```bash
curl -fsSL https://raw.githubusercontent.com/AllCare-ai/claude-skills/main/install.sh | bash -s -- feature-spec-interview
```

Other commands:

```bash
# Check for updates
curl -fsSL https://raw.githubusercontent.com/AllCare-ai/claude-skills/main/install.sh | bash -s -- --check

# Uninstall
curl -fsSL https://raw.githubusercontent.com/AllCare-ai/claude-skills/main/install.sh | bash -s -- --uninstall
```

Restart Claude Code after installing. Type `/find-skills` to browse everything.

---

## Skills by Category

### Specification & Planning

Skills for writing specs, planning implementation, and structuring creative work before touching code.

| Skill | Invoke | Description |
|-------|--------|-------------|
| feature-spec-interview | `/feature-spec-interview` | Interactive interview that produces AI-agent-executable feature specs (NLSpec). 5 modes: All, PM-first, Eng-first, Fill-gaps, Quick. 40 question groups across behavioral, production bridge, and MECE gap coverage. |
| brainstorming | `/brainstorming` | Structured creative exploration. Use before any creative work: features, components, functionality, or behavior changes. |
| writing-plans | `/writing-plans` | Create implementation plans from specs or requirements before touching code. |
| executing-plans | `/executing-plans` | Execute written implementation plans with review checkpoints. |
| doc-coauthoring | `/doc-coauthoring` | Structured workflow for co-authoring documentation, proposals, technical specs, and decision docs. |

### Code Quality (Python)

Clean Code principles applied to Python. Each skill enforces a specific dimension of Robert Martin's catalog.

| Skill | Invoke | Description |
|-------|--------|-------------|
| python-clean-code | `/python-clean-code` | Full Clean Code catalog for Python: naming, functions, comments, DRY, boundaries. |
| boy-scout | `/boy-scout` | Boy Scout Rule: always leave code cleaner than you found it. Orchestrates other clean code skills. |
| clean-names | `/clean-names` | Descriptive names, appropriate length, no encodings. |
| clean-functions | `/clean-functions` | Max 3 arguments, single responsibility, no flag parameters. |
| clean-comments | `/clean-comments` | No metadata, no redundancy, no commented-out code. |
| clean-tests | `/clean-tests` | Fast tests, boundary coverage, one assert per test. |
| clean-general | `/clean-general` | DRY, single responsibility, clear intent, no magic numbers. |
| solid | `/solid` | SOLID principles, TDD, clean code practices for production-grade software. |

### Code Review

Skills for reviewing code, giving feedback, and verifying work before shipping.

| Skill | Invoke | Description |
|-------|--------|-------------|
| code-review | `/code-review` | Structured code review for PRs and implementation phases. |
| pr-review-toolkit | `/pr-review-toolkit` | Multi-agent PR review: code review, silent failure hunting, type design analysis, test coverage. |
| frontend-code-review | `/frontend-code-review` | Review frontend files (.tsx, .ts, .js) against checklist rules. |
| requesting-code-review | `/requesting-code-review` | Prepare work for review: verify requirements met before submitting. |
| receiving-code-review | `/receiving-code-review` | Process review feedback with technical rigor, not blind implementation. |
| verification-before-completion | `/verification-before-completion` | Run verification commands and confirm output before claiming work is done. |
| code-simplifier | `/code-simplifier` | Simplify and refine code for clarity and maintainability. Preserves all functionality. |
| ralph-wiggum | `/ralph-wiggum` | Devil's advocate reviewer. Finds logical gaps, questionable assumptions, and missing data, all with humor. |

### Frontend & UI Design

Skills for building interfaces, from design systems to production components.

| Skill | Invoke | Description |
|-------|--------|-------------|
| frontend-design | `/frontend-design` | Production-grade frontend interfaces with high design quality. Avoids generic AI aesthetics. |
| frontend-ui-ux-engineer-skill | `/frontend-ui-ux-engineer-skill` | Designer-turned-developer approach. Stunning UI/UX without design mockups. |
| ui-ux-pro-max | `/ui-ux-pro-max` | 50 styles, 21 palettes, 50 font pairings, 20 charts, 9 stacks. Comprehensive UI/UX intelligence. |
| ui-ux-designer | `/ui-ux-designer` | UI/UX design expert: interface design, interaction design, user experience, design systems. |
| web-design-guidelines | `/web-design-guidelines` | Review UI code for Web Interface Guidelines compliance and accessibility. |
| web-artifacts-builder | `/web-artifacts-builder` | Multi-component HTML artifacts using React, Tailwind, shadcn/ui. |
| interface-craft | `/interface-craft` | Polished animated interfaces in React. Storyboard Animation DSL, DialKit, Design Critique. |
| canvas-design | `/canvas-design` | Visual art in .png and .pdf using design philosophy. Posters, art, static designs. |
| theme-factory | `/theme-factory` | 10 pre-set themes for slides, docs, reports, landing pages. Or generate a theme on-the-fly. |
| vercel-react-best-practices | `/vercel-react-best-practices` | React and Next.js performance optimization guidelines from Vercel Engineering. |
| motion | `/motion` | Motion Vue (motion-v) animations: gesture, scroll-linked effects, layout transitions for Vue 3/Nuxt. |

### Swift & iOS

Skills for building native Apple platform apps.

| Skill | Invoke | Description |
|-------|--------|-------------|
| swift-expert | `/swift-expert` | iOS/macOS/watchOS/tvOS apps. SwiftUI, async/await, actors, protocol-oriented design. |
| ios-swift-development | `/ios-swift-development` | MVVM architecture, SwiftUI, URLSession, Combine, Core Data. |
| swift-style | `/swift-style` | Swift code style conventions for consistent formatting, naming, organization. |
| swift-testing-expert | `/swift-testing-expert` | Swift Testing: #expect/#require macros, traits, parameterized tests, async patterns. |
| swift-protocol-di-testing | `/swift-protocol-di-testing` | Protocol-based dependency injection for testable Swift code. Mock file system, network, APIs. |
| swift-mcp-server-generator | `/swift-mcp-server-generator` | Generate MCP server projects in Swift using the official MCP Swift SDK. |
| swiftui-expert-skill | `/swiftui-expert-skill` | SwiftUI best practices: state management, view composition, performance, iOS 26+ Liquid Glass. |
| swiftui-animation | `/swiftui-animation` | Advanced SwiftUI animations, transitions, matched geometry, Metal shader integration. |
| swiftui-liquid-glass | `/swiftui-liquid-glass` | iOS 26+ Liquid Glass API implementation and review. |
| swiftui-performance-audit | `/swiftui-performance-audit` | Diagnose slow rendering, janky scrolling, excessive view updates in SwiftUI. |
| swiftui-pro | `/swiftui-pro` | Comprehensive SwiftUI code review for modern APIs, maintainability, performance. |
| swiftui-ui-patterns | `/swiftui-ui-patterns` | Example-driven guidance for SwiftUI views, components, TabView architecture. |
| rivetkit-client-swiftui | `/rivetkit-client-swiftui` | RivetKit SwiftUI client: connect to Rivet Actors with @Actor and view modifiers. |

### Mobile (Cross-Platform)

| Skill | Invoke | Description |
|-------|--------|-------------|
| building-native-ui | `/building-native-ui` | Complete guide for Expo Router: styling, components, navigation, animations, native tabs. |

### Testing & Debugging

| Skill | Invoke | Description |
|-------|--------|-------------|
| test-driven-development | `/test-driven-development` | TDD workflow. Use before writing implementation code. |
| systematic-debugging | `/systematic-debugging` | Scientific debugging: hypotheses, systematic investigation, root cause documentation. |
| webapp-testing | `/webapp-testing` | Test local web apps with Playwright. Frontend verification, screenshots, browser logs. |

### DevOps & Git

| Skill | Invoke | Description |
|-------|--------|-------------|
| commit-commands | `/commit-commands` | Git commit, push, and PR creation workflows. |
| using-git-worktrees | `/using-git-worktrees` | Isolated git worktrees for feature work. Smart directory selection, safety verification. |
| finishing-a-development-branch | `/finishing-a-development-branch` | Guide completion: merge, PR, or cleanup when implementation is done and tests pass. |
| safe-file-deletion | `/safe-file-deletion` | Enforces explicit permission before any file deletion (rm, unlink, fs.rm). |

### AI & Agent Development

Skills for building AI features, MCP servers, and multi-agent systems.

| Skill | Invoke | Description |
|-------|--------|-------------|
| agent-sdk-dev | `/agent-sdk-dev` | Claude Agent SDK application development. |
| mcp-builder | `/mcp-builder` | Build MCP servers for LLM-external service integration. Python (FastMCP) and Node/TypeScript. |
| context7 | `/context7` | Retrieve up-to-date docs for any library via Context7 API. |
| explain-code | `/explain-code` | Explain code with visual diagrams and analogies. |
| feature-dev | `/feature-dev` | Feature development workflow with agents. |
| orchestrator | `/orchestrator` | Multi-agent orchestration. |
| dispatching-parallel-agents | `/dispatching-parallel-agents` | Run 2+ independent tasks in parallel without shared state. |
| subagent-driven-development | `/subagent-driven-development` | Execute implementation plans with independent tasks using subagents. |
| get-shit-done | `/gsd:help` | GSD meta-prompting system: planning, executing, and verifying with context-rot prevention. |

### Marketing & Growth

Skills for writing copy, running campaigns, and optimizing conversion.

| Skill | Invoke | Description |
|-------|--------|-------------|
| copywriting | `/copywriting` | Marketing copy for homepage, landing pages, pricing, feature pages, product pages. |
| copy-editing | `/copy-editing` | Systematic editing of marketing copy through multiple focused passes. |
| social-content | `/social-content` | Create and optimize social media content for LinkedIn, Twitter/X, Instagram, TikTok. |
| email-sequence | `/email-sequence` | Drip campaigns, nurture sequences, onboarding emails, lifecycle email programs. |
| marketing-psychology | `/marketing-psychology` | 70+ mental models organized for marketing: cognitive biases, persuasion, decision-making. |
| launch-strategy | `/launch-strategy` | Product launch planning: phased launches, channel strategy, launch momentum. |
| signup-flow-cro | `/signup-flow-cro` | Optimize signup, registration, and trial activation flows. |
| onboarding-cro | `/onboarding-cro` | Post-signup onboarding, user activation, first-run experience, time-to-value. |
| analytics-tracking | `/analytics-tracking` | Set up GA4, GTM, conversion tracking, event tracking, UTM parameters. |

### Communication & Docs

| Skill | Invoke | Description |
|-------|--------|-------------|
| internal-comms | `/internal-comms` | Status reports, leadership updates, newsletters, FAQs, incident reports, project updates. |
| slack-gif-creator | `/slack-gif-creator` | Create animated GIFs optimized for Slack with constraints and validation. |
| ask-user-question | `/ask-user-question` | Ask users questions via UI when you need clarification or confirmation. |

### Document & File Formats

Skills for creating and manipulating office documents programmatically.

| Skill | Invoke | Description |
|-------|--------|-------------|
| pdf | `/pdf` | Extract text/tables, create PDFs, merge/split, handle forms. |
| docx | `/docx` | Create, edit, analyze Word docs. Tracked changes, comments, formatting. |
| pptx | `/pptx` | Create, edit, analyze presentations. Layouts, speaker notes, content modification. |
| xlsx | `/xlsx` | Create, edit, analyze spreadsheets. Formulas, formatting, data analysis, visualization. |

### Database & Backend

| Skill | Invoke | Description |
|-------|--------|-------------|
| sql-optimization-patterns | `/sql-optimization-patterns` | Query optimization, indexing strategies, EXPLAIN analysis for database performance. |
| supabase-postgres-best-practices | `/supabase-postgres-best-practices` | Postgres performance optimization and best practices from Supabase. |
| better-auth-best-practices | `/better-auth-best-practices` | Better Auth integration: the comprehensive TypeScript authentication framework. |

### Browser Automation

| Skill | Invoke | Description |
|-------|--------|-------------|
| agent-browser | `/agent-browser` | Browser automation for web testing, form filling, screenshots, data extraction. |
| dev-browser | `/dev-browser` | Persistent browser state. Navigate, fill forms, screenshot, scrape, automate workflows. |

### Diagrams & Visualization

| Skill | Invoke | Description |
|-------|--------|-------------|
| mermaid-diagrams | `/mermaid-diagrams` | Software diagrams in Mermaid: class, sequence, flowchart, ERD, C4, state, gantt. |
| algorithmic-art | `/algorithmic-art` | Algorithmic art with p5.js: seeded randomness, flow fields, particle systems. |

### Video

| Skill | Invoke | Description |
|-------|--------|-------------|
| remotion-best-practices | `/remotion-best-practices` | Video creation in React with Remotion. |

### Brand

| Skill | Invoke | Description |
|-------|--------|-------------|
| brand-guidelines | `/brand-guidelines` | Anthropic's official brand colors and typography for artifacts. |

### Workflow & Meta

Skills that modify how Claude Code itself operates.

| Skill | Invoke | Description |
|-------|--------|-------------|
| using-superpowers | `/using-superpowers` | Skill discovery and invocation framework. Establishes how to find and use skills. |
| find-skills | `/find-skills` | Discover and install skills. "How do I do X?" |
| skill-creator | `/skill-creator` | Create new skills or update existing ones. |
| writing-skills | `/writing-skills` | Create, edit, and verify skills before deployment. |
| hookify | `/hookify` | Create hooks to prevent unwanted behaviors from conversation analysis. |
| learning-output-style | (auto) | Learning mode: interactive learning with educational explanations. |
| explanatory-output-style | (auto) | Explanatory mode: educational insights about the codebase as you work. |
| claude-opus-4-5-migration | (auto) | Migration utilities for Claude Opus 4.5. |
| security-guidance | (auto) | Security hooks and guidance. |
| file-permission | (auto) | File permission enforcement. |

---

## How Skills Work

Each skill is a folder under `skills/` with a `SKILL.md` file and optional `references/` directories. Claude Code reads the `SKILL.md` when the skill is invoked and loads reference files as needed.

Skills installed to `~/.claude/skills/` are globally available in every project.

Some skills are "plugins" with richer structure (commands/, agents/, hooks/ directories). These provide slash commands, sub-agents, and automated hooks in addition to the core skill behavior.

## Contributing

We welcome contributions from the AllCare team and the community.

**Adding a new skill:**

1. Create a folder under `skills/` with your skill name
2. Add a `SKILL.md` with YAML frontmatter (`name`, `description`, `category`) and the skill instructions
3. Optionally add a `references/` directory for supporting files
4. Update `install.sh` if the skill needs hooks or special setup
5. Open a PR with a clear description of what the skill does and when to use it

**Improving an existing skill:**

- Fix unclear instructions, add missing edge cases, improve question flows
- Test by copying the updated `SKILL.md` to `~/.claude/skills/<skill-name>/` and running it

**Guidelines:**

- Skills should be self-contained. No external dependencies beyond Claude Code.
- Write instructions as if the reader has never seen the skill before.
- Include concrete examples where possible.
- Keep the scope focused. One skill, one job.

## Team

Built by [AllCare](https://allcare.ai). Maintained by the product and engineering teams.

## License

MIT
