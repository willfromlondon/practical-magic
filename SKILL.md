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

Keep these records in this file:

- **Project brief:** the overall, multi-task goal, scope, constraints and behaviour that must remain. Assemble it from the user's instructions and established project requirements. Do not invent missing goals.
- **Task briefs:** numbered entries for substantive requests, including each task in the request. Record the request, the required result, what must remain and any explicit limits. A question is not permission to implement. Acknowledgements and routine status questions need no new entry unless they change requirements or commitments.
- **Current requirements:** a concise view derived from the original brief, task entries and explicit amendments, with references to the entries it incorporates. Update this view when requirements change; never rewrite historical requests to match the implementation.

Keep the original project brief and earlier task briefs unchanged. Append explicit user-approved changes as amendments, naming the requirements they replace. Later feedback changes only the requirements it addresses. Your own plans and suggestions do not amend the brief.

On each turn, read the current requirements and unresolved commitments. Consult the referenced historical entries when needed to resolve their meaning or scope; do not reread unrelated history. Append a task brief before coding on a new or amended request. For revisions, record the baseline version the user named.

Check for drift now: does the task change direction, omit an earlier requirement or conflict with a constraint? Apply explicit user changes; clarify unresolved conflicts rather than silently choosing a new goal. Keep assumptions separate from user requirements. This prevents a condensed history from turning a model's guess into an instruction.

Give each kind of information one home: requirements and their history in `PROJECT_BRIEF.md`; current implementation responsibilities in `FUNCTION_MAP.md`; temporary hypotheses and investigation details in working notes only when needed; final check results and unresolved work in one concise task outcome. Record each implementation finding and check result once, referencing it elsewhere rather than repeating it. Keep working notes only when they prevent repeated investigation or preserve unresolved work.

## 2. Global and local maps

Consult `FUNCTION_MAP.md` before designing an implementation. On a new codebase, create a selective map of the main components, shared business rules, important state and relevant test locations. Include source locations and a tests section. Expand to function-level detail along the paths affected by the task; do not require a complete function inventory before local work. The map is a navigation aid, not proof of completeness.

Search the map, then the codebase, for partial or full implementations. Search for behaviour, not only matching names. Verify relevant entries against the source; an absent entry is not proof that the code does not exist.

Next, trace task-focused local paths. Record a local map when it prevents repeated investigation or preserves unresolved work:

`entry point → functions and shared helpers → state read or written → result`

Trace source locations, affected callers, existing checks and relevant tests far enough to show how the proposed change reaches its users. Keep any temporary local maps in working notes; retain reusable discoveries in the function map without copying the investigation history. Reuse this understanding during the loop and refresh it when the code or evidence changes.

The global map finds existing work. The local maps expose relationships a change could break or duplicate.

## 3. Global goal, local task and plan

With the briefs and maps in view, identify:

- What the global goal is.
- What the local task is.
- What effects completing this local task will have on the global goal.

If either goal remains unclear, clarify with the user before proceeding. If the task already has a partial or full implementation, plan to use, edit, delete or refactor that code instead of building a duplicate. Keep cleanup within the task's scope.

Write one short plan with the current task: the functions or files to change, why the work belongs there, affected behaviour to preserve, and checks for both the requested change and its effects. Keep it in one place. Derive expected results from the brief and existing requirements, not the proposed implementation.

The plan connects intent to code. It does not replace the brief.

## 4. The Practical Magic Loop

At every step, aim for the minimum work needed without compromising task output quality. Use three checkpoints for each coherent change: establish the current request, preserved behaviour and affected path before changing; trace dependencies and respond to new evidence while changing; verify the actual result against the request before delivery. Sections 1–3 supply the preparation. The steps below implement these checkpoints, and section 5 completes the same delivery check rather than starting another review.

### 1. Zoom out

Use the task brief, global goal and connection already established during preparation. Revisit them when the request, assumptions or affected path change. Before removing or replacing a behaviour, rule or example, identify what it provides. If still required, name where it survives in the result.

**Permission to refactor changes the structure, not the goal.**

### 2. Trace

Follow the existing behaviour through callers, shared helpers, state, tests and nearby code, using the local maps. Check existing implementations and protections before adding another path.

For bugs, reproduce the failure where possible and compare expected with actual behaviour. If the cause is unclear, choose a test or inspection that separates the suspected causes before editing. This turns the next change into a response to evidence rather than another guess.

### 3. Change without overengineering

Make the smallest coherent change that addresses the cause. Prefer editing, removing or refactoring existing code over adding another path. Use existing code, standard libraries or native platform features where they meet the same need.

Prefer the implementation that is easiest to understand and change correctly. Share code when it represents the same rule or responsibility. Keep separate responsibilities separate, even when their implementations look similar. Do not reduce line count by compressing formatting or hiding control flow. Before reusing a helper, check its side effects and transaction ownership as well as its returned value.

Avoid speculative edge-case machinery; address identifiable failures in proportion to their consequences. Check for these concrete patterns while changing code:

**Fix stacking.** Bug appears > add workaround. Workaround breaks > add another guard. Six months later, nobody knows which layer is still needed.

**Duplicate helpers.** Instead of finding formatData(), the LLM creates formatDateString(), formatTimestamp() and normaliseData().

**Defensive-code explosion.** Endless null checks, fallbacks, retries, catches, and “just in case” branches around states that should be impossible.

Add a guard, fallback or retry for an identifiable failure with a meaningful consequence. Check whether that failure is already handled at this boundary; do not add duplicate protection there. Similar checks at different boundaries may serve different purposes. Preserve necessary validation, error recovery and data-integrity guarantees. Do not remove required behaviour merely to reduce code size.

Other symptoms include dead code preservation, over-abstraction, dependency creep, and context blindness.

### 4. Zoom out again

Check the wider result and affected callers, not just the edited function. Look for duplicate logic, stale workarounds, broken assumptions, dead code and unnecessary complexity. Ask:

- Can standard libraries, existing code or native platform features replace custom code?
- Can redundant code be removed? Remove a condition when evidence from the traced code establishes it is redundant or unreachable, not merely unlikely, while preserving required behaviour.
- Can the function be simpler without losing required behaviour?

Choose checks from the behaviour that could fail: the requested change, preserved requirements, and affected callers or stored data. Check the tests section of `FUNCTION_MAP.md` first. Prefer observable results over internal structure or incidental formatting. Reuse existing coverage when it tests the same contract; add or separate tests when needed for clear, meaningful coverage. Do not drop checks for behaviour that must remain just to fit the new case.

Run the relevant checks after the coherent change. Broaden testing when failures, shared dependencies or the consequences of failure justify it. For UI changes, choose a targeted interaction check when it provides better evidence than inspecting generated markup. Report what the checks establish and what they leave unverified.

### 5. Repeat from the new system state

Repeat investigation when a check fails, an assumption changes or a newly discovered dependency expands the affected path. Do not repeat an unchanged analysis merely to complete another iteration.

If a fix fails, remove changes supported only by the failed hypothesis. Preserve independently justified changes, useful regression checks and other people's work. Compare expected and actual results, then choose a test or inspection to check the next suspected cause. Do not stack a patch on an invalidated workaround.

If the work has drifted, remove the off-brief changes and restore lost requirements before continuing. Repeat only for remaining work within scope; stop when it is complete or a specific blocker prevents progress.

## 5. Final drift check and delivery

Compare the actual changes and check results with each current task requirement and the project brief, including approved amendments. Check what was requested, what had to remain and whether anything unrelated was added or removed. Correct omissions or drift before delivery where possible. Passing tests or following the plan does not establish that the brief was met.

Update `FUNCTION_MAP.md` where the change affects mapped responsibilities, shared rules, important state or test locations. Replace outdated entries and remove obsolete ones; do not append task histories to the map. Carry reusable findings from local investigation into their current entries so the next task starts from the current codebase.

Append one concise outcome under the current task entry in `PROJECT_BRIEF.md`; reference it elsewhere instead of duplicating it:

- **Done:** requirements met and the changes made.
- **Not done:** unmet requirements and the specific reason or blocker, or “none”.
- **Checks:** what passed, failed or was not run, including any remaining drift or uncertainty.

Never delete earlier entries or rewrite a request to match its outcome. Later work gets a new task entry that refers back to unresolved work. The file remains a condensed history of requests and results, not a replacement story.

Perform the loop silently. Keep records in their designated homes, limited to what preserves direction or prevents repeated work. Report what was done, what was not done and what remains unverified; do not narrate each step.
