# hospital-performance-system

> Enterprise hospital performance management platform — 10+ years in production, handling indicators across all hospital sectors with third-party API integrations and federal healthcare regulatory compliance.

---

## Overview

This repository documents the architecture and technical decisions behind a hospital performance management system I developed and maintained for 5 years (2021–2026) at NCI Innova.

The system has been running in production for **10+ years**, serving hospital institutions with compliance requirements aligned to Brazilian federal healthcare regulations and integration with national public health data infrastructure.

> ⚠️ This repository contains documentation only. No proprietary code, client data, or sensitive information is included.

---

## What the System Does

A comprehensive platform for monitoring and managing hospital performance indicators across all major sectors:

- **Clinical** — patient flow, occupancy rates, clinical outcome indicators
- **Financial** — revenue, cost centers, budget tracking, financial compliance
- **Operational** — resource utilization, process efficiency, capacity management
- **HR & People Management** — workforce indicators, scheduling, performance metrics
- **Compliance** — regulatory adherence, audit trails, reporting

---

## Architecture

| Layer | Technology |
|---|---|
| Language | Java 8 |
| Framework | Spring Framework + Spring Boot |
| Security | Spring Security |
| Database | PostgreSQL |
| API | Public REST API for third-party consumption |
| Frontend | JavaScript, jQuery, Ajax, Handlebars, HTML, CSS |
| Infrastructure | AWS Elastic Beanstalk |

---

## Key Technical Challenges

**Legacy Stability**
Maintaining and evolving a system with 10+ years of production history requires deep understanding of the existing data model, business rules accumulated over a decade, and careful migration strategies that preserve stability while modernizing components.

**Security at Scale**
Implementing Spring Security across a system handling sensitive healthcare data — managing granular access control, audit logging, and data protection requirements across multiple user roles and institution types.

**Third-Party API Design**
Designing a public REST API that exposes hospital performance data to external consumers while maintaining data integrity, access control, and versioning stability over time.

**Regulatory Compliance**
Building reporting and data management features compliant with Brazilian federal healthcare regulatory standards, requiring close collaboration with domain experts and ongoing adaptation as regulations evolve.

---

## Scale & Impact

- **10+ years** in continuous production
- Indicators across **all hospital sectors** — financial, clinical, operational, HR, compliance
- **Public REST API** consumed by third-party systems
- Compliant with **Brazilian federal healthcare regulatory standards**
- Integrated with **national public health data infrastructure**
- Hosted on **AWS** with production-grade reliability requirements

---

## My Involvement

Over 5 years I was involved in every layer of the system:

- Gathering and refining client requirements
- Backend development and module evolution in Java / Spring Boot
- Spring Security implementation and access control management
- Public REST API design and maintenance
- Database schema evolution and migration
- Frontend interactions with jQuery, Ajax, and Handlebars
- AWS infrastructure management via Elastic Beanstalk
- Deploy and production monitoring
- Introduction of AI-assisted development workflows for safe evolution of a complex legacy codebase

---

## Author

**Lucas da Silva Santos**
Full Stack Developer
[LinkedIn](https://linkedin.com/in/lucas-da-silva-santos-a46879285)
