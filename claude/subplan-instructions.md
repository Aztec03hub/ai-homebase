You're the orchestrator.

CRITICAL: Follow your `Orchestrated Subagent Workflow` (from CLAUDE.md), remembering to efficiently delegate to your appropriate subagents and use parallel tasks wherever necessary.

CRITICAL: Since YOU are the orchestrator of all the subagents, you must take any important information you get back from any subagent and if it's important for another subagent, you must remember to give that info in detail to that subagent.

CRITICAL: ALWAYS make sure your TODO follows the `Orchestrated Subagent Workflow`.

CRITICAL: We are in hyper-fast development mode. 

CRITICAL: ALWAYS use your `vscode-mcp-server` tools for *all* operations possible, *especially* for bash and shell operations.

We are now going to follow your `Orchestrated Subagent Workflow` to COMPLETELY, ACCURATELY, and ELEGANTLY, to perform a robust and clean implementation/fix.

I need you to try to keep your main context as clean as possible, and use your subagents to successfully perform the necessary work and communicate with you to do a GREAT job.

Use read_file_code tool to read `docs\dev\architecture\master-ux-flows-design.md` fully.

Our goal is to come up with a subplan to fix or implement the following:
```
When users don't have access to certain endpoints or features, they shouldn't be able to see UI elements which access those features. For example, if logged in as learner, viewing the "Assignments" page, each assignment has an "Edit" button visible, but learner role users don't have access to edit assignments, so it'd cause a 403 error. They simply shouldn't even *see* the edit button. An explore agent should be used to see if there are any other instances of UI elements which should be hidden from non-instructor or non-admin roles.
```

By the end of the implementation, we should have the UX for this feature/system/implementation COMPLETELY finished.

We will start with the `architect-planner`. Remember to remind `architect-planner` to read the main project's (relevant) existing architecture docs, as they are the original source of truth for the entire project. 

Remind `architect-planner` that during the `**RESEARCH EXISTING CODEBASE**` step, it should be aware of the links in `src\lib\components\layouts\Navigation.svelte`, which will likely integrate with the feature. 

Remind `architect-planner` that the existing `src\lib\components\layouts` components need to be considered for this feature plan, as well as `src\routes\(protected)\dashboard\+page.server.ts` and `src\routes\(protected)\dashboard\+page.svelte`, which should be respected as existing UX and integrated seamlessly into our feature plan. 

Remind `architect-planner` that UI and UX planning are MANDATORY aspects of this plan, and consideration should be given towards understanding the current UI/UX and staying consistent with existing UI/UX patterns, and it should include a few "user stories" and/or "user flows" describing how things should be accessed. The dashboard and sidebar navigation are mandatory for accessing any main feature pages, and we want to especially avoid any orphaned pages or pages not accessible or clickable from an intuitive portion of the UI. We should consider getting in as much of the relevant functionality from the MVP as is possible in this feature plan.

Since subagents are called as a one-shot subtask, they should be instructed that if they have any questions about UI/UX, or ANY questions, they should be saved in a separate, single file output IMMEDIATELY to you the orchestrator, so that you and I may go over them and make decisions together, before calling a fresh instance of whatever subagent is next to be delegated-to, with those decisions/answers given.

Remember to give any subagents as much detail as possible when calling them, to ALWAYS instruct them to use vscode-mcp-server tools for all operations possible, and to ALWAYS ask me before moving onto the next phase of your todo list.

Feel free to ask me any clarifying questions as needed.