# Sidebar Subheadings Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Show only the active chapter's level-two headings in the Docsify sidebar and remove the “课程章节” label.

**Architecture:** Use Docsify's built-in `subMaxLevel` option so headings are generated from each chapter's Markdown rather than duplicated in `_sidebar.md`. Keep `_sidebar.md` responsible only for the home and chapter navigation links.

**Tech Stack:** Docsify 4, Markdown, PowerShell, headless Chrome

## Global Constraints

- Set `subMaxLevel` to exactly `2` so level-three headings such as `2.1.1` remain hidden.
- Preserve all ten course links in `docs/_sidebar.md`.
- Do not modify or stage the user's unfinished chapter 3 files.

---

### Task 1: Configure and verify the sidebar

**Files:**
- Modify: `docs/index.html:15-23`
- Modify: `docs/_sidebar.md:1-5`

**Interfaces:**
- Consumes: Docsify's `window.$docsify` configuration and Markdown sidebar source.
- Produces: An active-page sidebar containing H2 links, without the “课程章节” heading or H3 links.

- [ ] **Step 1: Record the current failing state**

Run:

```powershell
$html = Get-Content -LiteralPath 'docs/index.html' -Raw
$sidebar = Get-Content -LiteralPath 'docs/_sidebar.md' -Raw
if ($html -match 'subMaxLevel\s*:\s*2') { throw 'Expected subMaxLevel to be absent before implementation' }
if ($sidebar -notmatch '## 课程章节') { throw 'Expected heading to exist before implementation' }
```

Expected: exit code `0`, confirming both reported problems exist.

- [ ] **Step 2: Implement the minimal configuration change**

Add this property after `loadSidebar: true` in `docs/index.html`:

```javascript
subMaxLevel: 2,
```

Delete these lines from `docs/_sidebar.md`:

```markdown
## 课程章节

```

- [ ] **Step 3: Run static verification**

Run:

```powershell
$html = Get-Content -LiteralPath 'docs/index.html' -Raw
$sidebar = Get-Content -LiteralPath 'docs/_sidebar.md' -Raw
if ($html -notmatch 'subMaxLevel\s*:\s*2') { throw 'Missing subMaxLevel: 2' }
if ($sidebar -match '课程章节') { throw 'Sidebar heading was not removed' }
if (([regex]::Matches($sidebar, '^- \[第 \d+ 课：', 'Multiline')).Count -ne 10) { throw 'Expected ten course links' }
```

Expected: exit code `0`.

- [ ] **Step 4: Verify rendered behavior in Chrome**

Serve `docs` locally, open the chapter 2 hash route in headless Chrome, and inspect the rendered DOM. Confirm that the active sidebar contains links for the chapter's H2 headings, contains no `2.1.1` link, and contains no “课程章节” text.

Expected: all rendered-DOM assertions pass.

- [ ] **Step 5: Commit only the intended files**

```powershell
git add -- docs/index.html docs/_sidebar.md
git commit -m "feat: show chapter subheadings in sidebar"
```

Expected: the commit contains only `docs/index.html` and `docs/_sidebar.md`; chapter 3 remains uncommitted.
