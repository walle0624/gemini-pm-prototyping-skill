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
3. Prefer Gemini CLI over the Gemini web app.
4. Before the main prompt, confirm Gemini CLI is usable in the current environment:
   - `gemini --version` works
   - the user is already authenticated, or can authenticate locally
   - `/model` or `-m` can target the desired model
5. Run a smoke test before the main prototype prompt.
6. Ask Gemini for:
   - a single-file HTML prototype
   - inline CSS and JS
   - real interaction logic, not static mock markup
   - no markdown fences
7. Save the returned HTML locally and review it as a PM:
   - are all requested modules present?
   - are the states complete?
   - are the rules and feedback clear?
   - is the experience aligned with the intended audience?
8. When the prototype has issues, first write a precise correction brief and ask Gemini to revise the prototype.
9. Only patch locally for very small finish-work items after Gemini has already produced the right overall direction.
10. Verify the artifact by opening it locally and testing the main flow.
11. Deliver the final file path and summarize what was improved.

## Gemini CLI Rules

Default to Gemini CLI for prototype generation and revision.

Preferred order:

1. Use interactive Gemini CLI when possible.
2. Use `gemini -p` for short or bounded prompts that are known to return cleanly in the current environment.
3. Fall back to the Gemini web app only when the user explicitly wants it, or when the local terminal environment cannot reliably capture long CLI output.

Do not require `Canvas`. For this workflow, `Canvas` is optional and not a prerequisite.

If the user already authenticated in Gemini CLI, do not re-route them through a browser-only workflow just to preserve login state.

## Model Selection

Before each major prototype prompt, confirm the model path:

1. Prefer `gemini-3.1-pro-preview` when the account has access to it.
2. Otherwise fall back to `Auto (Gemini 3)`.
3. Use `/model` in interactive sessions when you want a persistent model choice.
4. Use `-m gemini-3.1-pro-preview`, `-m auto`, `-m pro`, `-m flash`, or another concrete model name when running a one-shot CLI command.

Do not assume a previous model selection persisted unless you verified it in the current CLI session.

## Execution Pattern

Use one of these patterns:

### Interactive Gemini CLI

- Start `gemini`
- confirm authentication and model
- send the PM brief
- capture the returned HTML from terminal output
- save to a local file

### Headless Gemini CLI

- Start with `gemini -p` for compact prompts
- prefer `--approval-mode yolo` for one-shot prototype generation if the environment tends to stall on hidden approval prompts
- write output to a local file when large HTML responses may be truncated in the terminal

If headless mode hangs or returns unusable output in the current terminal harness, stop treating that as the primary path and switch back to interactive CLI or a user-visible local terminal.

Validated pattern:

- `gemini -m gemini-3.1-pro-preview -p "只回复 OK"` is a good authentication and model smoke test
- `gemini -m gemini-3.1-pro-preview --approval-mode yolo -p "<prototype brief>" > artifact.html` is a valid one-shot generation path when the local machine already has a working Gemini CLI login

Recommended run order:

1. Run the smoke test and confirm it returns a short literal response such as `OK`
2. Run the prototype generation command and redirect into a local `.html` file
3. Inspect the saved file before claiming success

Minimum acceptance checks for the saved artifact:

- starts with `<!DOCTYPE html>` or `<html`
- contains inline `<style>` and `<script>` when the prompt asked for single-file interactivity
- does not include Markdown fences or explanatory prose before the HTML

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

## Output Handling

Prefer capturing Gemini CLI output straight into a local file before review.

- For short responses, direct terminal capture is enough.
- For larger HTML outputs, redirect stdout to a file or paste the response into a local file immediately.
- If the terminal environment garbles or truncates long output, switch execution mode before continuing instead of pretending the artifact is complete.

When using the Gemini web app as a fallback, use the page's actual code view or artifact source of truth instead of copying only the visible preview.

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
- Gemini CLI long output hangs in the current terminal harness
  - switch from headless mode to interactive CLI, or use a user-visible local terminal instead of insisting on the same execution path
- Model choice silently differs from the intended one
  - re-check `/model` or the `-m` flag before the next major prompt
- Prototype looks complete but misses a required state
  - review against the PM brief, then ask Gemini for a targeted revision before considering local edits
