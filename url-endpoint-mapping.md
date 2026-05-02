# MGF — Frontend Page Flows & API Endpoint Map
Version: 1.0 — MVP | Status: Draft

---

## Page Index

| Page | Path | Access |
|---|---|---|
| Landing | `/` | Public |
| Register | `/register` | Guest only |
| Login | `/login` | Guest only |
| Template Gallery | `/templates` | Public |
| Template Detail | `/templates/{id}` | Public |
| Dashboard | `/dashboard` | Auth required |
| Project Editor | `/editor/projects/{id}` | Auth + Owner |
| Settings | `/settings` | Auth required |
| Resource Library | `/resources` | Public |
| Resource Detail / New | `/resources/{id}` · `/resources/new` | Public view / Auth to create |
| Public User Profile | `/users/{id}` | Public |
| Admin Panel | `/admin` | Admin + Superadmin |

---

## 1. Landing — `/`

**Access:** Public

### User Flow
1. Arrives — sees hero, feature highlights, sample templates
2. Browses featured templates carousel (static/seeded)
3. → `/templates` Browse all templates
4. → `/login` or `/register` via CTA buttons

### API Calls
| Method | Endpoint |
|---|---|
| GET | `/types` |
| GET | `/templates?sort=popular&per_page=6` |

### Navigates To
- `/templates`
- `/login`
- `/register`

---

## 2. Register — `/register`

**Access:** Guest only (redirect authenticated users to `/dashboard`)

### User Flow
1. Fills name, email, password, confirm password
2. Submits → token stored (httpOnly cookie / memory)
3. → `/dashboard` on success
4. 422 → inline field errors shown per field
5. Link to `/login` if already has account

### API Calls
| Method | Endpoint |
|---|---|
| POST | `/auth/register` |

### Navigates To
- `/dashboard` (on success)
- `/login`

---

## 3. Login — `/login`

**Access:** Guest only (redirect authenticated users to `/dashboard`)

### User Flow
1. Fills email + password
2. Submits → token stored
3. → `/dashboard` or back to `redirect_to` query param (e.g. if redirected from fork attempt)
4. 422 → invalid credentials message
5. Link to `/register`

### API Calls
| Method | Endpoint |
|---|---|
| POST | `/auth/login` |

### Navigates To
- `/dashboard` (default)
- `redirect_to` param value (e.g. `/templates/{id}` after fork redirect)
- `/register`

---

## 4. Template Gallery — `/templates`

**Access:** Public

### User Flow
1. Lands — filter bar (type, tags, sort order), grid of template cards
2. Filters by type / tag / keyword → list refreshes reactively
3. Clicks card → template detail page
4. Clicks Fork on a card (auth required) → editor opens
5. Guest attempts fork → redirect to `/login?redirect_to=/templates/{id}`
6. Upvote / bookmark on card (auth required, toggle)

### API Calls
| Method | Endpoint | Notes |
|---|---|---|
| GET | `/types` | Populate type filter |
| GET | `/templates?q=&type_id=&tags=&sort=` | Main list, paginated |
| POST | `/templates/{id}/fork` | Auth required |
| POST | `/templates/{id}/upvote` | Auth required, toggle |
| POST | `/templates/{id}/bookmark` | Auth required, toggle |

### Navigates To
- `/templates/{id}`
- `/editor/projects/{id}` (after fork)
- `/login?redirect_to=...` (guest fork attempt)

---

## 5. Template Detail — `/templates/{id}`

**Access:** Public (read) | Auth required for fork/upvote/bookmark/comment

### User Flow
1. Views preview, metadata, author avatar, fork count, upvote count
2. Scrolls file layers panel — read-only list of template files
3. Reads comments (paginated), posts reply if authenticated
4. Clicks Fork → modal: enter project name → POST fork → redirects to editor
5. Owner only — edit metadata, change visibility, delete template
6. Upvote and bookmark toggle buttons

### API Calls
| Method | Endpoint | Who |
|---|---|---|
| GET | `/templates/{id}` | All |
| GET | `/templates/{id}/files` | All |
| GET | `/templates/{id}/comments` | All |
| POST | `/templates/{id}/comments` | Auth |
| POST | `/templates/{id}/fork` | Auth |
| POST | `/templates/{id}/upvote` | Auth |
| POST | `/templates/{id}/bookmark` | Auth |
| PUT | `/templates/{id}` | Owner / Admin |
| DELETE | `/templates/{id}` | Owner / Admin |

### Navigates To
- `/editor/projects/{id}` (after fork)
- `/users/{id}` (author link)
- `/templates` (back / after delete)

---

## 6. Dashboard — `/dashboard`

**Access:** Auth required

### User Flow
1. Lands — my projects grid, filterable by status (draft / published / archived) and type
2. Clicks project card → opens editor
3. New blank project → modal: pick type, enter name → POST → opens editor
4. New from template → navigates to `/templates`
5. Kebab menu on project card → rename, archive, delete (with confirmation)
6. My Templates tab → shows own published templates, toggle visibility inline
7. Bookmarks tab → shows bookmarked templates and resources

### API Calls
| Method | Endpoint | Notes |
|---|---|---|
| GET | `/auth/me` | Current user info |
| GET | `/types` | Type filter options |
| GET | `/projects?status=&type_id=&q=` | User's projects |
| POST | `/projects` | Create blank project |
| PUT | `/projects/{id}` | Rename / archive |
| DELETE | `/projects/{id}` | Soft delete |
| GET | `/templates` (filtered by own user) | My templates tab |
| PUT | `/templates/{id}` | Toggle visibility |

### Navigates To
- `/editor/projects/{id}`
- `/templates` (browse / new from template)
- `/settings`

---

## 7. Project Editor — `/editor/projects/{id}`

**Access:** Auth + Owner (admin can access any)

### User Flow
1. Loads — file tree left panel, live preview centre, CSS attribute panel right
2. Selects file/slide from left panel → content loads, preview updates
3. Edits CSS attributes in right panel → debounced PUT autosave (500ms idle)
4. Adds slide → POST file with `layer=slide`, reorder if needed
5. Reorders slides via drag-drop → PATCH reorder with new id array
6. Generate all button:
   - Opens modal: select provider, enter optional prompt override
   - POST generate → 202 Accepted → poll GET /jobs/{jid} every 2s
   - On success → re-fetch all project files → preview refreshes
7. Regenerate single layer button per file → same poll flow
8. Export button → modal: pick format and options → POST export → poll export-job → download URL appears
9. Publish as template → modal: enter name, description, tags, visibility → POST /templates
10. No AI provider configured → banner with link to `/settings#ai-providers`

### API Calls
| Method | Endpoint | Notes |
|---|---|---|
| GET | `/projects/{id}` | Project metadata |
| GET | `/projects/{id}/files` | All file layers |
| POST | `/projects/{id}/files` | Add slide or layer |
| PUT | `/projects/{id}/files/{fid}` | Autosave content |
| DELETE | `/projects/{id}/files/{fid}` | Delete file |
| PATCH | `/projects/{id}/files/reorder` | Slide reorder |
| GET | `/me/ai-providers` | Populate provider selector |
| POST | `/projects/{id}/generate` | Full generation job |
| POST | `/projects/{id}/files/{fid}/generate` | Layer-level generation |
| GET | `/jobs/{jid}` | Poll job status (2s interval) |
| GET | `/projects/{id}/jobs` | Generation history panel |
| POST | `/projects/{id}/export` | Queue export |
| GET | `/export-jobs/{jid}` | Poll export status |
| POST | `/templates` | Publish as template |

### Navigates To
- `/settings#ai-providers` (no provider configured)
- `/templates/{id}` (after publish)
- `/dashboard` (back)

---

## 8. Settings — `/settings`

**Access:** Auth required

### User Flow
1. **Profile tab** — edit name, bio, avatar URL, website, location
2. **AI Providers tab** — list of connected providers with status indicator
   - Add provider: pick type, enter key + base URL → Test button pings → Save
   - Edit provider: rotate key, change default model, toggle active
   - Delete provider: confirmation modal
3. **Account tab** — change email, change password
4. Logout button → POST logout → clears token → redirect to `/`

### API Calls
| Method | Endpoint |
|---|---|
| GET | `/auth/me` |
| PUT | `/me/profile` |
| GET | `/me/ai-providers` |
| POST | `/me/ai-providers` |
| PUT | `/me/ai-providers/{id}` |
| DELETE | `/me/ai-providers/{id}` |
| POST | `/me/ai-providers/{id}/test` |
| POST | `/auth/logout` |

### Navigates To
- `/dashboard`
- `/` (after logout)

---

## 9. Resource Library — `/resources`

**Access:** Public (read) | Auth required to create / fork / upvote / bookmark

### User Flow
1. Browses — filter by kind (prompt, skill, agent, rule, mcp, design_doc, hook)
2. Searches by keyword / tag
3. Clicks card → `/resources/{id}`
4. New resource button (auth) → `/resources/new`
5. Quick fork from card (auth)
6. Upvote / bookmark on card (auth, toggle)

### API Calls
| Method | Endpoint |
|---|---|
| GET | `/resources?kind=&q=&tags=&sort=` |
| POST | `/resources/{id}/fork` |
| POST | `/resources/{id}/upvote` |
| POST | `/resources/{id}/bookmark` |

### Navigates To
- `/resources/{id}`
- `/resources/new`
- `/login` (guest action attempt)

---

## 10. Resource Detail / New — `/resources/{id}` · `/resources/new`

**Access:** Public view | Auth required to create, edit, fork, comment

### User Flow
1. Views content with `{{placeholder}}` brackets highlighted, metadata, tags
2. Fork → creates own copy → navigates to forked resource page
3. Owner — inline edit content, placeholders schema, visibility
4. Comments — read publicly, post if authenticated
5. View forks tab — lists public forks with authors
6. Use in editor — copy to clipboard button for injecting into `context.md`

### API Calls
| Method | Endpoint | Who |
|---|---|---|
| GET | `/resources/{id}` | All |
| POST | `/resources` | Auth (new) |
| PUT | `/resources/{id}` | Owner / Admin |
| DELETE | `/resources/{id}` | Owner / Admin |
| POST | `/resources/{id}/fork` | Auth |
| GET | `/resources/{id}/forks` | All |
| POST | `/resources/{id}/upvote` | Auth |
| POST | `/resources/{id}/bookmark` | Auth |
| GET | `/resources/{id}/comments` | All |
| POST | `/resources/{id}/comments` | Auth |
| PUT | `/comments/{id}` | Owner / Admin |
| DELETE | `/comments/{id}` | Owner / Admin |

### Navigates To
- `/resources/{forked_id}` (after fork)
- `/users/{id}` (author link)
- `/resources` (back)

---

## 11. Public User Profile — `/users/{id}`

**Access:** Public

### User Flow
1. Views avatar, bio, stats (templates published, total fork count, total upvotes)
2. Templates tab — user's public templates, can fork from here
3. Resources tab — user's public resources
4. Viewing own profile → shows Edit profile link to `/settings`

### API Calls
| Method | Endpoint |
|---|---|
| GET | `/users/{id}` |
| GET | `/users/{id}/templates` |
| GET | `/users/{id}/resources` |

### Navigates To
- `/templates/{id}`
- `/resources/{id}`
- `/settings` (own profile)

---

## 12. Admin Panel — `/admin`

**Access:** Admin + Superadmin only (middleware guard)

### User Flow
1. **Overview tab** — platform stats: users, templates, active jobs, exports
2. **Users tab** — search all users, view profile, suspend/unsuspend
   - Superadmin only: promote to admin, demote to user
3. **Templates tab** — force change visibility, force delete, view any private template
4. **Resources tab** — moderate community content, force delete
5. **Types tab** — add new output types, edit existing

### API Calls
| Method | Endpoint | Notes |
|---|---|---|
| GET | `/admin/users` | Paginated user list |
| PUT | `/admin/users/{id}/role` | Superadmin only |
| PUT | `/admin/users/{id}/suspend` | Admin + Superadmin |
| DELETE | `/admin/templates/{id}` | Force delete |
| DELETE | `/admin/resources/{id}` | Force delete |
| DELETE | `/admin/comments/{id}` | Force delete |
| PUT | `/admin/templates/{id}` | Force visibility change |
| POST | `/admin/types` | Create type |
| PUT | `/admin/types/{id}` | Update type |
| GET | `/admin/stats` | Platform overview |

### Navigates To
- `/users/{id}` (view user's public profile)
- `/templates/{id}` (view template)
- `/resources/{id}` (view resource)

---

*Next: ERD · API Contract · System Architecture*
