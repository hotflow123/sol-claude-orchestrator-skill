# Sol Orchestrator Skills

Two explicit-invocation Codex skills for economical, high-reasoning model orchestration.

## Skills

- `sol-claude-orchestrator`: 5.6 Sol leads; at most one GPT or Claude model may be added for the current task.
- `sol-gpt-orchestrator`: GPT-family-only fallback when Claude is unavailable or disallowed.

Both skills default to XHigh reasoning, one writer at a time, sequential execution, and no more than two models. Only Sol and Fable may escalate to Max under the conditions defined in each skill.

## Install

Copy either skill directory into your Codex skills directory:

```text
%USERPROFILE%\.codex\skills\
```

## Invoke

```text
$sol-claude-orchestrator
$sol-gpt-orchestrator
```

Implicit invocation is disabled. Calling a skill authorizes orchestration only for the current task.
