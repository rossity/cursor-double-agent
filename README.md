# Double-Agent System for Cursor

[![Version](https://img.shields.io/badge/Version-3.1-blue)](https://github.com/rossity/cursor-double-agent) <!-- Optional: Add your repo link -->

Welcome to the Double-Agent System for the Cursor IDE! This system enhances Cursor's AI capabilities by providing a structured framework for managing context, planning tasks, and ensuring code quality, especially within complex or long-running projects.

## What it Does

The Double-Agent System aims to make the AI a more reliable coding partner by:

*   **Maintaining Context:** Reduces the AI forgetting instructions or project details by using dedicated context files (`context/`) and structured indices (`indices/`) instead of relying solely on chat history or monolithic context blobs.
*   **Structured Planning:** Facilitates breaking down complex goals into manageable steps using a dedicated task manager (`ai_task_manager.md`) and tracking the overall project direction with a roadmap (`ai_project_roadmap.md`).
*   **Procedural Operation:** Guides the AI through a defined state machine (`core_ai_rules.md`) for tasks: Planning -> Checking -> Executing -> Verifying -> Reviewing -> Updating Context.
*   **Integrated Self-Review (Optional):** Allows the AI to review its own *uncommitted* changes using `git diff` against project standards and context before finalizing its work. This promotes code quality and consistency.
*   **Adaptability:** Designed to be set up in both new and existing projects through an initial user interview and project scan.

## Requirements

*   **Cursor IDE:** This system is designed specifically for the Cursor environment.
*   **Git:** Git must be installed and the target project must be a Git repository (`git init` if needed). `git status` and `git diff` must work correctly from the terminal. The Self-Review feature heavily relies on Git.
*   **Basic Terminal Tools:** Standard Unix/Linux tools like `mkdir`, `cp`, `echo`, `grep`, `find` should be available in the Cursor terminal environment.
*   **(Optional but Recommended) Universal Ctags:** For better code scanning during setup and context refreshing (`brew install universal-ctags` or equivalent).

## How to Install and Setup

Follow these steps carefully:

1.  **Prepare Project & Interview:**
    *   Ensure your project is a Git repository and you have no uncommitted changes you want to keep separate from the setup.
    *   Copy the required files/folders from this distribution into the **root directory** of your target project (see "Files to Copy" below).
    *   **Fill out the Interview:**
        *   Locate the template at `.cursor/double_agent_system/project_interview.md` and fill it out thoroughly with details about *your* project.
        *   **Save the file.** This file is crucial for the setup process.

2.  **Run Setup Script via AI:**
    *   Open the file `@.cursor/double_agent_system/setup_instructions.md` within Cursor.
    *   Select and copy the **entire content** of this file.
    *   Paste the copied content directly into the Cursor AI chat panel and press Enter/Send.

3.  **Follow AI Instructions:**
    *   The AI agent will execute the setup script. It will read your pre-filled `project_interview.md` from the root directory. Follow its prompts carefully:
        *   **Wait:** Allow time for directory/file creation and initial scan.
        *   **Add User Rule:** Manually add the **exact** User Rule text provided by the agent into Cursor Settings -> Agent -> User Rules. Confirm when done. This rule tells Cursor to use the system.
        *   **Validate Context & Roadmap:** Review the generated files in `@.cursor/double_agent_system/context/`, `@.cursor/double_agent_system/indices/`, and `@.cursor/double_agent_system/ai_project_roadmap.md`. Correct any inaccuracies based on the scan and your interview input. Add initial entries to index files (`indices/*.json`) if you know key components. Confirm validation when done.

4.  **Setup Complete:** The Double-Agent System is active. The AI will now operate according to the defined rules.

## How to Use

*   **Standard Prompts:** Start development tasks as usual (e.g., "Implement user login using JWT"). The agent should automatically follow the procedures defined in `@.cursor/rules/core_ai_rules.md`.
*   **Generate/Regenerate Roadmap:** `AI: Generate Project Roadmap: [Describe project vision and key goals]`
*   **Control Self-Review:**
    *   `AI: Set self-review level to Off`
    *   `AI: Set self-review level to Basic` (Review + Report)
    *   `AI: Set self-review level to Full` (Review + Report + Propose Fixes + Await Confirmation)
*   **Manually Trigger Review:** `AI: Review last changes` (Reviews current uncommitted changes)
*   **Check Status:** `AI: Report Double-Agent status`

## Git Workflow Recommendation

This system integrates with Git for self-review, but **it does not automate branching or committing.** We recommend the following workflow for best results:

1.  **User: Create Branch:** Before starting a new feature or significant task, create and check out a new branch (e.g., `git checkout -b feat/new-login-flow`).
2.  **User: Instruct AI:** Give the AI the task.
3.  **AI: Executes Task:** The AI works on the task within the current branch, using the Double-Agent state machine (Plan -> Pre-check -> Execute -> Post-check -> Self-Review -> Update). Self-review (`git diff HEAD`) checks the AI's *uncommitted* work on this branch.
4.  **User: Reviews AI Work:** Once the AI indicates the task step (or whole task) is complete, review the changes (`git diff`, visually inspect).
5.  **User: Commits Changes:** If satisfied, stage (`git add .`) and commit the changes with a meaningful message (`git commit -m "feat: Implement initial login form"`).
6.  **User: Manages PRs/Merges:** Handle pull requests and merges as per your team's standard process.

This keeps the user in control of the Git history while leveraging the AI for code generation and review within a specific task context.

## Files to Copy into Your Project Root

*   `.cursor/` (containing the `templates/` and `double_agent_system/` subdirectories with their contents, including `setup_instructions.md` and `project_interview.template.md`)

## File Descriptions

*   `.cursor/double_agent_system/setup_instructions.md`: AI script to automate setup (reads `project_interview.md` from root).
*   `.cursor/double_agent_system/project_interview.template.md`: Template for the user interview file (`project_interview.md`).
*   `.cursor/templates/`: Base templates used during setup.
    *   `core_ai_rules.template.md`: Core agent logic state machine.
    *   `context/`: Templates for structured context files (`architecture.md`, `patterns.md`, etc.).
    *   `indices/`: Templates for JSON indices (`components.json`, etc. - contain examples).
    *   `ai_project_roadmap.template.md`, `ai_task_manager.template.md`
*   `.cursor/double_agent_system/` (Created by setup): Active state files.
    *   `context/`: Holds current project context (`architecture.md`, `patterns.md`, `double.yaml`, `learnings.log`, etc.).
    *   `indices/`: Holds JSON arrays of project elements (`components.json`, `utils.json`, etc.).
    *   `ai_project_roadmap.md`: High-level project plan.
    *   `ai_task_manager.md`: Tracks the AI's current task state.
*   `.cursor/rules/` (Created by setup): Contains the active `core_ai_rules.md`.
*   `TROUBLESHOOTING.md`: Help guide.
*   `README.md`: This file.

## Version Control (.gitignore Recommendations)

*   **Commit:**
    *   `.cursor/rules/core_ai_rules.md`
    *   `.cursor/double_agent_system/context/` (All files, except potentially large `learnings.log`)
    *   `.cursor/double_agent_system/indices/` (All JSON files)
    *   `.cursor/double_agent_system/ai_project_roadmap.md`
    *   `.cursor/double_agent_system/project_interview.template.md` (To keep the template source)
    *   `.cursor/double_agent_system/setup_instructions.md` (To track setup process)
    *   `README.md`
    *   `TROUBLESHOOTING.md`
    *   (Optionally: `.cursor/templates/` if tracking base template changes)
*   **Ignore (add to project's `.gitignore`):**
    *   `.cursor/double_agent_system/ai_task_manager.md` (Highly dynamic state file)
    *   `.cursor/double_agent_system/tags.json` (Generated ctags data)
    *   `project_interview.md` (Project-specific setup info, potentially sensitive)
    *   Optionally: `.cursor/double_agent_system/context/learnings.log` (Can get large)

## Disclaimer

This system enhances AI capabilities but relies on the current behavior of Cursor's AI. Effectiveness may vary. Use with awareness and supervise the AI's actions, especially file modifications and Git operations. See `TROUBLESHOOTING.md`. The Self-Review feature reviews uncommitted changes; the user is responsible for final validation and committing code.