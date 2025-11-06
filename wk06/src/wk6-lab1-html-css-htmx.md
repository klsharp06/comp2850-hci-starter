# Week 6 • Lab 1: Server-First Foundations with HTMX

![COMP2850](https://img.shields.io/badge/COMP2850-HCI-blue)
![Week 6](https://img.shields.io/badge/Week-6-orange)
![Lab 1](https://img.shields.io/badge/Lab-1-green)
![Status](https://img.shields.io/badge/Status-Draft-yellow)

---

## Terminology Note

Throughout COMP2850 we use **people-centred language** (e.g., "person using a screen reader") rather than deficit-based terms (e.g., "blind user"). This reflects contemporary inclusive-design practice and acknowledges that disability arises from environmental barriers, not individual impairment.

---

## Pre-reading

**Essential**
- [Gross, Haddad & Weibel (2023). *Hypermedia Systems*, Ch. 1-3](https://hypermedia.systems/)
- [HTMX Documentation: Core Concepts](https://htmx.org/docs/)
- [GOV.UK Service Manual: Progressive Enhancement](https://www.gov.uk/service-manual/technology/using-progressive-enhancement)

**Recommended**
- [W3C (2024). WCAG 2.2 Quick Reference](https://www.w3.org/WAI/WCAG22/quickref/)
- [MDN: Semantic HTML](https://developer.mozilla.org/en-US/docs/Glossary/Semantics#semantic_elements)
- `../../references/privacy-by-design.md` (module-specific ethics guidance)

---

## Introduction

### Context

This lab marks the beginning of the **HCI component** of COMP2850 (Weeks 6-11). For the first five weeks you built foundational Kotlin/OOP skills. Now you'll apply those skills to a real-world HCI challenge: building inclusive, accessible web applications using **server-first architecture** with **progressive enhancement**.

Modern web development often defaults to client-heavy JavaScript frameworks (React, Vue, Angular). While powerful, these approaches can create accessibility barriers:
- Screen readers struggle with dynamically-rendered content
- Keyboard navigation breaks when focus management is incorrect
- Customers with JavaScript disabled (corporate firewalls, data-saving modes) lose functionality
- Development complexity increases, making accessibility fixes expensive to retrofit

**Server-first architecture** inverts this model: the server renders complete, semantic HTML that works *without* JavaScript. We then add HTMX as a **progressive enhancement layer** to improve interactivity without sacrificing the baseline experience.

### Why This Matters

**Professionally**, server-first patterns are gaining traction in industry:
- **GOV.UK** (UK government digital services) mandates progressive enhancement
- **Basecamp** (project management, 3M+ users) uses server-rendered HTML + Turbo (similar to HTMX)
- **GitHub** serves primarily server-rendered HTML with JavaScript enhancements
- **Stack Overflow** is 90% server-rendered for performance and accessibility

**Academically**, this lab introduces core HCI concepts:
- **Inclusion by design**: building accessibility into the foundation, not retrofitting it
- **Progressive enhancement**: layering capabilities so everyone gets a baseline experience
- **Server-side rendering (SSR)**: generating HTML on the server vs. client-side rendering (CSR)
- **Hypermedia-driven architecture**: using HTML as the engine of application state (HATEOAS)

### Learning Outcomes

By the end of this lab, you will be able to:

| Learning Outcome | Module LO | ACM/WCAG Ref |
|---|---|---|
| Implement server-first routes using Ktor and Pebble templates | LO5: Apply software design patterns | ACM: SEP/2 |
| Enhance forms with HTMX without breaking no-JS functionality | LO6: Integrate web technologies | ACM: SEP/3 |
| Create accessible HTML with semantic structure, ARIA live regions, skip links | LO9: Apply accessibility standards | WCAG 2.2 AA |
| Verify keyboard navigation, screen reader announcements, no-JS parity | LO10: Evaluate inclusive design | ACM: HC/4 |

---

## Key Concepts

### 1. Server-First Architecture

**Server-first** (also called "server-side rendering" or SSR) means the server generates complete HTML pages and sends them to the browser. The browser displays them immediately without waiting for JavaScript to run.

**Example**:
```
User requests /tasks
→ Server queries database
→ Server renders tasks/index.peb template with data
→ Server sends complete HTML to browser
→ Browser displays page (works even if JS is disabled)
```

**Contrast with client-side rendering (CSR)**:
```
User requests /tasks
→ Server sends empty HTML + JavaScript bundle
→ Browser downloads and executes JS
→ JS makes API call to /api/tasks
→ JS renders HTML and inserts into DOM
❌ If JS fails to load, page is blank
```

**Benefits**:
- ✅ Faster initial page load (no JS parsing delay)
- ✅ Works without JavaScript (resilience)
- ✅ Search engines can index content (SEO)
- ✅ Screen readers receive semantic HTML immediately

### 2. Progressive Enhancement

**Progressive enhancement** is a design philosophy: start with a baseline experience that works for everyone, then add enhancements for browsers that support them.

**Layers**:
1. **HTML** (content layer): semantic markup, accessible by default
2. **CSS** (presentation layer): styling, gracefully degrades if not supported
3. **JavaScript** (behavior layer): dynamic interactions, optional

**Example (COMP2850 task manager)**:
- **Baseline**: Form POSTs to `/tasks`, server validates, redirects (PRG pattern)
- **Enhancement**: HTMX intercepts POST, swaps HTML fragment, no page reload

**Key principle**: JavaScript failure (network error, blocked script, unsupported browser) should not break core functionality.

### 3. HTMX Fundamentals

**HTMX** extends HTML with attributes that trigger AJAX requests and update the DOM. Instead of writing JavaScript, you declare behavior in HTML.

**Core attributes**:
| Attribute | Purpose | Example |
|-----------|---------|---------|
| `hx-get` | HTTP GET request | `<button hx-get="/tasks/1">Load</button>` |
| `hx-post` | HTTP POST request | `<form hx-post="/tasks">` |
| `hx-target` | Where to insert response | `hx-target="#task-list"` |
| `hx-swap` | How to insert response | `hx-swap="beforeend"` (append), `hx-swap="outerHTML"` (replace) |
| `hx-swap-oob` | Out-of-band swap (update element not in target) | `<div id="status" hx-swap-oob="true">` |

**How HTMX works**:
1. User triggers event (click, submit)
2. HTMX makes AJAX request to server
3. Server returns HTML fragment (not JSON)
4. HTMX swaps fragment into target element
5. Screen reader live regions announce changes

**Why this helps accessibility**:
- Server controls HTML structure (consistent semantics)
- No manual DOM manipulation (reduces ARIA errors)
- Live regions work automatically (if server includes them)

### 4. Post-Redirect-Get (PRG) Pattern

**Problem**: If a user submits a form with POST and then refreshes the page, the browser re-submits the form (duplicate submission).

**Solution**: After processing a POST, return a redirect (HTTP 303) to a GET URL.

**Flow**:
```
1. User submits form → POST /tasks (title="Buy milk")
2. Server validates, saves to database
3. Server returns: HTTP 303 See Other, Location: /tasks
4. Browser follows redirect → GET /tasks
5. Server renders updated task list
6. User sees new task; refresh = safe GET (no duplicate)
```

**In Ktor**:
```kotlin
post("/tasks") {
    val title = call.receiveParameters()["title"].orEmpty().trim()
    if (title.isNotBlank()) {
        repo.add(title)
    }
    call.respondRedirect("/tasks") // PRG redirect
}
```

### 5. ARIA Live Regions

**ARIA** (Accessible Rich Internet Applications) provides attributes that help screen readers understand dynamic content.

**Live regions** announce changes without moving focus. Essential for AJAX-updated content.

**Key attributes**:
| Attribute | Purpose | Example |
|-----------|---------|---------|
| `role="status"` | Announces non-critical updates | `<div role="status" aria-live="polite">Task added</div>` |
| `role="alert"` | Announces critical errors | `<div role="alert">Title is required</div>` |
| `aria-live="polite"` | Waits for user to finish before announcing | Status messages |
| `aria-live="assertive"` | Interrupts to announce immediately | Error messages |

**Example (COMP2850 pattern)**:
```html
<!-- In base.peb -->
<div id="status" role="status" aria-live="polite" class="visually-hidden"></div>

<!-- Server response (HTMX OOB swap) -->
<div id="status" hx-swap-oob="true">Task "Buy milk" added</div>
```

**How it works**:
1. HTMX request completes
2. Server returns fragment + status div with `hx-swap-oob="true"`
3. HTMX swaps main content *and* status div (out-of-band)
4. Screen reader detects status div change, announces "Task 'Buy milk' added"

---

## Activity 1: Project Setup & Scaffold Inspection

**Time**: 15 minutes
**Materials**: Starter pack (Gradle project with Ktor + Pebble)

### Step 1: Clone Starter Pack

```bash
# Clone starter repository (URL provided on Minerva)
git clone [REPO_URL]/comp2850-hci-starter.git
cd comp2850-hci-starter

# Or open in Codespaces: Click "Code" → "Create codespace on main"
```

### Step 2: Verify Dependencies

Open `build.gradle.kts` and confirm these dependencies:

```kotlin
dependencies {
    implementation("io.ktor:ktor-server-core:2.3.12")
    implementation("io.ktor:ktor-server-netty:2.3.12")
    implementation("io.pebbletemplates:pebble:3.2.2")
    implementation("ch.qos.logback:logback-classic:1.5.6")
}
```

**What these do**:
- **Ktor**: Kotlin web framework (routes, HTTP handling)
- **Pebble**: Template engine (renders HTML with data)
- **Logback**: Logging (debug server behavior)

### Step 3: Run the Server

```bash
./gradlew run

# Windows
gradlew.bat run
```

**Expected output**:
```
Application started in 0.5s
Listening on http://0.0.0.0:8080
```

**Visit**: http://localhost:8080/tasks

You should see placeholder text: "Task manager coming soon..." This confirms the server works.

### Step 4: Inspect Directory Structure

```
comp2850-hci-starter/
├── src/main/kotlin/
│   ├── Main.kt               # Server entry point
│   ├── model/
│   │   └── Task.kt           # Data model
│   ├── routes/
│   │   └── TaskRoutes.kt     # CRUD operations
│   ├── storage/
│   │   └── TaskStore.kt      # CSV persistence
│   └── utils/
│       └── SessionUtils.kt   # Anonymous sessions
├── src/main/re../../references/
│   ├── templates/
│   │   ├── _layout/
│   │   │   └── base.peb      # Accessible base layout
│   │   └── tasks/
│   │       ├── index.peb     # Full page view
│   │       ├── _list.peb     # Task list partial
│   │       └── _item.peb     # Single task partial
│   └── static/
│       ├── css/custom.css    # WCAG styles
│       └── js/htmx-1.9.12.min.js
├── data/
│   └── tasks.csv             # File-based storage
├── build.gradle.kts          # Dependencies
└── README.md                 # Comprehensive guide
```

**Stop and check**:
- ✅ Server runs without errors
- ✅ http://localhost:8080/tasks loads (even if placeholder)
- ✅ You can see `base.peb` and `tasks/index.peb` in your IDE

---

## Activity 2: Build Accessible Base Layout

**Time**: 30 minutes
**Materials**: `src/main/re../../references/templates/base.peb`

### Step 1: Create Semantic HTML Structure

Replace the contents of `base.peb` with:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{ title | default("COMP2850 Task Manager") }}</title>
  <link rel="stylesheet" href="https://unpkg.com/@picocss/pico@2/css/pico.min.css">
  <style>
    /* Visually hidden but accessible to screen readers */
    .visually-hidden {
      position: absolute !important;
      clip: rect(1px, 1px, 1px, 1px);
      padding: 0 !important;
      border: 0 !important;
      height: 1px !important;
      width: 1px !important;
      overflow: hidden;
      white-space: nowrap;
    }

    /* Skip link (keyboard only) */
    .skip-link {
      position: absolute;
      left: -10000px;
      width: 1px;
      height: 1px;
      overflow: hidden;
    }
    .skip-link:focus {
      position: static;
      width: auto;
      height: auto;
      background: #1976d2;
      color: white;
      padding: 0.5rem 1rem;
      text-decoration: none;
      font-weight: bold;
    }

    /* ARIA live region styling */
    #status:not(:empty) {
      background: #e3f2fd;
      border-left: 4px solid #1976d2;
      padding: 1rem;
      margin: 1rem 0;
    }
  </style>
  <script src="https://unpkg.com/htmx.org@2.0.0"></script>
</head>
<body>
  <a href="#main" class="skip-link">Skip to main content</a>

  <main class="container" id="main">
    <div id="status" role="status" aria-live="polite" class="visually-hidden"></div>
    {% block content %}{% endblock %}
  </main>

  <footer class="container">
    <p><small>COMP2850 HCI • University of Leeds • 2024/25</small></p>
  </footer>
</body>
</html>
```

### Step 2: Understanding the Accessibility Features

**Skip link** (line 35-36):
- Allows keyboard/SR users to jump past repeated navigation
- Hidden until focused (keyboard Tab reveals it)
- **WCAG 2.4.1 (Bypass Blocks, A)**: Mechanism to bypass repeated content

**Live region** (line 39):
- `role="status"`: Announces updates without stealing focus
- `aria-live="polite"`: Waits for user to pause before announcing
- `.visually-hidden`: Hidden visually but announced by SR
- **WCAG 4.1.3 (Status Messages, AA)**: Status messages programmatically announced

**Semantic HTML**:
- `<main>`: Primary content landmark
- `<footer>`: Site information landmark
- `lang="en"`: Tells SR which language to use
- **WCAG 1.3.1 (Info and Relationships, A)**: Structure conveyed programmatically

### Step 3: Test the Base Layout

Reload http://localhost:8080/tasks. You should still see placeholder text, but now:

1. **Tab once**: Skip link appears with blue background
2. **Press Enter on skip link**: Focus jumps to `#main` (no effect yet, but will matter when we add navigation)
3. **Inspect with DevTools**:
   - Open Elements panel
   - Find `<div id="status">` with `role="status"` and `aria-live="polite"`
   - Confirm `.visually-hidden` class applied

**Stop and check**:
- ✅ Skip link appears on keyboard focus
- ✅ Live region exists in DOM (empty for now)
- ✅ Pico.css loaded (page has default styling)

---

## Activity 3: Implement Server-Side Routes & Repository

**Time**: 35 minutes
**Materials**: `src/main/kotlin/routes/Tasks.kt`, `data/tasks.csv`

### Step 1: Create a Simple Repository

Create `src/main/kotlin/data/TaskRepository.kt`:

```kotlin
package data

import java.io.File
import java.util.concurrent.atomic.AtomicInteger

data class Task(val id: Int, var title: String)

object TaskRepository {
    private val file = File("data/tasks.csv")
    private val tasks = mutableListOf<Task>()
    private val idCounter = AtomicInteger(1)

    init {
        file.parentFile?.mkdirs()
        if (!file.exists()) {
            file.writeText("id,title\n")
        } else {
            file.readLines().drop(1).forEach { line ->
                val parts = line.split(",", limit = 2)
                if (parts.size == 2) {
                    val id = parts[0].toIntOrNull() ?: return@forEach
                    tasks.add(Task(id, parts[1]))
                    idCounter.set(maxOf(idCounter.get(), id + 1))
                }
            }
        }
    }

    fun all(): List<Task> = tasks.toList()

    fun add(title: String): Task {
        val task = Task(idCounter.getAndIncrement(), title)
        tasks.add(task)
        persist()
        return task
    }

    fun delete(id: Int): Boolean {
        val removed = tasks.removeIf { it.id == id }
        if (removed) persist()
        return removed
    }

    private fun persist() {
        file.writeText("id,title\n" + tasks.joinToString("\n") { "${it.id},${it.title}" })
    }
}
```

**Why CSV?**
- Simple, inspectable, no database setup required
- Good for learning (focus on HCI, not DB configuration)
- Production apps would use PostgreSQL/MongoDB

### Step 2: Create Baseline Routes (No HTMX Yet)

Edit `src/main/kotlin/routes/Tasks.kt`:

```kotlin
package routes

import data.TaskRepository
import io.ktor.http.*
import io.ktor.server.application.*
import io.ktor.server.request.*
import io.ktor.server.response.*
import io.ktor.server.routing.*
import io.pebbletemplates.pebble.PebbleEngine
import java.io.StringWriter

fun Route.taskRoutes() {
    val pebble = PebbleEngine.Builder().build()

    get("/tasks") {
        val model = mapOf(
            "title" to "Tasks",
            "tasks" to TaskRepository.all()
        )
        val template = pebble.getTemplate("templates/tasks/index.peb")
        val writer = StringWriter()
        template.evaluate(writer, model)
        call.respondText(writer.toString(), ContentType.Text.Html)
    }

    post("/tasks") {
        val title = call.receiveParameters()["title"].orEmpty().trim()
        if (title.isNotBlank()) {
            TaskRepository.add(title)
        }
        call.respondRedirect("/tasks") // PRG pattern
    }

    post("/tasks/{id}/delete") {
        val id = call.parameters["id"]?.toIntOrNull()
        id?.let { TaskRepository.delete(it) }
        call.respondRedirect("/tasks") // PRG pattern
    }
}
```

**Update `Application.kt`** (or wherever routing is configured):

```kotlin
import io.ktor.server.application.*
import io.ktor.server.routing.*
import routes.taskRoutes

fun Application.module() {
    routing {
        taskRoutes()
    }
}
```

### Step 3: Create Task List Template

> **💡 Pebble Template Syntax Primer**
>
> Pebble uses three types of delimiters:
>
> | Syntax | Purpose | Example |
> |--------|---------|---------|
> | `{{ variable }}` | **Output** - Print variable values | `{{ task.title }}` outputs "Buy milk" |
> | `{% statement %}` | **Logic** - Control structures (if, for, extends) | `{% for task in tasks %}...{% endfor %}` |
> | `{# comment #}` | **Comments** - Not rendered in HTML | `{# TODO: Add pagination #}` |
>
> **Common statements**:
> - `{% extends "base.peb" %}` - Inherit from parent template
> - `{% block content %}...{% endblock %}` - Define/override content sections
> - `{% for item in list %}...{% endfor %}` - Loop over collections
> - `{% if condition %}...{% endif %}` - Conditional rendering
> - `{% empty %}` - Inside `{% for %}`, shown if list is empty
>
> **Filters** (pipe syntax):
> - `{{ tasks | length }}` - Get list size (outputs: `3`)
> - `{{ title | escape }}` - HTML-escape (auto-enabled in Pebble)
> - `{{ price | default(0) }}` - Fallback value if null
>
> **Reference**: [Pebble Documentation](https://pebbletemplates.io/) • `../../references/pebble-intro.md`

Edit `src/main/re../../references/templates/tasks/index.peb`:

```html
{% extends "base.peb" %}

{% block content %}
<h1>Tasks</h1>

<section aria-labelledby="add-heading">
  <h2 id="add-heading">Add a new task</h2>
  <form action="/tasks" method="post">
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
      <li id="task-{{ task.id }}">
        <span>{{ task.title }}</span>
        <form action="/tasks/{{ task.id }}/delete" method="post" style="display: inline;">
          <button type="submit" aria-label="Delete task: {{ task.title }}">Delete</button>
        </form>
      </li>
    {% empty %}
      <li>No tasks yet. Add one above!</li>
    {% endfor %}
  </ul>
</section>
{% endblock %}
```

**Accessibility features**:
- `aria-labelledby`: Links sections to headings (SR announces "Add a new task, region")
- `aria-describedby`: Links input to hint (SR announces "Title, edit text, Keep it short and specific")
- `aria-label` on Delete button: SR announces "Delete task: Buy milk" (context-specific)
- Semantic `<section>`, `<h2>`, `<ul>` structure

### Step 4: Test No-JS Baseline

1. **Disable JavaScript** in browser:
   - Chrome: DevTools (F12) → Settings (⚙️) → Debugger → "Disable JavaScript"
   - Firefox: about:config → javascript.enabled → false

2. **Reload http://localhost:8080/tasks**

3. **Add a task**:
   - Type "Buy milk" in Title field
   - Click "Add Task"
   - **Expected**: Page reloads, "Buy milk" appears in list

4. **Delete a task**:
   - Click "Delete" next to "Buy milk"
   - **Expected**: Page reloads, task removed

**If this works, you have a fully functional, accessible baseline without any JavaScript.**

**Stop and check**:
- ✅ Adding tasks works (no-JS)
- ✅ Deleting tasks works (no-JS)
- ✅ Page reloads on each action (PRG pattern)
- ✅ Screen reader announces labels correctly (test with NVDA/VoiceOver if available)

---

## Activity 4: Add HTMX Progressive Enhancement

**Time**: 40 minutes
**Materials**: Updated routes and templates

### Step 1: Detect HTMX Requests

Add helper function to `Tasks.kt`:

```kotlin
fun ApplicationCall.isHtmx(): Boolean =
    request.headers["HX-Request"]?.equals("true", ignoreCase = true) == true
```

**How it works**: HTMX adds `HX-Request: true` header to all AJAX requests. Server checks this to decide whether to return full page or fragment.

### Step 2: Update POST /tasks Route

Replace `post("/tasks")` route with:

```kotlin
post("/tasks") {
    val title = call.receiveParameters()["title"].orEmpty().trim()

    // Validation
    if (title.isBlank()) {
        if (call.isHtmx()) {
            val error = """<div id="status" hx-swap-oob="true" role="alert" aria-live="assertive">
                Title is required. Please enter at least one character.
            </div>"""
            return@post call.respondText(error, ContentType.Text.Html, HttpStatusCode.BadRequest)
        } else {
            // No-JS path: redirect with error flag (handle in GET if needed)
            return@post call.respondRedirect("/tasks?error=required")
        }
    }

    val task = TaskRepository.add(title)

    if (call.isHtmx()) {
        // Return HTML fragment for new task
        val fragment = """<li id="task-${task.id}">
            <span>${task.title}</span>
            <form action="/tasks/${task.id}/delete" method="post" style="display: inline;"
                  hx-post="/tasks/${task.id}/delete"
                  hx-target="#task-${task.id}"
                  hx-swap="outerHTML">
              <button type="submit" aria-label="Delete task: ${task.title}">Delete</button>
            </form>
        </li>"""

        val status = """<div id="status" hx-swap-oob="true">Task "${task.title}" added successfully.</div>"""

        return@post call.respondText(fragment + status, ContentType.Text.Html, HttpStatusCode.Created)
    }

    call.respondRedirect("/tasks") // No-JS fallback
}
```

**Key pattern**:
- **HTMX path**: Returns `<li>` fragment + OOB status message
- **No-JS path**: Redirects to `/tasks` (page reload)
- Both paths end up in the same state (DRY principle)

### Step 3: Update POST /tasks/{id}/delete Route

```kotlin
post("/tasks/{id}/delete") {
    val id = call.parameters["id"]?.toIntOrNull()
    val removed = id?.let { TaskRepository.delete(it) } ?: false

    if (call.isHtmx()) {
        val message = if (removed) "Task deleted." else "Could not delete task."
        val status = """<div id="status" hx-swap-oob="true">$message</div>"""
        // Return empty content to trigger outerHTML swap (removes the <li>)
        return@post call.respondText(status, ContentType.Text.Html)
    }

    call.respondRedirect("/tasks")
}
```

### Step 4: Add HTMX Attributes to Template

Update `tasks/index.peb`:

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
      <li id="task-{{ task.id }}">
        <span>{{ task.title }}</span>
        <form action="/tasks/{{ task.id }}/delete" method="post" style="display: inline;"
              hx-post="/tasks/{{ task.id }}/delete"
              hx-target="#task-{{ task.id }}"
              hx-swap="outerHTML">
          <button type="submit" aria-label="Delete task: {{ task.title }}">Delete</button>
        </form>
      </li>
    {% empty %}
      <li>No tasks yet. Add one above!</li>
    {% endfor %}
  </ul>
</section>
{% endblock %}
```

**HTMX attributes explained**:
- **Add form**:
  - `hx-post="/tasks"`: AJAX POST on submit
  - `hx-target="#task-list"`: Insert response into task list
  - `hx-swap="beforeend"`: Append (not replace)

- **Delete form**:
  - `hx-post="/tasks/{id}/delete"`: AJAX POST
  - `hx-target="#task-{{ task.id }}"`: Target the specific `<li>`
  - `hx-swap="outerHTML"`: Replace entire `<li>` (removes it if response is empty)

### Step 5: Test HTMX Enhancement

1. **Re-enable JavaScript** in browser

2. **Reload http://localhost:8080/tasks**

3. **Add a task**:
   - Type "Buy oat milk"
   - Click "Add Task"
   - **Expected**: Task appears instantly, NO page reload
   - **Check DevTools Network tab**: See AJAX POST to `/tasks`

4. **Check live region announcement**:
   - Open browser console
   - Type: `document.getElementById('status').textContent`
   - **Expected**: "Task 'Buy oat milk' added successfully."
   - **With screen reader**: Should announce this message

5. **Delete a task**:
   - Click "Delete" next to "Buy oat milk"
   - **Expected**: Task removed instantly, NO page reload

6. **Verify no-JS still works**:
   - Disable JavaScript again
   - Repeat add/delete tests
   - **Expected**: Both still work (page reloads)

**Stop and check**:
- ✅ HTMX path: instant updates, no reload
- ✅ No-JS path: still works with redirects
- ✅ Live region updates (inspect with DevTools)
- ✅ Network tab shows AJAX requests (JS enabled) vs. full page loads (JS disabled)

---

## Activity 5: Accessibility Verification

**Time**: 25 minutes
**Materials**: Keyboard, screen reader (NVDA/VoiceOver), browser DevTools

### Test 1: Keyboard Navigation

**Enable JavaScript**, reload page, then:

1. **Tab through page**:
   - Tab 1: Skip link appears → Press Enter → Focus jumps to main
   - Tab 2: Title input
   - Tab 3: Add Task button
   - Tab 4: First Delete button
   - Continue tabbing through all Delete buttons

2. **Check focus indicators**:
   - Pico.css provides default focus outlines
   - Ensure you can always see which element has focus

3. **Submit form with keyboard**:
   - Focus Title input, type "Test task"
   - Press Enter (submits form)
   - **Expected**: Task added via HTMX (no reload)

**Result**: ✅ All interactive elements keyboard-accessible

### Test 2: Screen Reader Testing

**Tools**:
- **Windows**: NVDA (free, https://www.nvaccess.org/)
- **macOS**: VoiceOver (built-in, Cmd+F5)
- **Linux**: Orca (pre-installed on RHEL labs)

**Test with NVDA** (Windows):
1. Start NVDA (Ctrl+Alt+N)
2. Navigate to http://localhost:8080/tasks
3. **Listen for**:
   - "Tasks, heading level 1"
   - "Add a new task, heading level 2"
   - "Title, edit, Keep it short and specific" (input + hint)
4. **Type** "NVDA test task" and press Enter
5. **Listen for**: "Task 'NVDA test task' added successfully" (from live region)
6. **Tab to Delete button**:
   - **Expected**: "Delete task: NVDA test task, button"
7. **Press Space** (activates Delete)
8. **Listen for**: "Task deleted" (from live region)

**Result**: ✅ Screen reader announces labels, hints, and status messages

### Test 3: No-JS Parity

1. **Disable JavaScript** (DevTools → Settings → Disable JavaScript)
2. **Reload page**
3. **Add task**: "No-JS test"
   - **Expected**: Page reloads, task appears
4. **Delete task**:
   - **Expected**: Page reloads, task removed
5. **Compare with JS-enabled**:
   - Functionality identical (only UX differs: reload vs. instant)

**Result**: ✅ No-JS parity achieved

### Test 4: WCAG Quick Check

| Criterion | Test | Result |
|-----------|------|--------|
| **1.3.1 Info and Relationships (A)** | Inspect HTML: `<label for="title">` links to `<input id="title">` | ☐ Pass |
| **2.1.1 Keyboard (A)** | All features accessible via Tab, Enter, Space | ☐ Pass |
| **2.4.1 Bypass Blocks (A)** | Skip link appears on focus, jumps to main | ☐ Pass |
| **3.2.2 On Input (A)** | Changing input doesn't auto-submit (only explicit button press) | ☐ Pass |
| **3.3.2 Labels or Instructions (A)** | All inputs have `<label>` and hint text | ☐ Pass |
| **4.1.3 Status Messages (AA)** | Live region announces add/delete confirmations | ☐ Pass |

**Stop and check**:
- ✅ All WCAG tests pass
- ✅ Keyboard and SR users can complete all tasks
- ✅ No-JS path works identically

---

## Reflection Questions

1. **Server-first vs. client-first**: How would this lab differ if we used React instead of server-rendered HTML + HTMX? What would be harder? What would be easier?

2. **Progressive enhancement**: Imagine a user on a slow 3G connection where HTMX fails to load. How does our implementation handle this gracefully?

3. **Live regions**: Why do we use `aria-live="polite"` for success messages but `aria-live="assertive"` for errors? When would you choose one over the other?

4. **HTMX trade-offs**: What are the limitations of HTMX compared to a full JavaScript framework? When might HTMX *not* be appropriate?

---

## Further Reading

**Server-first architecture**
- Gross, C., Haddad, D., & Weibel, A. (2023). *Hypermedia Systems*. <https://hypermedia.systems/>
- Miller, J. (2022). "The case for server-side rendering." *Increment*, 21. <https://increment.com/frontend/case-for-server-side-rendering/>

**Progressive enhancement**
- GOV.UK Service Manual. "Using Progressive Enhancement." <https://www.gov.uk/service-manual/technology/using-progressive-enhancement>
- Champeon, S. (2003). "Progressive Enhancement and the Future of Web Design." SXSW presentation.

**HTMX & hypermedia**
- HTMX Documentation. <https://htmx.org/docs/>
- Gross, C. (2024). "HTMX: High Power Tools for HTML." *Communications of the ACM*, 67(2), 50-57.

**WCAG & accessibility**
- W3C (2024). *Web Content Accessibility Guidelines (WCAG) 2.2*. <https://www.w3.org/WAI/WCAG22/quickref/>
- Pickering, H. (2016). *Inclusive Design Patterns*. Smashing Magazine.

---

## Glossary Summary

| Term | Definition | Example/Context |
|------|------------|-----------------|
| **Server-first** | Architecture where server generates complete HTML pages | Ktor renders `tasks/index.peb` → sends full HTML to browser |
| **Progressive enhancement** | Building baseline (HTML) first, adding optional layers (CSS, JS) | Form works with POST/redirect; HTMX adds instant updates |
| **HTMX** | Library that adds AJAX capabilities via HTML attributes | `<form hx-post="/tasks" hx-target="#list">` |
| **PRG (Post-Redirect-Get)** | Pattern to prevent duplicate form submissions | POST /tasks → 303 Redirect → GET /tasks |
| **ARIA live region** | Element that announces dynamic changes to screen readers | `<div role="status" aria-live="polite">` |
| **Out-of-band (OOB) swap** | HTMX updating an element outside the main target | `<div id="status" hx-swap-oob="true">` |
| **Semantic HTML** | Using tags that convey meaning (not just structure) | `<main>`, `<section>`, `<label>`, `<button>` (not `<div onclick>`) |
| **Skip link** | Link to jump past repeated content (navigation) | `<a href="#main">Skip to content</a>` |
| **WCAG 2.2 AA** | Web accessibility standard (Level AA = target for most orgs) | GOV.UK, universities, large companies must comply |

---

## Git Commit Best Practices

Good commit messages are essential for your **portfolio assessment** and **professional practice**. Your commits tell the story of your development process.

### Conventional Commit Format

Use this structure for clear, searchable commits:

```
<type>(<scope>): <short description>

[Optional longer explanation]
```

**Examples**:
```bash
# Good ✅
git commit -m "feat(tasks): add HTMX progressive enhancement to delete button"
git commit -m "fix(a11y): add aria-label to delete buttons for screen readers"
git commit -m "docs(readme): add setup instructions for Codespaces"
git commit -m "refactor(templates): extract task item to partial template"

# Bad ❌
git commit -m "stuff"
git commit -m "fixed it"
git commit -m "update"
git commit -m "changes"
```

### Common Types

| Type | When to Use | Example |
|------|-------------|---------|
| `feat` | New feature or capability | `feat(tasks): add search by title` |
| `fix` | Bug fix | `fix(validation): prevent blank task titles` |
| `refactor` | Code restructure (no behavior change) | `refactor(store): move CSV logic to TaskStore class` |
| `docs` | Documentation only | `docs(readme): add IntelliJ setup guide` |
| `style` | Formatting, CSS, no code change | `style(tasks): improve focus indicator contrast` |
| `test` | Adding or fixing tests | `test(store): add TaskStore CRUD tests` |
| `chore` | Build, dependencies, tooling | `chore(deps): update Ktor to 2.3.11` |

### Scope (Optional but Helpful)

Scope = which part of the codebase changed:
- `(tasks)` = Task management feature
- `(a11y)` = Accessibility improvements
- `(htmx)` = HTMX enhancements
- `(templates)` = Pebble template changes
- `(docs)` = Documentation
- `(store)` = Data storage/persistence

### Why This Matters

**For portfolio assessment**:
- Demonstrates professional development practices
- Shows your understanding of changes (not just "what" but "why")
- Makes it easy to find specific features when preparing evidence

**For collaboration**:
- Team members understand changes at a glance
- Easy to search git history (`git log --grep="feat(a11y)"`)
- Tools can auto-generate changelogs from commit messages

### Week 6 Lab 1 Example Commit

For this lab, a good commit message would be:

```bash
git add src/main/kotlin/ src/main/re../../references/templates/ build.gradle.kts
git commit -m "feat(scaffold): implement server-first task manager with HTMX

- Add Ktor server with Pebble templating
- Implement TaskStore with CSV persistence
- Add dual-mode CRUD routes (HTMX + no-JS)
- Include WCAG 2.2 AA accessibility baseline (skip link, ARIA live region)
- Add progressive enhancement with HTMX for add/delete
- Tested with keyboard navigation and NVDA screen reader

Addresses Week 6 Lab 1 requirements.
WCAG: 2.4.1 (skip link), 4.1.3 (status messages)"
```

**Quick version** (for smaller changes):
```bash
git commit -m "feat(wk6-lab1): server-first scaffold with HTMX and WCAG 2.2 AA"
```

---

## Lab Checklist

Before leaving lab, confirm:

- [ ] **Server runs**: `./gradlew run` → http://localhost:8080/tasks loads
- [ ] **No-JS works**: Add/delete tasks with JS disabled (page reloads)
- [ ] **HTMX works**: Add/delete tasks with JS enabled (no reloads)
- [ ] **Keyboard accessible**: Tab through all controls, submit with Enter
- [ ] **Screen reader tested**: Labels, hints, and status messages announced (NVDA/VoiceOver)
- [ ] **Live region updates**: Inspect DevTools → `#status` text changes after add/delete
- [ ] **Code committed**: `git add .`, `git commit -m "wk6-lab1: server-first scaffold with HTMX"`

---

## Next Steps

In **Week 6 Lab 2** you will:
1. Conduct peer interviews (needs-finding)
2. Document consent protocol (ethics)
3. Build an inclusive backlog from research insights
4. Plan instrumentation for Week 9 evaluation

**Preparation**:
- Read `../../references/consent-pii-faq.md` and `../../references/privacy-by-design.md`
- Bring laptop with working scaffold (Lab 1 code)
- Be ready to pair with a classmate for interviews

---

**Lab authored by**: COMP2850 Teaching Team, University of Leeds
**Last updated**: 2025-01-14
**Licence**: Academic use only (not for redistribution)
