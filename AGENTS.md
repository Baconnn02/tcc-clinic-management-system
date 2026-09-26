# AGENTS.md

## Purpose and precedence

This file defines how AI coding agents work in the TCC Clinic Management System repository. Follow explicit user instructions first, then this file, then the repository's other approved documentation. For UI or frontend work, `DESIGN.md` is the design authority; its **TCC Clinic Compatibility Profile (Controlling)** overrides the generated Claude reference where they conflict.

Do not invent clinic policies, medical rules, institutional requirements, permissions, workflows, data, validation results, or project decisions. Label genuinely new suggestions `PROPOSED`. Treat unresolved decisions that materially affect behavior, security, data, or user experience as blockers and request confirmation.

## Required orientation before changes

Before modifying the repository:

1. Read this file completely.
2. Read the root `README.md` and the README for each affected application.
3. Read `DESIGN.md` completely before any UI, UX, styling, layout, responsive, accessibility, or frontend component work.
4. Look for and read the documentation index, progress tracker, decision register, open questions, roadmap, current sprint, and relevant feature documents if they exist. At the time this file was created, those project-governance documents were not present; do not pretend they were reviewed.
5. Inspect the affected routes, components, controllers, models, migrations, services, tests, and configuration before proposing or editing code.
6. Check `git status` and preserve unrelated or pre-existing user changes. Never overwrite work merely to make the tree clean.

## Repository map and technology baseline

Use the actual structure below. Do not force a generic monorepo or `src` layout onto it.

```text
/
|- AGENTS.md                 # AI engineering rules
|- DESIGN.md                 # UI/UX authority and generated design reference
|- README.md                 # Project entry point (currently minimal)
|- frontend/                 # Primary clinic web client
|  |- src/
|  |  |- components/layout/  # Shared application shell and navigation
|  |  |- pages/              # Route-level React screens
|  |  `- services/api.js     # Shared Axios client
|  |- public/                # TCC logo and static assets
|  `- package.json
|- backend/                  # Laravel API application
|  |- app/Http/Controllers/  # HTTP request/response layer
|  |- app/Models/            # Eloquent models and relationships
|  |- config/                # Laravel configuration
|  |- database/migrations/   # Versioned schema changes
|  |- database/seeders/      # Seed data
|  |- routes/api.php         # JSON API routes
|  `- tests/                 # PHPUnit tests
|- backend/frontend/         # Secondary React scaffold; authority is unresolved
`- tcc (4).sql               # Legacy SQL artifact; not migration authority
```

Current stack:

- Backend: PHP 8.2, Laravel 12, Eloquent, Laravel Sanctum personal access tokens, PHPUnit 11, Laravel Pint, and `openai-php/client`.
- Primary frontend: React 19 with JavaScript/JSX, Vite 8, React Router 7, Tailwind CSS 4 through `@tailwindcss/vite`, Axios, Lucide React, and ESLint 10.
- API contract: JSON endpoints under Laravel's `/api` prefix; protected routes use `auth:sanctum`. The shared frontend Axios client attaches the stored bearer token.
- Deployment/configuration: `frontend/vercel.json` exists for the client; the backend has a Dockerfile. Do not infer a complete deployment process without further documentation.

The root `frontend/` is the working client authority because it contains the clinic routes, shared API client, Tailwind setup, assets, and deployment configuration. `backend/frontend/` is a smaller duplicate scaffold and `backend/resources/js` is the Laravel Vite starter surface. Do not implement or synchronize changes in either secondary frontend location unless the user confirms its purpose. Do not delete them as cleanup without explicit approval.

Treat Laravel migrations and current application behavior as the schema implementation authority. The root SQL dump is historical/reference material unless the user explicitly designates it otherwise.

## Clean and maintainable code

- Avoid spaghetti code. Apply separation of concerns and give every file, component, class, and function one clear responsibility.
- Prefer simple, readable, maintainable solutions over clever or overly abstract implementations.
- Keep nesting shallow with early returns, focused helpers, and clear state transitions where appropriate.
- Avoid unnecessarily large files. Several current pages and controllers are already large; do not add unrelated responsibilities to them. Extract only cohesive code required by the task rather than performing a broad rewrite.
- Reuse existing components, services, utilities, helpers, model relationships, query patterns, and functions when they fit.
- Do not duplicate logic to create a slightly different implementation. Extend or refactor the relevant existing behavior when that is the smallest safe change.
- Do not introduce abstractions speculatively. A shared abstraction should have a concrete responsibility and demonstrated reuse or complexity benefit.
- Do not create unnecessary files, directories, classes, packages, or dependencies.
- Keep public interfaces small and naming explicit. Remove dead code only when its ownership and lack of use have been verified.

## Project structure and code organization

Before creating anything, inspect neighboring files and place the change where this repository and its frameworks expect it.

### Laravel backend

- Keep `routes/api.php` declarative. Route closures are acceptable only for intentionally trivial endpoints; production workflows belong in controllers and supporting classes.
- Keep controllers focused on HTTP concerns: authorization, validated input, orchestration, and response mapping. Move substantial or reusable business workflows into a cohesive service or domain class under `backend/app/` when warranted.
- Use dedicated Form Request classes for validation that is complex, reused, or authorization-sensitive. Small one-off validation may remain in a controller if that matches nearby code.
- Put persistence and relationships in Eloquent models according to Laravel conventions, but do not turn models into unrelated utility containers.
- Prevent mass-assignment, over-posting, N+1 queries, and accidental exposure of sensitive fields. Select, validate, authorize, and serialize deliberately.
- Use migrations for schema evolution. Never edit an already-deployed migration merely to change a live schema unless the project is explicitly confirmed to be pre-deployment and the user requests it.
- Keep secrets and environment-specific values in environment/configuration boundaries. Never commit credentials, tokens, patient data, or local `.env` contents.
- Follow PSR-12/Laravel conventions and format PHP with Pint.

### React frontend

- Keep route-level screens in `frontend/src/pages`, shared shell/navigation in `frontend/src/components/layout`, and shared HTTP behavior in `frontend/src/services/api.js` unless a more specific existing home is established.
- Components own presentation and interaction. Move reusable data transformation, API orchestration, or complex state behavior into focused hooks/services/helpers when it reduces a real responsibility burden.
- Reuse the shared Axios instance. Do not scatter alternate API base URLs, authentication headers, or raw request clients through components.
- Preserve React Router and lazy-loaded route conventions already used by `App.jsx`.
- Use Lucide React for interface icons and Tailwind CSS 4 utilities plus the established global layers. Follow `DESIGN.md`; do not create an unrelated visual language.
- Keep utilities genuinely reusable. Do not create a generic `utils` dumping ground or a component for a single line of markup without a clear benefit.
- Preserve accessibility, responsive behavior, dark mode, loading states, failure states, and empty states when changing a screen.

### API boundaries

- The Laravel API is authoritative for protected data, validation, authorization, and business rules. Client-side checks improve usability but never replace server-side enforcement.
- Coordinate route, payload, validation, serialization, frontend consumption, error handling, tests, and documentation whenever an API contract changes.
- Preserve Sanctum protection and ownership/role checks for protected operations. Never weaken authentication to hide a client error.
- Return predictable JSON and appropriate HTTP status codes. Do not expose stack traces, secrets, database details, or internal provider errors to clients.

## Existing code and safe change policy

- Inspect before editing and preserve working behavior unless the request requires a change.
- Make the smallest clean, reviewable, and reversible change that accomplishes the task.
- If nearby code is messy, improve only the portion directly needed for the requested work. Avoid broad unrelated refactors, formatting churn, or wholesale rewrites.
- Before adding an abstraction, dependency, endpoint, component, or schema field, verify that an existing solution cannot be extended safely.
- Follow established naming and framework conventions. When existing patterns conflict, identify the conflict and choose the pattern supported by current runtime use and documentation.
- Do not replace React, Vite, Tailwind, Laravel, Eloquent, Sanctum, the database strategy, or the application architecture without explicit instruction and an approved migration plan.
- Do not add a library when the platform or an installed dependency already provides a suitable solution. Explain and document any necessary new dependency.

## Security, privacy, and data integrity

- Treat student, faculty, staff, visit, medicine, profile, and medical information as sensitive data.
- Enforce authentication, authorization, ownership, validation, file constraints, and least-privilege data access on the server.
- Never use real personal or medical data in tests, seeders, screenshots, logs, examples, prompts, or documentation.
- Avoid logging access tokens, passwords, complete request bodies containing health data, or provider secrets.
- Validate uploaded files by content/type and size, store them through an intentional storage boundary, and prevent path traversal and executable uploads.
- Use transactions for multi-record workflows that must succeed or fail together, especially inventory and clinic-visit updates.
- AI-assisted functionality must not invent medical advice or silently send sensitive records to a provider. Document and obtain approval for the exact data boundary, user disclosure, failure behavior, and server-side controls before expanding it.

## Testing and validation

Functional changes require proportionate automated tests. Add or update tests close to the affected layer; do not claim behavior is verified from inspection alone.

Use the relevant commands from their application directories:

```powershell
# Primary frontend
cd frontend
npm.cmd run lint
npm.cmd run build

# Laravel backend
cd backend
php artisan test
vendor\bin\pint.bat --test
```

When a command is unavailable or fails because of an unrelated pre-existing issue, report the exact limitation. Run focused tests during development and the relevant broader suite before completion. For UI work, automated lint/build checks do not replace browser checks of responsive layout, keyboard navigation, focus visibility, light/dark themes, loading/error/empty states, and affected workflows.

Never claim deployment, migration safety, browser behavior, provider integration, data correctness, or test success without direct evidence.

## Documentation and status discipline

- Keep `AGENTS.md` focused on coding-agent behavior, repository structure, architecture, safety, and verification.
- Keep `DESIGN.md` focused on UI/UX, visual language, interaction, responsiveness, and accessibility.
- Document significant architectural decisions and update affected API/schema/operational documentation with the implementation.
- If the project later adds a documentation index, progress tracker, changelog, backlog, sprint board, decision register, roadmap, or open-questions log, keep those artifacts synchronized after completed work.
- Use status labels precisely where project tracking is involved: `APPROVED`, `PROPOSED`, `PROVISIONAL`, `BLOCKED`, `IN PROGRESS`, `COMPLETED`, `DEFERRED`, and `OUT OF SCOPE`.
- Do not create duplicate governance documents. Update the authoritative existing file once its role is known.

## Completion report

At the end of every repository task, report:

- Files changed.
- Tests and checks actually performed, with results.
- Remaining blockers or unavailable validation.
- Material risks or assumptions.
- Recommended next action.

Keep the report evidence-based. A task is `COMPLETED` only when the requested scope is implemented, relevant documentation is synchronized, and required validation has passed or clearly identified limitations have been accepted.
