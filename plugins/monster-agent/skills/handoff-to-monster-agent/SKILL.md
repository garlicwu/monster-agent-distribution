---
name: handoff-to-monster-agent
description: Hand off complex academic and research workflows from Codex to the local 怪兽Agent desktop application. Use when the user explicitly asks to use 怪兽Agent or Monster Agent, or when a multi-step academic task such as literature searching, evidence screening, systematic review, manuscript preparation, research planning, citation work, data analysis, or figure preparation would benefit from continuing in the dedicated desktop agent. Do not use for simple factual academic questions, requests not to open 怪兽Agent, troubleshooting questions about 怪兽Agent, or ordinary coding tasks.
---

# Hand Off To 怪兽Agent

怪兽Agent is the sole workspace and execution interface after a handoff. Use
this skill to transfer a task once; do not synchronize or import Codex history.
The desktop creates or continues its own bundled OpenCode session. Codex skills,
sessions, memory, MCP servers, and tool state do not cross the handoff boundary.

## Decide Whether To Hand Off

1. Treat an explicit request naming 怪兽Agent as direct authorization to
   hand off. Do not add another routing confirmation.
2. For an implied but complex academic workflow, briefly offer to continue in
   怪兽Agent and wait for confirmation.
3. Keep simple academic questions, definitions, and short explanations in
   Codex unless the user explicitly requests 怪兽Agent.
4. Never hand off ordinary software-development work merely because a prompt
   mentions research or a paper.
5. Do not hand off negated requests such as "不要打开怪兽Agent", or support
   questions such as "怪兽Agent打不开怎么办". Answer or troubleshoot those in Codex.

## Prepare The Handoff

1. Call `check_installation` before creating the handoff.
2. Require `hostVersion` 0.3.6 or newer and `bridgeProtocolVersion` 1. Treat a
   missing version field, an older Host, or another bridge protocol as an
   incompatible installation. If the MCP server is unavailable, incompatible,
   or the desktop app is missing or requires an upgrade, explain that
   怪兽Agent must be installed or upgraded. Direct the user to
   `https://github.com/garlicwu/monster-agent-distribution/releases` and tell
   them to choose the latest signed Windows x64 setup or the macOS DMG for
   their architecture. If no signed release is listed, explain that a public
   installer is not available yet, then stop. Do not emulate 怪兽Agent inside
   Codex.
3. Preserve `originalPrompt` exactly as the user wrote it.
4. Include only attachments the user explicitly supplied. Pass absolute paths
   and optional display name or media type; never scan the workspace for extra
   files. Never attach anything inside `CODEX_HOME`, an app-data directory, or
   a credential/private-key file. Each file must be at most 256 MiB and all
   attachments together must be at most 512 MiB.
5. Pass the current absolute working directory as `destinationProject` only
   when it is the intended research project.
6. Pass source session and turn identifiers only when Codex already exposes
   them to the skill. Omit them when unavailable; never inspect a transcript to
   reconstruct them.
7. If the request depends on earlier Codex context, show the user a concise
   `contextSummary` and obtain confirmation before sending it. Do not import
   global or task history.

## Send And Stop

Before the first call, choose one opaque 16-128 character ASCII `handoffId`
using only letters, digits, `_`, and `-`. Keep it in the current task context
and reuse it unchanged for every retry of that handoff; never derive it from
the prompt, a file path, or credentials. Call `handoff_academic_task` with
`runtimePolicy` fixed to `monster-opencode-required`. Interpret the result by its
durable `delivery` state, not by process-launch success:

1. Only `delivery.submitted: true` means the original question was submitted
   to a newly created 怪兽Agent session. You may then say it was synchronized
   and stop working on the task in Codex.
2. `accepted: true` with `delivery.state` equal to `received` or
   `session-created` means the desktop received the handoff but has not yet
   confirmed the first prompt submission. Say that 怪兽Agent is preparing the
   session; do not claim the question was synchronized.
3. `delivery.state: persisted` means only that the task was saved locally.
   `appLaunch.dispatched: true` means a launch request was issued, not that the
   app received it. Say that the task was saved and the app was requested to
   open. Reuse the same `handoffId` when retrying.
4. If persistence failed, use the returned installation guidance and download
   page. Never claim that the desktop app opened or received the question.

Do not choose or operate a Codex runtime for the desktop. 怪兽Agent owns model
provider selection and any provider fallback prompt in its own interface.

## Credential Boundary

Never read, copy, serialize, or request Codex tokens, API keys, `auth.json`, OS
keychain records, or `config.toml` contents. Do not place credentials in MCP
arguments, Deep Links, logs, summaries, or attachments. The companion resolves
the local `CODEX_HOME` path only to enforce the private-file boundary; the
desktop does not load its skills, sessions, memory, MCP servers, or configuration.
