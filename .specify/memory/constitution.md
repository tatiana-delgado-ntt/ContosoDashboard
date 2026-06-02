<!--
SYNC IMPACT REPORT
==================
Version Change: INITIAL → 1.0.0
Constitution Created: 2026-06-02

New Principles:
1. Spec-Driven Development (added)
2. Offline-First Architecture (added)
3. Security by Design (added)
4. Test-First Development (added)
5. Clean Architecture (added)

New Sections:
- Technology Standards (added)
- Development Workflow (added)
- Governance (added)

Templates Status:
✅ spec-template.md - Aligned with spec-driven principle
✅ plan-template.md - Aligned with constitution check gates
✅ tasks-template.md - Aligned with test-first and phase structure
⚠ Command files - Pending review for agent-agnostic language

Follow-up TODOs: None
-->

# ContosoDashboard Constitution

## Core Principles

### I. Spec-Driven Development (NON-NEGOTIABLE)

Every feature MUST begin with a specification before any code is written. The specification workflow is mandatory:

- Feature specifications created in `.specify/specs/[###-feature-name]/spec.md` using the spec template
- Specifications MUST include prioritized user stories (P1, P2, P3...) that are independently testable
- Each user story MUST deliver standalone value (MVP principle)
- Implementation plans MUST be derived from approved specifications
- Tasks MUST be organized by user story to enable incremental delivery
- No code implementation without prior specification approval

**Rationale**: Spec-driven development ensures alignment with user needs, enables clear communication with stakeholders, and provides a reference for testing and validation. This training project demonstrates production-grade specification practices.

### II. Offline-First Architecture

All features MUST be designed for offline/local operation with a clear migration path to Azure cloud services:

- Primary implementation uses local resources (SQL Server LocalDB, local file storage)
- Service abstractions (interfaces) MUST enable cloud provider swapping without business logic changes
- Database operations via Entity Framework Core for cloud portability
- File storage via `IFileStorageService` interface pattern (local implementation, future Azure Blob)
- Configuration designed for environment-based switching (local vs. Azure)
- No hardcoded dependencies on cloud-specific APIs in business logic

**Rationale**: Offline-first architecture maximizes training availability (no cloud dependencies required), reduces training costs, and demonstrates proper abstraction patterns that enable seamless Azure migration for production scenarios.

### III. Security by Design (NON-NEGOTIABLE)

Security MUST be implemented at all architectural layers with defense-in-depth:

- Authorization required at page level (`[Authorize]` attribute on all protected pages)
- Authorization required at service level (prevent IDOR vulnerabilities)
- Claims-based identity with role-based access control (RBAC)
- User isolation enforced (users only access their authorized data)
- Cookie-based authentication with secure settings (sliding expiration, HttpOnly, Secure flags)
- Security headers configured (CSP, X-Frame-Options, X-XSS-Protection)
- Input validation on all user-supplied data
- File upload security (whitelist validation, path traversal protection, GUID-based filenames)
- Files stored outside `wwwroot` with controller-mediated access

**Rationale**: Multi-layer security demonstrates production security patterns. Even in training mode with mock authentication, the application structure teaches proper security architecture for Azure AD/Microsoft Entra ID migration.

### IV. Test-First Development

Tests MUST be written before implementation (Test-Driven Development):

- Acceptance scenarios defined in specification become test cases
- Tests written and reviewed before implementation code
- Tests MUST fail initially (Red phase)
- Implementation makes tests pass (Green phase)
- Refactoring maintains passing tests (Refactor phase)
- Test categories: Unit tests, Integration tests, Contract tests (as specified in spec)
- Independent testing per user story (each P1, P2, P3 story validates independently)

**Rationale**: TDD ensures code meets specifications, catches regressions early, and serves as living documentation. Test-first discipline is essential for production-quality software and aligns with spec-driven methodology.

### V. Clean Architecture

Code MUST follow clear separation of concerns with layered architecture:

- **Models** (`/Models`): Domain entities with EF Core attributes, relationships, validation
- **Data** (`/Data`): DbContext, migrations, database configuration, seed data
- **Services** (`/Services`): Business logic, authorization checks, data access orchestration
- **Pages** (`/Pages`): Blazor components and Razor pages for UI, minimal logic
- **Shared** (`/Shared`): Reusable UI components, layouts, navigation
- Service layer MUST NOT contain UI logic
- UI layer MUST NOT contain direct database access (use services)
- Models MUST NOT contain business logic (data structures only)
- Dependencies flow inward (UI → Services → Data → Models)

**Rationale**: Layered architecture improves testability, maintainability, and enables independent evolution of each layer. This structure prepares for scaling (adding APIs, mobile apps) and demonstrates enterprise patterns.

## Technology Standards

**Mandatory Technology Stack** (for consistency in training environment):

- **Framework**: ASP.NET Core 8.0 or higher
- **UI Framework**: Blazor Server (maintains server-side state for simplified training)
- **Database**: SQL Server LocalDB (offline), Entity Framework Core for ORM
- **Authentication**: Cookie-based with claims-based identity (mock for training, Azure AD ready)
- **Styling**: Bootstrap 5.3+ with Bootstrap Icons
- **Language**: C# 12+ with nullable reference types enabled
- **Testing**: xUnit, Moq, FluentAssertions (if tests implemented)

**Cloud Migration Path** (documented but not implemented in training):

- Azure SQL Database or Azure SQL Managed Instance (replaces LocalDB)
- Azure Blob Storage (replaces local file storage via `IFileStorageService` swap)
- Azure AD / Microsoft Entra ID (replaces mock authentication)
- Azure App Service (hosting)
- Application Insights (observability)

**Development Tools**:

- Visual Studio 2022 or VS Code with C# Dev Kit
- .NET 8 SDK
- SQL Server Express LocalDB (included with Visual Studio)
- Git for version control

## Development Workflow

**Spec Kit Workflow** (enforced by `.specify/extensions.yml` hooks):

1. **Constitution**: Project principles (this document) established first
2. **Git Setup**: Repository initialized with feature branch workflow
3. **Specify** (`/speckit.specify`): Create feature specification with user stories
4. **Clarify** (`/speckit.clarify`): Address underspecified areas with targeted questions
5. **Plan** (`/speckit.plan`): Generate implementation plan with constitution checks
6. **Tasks** (`/speckit.tasks`): Generate dependency-ordered task list by user story
7. **Implement** (`/speckit.implement`): Execute tasks with test-first approach
8. **Analyze** (`/speckit.analyze`): Cross-artifact consistency validation

**Git Integration** (automated via hooks):

- Feature branches follow `###-feature-name` naming convention
- Auto-commit hooks available at workflow gates (before clarify, plan, tasks)
- Main branch protected (feature branch workflow mandatory)

**Quality Gates**:

- Constitution compliance checked during planning phase
- All protected pages MUST have `[Authorize]` attribute
- All services MUST implement authorization checks
- User isolation MUST be verified in service layer
- Complexity exceptions MUST be justified in plan.md

**Documentation Requirements**:

- Feature specs include user scenarios, requirements, success criteria
- Plans include constitution check results, technical context, structure decisions
- Tasks organized by user story with independent test verification
- README updated with new features and architecture changes

## Governance

**Authority and Precedence**:

- This constitution supersedes all other development practices and preferences
- In case of conflict between constitution and individual judgment, constitution prevails
- Constitution amendments require:
  1. Written proposal with rationale
  2. Review of impact on existing features and templates
  3. Version increment (MAJOR for breaking changes, MINOR for additions, PATCH for clarifications)
  4. Update of dependent templates and documentation

**Compliance Enforcement**:

- Constitution Check gate in plan template MUST verify principle adherence
- Violations require explicit justification in Complexity Tracking section
- Code reviews MUST verify security layer enforcement (page + service authorization)
- Feature branches MUST follow spec → plan → tasks → implement workflow

**Training Context**:

- This application is for TRAINING PURPOSES ONLY (not production deployment)
- Mock authentication acceptable for training; production requires proper identity provider
- Local database acceptable for training; production requires Azure SQL
- Security architecture demonstrates patterns but training shortcuts documented in README

**Amendment Process**:

- Version follows semantic versioning (MAJOR.MINOR.PATCH)
- Ratification date preserved (original adoption)
- Last amended date updated with each change
- Sync impact report prepended as HTML comment documenting changes

**Version**: 1.0.0 | **Ratified**: 2026-06-02 | **Last Amended**: 2026-06-02
