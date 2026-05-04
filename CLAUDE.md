# CLAUDE.md

## Project overview

This repo provides a shareable, opinionated code style configuration for Groovy/Spock test projects in IntelliJ IDEA. It is not a runnable application -- it is a set of configuration files that users copy into their own projects.

## Repository structure

```
.editorconfig                         # Primary formatting rules (EditorConfig + IntelliJ ij_* extensions)
.idea/codeStyles/codeStyleConfig.xml  # Enables project-level code style in IntelliJ
.idea/codeStyles/Project.xml          # Groovy-specific rules not expressible in EditorConfig
CLAUDE.md                             # Project context for Claude Code (this file)
.gitignore                            # macOS .DS_Store exclusion
LICENSE                               # MIT License
README.md                             # Usage instructions and documentation
```

## Key technical details

- **No build system, no dependencies, no tests.** This is a config-only repo.
- **Target IDE:** IntelliJ IDEA (any edition). Files use IntelliJ's `ij_*` EditorConfig extensions.
- **Target language:** Groovy (Spock test framework), with secondary coverage for Java, XML, JSON, YAML, properties, shell scripts, and Markdown.
- `.editorconfig` takes precedence over `Project.xml` when both define the same rule. `Project.xml` exists only for Groovy settings that EditorConfig cannot express.
- `codeStyleConfig.xml` contains a single flag (`USE_PER_PROJECT_SETTINGS = true`) that tells IntelliJ to use project settings instead of the user's personal IDE scheme.

## Core Groovy formatting rules

- Indent: tabs, size 2, continuation indent 4
- Max line length: 120
- Braces: always forced (if, for, while, do-while)
- Import on demand threshold: 100 (effectively disabled -- prefer explicit imports)
- Import layout: `*, |, javax.**, java.**, |, $*`
- Blank lines kept: max 1 in code and declarations

## Git conventions

- Follow a branch-per-change workflow: create a feature branch, commit, push, open a PR against `main`.
- Commit messages: short summary line describing the change.
- The `.idea/` directory is intentionally tracked (for codeStyles only). The README explains the `.idea/*` vs `.idea/` gitignore distinction that consumers of this config need to understand.

## What not to change

- Do not add files under `.idea/` other than `codeStyles/`. This repo intentionally tracks only the code style configuration.
- Do not remove `ij_*` properties from `.editorconfig` even if they look like IntelliJ defaults -- they ensure consistent behavior across different IntelliJ versions and configurations.
