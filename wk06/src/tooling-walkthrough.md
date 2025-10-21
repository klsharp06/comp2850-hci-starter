# Running the Week 6 HCI labs in Codespaces and IntelliJ

This guide shows you how to build and run the Ktor + Pebble starter in two environments our cohort uses most: **GitHub Codespaces** (cloud) and **IntelliJ IDEA** (local). Follow the path that matches your setup; try both if you want to double-check parity.

---

## 1. Before you start
- Repository cloned or forked on GitHub.
- Java 17 available (Codespaces ships with it; on your machine install Temurin 17 or OpenJDK 17).
- Gradle wrapper (`./gradlew`) is already part of the repo—do not install Gradle globally.

You will run the Ktor app via `./gradlew run`. That task compiles the Kotlin code, starts the embedded server on port `8080`, and serves the Pebble templates you edit during the lab.

---

## 2. GitHub Codespaces walkthrough

### 2.1 Launch a Codespace
1. Open the repository on GitHub.
2. Click **Code → Codespaces → Create codespace on main** (or pick the branch you need).
3. Wait for the container to build and VS Code in the browser to appear (first build ~1–2 minutes).

### 2.2 Terminal, build, run
```bash
./gradlew run
```
- The first run downloads dependencies, so expect a short delay.
- When the build finishes you’ll see `Application started…` with port `8080` in the terminal.

### 2.3 Preview the app
1. Codespaces shows a **Ports** tab. When Gradle starts the server you’ll see port `8080` listed.
2. Click the globe icon to open the forwarded port in a new browser tab (or use the `Open in Browser` button).
3. The `Tasks` page should load. Leave this tab open while you work.

### 2.4 Develop and hot-refresh
- Edit templates under `src/main/re../../references/templates/…` or Kotlin under `src/main/kotlin/…`.
- After saving, switch back to the preview tab and use the browser’s refresh (`Ctrl/Cmd+R`).
- If you change Kotlin code you must stop (`Ctrl+C`) and re-run `./gradlew run` to pick up the changes.

### 2.5 Disable JavaScript for parity checks
- In the preview tab, open DevTools (F12), go to **Settings → Debugger → Disable JavaScript** (Chrome) or use the quick toggle in the **Command Palette** (`Ctrl+Shift+P`, search “Disable JavaScript”).
- Reload the page to test the server-first path, then re-enable JavaScript for HTMX checks.

### 2.6 Run tests (optional but recommended)
```bash
./gradlew test
```
Use this when we add linting or HTML validation later in the strand.

### 2.7 Git workflow inside Codespaces
- The VS Code UI has **Source Control** (left sidebar) for staging, committing, and pushing.
- Preferred workflow: `git status` in the terminal, `git add …`, `git commit -m "wk6s1: …"`, then `git push origin <branch>`.

When you close the browser tab, Codespaces pauses automatically. Reopen from GitHub to resume; the server command will need re-running.

---

## 3. IntelliJ IDEA walkthrough (local machine)

### 3.1 Clone and open
1. Clone the repo locally:
   ```bash
   git clone git@github.com:<org>/comp2850_hci.git
   cd comp2850_hci
   ```
2. Launch IntelliJ IDEA (Community or Ultimate) and choose **Open** → select the project directory.
3. IntelliJ will detect the Gradle project; accept the default settings when prompted.

### 3.2 Configure JDK & Gradle
- Check the JDK: `File → Project Structure → Project → SDK` should point to a JDK 17 installation.
- In the Gradle tool window, ensure **Gradle JVM** is also set to 17.
- Wait for the Gradle sync to finish (status bar bottom-right).

### 3.3 Apply the run configuration
The project already has a `main` function (for example in `comp2850/App.kt`). To run it:
1. Open `App.kt` (or `Application.kt` depending on the scaffold).
2. Click the green gutter icon next to `fun main` → **Run 'AppKt'** (IntelliJ creates a run configuration automatically).
3. The Run tool window shows the server log; look for `Application started…` on port `8080`.

### 3.4 View the site
- Open your browser (Chrome/Firefox) and go to `http://localhost:8080/tasks`.
- Leave the server running while you work. If you modify Kotlin files, press the green **Rerun** arrow or `Ctrl+F5` in the Run tool window to restart quickly.

### 3.5 Edit templates and Kotlin together
- IntelliJ watches resource files; you can edit `templates/base.peb`, `templates/tasks/index.peb`, etc., and just refresh the browser.
- For Kotlin changes (routes, repositories) restart the run configuration.

### 3.6 Toggle JavaScript for parity checks
- Use Chrome/Firefox DevTools → **Settings → Disable JavaScript** or install the “Disable JavaScript” extension. Repeat the same no-JS workflow as in Codespaces.

### 3.7 Run tests locally
- In the Gradle tool window, double-click **Tasks → verification → test** or run `./gradlew test` in IntelliJ’s integrated terminal.

### 3.8 Commit and push
- Use `VCS → Commit` (`Ctrl+K`) to stage, review diffs, and commit.
- Push with `Ctrl+Shift+K` once you are ready to share your Week 6 work or open a PR.

---

## 4. Quick troubleshooting
| Symptom | Fix |
|---------|-----|
| `Address already in use` | Another process is on port 8080. Stop previous runs or change the port in `application.conf` temporarily. |
| `Unsupported major.minor version` | IntelliJ/Codespaces is using the wrong JDK. Switch to 17. |
| Page blank / 500 errors | Check the server log; Pebble usually reports the template file + line number. |
| HTMX not firing | Confirm you added the `hx-*` attributes and that the server returns HTML (not JSON). |

Document assumptions in your README or wrap-up if you deviate (e.g. using a different IDE). Keep verifying that both environments behave identically—that parity is part of our accessibility and evaluation story.
