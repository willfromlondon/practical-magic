# Practical Magic

**Direction before implementation.** Good code cannot rescue a task aimed at the wrong goal.

An agent skill for software development and debugging that keeps the original brief in view, maps existing behaviour, and makes local changes serve the whole project.

## Install

With Node.js and npm installed, run:

```sh
npx skills add willfromlondon/practical-magic
```

Follow the prompts to choose your coding agent and installation scope. To install across your projects, add `-g`:

```sh
npx skills add willfromlondon/practical-magic -g
```

Installation uses the [Skills CLI](https://github.com/vercel-labs/skills).

## Use

Ask your agent:

> Use practical-magic to implement this change. Preserve the project brief, trace the existing behaviour, and check the final result against the request.

The skill guides the agent through five parts:

1. **Preserve direction.** Record the project brief and task requests, with explicit amendments when requirements change.
2. **Map existing behaviour.** Find relevant functions, callers, shared helpers, state and tests before adding code.
3. **Connect the plan to the goal.** Identify the smallest coherent change and the behaviour it must preserve.
4. **Run the Practical Magic Loop.** Zoom out, trace, change, check the wider result, and repeat where needed.
5. **Check for drift.** Compare the actual result with the user's brief and report what was done and what remains.

During use, the skill asks the agent to maintain `PROJECT_BRIEF.md` and `FUNCTION_MAP.md` in the project root or your chosen supporting-documents directory. These records are created in the project where you use the skill.

Read the complete instructions in [SKILL.md](SKILL.md).
