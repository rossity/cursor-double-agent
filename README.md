# Double-Agent System v3 for Cursor

This repository contains the configuration files and setup instructions to implement the "Double-Agent System" within the Cursor IDE. This system enhances the AI agent's capabilities by providing structured, persistent context, robust task management, high-level planning, self-correction routines, and an optional Git-based self-review mechanism. It aims to mitigate issues like context loss, redundancy, and lack of strategic planning in complex projects.

**Problems Addressed:**

*   **Context Scalability:** Handles large projects better using structured context files (`context/`) and JSON indices (`indices/`) instead of one monolithic file.
*   **Contextual Drift:** Reduces forgetting instructions via a state-machine-like approach (`core_ai_rules.md`) and focused context loading.
*   **Task Planning:** Assists in breaking down goals via `ai_task_manager.md`.
*   **Project Planning:** Enables tracking high-level roadmap via `ai_project_roadmap.md`.
*   **Code Quality & Consistency:** Introduces an optional **Self-Review** mechanism where the agent uses `git diff` to review its own changes against project standards before finalizing.
*   **Adaptability:** Designed for new and existing projects via scanning and a structured user interview conducted *before* setup.

**Key v3 Improvements:**

*   **Structured Context:** `ai_context.md` is replaced by specific files in `.cursor/ai_double_system/context/` (architecture, patterns, etc.) and `.cursor/ai_double_system/indices/` (JSON files for components, utils, etc.). This improves retrieval speed and reliability.
*   **YAML Task Manager:** `ai_task_manager.md` uses YAML frontmatter for better state tracking and structure.
*   **State Machine Rules:** `core_ai_rules.md` is structured more like a state machine for clearer agent operation.
*   **Optional Self-Review:** A new feature controlled via `.cursor/ai_double_system/context/double.yaml`. Can be `Off`, `Basic` (review + report), or `Full` (review + report + propose fixes + await confirmation). Requires Git.
*   **Pre-Setup Interview:** User provides high-level context via `project_interview.md` *before* the AI setup process begins.

**How to Use (Setup Process):**

1.  **Prepare Project & Interview:**
    *   Ensure your project is a Git repository (`git init` if needed, `git status` should work).
    *   Copy the files from this distribution (listed below) into the **root directory** of your target project.
    *   **Manually create `project_interview.md`:** Use `project_interview.template.md` as a guide. Create a file named `project_interview.md` in your project root and fill it out thoroughly with details about your project. **Save this file.**

2.  **Run Setup Script via AI:**
    *   Open your project in the Cursor IDE.
    *   Open the file `setup_instructions.md` within Cursor.
    *   Select and copy the **entire content** of `setup_instructions.md`.
    *   Paste the copied content directly into the Cursor AI chat panel and press Enter/Send.

3.  **Follow AI Instructions:**
    *   The AI agent will execute the setup script, which now *reads* your pre-filled `project_interview.md`. Follow its prompts carefully:
        *   **Wait:** Allow time for directory/file creation and initial scan.
        *   **Add User Rule:** Manually add the **exact** User Rule text provided by the agent into Cursor Settings -> Agent -> User Rules. Confirm addition.
        *   **Validate Context & Roadmap:** Review the generated files in `@.cursor/ai_double_system/context/`, `@.cursor/ai_double_system/indices/`, and `@.cursor/ai_double_system/ai_project_roadmap.md`. Correct inaccuracies based on the scan and your interview input. Confirm validation.

4.  **Setup Complete:** The Double-Agent System v3 is active.

**Starting Development:**

*   Use standard prompts. The agent should follow the procedures in `@.cursor/rules/core_ai_rules.md`.
*   To generate/regenerate the roadmap: `Generate Project Roadmap: [Describe vision]`.
*   To control self-review: `AI: Set self-review level to Off | Basic | Full`.
*   To manually trigger review: `AI: Review last changes`.

**Files to Copy into Your Project Root:**

*   `.cursor/` (containing the `templates/` subdirectory)
*   `project_interview.template.md` (Use this as a guide to create `project_interview.md`)
*   `setup_instructions.md`
*   `TROUBLESHOOTING.md`
*   `README.md` (this file)

**File Descriptions:**

*   `setup_instructions.md`: AI script to automate setup (reads pre-filled `project_interview.md`).
*   `project_interview.template.md`: Reference template for the user to create `project_interview.md` *before* setup.
*   `.cursor/templates/`: Base templates used during setup.
    *   `core_ai_rules.template.md`: Core agent logic (v3).
    *   `context/`: Templates for structured context files.
    *   `indices/`: Templates for JSON indices (contain commented examples for reference).
    *   `ai_project_roadmap.template.md`, `ai_task_manager.template.md`
*   `.cursor/ai_double_system/` (Created by setup)`: Active state files (context/, indices/, roadmap, task manager). JSON files in `indices/` are initialized as `[]`.
*   `.cursor/rules/` (Created by setup)`: Contains `core_ai_rules.md`.
*   `TROUBLESHOOTING.md`: Help guide (v3).
*   `README.md`: This file.

**Version Control (Git):**

*   **Recommended to Commit:**
    *   `.cursor/rules/core_ai_rules.md`
    *   `.cursor/ai_double_system/context/` (All files inside, except maybe large `learnings.log`)
    *   `.cursor/ai_double_system/indices/` (All JSON files)
    *   `.cursor/ai_double_system/ai_project_roadmap.md`
    *   `TROUBLESHOOTING.md`
    *   `README.md`
    *   (Optionally: `.cursor/templates/` if tracking base templates)
*   **Recommended to Ignore (add to your project's `.gitignore`):**
    *   `.cursor/ai_double_system/ai_task_manager.md`
    *   `.cursor/ai_double_system/tags.json` (if ctags used)
    *   `project_interview.md` (Contains project-specific setup info)
    *   Optionally: `.cursor/ai_double_system/context/learnings.log` (if large)

**Disclaimer:**

This system relies on current Cursor AI capabilities. Effectiveness may vary. Use with awareness. See `TROUBLESHOOTING.md`. The Self-Review feature assumes uncommitted changes reflect the agent's last action and does not automatically commit code.
