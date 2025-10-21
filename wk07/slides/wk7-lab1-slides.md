
---
marp: true
title: Week 7 • Lab 1 — Ethics & Accessible Inline Edit
paginate: true
---

# Week 7 • Lab 1
### Ethics in practice · Inline edit · Live status

- Refresh consent + low-risk protocol
- Implement dual-path inline edit
- Ensure validation + status announcements are accessible

---

## Why this matters
- Ethical practice underpins inclusive HCI
- Inline edits often break assistive tech flows
- This task becomes audit evidence in Week 7/Task 1
- References:
  - `../../references/consent-pii-faq.md`
  - `../../references/privacy-by-design.md`
  - `../../references/example-accessible-inline-edit.md`

---

## Session outline
1. Ethics guest overlay (principles, red flags)
2. Apply consent protocol to peer activities
3. Implement inline edit with HTMX + PRG
4. Verify accessibility (keyboard, SR, JS-off)
5. Capture findings in backlog + notes

---

## Ethics essentials
- Autonomy, beneficence, non-maleficence, justice
- Peers only, no recordings, pseudonym notes
- Consent is ongoing; log opt-outs
- Data lifecycle: collection → analysis → deletion

---

## Inline edit architecture
```
view.peb  ↔  edit.peb
GET /tasks/{id}/edit   (HTMX partial / full page)
POST /tasks/{id}/edit  (validation + status)
GET /tasks/{id}/view   (HTMX revert)
```
- `hx-swap="outerHTML"` to replace `<li>`
- PRG fallback to keep history clean

---

## Accessibility hooks
- `<label for="title-{{id}}">` + `aria-describedby`
- Error paragraph always present (`role="alert"` when needed)
- Live region in base layout for success messages
- Cancel link with dual attributes (`href` + `hx-get`)

---

## Worked example
Validation error (HTMX path):
```kotlin
if (title.isBlank()) {
  val html = render("tasks/edit.peb", mapOf("t" to task, "error" to "Title is required."))
  call.respondText(html, ContentType.Text.Html)
}
```
No-JS path redirects to `/tasks/{id}/edit?error=empty` and page renders alert banner.

---

## Verification checklist
- Keyboard: tab order, focus after save/cancel
- Screen reader: label announcement, error message, status update
- JS-off: edit/save/cancel via full-page renders
- Log outcomes in `assistive-testing-checklist`

---

## Evidence capture
- Screenshots (before/after, alt text)
- SR transcript snippet (NVDA/Orca)
- Backlog entry referencing metrics/notes
- Update `research/notes.md` with ethical observations

---

## Deliverables today
- `view.peb` + `edit.peb` partials
- Routes updated with dual-path handling
- Accessibility verification notes committed
- Backlog item marked `candidate_fix` for future focus

---

## Next lab
- Conduct accessibility audit, triage backlog, implement one fix
- Today’s artefacts become audit evidence

