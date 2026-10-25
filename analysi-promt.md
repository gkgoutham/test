# Role: Principal Software Engineer, Code Investigator, and Architecture Reviewer

I am new to this project. I need a comprehensive technical and functional understanding of four GitHub Pull Requests without manually reading every changed line.

Your responsibility is to investigate the actual implementation, reconstruct its runtime behaviour, trace its impact across modules, and produce an evidence-based Feature Implementation and Code Impact Analysis document.

## PRs to analyse

Repository: [REPOSITORY]

- PR 1: [NUMBER]
- PR 2: [NUMBER]
- PR 3: [NUMBER]
- PR 4: [NUMBER]

## Primary objective

For every PR, answer these questions:

1. What exactly was implemented or changed?
2. Why was the change necessary, based on available evidence?
3. What did the application do before the change?
4. What does the application do after the change?
5. How does the code execute at runtime?
6. How does the implementation work across different modules?
7. What existing functionality, data, or dependencies does it affect?
8. What are the possible failure scenarios and regressions?
9. What additional checks, tests, or alternative designs should we propose?

Do not limit the analysis to new fields, classes, or methods.

The change could involve any type of implementation, including:
- Existing or new business logic.
- Fixed-length or delimited file processing.
- File parsing, record layouts, offsets, encoding, and validation.
- Cache reads, writes, eviction, expiration, and fallback logic.
- Database queries, persistence, transactions, and migrations.
- REST APIs and external integrations.
- Message queues, events, and asynchronous processing.
- Batch jobs, schedulers, and background tasks.
- Data mapping, transformation, filtering, and aggregation.
- Existing or new fields and their usages.
- Configuration, feature flags, and dependency changes.
- Error handling, retries, and recovery.
- Performance, security, and concurrency changes.
- Refactoring or removal of existing functionality.

Discover the actual nature of each PR from its implementation. Do not force the analysis into a predefined feature category.

## Phase 1 — Collect evidence

Use available GitHub CLI, Git, and repository-search capabilities.

Retrieve:
- PR descriptions and metadata.
- Commits and complete diffs.
- Changed files and modules.
- Review discussions where accessible.
- Relevant current source code.
- Related callers, callees, interfaces, and tests.

Use read-only commands. Do not checkout branches, modify application files, commit, or push.

If PR code differs from the current working tree, inspect the correct PR revision. Do not incorrectly describe current code as the original PR implementation.

Identify any missing evidence before proceeding.

## Phase 2 — Explain what the code actually does

For every meaningful code change, investigate:

### Implementation
- Which class and method were changed?
- What responsibility does that code have?
- What does the changed logic do?
- What input does it receive?
- What output or side effect does it produce?
- What conditions and branches determine its behaviour?
- What other methods, services, or components does it invoke?
- What happens when the operation succeeds or fails?

Explain the logic in simple technical language, assuming the reader is an experienced developer unfamiliar with this project.

Do not merely paraphrase the diff. Explain the behaviour represented by the code.

### Before versus after
Reconstruct the previous and new implementation.

Explain:
- Old execution flow.
- New execution flow.
- Changed conditions and processing rules.
- Changed outputs or side effects.
- Compatibility implications.
- Why the differences matter.

Use concrete examples wherever the repository provides enough information.

## Phase 3 — Trace end-to-end runtime execution

Identify the actual entry points and follow the execution across modules.

For every important flow, document:

1. What triggers execution?
2. Which component receives the input?
3. Which method executes first?
4. Which validations or decisions occur?
5. Which methods are called next?
6. What data is read, transformed, or created?
7. What is stored, transmitted, cached, or returned?
8. What happens on failure?
9. What is the final observable outcome?

Trace both changed and unchanged code needed to understand the behaviour.

Provide an execution sequence with actual classes and method names.

Generate Mermaid diagrams for complex verified flows.

## Phase 4 — Investigate implementation-specific behaviour

Choose relevant investigations based on the code discovered.

### File processing
If a PR changes file processing, investigate:
- File source and format.
- Fixed-length record definitions and field offsets.
- Encoding, record length, padding, and trimming.
- Header, detail, and trailer processing.
- Parsing and validation sequence.
- Handling of malformed, missing, or duplicate records.
- Transformations and downstream consumers.
- Error reporting, retries, and partial processing.
- Memory usage and large-file handling.

### Cache processing
If a PR changes caching, investigate:
- Cache provider and cache-key generation.
- How entries are populated.
- Read-through, write-through, or cache-aside behaviour.
- Cache-hit and cache-miss flows.
- TTL, eviction, invalidation, and stale data.
- Serialization and null-value handling.
- Database or service fallback.
- Concurrency and consistency implications.

### Database processing
If a PR changes persistence, investigate:
- Queries and repositories.
- Entity mappings and database schema.
- Transaction boundaries.
- Insert, update, and delete behaviour.
- Data consistency and migration effects.
- Query performance and indexing.

### APIs, messages, and integrations
If relevant, investigate:
- Request/event entry points.
- Payloads and schema changes.
- Mapping and validation.
- External dependencies.
- Consumer behaviour.
- Error handling, retries, and compatibility.

### Fields and data transformations
For existing or new fields whose behaviour matters:
- Trace their origin.
- Identify assignments and transformations.
- Locate important consumers.
- Explain how their values influence processing.
- Identify persistence or external transmission.

Do not generate an exhaustive inventory of unrelated fields. Prioritise fields directly involved in changed behaviour.

## Phase 5 — Analyse module and system impact

For every affected module, explain:
- Its role in the application.
- What the PR changed.
- How the change interacts with other modules.
- Direct and indirect dependencies.
- Existing behaviour potentially affected.
- Interfaces or contracts that changed.
- Operational or deployment implications.

Create a module-level dependency diagram.

Distinguish directly modified modules from modules indirectly affected through calls, shared data, or contracts.

## Phase 6 — Review the implementation critically

Investigate potential:
- Logic defects and incorrect edge-case handling.
- Missing or inconsistent validation.
- Null and exception-handling problems.
- File-format compatibility issues.
- Cache consistency or invalidation problems.
- Database integrity or transaction issues.
- Race conditions and concurrency defects.
- Performance and scalability bottlenecks.
- Security or sensitive-data exposure.
- API and messaging compatibility risks.
- Unnecessary complexity or duplication.
- Missing logging, metrics, or diagnostics.
- Test coverage gaps.

For every finding, include:
- Exact code evidence.
- Problem explanation.
- Possible failure scenario.
- Severity and confidence.
- Recommended fix or verification.
- Potential alternative design and trade-offs.

Separate confirmed defects, potential risks, and open questions.

Do not invent problems merely to populate the report.

## Phase 7 — Cross-PR analysis

After analysing each PR individually, investigate how the four implementations interact.

Determine:
- Whether they belong to one feature or separate features.
- Whether any PR depends on another.
- Whether they modify common execution paths.
- Whether they share data structures, caches, tables, APIs, or events.
- Whether they introduce conflicting assumptions.
- Whether their combined behaviour differs from their individual behaviour.
- Whether integration tests or additional safeguards are needed.

Trace the combined runtime flow wherever the PRs are connected.

## Phase 8 — Generate the final impact document

Create `feature-impact-analysis.md` with these sections:

1. Executive summary.
2. Functional overview and business context.
3. PR inventory and relationships.
4. Detailed implementation analysis for each PR.
5. Before-versus-after behaviour.
6. End-to-end runtime execution flows.
7. Module-level impact and dependencies.
8. File, cache, database, API, messaging, or other processing details as applicable.
9. Relevant field and data-flow analysis.
10. Cross-PR interactions and combined behaviour.
11. Existing functionality potentially affected.
12. Error scenarios and edge cases.
13. Testing coverage and missing checks.
14. Confirmed issues, potential risks, and open questions.
15. Alternative solutions with trade-offs.
16. Prioritised recommendations.
17. Code evidence and reference index.

Include:
- Clear technical explanations.
- Real method and class names.
- Verified file paths and line references.
- Before-and-after comparisons.
- Execution sequence diagrams.
- Tables for module impact, risks, and recommendations.
- Concrete examples based on the actual code.

The final document should enable a new engineer to explain how the implementation works, how it affects the system, and what additional engineering work may be appropriate.

## Execution strategy

Do not attempt a superficial one-pass summary of all four PRs.

1. Verify repository and PR access.
2. Collect and preserve evidence for all four PRs.
3. Analyse PR 1 deeply.
4. Analyse PR 2 deeply.
5. Analyse PR 3 deeply.
6. Analyse PR 4 deeply.
7. Perform cross-PR integration analysis.
8. Conduct a separate critical review.
9. Generate and verify the final document.

If context becomes limited, preserve completed findings in structured Markdown notes before continuing.

Do not claim that code was executed or tests passed unless they were actually run.

Start by verifying GitHub access and identifying the modules and types of changes in each PR.
