---
inclusion: always
---

# Shared Actions Repo — Standards & Guidelines

This repo (`jameselsey/actions`) contains reusable GitHub Actions for personal projects. It is **public**, so every file in it must be safe for public viewing at all times.

## Security rules — non-negotiable

- **Never hardcode secrets, tokens, API keys, webhook URLs, or credentials** in any action file, README, or config.
- All sensitive values must be passed in as `inputs` by the caller and sourced from their repository secrets (e.g. `${{ secrets.DISCORD_WEBHOOK_URL }}`).
- Never reference a specific repository name, organisation, or personal identifier inside an action's logic. Actions must be generic.
- Before adding or modifying any file, check: "would I be comfortable if anyone on the internet read this?" If not, it doesn't belong here.

## Action structure

Each action lives in its own directory: `<action-name>/action.yml`

Every action directory must contain:
- `action.yml` — the action definition
- `README.md` — documentation (see below)

### action.yml conventions

- Use `using: 'composite'` with `shell: bash` steps. Avoid Docker actions unless there is a strong reason.
- All inputs must have a `description`. Mark `required: true` only when there is genuinely no sensible default.
- All outputs must have a `description`.
- Use `jq` for JSON construction — never build JSON by string concatenation.
- Multi-line step outputs must use the heredoc pattern:
  ```bash
  {
    echo 'key<<EOF'
    echo "$VALUE"
    echo 'EOF'
  } >> $GITHUB_OUTPUT
  ```
- Include clear `echo` progress messages so the Actions log is readable.
- Exit with a non-zero code and a descriptive message on any unrecoverable error.

## README conventions

Every action README must include:

1. **One-line description** at the top — what it does and why you'd use it.
2. **Usage example** — a minimal working YAML snippet using `jameselsey/actions/<name>@main`.
3. **Inputs table** — columns: Input | Required | Default | Description.
4. **Outputs table** (if the action has outputs) — columns: Output | Description.
5. Any **prerequisites** the caller needs to set up (secrets, config file format, etc.).

Keep examples generic — no repo-specific names, no hardcoded values.

## Root README

`README.md` at the repo root must stay up to date. Whenever an action is added or removed:
- Add or remove its entry under the `## Actions` section.
- The entry should link to the action's directory and give a one-line description.

## Referencing these actions from other repos

Always pin to `@main`:
```yaml
uses: jameselsey/actions/discord-notify@main
uses: jameselsey/actions/android-api-level-check@main
```

Caller repos are responsible for:
- Providing all required secrets
- Providing any config files the action reads from their own repo
- Controlling the schedule/trigger via their own workflow files

## Adding a new action — checklist

- [ ] Create `<action-name>/action.yml` following the conventions above
- [ ] Create `<action-name>/README.md` with all required sections
- [ ] Update root `README.md` to include the new action
- [ ] Verify no sensitive data is present anywhere in the new files
- [ ] Ensure the action is generic enough to work across multiple repos, not just one
