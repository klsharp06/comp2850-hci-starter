
---
marp: true
title: Week 6 • Lab 1 — Server-first Foundations
paginate: true
---

# Week 6 • Lab 1
### Server-first · HTMX enhancement · Accessibility baseline

- Build accessible Ktor + Pebble scaffold
- Ensure flows work with JavaScript disabled
- Layer HTMX without breaking inclusion

---

## Learning outcomes
- Explain server-first benefits for inclusion
- Create base layout with live region & skip link
- Render full-page task list (add/delete)
- Enhance with HTMX fragments + OOB status
- Verify parity (keyboard, SR, no-JS)

---

## References
- `../../references/privacy-by-design.md`
- `../../references/consent-pii-faq.md`
- hypermedia.systems — Hypermedia reintroduction
- HTMX docs (request → target → swap pattern)

---

## Server-first mental model
- Server renders complete HTML (works everywhere)
- HTMX adds progressive enhancement
- Shared templates avoid duplication/bugs
- Live region + semantic structure baked in

---

## Base layout essentials
```html
<p id="status" role="status" aria-live="polite"></p>
<a class="skip-link" href="#main">Skip to content</a>
<link rel="stylesheet" href="https://unpkg.com/@picocss/pico@2/css/pico.min.css">
<script src="https://unpkg.com/htmx.org@1.9.12"></script>
```
- Define `.visually-hidden` + focus outlines
- Load HTMX once per page

---

## Full-page flows (baseline)
- `GET /tasks` renders list via Pebble
- `POST /tasks` validates → `respondRedirect("/tasks")`
- `POST /tasks/{id}/delete` → redirect
- Post-Redirect-Get avoids duplicate submits

---

## HTMX enhancement
```html
<form hx-post="/tasks" hx-target="#task-list" hx-swap="beforeend">
```
```kotlin
if (call.isHtmx()) {
  val fragment = render("tasks/_item.peb")
  val status = "<div id="status" hx-swap-oob="true">Added …</div>"
  call.respondText(fragment + status)
}
```
- Same routes serve both paths
- OOB status updates live region without moving focus

---

## Verification checklist
- Keyboard-only add/delete
- Screen reader: status announcement after actions
- No-JS: disable scripts, repeat flows
- Document results in `assistive-testing-checklist`

---

## Evidence capture
- Screenshots of both modes (with alt text)
- SR transcript snippet
- Backlog entry if issues found
- Commit message: `wk6s1: server-first scaffold + HTMX`

---

## Next lab
- Needs-finding, consent protocol, inclusive backlog (Week 6 Lab 2)
- Today’s scaffold becomes audit target in Week 7

