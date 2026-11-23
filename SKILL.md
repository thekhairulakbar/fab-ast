---
name: fab-ast
description: "Plan, delegate and verify larger builds across Claude Code and Codex. Use Astra 6.2 by default when available; use an explicitly selected alternative only after checking the route. Delegate suitable work, execute directly where appropriate, track and cancel only the exact launched task, and report who did what. Trigger on /fab-ast, fab ast, use astra for this, budget build, or requests to protect Anthropic usage. Costs and savings require evidence."
---

# Fab-Ast — Claude plans, Astra executes

Claude plans/checks and executor executes suitable builds when that cross-provider route is available. The purpose is to move eligible execution to the user's chosen account; quality, billing route and savings must be verified, never promised from generic percentages.

## Shared host and model selection

- In Claude Code, use the configured local proxy route only after verifying its endpoint, authentication, available models and CLI options. In Codex, inspect current tools first: direct work or an authorized native child-agent route may already provide the executor. Do not launch a second proxy session merely because the skill was loaded in Codex. Invoking fab-ast authorizes its scoped delegation policy; a review of this file does not invoke it.
- **Default: `gpt-6.2-astra`.** Invoking `fab-ast` selects Astra without an extra instruction. Keep helpers on `gpt-6-luna`. Use `gpt-6.1-sol` only when he explicitly selects Sol. If Astra is unavailable, report it rather than silently falling back. Verify all IDs on the chosen route; model names alone do not establish quality, cost, quota or tool access.
- **Astra mappings:** set `--model`, `ANTHROPIC_DEFAULT_SONNET_MODEL` and `ANTHROPIC_DEFAULT_OPUS_MODEL` together to `gpt-6.2-astra`; keep both helper mappings on verified `gpt-6-luna`. The Codex OAuth catalog for this route specifies 272000 context tokens for Astra 6.2, and the test reported the same window. Do not copy the different Copilot catalog window. Recheck the route's catalog on future upgrades.
- Check actual filesystem, shell, browser, connector and rendering access before splitting the task. Never assume a headless executor has or lacks a capability solely because of its name.
- Preserve host permissions. Never modify approval settings, broaden tool grants, or use another execution route to bypass a denied action. Obtain credentials only through the approved local mechanism; never print secrets or put them into specs or receipts.

## Step 0 — Fit triage (run on EVERY invocation; widened 2026-07-16)

**/fab-ast is an execution policy, not a gate.** the user attaches it to instructions as a general "go" — including multi-part instructions and tasks executor cannot do. Never stall or bounce the task back; triage each part into one of three buckets and proceed:

1. **Dispatch to executor** — heavy, self-contained building (files, code, documents, data grinding) needing only file tools + Bash, roughly 5+ minutes of work. This is the split that saves Anthropic usage: continue with Step 0.5 onward.
2. **Execute directly (Fable)** — anything the selected executor cannot reach or must not touch after capability checks: browser and MCP work, computer use, quick checks and verifications, questions, and correspondence or public-content drafts (those flow through humanizer → my-voice / the writer skills — executor may produce raw material, but its text never goes to the user directly). Also anything under ~5 minutes, where dispatch overhead costs more than it saves. Do it inline and say so in one line ("executed directly — needs the browser / under 5 min").
3. **Hybrid** — split the instruction: Fable does the session-bound parts, executor gets the buildable core as its own focused spec.

Always report who executed what (Step 4).

*(Prior narrower rule, kept for history: the skill originally refused sub-5-minute tasks, correspondence, and session-only-tool tasks outright. Those are now bucket 2, not refusals.)*

## Step 0.5 — Preflight (only when something will dispatch; silent when healthy)

1. Proxy up? `curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:8317/` → expect `200`.
   - If not: `brew services restart cliproxyapi`, wait 3s, retry once.
2. Credential present? `ls ~/.cli-proxy-api/codex-*.json` — a `-plus` (or better) file must exist.
   - If missing or requests return auth errors: STOP and tell the user to run `cliproxyapi --codex-login` in a terminal and log in (only he can authenticate).
3. Read the proxy key at runtime — never hardcode it anywhere:
   `KEY=$(grep -A2 '^api-keys:' /opt/homebrew/etc/cliproxyapi.conf | grep -oE 'sk-[a-zA-Z0-9-]+' | head -1)`
4. Query the configured endpoint's model list using the locally loaded key without printing it. Verify the executor and every auxiliary model ID are served. A listed model does not establish pricing, plan eligibility or feature support. Use a user-authorized available model; never silently replace the default with another family or edit this skill merely because today's list differs.
5. Check the installed dispatch CLI's help/version for supported options and the current permission configuration. Historical allow rules or prior success do not guarantee today's launch is authorized. If blocked, report the specific blocker; do not change global safety settings or bypass the rejection.

## Step 1 — PLAN (Fable, this session)

Write a build spec the way the user writes requests: **what → why → how**, with
zero ambiguity for the recipient (executor):

- Exact deliverables with full file paths
- Constraints and vault rules that apply (naming conventions, folder placement, no vault-root writes)
- Acceptance criteria — a checklist Fable will verify in Step 3
- What executor must NOT do (no deletions, no moves outside the target folder, no git)

Save the spec to `fab-ast-spec.md` inside the session scratchpad (never the vault).

### Spec-writing rules — the executor reproduces the shape you model (added 2026-08-06)

executor follows the spec faithfully. That is the point, and it is also the failure mode: **whatever shape the spec models, the output carries** — so a defect in the spec becomes a defect in every file executor writes, and it looks like an executor error in the review. Rules 1 and 2 come from real defects on the earlier schema-build dispatch, where two of five defect classes traced to the spec rather than to executor; rule 3 from the 2026-09-15 earlier dispatches:

1. **Never abridge an enumeration.** Paste the target's **full** allowed-value list, or point at the authority file that defines it — never a representative subset. A spec that listed five of a schema's ten `type:` values gave executor no correct option for a page needing one of the missing five, and the page landed outside the enum entirely. executor had nothing to work with; the spec had removed the right answer. Applies to frontmatter enums, status vocabularies, category lists, tag sets — anything the output must choose *from*.

2. **Express every path in the form the output must carry.** Paths given as examples get copied verbatim into deliverables. A spec that wrote raw sources as absolute filesystem paths produced five pages whose `source:` field held absolute paths, against a schema requiring wiki-relative pointers. If the output needs a relative path, write the relative form in the spec and name the root separately ("root is `<...>`; all `source:` values are relative to it"). Reserve absolute paths for executor's own read/write targets, and say which is which.

3. **When the deliverable has prose, include a two-sentence style model** (added 2026-09-28). executor copies the shape it is shown. With no model it writes keyword lists (every summary on the 2026-09-15 earlier build session needed a full rewrite). Two sentences written the way the output should read are enough.

Generalize past all three: before dispatching, reread the spec asking *what would a faithful copy of this produce* — not *is this understandable*. Ambiguity stalls a dispatch loudly; a faithfully-copied wrong shape ships silently and passes Step 3 unless the acceptance checklist names the schema.

## Step 2 — EXECUTE (executor, headless)

**Model choice:** use the verified default or the user's selected model. Resolve the auxiliary model to a verified, supported ID too. Record actual IDs and route. The command below is a template: replace model values only with verified authorized choices and confirm CLI flags first.

**Track this dispatch before launch.** Use a unique task directory and save a non-secret dispatch record: task ID, host session/agent handle or process handle, PID and start identity when available, working directory, spec path/hash, model, output paths and start time. Retain the launcher's process object/session handle; a PID file alone is insufficient if that PID can be reused. Capture exit status and logs scoped to this task. For native agents, retain the returned agent ID. Do not use process-name searches to discover ownership later.

```bash
cd "<target working folder>" && \
env -i HOME="$HOME" PATH="/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin" \
  ANTHROPIC_BASE_URL=http://127.0.0.1:8317 \
  ANTHROPIC_AUTH_TOKEN=$KEY \
  ANTHROPIC_SMALL_FAST_MODEL=gpt-6-luna \
  ANTHROPIC_DEFAULT_HAIKU_MODEL=gpt-6-luna \
  ANTHROPIC_DEFAULT_SONNET_MODEL=gpt-6.2-astra \
  ANTHROPIC_DEFAULT_OPUS_MODEL=gpt-6.2-astra \
  CLAUDE_CODE_MAX_CONTEXT_TOKENS=272000 \
  claude --model gpt-6.2-astra --permission-mode acceptEdits \
  --allowedTools "Bash(ffmpeg:*),Bash(ffprobe:*),Bash(python3:*),Bash(cp:*),Bash(mkdir:*),Bash(ls:*),Bash(cat:*),Bash(md5:*),Bash(cd:*),Bash(echo:*)" \
  -p "$(cat "<scratchpad>/fab-ast-spec.md")"
```

**If the spec requires shell commands (builds, ffmpeg, test runs), pass a scoped `--allowedTools` Bash allowlist for exactly those binaries; a dispatch without it will stall on the first Bash call.** `--permission-mode acceptEdits` covers file edits only — headless `-p` has no one to approve Bash prompts. Scope the allowlist to the binaries the spec actually needs (the ffmpeg/PIL example above is from a video-assembly build); for a pure file-writing spec, drop the flag entirely. Never `--dangerously-skip-permissions`.

- `env -i` is REQUIRED when dispatching from a desktop-app session: the desktop
  harness injects its own ANTHROPIC_BASE_URL and OAuth variables, and without a
  clean environment the nested claude authenticates against them and the proxy
  returns 401 Invalid API key (hit and confirmed 2026-07-15).
- **The four model-mapping vars are REQUIRED too.** The proxy serves only OpenAI
  models; Claude Code fires background/auxiliary requests (session titles,
  summaries, subagents) using hardcoded *Anthropic* model ids (e.g.
  `claude-haiku-4-5`). Against the OpenAI-only proxy those return **401 Invalid
  API key**, which storms as endless retries ("Churned") even though the main
  `gpt-6.2-astra` request is fine — the key is valid, the *model* isn't. Mapping
  haiku→a cheap sibling (keeps background off the flagship pool) and
  sonnet/opus→`gpt-6.2-astra` routes every model class to something the proxy can
  serve (diagnosed + confirmed 2026-07-15; light `-p` tests don't trigger it, so
  it only surfaces on real runs). The cheap sibling must be an id `/v1/models`
  lists today (Step 0.5.4). It was `gpt-5.4-mini` until the proxy dropped it
  (502 "unknown provider" on every side request, 2026-09-15); it is
  `gpt-6-luna` since 2026-10-24 (`gpt-5.6-luna` from 2026-09-28 before that).
- Run in the background with a generous timeout (10 min+); big builds take a while.
- **executor's write destinations must live inside its CWD tree, and never under any `.claude/` directory** — `acceptEdits` does not cover config-dir writes, and a headless session stalls politely with exit 0 and nothing built. A deliverable whose final home is a protected path gets built in a scratch dir (give it `--vault`/`--out`-style override flags so executor can self-test there); Fable installs to the real location and re-verifies.
- `--permission-mode acceptEdits` lets executor write files in the target folder without prompts; Bash needs the explicit allowlist above. Never escalate to skip-permissions.
- One task per dispatch. For multi-part work, send sequential focused specs rather than one mega-spec.

## Step 3 — CHECK (Fable, this session)

1. Read what executor produced — the target files on disk, not executor's end-of-run report. Walk the acceptance checklist item by item.
2. Gaps or spec violations → send ONE focused fix spec to executor (same mechanics as Step 2).
3. Still failing after 2 fix passes → finish the remainder yourself (Fable) and tell the user the handoff friction happened — that is signal for tuning this skill.
4. **On takeover:** stop the recorded dispatch before starting a replacement that can write to the same outputs. Use the native agent/session cancellation API or retained process handle. Verify task ownership and process start identity before signaling a PID. If ownership cannot be established, do not signal it; report the unresolved run and use a separate output directory to avoid concurrent writes.
5. **Close only this dispatch:** after verified deliverables, allow a bounded normal exit. If it stalls, request graceful cancellation of the exact recorded task, wait and check exit. Escalate termination only for that same verified task if required and permitted. Never use `pkill -f`, `killall`, a model-name search, or kill the shared proxy service. Signal a process group only when the launcher created an isolated group and proved it contains only this task. Record completion/cancellation and any unresolved child work; never claim stopped without checking. Apply the same ownership checks to retries and late results.

## Step 4 — Report

Tell the user: what was built (with clickable file paths), who did what (Fable
planned/checked, executor built), which checks passed, and the verified execution route. Report usage or savings only when measured
and comparable; otherwise say savings were not measured. A proxy response does
not prove which subscription was charged.
For anything triaged to direct execution (bucket 2), one line each: executed
directly + the reason (browser/MCP-bound, voice work, or under 5 minutes).
Requests are auditable in `~/.cli-proxy-api/logs/` (needs `logging-to-file: true` in the proxy config; failed requests always write `error-v1-messages-*.log`, showing the key, model, and status).

## Model migration — when a new GPT family ships

**Current pin:** Astra 6.2 since the user's explicit 22 November 2026 decision. Prior Sol migration history below is retained for diagnosis.

The executor id is pinned **explicitly** — the proxy exposes no `latest` alias, so a newer model (a future `gpt-5.7-*`, etc.) is never picked up on its own. Nothing here is automatic; a bump is a manual run-down of four pins. Confirmed 2026-07-21: on the 9 Jul 2026 GPT-5.6 launch the skill was already on `gpt-5.6-sol` and needed no change, but only because that id was the default all along — **2026-10-04: bumped by hand `gpt-5.6-sol` → `gpt-6.1-sol` after CLIProxyAPI 7.2.75 → 8.0.10; background ids stay on `gpt-6-luna`, still served.** the *next* family will not be.

Do them **in this order** (proxy first — the other three are premature until the id is actually served):

1. **CLIProxyAPI upstream** — `brew upgrade cliproxyapi`, then confirm the new id is live: `curl -s http://127.0.0.1:8317/v1/models -H "Authorization: Bearer $KEY"`. If the id isn't in that list, stop — nothing below will work yet.
2. **This SKILL.md, Step 2** — swap `--model gpt-6.2-astra` (the default since 2026-11-22) to the new flagship id, and the two mapping vars pointing at it: `ANTHROPIC_DEFAULT_SONNET_MODEL` and `ANTHROPIC_DEFAULT_OPUS_MODEL`. Check the background mappings (`ANTHROPIC_SMALL_FAST_MODEL` / `ANTHROPIC_DEFAULT_HAIKU_MODEL`, currently `gpt-6-luna`) against the same list: keep them while the id is still served, otherwise move them to the new family's cheapest sibling. Then **preflight + repackage** per Skill conventions.
3. **Host permission settings** — inspect the current permission rules. A previous successful dispatch does not authorize every future task. Do not edit or broaden approval settings to launch a child. If blocked, report the exact denial and follow the host approval process.
4. **`~/.zshrc` → `claudex` alias** — carries the same four `ANTHROPIC_*_MODEL` mapping vars; update to match Step 2. **Also update `CLAUDE_CODE_MAX_CONTEXT_TOKENS`** in both the alias and the Step 2 command to the new model's `context_length` from CLIProxyAPI's `internal/registry/models/models.json` (272000 for `gpt-6.2-astra` on the Codex OAuth route, checked 2026-11-22): Claude Code 2.1.285+ does not know GPT model names and otherwise assumes a 200k window for auto-compact.

Then smoke-test: one light `-p` dispatch on the new id (light tests don't trigger the background-model 401 storm, so also run one real build before declaring it done). The cheaper siblings `terra`/`luna` follow the same id pattern — only Step 2's `--model` swap references them, on request. **Never bump ahead of the proxy:** a dispatch against an unservable id returns 401 "Invalid API key" that looks like a credential fault but isn't.
