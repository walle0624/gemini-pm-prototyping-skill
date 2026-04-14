# Gemini PM Prototyping Skill

A Codex skill for product-manager-led prototyping with Gemini.

This skill is for workflows where Codex:

- clarifies requirements like a product manager
- reuses a logged-in Gemini session through Chrome CDP
- enforces `Pro + Canvas` before prompting
- asks Gemini for a runnable single-file HTML prototype
- extracts the generated code artifact
- reviews the output and sends revision briefs back to Gemini when needed

## Contents

- [gemini-pm-prototyping/SKILL.md](gemini-pm-prototyping/SKILL.md)

## Notes

- The workflow prefers Gemini revisions over local rewrites for meaningful prototype changes.
- It explicitly guards against losing login state by requiring a Chrome CDP session when Gemini access depends on existing auth.
