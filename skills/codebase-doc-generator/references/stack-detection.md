# Stack Detection & Scan Heuristics

This skill is multi-stack. Don't assume the stack — **detect first**, then apply the matching heuristics.
The more unfamiliar the stack, the more items fall into `⚠️ NEEDS CONFIRMATION`. That is healthy.

---

## 1. Detect Stack (read marker files)

| Marker file | Language | Check dependency for framework |
|-------------|----------|-------------------------------|
| `composer.json` | PHP | `laravel/framework` → Laravel · `symfony/*` → Symfony · `cakephp/*` → CakePHP |
| `package.json` | JS/TS | `next` → Next.js · `nuxt` → Nuxt · `@angular/core` → Angular · `vue` (+`vite`) → Vue SPA · `react`(+`vite`/`react-router`) → React SPA · `express`/`fastify`/`@nestjs/core` → Node backend |
| `requirements.txt` / `pyproject.toml` | Python | `django` → Django · `flask` → Flask · `fastapi` → FastAPI |
| `Gemfile` | Ruby | `rails` → Rails |
| `go.mod` | Go | check imports: `gin`, `echo`, `chi`, `net/http` |
| `pom.xml` / `build.gradle` | Java/Kotlin | `spring-boot-starter-web` → Spring Boot |

A project may be **split fullstack** (e.g. Laravel API + Vue SPA in separate folders) → detect both,
scan both, and explain how they connect (frontend route → API endpoint).

After detection, **show a summary + the list of locations to scan to the user for confirmation** before
heavy scanning.

---

## 2. "Page/Route" Sources per Stack

| Stack | Route/page source |
|-------|-------------------|
| Laravel | `routes/web.php`, `routes/api.php`, `app/Http/Controllers/**` |
| Symfony | `config/routes.yaml` / `#[Route]` attributes on controllers |
| Vue (SPA) | `src/router/**` (`path`/`component` declarations), `src/views/**`, `src/pages/**` |
| React Router | `<Route>` config / route objects in `App`/`router.{ts,jsx}` |
| Next.js (pages) | `pages/**` (file-based) |
| Next.js (app) | `app/**/page.{tsx,jsx}` + `route.{ts,js}` for APIs |
| Nuxt | `pages/**` (file-based) |
| Angular | `*-routing.module.ts` / `Routes[]` |
| Express/Fastify | `app.<method>(...)`, `router.<method>(...)`, files under `routes/**` |
| NestJS | controllers + `@Controller`/`@Get`/`@Post` decorators |
| Django | `urls.py` (urlpatterns), views in `views.py`, DRF `routers` |
| Flask | `@app.route` / blueprint `@bp.route` |
| FastAPI | `@app.<method>` / `@router.<method>` |
| Rails | `config/routes.rb`, controllers in `app/controllers/**`, views in `app/views/**` |
| Spring Boot | `@RequestMapping`/`@GetMapping`/`@PostMapping` on controllers |

**File-based routing** (Next/Nuxt): folder/file name = path. Dynamic segments: `[id]`, `[...slug]`.

---

## 3. "Input" Sources per Stack

| Stack | Input & validation source |
|-------|---------------------------|
| Laravel | FormRequest `rules()` in `app/Http/Requests/**`, `$request->validate([...])`, `{param}` in routes |
| Vue/React | form fields (`v-model`, controlled `value/onChange`), props, query param parsing |
| Next/Nuxt | `searchParams`/`useRouter().query`, body parsing in handler, schema (zod) |
| NestJS | DTO + `class-validator` decorators (`@IsString`, etc.) |
| Express | `req.body`/`req.params`/`req.query`, validators (`joi`/`zod`/`express-validator`) |
| Django | `forms.py`, DRF `serializers.py`, URL `<int:pk>` |
| FastAPI | handler parameters + Pydantic models |
| Rails | strong params (`params.require(...).permit(...)`), form helpers |
| Spring | `@RequestBody` DTO + Bean Validation (`@NotNull`, etc.), `@PathVariable`/`@RequestParam` |

---

## 4. "Output" & "How It Works" Sources

- **Output**: the handler's return value — rendered view (template name), JSON response (data shape),
  redirect (target), status code. For APIs, note response shape & status codes.
- **How it works**: read the handler/method body, summarize in plain language. Note VISIBLE side effects:
  DB queries / model calls, external API/service calls, dispatched jobs/queues, events, sent emails/notifs,
  file uploads. Name the services/models being called.

---

## 5. "Role/Auth" Sources (for "As a ..." in user stories)

- Laravel: `auth`, `role:*` middleware, Gate/Policy.
- Vue/React: navigation guards, route meta `requiresAuth`/`roles`.
- Next: `middleware.ts`, session checks in handlers.
- NestJS: `@UseGuards`, `@Roles`.
- Django: `@login_required`, `permission_classes`.
- Spring: `@PreAuthorize`, Spring Security config.

⚠️ TECHNICAL role (e.g. `role:admin`) → fact. Mapping to a **business persona** ("Property Manager",
"Front Office") → `⚠️ NEEDS CONFIRMATION`.

---

## 6. Knowable vs Needs-Confirmation (discipline summary)

| Aspect | Default status |
|--------|----------------|
| Page/route list | ✅ Fact |
| Input + validation | ✅ Fact |
| Handler output/return | ✅ Fact |
| Technical handler logic | ✅ Fact (summarized) |
| Technical role (middleware) | ✅ Fact |
| **UX flow ordering across pages** | ⚠️ Partial (redirects/links readable, intent isn't) |
| **Alternative flow & error handling** | ⚠️ Needs confirmation |
| **Why behind conditionals / business rules** | ⚠️ Needs confirmation |
| **Business persona from technical role** | ⚠️ Needs confirmation |
| **User-story benefit ("so that")** | ⚠️ Needs confirmation |
| **Business-level AC** | ⚠️ Partial (validation-derived = fact; rest = confirmation) |
