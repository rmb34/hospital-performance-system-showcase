# Hospital Performance System

Enterprise platform for collecting, validating, analyzing, and integrating hospital performance data across multiple institutions.

I worked as a core engineer on the system from 2021 to 2026, contributing across backend architecture, APIs, security, data processing, frontend maintenance, testing, releases, and production support.

[LinkedIn](https://linkedin.com/in/lucas-da-silva-santos-a46879285)

> This repository is a technical case study. The production source code, customer data, credentials, indicators, and internal documentation are proprietary and are not included.

---

## Product Overview

The platform supports hospitals that periodically submit operational, clinical, institutional, financial, and workforce information for analysis and comparison.

It centralizes data collection, validation, review, dashboards, benchmarking, imports, exports, audit history, and authenticated integrations. Different user profiles interact with the same data through permissions tied to their institution and responsibilities.

The application has been continuously evolved since 2016. That history makes backward compatibility, data integrity, incremental migration, and predictable releases as important as adding new functionality.

---

## Main Capabilities

### Indicator Collection

The system manages variables and calculated indicators over reporting periods.

It supports:

- Manual data entry.
- Monthly and annual reporting periods.
- Variable validity windows.
- Institution-specific applicability.
- Role-based entry and validation permissions.
- Retroactive-entry authorization.
- Required and advisory validation rules.
- Recalculation of dependent indicators.
- Review and correction workflows.
- Historical snapshots of submitted values.

Validation is part of the domain flow rather than a browser-only concern. API and spreadsheet imports reuse the same restrictions applied by the main application.

### Analysis and Dashboards

The analytical modules include:

- Indicator evolution over time.
- Institutional and group comparisons.
- Configurable executive dashboards.
- Benchmark views.
- Correlation between indicators.
- Outlier analysis.
- Coherence checks across periods.
- Consolidated and grouped indicators.
- Presentation-oriented videowall views.

Dashboard and comparison services preserve institution and permission context when selecting the data available to each user.

### Institutional Profiles and Questionnaires

Hospitals maintain structured institutional information used to contextualize indicators and analyses.

The platform contains:

- Institution and network records.
- Structural questionnaires.
- Institutional-profile history.
- Eligibility and participation settings.
- Questionnaire completion workflows.
- Administrative monitoring of submissions.

### Epidemiological Data

The epidemiological module processes hospital-discharge records and produces filtered analyses across multiple dimensions.

Large XLSX uploads are read with a streaming parser to avoid loading the complete workbook into memory. Imports validate the file structure, reporting period, duplicated records, field formats, and reference data before consolidating the result.

Long-running discharge imports can execute asynchronously while preserving an import history and detailed feedback for rejected or partially accepted records.

### Clinical Outcomes

The application contains dedicated modules for standardized clinical-outcome datasets across different care pathways.

These modules provide:

- Structured imports.
- Domain-specific validation.
- Export workflows.
- Indicator calculation.
- Protocol-specific dashboards.
- Consolidated outcome views.

The shared validation framework allows protocols to reuse infrastructure while keeping their own fields and domain rules isolated.

### Authenticated Integration API

Partner systems can integrate through a REST API for hospital-variable workflows.

The API provides operations for:

- Discovering available variables.
- Reading submissions for a reporting period.
- Creating records.
- Updating records.
- Removing eligible records.
- Handling retroactive-entry authorization.

Requests are authenticated with an API key associated with a user and institution. The API establishes the same authenticated and institutional context used by the internal application before executing domain services.

Swagger documentation is generated from the supported controllers. Interactive execution is disabled in the documentation interface.

### Auditing and Traceability

The system maintains audit information for:

- Variable submissions.
- Validation actions.
- Spreadsheet imports.
- Institutional changes.
- Review and correction workflows.

Records are retained to support investigation and historical reporting rather than being silently overwritten without context.

---

## Engineering Challenges

### Evolving a Long-Lived Java System

The application began in 2016 and still runs on Java 8, Spring Boot 1.4, and Spring Framework 4.3.

Work on this codebase requires balancing three goals:

- Preserve behavior accumulated over years of production use.
- Add new workflows without destabilizing shared domain classes.
- Improve safety incrementally without forcing a high-risk platform rewrite.

Changes are therefore kept localized, compatibility is explicit, and shared contracts are investigated before modification.

### Institution-Aware Access

The active institution is part of the authenticated request context. Repositories and services use that context to scope data and determine which variables, indicators, dashboards, and administrative functions are available.

Permission rules are concentrated in the established user and institution models. New functionality reuses those contracts instead of introducing parallel role checks.

This is essential in a system where a valid record identifier alone must never be enough to access another institution's information.

### One Domain, Multiple Entry Points

The browser application, REST API, and spreadsheet imports all create or update the same underlying indicator records.

The integration layers adapt their inputs, establish user and institution context, and then delegate to shared application services. This prevents each entry point from implementing a different version of permissions, reporting-period restrictions, and validation behavior.

### Large Spreadsheet Processing

Several workflows import XLSX files that can contain substantial hospital datasets.

The import pipeline uses streaming workbook readers, explicit templates, reference-data lookup, row-level validation, partial-result reporting, and asynchronous execution where processing time justifies it.

The design prioritizes actionable feedback: invalid files and records return structured reasons rather than failing with a generic upload error.

### API Concurrency and Rate Control

The authenticated API combines two protections:

- Bucket4j and JCache policies per API key and route group, plus a global route-group budget.
- A separate concurrency filter for high-cost submission operations.

Rejected requests receive HTTP `429` and a `Retry-After` header. Policies can be configured by route group without applying the API restrictions to regular pages and static assets.

### Incremental Database Evolution

PostgreSQL schema changes are versioned with Flyway. New work begins as guarded, idempotent scripts and is incorporated into the numbered migration sequence during release preparation.

This workflow supports repeated execution in controlled environments and reduces the risk of long locks or partially applied production changes.

### Legacy Frontend Maintenance

The server-rendered interface uses JavaScript, jQuery, Ajax, Handlebars, Bootstrap, LESS, and Browserify.

Rather than hiding that legacy, the project treats it as an operational constraint. Frontend changes are built through the existing asset pipeline and are kept compatible with the server-rendered application.

---

## Architecture

```text
Browser application
      |
      | form login and institution context
      v
Spring MVC controllers
      |
      v
Application services and domain rules
      |
      +---- validation and recalculation
      +---- permissions and reporting periods
      +---- auditing and history
      +---- imports and exports
      +---- dashboards and analytics
      |
      v
Spring Data JPA / Hibernate
      |
      v
PostgreSQL
```

External integrations enter through a separate authenticated boundary:

```text
Partner system
      |
      | X-API-KEY
      v
Rate and concurrency filters
      |
      v
API user + institution context
      |
      v
REST controllers and DTO validation
      |
      v
Existing application services
      |
      v
PostgreSQL + audit history
```

The application is packaged as a WAR and deployed through an AWS Elastic Beanstalk environment.

---

## Technology Stack

| Layer | Technology |
|---|---|
| Language | Java 8 |
| Framework | Spring Boot 1.4, Spring Framework 4.3 |
| Web | Spring MVC, Spring Security |
| Persistence | Spring Data JPA, Hibernate, PostgreSQL |
| Database migrations | Flyway |
| Cross-cutting behavior | Spring AOP, AspectJ |
| API documentation | Swagger 2 / Springfox |
| Rate limiting | Bucket4j, JCache, Ehcache |
| Spreadsheet processing | Apache POI-compatible streaming reader |
| File integration | AWS S3 |
| Frontend | JavaScript, jQuery, Ajax, Handlebars, Bootstrap |
| Frontend build | Browserify, LESS, npm |
| Testing | JUnit, Cucumber, HSQLDB, REST Assured, Mockito |
| Delivery | Gradle, WAR, AWS Elastic Beanstalk |

---

## Security and Data Integrity

- Browser access uses Spring Security and method-level authorization.
- Production requests are required to use HTTPS.
- API requests require a key associated with a valid system user.
- The authenticated institution is established before API domain operations run.
- Domain permissions are checked for indicator entry, validation, and administrative operations.
- Reporting-period and variable-validity restrictions are enforced by application services.
- API payloads use validated DTOs and centralized exception translation.
- Rate limits are applied per API key and globally by route group.
- High-cost API operations have an additional concurrency guard.
- Import workflows validate templates, references, periods, and record consistency.
- Submission, validation, and import operations produce audit history.
- Database changes follow versioned, guarded migration scripts.

The system operates in a regulated healthcare environment. This case study describes engineering controls without making claims about legal certification or exposing proprietary compliance documentation.

---

## Testing Strategy

The repository contains multiple test levels because the application mixes mature legacy modules with actively maintained workflows.

### Unit Tests

Focused JUnit tests protect calculations, parsers, validators, services, and permission-sensitive behavior.

### Integration Tests

Spring integration tests execute against an in-memory HSQLDB database rather than mocking persistence. They are used for repository, controller, import, and transactional behavior where the database contract matters.

### Behavior Tests

Cucumber is the active BDD workflow for end-to-end business scenarios. Older JBehave infrastructure still exists in the repository but is deprecated and is not used as the direction for new tests.

### API Tests

REST Assured and Spring MockMvc-based tests cover HTTP contracts, authentication, validation responses, tenant context, rate limiting, and failure behavior.

The verification command is chosen by risk: backend compilation for localized changes, focused unit or integration tests for altered behavior, Cucumber for active business workflows, and frontend builds when JavaScript or LESS changes.

---

## My Contribution

From 2021 to 2026, my work included:

- Refining technical requirements with product and domain stakeholders.
- Implementing and evolving Java and Spring modules.
- Maintaining institution-aware permissions and authenticated flows.
- Designing and hardening partner API endpoints.
- Building validation, import, dashboard, and reporting features.
- Evolving PostgreSQL schemas and Flyway migrations.
- Maintaining the legacy JavaScript frontend and asset pipeline.
- Creating unit, integration, API, and behavior tests.
- Investigating production issues and regressions.
- Supporting release preparation and AWS deployments.
- Introducing structured AI-assisted workflows for safer work in a large legacy codebase.

The most important skill this system developed was not familiarity with one framework. It was learning how to change a mature production system without discarding the constraints and knowledge already encoded in it.

---

## Confidentiality

This showcase intentionally excludes:

- Proprietary source code.
- Customer and institution names.
- Patient or hospital records.
- Production URLs and credentials.
- Internal indicator definitions.
- Infrastructure configuration.
- Regulatory and contractual documents.

Architecture and engineering practices can be discussed in more detail during an interview without exposing confidential information.

---

## Author

**Lucas da Silva Santos** — Full Stack Developer

[LinkedIn](https://linkedin.com/in/lucas-da-silva-santos-a46879285)
