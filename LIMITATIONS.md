# Known Limitations

## Claude.ai: One Connector Per OAuth Issuer

Claude.ai currently enforces a limit of **one MCP connector per OAuth authorization server (issuer)**. This means if you have multiple MCP servers behind a single mcp-front instance (e.g., `/archon/mcp`, `/obsidian/mcp`, `/openmemory/mcp`), Claude.ai will only let you add one of them as a connector — because they all share the same issuer URL.

mcp-front correctly implements RFC 8707 per-service audience scoping, where each service gets its own audience-bound token. However, Claude.ai's connector UI does not yet support RFC 8707 multi-service connections from a single OAuth issuer.

### Workarounds

1. **`dangerouslyAcceptIssuerAudience: true`** — Add this to your `auth` config. This allows a single base-issuer-scoped token to access all services, so Claude.ai only needs one connector. You lose per-service token isolation, but for trusted internal servers this is a reasonable trade-off. Easily reversible when Claude.ai adds RFC 8707 support.

2. **Separate mcp-front instances** — Run a different instance per server on different ports, each acting as its own OAuth issuer. Tailscale Funnel only supports ports 443, 8443, and 10000, capping you at 3 funneled services. Operationally heavy.

3. **Wait** — mcp-front is already built for RFC 8707. When Claude.ai catches up, the limitation goes away with no changes needed on the server side.

### References

- [RFC 8707 - Resource Indicators for OAuth 2.0](https://datatracker.ietf.org/doc/html/rfc8707)
- [Claude.ai Custom Connectors](https://support.claude.com/en/articles/11503834-building-custom-connectors-via-remote-mcp-servers)
- [MCP Remote Server Connection Guide](https://modelcontextprotocol.io/docs/develop/connect-remote-servers)
