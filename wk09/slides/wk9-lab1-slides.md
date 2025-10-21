
---
marp: true
title: Week 9 • Lab 1 — Evaluation Plan & Instrumentation
paginate: true
---

# Week 9 • Lab 1
### Tasks · Metrics · Protocol · Instrumentation

- Design ethical peer pilots
- Capture objective + subjective metrics
- Instrument server-side logging (JS on/off)
- Prepare Task 1 evidence pack foundations

---

## Why plan?
- Without structure, data becomes anecdotal
- Accreditation expects traceability (task → metric → backlog)
- Low-risk consent demands transparency
- References:
  - `../../references/consent-pii-faq.md`
  - `../../references/evaluation-metrics-quickref.md`
  - Nielsen Norman: Test plan essentials

---

## Learning outcomes
- Define tasks with measurable success criteria
- Select metrics covering performance + perception
- Write a concise, ethical protocol peers can follow
- Implement logging that respects privacy

---

## Task & metric design
- 3–4 tasks covering key flows (filter, edit, delete)
- Success defined in measurable terms (time, errors)
- Metrics to capture:
  - Completion (0/1)
  - Time-on-task (ms)
  - Validation/server errors
  - UMUX-Lite questions (1–7)
  - Confidence rating (1–5)
  - Optional: accessibility confirmations

---

## Worked example: T2_edit
```
Task: Rename a task and confirm save
Success: Title updated within 2 min, error-free
Metrics: completion, time, validation_error count, confidence, SR confirmation
```
- Notes in `research/stories.md` feed backlog references later
- Connect to Week 7/8 findings (e.g., inline edit parity issues)

---

## Protocol structure
- Scope: peers, no recordings, pseudonym notes
- Moderator script (intro → task prompts → wrap-up)
- Timing approach (server logs + optional stopwatch)
- Subjective questions after tasks (UMUX-Lite, confidence)
- Session ID assignment + opt-out process

---

## Instrumentation shape
```
ts_iso,session_id,request_id,task_code,step,outcome,ms,http_status,js_mode
```
- `step`: start, success, fail, validation_error, server_error
- `js_mode`: derived from `HX-Request`
- Log validation errors explicitly to tie metrics to backlog

---

## Logger & timing helper
```kotlin
Logger.write(session, reqId, taskCode, "success", "ok", ms, status, js)
```
- Initialise file with header if missing
- Wrap routes in `call.timed { … }`
- Manual notes in `wk09/lab-wk9/research/pilot-notes.md`

---

## Manual artefacts to prepare
- `research/tasks.md` / `measures.md` / `protocol.md`
- Consent script refresh (`consent_protocol.md`)
- Pilot notes template (observations, severity)
- Draft Task 1 pack structure (`submissions/task1-draft/`)

---

## Dry run checks
1. Set temp session ID (cookie)
2. Execute each task (JS on/off)
3. Inspect `data/metrics.csv` rows
4. Note anomalies, fix instrumentation now

---

## Deliverables today
- Documented tasks, metrics, protocol
- Logger + timing helper committed
- Quickcheck plan defined (median, completion)
- Draft Task 1 evidence folder scaffolded

---

## Next lab
- Run actual pilots, debrief into backlog
- Use today’s artefacts to move fast and stay consistent

