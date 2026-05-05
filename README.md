# Codex Skills

Reusable Codex skills maintained as portable folders. Each skill lives in its own directory and contains a required `SKILL.md` file.

## Available Skills

- `second-brain`: use a Markdown second-brain repository as persistent, auditable memory for Codex, including search, MCP/CLI workflows, bootstrap, governance, sync, privacy, and encryption guidance.

## Install A Skill

Clone this repository:

```powershell
git clone https://github.com/marcelorusso/codex-skills.git
```

Resolve the Codex skills directory:

```powershell
$skillsRoot = if ($env:CODEX_HOME) { Join-Path $env:CODEX_HOME "skills" } else { Join-Path $HOME ".codex\skills" }
New-Item -ItemType Directory -Force -Path $skillsRoot | Out-Null
```

Install `second-brain`:

```powershell
Copy-Item -Recurse -Force ".\codex-skills\second-brain" $skillsRoot
```

The resulting path is commonly `~/.codex/skills/second-brain` when `CODEX_HOME` is unset.

## Repository Layout

```text
codex-skills/
  README.md
  second-brain/
    SKILL.md
```

## Maintenance

Keep each skill generic and portable:

- Avoid personal paths, account names, machine names, and private repository assumptions.
- Prefer environment variables, current workspace detection, MCP discovery, or explicit user-provided paths.
- Keep `SKILL.md` focused on procedural instructions that another Codex instance needs at runtime.
- Do not store secrets, credentials, private tokens, or user-specific memory in this repository.

After updating a skill, copy it into the local Codex skills directory and test it in a fresh Codex session when practical.
