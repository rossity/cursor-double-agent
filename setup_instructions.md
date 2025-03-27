# AI Setup Script: Double-Agent System v3

Objective: Initialize the Double-Agent System v3 within this project. Follow these steps precisely. Report progress and any errors encountered.
**ASSUMPTION:** User has already created and filled out `project_interview.md` in the project root based on `project_interview.template.md`. Ensure this project is a Git repository (`git status` should work).

**Phase 1: File Structure and Core Rules Setup**

1.  **Create Base Directories:**
    *   Execute: `mkdir -p .cursor/ai_double_system/context`
    *   Execute: `mkdir -p .cursor/ai_double_system/indices`
    *   Execute: `mkdir -p .cursor/rules`
    *   Confirm directories were created. Report success or failure.

2.  **Create Core Rules File:**
    *   Read the entire content of the template file at `@.cursor/templates/core_ai_rules.template.md`.
    *   Create a new file at `@.cursor/rules/core_ai_rules.md`.
    *   Write the content you just read into `@.cursor/rules/core_ai_rules.md`.
    *   Confirm file creation and content match. Report success or failure.

3.  **Create Double System Files from Templates:**
    *   **Context Files:**
        *   Copy `@.cursor/templates/context/double.template.yaml` to `@.cursor/ai_double_system/context/double.yaml`.
        *   Copy `@.cursor/templates/context/architecture.template.md` to `@.cursor/ai_double_system/context/architecture.md`.
        *   Copy `@.cursor/templates/context/patterns.template.md` to `@.cursor/ai_double_system/context/patterns.md`.
        *   Copy `@.cursor/templates/context/requirements.template.md` to `@.cursor/ai_double_system/context/requirements.md`.
        *   Copy `@.cursor/templates/context/learnings.template.log` to `@.cursor/ai_double_system/context/learnings.log`.
    *   **Index Files (Initialize as Empty JSON Arrays):**
        *   Execute: `echo "[]" > .cursor/ai_double_system/indices/components.json`
        *   Execute: `echo "[]" > .cursor/ai_double_system/indices/utils.json`
        *   Execute: `echo "[]" > .cursor/ai_double_system/indices/services.json`
        *   *(Add similar `echo "[]" > ...` commands for any other index files like models.json, routes.json if templates exist)*
    *   **Other Double Files:**
        *   Copy `@.cursor/templates/ai_project_roadmap.template.md` to `@.cursor/ai_double_system/ai_project_roadmap.md`.
        *   Copy `@.cursor/templates/ai_task_manager.template.md` to `@.cursor/ai_double_system/ai_task_manager.md`.
    *   Confirm all files exist in their respective `@.cursor/ai_double_system/` subdirectories and that JSON index files contain only `[]`. Report success or failure.

**Phase 2: Process Pre-Filled Interview**

4.  **Process Interview Results:**
    *   Announce: "Processing pre-filled interview results from project_interview.md..."
    *   Read the contents of the `project_interview.md` file located in the project root. **If this file does not exist or is empty, report an error and stop.**
    *   Open `@.cursor/ai_double_system/context/architecture.md`. Update `Project Goal`.
    *   Open `@.cursor/ai_double_system/context/requirements.md`. Populate sections based on interview answers (Users, Non-functional reqs, Constraints, Tech Must/Must Not Use).
    *   Open `@.cursor/ai_double_system/context/patterns.md`. Add notes about Style Guides, Testing Approach, Branching Strategy.
    *   Open `@.cursor/ai_double_system/ai_project_roadmap.md`. Update `Project Vision`. Seed `Phase 1` tasks based on "most important features". Mark as `[ ]` (Planned). Add timestamp note.
    *   Open `@.cursor/ai_double_system/context/learnings.log`. Add entry: `[Timestamp] - INFO - Processed user input from pre-filled project_interview.md.`
    *   Save changes to all modified files. Confirm updates.

**Phase 3: Initial Project Scan and Context Population**

5.  **Perform Project Scan using Terminal Commands:**
    *   Announce: "Starting initial project scan using terminal commands..."
    *   *(Execute similar scan commands as before: find package managers, common languages/dirs, config files, framework keywords)*
    *   **Attempt Ctags (Optional but Recommended):**
        *   Run `command -v ctags` to check existence.
        *   If `ctags` exists: Run `ctags -R . --fields=+zK --languages=JavaScript,TypeScript,Python,PHP,Java --output-format=json > .cursor/ai_double_system/tags.json`. Note success/failure.
        *   If `ctags` does not exist: Note its absence.
    *   Announce: "Scan complete. Populating context files..."

6.  **Update Context Files (Initial Scan):**
    *   Open `@.cursor/ai_double_system/context/architecture.md`.
    *   Based *only* on scan results from Step 5:
        *   Fill in detected package managers, potential languages, common dirs, key config files (complementing info from interview).
        *   Make *tentative guesses* for Frontend/Backend Roots, Arch Patterns, Build/Run/Test Commands, clearly marked as inferred *if not provided definitively in interview*.
    *   Open `@.cursor/ai_double_system/context/patterns.md`.
    *   Add notes about detected frameworks/libraries (as guesses, complementing interview).
    *   Open `@.cursor/ai_double_system/context/double.yaml`.
    *   Set `initialSetupComplete: true`.
    *   Set `userValidationComplete: false`.
    *   Set `selfReviewLevel: Off` (default).
    *   Add a timestamp comment like `# Initial scan performed: [Timestamp]`.
    *   Open `@.cursor/ai_double_system/context/learnings.log`.
    *   Add entry: `[Timestamp] - INFO - Initial project scan performed. Ctags status: [Found/NotFound/Failed]. Confidence in scan: [Low/Medium/High based on clarity].`
    *   Save changes to all modified files. Confirm updates.

**Phase 4: User Interaction and Finalization**

7.  **Instruct User for Manual User Rule Setup:**
    *   **IMPORTANT:** Display the following message clearly to the user:
        ```
        ACTION REQUIRED (Manual Step): Please go to Cursor Settings (Cmd+,) -> Agent -> User Rules and add the following *exact* rule. This is CRITICAL for Double-Agent System v3:

        --- User Rule Text ---
        IMPORTANT: Always strictly follow the operational procedures and state machine defined in @.cursor/rules/core_ai_rules.md. Prioritize states: PLANNING, PRE-CHECK, EXECUTING, POST-CHECK, SELF_REVIEW (if enabled), UPDATING. Read from/write to @.cursor/ai_double_system/ subdirectories (context/, indices/, *.md) as specified. Parse/update JSON indices (expecting valid array '[]' or array of objects) and YAML task manager correctly. Use terminal commands for verification. Ask for clarification using numbered options if unsure. Handle self-review based on double.yaml setting.
        --- End User Rule Text ---

        Please confirm when you have added this rule by typing 'User Rule Added'.
        ```
    *   Wait for user confirmation.

8.  **Request User Validation of Context & Roadmap:**
    *   Display message:
        ```
        ACTION REQUIRED: I have processed your `project_interview.md` and performed an initial scan, populating the structured context files.

        Please **carefully review**:
        1.  `@.cursor/ai_double_system/context/` (esp. `architecture.md`, `patterns.md`, `requirements.md`, `double.yaml`) - Check how interview and scan results were merged.
        2.  `@.cursor/ai_double_system/indices/` (JSON files are currently `[]`. Add entries manually now for any known core components/utils from your project.)
        3.  `@.cursor/ai_double_system/ai_project_roadmap.md` (Check if roadmap reflects interview input accurately.)

        Correct inaccuracies, add missing details (e.g., specific entry points, test commands, crucial index entries). Save your changes.

        Once reviewed, type 'Validation Complete'.
        ```
    *   Wait for user confirmation.

9.  **Finalize Setup:**
    *   Once user confirms validation:
        *   Open `@.cursor/ai_double_system/context/double.yaml`.
        *   Set `userValidationComplete: true`.
        *   Add/update timestamp comment: `# User validation completed: [Timestamp]`.
        *   Save the file.
        *   Open `@.cursor/ai_double_system/context/learnings.log`. Add entry: `[Timestamp] - INFO - User validation of initial context completed.` Save.
        *   Announce: "Double-Agent System v3 setup is complete and validated. I will now operate according to the rules in @.cursor/rules/core_ai_rules.md. You can start giving me development tasks, manage the roadmap, or configure self-review (`AI: Set self-review level to ...`)."

**Setup Complete.**
