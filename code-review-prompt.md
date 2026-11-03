
# Migration Code Review – Master Prompt

## 1. Objective

Perform a thorough code-level verification of the application migration:

- Spring Boot: 2.7.18 → 3.5.16
- JDK: 17 → 21
- Internal SDK: uaa-token-lib 4.1.0 → 6.0.0

The objective is to identify unintended behavioral changes, regressions, incorrect migration implementations, compatibility issues, and deviations from documented application business flows.

This is NOT a migration checklist review or a generic migration documentation exercise.

Independently analyze the actual source code and determine whether the migration is correct.

## 2. Repository and Knowledge Base Discovery

Repositories are available under:

<REPOSITORY_ROOT>
[REPLACE WITH YOUR REPOSITORIES ROOT DIRECTORY]
</REPOSITORY_ROOT>

Application knowledge base:

<KNOWLEDGE_BASE_ROOT>
[REPLACE WITH YOUR APPLICATION KNOWLEDGE BASE DIRECTORY]
</KNOWLEDGE_BASE_ROOT>

The knowledge base contains multiple Markdown (.md) documents describing application architecture, business logic, execution flows, integrations, and functional behavior.

Instructions:

- Discover all relevant repositories under the repository root.
- Automatically discover and read relevant Markdown documents from the knowledge base directory, including nested directories.
- Do not require manually supplied knowledge-base filenames.
- Use the knowledge base as the reference for understanding intended application behavior.
- Identify relationships between repositories and shared components.
- Avoid treating each repository as an isolated application when execution flows span multiple repositories.

## 3. Review Methodology

### A. Understand the Application

Before reviewing migration changes:

- Understand the purpose of each repository.
- Identify application entry points, important modules, services, controllers, repositories, configurations, and shared libraries.
- Understand business flows using the application knowledge base.
- Identify interactions between repositories and external systems.
- Identify how uaa-token-lib is consumed across repositories.

### B. Analyze Migration Changes

Inspect Git history and source code to identify migration-related changes.

Use the appropriate Git baseline, preferably:

- Compare the migrated branch against origin/develop when that represents the pre-migration baseline.
- Verify the baseline and branch before making assumptions.
- Inspect relevant commits, diffs, dependency changes, and configuration changes.

Review:

- Spring Boot 3.5.16 compatibility.
- JDK 21 compatibility.
- Jakarta namespace migration.
- Spring Framework and Spring Security changes.
- Authentication, authorization, OAuth, and token validation.
- uaa-token-lib 4.1.0 → 6.0.0 API and behavioral changes.
- Dependency upgrades, removals, and transitive dependency conflicts.
- Configuration and auto-configuration behavior.
- Database, transaction, ORM, and repository behavior.
- REST APIs, serialization, exception handling, and validation.
- Messaging, asynchronous processing, scheduling, and integration behavior.
- Logging, tracing, resilience, and HTTP client changes.
- Application startup, runtime, and deployment compatibility.

### C. Verify Business Behavior

For every significant migration-related change:

1. Understand the previous implementation.
2. Understand the migrated implementation.
3. Trace the execution flow across relevant classes and repositories.
4. Validate behavior against the application knowledge base.
5. Identify unintended behavior changes.
6. Determine whether the change is intentional, required by migration, or potentially defective.

Pay special attention to:

- Authentication and token lifecycle.
- Authorization decisions and security boundaries.
- Request processing and response generation.
- Business validations and conditional logic.
- Exception propagation and error handling.
- Downstream service invocations.
- Data persistence and transaction boundaries.
- Retry, timeout, and failure-handling behavior.
- Changes in defaults or framework behavior that could affect business execution.

### D. Cross-Repository Execution Flow Analysis

When a business flow spans multiple repositories:

- Trace the complete execution path.
- Identify which repository, module, class, and method perform each step.
- Explain the actual sequence of execution.
- Identify changes in the sequence, conditions, transformations, or responsibilities.
- Verify that shared SDK changes do not introduce inconsistencies between consumers.

Do not restrict analysis to individual files or isolated Git diffs.

### E. Independent Verification

Do not trust existing migration assessments, PASS/FAIL statuses, migration checklists, or previous conclusions.

Use them only as contextual information if necessary.

Independently verify findings against:

- Actual implementation.
- Dependency configuration.
- Application knowledge base.
- Git history.
- Tests.
- Relevant framework and SDK APIs.
- Available build and runtime evidence.

Do not assume successful compilation or passing tests guarantees correct application behavior.

## 4. Finding Classification

Report only meaningful findings:

- Critical: Security vulnerabilities, severe data corruption, or major application failure.
- High: Significant business-flow regression, authentication/authorization defect, or major functional failure.
- Medium: Incorrect behavior affecting specific scenarios, integrations, or processing paths.
- Low: Minor functional or compatibility issue with limited impact.

Distinguish between:

- Confirmed defect.
- Potential defect requiring runtime verification.
- Verification gap.
- Intentional behavior change.

Do not report speculative issues as confirmed defects.

Do not include generic migration observations, unnecessary recommendations, or issues unrelated to the migration unless they directly affect migration correctness.

## 5. Evidence Requirements

Every finding must contain:

- Severity.
- Repository name.
- Exact file path.
- Class and method.
- Relevant line number(s).
- Concise explanation of the defect.
- Expected behavior versus actual behavior, when applicable.
- Business impact or migration risk.
- Evidence supporting the conclusion.

For application-flow deviations, explicitly reference the relevant knowledge-base document and explain:

- Documented expected flow.
- Actual implementation flow.
- Where the deviation occurs.
- Its functional impact.

Use exact line numbers from the current implementation.

Avoid unsupported conclusions and unnecessary code excerpts.

## 6. Output Requirements

Create exactly ONE consolidated Markdown report:

migration-review-summary.md

Save it under the repository root or the designated review output directory.

Do not create individual repository reports, separate migration reports, or unnecessary documentation files.

The report must be concise, actionable, and suitable for developers and reviewers.

Use this structure:

# Migration Review Summary

## Overall Assessment

- Migration readiness: READY / READY WITH ISSUES / NOT READY
- Critical: N
- High: N
- Medium: N
- Low: N

Provide a brief explanation of the overall assessment.

## Findings

| Severity | Repository | Location | Finding | Impact |
|---------|------------|----------|---------|--------|

Include only actual findings.

## Application Flow Deviations

| Flow | Expected Behavior | Actual Behavior | Impact | Evidence |
|------|------------------|-----------------|--------|----------|

Include only meaningful deviations identified using the application knowledge base.

## Verification Gaps

List only important unresolved items that require runtime testing, integration testing, or additional evidence.

## Recommendation

Provide a concise migration readiness recommendation and any essential remediation actions.

## 7. Reporting Constraints

- Keep the report small and focused.
- Avoid verbose descriptions and repeated explanations.
- No repository-wise descriptive summaries.
- No generic Spring Boot migration tutorials.
- No exhaustive listing of files reviewed.
- No PASS/FAIL checklist reproduction.
- No duplicate findings.
- No unnecessary background information.
- No speculative defect claims.
- Do not omit significant cross-repository issues merely to keep the report short.

Prioritize findings that could affect production behavior, security, correctness, or application stability.

## 8. Execution Instructions

Perform the review systematically across all relevant repositories.

Use Git commands, source inspection, dependency analysis, code navigation, and available tests as needed.

If build or test execution is possible, use it as supporting evidence.

Do not modify application source code or dependency versions during the review.

Do not automatically fix findings.

Only create or update migration-review-summary.md.

If a required repository, knowledge-base document, or baseline is unavailable, clearly mention the limitation rather than assuming its contents.

Begin by discovering the repositories and knowledge base, then proceed with the review.
