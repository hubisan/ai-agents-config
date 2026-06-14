# AGENTS.md

## Workflow

The workflow has three main phases:

1. Prepare
2. Decide between Plan and Build
3. Execute either Plan or Build

The AI must keep planning and implementation clearly separated.

### Prepare

1. AI works on tasks in state `NEXT` or `CONTINUE`, unless the user explicitly instructs otherwise.
2. For each AI task, the AI creates or reuses a dedicated task file in [../tasks/](../tasks/) based on [../tasks/template.org](../tasks/template.org):
   ```text
   ../tasks/YYYY-MM-DD--slug.org
   ```
   Remove template sections that are not relevant. Add additional sections when useful.
3. The AI adds the task start date and time at the top of the task file:
   ```org
   #+TASK_STARTED: [2026-06-14 So 17:06]
   ```
4. AI links the task file in [../tasks/todo.org](../tasks/todo.org) directly below the task heading:
   ```org
   Task file: [[./2026-05-24--anki-export-fix.org]]
   ```
   The link must be relative to `todo.org`.
5. AI reads and follows [./repository.org](./repository.org) for repository-specific instructions.
6. The AI prepares the Git branch before making task-related changes.
   If the repository is on `main`, create a focused branch named `type/description`, using one allowed type:
   ```text
   feat, fix, hotfix, refactor, perf, docs, test, release, ci, chore
   ```
   If the repository is not on `main`, continue on the current branch and report the suspicious repository state in the task file and to the user.
7. After preparation, the AI decides whether to enter `Plan` or `Build`.

### Decide between Plan and Build

Before modifying repository code, the AI must decide whether to enter `Plan` or `Build`.

The AI must use `Plan` when either of the following is true:

- the user explicitly requests `/plan`,
- the task requires planning.

The AI is responsible for deciding whether planning is required. The user does not need to request `/plan` manually for complex work.

Planning is required when the task is complex, risky, architectural, unclear, or likely to affect any of the following:

- multiple files,
- APIs,
- data models,
- workflows,
- infrastructure,
- migrations,
- security,
- performance,
- user-facing behavior.

If the user explicitly requests `/plan`, the AI must use `Plan` even if the task appears simple.

The AI may use `Build` directly only when all of the following are true:

- the user did not explicitly request `/plan`,
- the task is simple and low-risk,
- the task is narrowly scoped,
- the task is well understood,
- the task has no architectural impact,
- the task can be validated with straightforward checks.

### Plan

Use this path when the task requires planning or when the user explicitly requested `/plan`.

#### Plan: Prepare

1. Confirm that the task is in state `NEXT` or `CONTINUE`, unless the user explicitly instructed otherwise.
2. Confirm that the task file exists and is linked from [../tasks/todo.org](../tasks/todo.org).
3. Read the task description and relevant task file notes.
4. Read and follow [./repository.org](./repository.org).
5. Inspect relevant files as needed, but do not modify repository code.
6. Perform the model suitability and cost awareness assessment.
7. If continuing is appropriate, proceed with planning.
8. Do not modify repository code.

#### Plan: Execute

1. Write the planning result directly into the task file under a `* Planning` section.
2. The planning section must include:
   - planned date,
   - plan status,
   - problem summary,
   - relevant context,
   - goals,
   - non-goals,
   - architectural design,
   - step-by-step execution plan,
   - affected files or areas,
   - risks,
   - assumptions,
   - open questions,
   - proposed checks and tests.
3. If important information is missing, document the question or assumption in the task file.
4. If blocked, set the task to `WAIT`, document the blocker in the task file, notify the user, and stop.

#### Plan: Complete

1. Set the task state to `REVIEW` in [../tasks/todo.org](../tasks/todo.org).
2. Notify the user that the plan is ready for review.
3. Stop without modifying repository code.
4. The AI must not switch from `Plan` to `Build` until the user explicitly approves the plan.
5. After approval, the user sets the task to `CONTINUE` or explicitly instructs the AI to continue.

### Build

Use this path only when the workflow allows implementation.

#### Build: Prepare

1. Confirm that the task is in state `NEXT` or `CONTINUE`, unless the user explicitly instructed otherwise.
2. Confirm that the task file exists and is linked from [../tasks/todo.org](../tasks/todo.org).
3. Read the task description and relevant task file notes.
4. Read and follow [./repository.org](./repository.org).
5. Inspect relevant files as needed.
6. Perform the model suitability and cost awareness assessment.
7. If continuing is appropriate, prepare the branch.
8. Document the intended implementation approach briefly in the task file before editing code.

#### Build: Execute

1. Implement the requested task.
2. Keep changes focused.
3. Update relevant documentation, links, and references.
4. Run relevant checks, tests, linters, or manual verification steps.
4. Update [../../CHANGELOG.org](../../CHANGELOG.org) when the change is notable.
4. Update project documentation and [../../README.org](../../README.org).
5. If blocked:
   - set the task to `WAIT`,
   - record the blocker and reason in the task file,
   - notify the user,
   - stop until the task is set back to `NEXT` or `CONTINUE`.

#### Build: Complete

1. Write implementation notes directly into the task file under a `* Build` heading. The `* Build` section must include or be updated with:
   - build started date,
   - build status,
   - implementation summary,
   - changed files,
   - checks performed,
   - test results,
   - blockers,
   - open questions,
   - follow-ups.
2. Set the task to `REVIEW` in [../tasks/todo.org](../tasks/todo.org).
3. Notify the user that the task is ready for review.

## Review and Approval

1. The user reviews tasks in state `REVIEW`.
2. If more work is needed, the user sets the task to `CONTINUE`.
3. If the task was in `Plan`, `CONTINUE` allows the AI to enter `Build`.
4. If the task was already implemented, `CONTINUE` means the AI should continue implementation work.

5. After approval by the user, the AI completes the task.

## Done

After user approval, the AI must:
1. Set the task to `DONE`.
2. Update [../../CHANGELOG.org](../../CHANGELOG.org) for notable changes.
3. Add the completion date at the top of the task file:
   ```org
   #+TASK_COMPLETED: [2026-06-14 So 17:29]
   ```
4. Move the task file to [../tasks/archive/](../tasks/archive/).
5. Update the link in [../tasks/todo.org](../tasks/todo.org) to point to the archive subdirectory.
6. Move the completed task entry in [../tasks/todo.org](../tasks/todo.org) under the heading `* Completed` at the bottom.
   Create the heading if it does not exist.

## Commits

1. The AI commits only when explicitly asked.

2. Commits must be focused and have clear messages.

3. If asked to commit, the AI must:

   * review the branch diff,
   * update [../../CHANGELOG.org](../../CHANGELOG.org) if needed,
   * create one clear commit.

4. The user merges the branch or explicitly requests a merge into `main`.
