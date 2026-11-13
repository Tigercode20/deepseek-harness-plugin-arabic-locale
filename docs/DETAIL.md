# Arabic UI language

Adds **العربية** to the interface language list and supplies Arabic dictionaries for the surfaces translated so far.

## Selecting it

Settings → General → **Language** → **العربية**. The choice is stored with the profile and survives a restart.

## What is translated

| Namespace | Surface |
| --- | --- |
| `common` | Shared verbs and labels: copy, retry, cancel, load states, markdown chrome |
| `chat` | Transcript process labels, statistics, errors, turn usage |
| `conversation` | Composer, attachments, queue, to-dos, tool rows, detail fields, terminal |
| `workspace` | Session list, grouping and ordering, archive/pin, status rows |
| `settings` | Settings dialog shell, desktop updates, connection state |
| `model` | Model picker trigger, menu, search, load failures |
| `command` | Slash-command menu and per-command copy |
| `plan`, `skill`, `reference` | Plan chip and preview, skill rows, @-mention menu |
| `question`, `feedback`, `goal` | Question cards, rating dialog, goal chip |
| `sidebarFiles`, `sidebarRight`, `fileActions` | Files panel, right sidebar tabs, file context menu |
| `settings.models`, `settings.account`, `settings.permission` | Models page, account and sign-in, permission presets |
| `sidebarDocumentPreview`, `agent-team`, `entryCustomization` | Document preview, task board, entry pinning |
| `pluginManager`, `pluginMarket`, `settings.pluginInventory` | Plugin manager, plugin market, built-in plugin list |
| `sidebar` | New-session action, sidebar toggles, panel headings |
| `approval` | Permission prompt wording and its buttons |
| `settings.locale` | The Language row title itself |

Selecting Arabic also mirrors the document (`<html dir="rtl">`), so the sidebar
moves to the right and directional controls flip; choosing another language
restores the original direction.

## Coverage

1,787 of the interface's 1,793 English strings (99.7%), measured namespace by
namespace against each package's `en` dictionary. The six remaining keys are
the `/command` tokens (`goal`, `plan`, `feedback`, `compact`, `permission`,
`export`): they are text the user types after `/`, so they stay English.

## Fallback behavior

Each namespace falls back to English **per key**. A screen that is only partly translated shows Arabic where this pack supplies a key and English everywhere else — it never shows a raw key or an empty label. Adding a namespace later only means extending the `dictionaries` map in `client.js`.

## Limits

- The app chrome becomes Arabic; the conversation content direction comes from the core right-to-left rendering, independent of the selected language.
- Placeholders such as `{name}` and `{total}` are preserved verbatim and are filled by the caller.
