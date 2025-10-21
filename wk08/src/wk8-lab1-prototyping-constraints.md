# Week 8 • Lab 1 — Prototyping with Constraints: Partials, Pagination, Filtering

<div class="badges">
  <span class="badge lo">LO1</span>
  <span class="badge lo">LO5</span>
  <span class="badge lo">LO6</span>
  <span class="badge lo">LO9</span>
  <span class="badge lo">LO10</span>
  <span class="badge acm">HCI-Design</span>
  <span class="badge acm">System Design</span>
  <span class="badge acm">Evaluation</span>
  <span class="badge wcag">WCAG 2.2 AA</span>
</div>

> We keep saying “server-first for everyone, HTMX for enhancement.” Scaling that pattern to dozens or hundreds of tasks introduces new constraints. Today you will factor templates, add filtering and pagination, and document the trade-offs you are making.

---

## Why this lab matters
- Template partials reduce duplication and keep accessibility fixes in one place.
- Pagination and filtering control cognitive load and network cost when lists grow.
- History (`hx-push-url`) and live status updates maintain web affordances (back button, result count) for all participants.
- Documented trade-offs help you justify design decisions in critiques and assessments.

This lab feeds directly into Week 8 Lab 2 (no-JS parity verification) and sets up metrics collection in Week 9.

---

## Pre-lab reading (15 min)
- `../../references/template-map-week8.md` (structure overview of the partials you will build)
- [hypermedia.systems — Active Search](https://hypermedia.systems/active-search/)
- [HTMX reference: `hx-push-url`](https://htmx.org/attributes/hx-push-url/) and [`hx-trigger`](https://htmx.org/attributes/hx-trigger/)
- [Pebble template inheritance](https://pebbletemplates.io/wiki/guide/extending/) and include syntax

Keep `../../references/htmx-pattern-cheatsheet.md` open for copy/paste snippets.

---

## Learning outcomes
By the end of this session you will:
- Factor the tasks page into reusable Pebble partials (`_layout`, `_list`, `_item`, `_pager`).
- Implement filtering + pagination with a dual-path architecture (full-page and HTMX fragment routes).
- Maintain accessible result announcements (`aria-describedby`) and predictable focus.
- Use `hx-push-url` so history and bookmarking work across pages.
- Record the trade-offs and risks you introduce in `docs/prototyping-constraints.md`.

---
---

## Background: Inclusive prototyping at scale
Why refactor now? As lists grow, duplication becomes dangerous: accessibility fixes, aria hooks, and HTMX attributes drift if they live in multiple places. Partials keep the source of truth in one file so every flow (full page and fragment) inherits the same semantics.

Pagination and filtering are not just performance tricks; they control cognitive load, especially for screen-reader participants who would otherwise wade through hundreds of list items. Implement them with inclusive defaults: predictable focus, result announcements, and URLs that respect the back button.

Keep these references handy while you work:
- `../../references/template-map-week8.md` (visual map of the partials you are about to create)
- `../../references/htmx-pattern-cheatsheet.md` (Active Search, OOB status, indicators)
- [MDN: Accessible Pagination Patterns](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Understanding_WCAG/Navigation_and_orienting#pagination)

### Worked example: Active Search with parity
The filter form uses HTMX for fast updates, but must still function when JS is disabled. Compare the enhanced and fallback behaviours:

```html
<form action="/tasks" method="get"
      hx-get="/tasks/fragment"
      hx-target="#task-area"
      hx-trigger="keyup changed delay:300ms, submit from:closest(form)"
      hx-push-url="true">
  <label for="q">Filter tasks</label>
  <input id="q" name="q" value="{{ query|default('') }}" type="search"
         aria-describedby="q-hint">
  <small id="q-hint">Type to filter; works without JavaScript.</small>
  <button type="submit">Apply</button>
</form>
```

- **HTMX path**: typing triggers `/tasks/fragment`, which returns `_list` + `_pager` + a status update (`Found X tasks`). `hx-push-url"true"` keeps the URL in sync so back/forward works.
- **No-JS path**: pressing Enter submits the form to `/tasks`; the full page renders using the same partials. Because labels and hints live in the partial, accessibility remains intact in both cases.

When verifying parity, disable JS and watch the address bar: URLs should still reflect the current query and page so participants can bookmark or share the state.

---

## 0. Baseline check (5 min)
Run `./gradlew run` and load `http://localhost:8080/tasks`:
- Confirm add/edit/delete from Week 7 still work with JS on.
- Disable JavaScript and re-run the same flows to ensure parity.
- If anything regressed, fix it before adding complexity.

---

## 1. Factor templates into partials (25 min)

> **💡 Template Naming Convention: Underscore Prefix**
>
> Starting in Week 8, we adopt a **underscore prefix** (`_`) for **partial templates**:
>
> | Name | Type | Purpose | Example |
> |------|------|---------|---------|
> | `index.peb` | **Full page** | Complete HTML document (extends base) | `tasks/index.peb` |
> | `_item.peb` | **Partial** | Fragment included in other templates | `tasks/_item.peb` |
> | `_list.peb` | **Partial** | Reusable component (used multiple times) | `tasks/_list.peb` |
> | `_layout/base.peb` | **Layout** | Shared page structure | `_layout/base.peb` |
>
> **Why the underscore?**
> - **Visual distinction**: Instantly recognizable as a partial (not a standalone page)
> - **Common convention**: Used by Rails, Jekyll, Sass, and other template systems
> - **Prevent direct access**: In some frameworks, `_` files are not served directly
>
> **Rule of thumb**:
> - **No underscore** = Full page that can be rendered directly (e.g., `/tasks` → `tasks/index.peb`)
> - **Underscore** = Fragment included via `{% include %}` or HTMX `hx-get`
>
> **This week's structure**:
> ```
> templates/
> ├── _layout/
> │   └── base.peb        ← Shared layout (underscore because it's a partial)
> └── tasks/
>     ├── index.peb       ← Full page (no underscore)
>     ├── _item.peb       ← Partial: single task <li>
>     ├── _list.peb       ← Partial: task list <ul>
>     └── _pager.peb      ← Partial: pagination nav
> ```

We want a structure like the one in `../../references/template-map-week8.md`.

### 1.1 Base layout
Create `templates/_layout/base.peb` with the shared head, status region, skip link, Pico CSS, HTMX script, and utility classes. This keeps accessibility hooks (live region, focus outline, visually hidden class) in a single place.

### 1.2 Task item partial
Create `templates/tasks/_item.peb` that renders a single `<li>` with:
- Stable id `task-{{ t.id }}`.
- The title and the Edit/Delete forms from Week 7 (keep ARIA labels and dual attributes).
- No inline business logic—just markup and data from the model.

### 1.3 Task list partial
Create `templates/tasks/_list.peb` containing:
```pebble
<ul id="task-list" aria-describedby="result-count">
  {% for task in page.items %}
    {% include "tasks/_item.peb" with t=task %}
  {% endfor %}
</ul>
<p id="result-count" class="visually-hidden">
  Showing {{ page.items|length }} of {{ page.total }} tasks{% if query %} matching "{{ query }}"{% endif %}
</p>
```
This ensures screen readers hear the result count whenever the list changes.

### 1.4 Update index page
Modify `templates/tasks/index.peb` so it extends `_layout/base.peb`, renders the add form, and includes `_list.peb` and `_pager.peb` inside a `<div id="task-area">` container (we will populate `_pager.peb` shortly).

✋ **Checkpoint**: restart the server and confirm the tasks page renders exactly as before (no functionality has changed yet). If anything broke, inspect your partials and template paths.

---

## 2. Add pagination and filtering (35 min)
### 2.1 Repository paging helper
Add a `Page<T>` data class and a `search(query, page, size)` helper in your repository or service layer so routes can request paged data. Keep the page number clamped (`coerceIn`) to valid bounds.

### 2.2 Pager partial
Create `templates/tasks/_pager.peb`:
```pebble
<nav aria-label="Pagination">
  <ul class="pagination">
    {% if page.page > 1 %}
      <li>
        <a href="/tasks?q={{ query|default('') }}&page={{ page.page - 1 }}"
           hx-get="/tasks/fragment?q={{ query|default('') }}&page={{ page.page - 1 }}"
           hx-target="#task-area"
           hx-push-url="true">
          Previous
        </a>
      </li>
    {% endif %}
    <li aria-current="page">Page {{ page.page }} of {{ page.pages }}</li>
    {% if page.page < page.pages %}
      <li>
        <a href="/tasks?q={{ query|default('') }}&page={{ page.page + 1 }}"
           hx-get="/tasks/fragment?q={{ query|default('') }}&page={{ page.page + 1 }}"
           hx-target="#task-area"
           hx-push-url="true">
          Next
        </a>
      </li>
    {% endif %}
  </ul>
</nav>
```
Dual attributes guarantee the pager works both with and without HTMX.

### 2.3 Filter form
Above the task area add a filter form:
```pebble
<form action="/tasks" method="get"
      hx-get="/tasks/fragment"
      hx-target="#task-area"
      hx-trigger="keyup changed delay:300ms, submit from:closest(form)"
      hx-push-url="true">
  <label for="q">Filter tasks</label>
  <input id="q" name="q" value="{{ query|default('') }}" type="search"
         aria-describedby="q-hint">
  <small id="q-hint">Type to filter; works without JavaScript.</small>
  <button type="submit">Apply</button>
</form>
```
The visible submit button provides the no-JS fallback; HTMX enhances with Active Search. Use the indicator pattern from the cheat sheet if you want to show loading state.

### 2.4 Routes
Add two GET handlers in your Ktor routing block:
```kotlin
get("/tasks") {
    val query = call.request.queryParameters["q"].orEmpty()
    val page = call.request.queryParameters["page"]?.toIntOrNull() ?: 1
    val data = repo.search(query = query, page = page, size = 10)
    val model = mapOf("title" to "Tasks", "page" to data, "query" to query)
    call.respondHtml(PebbleRender.render("tasks/index.peb", model))
}

get("/tasks/fragment") {
    val query = call.request.queryParameters["q"].orEmpty()
    val page = call.request.queryParameters["page"]?.toIntOrNull() ?: 1
    val data = repo.search(query = query, page = page, size = 10)
    val list = PebbleRender.render("tasks/_list.peb", mapOf("page" to data, "query" to query))
    val pager = PebbleRender.render("tasks/_pager.peb", mapOf("page" to data, "query" to query))
    val status = """<div id="status" hx-swap-oob="true">Found ${data.total} tasks.</div>"""
    call.respondText(list + pager + status, ContentType.Text.Html)
}
```
`/tasks` handles full-page renders. `/tasks/fragment` returns fragments + an out-of-band status update for HTMX.

✋ **Checkpoint**:
- Filter form updates the list as you type (HTMX) and on submit (no-JS).
- Pager links update the list and URL with HTMX; back/forward work.
- Live region announces “Found X tasks.”
- With JavaScript disabled, the filter submits and redraws the whole page; pager links still navigate correctly.

---

## 3. Test with assistive technology (10 min)
Use the checklist:
- Keyboard-only: Tab through filter form, pagination links, and tasks. Focus order should make sense and remain visible.
- Screen reader: Trigger a filter change and ensure the result count is announced. Navigate the pager links; confirm `aria-current` is read out.
- No-JS parity: Disable JS and confirm filtering/paging still work via full-page loads.
- History: After filtering, use the back button—does the list revert? If not, check `hx-push-url` and your full-page route.

Record outcomes in `wk08/docs/prototyping-constraints.md` under an “Accessibility verification” section.

---

## 4. Document trade-offs (15 min)
Open `wk08/docs/prototyping-constraints.starter.md` (or create `prototyping-constraints.md`) and note:
- Rendering splits (full page vs fragments).
- Accessibility hooks (result count, live region, focus handling).
- State management decisions (query parameter naming, page size choices).
- Risks you introduced (duplicate HTML fragments, maintaining `_list` and `_pager`, potential 404 on out-of-range pages).

This documentation is evidence for Week 10 prioritisation and helps staff review your implementation quickly.

---

## Commit guidance
```bash
git add templates/_layout/base.peb templates/tasks/_*.peb templates/tasks/index.peb src/main/kotlin wk08/docs/prototyping-constraints.md

git commit -m "wk8s1: partials + pagination/filter with hx-push-url" --no-verify
```
Suggested commit body:
```
- factored tasks templates into base layout, list, item, pager partials
- added search form with dual-path (HTMX + full-page) handling
- implemented pagination routes returning fragments + status announcements
- verified keyboard, screen reader, JS-off parity
- documented rendering splits and trade-offs in wk08/docs/prototyping-constraints.md
```

---

## What’s next?
Week 8 Lab 2 focuses on wiring additional routes, error handling, and no-JS verification scripts. Keep your server running and your documentation open—we will extend the same patterns immediately.

Optional stretch this week: add a “mark complete” flow using the same partials and log any accessibility considerations.

---

## Further resources
- `../../references/template-map-week8.md`
- `../../references/htmx-pattern-cheatsheet.md`
- [Hypermedia Systems — Active Search](https://hypermedia.systems/active-search/)
- [MDN — Accessible Pagination Patterns](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Understanding_WCAG/Navigation_and_orienting#pagination)

You now have a scalable, accessible list structure ready for deeper evaluation and instrumentation.
