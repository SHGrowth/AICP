# AICP - AI Consumption Protocol

**AICP** (AI Consumption Protocol) dictates how AI systems should discover, select, disclose, and optionally unlock versions of open-source software.

It establishes a standard contract between maintainers and AI agents to ensure:
1.  **Stability**: AI defaults to "proven" versions (stable N-1) to reduce hallucinations.
2.  **Sustainability**: Maintainers can gate the "freshness" of the absolute latest version (stable N) behind sponsorship or auth.
3.  **Honesty**: AI systems must explicitly disclose which version they are using.

## The Core Rule: "Stable N-1"

By default, an AICP-compliant AI agent will consume the **previous stable release** (N-1) of a library.

-   **Why?** This version is battle-tested, has established documentation, and is less likely to change under the AI's feet.
-   **Can I get the latest?** Yes. If the maintainer allows it, you can "unlock" the latest stable version (N) by providing an entitlement (e.g., a GitHub Sponsor token).

## How it Works

Repositories publish a standard manifest file: `/.well-known/aicp.json`.

### Example `aicp.json`

```json
{
  "aicp_version": "0.1",
  "project": "example-lib",
  "default_policy": "stable-n-1",
  "public_snapshot": {
    "version": "1.8.0",
    "docs": "https://example.org/docs/1.8"
  },
  "latest_snapshot": {
    "version": "1.9.0",
    "access": "entitled",
    "entitlement": {
      "type": "github-sponsors",
      "url": "https://github.com/sponsors/example"
    }
  }
}
```

## For AI Vendors

To be AICP-Compliant, your agent must:
1.  **Check for `aicp.json`** in the repository root or `.well-known/`.
2.  **Respect `default_policy`**: Do not scrape `latest_snapshot` if it is marked restricted, unless the user provides credentials.
3.  **Disclose Version**: Your response must state: *"Using example-lib v1.8.0 (public stable)."*

## For Maintainers

1.  Add `aicp.json` to your repo.
2.  (Optional) Gate your latest docs/code behind a private repo or authenticated endpoint.
3.  Enjoy a customized lane for AI traffic that supports your project.
