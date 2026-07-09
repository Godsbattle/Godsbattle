# GitHub Profile Refresh Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace Christian Obanaka's outdated Web3 GitHub presentation with the approved futures-trader, community-and-affiliate, and trader-tools positioning.

**Architecture:** The profile README is versioned in `Godsbattle/Godsbattle`; account fields and repository presentation are GitHub-owned settings updated through authenticated GitHub APIs. Profile pins are the sole UI-only setting and must be changed through the signed-in GitHub profile page, then verified through GitHub's GraphQL read API.

**Tech Stack:** Markdown, Git, GitHub CLI, GitHub REST API, GitHub GraphQL API, signed-in GitHub web UI.

## Global Constraints

- Futures trading is the primary professional identity.
- Community and affiliate management for prop firms, trading companies, and brands is a core line of work.
- Tool creation is framed as practical problem-solving for traders, not as a full-time software-engineering identity.
- `trackmyprop` is the flagship long-term product without implying that all future work will be trading software.
- Keep the GitHub username `Godsbattle`; leave location, avatar, public-email settings, and account-security settings unchanged.
- Do not add statistics widgets, decorative badge walls, invented credentials, or private implementation details.
- Use only documented GitHub APIs. Profile pins must use GitHub's signed-in UI because GitHub does not publish a REST or GraphQL mutation for personal-profile pins.

---

### Task 1: Replace the profile README

**Files:**
- Modify: `README.md`

**Interfaces:**
- Consumes: Approved copy in `docs/superpowers/specs/2026-07-09-github-profile-refresh-design.md`.
- Produces: The Markdown GitHub renders at `https://github.com/Godsbattle`.

- [ ] **Step 1: Confirm the stale copy is still the repository baseline**

Run:

```bash
rg -n 'Blockchain|Web3|collaborate on Blockchain' README.md
```

Expected: three matches from the old profile text. If the baseline has changed remotely, fetch and review before editing rather than overwriting unreviewed work.

- [ ] **Step 2: Replace `README.md` with the approved content**

Write this exact file:

```md
# Christian Obanaka

**Futures trader · Community & affiliate manager · Creator of practical tools for traders**

I trade futures and work with prop firms, trading companies, and brands across community growth, affiliate partnerships, and trader experience.

Most things I create begin with a problem I encounter myself as a trader. I turn those problems into practical solutions other traders can use—from software and Notion systems to journals, templates, and educational resources. Some are free, some are paid; all are grounded in real trading workflows.

## Current focus

### trackmyprop

[trackmyprop](https://trackmyprop.app) is my flagship long-term product: desktop software for prop-firm traders who need a better way to manage accounts, journal trades, follow challenge rules, control risk, and understand their performance.

It began as a solution for my own trading workflow and is being developed for other traders facing the same problems.

Most development happens in a private monorepo. Public beta releases are available in [trackmyprop-desktop-releases](https://github.com/Godsbattle/trackmyprop-desktop-releases).

## What I do

- Trade futures and explore practical ways to improve trading workflows.
- Manage communities, affiliate programs, partnerships, and trader experience for prop firms and trading brands.
- Create trading journals, Notion templates, software, and other useful resources.
- Produce PC optimization and technology content through GodsBattle, reaching 9K+ YouTube subscribers and 4.5M+ views.

## Connect

[trackmyprop](https://trackmyprop.app) · [Website](https://godsbattle.net) · [YouTube](https://youtube.com/godsbattle) · [X](https://x.com/chrisgoingturbo) · [Email](mailto:christian@godsbattle.net)
```

- [ ] **Step 3: Validate the Markdown change and external links**

Run:

```bash
git diff --check
! rg -n 'Blockchain|Web3|software engineer|software developer' README.md
for url in \
  https://trackmyprop.app \
  https://godsbattle.net \
  https://youtube.com/godsbattle \
  https://x.com/chrisgoingturbo \
  https://github.com/Godsbattle/trackmyprop-desktop-releases
do
  test "$(curl -L -o /dev/null -sS -w '%{http_code}' --max-time 20 "$url")" = 200
done
```

Expected: no whitespace errors, no outdated or career-software wording, and every URL returns HTTP 200.

- [ ] **Step 4: Commit the README**

```bash
git add README.md
git commit -m "docs: refresh GitHub profile"
```

Expected: one commit containing only the approved `README.md` change.

### Task 2: Publish and verify the versioned profile content

**Files:**
- Verify: `README.md`
- Verify: `docs/superpowers/specs/2026-07-09-github-profile-refresh-design.md`
- Verify: `docs/superpowers/plans/2026-07-09-github-profile-refresh.md`

**Interfaces:**
- Consumes: Local commits on `main`.
- Produces: Updated `origin/main` in `Godsbattle/Godsbattle`.

- [ ] **Step 1: Check the exact commits and working tree before publication**

Run:

```bash
git status --short --branch
git log --oneline origin/main..HEAD
git diff --check origin/main..HEAD
```

Expected: a clean working tree after the implementation plan is committed; only the reviewed design, implementation plan, and README commits are ahead of `origin/main`.

- [ ] **Step 2: Push the approved profile changes**

Run:

```bash
git push origin main
```

Expected: `main -> main` succeeds without a non-fast-forward rejection.

- [ ] **Step 3: Verify GitHub serves the new README**

Run:

```bash
gh api repos/Godsbattle/Godsbattle/contents/README.md --jq .content | base64 --decode > /tmp/godsbattle-live-readme.md
diff -u README.md /tmp/godsbattle-live-readme.md
```

Expected: `diff` exits 0 with no output.

### Task 3: Update profile fields and repository metadata

**Files:**
- None; these are GitHub account and repository settings.

**Interfaces:**
- Consumes: Authenticated `gh` session for `Godsbattle`.
- Produces: Consistent public identity fields, descriptions, homepages, and website topics.

- [ ] **Step 1: Record the current public settings for comparison**

Run:

```bash
gh api user --jq '{login,name,bio,company,blog,location,twitter_username}'
gh api repos/Godsbattle/Godsbattle --jq '{full_name,description,homepage,topics}'
gh api repos/Godsbattle/website --jq '{full_name,description,homepage,topics}'
gh api repos/Godsbattle/trackmyprop-desktop-releases --jq '{full_name,description,homepage,topics}'
```

Expected: authenticated user is `Godsbattle`; the output shows the stale bio/company and repository metadata identified in the design.

- [ ] **Step 2: Update the public account fields**

Run:

```bash
gh api --method PATCH user \
  -f name='Christian Obanaka' \
  -f bio='Futures trader · Community & affiliate manager · Creating practical tools and resources for traders · Building @trackmyprop' \
  -f company='thePropTrade' \
  -f blog='https://godsbattle.net' \
  -f twitter_username='chrisgoingturbo'
```

Expected: HTTP 200 JSON for `Godsbattle`. If GitHub reports a missing `user` OAuth scope, run `gh auth refresh -h github.com -s user`, complete GitHub's device authorization in the already signed-in browser, and retry this exact request.

- [ ] **Step 3: Update public repository metadata**

Run:

```bash
gh repo edit Godsbattle/Godsbattle \
  --description "Christian Obanaka's GitHub profile." \
  --homepage 'https://godsbattle.net'

gh repo edit Godsbattle/website \
  --description "Christian Obanaka's portfolio, writing, projects, and trader resources." \
  --homepage 'https://godsbattle.net' \
  --add-topic nextjs,typescript,react,cloudflare,personal-website

gh repo edit Godsbattle/trackmyprop-desktop-releases \
  --description 'Beta installers and auto-update feed for trackmyprop desktop.' \
  --homepage 'https://trackmyprop.app'
```

Expected: all three commands exit 0.

- [ ] **Step 4: Verify the exact public settings**

Run:

```bash
gh api user --jq '{login,name,bio,company,blog,location,twitter_username}'
gh api repos/Godsbattle/Godsbattle --jq '{full_name,description,homepage,topics}'
gh api repos/Godsbattle/website --jq '{full_name,description,homepage,topics}'
gh api repos/Godsbattle/trackmyprop-desktop-releases --jq '{full_name,description,homepage,topics}'
```

Expected:

- Name `Christian Obanaka`.
- Approved 123-character bio.
- Company `thePropTrade`, website `https://godsbattle.net`, X username `chrisgoingturbo`, and unchanged blank location.
- All three approved descriptions and homepages.
- Website topics include `nextjs`, `typescript`, `react`, `cloudflare`, and `personal-website`.

### Task 4: Pin and visually verify the strongest public repositories

**Files:**
- None; this is a GitHub profile UI setting.

**Interfaces:**
- Consumes: Signed-in GitHub browser session and public repositories `Godsbattle/website` and `Godsbattle/trackmyprop-desktop-releases`.
- Produces: A two-item `Pinned` section replacing GitHub's automatic `Popular repositories` section.

- [ ] **Step 1: Open the signed-in profile pin editor**

Navigate to `https://github.com/Godsbattle`, locate the `Popular repositories` or `Pinned` section, and activate `Customize your pins`.

Expected: GitHub opens the pin-selection dialog for the authenticated `Godsbattle` account.

- [ ] **Step 2: Select only the two approved repositories**

In the dialog, deselect any existing items, select `website` and `trackmyprop-desktop-releases`, order `website` first and `trackmyprop-desktop-releases` second, then activate `Save pins`.

Expected: the dialog closes and the profile section is titled `Pinned` with exactly two cards in the approved order.

- [ ] **Step 3: Verify pins through GitHub's read API**

Run:

```bash
gh api graphql -f query='query { user(login: "Godsbattle") { pinnedItems(first: 6, types: REPOSITORY) { nodes { ... on Repository { nameWithOwner } } } } }' --jq '.data.user.pinnedItems.nodes[].nameWithOwner'
```

Expected output:

```text
Godsbattle/website
Godsbattle/trackmyprop-desktop-releases
```

If the in-app browser connection cannot be established, do not use undocumented GitHub endpoints or reuse browser credentials outside the browser. Leave GitHub's automatic `Popular repositories` section intact and report this single remaining UI-only action with the exact two repository names and order.

- [ ] **Step 4: Perform final public verification**

Inspect `https://github.com/Godsbattle` as rendered and confirm:

- The README begins with `Christian Obanaka` and the approved three-part identity line.
- Profile name, bio, company, website, and X link are visible and correct.
- No blockchain or Web3 positioning remains.
- `trackmyprop` is prominent without presenting software building as Christian's sole career.
- Repository cards show the updated descriptions and homepages.
- The page contains no broken Markdown or obviously broken public links.
