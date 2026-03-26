---
name: migrate-blits
description: "Migrate a Lightning JS project from Blits v1 (Lightning 3.0) to Blits v2 (Lightning 3.1). Handles props, effects, built-in variables, dimension aliases, and dependency updates."
argument-hint: "[path-to-project]"
---

# Blits v1 → v2 Migration Tool

Automatically migrate a Lightning JS project from **Blits v1** (`@lightningjs/blits ^1.x` / Lightning 3.0) to **Blits v2** (`@lightningjs/blits ^2.x` / Lightning 3.1).

The target project directory is: `$ARGUMENTS` (default: current working directory if not specified).

## Pre-flight

Before making any changes:
1. Read the project's `package.json` to confirm it uses `@lightningjs/blits` v1.x
2. If the project already uses v2.x, inform the user and stop
3. List all `.js` and `.ts` files under `src/` to understand the scope
4. Create a summary of what will change and present it to the user before proceeding

## Migration Steps

Execute these steps **in order**. After each step, report what was changed.

---

### Step 1: Update `package.json` dependencies

- Change `@lightningjs/blits` version from `^1.x` to `^2.0.0`
- Add `@lightningjs/msdf-generator` as a devDependency (`^1.1.1`) — this is no longer bundled with Blits
- Do NOT run `npm install` yet — just edit the file

```
BEFORE: "@lightningjs/blits": "^1.51.0"
AFTER:  "@lightningjs/blits": "^2.0.0"

ADD devDependency: "@lightningjs/msdf-generator": "^1.1.1"
```

---

### Step 2: Convert props from Array to Object syntax

Search all component files for `props:` declarations that use array syntax.

**Pattern to find:**
```js
props: ['propA', 'propB', 'propC']
```

**Replace with object syntax using `undefined` as default:**
```js
props: {
  propA: undefined,
  propB: undefined,
  propC: undefined,
}
```

**Also handle advanced array syntax with defaults:**
```js
// BEFORE:
props: ['simple', { key: 'withDefault', default: 42 }]

// AFTER:
props: {
  simple: undefined,
  withDefault: 42,
}
```

**Also handle empty arrays:**
```js
// BEFORE:
props: []

// AFTER:
props: {}
```

> **Important:** Prop casting (e.g., `{ key: 'x', cast: Number }`) has been removed in v2. Remove any cast definitions.

---

### Step 3: Replace `effects` with dedicated attributes (`rounded`, `border`, `shadow`)

Search all template strings for `:effects="[$shader(` patterns.

#### 3a: Rounded corners

```xml
<!-- BEFORE -->
<Element :effects="[$shader('radius', {radius: 12})]" />

<!-- AFTER — uniform corners -->
<Element rounded="12" />

<!-- AFTER — per-corner [topLeft, topRight, bottomRight, bottomLeft] (new in v2!) -->
<Element rounded="[10, 0, 0, 10]" />
```

For reactive radius values:
```xml
<!-- BEFORE -->
<Element :effects="[$shader('radius', {radius: $myRadius})]" />

<!-- AFTER -->
<Element :rounded="$myRadius" />
```

> **Note:** Per-corner rounding is a new v2 feature with no v1 equivalent. It takes an array of 4 values: `[topLeft, topRight, bottomRight, bottomLeft]`.

#### 3b: Borders

```xml
<!-- BEFORE -->
<Element :effects="[$shader('border', {width: 3, color: '#ffffff'})]" />

<!-- AFTER — uniform border -->
<Element border="{w: 3, color: '#ffffff'}" />

<!-- AFTER — per-side borders (new in v2!) -->
<Element border="{top: 2, bottom: 2, color: '#ffffff'}" />
```

> **Note:** `width` in border config becomes `w` in v2. Per-side borders (`top`, `bottom`, `left`, `right`) are a new v2 feature.

For reactive borders:
```xml
<!-- BEFORE -->
<Element :effects="[$shader('border', {width: $bw, color: $bc})]" />

<!-- AFTER -->
<Element :border="{w: $bw, color: $bc}" />
```

#### 3c: Combined effects

When an element has multiple effects (e.g., radius + border), split them into separate attributes:

```xml
<!-- BEFORE -->
<Element :effects="[$shader('radius', {radius: 8}), $shader('border', {width: 2, color: '#fff'})]" />

<!-- AFTER -->
<Element rounded="8" border="{w: 2, color: '#fff'}" />
```

#### 3d: Shadow (if present)

```xml
<!-- BEFORE -->
<Element :effects="[$shader('shadow', {color: '#00000080', blur: 16, offsetX: 4, offsetY: 4})]" />

<!-- AFTER -->
<Element shadow="{color: '#00000080', blur: 16, x: 4, y: 4}" />
```

> Shadow also supports `spread` property.

---

### Step 4: Rename built-in variables (add `$` prefix)

#### 4a: In JavaScript code

Search and replace in all `.js`/`.ts` files:

| Before (v1) | After (v2) |
|-------------|------------|
| `this.parent` | `this.$parent` |
| `this.hasFocus` | `this.$hasFocus` |
| `this.isHovered` | `this.$isHovered` |
| `this.focus(` | `this.$focus(` |
| `this.select(` | `this.$select(` |
| `this.trigger(` | `this.$trigger(` |

> **Be careful:** Only replace these when they refer to Blits built-ins, not user-defined state/methods with the same name. Check the component's `state()`, `methods`, and `computed` to avoid false positives. For example, if a component has `state() { return { parent: 'something' } }`, do NOT rename `this.parent`.

> **Note:** Built-in variables (`$parent`, `$hasFocus`, `$isHovered`) are now **read-only** in v2 type definitions. If any code attempts to assign to these (e.g., `this.hasFocus = true`), it will fail. Search for assignments to these variables and remove them — focus state should only be changed via `this.$focus()` / `this.$parent.$focus()`.

#### 4b: In template strings

| Before (v1) | After (v2) |
|-------------|------------|
| `$hasFocus` | `$$hasFocus` |
| `$isHovered` | `$$isHovered` |
| `$parent` (as built-in ref) | `$$parent` |

> In templates, built-in variables use double `$$` prefix to distinguish from user state/props (single `$`).

---

### Step 5: Replace `width`/`height` aliases with `w`/`h`

Search all template strings for `width=` and `height=` attributes on Elements.

```xml
<!-- BEFORE -->
<Element width="400" height="300" />

<!-- AFTER -->
<Element w="400" h="300" />
```

Also handle reactive versions:
```xml
<!-- BEFORE -->
<Element :width="$myWidth" :height="$myHeight" />

<!-- AFTER -->
<Element :w="$myWidth" :h="$myHeight" />
```

---

### Step 6: Replace `wordwrap` with `maxwidth`

```xml
<!-- BEFORE -->
<Text content="Hello" wordwrap="400" />

<!-- AFTER -->
<Text content="Hello" maxwidth="400" />
```

---

### Step 7: Check for prop/attribute name collisions

In v2, known element attributes (`x`, `y`, `w`, `h`, `color`, `src`, `alpha`, `rotation`, `scale`, `mount`, `pivot`) are **always applied to the component holder element**, even when also defined as a prop.

Scan all components for props named: `x`, `y`, `w`, `h`, `color`, `src`, `alpha`, `rotation`, `scale`, `mount`, `pivot`, `width`, `height`.

If found, suggest renaming them:
- `color` → `bgColor` or `cardColor`
- `src` → `img` or `imageSrc`
- `x` → `posX`, etc.

**Do not auto-rename these** — present the list to the user and let them decide on naming, since this affects the component's public API.

---

### Step 8: Update key mapping (if applicable)

**Important context:** Blits v2 now uses **only `KeyboardEvent.keyCode`** (not `KeyboardEvent.key`). This also fixes an issue where keycode 8 (backspace) could accidentally trigger the back button handler.

Search for `input:` sections and check for single-letter or single-number method names:

```js
// These no longer auto-map in v2:
input: {
  a() { /* ... */ },  // Won't work without keymap
  1() { /* ... */ },  // Won't work without keymap
}
```

If found, inform the user they need to either:
1. Add explicit keymaps in `Blits.Launch()` settings using `keyCode` values
2. Use the `any(e)` catch-all handler

Also check if the project has a custom `keymap` in `Blits.Launch()` settings that uses `KeyboardEvent.key` strings — these must be converted to `keyCode` numbers:

```js
// BEFORE (v1 — mixed key/keyCode):
keymap: { 'Backspace': 'back', 'Enter': 'enter' }

// AFTER (v2 — keyCode only):
keymap: { 8: 'back', 13: 'enter' }
```

---

### Step 9: Update custom shaders (if applicable)

Renderer v3 introduces a new shader registration system. If the project registers custom shaders, the API has changed.

Search for any custom shader registration patterns like `Blits.Shader()` or direct renderer shader imports.

If found, inform the user that:
- The dynamic shader stitching system has been replaced with **pre-baked shader combinations**
- Custom shaders need to be re-registered using the new Renderer v3 API
- This is an advanced change that may require consulting the [Renderer v3 docs](https://lightningjs.io/v3-docs/)

> **Note:** Most apps only use built-in shaders (radius, border, shadow) which are already handled by Step 3. This step only applies to apps with custom WebGL shaders.

---

### Step 10: Install dependencies

After all file changes are complete, run:

```bash
cd [project-path] && npm install
```

---

### Step 11: Verify the build

Run the project's build command:

```bash
cd [project-path] && npm run build
```

If the build fails, analyze the error and fix remaining migration issues.

---

## Post-Migration Report

After all steps, output a summary table:

```
## Migration Complete ✓

| Step | Files Changed | Status |
|------|--------------|--------|
| Dependencies | package.json | ✓ |
| Props syntax | [list files] | ✓ |
| Effects → attributes | [list files] | ✓ |
| Built-in variables | [list files] | ✓/skipped |
| width/height → w/h | [list files] | ✓/skipped |
| wordwrap → maxwidth | [list files] | ✓/skipped |
| Prop collisions | [list files] | ✓/review needed |
| Key mapping (keyCode only) | [list files] | ✓/skipped |
| Custom shaders | [list files] | ✓/skipped |

### Breaking changes to review manually:
- [any prop/attribute collisions found]
- [any key mapping issues found]
- [any custom shader registrations found]
- [any assignments to built-in read-only variables]
```
