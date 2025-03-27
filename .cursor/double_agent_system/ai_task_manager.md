# AI Task Manager

---
# YAML Frontmatter for State - DO NOT REMOVE --- lines
lastUpdated: 2023-10-27T12:00:00Z
overallGoal: "" # Populated by agent during PLANNING
roadmapId: "" # Optional: Link to ai_project_roadmap.md task ID (e.g., P1.2)
status: Idle # Idle, Planning, In Progress, Blocked, Completing
currentState: IDLE # Current state in the core_ai_rules.mdc machine (IDLE, PLANNING, PRE-CHECK, EXECUTING, POST-CHECK, SELF_REVIEW, UPDATING)
activeFiles: [] # List of files likely involved in the current step
contextSnippet: | # Concise context loaded during PLANNING for the current task/step
  # - Key architectural points
  # - Relevant patterns
  # - Specific index entries found

completedSteps: # List of completed actions for the overallGoal
#  - timestamp: YYYY-MM-DDTHH:mm:ssZ
#    description: "Description of completed action"
#    outcome: Success | Partial | Fail

currentFocus: # Details for the step currently being planned or executed
  action: "" # Description of the specific action
  contextRules: "" # Notes on context findings, rules, verification results relevant to this action
  plan: "" # Outline of the immediate plan for implementing the action

selfReviewFindings: # Populated by SELF_REVIEW state
#  confidenceScore: 0.0 # Float between 0.0 and 1.0
#  summary: "" # Brief assessment
#  issuesFound: [] # List of { file, line, description }
#  suggestionsForImprovement: [] # List of { file, action }

blockers: [] # List of issues preventing progress or questions for the user
# - type: Question | Error | ConfirmationNeeded
#   message: "Description of blocker or question with numbered options if applicable"
#   options: # Optional list for questions
#     - "Option 1"
#     - "Option 2"

---

## Human Readable Notes / Logs for this Task:
(Agent can add high-level notes, decisions, or summaries here if needed, supplementing learnings.log)