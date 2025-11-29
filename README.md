# Gigvora Platform Upgrade – Addon Architecture

This repository contains the Gigvora platform upgrade delivered as a modular addon stack. Each addon extends the Sociopro core without entangling routes, namespaces, or configuration, ensuring teams can enable the upgrade incrementally while preserving domain ownership.

## Upgrade at a Glance
- Addons remain fully isolated with their own service providers, configs, routes, and publish tags.
- Mobile companions mirror backend capabilities so both the Laravel host and Flutter/phone clients evolve in lockstep.
- Integration contracts require consuming another addon’s APIs instead of duplicating its data model or workflows.

## Advertisement Addon
- **Scope:** Complete ads manager spanning advertisers, campaigns, creatives, placements, targeting, reporting, forecasting, and affiliate payouts.
- **Backend package:** `Advertisement_Laravel_package`; APIs under `/api/advertisement/*`, dashboards under `/advertisement/dashboard`, views in `resources/views/vendor/advertisement/*`, config namespace `advertisement`.
- **Mobile addon:** `Advertisement_Flutter_addon` for campaign build, creative approvals, and reporting screens.
- **Ownership rule:** Central source for ads, targeting logic, keyword pricing, and affiliate settlements—other addons must integrate through its APIs.

## Freelance Addon
- **Scope:** Marketplace for gigs, projects, proposals, contracts, milestones, escrow, disputes, reviews, and dual-sided onboarding.
- **Backend package:** `freelance_laravel_package` with publishable routes/migrations and views under `publishable/resources/views/vendor/freelance`.
- **Mobile addon:** `freelance_phone_addon` (Riverpod) powering gig, project, dispute, escrow, and analytics journeys.
- **Ownership rule:** Canonical system for gigs, projects, and escrow. Jobs, live events, or utilities must reference these endpoints instead of recreating freelance flows.

## Interactive Addon
- **Scope:** Live-events stack for webinars, networking (incl. speed networking), podcasts, and broadcast interviews.
- **Backend package:** `Webinar_networking_interview_and_Podcast_Laravel_package` with routes in `routes/web.php` + `routes/api.php`, views under `resources/views/vendor/live`, config namespace `webinar_networking_interview_podcast`, publish tags `wnip-*`.
- **Mobile addon:** `webinar_networking_interview_podcast_flutter_addon` covering discovery, registration, waiting rooms, and live shells.
- **Ownership rule:** Sole owner of live/recorded event concepts; other addons subscribe to events rather than redefining webinars or interviews.

## Jobs Addon
- **Scope:** Jobs and ATS experience including search, posting, applications, pipeline stages, screening workflows, CV/cover letter storage, subscriptions, and interview scheduling.
- **Backend package:** `Jobs_Laravel_package` with API routes in `routes/api.php`, web routes in `routes/web.php`, and views under `resources/views/vendor/jobs`.
- **Mobile addon:** `Jobs_phone_addon` enabling seeker and employer journeys.
- **Ownership rule:** Canonical source for permanent job postings and ATS stages. Freelance addon continues to own gig/project concepts.

## Utilities Addon
- **Scope:** Professional networking utilities, security hardening, analytics, media enhancements, upgraded messaging/search/stories, and storage management that wrap Sociopro services.
- **Backend package:** `Utilities-Addon` with feature toggles in `config/pro_network_utilities_security_analytics.php`.
- **Mobile addon:** `utilities_phone_addon` exposing shared menus, API clients, and state containers.
- **Ownership rule:** Provides cross-cutting enhancements and must wrap (not replace) the core Sociopro domain—no duplication of ads, jobs, freelance, or live-event logic.

## Integration Guardrails
- Namespace every route group (URI + route names) before mounting into the host to avoid collisions.
- Use each addon’s config file to enable/disable upgrade features—never patch the host app config directly.
- When features intersect (e.g., recommending jobs inside freelance dashboards), always call the owning addon’s API instead of reimplementing its domain logic.
