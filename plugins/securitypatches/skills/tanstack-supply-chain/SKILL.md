---
name: tanstack-supply-chain
description: "Detect the Mini Shai-Hulud supply chain compromise (CVE-2026-45321) affecting @tanstack/*, @mistralai/*, @uipath/*, @squawk/*, and 160+ other npm packages. Scans lockfiles, highlights impacted dependencies, and proposes safe version pins."
argument-hint: "[path-to-project]"
---

# TanStack / Mini Shai-Hulud Supply Chain Compromise Scanner

Scan an npm project for packages affected by the **Mini Shai-Hulud** supply chain attack (CVE-2026-45321, CVSS 9.6) disclosed on May 11 2026.

Reference: https://security.snyk.io/TanStack-npm-Supply-Chain-Compromise-May-2026

The target project directory is: `$ARGUMENTS` (default: current working directory if not specified).

## Background

On May 11 2026 between 19:20–19:26 UTC, an attacker used a GitHub Actions cache-poisoning + OIDC token exploit to publish 84 malicious artifacts across 42 `@tanstack/*` packages. The worm then propagated to 160+ additional packages across `@squawk/*`, `@uipath/*`, `@mistralai/*`, `@tallyui/*`, and other scopes — 373 malicious package-version entries total.

Confirmed-clean TanStack families: `@tanstack/query*`, `@tanstack/table*`, `@tanstack/form*`, `@tanstack/virtual*`, `@tanstack/store`.

## Step 1: Locate dependency manifests

Find the project's dependency files. Check for:
- `package.json`
- `package-lock.json`
- `pnpm-lock.yaml`
- `yarn.lock`

If no lockfile exists, warn the user that only direct dependencies in `package.json` can be checked and transitive dependencies may be missed.

Read the lockfile (preferred) or `package.json` to build a map of every installed package name + resolved version.

## Step 2: Check against compromised package list

Compare every resolved dependency against the lists below. A match requires **both** the package name **and** a compromised version.

### @tanstack/* (42 packages, 84 versions)

| Package | Compromised Versions |
|---|---|
| `@tanstack/history` | 1.161.9, 1.161.12 |
| `@tanstack/react-router` | 1.169.5, 1.169.8 |
| `@tanstack/router-core` | 1.169.5, 1.169.8 |
| `@tanstack/router-utils` | 1.161.11, 1.161.14 |
| `@tanstack/router-plugin` | 1.167.38, 1.167.41 |
| `@tanstack/virtual-file-routes` | 1.161.10, 1.161.13 |
| `@tanstack/router-generator` | 1.166.45, 1.166.48 |
| `@tanstack/start-server-core` | 1.167.33, 1.167.36 |
| `@tanstack/start-client-core` | 1.168.5, 1.168.8 |
| `@tanstack/start-storage-context` | 1.166.38, 1.166.41 |
| `@tanstack/start-plugin-core` | 1.169.23, 1.169.26 |
| `@tanstack/react-start-server` | 1.166.55, 1.166.58 |
| `@tanstack/react-start-client` | 1.166.51, 1.166.54 |
| `@tanstack/start-fn-stubs` | 1.161.9, 1.161.12 |
| `@tanstack/react-start` | 1.167.68, 1.167.71 |
| `@tanstack/react-start-rsc` | 0.0.47, 0.0.50 |
| `@tanstack/react-router-devtools` | 1.166.16, 1.166.19 |
| `@tanstack/router-devtools-core` | 1.167.6, 1.167.9 |
| `@tanstack/router-devtools` | 1.166.16, 1.166.19 |
| `@tanstack/router-ssr-query-core` | 1.168.3, 1.168.6 |
| `@tanstack/react-router-ssr-query` | 1.166.15, 1.166.18 |
| `@tanstack/router-cli` | 1.166.46, 1.166.49 |
| `@tanstack/zod-adapter` | 1.166.12, 1.166.15 |
| `@tanstack/eslint-plugin-router` | 1.161.9 |
| `@tanstack/router-vite-plugin` | 1.166.53, 1.166.56 |
| `@tanstack/nitro-v2-vite-plugin` | 1.154.12, 1.154.15 |
| `@tanstack/solid-router` | 1.169.5, 1.169.8 |
| `@tanstack/solid-start` | 1.167.65, 1.167.68 |
| `@tanstack/solid-start-client` | 1.166.50, 1.166.53 |
| `@tanstack/solid-start-server` | 1.166.54, 1.166.57 |
| `@tanstack/solid-router-devtools` | 1.166.16, 1.166.19 |
| `@tanstack/start-static-server-functions` | 1.166.44, 1.166.47 |
| `@tanstack/vue-router` | 1.169.5, 1.169.8 |
| `@tanstack/solid-router-ssr-query` | 1.166.15, 1.166.18 |
| `@tanstack/valibot-adapter` | 1.166.12, 1.166.15 |
| `@tanstack/vue-start` | 1.167.61, 1.167.64 |
| `@tanstack/vue-start-server` | 1.166.50, 1.166.53 |
| `@tanstack/vue-start-client` | 1.166.46, 1.166.49 |
| `@tanstack/arktype-adapter` | 1.166.12, 1.166.15 |
| `@tanstack/eslint-plugin-start` | 0.0.4, 0.0.7 |
| `@tanstack/vue-router-ssr-query` | 1.166.15, 1.166.18 |
| `@tanstack/vue-router-devtools` | 1.166.16, 1.166.19 |

### @mistralai/*

| Package | Compromised Versions |
|---|---|
| `@mistralai/mistralai` | 2.2.2, 2.2.3, 2.2.4 |
| `@mistralai/mistralai-gcp` | 1.7.1, 1.7.2, 1.7.3 |
| `@mistralai/mistralai-azure` | 1.7.1, 1.7.2, 1.7.3 |

### @squawk/* (22 packages)

| Package | Compromised Versions |
|---|---|
| `@squawk/types` | 0.8.2, 0.8.3, 0.8.4 |
| `@squawk/mcp` | 0.9.1, 0.9.2, 0.9.3, 0.9.4 |
| `@squawk/weather` | 0.5.6, 0.5.7, 0.5.8, 0.5.9 |
| `@squawk/airspace` | 0.8.1, 0.8.2, 0.8.3, 0.8.4 |
| `@squawk/icao-registry-data` | 0.8.4, 0.8.5, 0.8.6, 0.8.7 |
| `@squawk/flightplan` | 0.5.2, 0.5.3, 0.5.4, 0.5.5 |
| `@squawk/airports` | 0.6.2, 0.6.3, 0.6.4, 0.6.5 |
| `@squawk/geo` | 0.4.4, 0.4.5, 0.4.6, 0.4.7 |
| `@squawk/procedure-data` | 0.7.3, 0.7.4, 0.7.5, 0.7.6 |
| `@squawk/navaid-data` | 0.6.4, 0.6.5, 0.6.6, 0.6.7 |
| `@squawk/fix-data` | 0.6.4, 0.6.5, 0.6.6, 0.6.7 |
| `@squawk/navaids` | 0.4.2, 0.4.3, 0.4.4, 0.4.5 |
| `@squawk/fixes` | 0.3.2, 0.3.3, 0.3.4, 0.3.5 |
| `@squawk/airport-data` | 0.7.4, 0.7.5, 0.7.6, 0.7.7 |
| `@squawk/airway-data` | 0.5.4, 0.5.5, 0.5.6, 0.5.7 |
| `@squawk/units` | 0.4.3, 0.4.4, 0.4.5, 0.4.6 |
| `@squawk/procedures` | 0.5.2, 0.5.3, 0.5.4, 0.5.5 |
| `@squawk/airways` | 0.4.2, 0.4.3, 0.4.4, 0.4.5 |
| `@squawk/icao-registry` | 0.5.2, 0.5.3, 0.5.4, 0.5.5 |
| `@squawk/notams` | 0.3.6, 0.3.7, 0.3.8, 0.3.9 |
| `@squawk/flight-math` | 0.5.4, 0.5.5, 0.5.6, 0.5.7 |
| `@squawk/airspace-data` | 0.5.3, 0.5.4, 0.5.5, 0.5.6 |

### @uipath/* (66 packages)

| Package | Compromised Versions |
|---|---|
| `@uipath/apollo-react` | 4.24.5 |
| `@uipath/apollo-wind` | 2.16.2 |
| `@uipath/cli` | 1.0.1 |
| `@uipath/rpa-tool` | 0.9.5 |
| `@uipath/apollo-core` | 5.9.2 |
| `@uipath/filesystem` | 1.0.1 |
| `@uipath/solutionpackager-tool-core` | 0.0.34 |
| `@uipath/solution-tool` | 1.0.1 |
| `@uipath/maestro-tool` | 1.0.1 |
| `@uipath/codedapp-tool` | 1.0.1 |
| `@uipath/agent-tool` | 1.0.1 |
| `@uipath/orchestrator-tool` | 1.0.1 |
| `@uipath/integrationservice-tool` | 1.0.2 |
| `@uipath/rpa-legacy-tool` | 1.0.1 |
| `@uipath/vertical-solutions-tool` | 1.0.1 |
| `@uipath/flow-tool` | 1.0.2 |
| `@uipath/codedagent-tool` | 1.0.1 |
| `@uipath/common` | 1.0.1 |
| `@uipath/resource-tool` | 1.0.1 |
| `@uipath/auth` | 1.0.1 |
| `@uipath/docsai-tool` | 1.0.1 |
| `@uipath/case-tool` | 1.0.1 |
| `@uipath/api-workflow-tool` | 1.0.1 |
| `@uipath/test-manager-tool` | 1.0.2 |
| `@uipath/robot` | 1.3.4 |
| `@uipath/traces-tool` | 1.0.1 |
| `@uipath/agent-sdk` | 1.0.2 |
| `@uipath/integrationservice-sdk` | 1.0.2 |
| `@uipath/maestro-sdk` | 1.0.1 |
| `@uipath/data-fabric-tool` | 1.0.2 |
| `@uipath/tasks-tool` | 1.0.1 |
| `@uipath/insights-tool` | 1.0.1 |
| `@uipath/insights-sdk` | 1.0.1 |
| `@uipath/uipath-python-bridge` | 1.0.1 |
| `@uipath/ap-chat` | 1.5.7 |
| `@uipath/project-packager` | 1.1.16 |
| `@uipath/packager-tool-case` | 0.0.9 |
| `@uipath/packager-tool-workflowcompiler-browser` | 0.0.34 |
| `@uipath/packager-tool-connector` | 0.0.19 |
| `@uipath/packager-tool-workflowcompiler` | 0.0.16 |
| `@uipath/packager-tool-webapp` | 1.0.6 |
| `@uipath/packager-tool-apiworkflow` | 0.0.19 |
| `@uipath/packager-tool-functions` | 0.1.1 |
| `@uipath/widget.sdk` | 1.2.3 |
| `@uipath/resources-tool` | 0.1.11 |
| `@uipath/agent.sdk` | 0.0.18 |
| `@uipath/codedagents-tool` | 0.1.12 |
| `@uipath/aops-policy-tool` | 0.3.1 |
| `@uipath/solution-packager` | 0.0.35 |
| `@uipath/packager-tool-bpmn` | 0.0.9 |
| `@uipath/packager-tool-flow` | 0.0.19 |
| `@uipath/telemetry` | 0.0.7 |
| `@uipath/tool-workflowcompiler` | 0.0.12 |
| `@uipath/vss` | 0.1.6 |
| `@uipath/solutionpackager-sdk` | 1.0.11 |
| `@uipath/ui-widgets-multi-file-upload` | 1.0.1 |
| `@uipath/access-policy-tool` | 0.3.1 |
| `@uipath/context-grounding-tool` | 0.1.1 |
| `@uipath/gov-tool` | 0.3.1 |
| `@uipath/admin-tool` | 0.1.1 |
| `@uipath/identity-tool` | 0.1.1 |
| `@uipath/llmgw-tool` | 1.0.1 |
| `@uipath/resourcecatalog-tool` | 0.1.1 |
| `@uipath/functions-tool` | 1.0.1 |
| `@uipath/access-policy-sdk` | 0.3.1 |
| `@uipath/platform-tool` | 1.0.1 |

### @tallyui/* (10 packages)

| Package | Compromised Versions |
|---|---|
| `@tallyui/connector-medusa` | 1.0.1, 1.0.2, 1.0.3 |
| `@tallyui/theme` | 0.2.1, 0.2.2, 0.2.3 |
| `@tallyui/storage-sqlite` | 0.2.1, 0.2.2, 0.2.3 |
| `@tallyui/connector-vendure` | 1.0.1, 1.0.2, 1.0.3 |
| `@tallyui/core` | 0.2.1, 0.2.2, 0.2.3 |
| `@tallyui/connector-woocommerce` | 1.0.1, 1.0.2, 1.0.3 |
| `@tallyui/components` | 1.0.1, 1.0.2, 1.0.3 |
| `@tallyui/pos` | 0.1.1, 0.1.2, 0.1.3 |
| `@tallyui/database` | 1.0.1, 1.0.2, 1.0.3 |
| `@tallyui/connector-shopify` | 1.0.1, 1.0.2, 1.0.3 |

### Other scoped packages

| Package | Compromised Versions |
|---|---|
| `@draftlab/auth` | 0.24.1, 0.24.2 |
| `@draftlab/db` | 0.16.1 |
| `@draftlab/auth-router` | 0.5.1, 0.5.2 |
| `@draftauth/core` | 0.13.1, 0.13.2 |
| `@draftauth/client` | 0.2.1, 0.2.2 |
| `@taskflow-corp/cli` | 0.1.24, 0.1.25, 0.1.26, 0.1.27, 0.1.28, 0.1.29 |
| `@tolka/cli` | 1.0.2, 1.0.3, 1.0.4, 1.0.5, 1.0.6 |
| `@beproduct/nestjs-auth` | 0.1.2, 0.1.3, 0.1.4, 0.1.5, 0.1.6, 0.1.7, 0.1.8, 0.1.9, 0.1.10, 0.1.11, 0.1.12, 0.1.13, 0.1.14, 0.1.15, 0.1.16, 0.1.17, 0.1.18, 0.1.19 |
| `@mesadev/sdk` | 0.28.3 |
| `@mesadev/rest` | 0.28.3 |
| `@mesadev/saguaro` | 0.4.22 |
| `@ml-toolkit-ts/xgboost` | 1.0.3, 1.0.4 |
| `@ml-toolkit-ts/preprocessing` | 1.0.2, 1.0.3 |
| `@dirigible-ai/sdk` | 0.6.2, 0.6.3 |
| `@supersurkhet/cli` | 0.0.2, 0.0.3, 0.0.4, 0.0.5, 0.0.6, 0.0.7 |
| `@supersurkhet/sdk` | 0.0.2, 0.0.3, 0.0.4, 0.0.5, 0.0.6, 0.0.7 |
| `@opensearch-project/opensearch` | 3.5.3, 3.6.2, 3.7.0, 3.8.0 |

### Unscoped packages

| Package | Compromised Versions |
|---|---|
| `safe-action` | 0.8.3, 0.8.4 |
| `ts-dna` | 3.0.1, 3.0.2, 3.0.3, 3.0.4 |
| `cross-stitch` | 1.1.3, 1.1.4, 1.1.5, 1.1.6 |
| `cmux-agent-mcp` | 0.1.3, 0.1.4, 0.1.5, 0.1.6, 0.1.7, 0.1.8 |
| `agentwork-cli` | 0.1.4, 0.1.5 |
| `git-branch-selector` | 1.3.3, 1.3.4, 1.3.5, 1.3.6, 1.3.7 |
| `wot-api` | 0.8.1, 0.8.2, 0.8.3, 0.8.4 |
| `git-git-git` | 1.0.8, 1.0.9, 1.0.10, 1.0.11, 1.0.12 |
| `nextmove-mcp` | 0.1.3, 0.1.4, 0.1.5, 0.1.6, 0.1.7 |
| `ml-toolkit-ts` | 1.0.4, 1.0.5 |

## Step 3: Report findings

Present the results to the user in a clear table:

```
## Supply Chain Scan Results — Mini Shai-Hulud (CVE-2026-45321)

| Status | Package | Installed Version | Compromised? | Source |
|--------|---------|-------------------|-------------|--------|
| CRITICAL | @tanstack/react-router | 1.169.5 | YES — compromised version | package-lock.json |
| OK | @tanstack/query-core | 5.62.0 | No — clean family | package-lock.json |
```

Use `CRITICAL` for exact version matches, `WARNING` for packages in an affected scope but on a non-compromised version (they should still verify), and `OK` for confirmed-clean packages.

If **no** compromised packages are found, report that clearly:

```
No packages matching the Mini Shai-Hulud compromise were found in this project.
```

If compromised packages **are** found, also display this warning:

```
IMPORTANT: If any compromised version was installed or ran (including in CI),
treat the environment as compromised. Rotate ALL secrets accessible from that
host: npm tokens, GitHub tokens, cloud credentials, environment variables.
```

## Step 4: Propose remediation

For each compromised package found, propose a concrete fix. The compromised versions have been deprecated on npm — the fix is to pin to the latest clean release.

**Strategy:**
1. For `@tanstack/*` packages: use versions released **after** the compromise was remediated. Check `npm view <package> versions --json` to find the latest non-compromised version.
2. For other packages: check whether a clean version exists on npm. If only compromised versions exist, recommend removing the dependency entirely.

Present the proposed changes as a table and **ask the user to confirm before making any changes**:

```
## Proposed Changes

| Package | Current (compromised) | Proposed Version | Action |
|---------|----------------------|------------------|--------|
| @tanstack/react-router | 1.169.5 | 1.170.1 | Update |
| @tanstack/router-core | 1.169.8 | 1.170.1 | Update |

Apply these changes? This will modify package.json.
```

Wait for the user to confirm. When confirmed:

1. Update version pins in `package.json`
2. Run `npm install --ignore-scripts` (or `pnpm install` / `yarn install` depending on the lockfile type) to update the lockfile
3. Verify no compromised versions remain in the updated lockfile

## Step 5: Post-remediation checklist

After applying fixes, present this checklist:

```
## Post-Remediation Checklist

- [ ] Rotated npm tokens for all team members who ran `npm install` on May 11
- [ ] Rotated GitHub tokens / PATs used in CI pipelines
- [ ] Rotated cloud credentials (Azure, AWS, GCP) accessible from CI runners
- [ ] Rotated any secrets stored in environment variables on affected hosts
- [ ] Reviewed CI logs from May 11 for unexpected network activity
- [ ] Checked for unexpected npm publish events in your org
- [ ] Notified security team / posted in #ai-guild
```
