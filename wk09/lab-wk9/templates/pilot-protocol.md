# Pilot Protocol Template

**Module**: COMP2850 HCI
**Week**: 9
**Pilot ID**: [e.g., P1, P2, P3, P4]
**Date**: [YYYY-MM-DD]
**Researcher**: [Your Name]

---

## Pre-Pilot Setup (5 minutes)

### 1. Generate Session ID
```bash
# Generate random 6-char hex ID
openssl rand -hex 3
# Example output: 7a9f2c
```

**Session ID**: `P#_______` (e.g., `P1_7a9f2c`)

### 2. Set Cookie (Browser Console)
```javascript
document.cookie = "sid=P1_7a9f2c; path=/";
```

**Verify**: Refresh page; check Application → Cookies → `sid` = `P1_7a9f2c`

### 3. Confirm Setup
- [ ] Application running on `http://localhost:8080/tasks`
- [ ] Browser configured (JS on/off, assistive tech if needed)
- [ ] `data/metrics.csv` ready (headers present)
- [ ] Consent script completed
- [ ] Pilot notes file created: `pilot-notes/P#_notes.md`

---

## Task Execution Protocol

For each task (T3, T1, T2, T4):

1. **Read task aloud**:
   > "Your task is to [describe task]. Let me know when you're done."

2. **Start timer** (manual or automated):
   ```
   [YYYY-MM-DD HH:MM:SS] Task [T#] START
   ```

3. **Observe silently**:
   - Do NOT guide the pilot unless they ask
   - Note any:
     - Hesitations (e.g., "Pilot paused for 5s")
     - Errors (e.g., "Validation message shown")
     - Unexpected behaviour (e.g., "Pilot used Back button")

4. **Stop timer when pilot says "done"**:
   ```
   [YYYY-MM-DD HH:MM:SS] Task [T#] END
   Duration: [##] seconds
   ```

5. **Check Logger output** (browser console or `data/metrics.csv`):
   ```
   ts_iso,session_id,request_id,task_code,step,outcome,ms,http_status,js_mode
   2025-01-10T14:35:22Z,P1_7a9f2c,req_001,T3_add,submit,success,450,200,js-on
   ```

6. **Ask confidence rating**:
   > "On a scale of 1 (not confident) to 5 (very confident), how confident are you that you completed this task correctly?"

   **Rating**: [ ] 1  [ ] 2  [ ] 3  [ ] 4  [ ] 5

7. **Record in pilot notes** (see template below)

---

## Task Definitions

### T3: Add Task (Baseline)
> "Add a new task with the title 'Buy milk'."

**Success criteria**:
- Task appears in list
- Title exactly matches "Buy milk"
- Status message confirms addition (if applicable)

### T1: Filter Tasks
> "Show only incomplete tasks by using the filter."

**Success criteria**:
- Filter applied (completed tasks hidden)
- URL includes `?filter=incomplete` (no-JS) or status updated (HTMX)
- Incomplete tasks visible

### T2: Edit Task (Accessibility-critical)
> "Change the title of 'Buy milk' to 'Buy oat milk'."

**Success criteria**:
- Inline edit activated (button clicked or keyboard shortcut)
- New title saved
- List updates to show "Buy oat milk"

### T4: Delete Task (Confirmation)
> "Delete the 'Buy oat milk' task."

**Success criteria**:
- Task removed from list
- Status message confirms deletion (if applicable)

---

## Pilot Notes Template

**File**: `pilot-notes/P#_notes.md`

```markdown
# Pilot [#] Notes

**Session ID**: P#_______
**Date**: [YYYY-MM-DD]
**Browser**: [Chrome/Firefox/Safari]
**JS Mode**: [js-on / js-off]
**Assistive Tech**: [None / NVDA / VoiceOver / keyboard-only]
**Input Method**: [Mouse / Keyboard / Touch]

---

## Task 3: Add Task (Buy milk)
- **Start**: [HH:MM:SS]
- **End**: [HH:MM:SS]
- **Duration**: [##s]
- **Outcome**: [Success / Partial / Failure]
- **Errors**: [None / "Title is required" / Other]
- **Confidence**: [1-5]
- **Observations**:
  - [e.g., "Pilot clicked 'Add' button immediately; no hesitation"]
  - [e.g., "Validation error triggered; pilot read message and corrected"]

---

## Task 1: Filter Tasks (Show incomplete)
- **Start**: [HH:MM:SS]
- **End**: [HH:MM:SS]
- **Duration**: [##s]
- **Outcome**: [Success / Partial / Failure]
- **Errors**: [None / Other]
- **Confidence**: [1-5]
- **Observations**:
  - [e.g., "Pilot used dropdown; filter applied instantly (HTMX)"]

---

## Task 2: Edit Task (Buy milk → Buy oat milk)
- **Start**: [HH:MM:SS]
- **End**: [HH:MM:SS]
- **Duration**: [##s]
- **Outcome**: [Success / Partial / Failure]
- **Errors**: [None / Focus issue / SR announcement missing / Other]
- **Confidence**: [1-5]
- **Observations**:
  - [e.g., "Pilot tabbed to Edit button; pressed Enter; focus moved to input"]
  - [e.g., "NVDA announced 'Title, edit, Buy milk' correctly"]

---

## Task 4: Delete Task (Buy oat milk)
- **Start**: [HH:MM:SS]
- **End**: [HH:MM:SS]
- **Duration**: [##s]
- **Outcome**: [Success / Partial / Failure]
- **Errors**: [None / Other]
- **Confidence**: [1-5]
- **Observations**:
  - [e.g., "Pilot clicked Delete; task removed via OOB swap (no page reload)"]

---

## Overall Observations
- **Most challenging task**: [T#]
- **Smoothest task**: [T#]
- **Unexpected behaviours**: [List any]
- **Verbatim quotes** (useful for qualitative analysis):
  - "[e.g., 'I didn't realise the edit button was there at first']"
  - "[e.g., 'The error message was really helpful']"

---

## Technical Notes
- **Console errors**: [None / Screenshot saved / Other]
- **Logger entries**: [Verified in metrics.csv / Issues noted below]
- **Network requests**: [HTMX partials / Full-page reloads / Both]
```

---

## Post-Pilot Checklist

- [ ] All 4 tasks completed (or pilot withdrew consent)
- [ ] `data/metrics.csv` updated with all rows
- [ ] `pilot-notes/P#_notes.md` saved with observations
- [ ] Confidence ratings recorded (1–5 for each task)
- [ ] Debrief completed (open questions asked)
- [ ] Browser console screenshot saved (if errors occurred)
- [ ] Thank pilot and confirm data deletion rights

---

**Template source**: Week 9 Lab Pack, COMP2850 (University of Leeds)
**References**: Week 9 Lab 1 (Activity 4), `../../../references/evaluation-metrics-quickref.md`
