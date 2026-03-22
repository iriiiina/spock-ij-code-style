# GitHub Copilot Instructions — Spock IntelliJ Code Style

## Project overview

This repo provides a shareable, opinionated code style configuration for Groovy/Spock test projects in IntelliJ IDEA. It is not a runnable application — it is a set of configuration files that users copy into their own projects.

## Repository structure

- `.editorconfig` — Primary formatting rules (EditorConfig + IntelliJ `ij_*` extensions). Covers Groovy, Java, XML, JSON, YAML, properties, shell scripts, and Markdown.
- `.idea/codeStyles/codeStyleConfig.xml` — Enables project-level code style in IntelliJ (single flag: `USE_PER_PROJECT_SETTINGS = true`).
- `.idea/codeStyles/Project.xml` — Groovy-specific rules not expressible in EditorConfig (blank lines, brace placement, import layout).
- `.gitignore` — macOS `.DS_Store` exclusion.
- `LICENSE` — MIT License.
- `README.md` — Usage instructions and documentation.

## Key technical details

- No build system, no dependencies, no tests. Config-only repo.
- Target IDE: IntelliJ IDEA. Target language: Groovy (Spock framework).
- `.editorconfig` takes precedence over `Project.xml` when both define the same rule.

## Core Groovy formatting rules

- Indent: tabs, size 2, continuation indent 4
- Max line length: 120
- Braces: always forced (if, for, while, do-while)
- Import on demand threshold: 100 (effectively disabled)
- Import layout: `*, |, javax.**, java.**, |, $*`
- Blank lines kept: max 1 in code and declarations

## Constraints

- Do not add files under `.idea/` other than `codeStyles/`.
- Do not remove `ij_*` properties from `.editorconfig` even if they look like defaults — they ensure consistent behavior across IntelliJ versions.
- Follow branch-per-change workflow: feature branch, commit, push, PR against `main`.
