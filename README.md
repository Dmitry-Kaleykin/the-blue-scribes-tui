# The Blue Scribes TUI

An interactive, scrollback-friendly terminal interface for
[The Blue Scribes](../the-blue-scribes). It uses Pi's terminal UI runtime for
differential rendering, fuzzy slash-command completion, input history, and
keyboard-driven selectors.

The TUI talks directly to the Scribes TypeScript services. It does not run a
web server, open a browser, or execute Scribes CLI subprocesses.

## Install

Keep the core and TUI repositories beside one another:

```text
Projects/
├── the-blue-scribes/
└── the-blue-scribes-tui/
```

Build and link both packages:

```sh
cd ../the-blue-scribes
npm install
npm run build
npm link

cd ../the-blue-scribes-tui
npm install
npm run build
npm link
```

Then launch the TUI from a source repository:

```sh
scribes-tui
```

The current directory is matched against the externally managed Scribes
project catalog. Running from any subdirectory selects the nearest indexed
parent project. When the directory is not indexed, `/index` uses its Git root
(or the current directory outside Git) as the new project root.

## Interaction

- Type a question and press `Enter` to search the active project.
- Type `/` to open fuzzy command completion.
- Use `Up` and `Down` to navigate commands, options, results, and history.
- Press `Enter` to select; `Tab` completes a command or argument.
- Press `Escape` to close a selector. During indexing it opens a cancellation
  confirmation.
- Press `Ctrl+C` once to clear the editor and twice to quit.

Search results are interactive: `Up` and `Down` select a result, `Enter`
expands its exact indexed content, `E` opens a project result at its source line
in `$VISUAL` or `$EDITOR`, and `Escape` returns focus to the editor.

## Commands

| Command | Behavior |
| --- | --- |
| `/index` | Indexes the current project. The first run selects a profile and preset; later runs reuse the project selection. There is no separate reindex operation. |
| `/project` | Fuzzy-selects a known project. `/project info` shows its state; `/project forget` removes only its managed index. |
| `/search` | Opens a guided query prompt. Plain text performs the same search directly. |
| `/profile` | Creates, selects, tests, edits, inspects, or removes OpenAI-compatible provider profiles. |
| `/preset` | Creates, selects, edits, inspects, or removes indexing presets. |
| `/builds` | Browses immutable build history and exact build metadata. |
| `/target` | Switches, renames, or removes named retrieval targets. |
| `/chunks <path>` | Shows the exact stored chunks for an indexed file. Paths autocomplete. |
| `/collection` | Creates collections; adds, tags, inspects, or removes sources; indexes collections with live progress; and searches them. |
| `/jobs` | Shows the current indexing phase and elapsed time. |
| `/mcp` | Prints read-only MCP configuration for the active project. |
| `/doctor` | Tests the active or selected provider profile. |
| `/settings` | Shows terminal interaction settings and shortcuts. |
| `/help` | Shows commands and keyboard shortcuts. |
| `/clear` | Clears the visible TUI transcript. |
| `/quit` | Exits, confirming first when indexing is active. |

## Indexing behavior

`/index` always means “create an up-to-date index for this project.” For an
existing project it uses that project's saved profile, preset, target, and
retention policy. The underlying indexing engine still reuses compatible
documents, chunks, and embeddings.

Index progress is rendered in a transient live region. It shows a phase-specific
progress bar, counters, and exactly one current path. Each path replaces the
previous path; processed filenames are not left in terminal scrollback. On
completion, the live region is replaced by one concise permanent summary.

The previous ready target remains searchable while a new index is being built.
The target advances atomically only after the build succeeds.

## State and privacy

The TUI never adds configuration files to indexed projects. Its project choices
are stored in:

```text
~/.blue-scribes/tui/project-preferences.json
```

Each preference selects one provider profile and one indexing preset. Each
immutable Scribes build continues to record its resolved configuration.

Set `BLUE_SCRIBES_TUI_HOME` to override the TUI state directory, which is useful
for tests or isolated environments. `OPENAI_COMPATIBLE_API_KEY` is passed through
when provider authentication is enabled. `LM_STUDIO_API_KEY` remains supported
as a deprecated fallback for existing setups.

## Development checks

```sh
npm run typecheck
npm test
npm run build
```
