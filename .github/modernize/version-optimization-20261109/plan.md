# Modernization Plan: Compatible Version and Runtime Optimization

**Project**: Kotlin_SpringBoot-java-docker

## Technical Framework

- **Language**: Java 25 (already applied)
- **Framework**: Spring Boot 4.0.8; Spring Cloud 2025.1.1
- **Build Tool**: Maven Wrapper; Node.js v24.11.1 and npm 11.6.4 for frontend assets
- **Database and cache**: PostgreSQL and Redis in Docker Compose; Vault dev service
- **Key Dependencies**: Spring Security, Spring Data Redis, MyBatis, Flyway, WebAuthn4J
- **Container setup**: Eclipse Temurin 25 JRE; Compose currently uses floating `latest`
  tags for PostgreSQL, Redis, and Vault

## Overview

This work will review and improve the application's dependency and local runtime
versions while preserving its existing Java 25 baseline and running Compose data.
It will make only stable, compatible updates that can pass the project's build and
tests, make frontend asset generation reproducible, and validate the rebuilt app
in Docker Desktop without removing existing services or volumes.

The assessment identifies PostgreSQL, Redis, Spring Cloud Vault, and environment
configuration in the application. This plan addresses them only where they affect
dependency compatibility, image versioning, or the existing local Compose
runtime; it does not migrate those services to Azure or change their data stores.

## Migration Impact Summary

| Area | Current state | Planned outcome | Scope |
|---|---|---|---|
| Java/Spring/Maven | Java 25, Boot 4.0.8 | Keep or update compatible stable versions | No Java rollback |
| Frontend | Node/npm pinned; npm deps use `latest` | Reproducible, tested toolchain and lockfile | Preserve generated assets |
| Compose images | PostgreSQL, Redis, Vault use `latest` | Pin verified compatible versions | Preserve services and data |
| Deployment | Local Docker Compose | Rebuild and verify in Docker Desktop | No Azure deployment |

## Execution Constraints and Validation

- Check current stable releases and compatibility when executing; do not assume
  that a newer major version is safe. Keep Java 25 and Spring Boot 4 unless a
  verified compatible update is available and the build/tests prove it.
- Lock frontend dependency versions and keep the lockfile consistent. Regenerate
  `src/main/resources/static/js` assets as required: those generated files are
  ignored by Git but are needed by the packaged/redeployed application.
- Inspect currently running image versions, volume mappings, and data before any
  database or cache image change. Do not change named-volume identity or data
  paths, run destructive volume cleanup, or perform a major data-service upgrade
  without a tested backup/restore and migration path. Defer such an update if its
  safety cannot be established.
- Build and run the project's tests, then rebuild/redeploy the affected app
  service in Docker Desktop and verify startup, health, logs, and application
  behavior while preserving the existing database/cache data and services.
- Only assess CVEs for dependency or image versions changed by this plan; no
  unrelated security audit is in scope.

## Out of Scope

- Repeating the completed Java 25 upgrade or changing Java preview-feature
  settings already removed.
- Azure service migration, Azure infrastructure provisioning, or Azure deploy.
- Unverified breaking-major dependency upgrades, destructive Compose operations,
  or database/cache volume upgrades without a safety-checked migration.
