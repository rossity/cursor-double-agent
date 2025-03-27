# Double-Agent System Troubleshooting

If the AI agent isn't behaving as expected with the Double-Agent System, here are common issues and solutions:

**Issue: Setup fails immediately, complaining about missing `project_interview.md`.**

*   **Cause:** You did not create and fill out `project_interview.md` in the **project root** *before* pasting the `@.cursor/double_agent_system/setup_instructions.md` script into the chat. The template is in `.cursor/double_agent_system/`, but the filled file must be in the root.
*   **Solution:** Stop the agent. Create `project_interview.md` in the project root using the template as a guide, fill it out, save it. Then, restart the setup process by pasting the setup script content into the chat again.

**Issue: Agent ignores the system, doesn't mention states/checks, repeats mistakes.**

*   **Cause:** Mandatory User Rule missing/incorrect; Agent context drift; Cursor update changed behavior.
*   **Solution:**
    1.  **Verify User Rule:** Check Cursor Settings -> Agent -> User Rules matches EXACTLY the text provided by the setup script (Phase 4, Step 7).
    2.  **Restart Cursor:** Fully close and reopen.
    3.  **Explicit Re-Read:** Start prompt: `AI: Read and strictly follow state machine in @.cursor/rules/core_ai_rules.md.`
    4.  **Check Core Files:** Ensure `@.cursor/rules/core_ai_rules.md` and `@.cursor/double_agent_system/context/double.yaml` exist and are valid.
    5.  **Simplify Prompt:** Break down complex tasks.

**Issue: Agent gets stuck asking for clarification, misunderstands context, hallucinates.**

*   **Cause:** Context files (`context/`, `indices/`) are inaccurate/incomplete/outdated; Agent misinterpreting rules or structured data (JSON/YAML); Task needs info not in context.
*   **Solution:**
    1.  **Review Context:** Manually check relevant files in `@.cursor/double_agent_system/context/` and `@.cursor/double_agent_system/indices/`. Ensure JSON files contain valid arrays `[]` or `[{}, {}...]`. Correct errors, add missing details (esp. paths, key entities based on your project knowledge and interview). Use `git status` or `git diff` to see if files have uncommitted changes.
    2.  **Use Context Refresh:** `AI: Refresh project context`. Review proposed changes carefully.
    3.  **Provide Specific Context:** Point agent to files (`@path/to/file`) or specific index entries.
    4.  **Guide Agent:** Provide corrections or more detailed instructions.
    5.  **Check Task Manager:** Ensure `@.cursor/double_agent_system/ai_task_manager.md` YAML is valid and `currentState` / `currentFocus` are correct. Manually fix if needed.

**Issue: Initial Scan Results are poor or inaccurate.**

*   **Cause:** Unconventional project structure; Missing tools (`git`, `find`, `grep`, `ctags`); Permissions issues.
*   **Solution:**
    1.  **Manually Populate Context/Indices:** After setup and validation, invest time manually filling crucial parts of `architecture.md`, `patterns.md`, and adding key entries to JSON indices based on your knowledge. A good starting context is vital.
    2.  **Install Tools:** Ensure `git`, standard Unix tools are available. Install Universal Ctags (`brew install universal-ctags`, etc.). Re-run `AI: Refresh project context` after installing ctags.
    3.  **Check Permissions:** Ensure Cursor terminal has read access.

**Issue: Performance is slow due to checks/parsing.**

*   **Cause:** Overhead of state transitions, file I/O (esp. large indices), terminal commands, JSON/YAML parsing.
*   **Solution:**
    1.  **Accept Trade-off:** The system prioritizes consistency/awareness over raw speed. Some latency is expected.
    2.  **Optimize Indices:** Ensure JSON indices aren't excessively large.
    3.  **Optimize Terminal Commands:** Ensure `grep`/`find` in rules aren't too slow.
    4.  **Check `ctags` Usage:** Ensure `ctags` isn't taking excessively long.

**Issue: Agent corrupts JSON/YAML format in double files.**

*   **Cause:** Agent error during file writing; Complex edits; Misinterpreting structure; Trying to write comments into JSON.
*   **Solution:**
    1.  **Manual Correction:** Open corrupted file and fix format. Use a YAML/JSON validator. Ensure JSON files are valid arrays.
    2.  **Version Control (Crucial):** Use Git. Commit context/index/rule files regularly. Inspect changes (`git diff`) or revert (`git checkout <file>`).
    3.  **Reinforce Rules:** Remind the agent: `AI: Remember to only write valid JSON arrays to the index files in @.cursor/double_agent_system/indices/. Do not add comments inside JSON.`

**Issue: Agent doesn't run terminal commands or reports errors.**

*   **Cause:** Agent capability issue; Syntax error in rules; Tool not installed; Permissions.
*   **Solution:** Verify rules, test commands manually in Cursor terminal, check tool installation, check permissions, report issue to Cursor team if it persists.

**Issue: Self-Review isn't working as expected.**

*   **Cause:** `SelfReviewLevel` in `double.yaml` is `Off`; No uncommitted changes (`git diff HEAD` is empty); `git diff` command fails; Agent fails to generate/parse review JSON; Agent doesn't understand review criteria.
*   **Solution:**
    1.  **Check `double.yaml`:** Ensure `selfReviewLevel` is `Basic` or `Full` in `@.cursor/double_agent_system/context/double.yaml`.
    2.  **Check Git Status:** Run `git status` and `git diff HEAD` manually. Self-review only works on uncommitted changes made by the AI in the `EXECUTING` step. If you committed changes manually mid-task, the diff might be empty.
    3.  **Test `git diff`:** Ensure the command works in the Cursor terminal.
    4.  **Review Prompt:** Check the review prompt logic within `@.cursor/rules/core_ai_rules.md`.
    5.  **Provide Feedback:** Guide the agent if its review quality is poor.

**Issue: Agent tries to manage Git branches or commits.**

*   **Cause:** Agent misinterpreting rules or user prompts.
*   **Solution:** Remind the agent: `AI: You should focus on code changes and self-review using 'git diff HEAD'. Branching and committing are handled by the user.` Ensure prompts don't imply the AI should manage Git history. Refer to the recommended Git Workflow in the README.