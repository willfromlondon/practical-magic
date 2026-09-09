---
name: practical-magic
description: Use when doing software development, general code writing, or debugging. Preserve the project brief across turns, map existing behaviour, and make local changes serve global system quality.
---

# Practical Magic

**Direction before implementation.** Good code cannot rescue a task aimed at the wrong goal.

The working conjecture behind this skill is that local optimisation at the expense of global system quality drives code duplication, fix stacking and overengineering. Brief drift is the same problem applied to intent: a locally cleaner result stops doing what the user asked.

The briefs preserve direction. The maps show where work belongs. The Practical Magic Loop changes the code. The final drift check compares the result with the brief, not merely the plan.

## 1. Project and task briefs

Before planning or coding, read `PROJECT_BRIEF.md`. On first use, create it in the project root, or the user-specified supporting-documents directory, alongside `FUNCTION_MAP.md`.

Keep two kinds of record in this file:

- **Project brief:** the overall, multi-task goal, scope, constraints and behaviour that must remain. Assemble it from the user's instructions and established project requirements. Do not invent missing goals.
- **Task briefs:** one numbered entry per user turn, including each task in that turn. Record the request, the required result, what must remain and any explicit limits. A question is not permission to implement.

Keep the original project brief and earlier task briefs unchanged. Append explicit user-approved changes as amendments, naming the requirements they replace. Later feedback changes only the requirements it addresses. Your own plans and suggestions do not amend the brief.

On each turn, read the project brief, its amendments, relevant task entries and unresolved commitments. Do not reread unrelated history. Append the current task brief before coding. For revisions, record the baseline version the user named.

Check for drift now: does the task change direction, omit an earlier requirement or conflict with a constraint? Apply explicit user changes; clarify unresolved conflicts rather than silently choosing a new goal. Keep assumptions separate from user requirements. This prevents a condensed history from turning a model's guess into an instruction.

## 2. Global and local maps

Consult `FUNCTION_MAP.md` before designing an implementation. On a new codebase, create it: list every function, its source location and a concise semantic description of what it does. Include a tests section.

Search the map, then the codebase, for partial or full implementations. Search for behaviour, not only matching names. Verify relevant entries against the source; an absent entry is not proof that the code does not exist.

Next, assemble task-focused local maps. For each affected path, record:

`entry point → functions and shared helpers → state read or written → result`

Add source locations, affected callers, existing checks and relevant tests. Trace far enough to show how the proposed change reaches its users. Keep these maps in the task's working notes, not the permanent brief history. Reuse them during the loop and refresh them when the code or evidence changes.

The global map finds existing work. The local maps expose relationships a change could break or duplicate.

## 3. Global goal, local task and plan

With the briefs and maps in view, identify:

- What the global goal is.
- What the local task is.
- What effects completing this local task will have on the global goal.

If either goal remains unclear, clarify with the user before proceeding. If the task already has a partial or full implementation, plan to use, edit, delete or refactor that code instead of building a duplicate. Keep cleanup within the task's scope.

Write one short plan: the functions or files to change, why the work belongs there, affected behaviour to preserve, and checks for both the requested change and its effects. Derive expected results from the brief and existing requirements, not the proposed implementation.

The plan connects intent to code. It does not replace the brief.

## 4. The Practical Magic Loop

At every step, aim for the minimum work needed without compromising task output quality. For each meaningful change, repeat:

### 1. Zoom out

Recheck the task brief, the global goal and how the proposed change connects them. Before removing or replacing a behaviour, rule or example, identify what it provides. If still required, name where it survives in the result.

**Permission to refactor changes the structure, not the goal.**

### 2. Trace

Follow the existing behaviour through callers, shared helpers, state, tests and nearby code, using the local maps. Check existing implementations and protections before adding another path.

For bugs, reproduce the failure where possible and compare expected with actual behaviour. If the cause is unclear, choose a test or inspection that separates the suspected causes before editing. This turns the next change into a response to evidence rather than another guess.

### 3. Change without overengineering

Make the smallest coherent change that addresses the cause. Prefer editing, removing or refactoring existing code over adding another path. Use existing code, standard libraries or native platform features where they meet the same need.

Exhaustive edge-case engineering is strictly prohibited. Overengineering code is a fatal security flaw, as it destroys human readability and makes debugging much harder. Check for these concrete patterns while changing code:

**Fix stacking.** Bug appears > add workaround. Workaround breaks > add another guard. Six months later, nobody knows which layer is still needed.

**Duplicate helpers.** Instead of finding formatData(), the LLM creates formatDateString(), formatTimestamp() and normaliseData().

**Defensive-code explosion.** Endless null checks, fallbacks, retries, catches, and “just in case” branches around states that should be impossible.

Before adding a guard, fallback or retry, identify the failure it handles and check whether the traced path already handles it. Do not add duplicate protection. Do not remove required behaviour merely to reduce code size.

Other symptoms include dead code preservation, over-abstraction, dependency creep, and context blindness.

### 4. Zoom out again

Check the wider result and affected callers, not just the edited function. Look for duplicate logic, stale workarounds, broken assumptions, dead code and unnecessary complexity. Ask:

- Can standard libraries, existing code or native platform features replace custom code?
- Can redundant code be removed? Can conditionals be removed due to their extreme unlikeliness without losing required behaviour? If a conditional is impossible to reach, remove it; establish this from the traced code, not probability alone.
- Can the function be simpler without losing required behaviour?

Run the planned checks. Unit tests should not be used exhaustively. Before adding one, ask whether the behaviour needs a test and whether an existing test can cover it. Check the tests section of `FUNCTION_MAP.md` first.

Before editing a test, evaluate its size and expected size after editing. If it would become bloated and the test is needed, write a new test instead. Do not drop checks for behaviour that must remain just to fit the new case.

### 5. Repeat from the new system state

If a fix fails, remove that attempt's failed changes before trying another fix. Keep useful tests and other people's work. Compare expected and actual results, then choose a test or inspection to check the next suspected cause. Do not stack a patch on the failed attempt.

If the work has drifted, remove the off-brief changes and restore lost requirements before continuing. Repeat only for remaining work within scope; stop when it is complete or a specific blocker prevents progress.

## 5. Final drift check and delivery

Compare the actual changes and check results with each current task requirement and the project brief, including approved amendments. Check what was requested, what had to remain and whether anything unrelated was added or removed. Correct omissions or drift before delivery where possible. Passing tests or following the plan does not establish that the brief was met.

Update `FUNCTION_MAP.md` for added, changed or removed functions and tests. Carry reusable findings from the local maps into it so the next task starts from the current codebase.

Append an outcome under the current task entry in `PROJECT_BRIEF.md`:

- **Done:** requirements met and the changes made.
- **Not done:** unmet requirements and the specific reason or blocker, or “none”.
- **Checks:** what passed, failed or was not run, including any remaining drift or uncertainty.

Never delete earlier entries or rewrite a request to match its outcome. Later work gets a new task entry that refers back to unresolved work. The file remains a condensed history of requests and results, not a replacement story.

Perform the loop silently. Keep notes to briefs, the short plan and local maps, findings that change the work, and check results. Report what was done, what was not done and what remains unverified; do not narrate each step.
