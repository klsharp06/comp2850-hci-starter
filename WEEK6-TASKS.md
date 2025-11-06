# Week 6 Lab 1: What You Need to Implement

This starter repo provides **scaffolding only**. You will build the core features by following the mdbook instructions.

---

## ✅ Already Complete (Don't Change)

- `Main.kt` - Server configuration, Pebble setup, session handling
- `_layout/base.peb` - Accessible base layout with skip link, ARIA live region
- `static/css/custom.css` - WCAG 2.2 AA styles
- `static/js/htmx-1.9.12.min.js` - HTMX library
- `routes/HealthCheck.kt` - Health endpoint
- `utils/SessionUtils.kt` - Anonymous session management

---

## 📝 TODO: Files You Must Create/Implement

### 1. `src/main/kotlin/data/TaskRepository.kt`
**Location**: Activity 3 Step 1 in mdbook

**What to copy**:
```kotlin
data class Task(val id: Int, var title: String)

object TaskRepository {
    // CSV storage with CRUD operations
}
```

**Purpose**: Simple task model with integer IDs and in-memory repository with CSV persistence.

---

### 2. `src/main/kotlin/routes/TaskRoutes.kt`
**Location**: Activity 3 Step 2 in mdbook

**What to implement**:
```kotlin
fun Route.taskRoutes() {
    // GET /tasks - List all tasks
    // POST /tasks - Add task (dual-mode: HTMX + no-JS)
    // POST /tasks/{id}/delete - Delete task (dual-mode)
}
```

**Key patterns**:
- **Dual-mode**: Check `call.isHtmx()` to return fragment OR redirect
- **PRG pattern**: No-JS uses POST-Redirect-GET
- **HTMX**: Return HTML fragment + OOB status message

---

### 3. `src/main/resources/templates/tasks/index.peb`
**Location**: Activity 3 Step 3 in mdbook

**What to implement**:
```html
{% extends "_layout/base.peb" %}

{% block content %}
  <h1>Tasks</h1>

  <!-- Add Task Form -->
  <form action="/tasks" method="post" hx-post="/tasks" ...>
    <label for="title">Title</label>
    <input id="title" name="title" aria-describedby="title-hint" ...>
    <button>Add Task</button>
  </form>

  <!-- Task List -->
  <ul id="task-list">
    {% for task in tasks %}
      <li id="task-{{ task.id }}">
        <span>{{ task.title }}</span>
        <form action="/tasks/{{ task.id }}/delete" method="post" ...>
          <button aria-label="Delete task: {{ task.title }}">Delete</button>
        </form>
      </li>
    {% empty %}
      <li>No tasks yet. Add one above!</li>
    {% endfor %}
  </ul>
{% endblock %}
```

**ARIA requirements**:
- `aria-labelledby` on sections
- `aria-describedby` on inputs
- `aria-label` on delete buttons (specific per task)

---

## ⚙️ How to Build

```bash
./gradlew build -x test    # Compile (tests not required for Week 6)
./gradlew run              # Start server on http://localhost:8080
```

**After implementing all 3 files**, you should be able to:
1. Visit http://localhost:8080/tasks
2. Add tasks
3. Delete tasks
4. Test with **JavaScript disabled** (no-JS mode)
5. Test with **keyboard navigation** (Tab, Enter, Space)

---

## 🎯 Week 6 Lab 1 Checklist

- [ ] `data/TaskRepository.kt` created with Task and TaskRepository
- [ ] `routes/TaskRoutes.kt` implemented with GET, POST routes
- [ ] `tasks/index.peb` template complete with ARIA attributes
- [ ] Server runs: `./gradlew run`
- [ ] Can add tasks (JS enabled)
- [ ] Can delete tasks (JS enabled)
- [ ] Can add tasks (JS disabled - page reloads)
- [ ] Can delete tasks (JS disabled - page reloads)
- [ ] Keyboard navigation works (Tab, Enter)
- [ ] Live region announces status messages
- [ ] Git commit with meaningful message

---

## 🔍 Testing Instructions

### Test 1: No-JS Mode
1. Open DevTools → Settings → Disable JavaScript
2. Reload page
3. Add task "No-JS test" → **Expected**: Page reloads, task appears
4. Delete task → **Expected**: Page reloads, task gone

### Test 2: HTMX Mode
1. Re-enable JavaScript, reload
2. Add task "HTMX test" → **Expected**: NO page reload, task appears instantly
3. Check `#status` div in DevTools → **Expected**: "Task added successfully"
4. Delete task → **Expected**: NO page reload, task removed instantly

### Test 3: Keyboard Navigation
1. Tab through page
2. First Tab → Skip link appears (blue background)
3. Continue Tab → Title input, Add button, Delete buttons
4. **Expected**: All interactive elements accessible via keyboard

---

## 📚 Reference

- **mdbook**: Week 6 Lab 1 (all activities)
- **Pebble docs**: `references/pebble-cheatsheet.md`
- **HTMX docs**: https://htmx.org/docs/
- **WCAG 2.2**: https://www.w3.org/WAI/WCAG22/quickref/

---

**Version**: Week 6 baseline (simple Int IDs, no toggle, no pagination)
**Next week**: Week 7 adds `completed` field and inline edit
