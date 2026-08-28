# shoebum.io Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship shoebum.io as Shubham's personal portfolio site with the Padelato and Storywalk apps served at `/padelato` and `/storywalk`, then retire the github.io URLs and make the app repos private.

**Architecture:** One public GitHub Pages user-site repo (`shubhampansare/shubhampansare.github.io`, local folder `Work/Shoebum/`) serves everything: a hand-written single-file portfolio at `/` plus the two self-contained app artifacts copied in as `/padelato/index.html` and `/storywalk/index.html`. App development stays in each app's own folder and (renamed, private) repo.

**Tech Stack:** Static HTML/CSS/JS (no frameworks, no build step), GitHub Pages, gh CLI, Playwright MCP for browser verification, Namecheap DNS (user action).

**Spec:** `docs/superpowers/specs/2026-08-27-shoebum-io-design.md` (read it first; this plan implements it).

## Global Constraints

- NEVER use em dashes in any output: site copy, commit messages, README, comments. Plain hyphens only. (User's global rule.)
- NEVER mention ADHD or any personal health information in ANY public content: the site, the profile README, repo names/descriptions, commit messages. (User's explicit rule, 2026-08-28.) Storywalk's public positioning is "built for restless attention spans".
- The reading app is called **Storywalk** everywhere. The word "Hamming" must not appear anywhere on shoebum.io (it may appear in the Storywalk app itself; that app is out of scope).
- The padel app is called **Padelato** on the site. In-app branding is out of scope.
- Single-file philosophy: the portfolio is one `index.html`, no frameworks, no build step. Google Fonts is the only allowed external resource.
- App repos are modified only where this plan says so (deploy script + CLAUDE.md note in the Padel repo). No app feature changes.
- All GitHub operations via `gh` CLI. GitHub user: `shubhampansare`.
- Paths on this machine:
  - Site repo: `/Users/shubhampansare/Documents/Work/Shoebum`
  - Padelato app source: `/Users/shubhampansare/Documents/Work/Padel/index.html`
  - Storywalk app source: `/Users/shubhampansare/Documents/Work/Personal/hamming-path/index.html` (self-contained ~3 MB, audio embedded as data URIs; verified, no external asset references)
- Contact details for the site: GitHub `github.com/shubhampansare`, email `shubh.pansare@gmail.com`.
- End every commit message with: `Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>`
- macOS host: `sips` is available for image resizing, `python3 -m http.server` for local serving, `dig` for DNS checks.
- Playwright MCP tools (`browser_navigate`, `browser_take_screenshot`, `browser_click`, `browser_console_messages`, `browser_resize`) are the browser-verification mechanism. Load their schemas via ToolSearch first.

---

### Task 1: Capture project screenshots

**Files:**
- Create: `assets/padelato.png`, `assets/storywalk.png`, `assets/sendrishta.png` (in the site repo)

**Interfaces:**
- Produces: three optimized PNGs at exactly these paths; Task 2's `index.html` references them as `assets/padelato.png` etc.

- [ ] **Step 1: Serve both local apps**

```bash
cd /Users/shubhampansare/Documents/Work/Padel && python3 -m http.server 8321 &
cd /Users/shubhampansare/Documents/Work/Personal/hamming-path && python3 -m http.server 8322 &
```

Run (verification): `curl -s -o /dev/null -w '%{http_code}\n' http://localhost:8321/ http://localhost:8322/`
Expected: `200` twice.

- [ ] **Step 2: Capture Padelato and Storywalk at phone size**

Using Playwright MCP: `browser_resize` to 390x844, `browser_navigate` to `http://localhost:8321/`, wait for the app's first screen to render, `browser_take_screenshot` (PNG, full viewport, not fullPage) saved as `padelato-raw.png` in the scratchpad. Repeat for `http://localhost:8322/` -> `storywalk-raw.png` (the title screen is fine; do not start the game).

- [ ] **Step 3: Capture SendRishta at desktop size**

`browser_resize` to 1280x800, `browser_navigate` to `https://sendrishta.com`, dismiss any cookie banner if present, `browser_take_screenshot` -> `sendrishta-raw.png` in the scratchpad.

- [ ] **Step 4: Optimize into the site repo**

```bash
mkdir -p /Users/shubhampansare/Documents/Work/Shoebum/assets
cd /Users/shubhampansare/Documents/Work/Shoebum/assets
sips -Z 900 <scratchpad>/padelato-raw.png --out padelato.png
sips -Z 900 <scratchpad>/storywalk-raw.png --out storywalk.png
sips -Z 1200 <scratchpad>/sendrishta-raw.png --out sendrishta.png
```

Run (verification): `ls -la /Users/shubhampansare/Documents/Work/Shoebum/assets/ && du -h /Users/shubhampansare/Documents/Work/Shoebum/assets/*`
Expected: three PNGs, each under ~500 KB. If any is larger, re-export with `sips -s format jpeg -s formatOptions 80` and use the `.jpg` (then Task 2 must reference the `.jpg` name).

- [ ] **Step 5: Visually verify screenshots**

Read each PNG with the Read tool and confirm: Padelato shows its setup/home screen, Storywalk shows its title screen, SendRishta shows its landing page. No blank/white captures.

- [ ] **Step 6: Kill the servers and commit**

```bash
kill %1 %2 2>/dev/null
cd /Users/shubhampansare/Documents/Work/Shoebum
git add assets && git commit -m "Add project screenshots for portfolio cards

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 2: Build the portfolio site (`index.html` + `404.html`)

**Files:**
- Create: `index.html`, `404.html` (site repo root)

**Interfaces:**
- Consumes: `assets/padelato.png`, `assets/storywalk.png`, `assets/sendrishta.png` from Task 1.
- Produces: a complete site at `/`; internal links point to `/padelato/` and `/storywalk/` (trailing slash), external links to `https://sendrishta.com` and `https://github.com/shubhampansare`.

**Note for implementer:** load the `frontend-design:frontend-design` skill before writing the page. The spec's visual direction is "playful and personal". Everything below is the required content and design system; layout craft within it is yours.

- [ ] **Step 1: Write `index.html` using this exact copy deck**

All copy verbatim (fix nothing, no em dashes anywhere). This copy is a DRAFT the user will correct in review; ship it as written here.

```text
<title> tag: shoebum - Shubham Pansare
Meta description: Shubham Pansare builds AI apps and playful web things. SendRishta, Padelato, Storywalk, and products built at Tuul.

HERO
  Wordmark: shoebum
  Headline: Hi, I'm Shubham. I turn odd little ideas into real, working apps.
  Subline: I head product at Tuul, an AI studio that ships AI apps for clients. After hours I build small, sharp tools for things I care about: padel nights, matrimony biodatas, and long reads you can play.
  CTA buttons: "See my projects" (scrolls to Live projects), "Say hi" (scrolls to Contact)

ABOUT (section heading: "A few interesting things about me")
  - I like single-file software. Padelato and Storywalk each ship as one HTML file: no build, no backend, no excuses.
  - I play padel, which is why Padelato exists. Fair draws and live standings beat spreadsheet chaos.
  - (Corrected in review, 2026-08-28: About cards are now personal - padel, motorhead, adrenaline, curiosity, food and people, dance/music/art. See index.html for the shipped copy.)
  - Day job: AI apps for clients at Tuul. Night job: whatever idea will not leave me alone.

LIVE PROJECTS (section heading: "Live projects", subline: "Things you can use right now")
  Card: SendRishta
    One-liner: A modern matrimony biodata creator. Beautiful biodatas in minutes.
    Link: https://sendrishta.com  (label: sendrishta.com)
    Image: assets/sendrishta.png
  Card: Padelato
    One-liner: Run a padel Americano or Mexicano night without spreadsheets. Fair draws, live standings, works offline.
    Link: /padelato/  (label: shoebum.io/padelato)
    Image: assets/padelato.png
  Card: Storywalk
    One-liner: Long reads, played like a game. Walk through a classic talk one idea at a time. Built for restless attention spans.
    Link: /storywalk/  (label: shoebum.io/storywalk)
    Image: assets/storywalk.png

PRODUCTS (section heading: "Products I've worked on", subline: "Client and studio work")
  Card: AI Angler - A fishing tournament companion app.
  Card: FishScore - Fish measurement and scoring, from a photo.
  Card: PawsitivID - AI pet management, licensing, and pet services.
  Card: Travjoy - A travel app for discovering destinations and experiences.
  (No images for these; styled monogram/initial tiles instead.)

CONTACT (section heading: "Say hi")
  Line: The fastest way to reach me:
  Links: GitHub -> https://github.com/shubhampansare, Email -> mailto:shubh.pansare@gmail.com

FOOTER
  shoebum.io - handmade, one HTML file, no frameworks. (c) 2026 Shubham Pansare.
```

Design system (use these tokens; refine shades only if contrast requires):

```css
:root{
  --bg:#FFF6EC;        /* warm cream page background */
  --card:#FFFFFF;
  --ink:#221A14;       /* near-black warm ink, text + borders */
  --accent:#FF5D46;    /* coral, primary accent */
  --accent-2:#FFB525;  /* mustard, secondary accent */
  --accent-3:#0FA88E;  /* teal, tertiary accent */
  --radius:20px;
}
/* Fonts: Google Fonts "Fraunces" (600/700) for display, system sans stack for body.
   Cards: white, 2px solid var(--ink) border, offset solid shadow (6px 6px 0 var(--ink)),
   hover lifts card 2px and grows the shadow. Each live-project card uses a different
   accent as its shadow/tag color (SendRishta coral, Padelato teal, Storywalk mustard). */
```

Required behaviors and quality bars:

- Single file, semantic HTML (`header`, `main`, `section`, `footer`, one `h1`), CSS in one `<style>` block, JS (only for smooth-scroll CTAs and optional micro-interactions) in one `<script>` block.
- Fully responsive: cards stack in one column below 700 px; no horizontal page scroll at 390 px.
- Images: `loading="lazy"`, explicit `width`/`height` or `aspect-ratio` to prevent layout shift, descriptive `alt` text.
- Accessibility: visible focus states, color contrast of body text on cream >= 4.5:1, `prefers-reduced-motion` disables the micro-interactions.
- Single light theme (deliberate; no dark mode).
- No analytics, no external JS, no external CSS beyond the Google Fonts stylesheet.

- [ ] **Step 2: Write `404.html`**

Same design tokens and fonts as `index.html` (copy the token block; keep the file small). Content, verbatim:

```text
Heading: 404. This shoe never dropped.
Line: Whatever lived here is gone or never existed.
Link back: "Back to shoebum.io" -> /
```

- [ ] **Step 3: Serve and verify in a real browser**

```bash
cd /Users/shubhampansare/Documents/Work/Shoebum && python3 -m http.server 8320 &
```

Using Playwright MCP against `http://localhost:8320/`:
1. `browser_console_messages`: zero errors.
2. `browser_resize` 1440x900, `browser_take_screenshot` -> review with Read: all five sections present, screenshots render, nothing overflows.
3. `browser_resize` 390x844, `browser_take_screenshot` -> review: single column, no horizontal scroll, tap targets sane.
4. `browser_click` on "See my projects" scrolls to the Live projects section.
5. Verify the three project card links have `href` exactly `https://sendrishta.com`, `/padelato/`, `/storywalk/`.
6. Navigate to `http://localhost:8320/404.html`: renders, link points to `/`.

Expected: all six checks pass. Fix and re-check before proceeding.

- [ ] **Step 4: Grep guard for banned words**

Run: `grep -ic "hamming" index.html 404.html; grep -c $'—' index.html 404.html`
Expected: all zeros (no "Hamming", no em dash characters).

- [ ] **Step 5: Kill server and commit**

```bash
kill %1 2>/dev/null
cd /Users/shubhampansare/Documents/Work/Shoebum
git add index.html 404.html && git commit -m "Add portfolio site and 404 page

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

**STOP and verify checkpoint:** show the user the two screenshots from Step 3 and wait for a look-and-feel go-ahead before any GitHub work. The user corrects About/copy facts here too.

---

### Task 3: Copy app artifacts into the site repo

**Files:**
- Create: `padelato/index.html`, `storywalk/index.html`, `.nojekyll` (site repo)

**Interfaces:**
- Produces: apps at `/padelato/` and `/storywalk/` exactly where Task 2's links point. `.nojekyll` disables Jekyll processing on GitHub Pages (user sites Jekyll-build by default).

- [ ] **Step 1: Copy the artifacts**

```bash
cd /Users/shubhampansare/Documents/Work/Shoebum
mkdir -p padelato storywalk
cp /Users/shubhampansare/Documents/Work/Padel/index.html padelato/index.html
cp /Users/shubhampansare/Documents/Work/Personal/hamming-path/index.html storywalk/index.html
touch .nojekyll
```

Run (verification): `du -h padelato/index.html storywalk/index.html && ls -la .nojekyll`
Expected: `padelato/index.html` present (tens-to-hundreds of KB), `storywalk/index.html` ~3 MB, `.nojekyll` exists.

- [ ] **Step 2: Click-through both apps locally**

```bash
cd /Users/shubhampansare/Documents/Work/Shoebum && python3 -m http.server 8320 &
```

Using Playwright MCP:
1. `http://localhost:8320/padelato/`: app renders its setup/home screen; `browser_console_messages` shows no errors; one interaction works (e.g. tapping into player setup).
2. `http://localhost:8320/storywalk/`: title screen renders; no console errors; clicking start begins the game.

Expected: both pass.

- [ ] **Step 3: Kill server and commit**

```bash
kill %1 2>/dev/null
cd /Users/shubhampansare/Documents/Work/Shoebum
git add padelato storywalk .nojekyll && git commit -m "Add Padelato and Storywalk app artifacts

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 4: Rename the app repos, update remotes, add Padelato deploy script

**Files:**
- Modify: remotes of `/Users/shubhampansare/Documents/Work/Padel` and `/Users/shubhampansare/Documents/Work/Personal/hamming-path`
- Create: `/Users/shubhampansare/Documents/Work/Padel/deploy.sh`
- Modify: `/Users/shubhampansare/Documents/Work/Padel/CLAUDE.md` (deployment section only)

**Interfaces:**
- Produces: GitHub repos `shubhampansare/padelato` and `shubhampansare/storywalk`; `deploy.sh` that later releases will call.

- [ ] **Step 1: Verify gh auth and rename both repos**

```bash
gh auth status
gh repo rename padelato -R shubhampansare/padel-americano --yes
gh repo rename storywalk -R shubhampansare/hamming-path --yes
```

Run (verification): `gh repo view shubhampansare/padelato --json name,visibility -q '.name+" "+.visibility' && gh repo view shubhampansare/storywalk --json name,visibility -q '.name+" "+.visibility'`
Expected: `padelato PUBLIC` and `storywalk PUBLIC` (still public at this stage; they flip private in Task 8).

- [ ] **Step 2: Update local remotes**

```bash
cd /Users/shubhampansare/Documents/Work/Padel && git remote set-url origin https://github.com/shubhampansare/padelato.git
cd /Users/shubhampansare/Documents/Work/Personal/hamming-path && git remote set-url origin https://github.com/shubhampansare/storywalk.git
```

Run (verification): `cd /Users/shubhampansare/Documents/Work/Padel && git fetch origin --dry-run && cd /Users/shubhampansare/Documents/Work/Personal/hamming-path && git fetch origin --dry-run && echo OK`
Expected: no errors, `OK`.

- [ ] **Step 3: Add `deploy.sh` to the Padel repo**

Write `/Users/shubhampansare/Documents/Work/Padel/deploy.sh`:

```bash
#!/usr/bin/env bash
# Deploy Padelato to shoebum.io: copy the shipped file into the site repo and push.
set -euo pipefail
SITE=/Users/shubhampansare/Documents/Work/Shoebum
cp "$(dirname "$0")/index.html" "$SITE/padelato/index.html"
cd "$SITE"
git add padelato/index.html
git diff --cached --quiet && { echo "No changes to deploy."; exit 0; }
git commit -m "Deploy Padelato update"
git push
echo "Deployed. Verify at https://shoebum.io/padelato/"
```

```bash
chmod +x /Users/shubhampansare/Documents/Work/Padel/deploy.sh
```

Run (verification): `bash -n /Users/shubhampansare/Documents/Work/Padel/deploy.sh && echo SYNTAX-OK && /Users/shubhampansare/Documents/Work/Padel/deploy.sh`
Expected: `SYNTAX-OK`, then `No changes to deploy.` (Task 3 already copied the identical file).

- [ ] **Step 4: Update `Padel/CLAUDE.md` release rules**

In `/Users/shubhampansare/Documents/Work/Padel/CLAUDE.md`, find the deployment/release section (it references GitHub Pages / `shubhampansare.github.io/padel-americano`) and replace the deployment instruction with:

```text
Deployment: the app is served at https://shoebum.io/padelato/ from the Shoebum
site repo (Work/Shoebum). To release, run ./deploy.sh after committing here.
This repo's own GitHub Pages is disabled and the repo is private.
```

Keep all other release rules (version bumps, tests) untouched.

Run (verification): `grep -n "shoebum.io/padelato" /Users/shubhampansare/Documents/Work/Padel/CLAUDE.md && grep -c "padel-americano" /Users/shubhampansare/Documents/Work/Padel/CLAUDE.md`
Expected: the new line found; `padel-americano` count is 0 (or only in historical-changelog context if the file has one; judgment call, but no live instruction may reference it).

- [ ] **Step 5: Commit and push the Padel repo**

```bash
cd /Users/shubhampansare/Documents/Work/Padel
git add deploy.sh CLAUDE.md
git commit -m "Add shoebum.io deploy script, update release docs

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
git push
```

Run (verification): `git status --short` -> empty; `gh repo view shubhampansare/padelato --json pushedAt -q .pushedAt` -> timestamp is just now.

---

### Task 5: Create the site repo on GitHub and smoke test github.io

**Files:**
- No new files (pushes the existing site repo). Do NOT create a `CNAME` file yet (it would redirect the smoke test to a dead domain).

**Interfaces:**
- Produces: live site at `https://shubhampansare.github.io` with `/padelato/` and `/storywalk/` working.

- [ ] **Step 1: Ensure branch is `main` and push**

```bash
cd /Users/shubhampansare/Documents/Work/Shoebum
git branch --show-current   # if it prints master: git branch -m main
gh repo create shubhampansare/shubhampansare.github.io --public --source . --push
```

Run (verification): `gh repo view shubhampansare/shubhampansare.github.io --json defaultBranchRef -q .defaultBranchRef.name`
Expected: `main`.

- [ ] **Step 2: Confirm Pages is enabled (user-site repos usually auto-enable)**

```bash
gh api repos/shubhampansare/shubhampansare.github.io/pages --jq '.status + " " + .html_url'
```

Expected: a status (`building` or `built`) and `https://shubhampansare.github.io/`. If this 404s, enable it:

```bash
gh api -X POST repos/shubhampansare/shubhampansare.github.io/pages -f 'source[branch]=main' -f 'source[path]=/'
```

- [ ] **Step 3: Wait for the build, then smoke test**

Poll (background loop, not foreground sleep; or the Monitor tool):

```bash
for i in $(seq 1 30); do
  code=$(curl -s -o /dev/null -w '%{http_code}' https://shubhampansare.github.io/)
  [ "$code" = "200" ] && break
  sleep 10
done
curl -s -o /dev/null -w 'root:%{http_code}\n' https://shubhampansare.github.io/
curl -s -o /dev/null -w 'padelato:%{http_code}\n' https://shubhampansare.github.io/padelato/
curl -s -o /dev/null -w 'storywalk:%{http_code}\n' https://shubhampansare.github.io/storywalk/
```

Expected: all three `200`. (Run the poll via a background Bash invocation so the session is not blocked.)

- [ ] **Step 4: Browser check the live github.io site**

Playwright MCP: navigate to `https://shubhampansare.github.io/`, no console errors, hero renders; `/padelato/` and `/storywalk/` load their first screens.
Expected: pass. No commit needed (nothing changed locally).

**STOP and verify checkpoint:** report smoke-test results to the user before the DNS step.

---

### Task 6: DNS at Namecheap (user action), custom domain, HTTPS

**Files:**
- Create: `CNAME` (site repo root, contains exactly `shoebum.io`)

**Interfaces:**
- Consumes: live github.io site from Task 5.
- Produces: `https://shoebum.io` serving the site with HTTPS enforced.

- [ ] **Step 1: Hand the user the DNS records and WAIT**

Show this table and ask the user to add it in Namecheap (Domain List -> shoebum.io -> Advanced DNS), deleting any default parking records first:

| Type  | Host | Value                    | TTL       |
|-------|------|--------------------------|-----------|
| A     | @    | 185.199.108.153          | Automatic |
| A     | @    | 185.199.109.153          | Automatic |
| A     | @    | 185.199.110.153          | Automatic |
| A     | @    | 185.199.111.153          | Automatic |
| CNAME | www  | shubhampansare.github.io | Automatic |

This is a hard user-action gate. Do not proceed until the user says the records are in.

- [ ] **Step 2: Verify DNS resolution**

```bash
dig +short shoebum.io A @1.1.1.1
dig +short www.shoebum.io CNAME @1.1.1.1
```

Expected: the four 185.199.x.153 IPs; `shubhampansare.github.io.`. If empty, wait for propagation (poll every few minutes via background loop or Monitor; Namecheap TTL is usually minutes).

- [ ] **Step 3: Set the custom domain**

```bash
cd /Users/shubhampansare/Documents/Work/Shoebum
echo "shoebum.io" > CNAME
git add CNAME && git commit -m "Point Pages at shoebum.io

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
git push
gh api -X PUT repos/shubhampansare/shubhampansare.github.io/pages -f cname=shoebum.io
```

Run (verification): `gh api repos/shubhampansare/shubhampansare.github.io/pages --jq '.cname'`
Expected: `shoebum.io`.

- [ ] **Step 4: Wait for the TLS certificate, then enforce HTTPS**

Poll (background/Monitor; cert can take minutes to ~an hour):

```bash
gh api repos/shubhampansare/shubhampansare.github.io/pages --jq '.https_certificate.state'
```

When it reports `approved` or `issued`:

```bash
gh api -X PUT repos/shubhampansare/shubhampansare.github.io/pages -F https_enforced=true
gh api repos/shubhampansare/shubhampansare.github.io/pages --jq '.https_enforced'
```

Expected: `true`.

---

### Task 7: Full live verification of shoebum.io

**Files:** none.

- [ ] **Step 1: HTTP checks**

```bash
curl -s -o /dev/null -w 'apex:%{http_code}\n'      https://shoebum.io/
curl -s -o /dev/null -w 'www-final:%{http_code}\n' -L https://www.shoebum.io/
curl -s -o /dev/null -w 'padelato:%{http_code}\n'  https://shoebum.io/padelato/
curl -s -o /dev/null -w 'storywalk:%{http_code}\n' https://shoebum.io/storywalk/
curl -s -o /dev/null -w 'missing:%{http_code}\n'   https://shoebum.io/nope/
curl -s https://shoebum.io/ | grep -c "I turn odd little ideas"
```

Expected: apex 200, www-final 200 (after redirect), padelato 200, storywalk 200, missing 404 (served by our 404.html), hero grep 1.

- [ ] **Step 2: Browser click-throughs on the live domain**

Playwright MCP on `https://shoebum.io`:
1. Portfolio: no console errors; screenshots at 1440x900 and 390x844, review both with Read.
2. `/padelato/`: reach the app's setup screen with one interaction.
3. `/storywalk/`: start the game; confirm audio does not error in the console after the start interaction.
4. Project card clicks from the portfolio actually land on `/padelato/`, `/storywalk/`, and sendrishta.com.

Expected: all pass.

**STOP and verify checkpoint:** show the user the live site screenshots and get an explicit OK. Task 8 makes repos private and kills old URLs; do not run it without this OK.

---

### Task 8: Make app repos private, disable their Pages

**Files:** none (GitHub state only).

- [ ] **Step 1: Disable Pages on both app repos**

```bash
gh api -X DELETE repos/shubhampansare/padelato/pages
gh api -X DELETE repos/shubhampansare/storywalk/pages
```

Run (verification): `gh api repos/shubhampansare/padelato/pages --jq .status; gh api repos/shubhampansare/storywalk/pages --jq .status`
Expected: both return 404 (`Not Found`).

- [ ] **Step 2: Flip both repos private**

```bash
gh repo edit shubhampansare/padelato --visibility private --accept-visibility-change-consequences
gh repo edit shubhampansare/storywalk --visibility private --accept-visibility-change-consequences
```

Run (verification): `gh repo view shubhampansare/padelato --json visibility -q .visibility; gh repo view shubhampansare/storywalk --json visibility -q .visibility`
Expected: `PRIVATE` twice.

- [ ] **Step 3: Confirm the old URLs are dead**

```bash
curl -s -o /dev/null -w 'old-padel:%{http_code}\n'   -L https://shubhampansare.github.io/padel-americano/
curl -s -o /dev/null -w 'old-hamming:%{http_code}\n' -L https://shubhampansare.github.io/hamming-path/
```

Expected: final status 404 for both (they may 301 to shoebum.io first; `-L` follows to our 404 page, which is the desired end state).

---

### Task 9: Profile README

**Files:**
- Create: new repo `shubhampansare/shubhampansare` containing `README.md` (work in a scratchpad clone; this repo does NOT live in `Work/Shoebum`).

- [ ] **Step 1: Create the repo with this README**

`README.md`, verbatim:

```markdown
# Hey, I'm Shubham

I build AI apps and playful web things. Everything I ship lives at
**[shoebum.io](https://shoebum.io)**.

## Live projects

- **[SendRishta](https://sendrishta.com)** - a modern matrimony biodata creator
- **[Padelato](https://shoebum.io/padelato/)** - padel Americano and Mexicano
  tournament nights without spreadsheets
- **[Storywalk](https://shoebum.io/storywalk/)** - long reads, played like a
  game; built for restless attention spans

## Day job

I run [Tuul](https://tuul.ai), an AI studio shipping apps for clients:
AI Angler, FishScore, PawsitivID. Also building Travjoy, a travel app.

## Say hi

[shoebum.io](https://shoebum.io) - shubh.pansare@gmail.com
```

```bash
mkdir -p <scratchpad>/profile-readme && cd <scratchpad>/profile-readme
git init -b main
# write README.md with the content above
git add README.md
git commit -m "Add profile README

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
gh repo create shubhampansare/shubhampansare --public --source . --push
```

Run (verification): `gh api repos/shubhampansare/shubhampansare/readme --jq .name`
Expected: `README.md`.

- [ ] **Step 2: Verify it renders on the profile**

Playwright MCP: navigate to `https://github.com/shubhampansare`, confirm the README content shows on the profile page and the three project links are present.
Expected: pass.

---

### Task 10: Workspace housekeeping

**Files:**
- Modify: `/Users/shubhampansare/Documents/Work/CLAUDE.md` (Projects section)

- [ ] **Step 1: Update the workspace CLAUDE.md**

In the Projects section:
1. Replace the Padel entry's deployment sentence so it reads that Padelato is deployed to `https://shoebum.io/padelato/` via `./deploy.sh` (repo `shubhampansare/padelato`, private), keeping the rest of the entry.
2. In the Hamming Path entry, note the product is now named Storywalk, served at `https://shoebum.io/storywalk/`, repo renamed to `shubhampansare/storywalk` (private), and that the user will move the local folder out of `Personal/` themselves.
3. Add a Shoebum project entry:

```markdown
### Shoebum
Personal website at https://shoebum.io (git repo at `Shoebum/`, GitHub:
shubhampansare/shubhampansare.github.io, public). Portfolio at `/`, app deploy
artifacts at `/padelato/` and `/storywalk/` - the apps are DEVELOPED in their
own folders (`Padel/`, Storywalk's folder), never here; only `deploy.sh` copies
shipped files in. DNS: Namecheap. Specs/plans in `Shoebum/docs/superpowers/`.
```

Run (verification): `grep -n "shoebum.io" /Users/shubhampansare/Documents/Work/CLAUDE.md | head`
Expected: the new entries appear; no instruction in the file still points at `shubhampansare.github.io/padel-americano`.

- [ ] **Step 2: Final commit of the site repo**

```bash
cd /Users/shubhampansare/Documents/Work/Shoebum
git status --short   # commit any stragglers (e.g. plan checkbox updates)
git add -A && git commit -m "Update docs after launch

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>" || echo "nothing to commit"
git push
```

Expected: clean tree, pushed.

---

## Self-audit (run at the end, report to user)

Re-read the spec (`docs/superpowers/specs/2026-08-27-shoebum-io-design.md`) and this plan. Then report:

1. Every spec "Verification criteria" bullet, with the command run and its actual output.
2. Any plan item NOT implemented, with the reason. If none, say so explicitly.
3. Open items handed back to the user: About-section fact corrections, extra contact links (LinkedIn/X), moving the Storywalk local folder out of `Personal/`, and adding a Storywalk `deploy.sh` once that folder exists.
