
---
marp: true
title: Week 8 • Lab 1 — Prototyping & Constraints
paginate: true
---

# Week 8 • Lab 1
### Partials · Pagination · Filtering · No-JS parity

- Server-first scaffold scales to larger lists
- Keep accessibility hooks centralised
- HTMX enhances, but baseline must stand alone

---

## Learning outcomes
- Factor templates into reusable Pebble partials
- Implement filtering + pagination with dual paths
- Maintain accessible announcements (result counts, status)
- Preserve history with `hx-push-url`
- Document trade-offs for future reviewers

---

## Why this matters
- Large lists overwhelm people + assistive tech
- Duplication causes accessibility regressions
- Accreditation looks for evidence of scalability + inclusion
- References:
  - `../../references/template-map-week8.md`
  - `../../references/htmx-pattern-cheatsheet.md`
  - MDN: Accessible pagination patterns

---

## Template hierarchy
```
_layout/base.peb
└── tasks/index.peb
     ├── tasks/_list.peb
     │    └── tasks/_item.peb
     └── tasks/_pager.peb
```
- Base layout: live region, skip link, Pico.css
- `_item` partial: single source of truth for forms + labels
- `_list` partial: wraps items, exposes result count via `aria-describedby`

---

## Base layout essentials
```html
<p id="status" role="status" aria-live="polite"></p>
<link rel="stylesheet" href="https://unpkg.com/@picocss/pico@2/css/pico.min.css">
<script src="https://unpkg.com/htmx.org@1.9.12"></script>
```
- Load HTMX once
- Define `.visually-hidden` + focus styles centrally
- Keeps enhancement opt-in, not required

---

## Active Search pattern
```html
<form action="/tasks" method="get"
      hx-get="/tasks/fragment"
      hx-target="#task-area"
      hx-trigger="keyup changed delay:300ms, submit"
      hx-push-url="true">
```
- HTMX path: fragment update with debounce
- No-JS path: submit button posts to `/tasks`
- Reminder: label + hint text stay in partial

---

## Accessible result count
```html
<ul id="task-list" aria-describedby="result-count">
  …
</ul>
<p id="result-count" class="visually-hidden">
  Showing {{ page.items|length }} of {{ page.total }} tasks
</p>
```
- Screen readers announce changes after filter/pagination
- Counts update in fragment response + full page render

---

## Pagination with history
```html
<a href="/tasks?q={{ query }}&page={{ page.page+1 }}"
   hx-get="/tasks/fragment?q={{ query }}&page={{ page.page+1 }}"
   hx-target="#task-area"
   hx-push-url="true">Next</a>
```
- Dual attributes keep parity
- `aria-current="page"` marks active page
- Back/forward works for everyone

---

## Fragment route structure
```kotlin
get("/tasks/fragment") {
  val data = repo.search(query, page)
  val list = render("tasks/_list.peb", …)
  val pager = render("tasks/_pager.peb", …)
  val status = "<div id="status" hx-swap-oob="true">Found ${data.total} tasks.</div>"
  call.respondText(list + pager + status)
}
```
- Returns only what HTMX needs
- OOB status updates live region
- Full-page route mirrors logic

---

## Verification checklist
- Keyboard: tab through filter + pager + list
- Screen reader: confirm result count announced
- No-JS: disable scripts, repeat flows (watch URL)
- History: use back/forward after filtering
- Log outcomes in `assistive-testing-checklist`

---

## Document constraints
Update `wk08/docs/prototyping-constraints.md`:
- Rendering splits (full page vs fragment)
- State management (query + page params)
- Accessibility hooks (status, counts)
- Risks + mitigations (fragment duplication, paging bounds)

---

## Next steps
- Week 8 Lab 2: harden routes, parity scripts, trade-offs
- Prepare evidence for Week 9 pilots
- Keep helper docs handy for inclusivity spot-checks

