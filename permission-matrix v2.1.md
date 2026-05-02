# MGF — Permission Matrix
Version: 1.0 — MVP | Status: Draft

## Role Definitions

| Role | Description |
|---|---|
| **Guest** | Unauthenticated visitor. Can browse and read public content only. |
| **User** | Registered and authenticated. Can create, fork, generate, and export. Limited to own content. |
| **Admin** | Full content access. Can moderate any user's content. Cannot manage other admins. |
| **Superadmin** | All admin capabilities plus the ability to promote/demote admins and create other superadmins. |

### Legend
- ✓ = Allowed for all targets
- Own = Allowed only for own content (user_id must match auth user)
- ✗ = Not allowed
- — = Not applicable (e.g. login for already-authenticated users)

---

## Public Browsing

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Browse template gallery | GET /templates | ✓ | ✓ | ✓ | ✓ |
| View template detail | GET /templates/{id} | ✓ | ✓ | ✓ | ✓ |
| View template files | GET /templates/{id}/files | ✓ | ✓ | ✓ | ✓ |
| Browse resource library | GET /resources | ✓ | ✓ | ✓ | ✓ |
| View resource detail | GET /resources/{id} | ✓ | ✓ | ✓ | ✓ |
| View resource forks | GET /resources/{id}/forks | ✓ | ✓ | ✓ | ✓ |
| View user profile | GET /users/{id} | ✓ | ✓ | ✓ | ✓ |
| View user templates | GET /users/{id}/templates | ✓ | ✓ | ✓ | ✓ |
| View user resources | GET /users/{id}/resources | ✓ | ✓ | ✓ | ✓ |
| Read comments | GET /{target}/{id}/comments | ✓ | ✓ | ✓ | ✓ |
| List output types | GET /types | ✓ | ✓ | ✓ | ✓ |

---

## Authentication

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Register | POST /auth/register | ✓ | — | — | — |
| Login | POST /auth/login | ✓ | — | — | — |
| Logout | POST /auth/logout | ✗ | ✓ | ✓ | ✓ |
| Get current user | GET /auth/me | ✗ | ✓ | ✓ | ✓ |

---

## Profile & AI Providers

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Update own profile | PUT /me/profile | ✗ | ✓ | ✓ | ✓ |
| List own AI providers | GET /me/ai-providers | ✗ | ✓ | ✓ | ✓ |
| Add AI provider | POST /me/ai-providers | ✗ | ✓ | ✓ | ✓ |
| Update AI provider | PUT /me/ai-providers/{id} | ✗ | ✓ | ✓ | ✓ |
| Delete AI provider | DELETE /me/ai-providers/{id} | ✗ | ✓ | ✓ | ✓ |
| Test AI provider | POST /me/ai-providers/{id}/test | ✗ | ✓ | ✓ | ✓ |

---

## Templates

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Create template | POST /templates | ✗ | ✓ | ✓ | ✓ |
| Edit template | PUT /templates/{id} | ✗ | Own | ✓ any | ✓ any |
| Delete template | DELETE /templates/{id} | ✗ | Own | ✓ any | ✓ any |
| Fork template | POST /templates/{id}/fork | ✗ | ✓ | ✓ | ✓ |
| Add file to template | POST /templates/{id}/files | ✗ | Own | ✓ any | ✓ any |
| Update template file | PUT /templates/{id}/files/{fid} | ✗ | Own | ✓ any | ✓ any |
| Delete template file | DELETE /templates/{id}/files/{fid} | ✗ | Own | ✓ any | ✓ any |

---

## Projects

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Create project | POST /projects | ✗ | ✓ | ✓ | ✓ |
| List own projects | GET /projects | ✗ | Own | ✓ any | ✓ any |
| View project | GET /projects/{id} | ✗ | Own | ✓ any | ✓ any |
| Update project metadata | PUT /projects/{id} | ✗ | Own | ✓ any | ✓ any |
| Delete project | DELETE /projects/{id} | ✗ | Own | ✓ any | ✓ any |
| List project files | GET /projects/{id}/files | ✗ | Own | ✓ any | ✓ any |
| Add file to project | POST /projects/{id}/files | ✗ | Own | ✓ any | ✓ any |
| Update project file | PUT /projects/{id}/files/{fid} | ✗ | Own | ✓ any | ✓ any |
| Delete project file | DELETE /projects/{id}/files/{fid} | ✗ | Own | ✓ any | ✓ any |
| Reorder project files | PATCH /projects/{id}/files/reorder | ✗ | Own | ✓ any | ✓ any |
| Export project | POST /projects/{id}/export | ✗ | Own | ✓ any | ✓ any |
| Poll export job | GET /export-jobs/{jid} | ✗ | Own | ✓ any | ✓ any |

---

## AI Generation

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Generate full project | POST /projects/{id}/generate | ✗ | Own | ✓ any | ✓ any |
| Regenerate single layer | POST /projects/{id}/files/{fid}/generate | ✗ | Own | ✓ any | ✓ any |
| View generation history | GET /projects/{id}/jobs | ✗ | Own | ✓ any | ✓ any |
| Poll job status | GET /jobs/{jid} | ✗ | Own | ✓ any | ✓ any |

---

## Resources

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Create resource | POST /resources | ✗ | ✓ | ✓ | ✓ |
| Update resource | PUT /resources/{id} | ✗ | Own | ✓ any | ✓ any |
| Delete resource | DELETE /resources/{id} | ✗ | Own | ✓ any | ✓ any |
| Fork resource | POST /resources/{id}/fork | ✗ | ✓ | ✓ | ✓ |

---

## Social

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Upvote template | POST /templates/{id}/upvote | ✗ | ✓ | ✓ | ✓ |
| Upvote resource | POST /resources/{id}/upvote | ✗ | ✓ | ✓ | ✓ |
| Bookmark template | POST /templates/{id}/bookmark | ✗ | ✓ | ✓ | ✓ |
| Bookmark resource | POST /resources/{id}/bookmark | ✗ | ✓ | ✓ | ✓ |
| Post comment | POST /{target}/{id}/comments | ✗ | ✓ | ✓ | ✓ |
| Edit comment | PUT /comments/{id} | ✗ | Own | ✓ any | ✓ any |
| Delete comment | DELETE /comments/{id} | ✗ | Own | ✓ any | ✓ any |

---

## Admin — Content Moderation

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| View all users | GET /admin/users | ✗ | ✗ | ✓ | ✓ |
| Suspend/unsuspend user | PUT /admin/users/{id}/suspend | ✗ | ✗ | ✓ | ✓ |
| Force-delete any template | DELETE /admin/templates/{id} | ✗ | ✗ | ✓ | ✓ |
| Force-delete any resource | DELETE /admin/resources/{id} | ✗ | ✗ | ✓ | ✓ |
| Force-delete any comment | DELETE /admin/comments/{id} | ✗ | ✗ | ✓ | ✓ |
| Force-change template visibility | PUT /admin/templates/{id} | ✗ | ✗ | ✓ | ✓ |
| Create output type | POST /admin/types | ✗ | ✗ | ✓ | ✓ |
| Edit output type | PUT /admin/types/{id} | ✗ | ✗ | ✓ | ✓ |
| View platform stats | GET /admin/stats | ✗ | ✗ | ✓ | ✓ |

---

## Superadmin Only

| Action | Endpoint | Guest | User | Admin | Superadmin |
|---|---|---|---|---|---|
| Promote user → admin | PUT /admin/users/{id}/role {"role":"admin"} | ✗ | ✗ | ✗ | ✓ |
| Demote admin → user | PUT /admin/users/{id}/role {"role":"user"} | ✗ | ✗ | ✗ | ✓ |
| Create another superadmin | PUT /admin/users/{id}/role {"role":"superadmin"} | ✗ | ✗ | ✗ | ✓ |
| List all admins | GET /admin/users?role=admin | ✗ | ✗ | ✗ | ✓ |

---

## Implementation Notes for Laravel

### Middleware Groups
```php
// routes/api.php
Route::middleware(['auth:sanctum'])->group(function () { ... });
Route::middleware(['auth:sanctum', 'role:admin,superadmin'])->prefix('admin')->group(function () { ... });
Route::middleware(['auth:sanctum', 'role:superadmin'])->group(function () { ... });
```

### Policy Pattern (ownership check)
```php
// app/Policies/ProjectPolicy.php
public function update(User $user, Project $project): bool
{
    return $user->id === $project->user_id
        || in_array($user->role, ['admin', 'superadmin']);
}
```

### Role Middleware
```php
// app/Http/Middleware/RoleMiddleware.php
public function handle(Request $request, Closure $next, string ...$roles): Response
{
    if (!in_array($request->user()?->role, $roles)) {
        abort(403, 'Insufficient permissions');
    }
    return $next($request);
}
```

---

*Part of: MGF Documentation Suite*
*See also: PRD.md · ERD.dbml · api-contract.yaml · user-flows-api-map.md*
