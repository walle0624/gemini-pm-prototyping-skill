---
name: gemini-pm-prototyping
description: Use when acting as a product manager and using Gemini to produce desktop-first HTML prototypes from product requirements, then reviewing and delivering the result
---

# Gemini PM Prototyping

Use this skill when the user wants a product-manager workflow where Codex structures requirements, directs Gemini to build an HTML prototype, reviews the output, and returns a usable artifact.

## When to Use

- The user wants you to act as a product manager
- The prototype should be generated through Gemini rather than hand-built first
- The desired output is a runnable HTML interaction demo
- Speed matters more than custom implementation from scratch

Do not use this skill when the user wants final production code without Gemini in the loop.

## Default Workflow

1. Clarify the product goal with one question at a time until scope is tight enough.
2. Convert the result into a short PM brief:
   - target platform
   - core audience
   - goal of the experience
   - required modules
   - required interactions
   - explicit non-goals
3. Reuse a named Gemini conversation when possible instead of starting from scratch each time.
4. Establish browser access in a way that preserves the user's Gemini login state.
5. Before prompting Gemini, verify the target conversation is in:
   - `Pro` model
   - `Canvas` tool
6. Ask Gemini for:
   - a single-file HTML prototype
   - inline CSS and JS
   - real interaction logic, not static mock markup
   - no markdown fences
7. Wait for Gemini to finish and inspect whether the response lands as:
   - inline HTML text, or
   - a `代码 / 预览` artifact card
8. Extract the full code, save it locally, and review it as a PM:
   - are all requested modules present?
   - are the states complete?
   - are the rules and feedback clear?
   - is the experience aligned with the intended audience?
9. When the prototype has issues, first write a precise correction brief and ask Gemini to revise the prototype.
10. Only patch locally for very small finish-work items after Gemini has already produced the right overall direction.
11. Verify the artifact by opening it locally and testing the main flow.
12. Deliver the final file path and summarize what was improved.

## Gemini Setup Rules

When Gemini login state matters, you MUST prioritize a Chrome CDP session over a fresh Playwright browser.

Use:
- `chrome-cdp-session` to reuse the persistent Chrome session
- `playwright` only after CDP is established, or when Playwright is only helping with browser automation patterns on top of the logged-in browser state

Never default straight to an isolated Playwright browser for Gemini work if login state is relevant.

Preferred order:

1. Use `chrome-cdp-session`
2. Confirm Gemini opens with the user's existing login state
3. Use browser automation against that session
4. Only fall back if CDP is unavailable and the user accepts the consequence of losing login state

If the environment does not expose the `chrome-cdp-session` skill or equivalent helper:

- explicitly tell the user that preserving Gemini login state requires Chrome remote-debugging or an equivalent persistent-browser workflow
- guide them to set that up before continuing
- do not silently proceed with an isolated browser and pretend the logged-in state will be present

Example guidance:

```text
当前环境没有可直接复用登录态的 Chrome CDP 能力。为了继续稳定操作已登录的 Gemini，需要先建立一个可复用的 Chrome remote debugging 会话；否则直接打开的新浏览器大概率没有你的登录状态。我可以先引导你完成这一步，再继续原型流程。
```

Before sending the main request, confirm the active Gemini conversation shows:
- selected tool as `Canvas`
- selected model as `Pro`

If either is wrong, switch it before prompting.

Do not assume the previous turn's selection persisted. Re-check before every new major prompt.

## Gemini Mode Switching

Before each major prototype prompt, explicitly inspect and, if needed, switch:

1. open the target Gemini conversation
2. verify the tool chip shows `Canvas`
3. if not, open the tool chooser and switch to `Canvas`
4. verify the model chip shows `Pro`
5. if not, open the model chooser and switch to `Pro`
6. only then send the PM brief

If Gemini responds with text that indicates Canvas was not used, treat that as a configuration failure and correct the mode before retrying.

## Revision Policy

Default to revising through Gemini, not by replacing Gemini's work yourself.

Use Gemini revision prompts for:
- missing modules
- wrong interaction flow
- unclear state transitions
- weak layout hierarchy
- visual direction mismatch
- missing success, failure, empty, or validation states

Use local patching only for:
- tiny copy fixes
- very small binding bugs
- final polish that does not change the overall product direction

If the issue changes layout, structure, interaction flow, state design, or visual hierarchy, send it back to Gemini instead of fixing it yourself.

When sending a revision request to Gemini:

- state what is wrong
- state what must stay unchanged
- ask for a full updated single-file HTML result
- keep the feedback concrete and product-facing

Preferred revision framing:

```text
这版原型整体方向可以保留，但请基于下面问题做一轮修订，并重新输出完整单文件 HTML。

需要修正的问题：
1. [问题]
2. [问题]

必须保留的部分：
- [保留点]
- [保留点]

修订要求：
- 继续输出单文件 HTML
- 不要输出解释
- 不要加 Markdown 代码块围栏
```

## PM Brief Template

Use this structure when turning the user's request into a Gemini prompt:

```text
你现在是一名专业的原型设计师，请基于下面的产品需求，直接输出一个可运行的单文件 HTML 原型，用于桌面浏览器演示。

角色要求：
- 你擅长输出 HTML 类型的交互原型
- 这不是技术方案说明，而是可直接预览和试玩的交互稿
- 请直接输出完整 HTML，不要输出额外解释，不要加 Markdown 代码块围栏

产品背景：
[一句话产品描述]

产品目标：
- [目标 1]
- [目标 2]

范围要求：
- [平台]
- [必须有]
- [不做内容]

必须包含的模块：
1. [模块]
2. [模块]

交互要求：
- [关键交互]
- [状态要求]

视觉要求：
- [风格要求]
- [布局约束]

实现要求：
- 输出单文件 HTML
- 内含 CSS 和 JS
- 打开即可运行
- 不依赖外部资源

额外要求：
- 请把核心逻辑做成真实可交互逻辑，不要只做静态展示
- 请优先保证交互完整性和可演示性
```

Keep the prompt concrete. Gemini performs better when non-goals are explicit.

## Extraction Guidance

When Gemini responds with a code artifact instead of plain text:

- switch to the `代码` tab
- prefer extracting code from the editor model instead of visible DOM text
- if Monaco is present, read `window.monaco.editor.getModels()[0].getValue()`
- save the extracted HTML to a local file before review

Avoid copying only the visible viewport from the code editor. It will truncate the artifact.

## Review Checklist

Check these before calling the prototype done:

- requested modules exist
- empty states exist where needed
- invalid input has a user-facing response
- success and failure states are visually distinct
- restart or retry flow works
- labels and metrics match the game or product rules
- desktop layout feels intentional, not mobile-first stretched wider

Prefer Gemini revisions for meaningful changes. Use local patches only for tiny finish-work.

## Delivery Pattern

Your final response should usually include:

- where the final artifact lives
- whether it came directly from Gemini or was lightly patched after review
- what you verified
- any remaining gaps worth addressing later

## Common Failure Modes

- Gemini answers with explanation instead of raw HTML
  - reprompt with `请直接输出完整 HTML，不要输出额外解释，不要加 Markdown 代码块围栏`
- Gemini returns an artifact card and the DOM only shows a preview snippet
  - extract from Monaco, not from rendered page text
- Tool/model silently drift from `Canvas + Pro`
  - re-check before each new major prompt
- The agent launches a fresh browser and loses Gemini login state
  - stop and re-route through `chrome-cdp-session`, or explicitly guide the user to establish CDP support first
- Prototype looks complete but misses a required state
  - review against the PM brief, then ask Gemini for a targeted revision before considering local edits
