# Week 9 Lab Pack: Evaluation & Instrumentation (Consolidated)

**Purpose**: Complete lab pack for Week 9 evaluation activities (Labs 1 & 2)
**Status**: Consolidated from `wk09/lab-wk9/` and `wk09/lab-w9/`
**Date**: 2025-10-14

---

## Directory Structure

```
consolidated/lab-wk9/
├── README.md                        # This file
├── instr/                           # Server-side instrumentation code
│   ├── Logger.kt                    # CSV logging utility
│   ├── Timing.kt                    # Request timing helper
│   └── schema.md                    # CSV schema documentation
├── research/                        # Evaluation planning materials
│   ├── tasks.md                     # Task definitions (T1-T4)
│   ├── measures.md                  # Metrics specifications
│   ├── protocol.md                  # Pilot session protocol
│   └── pilot-notes.md               # Observation template
├── submission/                      # Task 1 submission structure
│   └── task1-draft/                 # Evidence package folder
└── templates/                       # Student starter templates
    ├── consent-script.md            # Informed consent protocol
    ├── Logger.kt                    # Copy for student projects
    ├── Timing.kt                    # Copy for student projects
    ├── pilot-protocol.md            # Step-by-step pilot guide
    └── pilot-notes-template.md      # Qualitative notes template
```

---

## Contents Overview

### Instrumentation (`instr/`)

**Logger.kt**
- Thread-safe CSV logging
- Privacy-safe (session IDs only, no PII)
- Schema: `ts_iso,session_id,request_id,task_code,step,outcome,ms,http_status,js_mode`
- Usage: `Logger.write(session, req, task, step, outcome, ms, status, js)`

**Timing.kt**
- Higher-order function wrapper for request timing
- Integrates with Logger automatically
- Usage: `timed(call, "T1_filter", "submit") { /* route logic */ }`

**schema.md**
- Complete CSV column definitions
- Example rows
- Privacy notes

### Research Materials (`research/`)

**tasks.md**
- 3-4 task definitions for peer pilots
- Success criteria for each task
- Realistic scenarios (not feature-based)

**measures.md**
- Objective metrics: time-on-task, completion rate, error rate
- Subjective metrics: confidence (1-5), UMUX-Lite (1-7)
- Calculation formulas (median, MAD)

**protocol.md**
- Session flow: intro → consent → tasks → debrief
- Moderator script
- Timing approach
- Ethical considerations

**pilot-notes.md**
- Template for qualitative observations
- Severity ratings
- Linkage to backlog items

### Templates (`templates/`)

**For students to copy to their projects:**

- `Logger.kt` and `Timing.kt` - Ready to use in `src/main/kotlin/utils/`
- `consent-script.md` - Read to participants before starting
- `pilot-protocol.md` - Step-by-step instructions for running pilots
- `pilot-notes-template.md` - Structured observation recording

### Submission Structure (`submission/`)

**task1-draft/**
- Folder structure for Week 9 Lab 2 Task 1 evidence pack
- Students populate with:
  - `01-evaluation-plan.md`
  - `02-protocol.md`
  - `03-consent-log.md`
  - `04-results.csv`
  - `05-summary-stats.md`
  - `06-evidence/` (screenshots, cropped)

---

## Usage for Students

### Week 9 Lab 1: Setup Instrumentation

1. **Copy Logger and Timing to your project**:
   ```bash
   mkdir -p src/main/kotlin/utils
   cp consolidated/lab-wk9/templates/Logger.kt src/main/kotlin/utils/
   cp consolidated/lab-wk9/templates/Timing.kt src/main/kotlin/utils/
   ```

2. **Define tasks** (use `research/tasks.md` as guide):
   ```markdown
   # Task T1: Filter tasks
   Scenario: "Find all tasks containing 'invoice'..."
   Success: Correct count within 2 minutes
   ```

3. **Specify metrics** (use `research/measures.md`):
   - Time-on-task (ms)
   - Completion (0/1)
   - Errors (count)
   - Confidence (1-5)

4. **Write protocol** (use `research/protocol.md`):
   - Consent script
   - Task presentation order
   - Post-task questions

5. **Integrate instrumentation**:
   ```kotlin
   import utils.Logger
   import utils.timed

   post("/tasks/{id}/delete") {
       timed(call, "T3_delete", "submit") {
           // Your delete logic
           Logger.write(sid, rid, "T3_delete", "success", "ok", ms, 200, jsMode)
       }
   }
   ```

### Week 9 Lab 2: Run Pilots

1. **Review protocol** (`templates/pilot-protocol.md`)
2. **Prepare consent** (`templates/consent-script.md`)
3. **Run 4-5 peer pilots** (use `templates/pilot-notes-template.md`)
4. **Package evidence** in `submission/task1-draft/`

---

## Privacy & Ethics

### UK GDPR Compliance
- **No PII**: Session IDs are anonymous (e.g., `P1_7a9f2c`)
- **Verbal consent**: Required before each pilot
- **Opt-out**: Participants can withdraw at any time
- **Data storage**: Local CSV only (no cloud services)

### Low-Risk Research Protocol
- **Peer pilots**: No vulnerable participants
- **No recordings**: Audio/video not used
- **Pseudonyms**: Notes use participant codes only
- **Cropped screenshots**: Remove personal information

---

## References

### Lab Documentation
- Week 9 Lab 1: `wk09/src/wk9-lab1-eval-plan-instrumentation.md`
- Week 9 Lab 2: `wk09/src/wk9-lab2-pilots-debrief-draft.md`

### Supporting Guides
- `../../references/evaluation-metrics-quickref.md` - Quick metric formulas
- `../../references/consent-pii-faq.md` - Privacy questions answered
- `../../references/assistive-testing-checklist.md` - Accessibility verification

### External Resources
- [Nielsen Norman: Usability Testing 101](https://www.nngroup.com/articles/usability-testing-101/)
- [W3C: Measuring Accessibility](https://www.w3.org/WAI/test-evaluate/metrics/)
- [GOV.UK: User Research Ethics](https://www.gov.uk/service-manual/user-research/getting-users-consent-for-research)

---

## Consolidation Notes

**Merged from**:
- `wk09/lab-wk9/` (older structure, comprehensive content)
- `wk09/lab-w9/` (newer templates)

**Changes**:
- Combined `instr/` files with `templates/` (Logger/Timing appear in both for convenience)
- Updated README to reflect complete structure
- No content changes to actual files

**Original directories preserved** - this is a consolidated copy for clarity.

---

**Created**: 2025-10-14
**Module**: COMP2850 HCI
**Contact**: See module staff on Minerva
