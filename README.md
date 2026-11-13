# deepseek-harness-plugin-arabic-locale

Arabic (right-to-left) language pack for the **DeepSeek Harness** Web UI.

Adds **العربية** to *Settings → General → Language*, translates the interface, and
mirrors the document to `dir="rtl"` while Arabic is active.

[العربية](README.ar.md) · English

---

## What it does

| Area | Behavior |
| --- | --- |
| Language list | Registers `ar` with the label **العربية**, falling back to English |
| Interface text | Arabic dictionaries for every namespace DSH ships (see coverage below) |
| Document direction | `<html dir="rtl">` while Arabic is selected, restored when another language is chosen |
| Fallback | Per key: a key this pack omits renders in English — never a raw key or a blank label |

### Coverage

Measured against every package's own `en` dictionary — 44 namespaces, 2,505 keys:

| | |
| --- | --- |
| English strings in the client | **2,505** |
| Translated here | **1,849** (74%) across **35** namespaces |
| Not yet translated | `trajectory` (198), `schedule.manager` (155), `deliverables` (57), `subagent` (47), `settings.webSearch` (43), `settings.subagent` (35), `shortcuts` (32), `open-in-app` (29), `workflowRun` (18), `settings.shell` (14), `settings.agentLoop` (12), `slash.menu` (9), `shortcuts.layout` (1) — each falls back to English per key |
| Deliberately untranslated | the six `/command` tokens (`goal`, `plan`, `feedback`, `compact`, `permission`, `export`) — they are text the user types after `/`, not labels |

Covered surfaces: conversation transcript, tool rows and detail fields, composer,
queue, attachments, to-dos, sidebar and session list, workspace menus, settings
(General, Models, Account, Permission, Language, Built-in plugins), files panel,
document preview, right sidebar, question cards, feedback dialog, goal chip,
plan, skills, `@`-mention menu, automation tasks, background jobs, agent-preset
picker, plugin manager, and plugin market.

### Verified

- 2,069 client tests in `@deepseek-ai/dsh-client-ui-primitives` pass with the
  companion RTL rendering change.
- The module is exercised by a loader shim: it registers the language, 35
  dictionaries, and flips `dir` to `rtl` on `ar` and back on any other locale.
- Coverage is compared programmatically against every package's `en` keys; there
  are no keys this pack defines that the host does not own.

**Not verified:** the rendered page. The author could not drive a browser in the
environment where this pack was built, so the visual result — including how the
existing left-to-right chrome behaves once the document is mirrored — needs a
human check. Please open an issue with a screenshot if something looks off.

---

## Install

### From npm (after publishing)

```bash
dsh plugin add deepseek-harness-plugin-arabic-locale
```

or in the app: **Plugins → Add plugin** → `deepseek-harness-plugin-arabic-locale`.

### From a GitHub repository

```bash
dsh plugin add https://github.com/<you>/deepseek-harness-plugin-arabic-locale
```

### From a local directory

1. Download or clone this repository.
2. In DSH: **Plugins → Add plugin** → the repository directory, or
   ```bash
   dsh plugin add /absolute/path/to/deepseek-harness-plugin-arabic-locale
   ```
3. Restart DSH if the plugin list asks for it.

### Use it

**Settings → General → Language → العربية**, then the interface reloads in
Arabic. Choosing any other language restores the previous layout direction.

---

## The companion core patch (RTL rendering)

Translation alone is not enough for Arabic: DSH's Markdown renderer, composer,
and message bubbles are left-to-right only. This repository ships that fix as a
patch:

```
patches/dsh-core-rtl.patch
```

It changes five files under `packages/client/**`:

| Change | Why |
| --- | --- |
| `dir="auto"` on paragraphs, headings, quotes, lists, table cells | Each block follows its own first strong character: Arabic right, English left, in the same message |
| Logical CSS (`padding-inline-start`, `border-inline-start`, …) | List markers, quote rules, and table padding move to the start side instead of staying on the left |
| `pre`/`code` forced `direction: ltr; unicode-bidi: isolate` | Code stays readable inside Arabic prose and does not reorder the sentence |
| `line-height: 1.85` for `:dir(rtl)` blocks, taller user bubble | Arabic script (harakat above, deeper connected baseline) needs more line box than Latin, otherwise lines collide |
| `dir="auto"` on the composer and the user bubble | Arabic typing and messages start at the right edge |

### Apply it

The upstream project states in `CONTRIBUTING.md` that it cannot accept external
pull requests at the moment, so this ships as a patch to apply to your own
checkout:

```bash
git clone https://github.com/deepseek-ai/deepseek-harness
cd deepseek-harness
git apply /path/to/patches/dsh-core-rtl.patch

# The change adds dir="auto" to rendered DOM, so the DOM-parity fixtures move with it:
pnpm exec vitest run -u packages/client/ui-primitives/tests/markdown-dom-parity.client.spec.tsx
pnpm run build:lib:client
```

Refresh the app afterwards. The patch is written against `0.2.2-rc.3`; if your
checkout is newer and does not apply cleanly, apply the five source edits by hand
— the diff is small and self-contained.

---

## Extending the translations

All dictionaries live in `client.js` under one `dictionaries` object keyed by
namespace. To translate another surface:

1. Find the namespace's English keys in the owning package's `src/client/locales.ts`.
2. Add or extend that key's entry in `dictionaries`.
3. Keep placeholders (`{name}`, `{count}`, `{time}`, …) verbatim.

There is no build step: `client.js` is the shipped browser module.

## Compatibility

- Built and verified against **DSH 0.2.2-rc.3**.
- Registers through the public `ctx.locale.addLanguage` / `ctx.locale.register`
  API, so a DSH release that changes locale internals may require an update.
- No dependencies, no install scripts.

## License

MIT — see [LICENSE](LICENSE).
