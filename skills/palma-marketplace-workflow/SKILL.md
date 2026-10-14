---
name: palma-marketplace-workflow
description: Connect a compatible agent to Palma through MCP OAuth, then request, track, inspect, and recover Mercado Livre work for an authorized seller account.
---

# Work through hosted Palma

Use Palma's hosted MCP tools from the selected harness. The operator's request sets the objective and scope. Palma does not start an agent or run a task worker.

## Connect before working

For first-time setup, unavailable Palma tools, or client authentication errors, read [references/setup.md](references/setup.md). Configure the current harness with the confirmed public MCP URL, preserve its existing configuration, and have the account holder complete OAuth in the browser. Installing this skill alone does not connect the MCP. Check `palma_mcp_account_status` after connection; report readiness only when it confirms an authorized seller. A connection request authorizes setup, not marketplace changes.

## Select the seller

Call `palma_mcp_account_status` and use its stable `accountId` in every account-scoped call. It lists only sellers authorized for this client. If the requested seller is unclear, ask which seller to use before reading or changing seller data. Do not inspect marketplace state to infer the destination.

To add a seller to this client, call `palma_mcp_connect_account` and have its account holder complete a separate Mercado Livre OAuth approval. Check account status again before working. Another client's seller ID does not grant access. A separate harness can authorize the same seller independently and recover its account history. Use `palma_mcp_select_account` only for an account already listed for this client.

The skill and any client-supported standing instructions are optional. Palma does not require workflow acceptance. Seller work-profile tools store optional source references, product/variation/photo mappings, and commercial preferences; those values do not grant access or approve marketplace effects.

## Read or change marketplace data

- Read current state with `palma_mcp_marketplace_read` when the request depends on it.
- Send the requested API operation with `palma_mcp_marketplace_write`. Provide the method, relative Mercado Livre endpoint, request body, and a new UUID `idempotencyKey`. A new listing uses `POST /items`.
- For corrections, include a precondition against current state and set `correctionOfOperationId` to the earlier operation when one exists.
- Use `palma_mcp_get_work_profile` for seller preferences and the product revision tools for retained source history or photos when relevant.
- Never send credentials to an arbitrary host. Report the actual result; do not describe an unobserved or uncertain effect as completed.

For a website-only action, use the browser available in the selected harness; Palma has no browser session. Confirm the seller ID shown by the site matches the selected Palma account before calling `palma_mcp_begin_browser_operation`. Call `palma_mcp_report_browser_operation` with `attempt_started` and the website seller ID immediately before the site action, then report the outcome as agent-reported evidence. Do not act when the seller identity is missing or different. If browser tools are unavailable before the seller ID is known, mark the claimed task item pending with `palma_mcp_set_task_item_status`, without creating an intent or guessing the seller. If an intent exists but a required browser or research capability is unavailable, report `capability_missing`.

Attempt a CAPTCHA only when the available harness tools can do so. For login, two-factor authentication, or an unsolved CAPTCHA, report `intervention_required`, stop the affected item, and ask the account holder to continue. After intervention, read the page and reconcile the same browser operation before retrying. Other independent items can continue.

## Preview significant changes

Before a significant marketplace write, show one concise preview in chat unless the operator explicitly delegated this bounded task as YOLO. Significant changes include new publications, price or stock changes, bulk edits, pausing or deleting listings, paid actions, and customer messages. Include the selected account, affected items, current and proposed effects, material cost or customer impact, and any invented product claims. Wait for clear approval before writing. One preview can cover a batch; preview changed effects again if the operator changes the request.

YOLO skips previews only for the current bounded task and scope. It does not authorize ongoing account management or unrelated effects. A task, earlier write, saved profile, operation record, or `taskExecution` does not approve a new effect.

Search broadly with available user data and internet research tools to investigate missing product details. Do not claim a search or source you did not use. If research does not resolve a missing fact and you use an invented product claim, identify it as invented and unverified. Outside YOLO, disclose it in the preview before writing. In YOLO, disclose it only in the completion report.

## Track requested batch work

Create a durable task only when the operator wants saved progress or recovery. Call `palma_mcp_create_task` with the bounded objective, stable item identities, selected account IDs, and a new UUID `requestId`. For work across sellers, include every target in `accountIds` and assign each item its target `accountId`; the top-level `accountId` is the connected account used to access the task. Reuse the same ID and content if the request times out. A task tracks work and does not change account access.

Before creating or continuing a multi-seller task, confirm this client has authorized every seller in the task. Task progress and reports remain available to another client explicitly authorized for the same seller account. If a known task returns `task_not_found`, check for a missing seller grant before creating a replacement. Authorize each missing seller separately, then read the task again.

Advance work with `palma_mcp_advance_task`. Use the returned item account and `taskExecution` fields on its marketplace writes and item status updates. Respect pause, cancellation, limits, and executor fences. Use `palma_mcp_control_task` to pause, resume, cancel, or extend limits at the operator's request. Pause and cancel preserve recorded marketplace effects; they do not undo them. Work advances only while a selected client is running.

Task execution does not create approval or YOLO delegation. Preserve any explicit bounded delegation as agent-reported request context and follow the preview rule above.

After an interruption, read the existing task and items from an authorized client. Inspect linked operation records before continuing. Keep the same task and operation IDs; do not start replacements to bypass an active lease or uncertain effect.

## Reconcile and report

For a timed-out or unknown write, call `palma_mcp_get_operation_record`, then reconcile that same operation with `palma_mcp_reconcile_marketplace_operation`. Do not send another write until the result is clear. Reuse the original idempotency key for an allowed retry.

Use `palma_mcp_list_operation_records` to find paginated history. For a durable task, record client-reported usage with `palma_mcp_record_task_usage` and submit outcomes and evidence with `palma_mcp_record_task_completion_report`. Include linked operation IDs and source references, and distinguish service-observed API results from agent-reported browser or research activity. Mark each metric measured, partial, estimated, or unavailable based on the client report. Read summaries with `palma_mcp_list_task_usage_reports` and `palma_mcp_list_task_completion_reports`, then details with `palma_mcp_get_task_usage_report` or `palma_mcp_get_task_completion_report`.

For connection, client authentication, and account reconnection, read [references/setup.md](references/setup.md). Discover exact tool arguments from the connected server's tool schemas before calling them.
