Act as a senior Java/Spring Boot migration engineer and Python tooling architect. Build a complete, runnable local mini-project named spring-migration-validator.

## CONTEXT
I am migrating 18 microservice repositories from Spring Boot 2.7.18 / JDK 17 to Spring Boot 3.5.16 / JDK 21 using OpenRewrite. The migration is in progress: the baseline applications run locally, while migrated applications currently have compile/runtime issues.

Both source trees are already checked out locally:
- Code/EdgeManager/ — baseline develop branch
- Code/spring-boot-migrations/ — migrated pre-dev branch

The shared cloud-device-common SDK is a published Maven artifact and is a multi-module project containing:
1. cloud-device-common
2. cloud-em-model
3. cloud-em-utils
4. cloud-em-test

All 18 services consume the SDK, potentially at different versions.

Languages: Java and Kotlin. Configurations include @Configuration, @Bean, @Profile, @Value, @ConfigurationProperties, conditional annotations, imports, component scanning, XML Spring configuration, application.properties, application.yml, Maven POMs, and Spring Security XML migrated to Java configuration.

Known profiles include local, test, k8scloud, kubernates, kube-test. Profile activation is currently managed through Maven/IDE run configurations.

## OBJECTIVE
Create a local Python-first configuration migration validation tool that compares baseline and migrated source code and produces CSV reports plus a Markdown summary. It must identify missing, changed, added, renamed, and potentially behaviorally different configurations.

It must work even when migrated code cannot compile or start. Static analysis must be independent of runtime analysis.

## REQUIRED WORKFLOW
1. Discover services and SDK modules in both source trees.
2. Allow explicit repository mapping when folder names differ.
3. Record Git branch, commit SHA, and working-tree status for provenance.
4. Extract static configuration inventories from Java, Kotlin, XML, properties, YAML, and Maven POM files.
5. Resolve profile expressions and configuration relationships as far as statically possible.
6. Compare old and new inventories using exact matching and configurable semantic mappings.
7. Generate clear CSV reports and a Markdown summary.
8. Optionally collect runtime evidence from baseline and migrated services when they can start.
9. Merge runtime observations with static findings without treating missing runtime evidence as proof of missing configuration.

## STATIC EXTRACTION REQUIREMENTS
Java and Kotlin:
- @Configuration, @Component, @ComponentScan, @Import, @ImportResource
- @Bean methods, names, return types, qualifiers, primary, scope, dependencies
- @Profile and profile expressions
- @Conditional, @ConditionalOnProperty, @ConditionalOnClass, @ConditionalOnBean, @ConditionalOnMissingBean
- @Value, @ConfigurationProperties, @PropertySource
- Environment.getProperty, Binder, property placeholders
- SecurityFilterChain and related security configuration
- Bean factory methods, configuration inheritance, relevant Kotlin constructs
- Preserve original annotation expressions and source locations.

XML:
- Bean definitions, IDs, classes, references, imports, component scanning
- Property placeholders and XML security namespace elements
- Spring Security, OAuth2, filters, authentication providers, entry points
- Namespace and schema declarations
- Map old XML security configurations to new Java security capabilities using configurable mappings.

Properties/YAML:
- application.properties, application.yml, application-<profile>.properties/yml
- Multi-document YAML and spring.config.activate.on-profile
- Property declarations, placeholders, imports, includes, profile groups
- Property references in code and XML
- Preserve declarations separately from consumers and runtime effective values.
- Handle duplicate keys and precedence where possible.

Maven:
- Effective POM where Maven can resolve it, raw POM as fallback
- Parent POM, modules, dependencies, dependencyManagement, profiles, exclusions
- Spring Boot and Java versions
- cloud-device-common artifact coordinates and versions
- Record uncertainty when dependency resolution fails.

## SDK REQUIREMENTS
Treat the four SDK modules as separate inventory units and as a shared dependency graph.

Record:
- Module dependencies and Maven coordinates
- SDK configurations, beans, profiles, properties, XML resources
- Which services consume which SDK versions
- Differences between SDK source and the artifact resolved by a service
- Potential stale published-artifact issues

Do not assume local SDK source is what a service actually loads.

## PROFILE ANALYSIS
Support:
local, test, k8scloud, kubernates, kube-test

Allow additional profiles through configuration.

Implement correct handling for @Profile expressions, negation, conjunction, disjunction, active profiles, includes, profile groups, imports, component scans, conditional YAML documents, and conditional annotations where feasible.

Distinguish:
- Declared
- Statically eligible
- Reachable through imports/scanning
- Runtime observed
- Unknown / not evaluated

Do not claim a configuration loads just because its profile annotation matches.

## COMPARISON
Create stable logical IDs and semantic mappings for renamed classes, beans, and XML-to-Java migrations.

Classify findings:
UNCHANGED, CHANGED, MIGRATED, MISSING, UNEXPECTED, RUNTIME_MISMATCH, NOT_TESTED, INCONCLUSIVE.

Do not automatically treat every renamed class or removed framework auto-configuration as a defect.

Every finding must include evidence: repository, relative file, line number if available, baseline element, migrated element, profile, and explanation.

## RUNTIME SUPPORT
Provide optional diagnostic support for successful application starts:
- Active/default profiles
- Registered bean names and types
- Bean definition origins
- Condition evaluation report
- Property sources and selected resolved values
- Application version and Git SHA
- Startup exceptions and failures

Provide a small reusable Spring Boot diagnostic module or sample that can be integrated into services without exposing secrets.

If a service fails before context initialization, preserve startup logs and mark runtime collection as failed/incomplete.

Do not require Actuator to be exposed publicly. Do not dump secrets, passwords, tokens, or unrestricted environment values.

Runtime comparisons must use matching profiles and clearly recorded environment inputs.

## REPORTS
Generate at minimum:
1. repository-summary.csv
2. configuration-inventory.csv
3. bean-inventory.csv
4. property-inventory.csv
5. xml-security-inventory.csv
6. dependency-inventory.csv
7. migration-differences.csv
8. runtime-observations.csv
9. runtime-discrepancies.csv
10. summary.md

CSV reports must be usable in Excel and include stable headers, UTF-8 encoding, and proper escaping.

Markdown summary must include totals by service, profile, category, and status; key unresolved issues; missing configurations; untested profiles; and limitations.

Never include secret values in reports.

## PROJECT STRUCTURE
Create a maintainable structure such as:

spring-migration-validator/
  README.md
  pyproject.toml
  config/
    repositories.yml
    profiles.yml
    capability-mappings.yml
  src/
    migration_validator/
      cli.py
      discovery/
      extractors/
      analysis/
      comparison/
      runtime/
      reporting/
  tests/
    fixtures/
      baseline/
      migrated/
    test_profiles.py
    test_java_extraction.py
    test_kotlin_extraction.py
    test_xml_extraction.py
    test_properties.py
    test_comparison.py
  examples/
  output/
  scripts/

Use actual package files and working imports, not placeholder directories.

## IMPLEMENTATION EXPECTATIONS
- Target Python 3.11+ unless the office environment requires another version.
- Prefer mature parsers over regex-only Java/Kotlin parsing.
- Investigate available parser dependencies and document installation requirements.
- Use Python for orchestration, normalization, comparison, and reporting.
- Use Java/Kotlin tooling only where needed for reliable parsing or runtime diagnostics.
- Make the tool read-only against the source repositories.
- Support Windows office laptops and macOS/Linux where practical.
- Handle missing tools, inaccessible repositories, malformed files, and parser failures gracefully.
- Never silently skip files.
- Maintain an extraction error report.
- Add unit tests using synthetic fixtures that reproduce profile-specific Redis configurations, properties, and XML-to-Java security migration.
- Include a dry-run mode and clear progress logs.
- Avoid hardcoding my actual 18 repository names or absolute paths.
- Do not invent source paths, SDK coordinates, service names, or configuration facts.

## CLI DESIGN
Provide a usable CLI with commands similar to:
- init
- discover
- extract
- compare
- runtime
- report
- validate

The exact command interface may be improved, but document it and implement it consistently.

Example intended usage:

python -m migration_validator.cli discover --config config/repositories.yml

python -m migration_validator.cli extract --config config/repositories.yml

python -m migration_validator.cli compare --profiles local,k8scloud

python -m migration_validator.cli report --format csv

Make paths configurable through YAML and CLI arguments. Include a sample repositories.yml with placeholders for the two source roots.

## EXECUTION PLAN
Work incrementally:
Phase 1: inspect the office laptop environment and available Python, Java, Maven, Git, and parser tools.
Phase 2: scaffold the project and implement repository discovery.
Phase 3: implement static extraction and tests.
Phase 4: implement profile and configuration graph analysis.
Phase 5: implement matching and migration comparison.
Phase 6: generate CSV and Markdown reports.
Phase 7: add optional runtime collection.
Phase 8: validate on synthetic fixtures and one real SDK module plus one service.

After each phase:
- Show created/modified files.
- Run relevant tests.
- Report what works and what remains.
- Do not claim completion if tests have not been executed.

Extend the mini-project with a Service Capability Matrix and a Service Dependency Graph.

For example:

EdgeManager A
 ├── cloud-device-common
 │    ├── Redis configuration
 │    └── Security configuration
 ├── PostgreSQL
 ├── Redis
 └── External REST API

The graph should identify the source of each capability and distinguish:

DECLARED — found in source code.

PROFILE_ELIGIBLE — profile and known conditions appear to match.

RUNTIME_OBSERVED — confirmed through runtime evidence.

UNKNOWN — insufficient information.

Start with a short environment assessment and a proposed implementation plan. Then create the actual project files in the current workspace, implement Phase 1 and Phase 2, and continue incrementally. Do not merely give me example code or an architecture document: create a runnable mini-project.
