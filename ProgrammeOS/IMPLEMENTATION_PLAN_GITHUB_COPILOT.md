# ProgrammeOS — GitHub Copilot Implementation Plan

## Project Overview

Build an MVP for **ProgrammeOS**, a WhatsApp-first SaaS platform for running high-trust grants, accelerator, livelihood, and SME-support programmes.

The platform must support:
- multi-tenant workspaces
- programme and cohort management
- participant onboarding
- WhatsApp messaging abstraction
- milestone and evidence submission
- reviewer approval workflows
- payout batch preparation
- audit logging
- integrity flags / fraud support
- AI-assisted summaries and reporting support

## Product Principles

1. WhatsApp-first for participant interaction
2. Deterministic control for workflow, approvals, payouts, and audit
3. AI only assists; it does not make final decisions
4. Auditability by design
5. Strong RBAC and tenant-aware access
6. Production-grade code quality and modular architecture

## Required Stack

### Frontend
- Next.js App Router
- TypeScript
- Tailwind CSS
- shadcn/ui
- React Hook Form
- Zod
- TanStack Query
- TanStack Table

### Backend
- Next.js route handlers / server actions
- TypeScript domain services
- Prisma ORM
- PostgreSQL
- Redis
- S3-compatible file storage
- optional Python FastAPI service for AI tasks

### Architecture
- monorepo
- modular domain structure
- provider abstractions for messaging, payments, and AI
- clean separation of UI, domain logic, and infrastructure

---

# Repository Structure

Use a monorepo structure like this:

/apps
  /web
  /ai-service (optional later)

/packages
  /ui
  /db
  /core
  /auth
  /tenants
  /programmes
  /participants
  /milestones
  /evidence
  /workflows
  /payouts
  /audit
  /integrity
  /messaging
  /ai
  /config
  /types
  /utils

---

# Delivery Phases

Implement in the following phases.

## Phase 1 — Scaffold and foundations

### Objectives
- setup monorepo
- setup Next.js app
- setup Prisma and Postgres
- setup Tailwind and shadcn/ui
- setup auth
- setup tenant model
- seed demo data

### Deliverables
- working monorepo
- login flow
- base app shell
- database schema initial migration
- demo tenant and demo users
- README with setup instructions

### Copilot Tasks
1. Create monorepo folder structure
2. Configure TypeScript paths and shared packages
3. Setup Next.js App Router app
4. Add Tailwind and shadcn/ui
5. Setup Prisma schema and DB client package
6. Implement auth foundation
7. Create tenant-aware middleware
8. Add seed scripts

### Acceptance Criteria
- app runs locally
- database migrates successfully
- demo login works
- tenant data can be seeded
- dashboard shell loads

---

## Phase 2 — Programme and cohort management

### Objectives
- allow tenant users to create and manage programmes and cohorts
- support milestone templates and assignment rules

### Deliverables
- programme list page
- programme detail page
- cohort list/detail page
- milestone template CRUD
- programme status management

### Domain Entities
- Programme
- Cohort
- MilestoneTemplate

### Copilot Tasks
1. Create Prisma models for Programme, Cohort, MilestoneTemplate
2. Generate CRUD service layer
3. Add Zod schemas for validation
4. Create programme pages in Next.js
5. Build forms with React Hook Form
6. Add tables for list views
7. Add audit events for create/update/delete

### Acceptance Criteria
- tenant admin can create programme
- programme manager can create cohort
- milestone templates can be configured
- changes appear in UI
- all writes create audit logs

---

## Phase 3 — Participant onboarding

### Objectives
- create participant records
- capture consent and profile data
- support onboarding state

### Deliverables
- participant list page
- participant detail page
- participant create/edit flow
- consent record support
- timeline / status history

### Domain Entities
- Participant
- ParticipantProfile
- ConsentRecord
- ParticipantStatusEvent
- ParticipantDocument

### Copilot Tasks
1. Add participant-related Prisma models
2. Build participant services and validation
3. Implement participant pages and forms
4. Add document upload integration
5. Add status timeline UI
6. Add audit logging

### Acceptance Criteria
- participant can be created and linked to a cohort
- consent status is recorded
- onboarding status is visible
- documents can be attached
- participant timeline is visible

---

## Phase 4 — Messaging abstraction and WhatsApp mock provider

### Objectives
- create a provider-based messaging abstraction
- support inbound/outbound message persistence
- support mock WhatsApp flows for local development

### Deliverables
- messaging provider interface
- mock WhatsApp provider
- webhook endpoint
- conversation model
- message event persistence
- session state storage
- idempotency handling

### Domain Entities
- MessagingChannel
- Conversation
- MessageEvent
- SessionState

### Copilot Tasks
1. Define messaging provider TypeScript interface
2. Implement mock WhatsApp provider
3. Build webhook route handler
4. Add message persistence in DB
5. Implement idempotency key logic using Redis
6. Link conversations to participants
7. Add conversation viewer in admin UI

### Acceptance Criteria
- inbound webhook can create/update a conversation
- duplicate webhook events are ignored safely
- outbound messages can be stored and mocked
- conversation links to a participant when matched
- local testing is easy

---

## Phase 5 — Milestones and evidence submission

### Objectives
- assign milestones to participants
- accept evidence submission
- enable reviewer actions

### Deliverables
- participant milestone records
- evidence upload flow
- evidence detail page
- review queue
- comments and review actions

### Domain Entities
- ParticipantMilestone
- EvidenceSubmission
- EvidenceReview
- ReviewComment

### Copilot Tasks
1. Extend schema for participant milestones and evidence
2. Build evidence upload API and UI
3. Store files in S3-compatible storage
4. Create review queue page
5. Implement approve / reject / request clarification actions
6. Add comments and activity timeline
7. Add audit events

### Acceptance Criteria
- evidence can be uploaded and linked correctly
- reviewer can act on evidence
- milestone status updates correctly
- review history is preserved
- uploads are securely stored

---

## Phase 6 — Deterministic workflow and approvals

### Objectives
- formalize state transitions
- enforce role-based actions
- support queue-based review assignment

### Deliverables
- workflow transition service
- review assignment service
- approval state machine
- status badges and action controls in UI

### Domain Entities
- WorkflowState
- ActionLog
- ReviewAssignment

### Copilot Tasks
1. Create workflow enums and service functions
2. Implement legal transitions only
3. Enforce role checks in services
4. Add assignment queue logic
5. Reflect workflow state in UI
6. Log all actions to audit system

### Acceptance Criteria
- invalid state transitions are blocked
- reviewer assignment works
- approvals are role-controlled
- UI reflects current state correctly
- workflow actions are audited

---

## Phase 7 — Payout operations

### Objectives
- prepare payout-ready records from approved milestones
- support payout batching and export
- keep payment integration provider-based

### Deliverables
- payout batch creation
- payout item list
- CSV export
- mock payment adapter
- payout status tracking

### Domain Entities
- PayoutBatch
- PayoutItem
- PaymentAdapterLog

### Copilot Tasks
1. Add payout models to Prisma
2. Build service to select approved payout-ready records
3. Create payout batch UI
4. Implement CSV export
5. Implement mock payment adapter interface
6. Track payout statuses
7. Notify participants through messaging abstraction

### Acceptance Criteria
- payout batch can be created
- payout items are traceable
- CSV export works
- statuses can be updated
- notifications can be triggered
- no real payment is executed by default

---

## Phase 8 — Audit and observability

### Objectives
- create a reliable audit trail
- support searchable audit views
- make all critical actions observable

### Deliverables
- audit event service
- audit middleware/helper
- audit log UI with filters
- metadata snapshots for key events

### Domain Entities
- AuditEvent

### Copilot Tasks
1. Implement audit service package
2. Add helper for all write operations
3. Build audit log page with filtering
4. Add entity-centric views for audit history
5. Ensure event metadata includes actor, role, entity, timestamp, and context

### Acceptance Criteria
- key actions are captured
- audit logs are searchable
- audit history visible from participant/programme context
- metadata is structured and useful

---

## Phase 9 — Integrity and fraud support

### Objectives
- create explainable rules-based integrity checks
- route suspicious activity into cases

### Deliverables
- duplicate phone detection
- repeated bank detail detection placeholder
- repeated document signal placeholder
- suspicious velocity rule
- integrity case queue

### Domain Entities
- RiskFlag
- IntegritySignal
- IntegrityCase

### Copilot Tasks
1. Add integrity-related schema
2. Create rule engine for simple explainable checks
3. Generate risk flags during relevant workflows
4. Create integrity case queue page
5. Show reasons in plain English
6. Add audit events and assignment support

### Acceptance Criteria
- suspicious cases are generated
- reasons are visible to reviewers
- flags do not auto-reject by themselves
- cases can be triaged and resolved

---

## Phase 10 — AI-assisted operator support

### Objectives
- add safe, narrow AI assistance
- improve operator productivity without bypassing controls

### Deliverables
- AI provider abstraction
- evidence summary generation
- reviewer summary generation
- cohort reporting draft generation
- participant reply suggestion support
- AI output persistence and review

### Domain Entities
- AIJob
- AIOutput
- AIReviewDecision

### Copilot Tasks
1. Create AI provider interface
2. Implement service methods:
   - summarizeEvidence()
   - draftReviewerSummary()
   - generateCohortSummary()
   - suggestParticipantReply()
3. Store prompts, outputs, confidence, timestamps, and metadata
4. Build UI panels to display AI suggestions
5. Ensure outputs are clearly marked as suggestions
6. Add review/accept/reject flows where needed

### Acceptance Criteria
- AI output can be generated and viewed
- operator can use suggestions safely
- no AI output changes approval or payout state directly
- AI usage is logged

---

## Phase 11 — UX refinement and testing

### Objectives
- make the console feel production-grade
- add tests for core logic and flows

### Deliverables
- polished navigation
- status badges and timelines
- test suite
- better error states
- README improvements

### Copilot Tasks
1. Improve side navigation and page layouts
2. Add loading, empty, and error states
3. Add unit tests for workflow logic
4. Add integration tests for evidence review and payout batching
5. Add webhook idempotency tests
6. Add seed demo walkthrough instructions

### Acceptance Criteria
- app is demo-ready
- core flows are covered by tests
- README enables a new dev to run locally
- UX feels coherent and enterprise-ready

---

# Coding Standards

## General
- use TypeScript everywhere in the web app
- prefer explicit types
- use Zod for all input validation
- use service-layer architecture rather than fat route handlers
- separate domain logic from UI rendering
- keep components small and reusable

## Backend
- every write path should create audit events
- every tenant-scoped query must enforce tenant filtering
- sensitive actions must check role permissions explicitly
- webhook handlers must be idempotent

## AI
- AI outputs must be stored as suggestions
- do not let AI directly trigger payout or approval state changes
- include confidence or metadata where possible

## Messaging
- all messaging should go through provider interface
- do not hardcode one BSP too early
- implement mock/local dev support first

---

# Key Screens to Build

1. Login page
2. Dashboard
3. Programme list
4. Programme detail
5. Cohort detail
6. Participant list
7. Participant detail
8. Milestone review queue
9. Evidence detail
10. Payout batch page
11. Integrity case queue
12. Audit log page
13. AI summary/report panel

---

# Seed Data Requirements

Seed a demo environment with:
- 1 tenant
- 2 programmes
- 2 cohorts
- 20 participants
- milestone templates
- sample evidence submissions
- approved and rejected reviews
- 1 payout batch
- several integrity cases
- several AI outputs

The seed data should make the app immediately demoable.

---

# How to Use This Plan with GitHub Copilot

When implementing, work phase by phase.

For each phase:
1. create or update the relevant Prisma models
2. create service layer code
3. add validation schemas
4. add API routes or server actions
5. build the corresponding UI
6. add audit events
7. add tests

Do not jump ahead to advanced features before completing the core deterministic workflows.

---

# Immediate First Task for Copilot

Start by generating:
1. monorepo folder structure
2. initial package.json setup
3. Next.js app shell
4. Prisma schema draft
5. auth foundation
6. tenant-aware layout
7. seed script outline

Then proceed phase by phase.
