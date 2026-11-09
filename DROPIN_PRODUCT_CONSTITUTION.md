# DROPIN — Product Constitution
Version: 1.0
Status: Baseline for PoC
Product owner: User
Product / architecture partner: ChatGPT
Engineering executor: Codex

## 1. Product definition

**Dropin is a mobile-first, cross-platform personal knowledge agent that connects a person's saved knowledge to their active intentions and brings useful knowledge back at the right moment, with minimal interruption.**

Working product promise:
> Your knowledge, at the right moment.

Dropin is not merely:
- a bookmark manager;
- an AI summarizer;
- a chat interface over saved documents;
- a knowledge graph visualization;
- a notification engine.

Those can be supporting capabilities. The distinctive product value is **intent-aware knowledge recall and controlled intervention**.

## 2. Core product loop

1. Capture — the user saves text, links, images, PDFs, or other supported content.
2. Understand — the system extracts content and metadata, summarizes and indexes it.
3. Connect — the system relates saved items to user-declared active intentions.
4. Recall — when an intention is active or the user asks, Dropin retrieves relevant prior knowledge.
5. Apply — the user opens, combines, cites, or uses the recalled knowledge.
6. Learn from feedback — the user can use, dismiss, snooze, or correct recommendations.

The loop must remain traceable to original sources. AI-generated summaries are not the source of truth.

## 3. Non-negotiable differentiator

The core owned by Dropin is the **Intent-aware Recall Engine**:
- Intent Model: what the user is trying to accomplish, topics, stage, status, and time context.
- Knowledge Matching: match saved items to active intentions using available lexical and semantic signals.
- Recall Ranking: rank candidates with explainable factors.
- Intervention Policy: decide whether to stay silent, show an in-app discovery, or offer a more prominent suggestion.
- Feedback Loop: use explicit user actions to tune future recommendations.

The system must explain *why* a knowledge item was recommended. Do not surface opaque recommendations.

## 4. Product boundaries

### In scope for the first useful MVP
- Personal knowledge, single-user-first experience.
- Mobile-first interaction; desktop is a companion workspace.
- User-initiated capture.
- Reliable persistence before AI processing.
- Asynchronous content processing with visible states and retry.
- User-created / user-confirmed active intentions.
- Retrieval of saved knowledge against an active intention.
- Recommendation explanation and explicit feedback controls.
- Source links and original content preservation.

### Explicitly out of scope until validated
- Reading all activity across other apps.
- Unconsented background surveillance or inferred sensitive profiling.
- Automatic access to private WeChat, Xiaohongshu, or other app data.
- Complex multi-agent orchestration.
- A graph database as a prerequisite.
- Building a generic all-purpose assistant.
- Native HarmonyOS system-level Live Window integration.
- Automatic notifications without a clear user-controlled policy.
- Replacing every existing knowledge-management product.

HarmonyOS is an inspiration and a potential future integration target, not a platform restriction.

## 5. Platform and technical baseline

Current PoC decision:
- Use Karakeep as the candidate codebase / infrastructure foundation.
- Keep its upstream code and product capabilities intact wherever possible.
- Reuse existing capture, bookmark, content extraction, indexing, search, API, worker, and mobile/web foundations after verifying the exact current implementation.
- Add Dropin-specific capabilities in clearly separated modules.

Important source-grounded architecture notes:
- Karakeep has a Next.js web app and an Expo / React Native mobile app.
- Its current database layer uses Drizzle with SQLite (`packages/db/schema.ts` and `packages/db/sqlite.ts`).
- It has worker processes and queues, including crawler, inference, and embedding workers.
- Its mobile app has offline *reading/download synchronization*. This must not be mistaken for a complete offline *capture/upload queue*.
- Do not assume PostgreSQL or pgvector is the existing Karakeep database architecture. Verify the current vector-store adapter before choosing how Dropin reuses embeddings.

AGPL-3.0 is accepted for this project. Preserve upstream copyright and license notices, document modifications, track dependency licenses, and ensure distribution/network deployment obligations are reviewed before release. This is an engineering policy, not legal advice.

## 6. Core interaction principles

1. **Save first, process later.** A capture must become safely persisted before slow AI work starts.
2. AI failure must never erase the original capture.
3. Every processing state must have a clear terminal or retry path.
4. Keep the mobile capture path short.
5. Recommendations must have reasons and source references.
6. Users can dismiss, snooze, disable, or correct proactive suggestions.
7. Silence is a valid Agent decision.
8. Avoid repeated or irrelevant interruptions.
9. Desktop supports deeper organization and review; it must not dictate mobile UX.
10. Do not claim system-level OS capabilities when only a Web or app-level simulation exists.

## 7. MVP acceptance journey

A tester can:
1. Save a link or text on mobile.
2. See immediate confirmation that the original item was saved.
3. See processing status and recover from a failed processing attempt.
4. Create an intention such as "Research AI beauty education".
5. Ask Dropin to find useful prior items for that intention.
6. See ranked results with clear reasons and source links.
7. Mark a result useful, dismiss it, or snooze it.
8. Reopen the app and find their saved items and intention still present.

For the first PoC, a recommendation may be user-triggered. Automatic proactive surfacing is a later milestone after retrieval quality is evaluated.

## 8. Engineering guardrails

- Do not rewrite Karakeep features that already satisfy the requirement.
- Do not fork or copy code from unrelated projects without recording source, version/commit, license, and obligations.
- Do not introduce Mem0, Graphiti, a vector database, or a second Agent framework unless a demonstrated requirement justifies it.
- Prefer existing Karakeep APIs, schema conventions, queue abstractions, worker patterns, and UI components.
- Keep Dropin code isolated and named (e.g. `dropin/intent`, `dropin/recall`, `dropin/intervention`) rather than scattering product logic throughout upstream code.
- Migrations must be additive where possible, tested against a clean database and an upgraded database.
- Use stable IDs, idempotency keys, explicit job states, bounded retries, and observable errors.
- Add tests for ranking, explanation, intervention policy, deduplication, and failure recovery.
- Never hard-code secrets or commit `.env` files.
- Never state a feature is implemented unless code and tests demonstrate it.
- Do not silently change product scope, platform strategy, core data ownership, or intervention policy. Ask the product owner / architect first.

## 9. Decision ownership

Codex may decide implementation details that do not alter product behavior or architecture boundaries.

The following require explicit product / architecture approval:
- changing the product's core promise or target user;
- replacing mobile-first with desktop-first;
- changing from cross-platform to Harmony-only;
- adding automatic background sensing or notifications;
- introducing a new database, vector store, or Agent framework;
- changing data retention, privacy, export, or deletion behavior;
- adopting a dependency with materially different licensing obligations;
- removing source traceability or user feedback controls.

## 10. Definition of done

A task is done only when:
- the requested behavior exists in code;
- relevant automated tests pass;
- migrations and failure paths are covered where applicable;
- mobile and desktop behavior are checked when affected;
- screenshots or a short recording are supplied for UI work;
- limitations and untested areas are listed;
- no unrelated redesign or scope expansion was introduced.
