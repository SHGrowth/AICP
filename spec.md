# AICP Specification v0.1

**Status**: Draft
**Version**: 0.1
**Date**: 2026-01-08

## 1. Introduction

The AI Consumption Protocol (AICP) defines a mechanism for open-source maintainers to declare how Artificial Intelligence systems should consume their intellectual property (code and documentation). It introduces the concept of **"Freshness Gating"**, allowing differentiation between public access (defaulting to previous stable versions) and entitled access (allowing access to the latest versions).

## 2. Terminology

-   **Consumer**: An AI agent, LLM, or scraper accessing the repository.
-   **Maintainer**: The owner of the repository.
-   **Stable N-1**: The *previous* major or minor stable release of the software.
-   **Stable N**: The *current*, most recent stable release.
-   **Snapshot**: A frozen state of documentation and code corresponding to a specific version.

## 3. Protocol Requirements

### 3.1. Discovery

The Consumer MUST look for a manifest file in one of the following locations (in order of preference):
1.  `/.well-known/aicp.json`
2.  `/aicp.json`

### 3.2. The Manifest (`aicp.json`)

The manifest MUST be a valid JSON file adhering to the `aicp.schema.json`.

#### Fields:

-   `aicp_version` (Required): String. The version of the protocol (e.g., "0.1").
-   `project` (Required): String. The name of the project.
-   `default_policy` (Required): Enum [`stable-n-1`, `latest-public`].
    -   `stable-n-1`: Consumers MUST use the `public_snapshot` by default.
    -   `latest-public`: Consumers MAY use `latest_snapshot` freely.
-   `public_snapshot` (Required): Object describing the free, publicly accessible version.
    -   `version`: Semver string.
    -   `docs`: URL to documentation.
    -   `code`: URL to source code (tag/branch).
-   `latest_snapshot` (Optional): Object describing the current stable version.
    -   `version`: Semver string.
    -   `access`: Enum [`public`, `entitled`].
    -   `entitlement` (Optional): Object describing how to unlock.

### 3.3. Consumer Behavior

#### 3.3.1. Default Access
If `default_policy` is `stable-n-1`:
-   The Consumer MUST NOT use data from `latest_snapshot` unless it possesses a valid entitlement.
-   The Consumer MUST use `public_snapshot` for generation and retrieval.

#### 3.3.2. Entitled Access
If the Consumer possesses a valid entitlement (e.g., a verified GitHub Sponsor status):
-   The Consumer MAY access and use `latest_snapshot`.

#### 3.3.3. Mandatory Disclosure
The Consumer MUST explicitly state which version relies on in its final response to the user.
-   *Template*: "Based on {Project} {Version} ({Type})."
-   *Example*: "Based on Pandas v1.1.0 (Public Previous Stable)."

#### 3.3.4. Upgrade Notice
If the Consumer is using `public_snapshot` and a `latest_snapshot` exists with `access: entitled`:
-   The Consumer MUST inform the user that a newer version is available via the entitlement method.

## 4. Entitlement Mechanisms

### 4.1. GitHub Sponsors (Type: `github-sponsors`)
The entitlement is verified by the user's ability to access a private repository or resource linked to a generic GitHub Sponsors tier.

## 5. Security & Privacy
AICP is a signaling protocol. It does not enforce DRM. Enforcement relies on:
1.  **Honesty policy** of compliant AI vendors.
2.  **Physical access control** (e.g., private repos) for the `latest_snapshot` assets, if the maintainer chooses to gate them.
