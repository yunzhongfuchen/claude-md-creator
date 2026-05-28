---
name: claude-md-creator
description: >
  Generate or update a project's AI assistant guide file (CLAUDE.md, GEMINI.md, AGENTS.md, .cursorrules, etc.).
  ALWAYS use this skill when the user wants to create, write, generate, update, or modify any project guide file, coding standards, or AI assistant instructions.
  Trigger on phrases like: "帮我写个 claude.md", "生成项目规范", "初始化规则", "coding guidelines", "project handbook", "AI assistant setup", "cursor rules".
  Also trigger when the user wants to document project conventions, set up developer workflows, or tell an AI how to work with the codebase — even if they don't explicitly name the file.
---

# Project Guide Creator

Generate a project-specific guide file that tells AI assistants how to work with the codebase.

## Supported file names

- `CLAUDE.md` — default for Claude Code / Claude.ai
- `GEMINI.md` — for Gemini CLI / Gemini in IDE
- `AGENTS.md` — generic multi-agent guide
- `.cursorrules` — for Cursor IDE

## When to use

- User explicitly asks for `CLAUDE.md`, `GEMINI.md`, `.cursorrules`, or similar.
- User wants to "document project conventions", "set up coding guidelines", or "create a project handbook".
- User says things like "帮我写个 claude.md", "生成项目规范", "初始化规则", or "初始化项目文档".

## Workflow

### 1. Determine the target file

Check what AI assistant the user is interacting with:
- If the user is in **Claude Code** or **Claude.ai** → default to `CLAUDE.md`
- If the user mentions **Gemini** or **Google AI** → use `GEMINI.md`
- If the user mentions **Cursor** → use `.cursorrules`
- If the user mentions **generic agents** or **multiple AI tools** → use `AGENTS.md`
- If the user explicitly names a file → use that name

When uncertain, default to `CLAUDE.md`.

### 2. Analyze the project

Read the project structure to understand:
- Programming language(s) and framework(s)
- Build system and package manager (npm, pip, cargo, etc.)
- Testing setup
- Existing configuration files (tsconfig, eslint, pyproject.toml, etc.)
- Directory conventions (src/, lib/, tests/, docs/, etc.)
- Version control practices (main branch name, commit style if visible)

Use `Glob` and `Read` tools to explore. Focus on top-level files and 2-3 representative source files. Don't read everything.

### 3. Interview the user (brief)

Ask up to 2 concise questions. Skip if the project is small and conventions are obvious.

**Required question — only if environment is not documented:**
> "What runtime environment should be used? (e.g., conda env name, Node version, Docker, Python venv). Skip if there's already a `.python-version`, `Dockerfile`, or `package.json` with engines."

**Optional question (ask only if unclear):**
> "Any workflow preferences not visible in configs? (TDD, branch naming, PR requirements)"

**Do not ask about coding style** — derive it from existing configs or use the template defaults.

### 4. Generate the document

1. **Read** `references/project-guide-template.md`. This file contains the complete document skeleton: the `# Project Guidelines` title, all sections, and the full 5-section Coding Guidelines body.

2. **Fill in** the dynamic sections based on your analysis:
   - `Project Overview`: Language, build tool, test command
   - `Directory Conventions`: Map actual directories and their purposes

3. **Conditional sections** — handle based on user responses:
   - `Environment`: **Keep only if** the user provided environment info. Fill in the runtime and execution rule they specified. **Delete the entire section** if they had no specific environment.
   - `Workflow Preferences`: **Keep only if** the user specified workflow preferences. **Delete the entire section** if they had none.

4. **Write** the final content to the target file in the project root.

### 5. Present and confirm

Show the generated file to the user. Ask:
- "Anything you'd like to add or change?"
- If an existing guide file would be overwritten, show a diff and ask for confirmation.

## Key rules

- **Start from the full template.** Always read `references/project-guide-template.md` first. It already contains the `# Project Guidelines` title, section structure, and all 5 coding guideline sections. Do not rewrite or summarize the Coding Guidelines content.
- **Delete unused sections.** If the user did not provide environment or workflow info, remove those entire sections from the output. Do not leave empty placeholders.
- **Be concise on dynamic sections.** Keep `Project Overview` and `Directory Conventions` brief. A good guide file is under 200 lines.
- **Don't invent rules.** For `Directory Conventions` and `Workflow Preferences`, derive from what you observed or what the user told you.
- **Preserve existing content.** If a guide file already exists, merge rather than replace unless the user says otherwise. Keep sections the user added manually.
