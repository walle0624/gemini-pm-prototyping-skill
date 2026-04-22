# Gemini PM Prototyping Skill

A Codex skill for product-manager-led prototyping with Gemini.

This skill is for workflows where Codex:

- clarifies requirements like a product manager
- prefers Gemini CLI over the Gemini web app
- defaults to `gemini-3.1-pro-preview` when available
- runs a smoke test before the main prototype prompt
- asks Gemini for a runnable single-file HTML prototype
- captures the result into a local `.html` artifact
- reviews the output and sends revision briefs back to Gemini when needed

## Contents

- [gemini-pm-prototyping/SKILL.md](gemini-pm-prototyping/SKILL.md)

## Notes

- The workflow prefers Gemini revisions over local rewrites for meaningful prototype changes.
- The preferred one-shot path is `gemini -m gemini-3.1-pro-preview --approval-mode yolo -p "<brief>" > artifact.html`.
- The validated smoke test is `gemini -m gemini-3.1-pro-preview -p "只回复 OK"`.
