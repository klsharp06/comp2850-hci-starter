
---
marp: true
title: Week 8 • Lab 2 — Routes, No-JS, Trade-offs
paginate: true
---

# Week 8 • Lab 2
### Wiring flows · Parity · Trade-offs

- Harden create/edit/delete flows
- Ensure validation works on both paths
- Script no-JS verification
- Document decisions for auditors

---

## Learning outcomes
- Implement dual-path POST handlers (HTMX + PRG)
- Surface validation errors accessibly in both modes
- Automate parity checks for repeatability
- Record constraints + risks transparently

---

## Why parity now?
- Participants may disable JS (privacy, AT conflicts)
- Accreditation expects evidence of resilience
- Parity scripts become Task 1 artefacts
- References:
  - `../../references/assistive-testing-checklist.md`
  - `../../references/htmx-pattern-cheatsheet.md`
  - HTMX essay: Handling server errors

---

## Create flow recap
```kotlin
post("/tasks") {
  val title = call.receiveParameters()["title"].orEmpty().trim()
  if (title.isBlank()) {
    val status = "<div id="status" hx-swap-oob="true">Title is required.</div>"
    return@post call.respondText(status, ContentType.Text.Html)
  }
  val task = repo.add(title)
  if (call.isHtmx()) {
    val item = render("tasks/_item.peb", mapOf("t" to task))
    val status = "<div id="status" hx-swap-oob="true">Added "${task.title}".</div>"
    call.respondText(item + status)
  } else {
    call.respondRedirect("/tasks")
  }
}
```
- Same logic, different response shape
- Validation returns OOB status; PRG handles fallback

---

## Validation for no-JS users
- Redirect with query flag (`?error=title`)
- Full page render shows alert with `role="alert"`
- Link alert to input (`href="#title"`)
- Document in backlog + constraints doc

---

## Delete flow essentials
```html
<button hx-post="/tasks/{{ t.id }}/delete"
        hx-target="#task-{{ t.id }}"
        hx-swap="outerHTML"
        hx-confirm="Delete {{ t.title }}?"
        aria-label="Delete task: {{ t.title }}">
  Delete
</button>
```
- Confirmation prevents accidental loss
- `aria-label` gives SR context
- Consider no-JS confirmation stage (note trade-off)

---

## Fragment responses sanity check
- Use DevTools or `curl` to inspect `/tasks/fragment`
- Ensure HTML snippets are valid + complete
- Status element ID must match base template (`status`)
- If markup diverges, refactor partials

---

## No-JS parity script
`wk08/lab-w8/scripts/nojs-check.md`
1. Disable JS → repeat add/filter/paginate/edit/delete
2. Capture screenshots + notes (link evidence)
3. Log issues into backlog if parity fails
4. Share script with team for repeat runs

---

## Update constraints doc
Add to `wk08/docs/prototyping-constraints.md`:
- Dual routes maintained (full page vs fragment)
- Validation strategies and alerts
- Risks: duplicated rendering, forgotten query params
- Mitigations: shared partials, parity scripts, tests

---

## Accessibility verification
- Keyboard: ensure alerts are reachable, focus stable
- Screen reader: confirm alerts + status announced
- No-JS: follow script, watch URL/state
- Update `assistive-testing-checklist` with outcomes

---

## Deliverables today
- Updated routes/templates with parity support
- Completed no-JS script and checklist notes
- Constraints doc expanded with trade-offs
- Backlog updated with new findings/fixes

---

## Next steps
- Week 9: run pilots using the hardened flows
- Evidence today becomes part of Task 1 pack
- Keep parity script for regression checks later

