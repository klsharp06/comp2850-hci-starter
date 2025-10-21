
---
marp: true
title: Week 6 • Lab 2 — Needs-Finding & Inclusive Backlog
paginate: true
---

# Week 6 • Lab 2
### Needs-finding · Consent protocol · Inclusive backlog

- Interview peers ethically (jobs-to-be-done)
- Document consent + data boundaries
- Build backlog with inclusion risks
- Sketch instrumentation plan

---

## Learning outcomes
- Craft job/customer stories from observations
- Record consent in low-risk format
- Capture backlog entries with severity × inclusion
- Prepare instrumentation teaser for Week 9

---

## References
- `../../references/consent-pii-faq.md`
- `../../references/privacy-by-design.md`
- Jobs-to-be-done primer
- Microsoft Inclusive Design Toolkit

---

## Session outline
1. Warm-up + folder setup
2. Needs-finding interviews → stories
3. Consent script + ethics log
4. Backlog population (severity, inclusion risk)
5. Instrumentation teaser notes

---

## Needs-finding workflow
- Interview: context, motivations, barriers
- Notes (`research/notes.md`) with timestamps, pseudonyms
- Stories (`research/stories.md`) using “When… I want… so I can… Because…”
- Tag story type (job/customer/pain)

---

## Consent protocol essentials
- Script: peers, voluntary, no PII, opt-out path
- Checklist in `consent_protocol.md`
- Record session IDs or initials
- Log unusual events in notes

---

## Inclusive backlog format
```
id,title,story_ref,need,type,severity,inclusion_risk,evidence,notes
```
- Severity High/Med/Low (impact on people)
- Inclusion risk tags (Screen reader, Keyboard, etc.)
- Evidence links (stories, notes, screenshots)
- Flag one `candidate_fix=true`

---

## Example entry
```
4,Status message hidden reflow,S3,Confirmation,Accessibility,High,
  Screen reader;Keyboard,"wk06/research/notes.md#L15","Need visible alert"
```
Ties backlog to story + evidence; ready for Week 7 audit.

---

## Instrumentation teaser
`wk06/instr/plan.md`
- Potential events: task_created, task_edited, validation_error
- Fields: timestamp, session_id, js_mode
- Ethics: store locally, allow deletion on request
- Prepares Week 9 logging work

---

## Deliverables today
- Stories + notes committed
- Consent protocol documented
- Backlog with ≥8 entries (severity + inclusion)
- Instrumentation plan stub
- Commit: `wk6s2: needs + consent + backlog`

---

## Next steps
- Week 7: ethics overlay + accessibility audit using this backlog
- Keep evidence organised for future assessments

