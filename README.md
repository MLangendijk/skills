# Skills

A collection of Claude Code plugins and skills by MLangendijk.

## Getting Started

### Add the marketplace

```
/plugin marketplace add MLangendijk/skills
```

### Install a plugin

```
/plugin install <plugin-name>@skills
```

### Reload after installing

```
/reload-plugins
```

## Available Plugins

| Plugin | Description |
|--------|-------------|
| `blits` | A collection of skills for working with Lightning JS Blits projects, including migration, scaffolding, and more. |
| `securitypatches` | Security skills for detecting supply chain compromises, vulnerable dependencies, and other CVEs in your project. |

### blits

Install with `/plugin install blits@skills`

**Skills included:**

- **migrate-blits** — Migrate a Lightning JS project from Blits v1 (Lightning 3.0) to Blits v2 (Lightning 3.1). Handles props, effects, built-in variables, dimension aliases, and dependency updates.

### securitypatches

Install with `/plugin install securitypatches@skills`

**Skills included:**

- **tanstack-supply-chain** — Detect the Mini Shai-Hulud supply chain compromise (CVE-2026-45321) affecting @tanstack/*, @mistralai/*, @uipath/*, @squawk/*, and 160+ other npm packages. Scans lockfiles, highlights impacted dependencies, and proposes safe version pins.
