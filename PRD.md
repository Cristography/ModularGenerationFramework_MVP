# Part 1 — Product Requirements Document (PRD)

## 1.1 Problem statement

Content creators and developers repeatedly rebuild the same structural foundations for every project they make — slide layouts, website sections, resume structures. The boilerplate is always the same; only the content and visual theme differ. There is no good platform that separates these three concerns cleanly, making it impossible to share structure without also locking in style and data.

Existing tools either bundle everything together (Canva, PowerPoint) or are too low-level (raw HTML/CSS). There is no middle ground that lets you share a reusable structural template, clone it for private use, fill in your own content — and optionally let AI do that filling for you.

## 1.2 Vision

MGF (Modular Generation Framework) is a collaborative platform where structure, style, and content are fully separated. Contributors share templates. Users clone them into private projects and fill in their real content, manually or via AI. The result is always a clean, renderable output file — with every layer independently swappable.

## 1.3 Users & roles

| Role | Description | Key permissions |
|------|-------------|-----------------|
| Guest (unauthenticated) | Visitor browsing the platform | Browse public templates, view template detail, read comments |
| User (authenticated) | Registered member | All of the above + create templates, clone, manage projects, upvote, comment |
| Admin | Platform administrator | All of the above + manage types, moderate content, delete any resource |

## 1.4 User stories

### Guest
- As a guest I want to browse public templates filtered by type so I can find what I need quickly.
- As a guest I want to see the file structure of a template before deciding to register.
- As a guest I want to read comments on a template to understand its quality.

### Authenticated user — templates
- As a user I want to create a template by uploading HTML, CSS, JSON, and MD files so I can share my structure with the community.
- As a user I want to choose the visibility of my template (public, private, unlisted) so I control who can find it.
- As a user I want to use AI to generate a full template from a text prompt so I can skip the initial scaffolding.
- As a user I want to upvote templates I find useful so quality templates get surfaced.
- As a user I want to comment on and reply to comments on templates so I can give feedback to authors.

### Authenticated user — projects
- As a user I want to clone any public template into a private project so I can personalise it without affecting the original.
- As a user I want to edit content fields in a visual editor without touching the HTML so I do not need to know code.
- As a user I want to change CSS variable values in the theme panel so I can restyle the project without editing raw CSS.
- As a user I want to use AI to fill actual_data.json from a text description so I can populate all slide content in one step.
- As a user I want to preview the rendered project so I can see the final output before exporting.
- As a user I want to export the project as a zip so I can deploy or present it independently.
- As a user I want the platform to support Arabic (RTL) so I can create content in my native language.

### Admin
- As an admin I want to add and manage template types so the type list stays accurate as the platform grows.

## 1.5 Scope — MoSCoW

| Priority | Feature | Notes |
|----------|---------|-------|
| Must have | User registration and authentication | Sanctum token-based |
| Must have | Create, read, update, delete templates | With visibility control |
| Must have | File upload per layer (structure/style/content) | Text content stored in DB |
| Must have | Clone template → private project | Copies all files |
| Must have | Project editor — content field editing | Right panel Content tab |
| Must have | Project editor — CSS variable editing | Right panel Theme tab |
| Must have | Upvotes on templates | One per user, unique constraint |
| Must have | Comments and replies on templates | Self-referencing parent_id |
| Must have | AI data.json generation in editor | POST /ai/projects/{id}/data |
| Should have | AI full template generation page | POST /ai/templates/generate |
| Should have | Project render + export | Merge layers → output file + zip |
| Should have | RTL / multi-locale support | locale + direction on Project |
| Should have | AI job history panel | GET /ai/jobs |
| Could have | AI style layer regeneration | POST /ai/projects/{id}/style |
| Could have | AI auto-tag suggestion | POST /ai/templates/{id}/tags |
| Could have | Template versioning (git-like history) | Out of semester scope |
| Won't have | Real-time collaboration | Future version |
| Won't have | Binary file storage (images, video) | Phase 2 only |
| Won't have | Payments / marketplace monetisation | Out of scope |

## 1.6 Success metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Template creation | User can create a template with 3+ files in under 5 minutes | Manual QA |
| Clone to editor | Clone → editor loads in under 2 seconds on local network | Browser DevTools timing |
| AI data generation | AI fills data.json in under 15 seconds on claude-sonnet-4-5 | AI_JOB created_at vs done_at |
| API error rate | < 2% of all requests return 5xx in staging | Laravel Telescope logs |
| Test coverage | ≥ 70% line coverage on backend feature tests | php artisan test --coverage |
| RTL rendering | Arabic project renders correctly with dir=rtl in output HTML | Manual QA with Arabic content |

## 1.7 Out of scope
- Real-time multiplayer editing
- Native mobile app
- Payment processing or premium tiers
- WYSIWYG HTML structure editing (layout HTML is edited as plain text)
- CDN-backed binary asset storage (images stay out of scope for semester)