---
name: llm-cost-optimizer-mcp
description: 'Use MCP tools for prompt-heavy tasks: when this skill is explicitly invoked, always optimize the effective task prompt first, then continue downstream work using the optimized prompt unless the result is degraded or unsafe.'
---

# LLM Cost Optimizer MCP

Use this skill when an MCP server exposes these tools:

- `analyze_prompt_waste`
- `optimize_prompt`
- `estimate_prompt_cost`
- `compare_models`

This skill assumes transport-level bearer authentication is already configured. If the server still requires an `apiKey` tool argument, pass it only as a fallback.

## Required wrapper behavior

If the user explicitly mentions `llm-cost-optimizer-mcp` or selects this skill by name, this skill becomes a mandatory wrapper around the downstream task:

1. Build the effective downstream task prompt from the user's request.
2. Run `analyze_prompt_waste` on that effective prompt.
3. Run `optimize_prompt` on that effective prompt.
4. If the optimization result is `success` and does not materially change intent, continue the real task using the optimized prompt as the working prompt.
5. If the optimization result is `degraded`, invalid, or unsafe, fall back to the original user request and mention the fallback.

Do not skip the optimization step when this skill was explicitly invoked.

## Tool order

1. Use `analyze_prompt_waste` first for prompt compression, redundancy, or placeholder analysis.
2. If this skill is explicitly named or selected for the task, follow the required wrapper behavior above.
3. Use `optimize_prompt` when the user wants a rewritten prompt or when this skill is acting as the explicit wrapper described above.
4. Use `estimate_prompt_cost` for one known target model.
5. Use `compare_models` when the user wants the lowest-cost supported option.

## Downstream handoff

- If the user explicitly mentions `llm-cost-optimizer-mcp`, the backend optimization step must happen before coding or other downstream skill work begins.
- Use the optimized prompt as the prompt handed off to Codex for downstream reasoning and execution.
- If the optimization result is `degraded`, invalid, or would materially change intent, fall back to the original user request and mention the fallback.
- Do not silently optimize unrelated conversation when this skill was not explicitly invoked or clearly triggered by a prompt-heavy task.

## Response handling

- Read `structuredContent.status` first.
- `success`: use `structuredContent.data`.
- `degraded`: treat the result as analysis-only or suggest-only.
- `error`: surface `structuredContent.error.code`, `message`, and any relevant `details`.

## Optimize rules

- Preserve placeholders exactly.
- Use `structuredContent.data.analysis` to explain waste signals and placeholder checks.
- Use `structuredContent.data.optimization` for the suggested rewrite.
- Use `structuredContent.data.delivery.mode` to describe whether the result is:
  - `analysis`
  - `suggest`
  - `auto_apply`
- Unless `delivery.autoApplyEnabled` is `true`, never describe the rewrite as automatically applied.

## Guardrails

- If `status` is `degraded`, mention that the server safely fell back.
- If `error.code` is `quota_exceeded` or `rate_limited`, stop and surface the limit details.
- If `error.code` is `payload_too_large`, ask for a smaller prompt instead of retrying the same payload.
- If `error.code` is `invalid_api_key`, do not keep retrying the same request.
