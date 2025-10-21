# Informed Consent Script Template

**Module**: COMP2850 Human-Computer Interaction
**Activity**: Peer pilot evaluation (Week 9)
**Researcher**: [Your Name]
**Date**: [YYYY-MM-DD]

---

## Introduction

> Hi, thanks for agreeing to help with my COMP2850 evaluation. This should take about 15 minutes. I'll ask you to complete 4 tasks using my task manager application, and I'll collect some basic timing and error data. Everything is anonymous—I won't record your name or any personal details.

## Purpose

> The purpose of this pilot is to evaluate whether my task manager is usable and accessible. I'm testing the interface, not you. If something is confusing or doesn't work, that's valuable feedback for me.

## What You'll Do

> I'll give you 4 tasks:
> 1. **T3 (Add)**: Add a new task with the title "Buy milk"
> 2. **T1 (Filter)**: Filter tasks to show only incomplete items
> 3. **T2 (Edit)**: Edit the title of "Buy milk" to "Buy oat milk"
> 4. **T4 (Delete)**: Delete the "Buy oat milk" task
>
> For each task, I'll start a timer when you begin and stop it when you tell me you're done. I'll also note any errors (like validation messages).

## Data Collected

> I will collect:
> - **Anonymous session ID** (random 6-character code, e.g., `P1_a3f7`)
> - **Task completion times** (in seconds)
> - **HTTP status codes** (200 = success, 400 = validation error, etc.)
> - **Brief notes** about what I observe (e.g., "Pilot paused to read error message")
> - **Your confidence ratings** (1–5 scale) after each task
>
> I will NOT collect:
> - Your name
> - Student ID
> - Email address
> - Any other personally identifiable information (PII)

## Data Storage & Access

> Data will be stored in:
> - A CSV file (`data/metrics.csv`) on my local machine (not uploaded to any cloud service)
> - My GitLab repository (private, accessible only to me and module staff)
>
> Data will be used for:
> - My Week 9 Lab 2 submission (Task 1 evidence pack)
> - Analysis in Week 10 (identifying usability issues)
>
> Data will be retained until:
> - End of academic year (2024/25), then deleted
> - You can request deletion at any time by contacting me at [your email]

## Your Rights (UK GDPR)

> - **Right to withdraw**: You can stop at any time, no explanation needed
> - **Right to access**: You can see the data I've collected about you (ask for your session ID data)
> - **Right to erasure**: You can request I delete your data (I'll remove your session ID from the CSV)
> - **Right to complain**: If you have concerns, contact the University's Data Protection Officer: dpo@leeds.ac.uk

## Consent Confirmation

> Do you consent to participate in this pilot under the terms described?
> - [ ] **Yes, I consent**
> - [ ] **No, I do not consent** (thank you for your time; pilot ends here)

**Signature (verbal or written)**: ___________________________
**Date**: ___________________________
**Session ID assigned**: ___________________________

---

## Post-Pilot Debrief

> Thanks for completing the tasks! Do you have any questions or feedback?
>
> - Did anything confuse you?
> - Were there any moments where you felt stuck?
> - [Note responses for qualitative analysis]

**Debrief completed**: ___________________________
**Pilot notes saved**: [ ] `pilot-notes/P#_notes.md`
**Metrics saved**: [ ] `data/metrics.csv` updated

---

**Template source**: Week 9 Lab Pack, COMP2850 (University of Leeds)
**References**: `../../../references/consent-pii-faq.md`, ICO UK GDPR guidance
