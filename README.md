# Copilot CLI Skills

A collection of custom skills for [GitHub Copilot CLI](https://docs.github.com/en/copilot/github-copilot-in-the-cli). Each skill teaches Copilot CLI domain-specific expertise so it can provide better, more targeted assistance.

## Repository Structure

```
copilot-cli-skills/
├── README.md
├── code-review-csharp/
│   └── SKILL.md
└── <your-skill>/
    └── SKILL.md
```

Each skill lives in its own directory and is defined by a single `SKILL.md` file.

## Creating a New Skill

### 1. Create a skill directory

Create a new directory at the repository root. Use a descriptive, kebab-case name:

```bash
mkdir <skill-name>
```

For example: `code-review-python`, `generate-unit-tests`, `explain-architecture`.

### 2. Add a `SKILL.md` file

Create a `SKILL.md` file inside your skill directory:

```bash
touch <skill-name>/SKILL.md
```

### 3. Add YAML frontmatter

Every `SKILL.md` must start with YAML frontmatter containing `name` and `description` fields:

```markdown
---
name: <skill-name>
description: >
  A clear description of what the skill does and when it should be used.
  This helps Copilot CLI decide when to apply the skill.
---
```

- **`name`** — A unique identifier for the skill (should match the directory name).
- **`description`** — Explains the skill's purpose and trigger conditions. Be specific about *when* the skill should activate (e.g., "Use this skill when asked to review Python code").

### 4. Write the skill instructions

Below the frontmatter, write the instructions in Markdown. This is the core of the skill — it tells Copilot CLI *how* to behave when the skill is active. A good skill typically includes:

- **Context / overview** — What the skill does at a high level.
- **Step-by-step process** — A numbered workflow Copilot CLI should follow.
- **Rules and guidelines** — Categorized best practices, patterns, or checks.
- **Output format** — How findings or results should be presented (e.g., severity icons, code snippets, grouped by file).

### 5. Commit and push

```bash
git add <skill-name>/SKILL.md
git commit -m "Add <skill-name> skill"
git push
```

## Tips for Writing Effective Skills

- **Be specific** — Vague instructions produce vague results. Include concrete examples, rule IDs, and code snippets where possible.
- **Define severity levels** — If the skill involves review or analysis, categorize findings (e.g., 🔴 Critical, 🟠 Warning, 🟡 Suggestion).
- **Include before/after examples** — Show what bad code looks like and how to fix it.
- **Scope appropriately** — A skill should focus on a single domain or task. Prefer multiple focused skills over one monolithic skill.
- **Keep it actionable** — Every guideline should tell Copilot CLI what to *do*, not just what to *know*.

## Existing Skills

| Skill | Description |
|-------|-------------|
| [code-review-csharp](code-review-csharp/SKILL.md) | Reviews C# code for .NET best practices, performance, security, and maintainability. |
