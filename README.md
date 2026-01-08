# AICP - AI Consumption Protocol

**AICP** (AI Consumption Protocol) dictates how AI systems should discover, select, disclose, and optionally unlock versions of open-source software.

## The Problem: The "Tailwind Paradox"

In late 2024, a Pull Request to add an `llms.txt` file (AI-optimized documentation) to Tailwind CSS was [closed by the creator](https://github.com/tailwindlabs/tailwindcss.com/pull/2388).

His reasoning was brutally honest: **"I just don't have time to work on things that don't help us pay the bills."**

This highlights a growing crisis in Open Source:
1.  **AI Agents consume OSS maintainers' work** at massive scale to power commercial coding tools.
2.  **Maintainers see zero return** from this consumption.
3.  **Gating everything hurts the community** (startups, students, and hobbyists lose access).

We need a middle ground. We need a way to monetize **professional utility** without killing **open access**.

## The Solution: Monetize "Freshness", Not Code

AICP introduces a standard modeled after professional data markets: **Freshness Gating**.

-   **Stable N-1 (Previous Version)**: Always **FREE** and public.
    -   *Perfect for:* Students, indie hackers, startups, OSS contributors.
    -   *AI Behavior:* Unauthenticated agents use this version by default.
-   **Stable N (Latest Version)**: Can be **GATED** (e.g., for 30 days).
    -   *Perfect for:* Enterprises, serious businesses, power users who need the bleeding edge.
    -   *AI Behavior:* Agents check for a "Sponsorship Token". If valid, they unlock and use the latest docs/code.

This creates a sustainable loop:
1.  **Community stays happy**: 99% of the code/docs is always open.
2.  **Startups & Learners** get a stable, proven version (less "hallucinations" from beta features anyway).
3.  **Enterprises pay** a small sponsorship fee to unlock the latest intelligence for their AI fleets.

## How It Works

Repositories publish a simple manifest file: `/.well-known/aicp.json`.

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
  },
  "notice": {
     "short": "Using v1.8.0. Sponsor to unlock v1.9.0 (Latest)."
  }
}
```

## Integration Guide: Add AICP to Your Repo

You can add AICP to any GitHub repository in 5 minutes.

### Step 1: Create the Manifest
Create a file at `.well-known/aicp.json` in your public repository. Use the example above, pointing `public_snapshot` to your previous stable release and `latest_snapshot` to your current one.

### Step 2: Gate the "Latest" Content (Optional)
If you want to enforce the gate:
-   Keep your **latest** documentation or release notes in a private repo or behind a simple auth proxy.
-   Grant access to this private repo to your **GitHub Sponsors** (GitHub handles this automation natively!).

### Step 3: AI Agents Do the Rest
AICP-compliant agents (IDEs, CLI tools) will:
1.  Read your `aicp.json`.
2.  Check if the user has a GitHub Sponsor token for your repo.
3.  **If Sponsored**: Fetch and context-load your *latest* docs.
4.  **If Not**: Fetch and context-load your *N-1* docs and notify the user: *"A newer version is available for Sponsors."*

---

### Core Rules for AI Vendors
To be AICP-Compliant, your agent must:
1.  **Respect `default_policy`**: Do not scrape `latest_snapshot` if it is marked restricted, unless authorized.
2.  **Disclose Version**: Your response must state: *"Using example-lib v1.8.0 (Public Stable)."*
3.  **Honesty**: Do not hallucinate features from the latest version if you only have access to N-1.
