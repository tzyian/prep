# Repository Guidelines

## Project Structure & Module Organization
- Root contains Markdown notes for interview prep. The Obsidian vault lives under `Interview Prep/` (e.g., `Interview Prep/Welcome.md`).
- Organize topics by directory: e.g., `distributed-systems/`, `web-security/`, `os-concurrency/` under `Interview Prep/`.
- Place related media next to the note that references it (e.g., `Interview Prep/distributed-systems/raft.md` and `raft-diagram.png`).
- Use internal links for cross-references: `[[Lamport Time]]`, and relative links for external assets when needed.

## Build, Preview, and Development Commands
- No build or test pipeline is required. Notes are plain Markdown.
- Preview with Obsidian (recommended) or any Markdown viewer.
- Helpful local searches:
  - Find mentions: `rg "Raft" -n "Interview Prep/"`
  - List Markdown files: `rg --files "Interview Prep/" | rg "\\.md$"`

## Coding Style & Naming Conventions
- File names: kebab-case without spaces (e.g., `two-generals-problem.md`). Keep existing names, but prefer kebab-case for new files.
- Headings: one `#` H1 matching the file topic; use hierarchical `##/###`.
- Snippets: fenced code blocks with language hints (e.g., ```go, ```python).
- Lists and callouts: use concise bullets; add brief context and one concrete example per concept where helpful.

## Testing Guidelines
- No automated tests. Instead, verify:
  - Examples compile/run when applicable; commands are accurate.
  - Links resolve (internal `[[...]]` and external URLs).
  - Spelling of key terms (e.g., “Lamport”, “linearizability”).

## Commit & Pull Request Guidelines
- Commits: use clear, scoped messages. Examples:
  - `docs(ds): add notes on FIFO vs causal broadcast`
  - `docs(security): clarify TLS handshake sequence`
- PRs: include a brief summary, before/after (when refactoring), and links to sources. Keep diffs focused; avoid unrelated renames.

## Security & Configuration Tips
- Do not commit secrets, tokens, or private endpoints in notes or links.
- Large binaries are discouraged; prefer links or lightweight diagrams.
- Maintain the Obsidian structure; avoid renaming `Interview Prep/` without discussion.
