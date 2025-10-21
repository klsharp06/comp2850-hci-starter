# Week 6 • Lab 2: Needs-Finding, Consent & Inclusive Backlog

![COMP2850](https://img.shields.io/badge/COMP2850-HCI-blue)
![Week 6](https://img.shields.io/badge/Week-6-orange)
![Lab 2](https://img.shields.io/badge/Lab-2-green)
![Status](https://img.shields.io/badge/Status-Draft-yellow)

---

## Terminology Note

Throughout COMP2850 we use **people-centred language** (e.g., "person using a screen reader") rather than deficit-based terms (e.g., "blind user"). This reflects contemporary inclusive-design practice and acknowledges that disability arises from environmental barriers, not individual impairment.

---

## Pre-reading

**Essential**
- [Ulwick, A. (2016). "Jobs to Be Done: Theory to Practice"](https://hbr.org/2016/09/know-your-customers-jobs-to-be-done)
- [ICO (2024). "Guide to Data Protection by Design and Default"](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-re../../references/)
- `../../references/consent-pii-faq.md` (module ethics guidance)
- `../../references/privacy-by-design.md` (UK GDPR compliance framework)

**Recommended**
- [Microsoft Inclusive Design Toolkit](https://inclusive.microsoft.design/)
- [Patton, J. (2014). *User Story Mapping*, Ch. 1-3](https://www.jpattonassociates.com/user-story-mapping/)
- [Nielsen, J. (1993). "Usability Engineering", Ch. 5: User Interviews](https://www.nngroup.com/articles/interviewing-users/)

---

## Introduction

### Context

In **Week 6 Lab 1** you built a server-first task manager with progressive enhancement (HTMX). The scaffold is technically sound: it works without JavaScript, keyboard navigation is accessible, and ARIA live regions announce status messages.

**But we don't yet know if it meets real people's needs.**

This lab introduces **needs-finding**: a lightweight research activity to understand how people work with task managers, what frustrates them, and what inclusive features would help. You'll capture insights using **job stories** (a people-centred alternative to user stories), document an **ethical consent protocol** for peer interviews, and build an **inclusive backlog** that will drive your accessibility work in Weeks 7-10.

### Why This Matters

**Professionally**, needs-finding prevents "solution looking for a problem":
- **Basecamp** (project management) conducts customer interviews every sprint
- **GOV.UK** runs user research sessions before building new services
- **GitHub** uses job stories to prioritise accessibility features

Research shows that teams who ground design decisions in **evidence** (pilot data, interviews, observations) build more inclusive products than teams who rely on assumptions (Norman, 2013).

**Academically**, this lab teaches:
- **Qualitative research methods**: interviewing, note-taking, thematic coding
- **Ethics in practice**: informed consent, anonymisation, right to withdraw (UK GDPR)
- **Jobs-to-Be-Done framework**: understanding needs vs. specifying solutions
- **Inclusive backlog management**: prioritising features by severity × inclusion risk

### Learning Outcomes

By the end of this lab, you will be able to:

| Learning Outcome | Module LO | ACM/WCAG Ref |
|---|---|---|
| Conduct peer interviews using structured prompts and consent protocols | LO1: Apply HCI research methods | ACM: HC/1 |
| Capture job stories that represent diverse needs (SR, keyboard, low-vision) | LO2: Understand people & inclusion | ACM: HC/2 |
| Document consent and data handling per UK GDPR | LO3: Apply ethical frameworks | BCS Code of Conduct |
| Build inclusive backlog with severity and inclusion-risk tagging | LO10: Evaluate accessibility systematically | WCAG 2.2 (process) |

---

## Key Concepts

### 1. Needs-Finding vs. Requirements Gathering

**Traditional requirements gathering**:
> "The system shall allow users to filter tasks by status (complete/incomplete)."

**Needs-finding**:
> "When I'm preparing for a crit, I want to focus only on deliverable tasks so I can check I haven't missed anything, because I get anxious about deadlines."

**Difference**:
- **Requirements** specify *solutions* ("filter by status")
- **Needs** uncover *motivations* ("reduce anxiety about missed deadlines")

**Why this matters**: Solutions can change (filter, search, tags, AI assistant), but needs stay constant. Understanding needs lets you pivot to better solutions as technology evolves.

### 2. Jobs-to-Be-Done (JTBD) Framework

**Job story template**:
```
When [situation/context],
I want to [motivation/goal],
so I can [desired outcome],
because [underlying need/constraint].
```

**Example (COMP2850 context)**:
```
When I'm using a screen reader to check my task list,
I want confirmation messages announced immediately after I delete a task,
so I can know the action succeeded without navigating back to the list,
because re-scanning 20 items to confirm deletion is time-consuming and error-prone.
```

**Contrast with persona-based user story**:
> "As a blind user, I want screen reader support so I can use the app."

**Problems with persona stories**:
- Assumes all "blind users" have same needs (heterogeneity ignored)
- Focuses on disability label, not situational context
- Doesn't explain *why* the need exists

**JTBD advantages**:
- Situation-specific (works for temporary impairments: broken mouse, bright sunlight)
- Explains motivation (builds empathy)
- Actionable (clear what success looks like)

### 3. Informed Consent (UK GDPR)

**UK GDPR principles** (Data Protection Act 2018):
1. **Lawfulness, fairness, transparency**: People must know what data you collect and why
2. **Purpose limitation**: Only collect data needed for stated purpose
3. **Data minimisation**: Collect as little as possible
4. **Accuracy**: Keep data correct and up-to-date
5. **Storage limitation**: Delete when no longer needed
6. **Integrity & confidentiality**: Protect from unauthorised access

**Low-risk peer research** (COMP2850 context):
- **No PII**: Use pseudonyms ("Participant A"), not names
- **Local storage**: CSV files on your machine, not cloud services
- **Opt-out**: Participant can withdraw at any time
- **No recordings**: Text notes only (unless explicit consent for audio)
- **Deletion plan**: Delete at end of Semester 1 (or anonymise for portfolio)

**Consent script elements**:
1. Purpose ("We're researching task manager usability")
2. What you'll do ("5-minute interview, taking notes")
3. Data stored ("Pseudonymised notes in local Git repo")
4. Rights ("You can stop at any time, request deletion")
5. Contact ("Email <module> to opt out")

### 4. Inclusive Backlog Structure

**Standard backlog**:
```
ID | Title | Priority | Effort
1  | Add filter | P1 | 3
```

**Inclusive backlog** (COMP2850 format):
```
ID | Title | Story Ref | Need | Type | Severity | Inclusion Risk | Evidence | Notes
1  | Filter remembers query | stories.md#S2 | Reduce cognitive load | Accessibility | High | Cognitive, SR | P2 notes L15 | Filter resets on reload; SR users lose context
```

**Why additional columns**:
- **Story Ref**: Traceable to research evidence
- **Inclusion Risk**: Tags who's affected (SR, keyboard, low-vision, cognitive, motor)
- **Evidence**: Links to pilot notes, WCAG audits, screenshots
- **Severity**: Impact on task completion (High = blocks, Medium = hinders, Low = cosmetic)

**Benefits**:
- Transparent prioritisation (not just "gut feel")
- Ensures diverse needs considered (not just mouse + sighted users)
- Creates evidence chain for assessments (Task 1, Task 2, studio crit)

### 5. Thematic Coding (Light Touch)

**Thematic coding** = systematically identifying patterns in qualitative data.

**Example process**:
1. **Read notes**: Highlight key phrases
2. **Code observations**: Tag with themes (e.g., `status_feedback`, `keyboard_nav`, `cognitive_load`)
3. **Group codes**: Cluster similar tags
4. **Synthesise themes**: "Participants struggle to confirm actions succeeded" → backlog item: "Add persistent confirmation messages"

**Tools** (COMP2850 level):
- Markdown with inline tags: `[status_feedback] P2 said "I didn't know if it saved"`
- Spreadsheet with code column
- Simple frequency count (how many times each theme appears)

---

## Activity 1: Setup & Consent Protocol

**Time**: 20 minutes
**Materials**: Git repo from Lab 1, consent script template

### Step 1: Create Research Directories

```bash
cd comp2850-hci-starter  # Your project from Lab 1
mkdir -p research
mkdir -p backlog
mkdir -p wk06/instrumentation

# Create placeholder files
touch wk06/research/consent-protocol.md
touch wk06/research/stories.md
touch wk06/research/notes.md
touch wk06/backlog/backlog.csv
touch wk06/instrumentation/plan.md
```

### Step 2: Document Consent Protocol

Edit `wk06/research/consent-protocol.md`:

```markdown
# Informed Consent Protocol — Week 6 Peer Interviews

**Module**: COMP2850 Human-Computer Interaction
**Activity**: Low-risk needs-finding (peer interviews)
**Date**: [YYYY-MM-DD]
**Researcher**: [Your Name/Student ID]

---

## Purpose

We are conducting short peer interviews (5-10 minutes) to understand how people use task managers and identify accessibility improvements. This is part of our HCI coursework for Weeks 6-11.

## What You'll Do

I will ask you 3-5 questions about your experience with task management tools (e.g., "Tell me about a time you struggled to find a specific task"). I'll take brief notes. **No recordings** will be made unless you explicitly agree (and I'll ask again before starting).

## Data Collected

- **What I will collect**:
  - Pseudonymised notes (e.g., "Participant A said...")
  - Timestamps of interview
  - Contextual tags (e.g., "Uses keyboard only", "Prefers dark mode")

- **What I will NOT collect**:
  - Your name (unless you want credit in acknowledgements)
  - Student ID number
  - Email address
  - Any other personally identifiable information (PII)

## Data Storage

- **Where**: Local Git repository on my laptop (private repo, not shared publicly)
- **Who can access**: Me, my lab partner, module teaching staff (if requested for marking)
- **How long**: Until end of Semester 1 (January 2025), then deleted OR anonymised for portfolio

## Your Rights (UK GDPR / Data Protection Act 2018)

- **Right to withdraw**: You can stop at any time, no explanation needed
- **Right to access**: You can ask to see your data (I'll show you my notes)
- **Right to erasure**: You can request I delete your data (email me with interview date/pseudonym)
- **Right to complain**: Contact University Data Protection Officer if concerned: dpo@leeds.ac.uk

## Consent Confirmation

Before starting, I will ask:
- [ ] Have I explained the purpose clearly?
- [ ] Do you understand what data I'll collect?
- [ ] Do you know you can stop at any time?
- [ ] Do you consent to participate?

**Verbal consent is sufficient** for this low-risk activity. If you say "yes", I'll note:
- Date/time: [YYYY-MM-DD HH:MM]
- Pseudonym assigned: [e.g., "Participant A"]
- Consent confirmed: [Initials]

---

## Opt-Out Process

If you change your mind after the interview:
1. Email me at [your-university-email]
2. Include: interview date and pseudonym (if you remember it)
3. I will delete all notes related to your session within 48 hours
4. I will confirm deletion via email

---

## Contact

**Researcher**: [Your Name], [your-email@leeds.ac.uk]
**Module Lead**: Dr. [Name], [module-email]
**University Ethics**: Research Ethics, ethics@leeds.ac.uk

---

**Template source**: COMP2850 HCI, University of Leeds
**Reference**: ICO (2024). Guide to GDPR, <https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-re../../references/>
```

### Step 3: Review with Lab Partner

Pair up with a classmate and:
1. Read each other's consent protocols
2. Check for **missing elements**:
   - ✅ Purpose clearly stated?
   - ✅ Data storage location specified?
   - ✅ Participant rights listed (withdraw, access, delete)?
   - ✅ Opt-out process described?
3. Suggest improvements (clearer language, missing details)

**Stop and check**:
- ✅ Consent protocol saved in `wk06/research/consent-protocol.md`
- ✅ Reviewed by peer (get their initials/signature in notes)
- ✅ Ready to use for interviews

---

## Activity 2: Conduct Peer Interviews

**Time**: 40 minutes (20 min each direction)
**Materials**: Interview prompts, note-taking template

### Step 1: Interview Prompts

Use these open-ended prompts (adapt as needed):

**General task management**:
1. "Tell me about the last time you used a to-do list or task manager. What were you trying to accomplish?"
2. "What frustrates you most about managing tasks? Can you give me a specific example?"
3. "Have you ever lost track of an important task? What happened?"

**Accessibility & interaction**:
4. "Do you ever work without a mouse (e.g., trackpad broken, using laptop on train)? How does that change how you interact with apps?"
5. "Have you used a task manager with your eyes closed or in bright sunlight? What was hard?"
6. "If you could add one feature to make task management less stressful, what would it be?"

**Follow-up probes**:
- "Can you tell me more about that?"
- "How did that make you feel?"
- "What did you do to work around it?"

### Step 2: Note-Taking Template

Create `wk06/research/notes.md`:

```markdown
# Interview Notes — Week 6

## Participant A
**Date**: [YYYY-MM-DD HH:MM]
**Context**: [e.g., Uses keyboard only, prefers dark mode, has ADHD]
**Consent**: ✅ Confirmed verbally
**Duration**: [~10 minutes]

### Q1: Last time you used a task manager
**Response**: "I use Notion for uni work. Last week I had to find all tasks tagged 'COMP2850' to prepare for a deadline. It took ages because the filter kept resetting."

**Observations**:
- Mentioned filter UX issue (cognitive load)
- Time pressure context (deadline stress)
- Tag-based workflow (not just chronological)

**Themes**: `filter_persistence`, `cognitive_load`, `deadline_anxiety`

---

### Q2: What frustrates you?
**Response**: "When I submit a form and nothing happens—like, did it save? I have to refresh the whole page to check."

**Observations**:
- Lack of confirmation feedback
- Low trust in interface
- Workaround = page reload (inefficient)

**Themes**: `status_feedback`, `confirmation`, `trust`

---

### Q3: Lost track of important task?
**Response**: "Yeah, I once forgot to submit coursework because it was buried in my list. I wish there was a way to pin urgent things."

**Observations**:
- List length issue (visibility)
- Prioritisation need
- Consequence = missed deadline (high impact)

**Themes**: `prioritisation`, `visibility`, `urgent_tasks`

---

### Q4: Work without a mouse?
**Response**: "My trackpad broke last month. I tried using Tab to navigate, but some buttons were impossible to reach. Had to borrow a friend's mouse."

**Observations**:
- Keyboard-only experience = friction
- Temporary impairment (broken hardware)
- Exclusion from features

**Themes**: `keyboard_nav`, `temporary_impairment`, `button_accessibility`

---

### Q5: Eyes closed / bright sunlight?
**Response**: "Haven't tried eyes closed, but in sunlight I can't read low-contrast text. I increase zoom but then the layout breaks."

**Observations**:
- Contrast issue (situational disability)
- Zoom breaks responsive design
- Environmental factor (sunlight)

**Themes**: `contrast`, `zoom`, `responsive_design`

---

### Q6: One feature to add?
**Response**: "A way to see progress—like, 'You've completed 8 out of 12 tasks this week.' That would motivate me."

**Observations**:
- Motivation through feedback
- Progress visualisation
- Weekly scope (not just daily)

**Themes**: `progress_tracking`, `motivation`, `feedback`

---

## Summary (Participant A)
**Top pain points**:
1. Filter resets → cognitive overload
2. No confirmation feedback → uncertainty
3. Keyboard navigation gaps → temporary exclusion
4. Contrast issues in bright light → situational disability

**Job story ideas**:
- "When I'm filtering tasks, I want the selection to persist across page reloads so I don't lose my place."
- "When I submit a form, I want immediate confirmation so I know it worked without refreshing."
- "When my mouse breaks, I want full keyboard access so I can still complete tasks."
```

**Repeat for Participant B, C, etc.**

### Step 3: Swap Roles

1. **Researcher → Participant**: Your partner interviews you (10 minutes)
2. **Participant → Researcher**: You interview your partner (10 minutes)

**After both interviews**:
- Compare notes
- Identify overlapping themes (both mentioned confirmation feedback?)
- Flag unique insights (only one person mentioned colour-blindness?)

**Stop and check**:
- ✅ At least 2 interviews completed (ideally 3-4)
- ✅ Notes saved in `wk06/research/notes.md`
- ✅ Themes tagged consistently (use same terms across interviews)

---

## Activity 3: Synthesise Job Stories

**Time**: 25 minutes
**Materials**: Interview notes, job story template

### Step 1: Extract Situations & Needs

Review `notes.md` and list **situations** where people struggled:

| Situation | Need | Evidence |
|-----------|------|----------|
| Filtering tasks for specific project | Filter persists across reloads | P-A notes L5 |
| Submitting a form | Confirmation feedback | P-A notes L12, P-B notes L8 |
| Using keyboard only (broken mouse) | Full keyboard access to all buttons | P-A notes L20 |
| Bright sunlight | High-contrast text | P-A notes L28 |
| Managing 20+ tasks | Visual progress indicator | P-A notes L35, P-B notes L15 |

### Step 2: Write Job Stories

Create `wk06/research/stories.md`:

```markdown
# Job Stories — Week 6 Needs-Finding

## Story S1: Filter Persistence
**Situation**: When I'm filtering tasks by project tag (e.g., "COMP2850")
**Motivation**: I want the filter selection to persist across page reloads
**Outcome**: So I can pick up where I left off without re-selecting the filter
**Underlying need**: Because re-filtering 10+ times per session creates cognitive overload and wastes time

**Evidence**: Participant A (notes L5), Participant B (notes L3)
**Inclusion risk**: Cognitive, memory impairment, ADHD
**Type**: Job story (situation-specific)

---

## Story S2: Confirmation Feedback
**Situation**: When I submit a form (add task, edit task, delete task)
**Motivation**: I want immediate, explicit confirmation that the action succeeded
**Outcome**: So I can trust the interface without refreshing to verify
**Underlying need**: Because uncertainty about save status causes anxiety and inefficient workarounds (page reload)

**Evidence**: Participant A (notes L12), Participant B (notes L8)
**Inclusion risk**: Cognitive, screen reader (if confirmation not announced), low digital literacy
**Type**: Job story

---

## Story S3: Full Keyboard Access
**Situation**: When my mouse/trackpad is unavailable (broken hardware, RSI flare-up, preference)
**Motivation**: I want to access all features using only Tab, Enter, Space, and arrow keys
**Outcome**: So I can complete tasks without being excluded
**Underlying need**: Because reliance on pointing device excludes people with motor impairments or temporary injuries

**Evidence**: Participant A (notes L20)
**Inclusion risk**: Motor impairment, RSI, temporary disability, keyboard-only preference
**Type**: Job story
**WCAG**: 2.1.1 Keyboard (A), 2.1.3 Keyboard (No Exception, AAA)

---

## Story S4: High Contrast
**Situation**: When I'm working in bright sunlight or have low vision
**Motivation**: I want text to have sufficient contrast against background
**Outcome**: So I can read task titles and buttons without straining
**Underlying need**: Because low contrast creates situational disability (sunlight) or permanent exclusion (low vision)

**Evidence**: Participant A (notes L28)
**Inclusion risk**: Low vision, colour-blindness, situational (bright light)
**Type**: Job story
**WCAG**: 1.4.3 Contrast (Minimum, AA) — 4.5:1 for normal text

---

## Story S5: Progress Visualisation
**Situation**: When I'm managing a long task list (15+ items)
**Motivation**: I want to see completion progress (e.g., "8/12 done this week")
**Outcome**: So I can feel motivated and track productivity
**Underlying need**: Because invisible progress reduces motivation and makes it hard to assess workload

**Evidence**: Participant A (notes L35), Participant B (notes L15)
**Inclusion risk**: Cognitive, ADHD (executive function support)
**Type**: Job story

---

## Story S6: Persistent Error Messages (No-JS)
**Situation**: When JavaScript is disabled (corporate firewall, data-saving mode) and I submit invalid data
**Motivation**: I want error messages to persist after page reload
**Outcome**: So I can understand what went wrong and correct it
**Underlying need**: Because ephemeral error messages (lost on redirect) require perfect memory or multiple submission attempts

**Evidence**: Inferred from Lab 1 no-JS testing; no explicit interview mention (add if time)
**Inclusion risk**: Cognitive, screen reader (needs page-level error summary)
**Type**: Pain point (internally identified)
**WCAG**: 3.3.1 Error Identification (A), 3.3.3 Error Suggestion (AA)
```

### Step 3: Map Stories to WCAG

For each story, check if it relates to a WCAG criterion:

| Story | WCAG Criterion | Level |
|-------|----------------|-------|
| S3 (Keyboard access) | 2.1.1 Keyboard | A |
| S4 (Contrast) | 1.4.3 Contrast (Minimum) | AA |
| S6 (Error messages) | 3.3.1 Error Identification | A |

**Why this matters**: When you build the backlog, you can justify priority with "This is a WCAG Level A failure" vs. "This is a nice-to-have."

**Stop and check**:
- ✅ 5-6 job stories written
- ✅ Each story cites evidence (participant + line number)
- ✅ Inclusion risk tags added (SR, keyboard, cognitive, etc.)
- ✅ At least 2 stories map to WCAG criteria

---

## Activity 4: Build Inclusive Backlog

**Time**: 30 minutes
**Materials**: Job stories, `backlog.csv` template

### Step 1: Create CSV Headers

Edit `wk06/backlog/backlog.csv`:

```csv
id,title,story_ref,story_type,need,type,severity,inclusion_risk,evidence,notes,candidate_fix
```

**Column definitions**:
- **id**: Unique number (1, 2, 3...)
- **title**: Short summary (< 10 words)
- **story_ref**: Link to `stories.md#S1` or `notes.md#L15`
- **story_type**: "Job story", "Pain point", "WCAG violation", "Research insight"
- **need**: High-level category ("Confirmation feedback", "Keyboard access")
- **type**: "Accessibility", "Usability", "Performance", "Tech debt"
- **severity**: High (blocks task), Medium (hinders), Low (cosmetic)
- **inclusion_risk**: Comma-separated tags (SR, Keyboard, Cognitive, Motor, Low vision)
- **evidence**: Path to notes, screenshot, WCAG reference
- **notes**: Free-form (root cause, affected flows)
- **candidate_fix**: `true` if planning to fix in Week 7 (pick 1-2)

### Step 2: Populate Backlog Rows

Example entries:

```csv
id,title,story_ref,story_type,need,type,severity,inclusion_risk,evidence,notes,candidate_fix
1,Filter selection resets on page reload,stories.md#S1,Job story,Reduce cognitive load,Usability,Medium,"Cognitive,Memory","P-A notes L5",Current implementation doesn't persist filter state in session or URL,false
2,No confirmation after form submission,stories.md#S2,Job story,Trust & feedback,Accessibility,High,"Cognitive,SR,Low digital literacy","P-A notes L12; P-B notes L8",HTMX shows status in live region (good) but no-JS path gives no feedback (PRG redirect with no message),true
3,Delete button not keyboard-accessible,stories.md#S3,Job story,Full keyboard access,Accessibility,High,"Keyboard,Motor","P-A notes L20; WCAG 2.1.1","Inline form works but focus order unclear; test with Tab-only navigation",true
4,Text contrast below 4.5:1 in default theme,stories.md#S4,Job story,Visibility,Accessibility,Medium,"Low vision,Colour-blind,Situational","P-A notes L28; WCAG 1.4.3",Pico.css default gray (#6c757d) on white = 4.2:1 (fails AA); needs adjustment,false
5,No progress indicator for completed tasks,stories.md#S5,Job story,Motivation & feedback,Usability,Low,Cognitive,"P-A notes L35; P-B notes L15",Nice-to-have; defer to Semester 2 backlog,false
6,Error messages not persistent in no-JS path,stories.md#S6,Pain point,Error recovery,Accessibility,High,"Cognitive,SR","Internal testing; WCAG 3.3.1",PRG redirect loses error context; need error summary on target page,true
7,Skip link not tested with screen reader,Internal audit,WCAG check,Bypass blocks,Accessibility,Medium,SR,"WCAG 2.4.1",Lab 1 tested keyboard focus but not SR announcement; verify with NVDA,false
8,Live region uses polite (should be assertive for errors),Internal audit,WCAG check,Error announcement,Accessibility,High,SR,"WCAG 4.1.3",Errors need aria-live=assertive so SR interrupts to announce; success messages stay polite,false
```

### Step 3: Prioritise with Severity × Inclusion

**Severity scoring**:
- **High (3 points)**: Blocks task completion or excludes a group
- **Medium (2 points)**: Makes task harder but workaround exists
- **Low (1 point)**: Cosmetic or enhancement

**Inclusion weight**:
- **Multiple groups affected**: +1 point (e.g., SR + Keyboard + Cognitive = +1)
- **WCAG Level A failure**: +2 points
- **WCAG Level AA failure**: +1 point

**Example calculation** (Item #2):
- Severity: High (3)
- Inclusion: SR + Cognitive + Low digital literacy (3 groups) = +1
- WCAG: 4.1.3 (AA) = +1
- **Total priority**: 3 + 1 + 1 = 5 → **Fix in Week 7**

**Example calculation** (Item #5):
- Severity: Low (1)
- Inclusion: Cognitive only (1 group) = 0
- WCAG: N/A = 0
- **Total priority**: 1 → **Defer to Semester 2**

### Step 4: Select Candidate Fixes

Mark 1-2 items with `candidate_fix=true`. These are the issues you'll fix in **Week 7 Lab 2**. Criteria:
- High severity + high inclusion risk
- Fixable in 1-2 hours (scoped for lab time)
- Clear WCAG criterion to verify against

**Recommended first fixes**:
1. Item #2 (No confirmation in no-JS) → Add error summary
2. Item #3 (Keyboard access) → Verify Tab order, add aria-labels
3. Item #8 (Live region assertive for errors) → Change `aria-live` attribute

**Stop and check**:
- ✅ At least 8 backlog items (mix of research + WCAG audit)
- ✅ Severity and inclusion risk assigned to each
- ✅ 1-2 items marked as candidate fixes
- ✅ Evidence column links to notes or WCAG reference

---

## Activity 5: Instrumentation Planning (Teaser)

**Time**: 15 minutes
**Materials**: `wk06/instrumentation/plan.md`

In **Week 9** you'll run task-based pilots and collect metrics (time-on-task, error rate, completion rate). Today you'll sketch what data to capture.

### Step 1: Identify Events to Log

Based on your backlog, list key events:

| Event | Why Log It | Fields to Capture |
|-------|-----------|-------------------|
| `task_created` | Measure add task completion time | `ts_iso`, `session_id`, `title_length`, `js_mode` |
| `task_deleted` | Measure delete task completion time | `ts_iso`, `session_id`, `task_id`, `js_mode` |
| `validation_error` | Count errors (usability metric) | `ts_iso`, `session_id`, `field`, `error_type`, `js_mode` |
| `filter_applied` | Track filter usage (S1 story) | `ts_iso`, `session_id`, `filter_value`, `result_count` |

### Step 2: Draft Instrumentation Plan

Edit `wk06/instrumentation/plan.md`:

```markdown
# Instrumentation Plan — Week 6

**Purpose**: Capture objective metrics for Week 9 task-based pilots and Week 10 analysis.

---

## Events to Log

### 1. Task Created
**Trigger**: POST /tasks (success)
**Fields**:
- `ts_iso`: ISO 8601 timestamp (e.g., 2025-01-15T14:23:45Z)
- `session_id`: Anonymous 6-char hex (e.g., `P1_a3f7`)
- `request_id`: Unique per request (for tracing)
- `task_code`: `T3_add` (pilot task identifier)
- `step`: `submit`
- `outcome`: `success` | `validation_error`
- `ms`: Time from request start to response (server-side)
- `http_status`: 200 (success) | 400 (validation error)
- `js_mode`: `js-on` | `js-off`

**Why**: Measure task completion time, compare HTMX vs. no-JS.

---

### 2. Validation Error
**Trigger**: POST /tasks (blank title)
**Fields**: Same as Task Created, but `outcome=validation_error`, `http_status=400`

**Why**: Count errors as usability metric; high error rate = poor UX.

---

### 3. Task Deleted
**Trigger**: POST /tasks/{id}/delete (success)
**Fields**: Same structure as Task Created, but `task_code=T4_delete`

**Why**: Measure delete task time; verify live region announcement.

---

## Data Storage

- **Format**: CSV (human-readable, easy to analyse in Excel/Google Sheets)
- **Location**: `data/metrics.csv` (local file, not cloud)
- **Schema**:
  ```csv
  ts_iso,session_id,request_id,task_code,step,outcome,ms,http_status,js_mode
  ```

**Example rows**:
```csv
2025-01-15T14:23:45Z,P1_a3f7,req_001,T3_add,submit,success,120,200,js-on
2025-01-15T14:24:10Z,P1_a3f7,req_002,T4_delete,submit,success,85,200,js-on
2025-01-15T14:25:00Z,P2_b8c4,req_003,T3_add,submit,validation_error,0,400,js-off
```

---

## Ethics & Privacy

- **No PII**: Session IDs are random hex (not linked to names/emails)
- **Anonymisation**: Use `P1`, `P2` pseudonyms (consistent with interview notes)
- **Consent**: Covered by Week 6 consent protocol (low-risk research)
- **Retention**: Delete after Semester 1 or anonymise for portfolio

---

## Implementation (Week 9)

Create `src/main/kotlin/utils/Logger.kt`:
```kotlin
object Logger {
    private val file = File("data/metrics.csv").apply {
        parentFile?.mkdirs()
        if (!exists()) writeText("ts_iso,session_id,request_id,task_code,step,outcome,ms,http_status,js_mode\n")
    }

    @Synchronized
    fun write(session: String, req: String, task: String, step: String, outcome: String, ms: Long, status: Int, js: String) {
        val ts = DateTimeFormatter.ISO_INSTANT.format(Instant.now())
        file.appendText("$ts,$session,$req,$task,$step,$outcome,$ms,$status,$js\n")
    }
}
```

---

**Next steps**: In Week 9 Lab 1, integrate Logger into routes and test with manual pilots.
```

**Stop and check**:
- ✅ Instrumentation plan saved
- ✅ Events identified (at least 3)
- ✅ CSV schema defined
- ✅ Ethics considerations documented

---

## Reflection Questions

1. **Job stories vs. requirements**: Look at your backlog. How would the items differ if you'd written traditional requirements ("The system shall...") instead of job stories?

2. **Consent trade-offs**: Our protocol uses pseudonyms and no recordings. What insights might we *miss* by not recording audio? When would recordings be justified?

3. **Inclusion risk tagging**: Review your backlog. Did you find issues that affect *only* screen reader users, or do most issues affect multiple groups? What does this tell you about inclusive design?

4. **Evidence chains**: Pick one backlog item. Can you trace it from interview notes → job story → backlog → (future) fix → verification? What's missing in your chain?

---

## Further Reading

**Jobs-to-Be-Done**
- Ulwick, A. (2016). "Jobs to Be Done: Theory to Practice." *Harvard Business Review*. <https://hbr.org/2016/09/know-your-customers-jobs-to-be-done>
- Christensen, C. M., Hall, T., Dillon, K., & Duncan, D. S. (2016). *Competing Against Luck*. HarperCollins.

**Qualitative research methods**
- Lazar, J., Feng, J. H., & Hochheiser, H. (2017). *Research Methods in Human-Computer Interaction* (2nd ed.). Morgan Kaufmann. (Ch. 9: Interviews & Focus Groups)
- Braun, V., & Clarke, V. (2006). "Using thematic analysis in psychology." *Qualitative Research in Psychology*, 3(2), 77-101.

**Ethics & GDPR**
- ICO (2024). *Guide to the UK GDPR*. <https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-re../../references/>
- BCS (2022). *Code of Conduct*. <https://www.bcs.org/membership-and-registrations/become-a-member/bcs-code-of-conduct/>

**Inclusive design**
- Microsoft (2024). *Inclusive Design Toolkit*. <https://inclusive.microsoft.design/>
- Holmes, K. (2018). *Mismatch: How Inclusion Shapes Design*. MIT Press.

---

## Glossary Summary

| Term | Definition | Example/Context |
|------|------------|-----------------|
| **Needs-finding** | Research to understand people's motivations, not just feature requests | Interviews reveal "I want confirmation" (need) vs. "Add a popup" (solution) |
| **Job story** | Situation-specific story format: When/I want/So I can/Because | "When filtering, I want persistence so I don't lose context because re-filtering is tedious" |
| **Informed consent** | Explaining research purpose, data use, and participant rights before collecting data | Consent script explains what you'll note, where it's stored, how to opt out |
| **Pseudonym** | Fake name to protect identity | "Participant A" instead of "Alice Smith" |
| **PII (Personally Identifiable Information)** | Data that can identify an individual (name, email, student ID) | Names = PII; random session IDs ≠ PII |
| **UK GDPR** | Data protection law (Data Protection Act 2018) | Right to access, delete, withdraw consent |
| **Inclusive backlog** | Backlog with severity + inclusion risk tags | "High severity, affects SR + Keyboard users" |
| **Thematic coding** | Identifying patterns in qualitative data | Tag interview notes with `confirmation`, `keyboard_nav` themes |
| **Severity** | Impact on task completion (High/Medium/Low) | High = blocks completion; Low = cosmetic |
| **Inclusion risk** | Who's affected (SR, Keyboard, Cognitive, Motor, Low vision) | SR + Cognitive = multiple groups at risk |

---

## Lab Checklist

Before leaving lab, confirm:

- [ ] **Consent protocol written**: `wk06/research/consent-protocol.md` complete with opt-out process
- [ ] **Interviews completed**: At least 2 peer interviews (ideally 3-4)
- [ ] **Notes saved**: `wk06/research/notes.md` with themes tagged
- [ ] **Job stories written**: 5-6 stories in `wk06/research/stories.md` with evidence links
- [ ] **Backlog populated**: 8+ items in `wk06/backlog/backlog.csv` with severity + inclusion risk
- [ ] **Candidate fixes selected**: 1-2 items marked `candidate_fix=true`
- [ ] **Instrumentation plan drafted**: `wk06/instrumentation/plan.md` outlines metrics for Week 9
- [ ] **Code committed**: `git add wk06/`, `git commit -m "wk6-lab2: needs-finding + consent + backlog"`

---

## Next Steps

In **Week 7 Lab 1** you will:
1. Review ethics guidance from guest speaker
2. Implement accessible inline edit (dual-path HTMX + no-JS)
3. Add validation with `aria-describedby` and `role="alert"`
4. Test with screen reader (NVDA/VoiceOver)

**Preparation**:
- Ensure scaffold from Lab 1 still runs (`./gradlew run`)
- Review backlog items marked `candidate_fix=true`
- Install NVDA (Windows) or enable VoiceOver (macOS) for testing

---

**Lab authored by**: COMP2850 Teaching Team, University of Leeds
**Last updated**: 2025-01-14
**Licence**: Academic use only (not for redistribution)
