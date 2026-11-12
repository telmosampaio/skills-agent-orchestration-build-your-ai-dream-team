# Project Pulse implementation plan

## Summary and current context

Project Pulse is a lightweight, static contributor dashboard for understanding active AI projects, their owners, statuses, recent activity, priorities, and risks. The repository currently has no files in `app/`, so implementation will create the dashboard from the project brief rather than extend an existing UI. No external framework or runtime dependency is needed; use semantic HTML, CSS, browser-native `fetch`, and JSON.

The result must be runnable through VS Code's **Run Project Pulse Dashboard** launch configuration. It must serve `app/` as the working directory and open `index.html`, rather than presenting a directory listing.

## Deliverable and file ownership

| File | Owner | Implementation assignment |
| --- | --- | --- |
| `app/project-data.json` | Coder | Create strict JSON containing a top-level `projects` array. Every project object must have non-empty `name`, `owner`, `status`, `recentActivity`, and `priority` strings. Include representative active projects and values that exercise the supported status and priority styles. |
| `app/index.html` | Coder | Create the semantic static dashboard shell: page title, dashboard header, explanatory empty/loading/error regions, and a project-card container. Link `styles.css`, fetch `project-data.json`, validate the expected data shape, render text with safe DOM APIs, and give malformed/unavailable data a visible, actionable error message. |
| `app/styles.css` | Designer | Create the visual system and responsive layout for the HTML structure. Include deterministic `.dashboard` and `.project-card` hooks, card grid, status badges, priority/risk treatment, visible focus states, and narrow-screen behavior. |
| `.vscode/launch.json` | Coder | Create strict comment-free JSON with a deterministic **Run Project Pulse Dashboard** configuration. Start a static server with `cwd` set to `${workspaceFolder}/app` and open its `index.html` URL. Select a fixed available port and ensure the launch type/command is supported by the target Codespace/VS Code environment. |

The Orchestrator owns task dispatch, integration order, file-scope enforcement, and final acceptance. The Designer must not edit Coder-owned files; the Coder must not edit `app/styles.css`.

## Design and behavior decisions

- Use project cards as the primary scan unit. Each card displays project name, owner, status badge, priority badge or label, recent activity, and a concise contributor-friendly summary derived from the available project fields.
- Use a responsive CSS grid: multiple columns when the viewport permits, one readable column on small screens. Preserve sufficient contrast, generous spacing, rounded card surfaces, restrained shadows, and clear visual hierarchy.
- Represent known status and priority values with predictable CSS modifier classes; unknown values retain a neutral, readable fallback rather than failing to render.
- Keep all project content in `project-data.json`. The HTML must not duplicate project records or hard-code a count.
- Because `fetch()` cannot reliably load JSON from `file://`, dashboard behavior is validated through the launch configuration/static server, not by opening the file directly from the filesystem.

## Phases and execution order

### Phase 1 — Contract and task setup

1. **Orchestrator:** distribute this plan and lock the file ownership table above.
2. **Coder:** define the JSON contract while creating `app/project-data.json`: a top-level object with `projects`, where each item includes the five required fields.
3. **Designer:** review the fixed content contract and define the card information hierarchy, responsive breakpoints, color roles, focus treatment, and status/priority visual mapping.

**Dependency:** the Designer's content assumptions depend on the field names from the brief, which are already fixed. The Coder's final renderer depends on the same contract. No implementation file needs to be changed before either assignment begins.

### Phase 2 — Parallel asset implementation

4. **Coder (data):** implement `app/project-data.json` with valid, representative project records.
5. **Designer (styling):** implement `app/styles.css` against the agreed semantic class hooks: `.dashboard`, `.project-grid`, `.project-card`, status/priority badge classes, and state-message classes.
6. **Coder (runtime):** implement `app/index.html`, including the semantic shell and deterministic JSON loading/rendering logic.
7. **Coder (run support):** implement `.vscode/launch.json` with the required dashboard launch behavior.

**Can run in parallel:** steps 4, 5, and 7 edit disjoint files and have no runtime dependency on one another. Step 6 can start in parallel using the established JSON contract and agreed CSS hooks, but its final browser check must wait for steps 4 and 5.

**Reasoning:** parallel work shortens delivery without merge conflicts because each specialist has exclusive file ownership. The only shared interfaces are pre-agreed field names and class names, not shared files.

### Phase 3 — Integration and correction

8. **Orchestrator:** integrate the completed files and check that class hooks emitted by `index.html` match selectors in `styles.css`, and that rendered fields exactly match the JSON contract.
9. **Coder:** correct any data-loading, rendering, configuration, or semantic HTML defect found during integration. The Designer corrects only CSS issues within `app/styles.css`.
10. **Orchestrator:** run final acceptance checks and collect the specialists' validation results.

**Must be sequential:** phase 3 starts only after all phase 2 outputs exist. Integration needs the actual JSON, HTML, CSS, and launch configuration; testing an incomplete set can incorrectly report failures (for example, failed fetches before JSON exists or unstyled cards before CSS exists). Corrections follow the reported integration findings to avoid competing edits.

## Detailed responsibilities

### Designer

- Own `app/styles.css` exclusively.
- Establish hierarchy: page title and dashboard context first, project name second, then status/priority and metadata.
- Build polished project cards, visible status badges, clear priority/risk emphasis, readable spacing, and responsive card layout.
- Use semantic visual states that do not rely on color alone; maintain keyboard-visible focus styles and legible contrast.
- Report the class hooks expected from markup and validate at desktop and narrow mobile widths.

### Coder

- Own `app/index.html`, `app/project-data.json`, and `.vscode/launch.json` exclusively.
- Use strict JSON only: no comments, trailing commas, or unquoted keys in either JSON file.
- Implement small, explicit browser logic: load `project-data.json`, verify `projects` is an array and fields are usable strings, render one card per record, and show an explicit visible state for loading, empty data, invalid schema, and fetch failure.
- Avoid unsafe `innerHTML` for project-provided content; create DOM elements and assign `textContent`.
- Configure launching from `${workspaceFolder}/app` and open the server URL ending in `/index.html`.
- Report commands/browser checks used and any environment limitation, without editing the Designer's CSS.

## Dependencies and handoffs

| Consumer task | Depends on | Why |
| --- | --- | --- |
| HTML rendering in `app/index.html` | JSON contract and representative values in `app/project-data.json` | The renderer must read the exact top-level array and fields; examples expose style and empty/error behavior. |
| CSS modifier rules in `app/styles.css` | Agreed markup hooks and likely `status`/`priority` values | Selectors must match the generated card and badge class names. |
| Browser dashboard check | `index.html`, `styles.css`, and `project-data.json` | A successful visual result requires content, rendering behavior, and presentation together. |
| Launch check | `.vscode/launch.json` plus all `app/` files | The server must use `app/` as its root and load the completed dashboard at `index.html`. |
| Final acceptance | Browser and launch checks | Static file inspection alone cannot prove JSON loading or the configured start path. |

## Validation expectations

1. **JSON validity:** parse `app/project-data.json` and `.vscode/launch.json` with a JSON parser. Confirm `project-data.json` has only the required top-level `projects` array contract and each project includes all five required fields. Confirm the launch configuration is strict JSON with no comments.
2. **Static review:** confirm `index.html` links `styles.css`, requests `project-data.json`, includes `.dashboard` and a project-card rendering path, and does not hard-code project data. Confirm CSS contains responsive behavior, card styling, badge styling, and keyboard focus visibility.
3. **Browser/dashboard behavior:** launch a local static server from `app/`; navigate directly to `index.html`. Verify the page is a polished Project Pulse dashboard, all JSON projects render as visible cards, owner/status/recent activity/priority are readable, narrow viewport layout remains usable, and there is no console error. Temporarily test unavailable or invalid JSON only if practical, then restore valid data; verify the user sees an informative error state rather than a blank dashboard.
4. **VS Code launch behavior:** select **Run Project Pulse Dashboard**. Verify its `cwd` is `${workspaceFolder}/app`, it starts a server deterministically, the opened URL targets `index.html`, and the first browser view is the dashboard rather than a directory listing.
5. **Scope and source control:** verify only the four assigned implementation files were created or changed during implementation. Do not stage, commit, or push.

## Completion criteria

The work is complete when the four assigned files exist, their interfaces agree, both JSON files parse, the VS Code launch configuration opens the dashboard directly, and the dashboard renders responsive, accessible project cards from the JSON data with explicit loading and failure behavior.
