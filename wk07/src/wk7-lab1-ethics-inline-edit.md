# Week 7 • Lab 1: Ethics in Practice & Accessible Inline Edit

![COMP2850](https://img.shields.io/badge/COMP2850-HCI-blue)
![Week 7](https://img.shields.io/badge/Week-7-orange)
![Lab 1](https://img.shields.io/badge/Lab-1-green)
![Status](https://img.shields.io/badge/Status-Draft-yellow)

---

## Terminology Note

Throughout COMP2850 we use **people-centred language** (e.g., "person using a screen reader") rather than deficit-based terms (e.g., "blind user"). This reflects contemporary inclusive-design practice and acknowledges that disability arises from environmental barriers, not individual impairment.

---

## Pre-reading

**Essential**
- [W3C (2024). WCAG 2.2: 3.3.1 Error Identification](https://www.w3.org/WAI/WCAG22/Understanding/error-identification.html)
- [W3C (2024). WCAG 2.2: 4.1.3 Status Messages](https://www.w3.org/WAI/WCAG22/Understanding/status-messages.html)
- [HTMX: Click to Edit Pattern](https://htmx.org/examples/click-to-edit/)
- `../../references/example-accessible-inline-edit.md` (worked example)

**Recommended**
- [GOV.UK: Error Message Pattern](https://design-system.service.gov.uk/components/error-message/)
- [Deque: ARIA Live Regions](https://www.deque.com/blog/aria-live-regions-updated/)
- `../../references/privacy-by-design.md` (ethics framework)

---

## Introduction

### Context

In **Week 6** you built a server-first task manager with add/delete functionality. The scaffold works without JavaScript, uses ARIA live regions for status announcements, and follows the PRG (Post-Redirect-Get) pattern.

**This week you'll add inline editing**—one of the most challenging patterns to make accessible. Done poorly, inline edit breaks screen reader navigation, loses keyboard focus, and excludes people using assistive technology. Done well, it provides a seamless experience across HTMX, no-JS, keyboard, and screen reader interaction modes.

You'll also review **ethics in practice**: how consent, data minimisation, and privacy-by-design shape implementation decisions (not just policy documents).

### Why This Matters

**Professionally**, inline edit is everywhere:
- **Trello** (task management): click card title to edit
- **Notion** (knowledge base): click any block to edit
- **GitHub** (issue tracking): click issue title to edit

All these examples face the same accessibility challenge: how do you swap view mode ↔ edit mode without losing context for screen reader users?

**Academically**, this lab demonstrates:
- **Dual-path architecture**: Same feature, different implementation (HTMX vs. no-JS)
- **Progressive disclosure**: Edit form only appears when needed (not cluttering UI)
- **ARIA best practices**: `aria-describedby`, `role="alert"`, focus management

### Learning Outcomes

By the end of this lab, you will be able to:

| Learning Outcome | Module LO | ACM/WCAG Ref |
|---|---|---|
| Apply informed consent protocols from Week 6 research | LO1: HCI research methods | BCS Code of Conduct |
| Implement dual-path inline edit (HTMX + no-JS) | LO6: Integrate web technologies | ACM: SEP/3 |
| Create accessible validation with ARIA error identification | LO9: Apply accessibility standards | WCAG 3.3.1 (A) |
| Test with keyboard and screen reader (NVDA/VoiceOver) | LO10: Evaluate inclusive design | WCAG 2.1.1, 4.1.3 |

---

## Key Concepts

### 1. Inline Edit Pattern

**Inline edit** = editing content in place (not navigating to a separate edit page).

**View mode** → **Edit mode** → **View mode**

**Traditional approach** (separate edit page):
```
View: GET /tasks/1 → shows task details
Edit: GET /tasks/1/edit → shows edit form
Save: POST /tasks/1 → validates, redirects to /tasks/1
```

**Inline edit approach**:
```
View: Renders view template (title as text)
Edit: Swaps view → edit template (title as input)
Save: Swaps edit → view template (updated title)
```

**Why inline edit is harder for accessibility**:
- Focus moves between elements (button → input → button)
- Screen reader must announce mode change
- Cancel action must restore original state
- Keyboard shortcuts conflict (Enter = submit vs. newline)

### 2. Dual-Path Implementation

**HTMX path** (JavaScript enabled):
1. User clicks "Edit" button
2. `hx-get="/tasks/1/edit"` → server returns edit form HTML fragment
3. `hx-target="#task-1" hx-swap="outerHTML"` → replaces `<li>` with form
4. User edits, clicks "Save"
5. `hx-post="/tasks/1/edit"` → server validates, returns updated view fragment
6. HTMX swaps form → view mode
7. OOB status update announces "Task updated"

**No-JS path** (JavaScript disabled):
1. User clicks "Edit" button
2. GET `/tasks/1/edit` → full-page reload with edit form shown
3. User edits, clicks "Save"
4. POST `/tasks/1/edit` → validates, redirects to `/tasks` (PRG)
5. Page reloads showing updated task

**Key invariant**: Both paths end in same state (task updated), just different UX (instant swap vs. page reload).

### 3. ARIA Error Identification (WCAG 3.3.1)

**WCAG 3.3.1 (Level A)**: If an input error is detected, the item in error is identified and described to the user in text.

**Requirements**:
1. Error must be **identified**: Label or instruction shows which field has error
2. Error must be **described**: Specific message (not just "Invalid")
3. Error must be **programmatically associated**: Screen reader announces error with field

**Implementation**:
```html
<label for="title-1">Title</label>
<input id="title-1" name="title"
       aria-describedby="hint-1 error-1">
<small id="hint-1">Keep it short and specific.</small>
<p id="error-1" role="alert">Title is required. Please enter at least one character.</p>
```

**How it works**:
1. Input links to error via `aria-describedby="error-1"`
2. Screen reader announces: "Title, edit, Keep it short and specific, Title is required. Please enter at least one character."
3. `role="alert"` makes screen reader interrupt to announce error

### 4. Focus Management

**Problem**: When HTMX swaps DOM elements, focus can be lost.

**Example**:
```
1. User focuses "Edit" button
2. Clicks Enter (activates button)
3. HTMX swaps <li> → <form>
4. Focus lost (button no longer exists)
```

**Solutions**:
- **HTMX attempts restoration**: If swapped element has same `id`, HTMX tries to restore focus
- **Manual focus**: Use `hx-on::after-swap="document.getElementById('title-1').focus()"`
- **Tab order preserved**: Ensure new elements appear in logical position

**WCAG 2.4.3 (Focus Order, A)**: If a web page can be navigated sequentially, focusable components receive focus in an order that preserves meaning and operability.

### 5. Status Messages (WCAG 4.1.3)

**WCAG 4.1.3 (Level AA)**: Status messages can be programmatically determined through role or properties such that they can be presented to the user by assistive technologies without receiving focus.

**Status message** = information about the outcome of an action (success, error, progress).

**Implementation**:
```html
<!-- In base.peb -->
<div id="status" role="status" aria-live="polite"></div>

<!-- Server response (HTMX OOB) -->
<div id="status" hx-swap-oob="true">Task "Buy milk" updated successfully.</div>
```

**Why this matters**: Screen reader users need confirmation that save succeeded, but moving focus to a status message disrupts workflow. Live regions solve this by announcing without focus change.

---

## Activity 1: Ethics Refresher & Data Boundaries

**Time**: 20 minutes
**Materials**: Week 6 consent protocol, privacy-by-design guidance

### Step 1: Review Consent Protocol

Open `wk06/research/consent-protocol.md` from Week 6 Lab 2.

**Check**:
- ✅ Purpose clearly stated?
- ✅ Data storage location specified (local Git repo)?
- ✅ Participant rights listed (withdraw, access, delete)?
- ✅ Opt-out process described?

**If missing any**, update now before Week 7 interviews/testing.

### Step 2: Identify Data Boundaries

Create `wk07/ethics/data-boundaries.md`:

```markdown
# Data Boundaries — Week 7

## What We Collect (Allowed)
- **Pseudonymised session IDs**: Random 6-char hex (e.g., `P1_a3f7`)
- **Task metadata**: Title, completion status, timestamps
- **Interaction logs**: HTTP requests, response times, error codes
- **Accessibility testing notes**: "NVDA announced X", "Focus moved to Y"

## What We DO NOT Collect (Prohibited)
- ❌ Real names (use pseudonyms: Participant A, P1, etc.)
- ❌ Student ID numbers
- ❌ Email addresses
- ❌ IP addresses (Ktor logs disabled in production)
- ❌ Content of tasks beyond module examples (e.g., no personal to-do items)

## Storage & Retention
- **Location**: Local CSV files (`data/tasks.csv`, `data/metrics.csv`)
- **Access**: Researcher, lab partner, module staff (on request)
- **Encryption**: Standard filesystem permissions (chmod 600)
- **Deletion**: End of Semester 1 (January 2025) OR anonymised for portfolio

## Privacy by Design Principles Applied
1. **Data minimisation**: Only collect session ID (not name)
2. **Purpose limitation**: Metrics used only for HCI evaluation (not sold/shared)
3. **Storage limitation**: Delete after assessment complete
4. **Integrity & confidentiality**: Local storage (not cloud), Git repo private

## Ethics Risks Identified
| Risk | Mitigation |
|------|-----------|
| Task titles contain sensitive info | Provide example tasks ("Buy milk"); warn against personal content |
| Session IDs linked to participants | Use randomised IDs; don't store mapping |
| Git repo accidentally public | Verify `.git/config` remote is private; add `.gitignore` for `/data` |
| Screenshots include PII | Crop to relevant UI; blur names if visible |

---

**Reference**: ICO (2024). Guide to GDPR, <https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-re../../references/>
```

### Step 3: Update `.gitignore`

Ensure sensitive data isn't committed:

```bash
# Add to .gitignore
/data/tasks.csv
/data/metrics.csv
/wk*/research/*notes.md  # Interview notes with pseudonyms
*.log
```

**Rationale**: Even with pseudonyms, raw interview notes could be re-identified in small cohorts. Keep them local, don't push to remote.

**Stop and check**:
- ✅ Data boundaries documented
- ✅ `.gitignore` updated to exclude sensitive files
- ✅ Consent protocol from Week 6 still valid

---

## Activity 2: Implement View/Edit Templates

**Time**: 30 minutes
**Materials**: `templates/tasks/`, Pebble template engine

> **🔄 Refactoring Note: From Monolithic to Partials**
>
> In **Week 6**, your `tasks/index.peb` rendered each task as a simple `<li>` with title + delete button:
>
> ```html
> {% for task in tasks %}
>   <li id="task-{{ task.id }}">
>     <span>{{ task.title }}</span>
>     <form action="/tasks/{{ task.id }}/delete" method="post">
>       <button type="submit">Delete</button>
>     </form>
>   </li>
> {% endfor %}
> ```
>
> In **Week 7**, we introduce **inline editing**, which requires two states:
> - **View mode**: Display title with Edit/Delete buttons
> - **Edit mode**: Show input field with Save/Cancel buttons
>
> **Refactoring strategy**:
> 1. Extract `<li>` markup into **two partial templates**:
>    - `partials/view.peb` - Read-only display
>    - `partials/edit.peb` - Editable input
> 2. Update `tasks/index.peb` to `{% include %}` the appropriate partial
> 3. HTMX will swap partials in-place (view ↔ edit) without full page reload
>
> **Before** (Week 6):
> ```
> tasks/index.peb (full page)
>   └── <li> hardcoded for each task
> ```
>
> **After** (Week 7):
> ```
> tasks/index.peb (full page)
>   └── {% include "partials/view.peb" %}  ← Reusable partial
>   └── {% include "partials/edit.peb" %}  ← Reusable partial
> ```
>
> This **separation of concerns** makes it easier to:
> - Swap between states (HTMX swaps just the `<li>`, not the whole page)
> - Test each mode independently
> - Add more modes later (e.g., "completed" view with strikethrough)

### Step 1: Create Partials Directory

```bash
mkdir -p src/main/re../../references/templates/tasks/partials
```

We'll create **two modes**: view (display title) and edit (input field).

### Step 2: Create View Mode Partial

Create `templates/tasks/partials/view.peb`:

```html
<li id="task-{{ task.id }}" class="task-view">
  <span class="task-title">{{ task.title }}</span>

  <form action="/tasks/{{ task.id }}/edit" method="get" style="display: inline;"
        hx-get="/tasks/{{ task.id }}/edit"
        hx-target="#task-{{ task.id }}"
        hx-swap="outerHTML">
    <button type="submit" aria-label="Edit task: {{ task.title }}">Edit</button>
  </form>

  <form action="/tasks/{{ task.id }}/delete" method="post" style="display: inline;"
        hx-post="/tasks/{{ task.id }}/delete"
        hx-target="#task-{{ task.id }}"
        hx-swap="outerHTML">
    <button type="submit" aria-label="Delete task: {{ task.title }}">Delete</button>
  </form>
</li>
```

**Accessibility features**:
- Unique `id="task-{{ task.id }}"` for HTMX targeting
- `aria-label` on buttons provides context ("Edit task: Buy milk")
- Semantic `<li>` structure (screen readers announce "List item 1 of 5")

### Step 3: Create Edit Mode Partial

Create `templates/tasks/partials/edit.peb`:

```html
<li id="task-{{ task.id }}" class="task-edit">
  <form action="/tasks/{{ task.id }}/edit" method="post"
        hx-post="/tasks/{{ task.id }}/edit"
        hx-target="#task-{{ task.id }}"
        hx-swap="outerHTML">

    <label for="title-{{ task.id }}">Title</label>
    <input type="text"
           id="title-{{ task.id }}"
           name="title"
           value="{{ task.title }}"
           required
           autofocus
           aria-describedby="hint-{{ task.id }}{% if error %} error-{{ task.id }}{% endif %}">

    <small id="hint-{{ task.id }}">Keep it short and specific.</small>

    {% if error %}
      <p id="error-{{ task.id }}" class="error" role="alert" aria-live="assertive">
        {{ error }}
      </p>
    {% else %}
      <p id="error-{{ task.id }}" class="visually-hidden" aria-live="assertive"></p>
    {% endif %}

    <button type="submit">Save</button>
    <a href="/tasks"
       hx-get="/tasks/{{ task.id }}/view"
       hx-target="#task-{{ task.id }}"
       hx-swap="outerHTML"
       role="button">Cancel</a>
  </form>
</li>
```

**Accessibility features**:
- `autofocus`: Input receives focus when form appears (keyboard users can start typing immediately)
- `aria-describedby`: Links input to hint and error (screen reader announces both)
- `role="alert" aria-live="assertive"`: Error interrupts screen reader to announce immediately
- Error element always exists (even when hidden) so `aria-describedby` always points to valid ID
- Cancel link has `role="button"` for semantic consistency

### Step 4: Update Main Task List Template

Edit `templates/tasks/index.peb`:

```html
{% extends "base.peb" %}

{% block content %}
<h1>Tasks</h1>

<section aria-labelledby="add-heading">
  <h2 id="add-heading">Add a new task</h2>
  <form action="/tasks" method="post"
        hx-post="/tasks"
        hx-target="#task-list"
        hx-swap="beforeend">
    <label for="title">Title</label>
    <input type="text" id="title" name="title" required
           placeholder="e.g., Buy milk" aria-describedby="title-hint">
    <small id="title-hint">Keep it short and specific.</small>
    <button type="submit">Add Task</button>
  </form>
</section>

<section aria-labelledby="list-heading">
  <h2 id="list-heading">Current tasks ({{ tasks | length }})</h2>
  <ul id="task-list">
    {% for task in tasks %}
      {% if editingId and editingId == task.id %}
        {% include "tasks/partials/edit.peb" with {"task": task, "error": errorMessage} %}
      {% else %}
        {% include "tasks/partials/view.peb" with {"task": task} %}
      {% endif %}
    {% empty %}
      <li>No tasks yet. Add one above!</li>
    {% endfor %}
  </ul>
</section>
{% endblock %}
```

**Logic**:
- If `editingId` matches current task, render edit mode
- Otherwise, render view mode
- This enables no-JS path (full-page render with one task in edit mode)

**Stop and check**:
- ✅ `view.peb` created with Edit/Delete buttons
- ✅ `edit.peb` created with input + Save/Cancel
- ✅ `index.peb` uses conditional includes

---

## Activity 3: Implement Routes (Dual-Path)

**Time**: 35 minutes
**Materials**: `src/main/kotlin/routes/Tasks.kt`

### Step 1: Add Helper to Detect HTMX

Already exists from Week 6, but verify:

```kotlin
fun ApplicationCall.isHtmx(): Boolean =
    request.headers["HX-Request"]?.equals("true", ignoreCase = true) == true
```

### Step 2: Add GET /tasks/{id}/edit Route

```kotlin
get("/tasks/{id}/edit") {
    val id = call.parameters["id"]?.toIntOrNull() ?: return@get call.respond(HttpStatusCode.NotFound)
    val task = TaskRepository.find(id) ?: return@get call.respond(HttpStatusCode.NotFound)

    if (call.isHtmx()) {
        // HTMX path: return edit fragment
        val template = pebble.getTemplate("templates/tasks/partials/edit.peb")
        val model = mapOf("task" to task, "error" to null)
        val writer = StringWriter()
        template.evaluate(writer, model)
        call.respondText(writer.toString(), ContentType.Text.Html)
    } else {
        // No-JS path: full-page render with editingId
        val model = mapOf(
            "title" to "Tasks",
            "tasks" to TaskRepository.all(),
            "editingId" to id,
            "errorMessage" to null
        )
        val template = pebble.getTemplate("templates/tasks/index.peb")
        val writer = StringWriter()
        template.evaluate(writer, model)
        call.respondText(writer.toString(), ContentType.Text.Html)
    }
}
```

**Logic**:
- HTMX: Returns `<li>` edit fragment (swaps in place)
- No-JS: Returns full page with `editingId=1` (conditional rendering in template)

### Step 3: Add POST /tasks/{id}/edit Route

```kotlin
post("/tasks/{id}/edit") {
    val id = call.parameters["id"]?.toIntOrNull() ?: return@post call.respond(HttpStatusCode.NotFound)
    val task = TaskRepository.find(id) ?: return@post call.respond(HttpStatusCode.NotFound)

    val newTitle = call.receiveParameters()["title"].orEmpty().trim()

    // Validation
    if (newTitle.isBlank()) {
        if (call.isHtmx()) {
            // HTMX path: return edit fragment with error
            val template = pebble.getTemplate("templates/tasks/partials/edit.peb")
            val model = mapOf(
                "task" to task,
                "error" to "Title is required. Please enter at least one character."
            )
            val writer = StringWriter()
            template.evaluate(writer, model)
            return@post call.respondText(writer.toString(), ContentType.Text.Html, HttpStatusCode.BadRequest)
        } else {
            // No-JS path: redirect with error flag
            return@post call.respondRedirect("/tasks/${id}/edit?error=blank")
        }
    }

    // Update task
    task.title = newTitle
    TaskRepository.update(task)

    if (call.isHtmx()) {
        // HTMX path: return view fragment + OOB status
        val viewTemplate = pebble.getTemplate("templates/tasks/partials/view.peb")
        val viewWriter = StringWriter()
        viewTemplate.evaluate(viewWriter, mapOf("task" to task))

        val status = """<div id="status" hx-swap-oob="true">Task "${task.title}" updated successfully.</div>"""

        return@post call.respondText(viewWriter.toString() + status, ContentType.Text.Html)
    }

    // No-JS path: PRG redirect
    call.respondRedirect("/tasks")
}
```

**Validation paths**:
- **HTMX + error**: Returns edit fragment with error message, status 400
- **No-JS + error**: Redirects to `/tasks/{id}/edit?error=blank`
- **HTMX + success**: Returns view fragment + OOB status
- **No-JS + success**: PRG redirect to `/tasks`

### Step 4: Handle Error Query Parameter (No-JS)

Update GET `/tasks/{id}/edit` to handle `?error=blank`:

```kotlin
get("/tasks/{id}/edit") {
    val id = call.parameters["id"]?.toIntOrNull() ?: return@get call.respond(HttpStatusCode.NotFound)
    val task = TaskRepository.find(id) ?: return@get call.respond(HttpStatusCode.NotFound)
    val errorParam = call.request.queryParameters["error"]

    val errorMessage = when (errorParam) {
        "blank" -> "Title is required. Please enter at least one character."
        else -> null
    }

    if (call.isHtmx()) {
        val template = pebble.getTemplate("templates/tasks/partials/edit.peb")
        val model = mapOf("task" to task, "error" to errorMessage)
        val writer = StringWriter()
        template.evaluate(writer, model)
        call.respondText(writer.toString(), ContentType.Text.Html)
    } else {
        val model = mapOf(
            "title" to "Tasks",
            "tasks" to TaskRepository.all(),
            "editingId" to id,
            "errorMessage" to errorMessage
        )
        val template = pebble.getTemplate("templates/tasks/index.peb")
        val writer = StringWriter()
        template.evaluate(writer, model)
        call.respondText(writer.toString(), ContentType.Text.Html)
    }
}
```

### Step 5: Add GET /tasks/{id}/view (Cancel Handler)

```kotlin
get("/tasks/{id}/view") {
    val id = call.parameters["id"]?.toIntOrNull() ?: return@get call.respond(HttpStatusCode.NotFound)
    val task = TaskRepository.find(id) ?: return@get call.respond(HttpStatusCode.NotFound)

    // HTMX path only (cancel is just a link to /tasks in no-JS)
    val template = pebble.getTemplate("templates/tasks/partials/view.peb")
    val model = mapOf("task" to task)
    val writer = StringWriter()
    template.evaluate(writer, model)
    call.respondText(writer.toString(), ContentType.Text.Html)
}
```

### Step 6: Add TaskRepository Methods

If not already present:

```kotlin
object TaskRepository {
    // ... existing methods ...

    fun find(id: Int): Task? = tasks.find { it.id == id }

    fun update(task: Task) {
        tasks.find { it.id == task.id }?.let { it.title = task.title }
        persist()
    }
}
```

**Stop and check**:
- ✅ GET `/tasks/{id}/edit` returns edit fragment (HTMX) or full page (no-JS)
- ✅ POST `/tasks/{id}/edit` validates, returns fragment/redirect
- ✅ GET `/tasks/{id}/view` returns view fragment (cancel handler)
- ✅ Error messages passed to templates

---

## Activity 4: Test Dual-Path Functionality

**Time**: 25 minutes
**Materials**: Browser, keyboard, screen reader

### Test 1: HTMX Path (JavaScript Enabled)

1. **Load http://localhost:8080/tasks**
2. **Add a task**: "Test inline edit"
3. **Click "Edit"**:
   - **Expected**: Form appears in place (no page reload)
   - **Check Network tab**: See GET `/tasks/1/edit` (AJAX)
4. **Type new title**: "Updated title"
5. **Click "Save"**:
   - **Expected**: View mode appears (no page reload)
   - **Check DevTools**: `#status` text = "Task 'Updated title' updated successfully."
6. **Click "Cancel"** (after editing again):
   - **Expected**: Returns to view mode, original title preserved

**Result**: ✅ Inline edit works with HTMX

### Test 2: Validation (HTMX)

1. **Click "Edit"** on a task
2. **Delete all text**, click "Save"
3. **Expected**: Error message appears: "Title is required..."
4. **Check error element**:
   - Inspect `<p id="error-1" role="alert">`
   - Confirm `aria-live="assertive"`
5. **Type valid title**, click "Save"
6. **Expected**: Error disappears, save succeeds

**Result**: ✅ Validation works (HTMX)

### Test 3: No-JS Path (JavaScript Disabled)

1. **Disable JavaScript** (DevTools → Settings → Disable JavaScript)
2. **Reload page**
3. **Click "Edit"**:
   - **Expected**: Full page reload, edit form shown
4. **Type new title**: "No-JS test"
5. **Click "Save"**:
   - **Expected**: Page reloads, task updated
6. **Click "Edit"**, delete title, click "Save"**:
   - **Expected**: Page reloads, error message shown

**Result**: ✅ No-JS path works

### Test 4: Keyboard Navigation

**Re-enable JavaScript**, reload page:

1. **Tab to "Edit" button**
2. **Press Enter**:
   - **Expected**: Form appears, focus in input field (autofocus)
3. **Type new title**
4. **Tab to "Save" button**, press Enter:
   - **Expected**: Saves, returns to view mode
5. **Tab to "Edit"**, Enter, Tab to "Cancel"**, Enter:
   - **Expected**: Returns to view mode without saving

**Check focus indicators**:
- ✅ All buttons have visible outline on focus
- ✅ Input has visible outline
- ✅ Focus order logical (Edit → Title input → Save → Cancel)

**Result**: ✅ Keyboard accessible

### Test 5: Screen Reader (NVDA/VoiceOver)

**Windows (NVDA)**:
1. Start NVDA (Ctrl+Alt+N)
2. Navigate to task list
3. **Tab to "Edit" button**:
   - **Listen for**: "Edit task: Test inline edit, button"
4. **Press Enter** (activates Edit)
5. **Listen for**: "Title, edit, Keep it short and specific, [current value]"
6. **Delete text, Tab to Save, Enter**
7. **Listen for**: "Title is required. Please enter at least one character, alert"
8. **Type valid title, Save**
9. **Listen for**: "Task 'Valid title' updated successfully"

**macOS (VoiceOver)**:
1. Enable VoiceOver (Cmd+F5)
2. Navigate with VoiceOver cursor (VO+Right Arrow)
3. Repeat NVDA steps, listen for equivalent announcements

**Result**: ✅ Screen reader announces labels, errors, status

**Stop and check**:
- ✅ HTMX path: instant swaps
- ✅ No-JS path: page reloads
- ✅ Validation works both paths
- ✅ Keyboard accessible
- ✅ Screen reader announces correctly

---

## Activity 5: Evidence Collection

**Time**: 15 minutes
**Materials**: Screenshots, testing notes

### Step 1: Capture Evidence

Create `wk07/evidence/` directory:

```bash
mkdir -p wk07/evidence
```

**Screenshots to capture**:
1. **View mode** (HTMX): Before clicking Edit
2. **Edit mode** (HTMX): Form in place
3. **Validation error** (HTMX): Error message with `role="alert"`
4. **Status message** (HTMX): DevTools showing `#status` content
5. **No-JS edit mode**: Full page with form
6. **NVDA speech viewer**: Showing error announcement

**Example naming**:
- `01-view-mode-htmx.png`
- `02-edit-mode-htmx.png`
- `03-validation-error-htmx.png`
- `04-status-oob-devtools.png`
- `05-edit-mode-nojs.png`
- `06-nvda-error-announcement.png`

### Step 2: Document Testing Notes

Create `wk07/evidence/testing-notes.md`:

```markdown
# Testing Notes — Week 7 Lab 1

## HTMX Path
**Date**: [YYYY-MM-DD]
**Browser**: Chrome 120.0
**JavaScript**: Enabled

### Test: Inline edit activation
- **Action**: Clicked "Edit" button
- **Result**: ✅ Form appeared instantly (no page reload)
- **Network**: GET /tasks/1/edit (AJAX, 45ms)
- **Screenshot**: `01-view-mode-htmx.png`, `02-edit-mode-htmx.png`

### Test: Validation error
- **Action**: Deleted title, clicked "Save"
- **Result**: ✅ Error shown: "Title is required..."
- **ARIA**: `<p id="error-1" role="alert" aria-live="assertive">` confirmed in DevTools
- **Screenshot**: `03-validation-error-htmx.png`

### Test: Successful save
- **Action**: Entered valid title, clicked "Save"
- **Result**: ✅ View mode restored, status = "Task 'New title' updated successfully."
- **Screenshot**: `04-status-oob-devtools.png`

---

## No-JS Path
**Date**: [YYYY-MM-DD]
**Browser**: Chrome 120.0
**JavaScript**: Disabled

### Test: Edit activation
- **Action**: Clicked "Edit" button
- **Result**: ✅ Full page reload, form shown
- **URL**: http://localhost:8080/tasks/1/edit

### Test: Validation error
- **Action**: Deleted title, clicked "Save"
- **Result**: ✅ Redirect to /tasks/1/edit?error=blank, error shown
- **Screenshot**: `05-edit-mode-nojs-error.png`

---

## Keyboard Testing
**Date**: [YYYY-MM-DD]
**Input**: Keyboard only (no mouse)

### Test: Tab navigation
- **Path**: Tab → "Edit" → Enter → Title input (autofocus) → Tab → "Save" → Enter
- **Result**: ✅ Focus order logical, all buttons reachable
- **Focus indicators**: ✅ Visible outline on all elements

---

## Screen Reader Testing
**Date**: [YYYY-MM-DD]
**Tool**: NVDA 2024.1 (Windows 11)

### Test: Edit button announcement
- **Navigated to**: "Edit" button
- **NVDA said**: "Edit task: Buy milk, button"
- **Result**: ✅ Contextual label announced

### Test: Input field announcement
- **Activated**: Edit button (Enter)
- **NVDA said**: "Title, edit, Keep it short and specific, Buy milk"
- **Result**: ✅ Label + hint + value announced

### Test: Error announcement
- **Action**: Deleted title, pressed Save
- **NVDA said**: "Title is required. Please enter at least one character, alert"
- **Result**: ✅ Error announced immediately (assertive)

### Test: Status message announcement
- **Action**: Saved valid title
- **NVDA said**: "Task 'New title' updated successfully"
- **Result**: ✅ Status announced (polite, didn't interrupt)

---

## Issues Found
None. All tests passed.

## WCAG Compliance Check
| Criterion | Status | Evidence |
|-----------|--------|----------|
| 2.1.1 Keyboard (A) | ✅ Pass | All features accessible via Tab/Enter |
| 3.3.1 Error Identification (A) | ✅ Pass | Error message explicit, aria-describedby links input |
| 4.1.3 Status Messages (AA) | ✅ Pass | Live region announces success without focus change |
```

**Stop and check**:
- ✅ Screenshots saved in `wk07/evidence/`
- ✅ Testing notes documented
- ✅ WCAG criteria mapped to evidence

---

## Reflection Questions

1. **Dual-path trade-offs**: The HTMX path returns HTML fragments; the no-JS path returns full pages. Which is easier to maintain? Which is easier to test?

2. **Focus management**: HTMX tries to restore focus after swaps. Did you notice focus moving unexpectedly? How would you improve this?

3. **Error persistence**: In the no-JS path, errors survive redirects via query parameters. Could you use session storage instead? What are the trade-offs?

4. **Screen reader experience**: Compare the HTMX path (instant swap) vs. no-JS path (page reload). Which is better for screen reader users? Why?

---

## Further Reading

**Inline edit patterns**
- HTMX Examples: Click to Edit. <https://htmx.org/examples/click-to-edit/>
- Nielsen Norman Group (2015). "In-Place Editing". <https://www.nngroup.com/articles/in-place-editing/>

**WCAG Error Identification**
- W3C (2024). Understanding 3.3.1 Error Identification. <https://www.w3.org/WAI/WCAG22/Understanding/error-identification.html>
- GOV.UK Design System: Error Message Component. <https://design-system.service.gov.uk/components/error-message/>

**Focus management**
- Deque (2023). "Focus Management in ARIA". <https://www.deque.com/blog/give-focus-power/>
- WebAIM: Keyboard Accessibility. <https://webaim.org/techniques/keyboard/>

---

## Glossary Summary

| Term | Definition | Example/Context |
|------|------------|-----------------|
| **Inline edit** | Editing content in place (not separate page) | Click task title → form appears → save → title updates |
| **Dual-path** | Same feature, different implementation (HTMX vs. no-JS) | HTMX = fragment swap; no-JS = full page reload |
| **aria-describedby** | Links input to descriptive text (hint/error) | `<input aria-describedby="hint-1 error-1">` |
| **role="alert"** | Announces content immediately to screen readers | `<p role="alert">Error message</p>` |
| **OOB swap** | HTMX updating element outside main target | `<div id="status" hx-swap-oob="true">` |
| **Autofocus** | Input receives focus when rendered | `<input autofocus>` (use sparingly, can disorient SR users) |
| **Focus order** | Sequence of Tab navigation (WCAG 2.4.3) | Edit button → Title input → Save → Cancel |
| **WCAG 3.3.1** | Error Identification (Level A) | Error must be identified + described + programmatically associated |
| **WCAG 4.1.3** | Status Messages (Level AA) | Status announced without focus change (live regions) |

---

## Lab Checklist

Before leaving lab, confirm:

- [ ] **View/edit partials created**: `templates/tasks/partials/view.peb`, `edit.peb`
- [ ] **Routes implemented**: GET/POST `/tasks/{id}/edit`, GET `/tasks/{id}/view`
- [ ] **HTMX path works**: Inline edit, validation, save (no page reload)
- [ ] **No-JS path works**: Edit, validation, save (page reloads)
- [ ] **Keyboard accessible**: Tab through all controls, submit with Enter
- [ ] **Screen reader tested**: NVDA/VoiceOver announces labels, hints, errors, status
- [ ] **Evidence collected**: Screenshots + testing notes in `wk07/evidence/`
- [ ] **Code committed**: `git add .`, `git commit -m "wk7-lab1: inline edit with dual-path + ARIA"`

---

## Next Steps

In **Week 7 Lab 2** you will:
1. Run structured accessibility audit (WCAG checklist, heuristics)
2. Log findings in inclusive backlog
3. Implement **one priority fix** from backlog (error summary, focus indicator, etc.)
4. Package evidence for Task 1 (Gradescope submission)

**Preparation**:
- Review backlog from Week 6 (`wk06/backlog/backlog.csv`)
- Install axe DevTools browser extension
- Bring inline edit code from Lab 1 (working state)

---

**Lab authored by**: COMP2850 Teaching Team, University of Leeds
**Last updated**: 2025-01-14
**Licence**: Academic use only (not for redistribution)
