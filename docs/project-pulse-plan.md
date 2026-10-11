# Mona's Project Pulse implementation plan

## Summary and repository evidence

Build a small, polished static contributor dashboard showing active projects,
owners, status, recent activity, priority/risk, and a short summary. This document
is a plan only; dashboard implementation is a later task.

- `.github/project-pulse-brief.md` specifies the three app files, a top-level
  `projects` array, five required project fields, and a VS Code launch configuration.
- `.github/steps/3-step.md` requires the exact title `Project Pulse`, rendered
  `project-card` elements, `.dashboard` and `.project-card` styling, rounded cards,
  shadows, and the launch command `python3 -m http.server 5500` with
  `serverReadyAction` opening `http://localhost:%s/index.html`.
- `.github/agents/planner.agent.md` requires dependency-aware planning, explicit
  ownership, edge cases, and validation without implementation.
- `.github/agents/designer.agent.md` assigns visual hierarchy, accessibility,
  responsive behavior, and polished styling to Designer.
- `.github/agents/coder.agent.md` assigns deterministic implementation and explicitly
  permits assigned launch support; launch JSON must have no comments and use
  `cwd: "${workspaceFolder}/app"`.
- `.github/agents/orchestrator.agent.md` permits parallel tasks only when both file
  scopes and data dependencies are independent; integration follows implementation.
- `docs/agent-team.md` records the intended team. Planner and Orchestrator target
  Claude Opus 4.7, which is unavailable for the attempted Planner launch. This
  fallback plan does not change agent definitions or promise model availability.
- The repository currently has no `app/`, `.vscode/launch.json`, application
  dependency manifest, or dashboard tests. `.vscode/tasks.json` already opens the
  exercise terminal; preserve it. `.devcontainer/devcontainer.json` defines a
  universal Codespace image and does not explicitly add a launch/debug extension.
- `.github/workflows/2-step.yml` checks plan keywords and paths. Step 3 checks
  file/phrase presence and JSON syntax, not a running dashboard.
  `scripts/validate-exercise.sh` validates the exercise template and intentionally
  rejects tracked learner answer files; it is not an application acceptance suite.

## Scope and exclusive file ownership

| File | Sole writer | Deliverable |
| --- | --- | --- |
| `docs/project-pulse-plan.md` | Planner fallback (this task) | Saved implementation plan; no app changes |
| `app/styles.css` | Designer | Responsive dashboard/card layout, typography, badges, priority treatments, accessible states |
| `app/index.html` | Coder | Semantic shell, exact title, stylesheet reference, inline dependency-free rendering logic that loads `project-data.json` |
| `app/project-data.json` | Coder | Strict JSON fixture with realistic, clearly fictional project records |
| `.vscode/launch.json` | Coder | Strict JSON configuration named **Run Project Pulse Dashboard**, serving `app/` and opening `index.html` |

Designer owns CSS implementation as well as UX decisions; Coder consumes those
decisions and implements markup/data/behavior. Both may read each other's files,
but neither may edit the other's assigned files. Designer sends markup requests
to Coder rather than editing HTML. Coder sends styling requests to Designer rather
than editing CSS. Orchestrator assigns tasks, freezes contracts, coordinates
handoffs, and reviews without editing implementation files. No additional JS,
tooling, tasks, extensions, dependency files, or agent changes are authorized by
this plan; any necessary expansion requires an explicit later scope assignment.
Agents must not stage, commit, or push.

## Ordered tasks and dependencies

| ID / phase | Responsible party and write scope | Work and completion gate | Prerequisites |
| --- | --- | --- | --- |
| A — Contract | Orchestrator coordinates Designer and Coder; discussion only | Agree the schema, CSS hooks, page hierarchy, status/priority vocabulary, loading/empty/error presentation, and launch approach described below. Resolve any runtime support blocker before coding. | Saved plan |
| B — Visual implementation | Designer; `app/styles.css` only | Implement polished responsive styles against the frozen contract; hand back design rationale and browser review expectations. | A |
| C — App implementation | Coder; `app/index.html`, `app/project-data.json`, `.vscode/launch.json` only | Create fixtures, render data with semantic markup and safe text insertion, handle failures, and configure the required preview. Validate JSON, schema, loading, and launch readiness. | A; within C, freeze fixture schema before renderer completion |
| D — Integrated review | Orchestrator coordinates read-only review by Designer and Coder | Run the actual dashboard, verify all acceptance checks, and report defects to the sole owner of each affected file. | B and C |
| E — Corrections and recheck | Designer edits CSS only; Coder edits assigned app/launch files only | Correct review findings and repeat affected checks plus an integrated smoke test. | D; repeat D after corrections |

**Parallel work:** B and C may run concurrently after A because their files are
disjoint and the schema/markup interface is already frozen. Within C, fixture
preparation and launch configuration are independent deliverables, but keep one
Coder task to avoid concurrent writers. Independent CSS-only and Coder-only
corrections may run concurrently if they do not change the interface.

**Sequential work:** A precedes all implementation. Renderer completion depends
on the agreed data shape; final visual review depends on actual rendered HTML and
CSS. D waits for both B and C. Contract changes require an Orchestrator-coordinated
handoff before the dependent owner resumes; file separation alone does not make
those changes parallelizable. Do not call an unstyled HTML review final acceptance.

## Frozen starting contract

- Use a semantic page header and main dashboard with `.dashboard`, a visible
  `Project Pulse` heading and document title, and a project grid containing
  `.project-card` articles with project headings and labeled fields.
- Each record has nonempty string fields `name`, `owner`, `status`,
  `recentActivity`, and `priority`. Add `summary` as a nonempty string to satisfy
  the brief's contributor-friendly summary requirement.
- Start with at least three fictional examples spanning `Active`, `At risk`, and
  `Blocked`, with `High`, `Medium`, and `Low` priorities. These are proposed
  display vocabularies, not existing production data. Display every record;
  do not silently hide blocked projects or add filtering in the initial scope.
- Shared styling hooks: `.dashboard`, `.project-grid`, `.project-card`,
  `.status-badge`, `.priority-badge`, `.project-summary`, and `.dashboard-message`.
  Agree deterministic status/priority modifiers in A; use a neutral fallback for
  unknown values. Convey status and priority with readable text, not color alone.
- Load `./project-data.json` via HTTP; render records from JSON, not a separate
  hardcoded card list. Keep script inline in HTML to preserve the three-file app
  scope. Treat record values as text, never trusted HTML.
- Provide loading feedback, an explicit no-projects state for an empty array,
  and a contributor-readable error for HTTP failure, invalid JSON, or invalid
  required fields. Do not leave an apparently successful blank dashboard.
- Launch from `${workspaceFolder}/app` using `python3 -m http.server 5500`.
  The launch must use a VS Code-supported configuration type available in the
  environment, capture the server's port from its readiness output, and use
  `serverReadyAction` to open `http://localhost:%s/index.html`, not the directory
  root. Coder must confirm the chosen type supports these fields and that server
  output is observable; use unbuffered Python output if necessary. No assumed
  extension installation or edits to `.vscode/tasks.json`.

## Concrete validation expectations

These are future implementation acceptance checks, not claims of checks performed
on a dashboard during this planning task.

1. **Files and syntax:** All four assigned implementation files exist.
   Run `python3 -m json.tool app/project-data.json` and
   `python3 -m json.tool .vscode/launch.json`; both exit zero.
   Check that `projects` is an array and every fixture has all six agreed fields
   as nonempty strings. HTML references `styles.css` and `project-data.json`;
   CSS includes `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
2. **Runtime and launch:** Select **Run Project Pulse Dashboard** in VS Code.
   Confirm the server starts from `app/`, the browser opens
   `http://localhost:5500/index.html` (or its Codespace-forwarded equivalent),
   and HTML, CSS, and JSON load successfully. Check HTTP responses with
   `curl --fail http://localhost:5500/index.html` and
   `curl --fail http://localhost:5500/project-data.json` while the server runs.
   Confirm no directory listing, browser console errors, or missing assets.
   Stop the launched server cleanly after testing.
3. **Data-driven UI:** Visible card count equals fixture count. Every card shows
   its name, owner, status, recent activity, priority, and summary. Temporarily
   alter one fixture value and reload to prove JSON drives the visible result,
   then restore the fixture.
4. **Failure and edge cases:** Test an empty array, missing required field,
   malformed JSON, and failed data request; each produces the agreed visible
   state without uncaught exceptions. Test long names/owners, unknown status,
   and text containing HTML characters; cards remain readable and text is not
   executed as markup. Use reversible fixture changes and restore valid JSON
   before completion.
5. **Visual/accessibility:** Designer reviews actual rendering at approximately
   375px, 768px, and 1440px widths: no horizontal overflow, clipped content, or
   unreadable cards; clear spacing, hierarchy, status badges, and priority cues.
   Check heading order, visible field labels, keyboard access/focus for any
   controls or links, announced loading/error feedback, and text contrast
   (at least 4.5:1 for normal text and 3:1 for large text). Status must remain
   understandable without color. Avoid unnecessary motion.
6. **Integration/ownership:** Review changed paths and ensure only assigned files
   changed, no dependencies or secrets were added, and existing exercise terminal
   configuration remains intact. Orchestrator records what passed, failed, or
   could not be tested, including the exact launch name and remaining risks.
   Workflow keyword checks alone are insufficient evidence of working behavior.

## Assumptions, risks, and open questions

- Assume this is a dependency-free static prototype with fictional sample data,
  not a production API, authentication flow, or live activity feed. Real project
  names, update cadence, and authoritative data source remain unspecified.
- `summary` is an additive field inferred from the brief; risk is initially
  represented by status/priority rather than a separate numeric risk score.
  Confirm any alternative interpretation during A.
- Filtering, sorting, charts, detailed views, persistence, and dark-mode controls
  are not required for the initial deliverable.
- Python 3 availability, port 5500 availability, Codespace browser forwarding,
  and launch adapter/server-ready support must be verified by Coder. A working
  manual HTTP preview does not substitute for the required VS Code launch.
  If runtime support requires another file or extension, surface the blocker and
  obtain a scoped follow-up assignment; do not silently claim launch validation.
- Designer/Coder model availability is not established by their definition files.
  If a configured agent cannot run, assign its same bounded ownership to an
  available fallback rather than modifying model configuration or merging scopes.
- The later final handoff belongs to exercise Step 4; no handoff document or
  dashboard code is created in this planning task.
