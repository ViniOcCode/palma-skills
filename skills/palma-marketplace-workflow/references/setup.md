# Connect Palma to the current harness

Use this reference when the operator requests connection, Palma tools are unavailable, or authentication fails. Installation supplies instructions; the harness must separately connect to Palma's hosted MCP and complete OAuth. Palma has no separate user account, password, invitation, or dashboard.

## Resolve the service URL

Reuse a Palma MCP URL already configured in this harness, or the public HTTPS URL supplied by the operator or deployment owner. It ends in `/mcp`. If no URL has been supplied, ask for it before changing configuration. The skill's GitHub repository or download URL is not an MCP endpoint.

Identify the current harness from available runtime context and configuration. Use its installed CLI help when commands differ from these examples. Preserve existing servers and settings. Use the tools or shell available to configure the client when the operator requests setup; if this harness cannot change its configuration, give the exact steps the operator must perform. OAuth consent and marketplace authentication belong to the account holder in the browser. Keep tokens, client secrets, and marketplace credentials out of chat and skill files.

## Codex

Inspect existing MCP configuration with `codex mcp list`. For a new Palma connection, replace `<PALMA_MCP_PUBLIC_URL>` with the confirmed URL:

```sh
codex mcp add palma-hosted --url "<PALMA_MCP_PUBLIC_URL>" --oauth-client-registration dcr
```

If the intended connection already exists, keep its server name and authenticate it instead of adding a duplicate:

```sh
codex mcp login palma-hosted --oauth-client-registration dcr
```

Use the actual configured name in place of `palma-hosted` when different. Adding the server may already initiate OAuth; run login if authorization is still needed. If an existing entry points to another service, resolve the intended URL with the operator before replacing it. Reload MCP or start a new session when the current session cannot discover the newly configured tools.

Reference: [Codex MCP](https://learn.chatgpt.com/docs/extend/mcp).

## Hermes

Use the active Hermes profile's configuration; its default location is `~/.hermes/config.yaml`. Merge this entry into the existing `mcp_servers` mapping, replacing the URL:

```yaml
mcp_servers:
  palma-hosted:
    url: "<PALMA_MCP_PUBLIC_URL>"
    auth: oauth
```

Keep other servers and settings. Reuse an existing Palma entry and its name when it already points to the intended service. Run OAuth from a separate terminal if an automatic configuration reload interrupts the flow:

```sh
hermes mcp login palma-hosted
```

Reload MCP or start a new Hermes session when necessary. Hermes handles OAuth discovery and client registration; the account holder does not need Palma's Mercado Livre application secret.

Reference: [Hermes MCP](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/user-guide/features/mcp.md).

## Other harnesses

Use the harness's documented remote Streamable HTTP MCP connection and OAuth support with the confirmed Palma URL. Skill installation support alone does not establish MCP compatibility. If this harness cannot connect to a remote OAuth MCP server, explain the missing capability and use a compatible client at the operator's request. Configuration keys and commands are specific to each client.

## Authorize and verify

The browser goes to Mercado Livre for the account holder's authorization and returns to the MCP client. The provider may reuse an existing login or require sign-in, consent, or challenges. Palma stores marketplace tokens and grants the harness separate MCP access. Mercado Livre rejects API OAuth by operators/collaborators with `invalid_operator_user_id`; use the principal account holder for API authorization. Browser permissions alone do not enable Palma API tools.

After authorization and tool discovery, call `palma_mcp_account_status`. Report the seller identified by the response and whether its connection is usable. If multiple sellers could match the request, ask which to use before any account-scoped read or change. A listed account ID alone does not establish a usable marketplace connection; follow the reported connection state. A successful CLI exit or a tool listing alone does not prove account readiness.

Each new harness authorizes independently, including when another harness already accesses the same seller. For another seller, call `palma_mcp_connect_account`, have its holder complete the returned authorization flow, then check account status again.

## Recover access

- For client OAuth failure or expired/revoked harness access, restart the client's MCP login. If it fails again, report the error and ask the deployment owner to confirm the URL and OAuth availability; stop retrying the same failed flow.
- When a seller tool reports `seller_connection_unavailable` with `nextAction: reconnect_account`, call `palma_mcp_reconnect_account` for that `accountId`, have the holder complete the returned authorization for the same seller, and check account status again.
- `palma_mcp_disconnect_account` revokes this harness's seller access. `palma_mcp_disconnect_marketplace_account` disconnects the seller for all harnesses. Use the action matching the operator's explicit request.
- Reconnection preserves recorded operations. Reconcile an earlier uncertain write before retrying it.
