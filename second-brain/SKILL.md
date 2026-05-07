---
name: second-brain
description: Use a local Markdown second-brain repository for persistent, auditable memory. Use when Codex needs prior context, user preferences, project history, decisions, research notes, references, meeting/session summaries, task briefs, governance state, bootstrap status, encryption/sync status, or durable knowledge outside the current conversation; also use when asked to add, update, search, organize, audit, bootstrap, synchronize, encrypt, decrypt, capture, synthesize, or summarize second-brain memory from a repository derived from second-brain-template.
---

# Second Brain

## Core Rule

Use a second-brain repository as an external, file-first memory layer. Do not assume a personal path, account name, operating system, or clone name.

Resolve the active brain root in this order:

1. The current workspace, when it contains `tools/memory.ps1` and the second-brain layout.
2. `SECOND_BRAIN_HOME`, when set and valid.
3. The registered MCP server named `second-brain`, when available.
4. An explicit path supplied by the user.
5. A likely local clone such as `$HOME/second-brain` or `$HOME\second-brain`, only after checking that it exists.

Prefer MCP tools when available and healthy. Use the bundled CLI when MCP is unavailable, stale, missing a tool, or when direct validation is clearer. Use direct Markdown reads for inspection. Prefer MCP/CLI for writes because they preserve metadata conventions and rebuild the SQLite index.

`delete` is recoverable: it moves entries to `.trash/`.

## Template vs Daily-Use

Before substantive work, determine the repository mode from Git remotes and local instructions:

- Template repository: the remote repository name ends with `-template`. Do not treat startup sync as an operational requirement. The template stores workflows, hooks, bootstrap logic, encryption artifacts, docs, and rules for derived repositories.
- Daily-use repository: the remote repository name does not end with `-template`. Run startup sync before substantive work unless the user explicitly says not to.

```powershell
<brain-root>\tools\memory.ps1 start-sync
```

`start-sync` encrypts protected local plaintext changes, commits when needed, pulls with rebase, pushes, then decrypts protected entries locally for trusted editing. If setup requires package installation, secrets, key generation, Git config, or user-profile writes, ask for authorization before changing the environment.

Repository `AGENTS.md` instructions may restate or refine this rule; follow them for that repository.

## Natural Startup Command

When the user asks `iniciar segundo cérebro`, `inicie o segundo cérebro`, `start second brain`, or an equivalent direct startup request:

1. Resolve the active brain root using the core rule above.
2. Determine whether the repository is a template or daily-use from Git remotes and local instructions.
3. In daily-use repositories, run:

```powershell
<brain-root>\tools\memory.ps1 start-sync
```

4. Then run:

```powershell
<brain-root>\tools\memory.ps1 github-readiness
<brain-root>\tools\memory.ps1 doctor
```

5. In template repositories, do not run operational `start-sync`; run `github-readiness` and `doctor` only.
6. Summarize blockers and next authorized actions. Ask before installing packages, generating or storing keys, changing Git config, writing user-profile files, or saving secrets.

## MCP Surface

Available MCP tools may vary by installed version. Common tools include:

- `memory_stats`, `memory_config`, `memory_list`, `memory_recent`
- `memory_search`, `memory_semantic_search`, `memory_hybrid_search`, `memory_read`
- `memory_add`, `memory_update`, `memory_capture_session`
- `memory_tags`, `memory_move`, `memory_delete`, `memory_link`
- `memory_reindex`, `memory_embed`
- `memory_doctor`, `memory_review`, `memory_synthesize`, `memory_task_brief`
- `memory_sync_check`, `memory_encrypt`, `memory_decrypt`, `memory_encrypt_protected`
- `memory_bootstrap`, `memory_start_sync`, `memory_device_key_status`, `memory_github_readiness`, when exposed by the server

Encrypted bodies are redacted by default. Request decrypted content only when necessary for the user request and appropriate for the storage/security context.

If MCP returns transport errors, fall back to CLI/direct reads. A previously loaded MCP host can remain stale until Codex is restarted even when the standalone server is fixed.

## MCP Registration And Validation

The MCP server is normally registered as `second-brain` and launched with:

```powershell
python <brain-root>\tools\second_brain_mcp.py
```

To validate whether Codex has it registered, run:

```powershell
codex mcp list
```

If no server is configured, register it:

```powershell
codex mcp add second-brain -- python "<brain-root>\tools\second_brain_mcp.py"
```

Codex may need to be restarted before a newly registered MCP server appears in the native tool list. If the current session reports `unknown MCP server 'second-brain'`, validate the standalone server or CLI, then tell the user to restart Codex before marking native MCP exposure complete.

## CLI Pattern

Use the repository's script through the resolved root. On non-Windows systems, use `pwsh` when invoking the PowerShell script.

```powershell
<brain-root>\tools\memory.ps1 help
<brain-root>\tools\memory.ps1 sync-check
<brain-root>\tools\memory.ps1 doctor
<brain-root>\tools\memory.ps1 review
<brain-root>\tools\memory.ps1 search "<query>"
<brain-root>\tools\memory.ps1 index-search "<query>"
<brain-root>\tools\memory.ps1 hybrid-search "<query>"
<brain-root>\tools\memory.ps1 semantic-search "<query>"
<brain-root>\tools\memory.ps1 read <id-or-prefix>
<brain-root>\tools\memory.ps1 list
<brain-root>\tools\memory.ps1 recent
<brain-root>\tools\memory.ps1 tags
<brain-root>\tools\memory.ps1 add -Type note -Title "<title>" -Tags tag1,tag2 -Content "<summary>"
<brain-root>\tools\memory.ps1 capture-session -Title "<title>" -Content "<summary>" -Completed "<item>" -NextSteps "<item>"
<brain-root>\tools\memory.ps1 update <id-or-prefix> -Content "<summary>"
<brain-root>\tools\memory.ps1 move <id-or-prefix> -Type decision
<brain-root>\tools\memory.ps1 task-brief -Title "<title>" -Content "<summary>" -Owner codex -ExpectedValidation "<command>"
<brain-root>\tools\memory.ps1 synthesize "<filter>" -Mode project-state
<brain-root>\tools\memory.ps1 reindex
<brain-root>\tools\memory.ps1 embed
```

Mutating CLI/MCP operations auto-rebuild `.index/memory.db`. Embeddings are explicit; run `embed` only when semantic retrieval should be refreshed and the privacy policy allows it.

## Retrieval Workflow

1. Search before relying on memory for decisions, preferences, project state, personal context, or research context.
2. Start with 1-3 targeted queries using concrete nouns, project names, acronyms, dates, and likely tags.
3. Use `index-search` for FTS-ranked search over the SQLite index.
4. Use `hybrid-search` when wording may differ from note text.
5. Use `semantic-search` when embeddings exist and the query is conceptual.
6. Read the strongest matching entries with `read` or `memory_read`.
7. Treat entries as evidence, not automatic truth. Prefer high-confidence, recent, source-aware notes.
8. Cite note ids or titles when useful for auditability.

## Writing Workflow

### Autonomous Memory Writes

Codex may write to the resolved second-brain without asking when the content is durable, useful beyond the current conversation, and non-secret.
Treat this as bounded autonomy: write useful memory proactively, but ask before storing secrets, sensitive personal information, large imported content, external embeddings over private or sensitive notes, or direct edits to `people/` and `projects/` entries unless the user clearly requested them.

Write durable memory for:

- decisions and rationale
- stable preferences
- project conventions, architecture, and current state
- session summaries and handoffs
- reusable references or research findings
- explicit next steps that should survive the conversation
- task briefs, owner/status, expected validation, and handoffs

Use `type`: `note`, `decision`, `project`, `person`, `reference`, `summary`, `task`, or `inbox`.
Use `confidence`: `low`, `medium`, or `high`.
Use `kind`: `fact`, `observation`, `preference`, `decision`, `hypothesis`, `inference`, or `summary`.
Use `status`: `active`, `superseded`, or `archived`.
Use `sensitivity`: `public`, `private`, or `sensitive`.

Use `capture-session` or `memory_capture_session` at the end of substantive work. Include completed work, decisions, validation, next steps, and open questions.

Do not store secrets, credentials, private tokens, or sensitive raw material unless the user explicitly asks and the repository's storage policy supports it.

## Privacy And Encryption

Markdown files are the source of truth. SQLite indexes and embeddings are derived artifacts.

In daily-use repositories, protected entries must be encrypted before GitHub sync. Protected content includes entries with `sensitivity: sensitive` and directories configured as protected by `config.json`, commonly `people/` and `projects/`.

Template repositories keep encryption artifacts but do not require encryption during normal template work.

```powershell
<brain-root>\tools\memory.ps1 sync-check
<brain-root>\tools\memory.ps1 encrypt <id-or-prefix>
<brain-root>\tools\memory.ps1 decrypt <id-or-prefix>
<brain-root>\tools\memory.ps1 decrypt-in-place <id-or-prefix>
<brain-root>\tools\memory.ps1 encrypt-protected
<brain-root>\tools\memory.ps1 decrypt-protected
```

Keep encryption keys outside Git and outside Markdown notes. The default key name is `SECOND_BRAIN_ENCRYPTION_KEY`; repository tooling may also support an OS secret store. Ask before installing packages such as `cryptography`, generating keys, writing secrets, or changing user-level config.

External embeddings may send note content to a provider. By default, OpenAI embeddings skip `sensitivity: sensitive` and inactive entries. Use include flags only when the user explicitly wants that content sent externally.

## Governance

Use `doctor`/`memory_doctor` to audit frontmatter, duplicate ids, similar titles, broken links, stale index or embeddings, sensitivity policy drift, `.trash`, and redundant summaries.

Use `review`/`memory_review` for triage queues: inbox, open questions, active projects, low-confidence notes, older active notes, and suggested maintenance.

Use `synthesize`/`memory_synthesize` for reviewable drafts such as project state, decision changelogs, summary consolidation, and archive/superseded suggestions. These commands do not rewrite notes automatically.

Use `task-brief`/`memory_task_brief` for delegation memory with owner/status, expected validation, handoffs, and links to decisions/results.

## Bootstrap And Organization

Expected directories include `inbox`, `notes`, `projects`, `decisions`, `people`, `references`, `summaries`, `tasks`, `schemas`, `tools`, `.index`, and `.trash`.

On MCP `initialize`, daily-use repositories may run bootstrap checks and record/update `references/daily-use-bootstrap-status.md`. Template repositories expose and store the bootstrap flow without treating daily setup as required.

Use `install-git-hooks` for repository hooks and `install-agents` to install or refresh a managed global `AGENTS.md` block from `codex/AGENTS.global.md`. Ask before writing user-profile paths when approval is required.

## Index And Embeddings

Run `reindex` after manual Markdown edits or bulk changes. Local embeddings use deterministic `local/hash-v1` vectors for offline semantic search. Configuration lives in `config.json`.

For higher-quality embeddings, use `-Provider openai` only when `OPENAI_API_KEY` is configured and the selected notes are appropriate for external processing. If no OpenAI model is provided, the tooling resolves the default model from config.
