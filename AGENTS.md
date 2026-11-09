# AGENTS.md — Dropin instructions for Codex

Before coding, read `DROPIN_PRODUCT_CONSTITUTION.md` completely. It is the product source of truth.

## Role

You are the engineering executor. Implement the approved product and architecture decisions. Do not independently redefine Dropin.

## Required workflow for every task

1. Inspect the current repository state, branch, package scripts, and relevant existing implementation.
2. Identify the smallest upstream Karakeep components that already solve the requirement.
3. State a short implementation plan and list files likely to change.
4. Preserve upstream behavior. Add Dropin-specific behavior behind isolated modules, routes, tables, or feature flags where practical.
5. Implement the smallest vertical slice.
6. Add or update tests.
7. Run relevant tests, type checks, lint, and build checks available in the repository.
8. Report exactly what changed, commands run, results, limitations, and any decisions needing approval.

## Stop and ask before proceeding if

- the task requires changing the product promise, target user, mobile-first strategy, or cross-platform scope;
- the task requires system-wide background sensing or unsolicited notifications;
- a new database/vector store/Agent framework is proposed;
- a dependency introduces material license obligations not already approved;
- the implementation would remove original-source traceability, user control, export, or deletion;
- upstream code behavior is ambiguous and a change could break existing Karakeep functionality.

## Product invariants

- Dropin's differentiator is Intent-aware Knowledge Recall, not another bookmark manager or summarizer.
- Core loop: Capture → Understand → Connect to Intent → Recall → Apply → Feedback.
- User-declared intentions are the initial context source.
- A recommendation must include an explanation and source reference.
- Silence is a valid intervention decision.
- User must be able to dismiss, snooze, or disable proactive suggestions.
- Save first, process later. Original content must survive AI failure.
- Mobile is primary; desktop is a companion workspace.
- HarmonyOS is not an exclusive platform target.

## Reuse-first rules

- Inspect Karakeep's existing bookmark, upload, crawler, inference, embeddings, search, queue, worker, auth, API, mobile, and web implementations before adding equivalents.
- Reuse existing schema conventions and infrastructure where feasible.
- Do not assume the database is PostgreSQL. Current source uses Drizzle + SQLite; verify the active branch and adapters.
- Do not assume offline download equals offline capture. Verify the share/upload path and implement a durable local capture queue only if needed.
- Do not add Mem0 or Graphiti in the first PoC unless a measured gap requires it.
- Record any upstream code copied or modified, including source path, commit, and license.

## Intent Engine requirements

Keep the Dropin-owned logic isolated. The initial engine should accept:
- an active intention (description, topics, stage, status, timestamps);
- candidate saved items (title, extracted text, tags, source, created time, embeddings if available);
- explicit user feedback and cooldown state.

It should return:
- ranked candidates;
- factor-level scores or reasons;
- a recommendation explanation;
- an intervention decision such as `silent`, `discovery`, or `suggest`;
- a reason for not interrupting where appropriate.

Do not present heuristic scores as validated AI confidence. Keep scoring configurable and testable.

## Data and reliability

- Use additive migrations where possible.
- Preserve original source URL/content and generated metadata separately.
- Use idempotency for capture and background jobs.
- Define explicit statuses and terminal failure states.
- Add bounded retry and manual retry.
- Do not leave indefinite `processing` states.
- Ensure user-scoped data access on every API and query.
- Avoid logging private content or credentials.

## UI requirements

- Design mobile-first, with touch-friendly controls and short capture flow.
- Do not simply shrink desktop layouts.
- Provide clear loading, empty, offline, failure, retry, and success states.
- Recommendations must show why the item is relevant and provide access to the original source.
- Avoid notification spam and decorative AI activity that has no functional meaning.

## Reporting format

At the end of each task, provide:
1. Summary of behavior implemented.
2. Files changed.
3. Reused upstream components.
4. Tests/checks run and their exact outcomes.
5. Screenshots or recording for UI tasks.
6. Known limitations.
7. Decisions requiring product / architecture approval.

Never claim completion based only on a plan or mock UI.
