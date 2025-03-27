---
description: Core rules (v3) governing AI operation via state machine, structured context, and optional self-review.
globs: "**/*"
alwaysApply: true
---

- **Core Principle: Adhere to Double-Agent System v3 State Machine**
    - Always operate based on the current state defined in `@.cursor/ai_double_system/ai_task_manager.md#currentState`.
    - Transition between states (`IDLE`, `PLANNING`, `PRE-CHECK`, `EXECUTING`, `POST-CHECK`, `SELF_REVIEW`, `UPDATING`) as defined below.
    - Prioritize reading/writing structured files: `@.cursor/ai_double_system/context/` (YAML, MD), `@.cursor/ai_double_system/indices/` (JSON - **expect valid array `[]` or `[{}, ...]`**), `@.cursor/ai_double_system/ai_project_roadmap.md`, `@.cursor/ai_double_system/ai_task_manager.md` (YAML Frontmatter). Parse/update rigorously. **Do not write comments into JSON files.**
    - Use terminal commands (`ls`, `grep`, `git diff`, `ctags` if available) for verification.
    - Log significant actions, decisions, errors, and learnings to `@.cursor/ai_double_system/context/learnings.log` with `[Timestamp] - LEVEL - Message`.
    - Ask for clarification using numbered options if requirements, context, or next steps are ambiguous. Default to `IDLE` state when blocked.

- **State: IDLE**
    - **Entry:** Awaiting new user prompt or resuming after user input. Task manager may be empty or hold results of last action.
    - **Action:**
        - If new user prompt received: Parse the goal. Transition to `PLANNING`.
        - If resuming after user clarification: Parse user input, update task manager/blockers accordingly. Transition to the state indicated by user or logical next step (often `PLANNING` or `PRE-CHECK`).
    - **Exit:** Transition to `PLANNING` or other state based on user input/clarification.

- **State: PLANNING**
    - **Entry:** Received new task goal or need to determine next step for existing goal.
    - **Action:**
        1.  **Initialize/Update Task Manager:**
            - If new task: Clear/reset `@.cursor/ai_double_system/ai_task_manager.md` (keep file structure). Populate `overallGoal`, `status: Planning`, `currentState: PLANNING`. Add timestamp. Link `roadmapId` if applicable.
            - If continuing task: Ensure `overallGoal` is set.
        2.  **Load Context Snippet:**
            - Read relevant sections from `@.cursor/ai_double_system/context/architecture.md`, `patterns.md`, `requirements.md`.
            - Query relevant JSON indices (`@.cursor/ai_double_system/indices/*.json`) based on keywords in the goal or affected files. **Parse JSON carefully, expecting an array.**
            - Check recent relevant entries in `@.cursor/ai_double_system/context/learnings.log`.
            - Store a concise summary or key points in `ai_task_manager.md#contextSnippet`.
        3.  **Break Down Task / Determine Next Step:**
            - Based on `overallGoal`, completed steps, and `contextSnippet`, identify the next logical implementation step.
            - If task is large, outline first 2-3 steps under `## Human Readable Notes`.
        4.  **Update Task Manager:** Populate `currentFocus` (Action, Context/Rules, Plan) for the determined step. List `activeFiles` likely involved. Set `currentState: PLANNING`.
        5.  **Present Plan & Confirm (First Step Only):** If this is the first step of a new task, inform user: "Starting task '[Goal]'. First step: [currentFocus.Action]. I will use context: [brief summary of snippet/rules]. Active files: [activeFiles]. Confirm? (Yes/No/More Info)". Transition to `IDLE` and await confirmation. If not first step, proceed directly.
    - **Exit:**
        - If user confirmation needed: Transition to `IDLE`.
        - If plan ready: Update `currentState: PRE-CHECK` in Task Manager. Transition to `PRE-CHECK`.

- **State: PRE-CHECK**
    - **Entry:** Plan for next step defined in `currentFocus`.
    - **Action:**
        1.  **Assess Significance:** Determine if step is 'Significant' (needs full verification) or 'Minor' (brief check ok). Usually based on whether it creates/modifies reusable code, involves indices, or complex logic.
        2.  **Query Context/Indices:** Check `contextSnippet` and relevant JSON indices (`indices/*.json`) for existing entities (functions, components, etc.) mentioned in `currentFocus.Plan`. **Parse JSON array.**
        3.  **Verify (Full Check Only):**
            - If index suggests existing entity at `@path/to/file`:
                - Run `ls @path/to/file` to check existence.
                - Run `grep "[entityName]" @path/to/file` or query `tags.json` (if available and reliable) to verify basic signature/presence.
                - If verification fails: Update entity's `confidence` score in JSON index (lower it), log failure to `learnings.log`, add Blocker in Task Manager, set `currentState: IDLE`, transition to `IDLE`.
                - If verification passes: Optionally update `lastVerified` timestamp in JSON index, potentially increase confidence.
        4.  **Update Task Manager:** Add findings/verification results to `currentFocus.Context/Rules`. Ensure `Plan` reflects reuse if verified entity found. Set `currentState: PRE-CHECK`.
        5.  **Ask if Unsure:** If context conflicts, verification fails unexpectedly, or plan is unclear, formulate numbered options under `blockers`, set `currentState: IDLE`, transition to `IDLE`.
    - **Exit:**
        - If blocked/needs input: Transition to `IDLE`.
        - If check complete and clear: Update `currentState: EXECUTING` in Task Manager. Transition to `EXECUTING`.

- **State: EXECUTING**
    - **Entry:** Pre-check complete, plan is clear.
    - **Action:**
        1.  Implement the code change/action described in `currentFocus.Plan`, adhering to `currentFocus.Context/Rules`.
        2.  Modify files listed in `activeFiles` or others as necessary.
        3.  Log significant sub-steps or decisions briefly to `learnings.log` (optional, depends on verbosity).
    - **Exit:**
        - If major error occurs during execution: Report error, add Blocker in Task Manager, set `currentState: IDLE`, transition to `IDLE`.
        - If execution seems complete: Update `currentState: POST-CHECK` in Task Manager. Transition to `POST-CHECK`.

- **State: POST-CHECK**
    - **Entry:** Code change/action completed.
    - **Action:**
        1.  **Review Creation vs Plan:** Briefly analyze generated/modified code against `currentFocus.Plan` and `Context/Rules`.
        2.  **Basic Self-Correction:** Attempt immediate fixes for minor deviations (syntax errors, forgotten imports, obvious rule violations). Log correction briefly.
        3.  **Compliance & Test Check:**
            - Briefly verify against rules mentioned in Pre-Check or `patterns.md`. Add `// TODO: [Issue]` comments for minor persistent deviations.
            - **Run Tests (If Applicable):** Check `architecture.md` or `patterns.md` for `Key Test Command`. If exists and relevant, run it via terminal. Report Pass/Fail. If Fail, add failure details to `blockers`.
        4.  **Update Task Manager:** Record outcome (Success/Partial/Fail) of the step. Add basic test results. Set `currentState: POST-CHECK`.
    - **Exit:**
        - If tests failed or major issues found requiring user input: Add Blocker, set `currentState: IDLE`, transition to `IDLE`.
        - If basic checks pass: Read `selfReviewLevel` from `@.cursor/ai_double_system/context/double.yaml`.
            - If `Off`: Transition to `UPDATING`.
            - If `Basic` or `Full`: Update `currentState: SELF_REVIEW` in Task Manager. Transition to `SELF_REVIEW`.

- **State: SELF_REVIEW**
    - **Entry:** Post-check complete, self-review enabled (`Basic` or `Full`).
    - **Action:**
        1.  **Identify Scope:** Use `activeFiles` from Task Manager or determine files changed in last `EXECUTING` step.
        2.  **Run Git Diff:** Execute `git diff HEAD -- path/to/file1 path/to/file2 ...`. Handle errors (no diff, git error). If no diff/error, log and consider skipping to `UPDATING`.
        3.  **Gather Review Context:** Get `git diff` output, `overallGoal`, completed step details (`currentFocus`), `contextSnippet`/rules from Task Manager.
        4.  **Formulate & Execute Review Prompt:** Use the strict JSON output format prompt (see previous response) asking for `confidenceScore`, `summary`, `issuesFound`, `suggestionsForImprovement` based on correctness, rule adherence, quality, completeness.
        5.  **Parse Review Output:** Parse the JSON response. Handle errors (log, add Blocker, set `currentState: IDLE`, transition `IDLE`).
        6.  **Update Task Manager:** Add the parsed review results under `selfReviewFindings`. Set `currentState: SELF_REVIEW`.
        7.  **Handle Review Outcome:**
            - **If `confidenceScore` == 1.0:** Log success. Transition to `UPDATING`.
            - **If `confidenceScore` < 1.0:**
                - Read `selfReviewLevel` from `double.yaml`.
                - **If `Basic`:** Report score/findings to user. Add Blocker asking user how to proceed. Set `currentState: IDLE`. Transition to `IDLE`.
                - **If `Full`:** Report score/findings. Add Blocker asking user: "Propose implementing fixes: [summary of suggestions]. Proceed? (Yes/No)". Set `currentState: IDLE`. Transition to `IDLE` (awaiting user response).
                    - *(User response 'Yes' in IDLE state will trigger transition back to PLANNING/EXECUTING to apply fixes)*.
                    - *(User response 'No' in IDLE state acknowledges review, user decides next step)*.
    - **Exit:** Transition to `UPDATING` (if score 1.0), `IDLE` (if score < 1.0 or error), or potentially back to `PLANNING`/`EXECUTING` via `IDLE` if user confirms applying fixes.

- **State: UPDATING**
    - **Entry:** Step completed successfully (and passed self-review if enabled).
    - **Action:**
        1.  **Update Indices:** If a new reusable entity was created or significantly modified, **parse the relevant JSON index file (expecting array)**, add/update the entry (generate ID, path, description, set `lastVerified`, `confidence`), **write the updated valid JSON array back carefully.** Log action.
        2.  **Update Context/Patterns:** If a significant new pattern was established or learned, add note to `patterns.md` or `learnings.log`.
        3.  **Update Task Manager:**
            - Move description of completed action from `currentFocus` to `completedSteps` list with timestamp and outcome.
            - Clear `currentFocus`, `activeFiles`, `contextSnippet`, `selfReviewFindings`.
        4.  **Determine Next Step:** Check if `overallGoal` is met based on `completedSteps`.
            - If goal met: Proceed to Task Completion steps.
            - If goal not met: Identify next logical step. Transition back to `PLANNING`.
        5.  **Task Completion Steps (If Goal Met):**
            - **Update Roadmap:** If `roadmapId` exists, open `ai_project_roadmap.md`, find task, update status `[ ]` or `[In Progress]` to `[X]`. Save. Log.
            - **Clean Task Manager:** Reset `overallGoal`, `status: Idle`, `completedSteps`, `blockers`. Keep YAML structure. Update timestamp.
            - **Inform User:** Announce task completion (referencing goal/roadmap ID). Report final test status if applicable.
            - Set `currentState: IDLE`.
    - **Exit:** Transition to `PLANNING` (for next step) or `IDLE` (if task complete or error).

- **User Commands:**
    - **`AI: Set self-review level to Off | Basic | Full`**: Read `double.yaml`, update `selfReviewLevel`, write back. Acknowledge.
    - **`AI: Review last changes`**: If `currentState` is `IDLE` or similar safe state, transition to `SELF_REVIEW` to review current `git diff HEAD`.
    - **`AI: Report Double-Agent status`**: Read `ai_task_manager.md`. Report `overallGoal`, `currentState`, recent steps/blockers, `selfReviewLevel` from `double.yaml`.
    - **`AI: Refresh project context`**: Perform scan, compare with context/indices, propose updates to user for confirmation (complex, requires careful implementation).

- **Error Handling:**
    - File I/O errors (read/write/parse JSON/YAML): Report error clearly, log, add Blocker, transition to `IDLE`. **Verify JSON written is a valid array if error occurs during index update.**
    - Terminal command errors: Report output, log, add Blocker, transition to `IDLE`.
    - Unexpected state transitions: Log error, attempt recovery to `IDLE`, ask user.
