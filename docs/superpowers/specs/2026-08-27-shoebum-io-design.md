# shoebum.io - Personal Website and App Migration - Design

Date: 2026-08-27
Status: Approved design, pending user review of this document

## Goals

1. shoebum.io becomes Shubham's personal website: hero, about, live projects, products worked on, contact.
2. Padelato (formerly Padel Americano) and Storywalk (formerly Hamming Path)
   are served at `shoebum.io/padelato` and `shoebum.io/storywalk`.
3. The old `shubhampansare.github.io/*` URLs are retired. No redirects needed
   (both apps were experimental, no real users).
4. The app code repos become private with GitHub Pages disabled, while the
   GitHub profile still shows that these projects exist (via a profile README),
   without exposing the code inside.

## Non-goals / out of scope

- No feature changes to the Padelato or Storywalk apps themselves. That
  includes in-app branding: if the app UI still says "Padel Americano"
  anywhere, updating it to Padelato is a separate app-level task in
  `Work/Padel/`, not part of this project.
- SendRishta stays entirely on its own domain and repo; the site only links to it.
- Moving the local `Personal/hamming-path/` folder to its own Storywalk project
  folder: the user will do this separately after this project.
- No analytics, CMS, build tooling, frameworks, or backend. Static files only.

## Architecture

One public repo hosts everything that is served. App development stays in each
app's own folder and repo; only shipped artifacts are copied into the site repo.

- **Repo `shubhampansare/shubhampansare.github.io`** (public - GitHub Pages on
  the free plan requires a public repo). Local folder: `Work/Shoebum/`.
  - `/index.html` - the portfolio site (single file, no build step)
  - `/padelato/index.html` - deploy artifact copied from the Padelato repo
    (local dev folder stays `Work/Padel/`)
  - `/storywalk/index.html` - deploy artifact copied from the Storywalk repo
    (self-contained ~3 MB file; voice audio embedded as data URIs - verified)
  - `/CNAME` - contains `shoebum.io`
  - `/docs/superpowers/specs/` - design docs (this file)
- **Custom domain** `shoebum.io` set on this repo, HTTPS enforced.
- **App repos**: `hamming-path` renamed to `storywalk`, `padel-americano`
  renamed to `padelato`; then both flipped to private and their Pages sites
  disabled, only after the shoebum.io deployment is verified live.
- **Profile README**: public repo `shubhampansare/shubhampansare` whose README
  showcases the projects (SendRishta, Padel, Storywalk, plus products worked
  on) with links to shoebum.io and sendrishta.com. This is how visitors to the
  GitHub profile see the projects exist without seeing code.

### Accepted trade-offs (discussed and approved)

- The site repo is public, so the portfolio source and the shipped app files
  are visible there. Both apps are client-side single-file apps whose code is
  view-source-able by any visitor anyway. Going private protects the repos'
  history, tests, docs, and the Storywalk TTS voice pipeline (`tools/`,
  `voice/`), which never ship to the browser.
- localStorage state (e.g. Storywalk save key `hamming-path-v1`, Padel
  tournaments) does not carry over from the github.io origin to shoebum.io.
  Accepted: experimental, no real users.
- Old `shubhampansare.github.io/padel-americano` and `/hamming-path` deep links
  will 404 after migration. Accepted, no redirect stubs.

## DNS (Namecheap)

User pastes these into Namecheap Advanced DNS for shoebum.io (removing any
default parking records):

| Type  | Host | Value                     |
|-------|------|---------------------------|
| A     | @    | 185.199.108.153           |
| A     | @    | 185.199.109.153           |
| A     | @    | 185.199.110.153           |
| A     | @    | 185.199.111.153           |
| CNAME | www  | shubhampansare.github.io. |

After records resolve, set the custom domain in the site repo's Pages settings
and enable "Enforce HTTPS" once the certificate provisions.

## App deploy workflow (post-migration)

- Development continues in `Work/Padel/` and (later) the user's separate
  Storywalk folder. The Shoebum folder is never a dev workspace for the apps.
- Padelato: a small `deploy.sh` in the Padelato repo copies `index.html` to
  `../Shoebum/padelato/index.html`, then commits and pushes the site repo.
  `Padel/CLAUDE.md` release rules get a one-line update pointing releases at
  this script instead of the old Pages flow.
- Storywalk: same pattern once its local folder exists. For this migration the
  initial copy is taken from the current `Personal/hamming-path/` folder.

## Website design

Single `index.html`, hand-written, responsive, fast, accessible. Visual
direction: playful and personal - warm palette, copy with personality, small
tasteful micro-interactions. The "shoebum" name sets the tone. No frameworks.

Sections:

1. **Hero** - shoebum identity, one-line intro of who Shubham is and what he does.
2. **About** - interesting info, drafted strictly from facts already known
   (heads product at Tuul, ships AI apps, plays padel, dances, loves driving,
   adventure, science, food, and art). PUBLIC-CONTENT RULE (added 2026-08-28):
   never mention ADHD or any personal health information anywhere public.
   User corrects and extends on review; nothing invented.
3. **Live projects** - three cards with real screenshots and one-liners:
   - SendRishta -> sendrishta.com (matrimony biodata creator)
   - Padelato -> /padelato (free padel Americano/Mexicano tournament app)
   - Storywalk -> /storywalk (long reads played like a game, built for
     restless attention spans). Never referred to as "Hamming Path" anywhere
     on the site.
4. **Products I've worked on** - AI Angler, FishScore, PawsitivID, Travjoy.
   Logos/screenshots pulled from existing project folders where available.
5. **Contact** - GitHub + email; other links (LinkedIn/X) only if the user
   provides them.

## Migration sequence (high level)

1. Build the portfolio site locally in `Work/Shoebum/`; verify with Playwright
   screenshots at mobile (390 px) and desktop (1440 px) widths.
2. Rename GitHub repos: `hamming-path` -> `storywalk`,
   `padel-americano` -> `padelato`.
3. Copy app artifacts into `/padelato/` and `/storywalk/` in the site repo.
4. Create GitHub repo `shubhampansare.github.io`, push WITHOUT the CNAME file,
   enable Pages; smoke test at `https://shubhampansare.github.io`. (Pushing
   CNAME before DNS exists would redirect the smoke test to a dead domain.)
5. User adds the DNS records at Namecheap; once they resolve, set the custom
   domain in Pages settings (this adds the CNAME file) and enforce HTTPS.
6. Verify live: apex, www, `/padelato`, `/storywalk` all load correctly.
7. Flip `padelato` and `storywalk` repos to private, disable their
   Pages sites; verify old github.io URLs are dead.
8. Create the profile README repo and verify it renders on the profile.

## Verification criteria ("working" means)

- `curl -sI https://shoebum.io` returns 200 and the page contains the hero copy.
- `https://shoebum.io/padelato/` and `https://shoebum.io/storywalk/` return 200
  and each app is interactive in a real browser (Playwright click-through:
  Padelato reaches its setup screen, Storywalk starts and plays audio on
  interaction).
- `https://www.shoebum.io` resolves (redirects to apex or serves the site).
- Old URLs `shubhampansare.github.io/padel-americano` and `/hamming-path`
  return 404.
- `gh repo view` shows `padelato` and `storywalk` as private; the
  GitHub profile shows the profile README with working links.
- Playwright screenshots at 390 px and 1440 px reviewed before first push.

## Open items (resolved during implementation review)

- About section facts: user corrects the draft.
- Extra contact links: user provides if wanted.
