# matthewhand.github.io — Direction Briefing

*Consultant review, 26 Aug 2026. Repo `matthewhand/matthewhand.github.io` compared against the
live site at https://matthewhand.github.io and the WordPress blog at
matthewhand.mywebcommunity.org (read-only). No changes were made to either site.*

---

## 1. What this site actually is

**A real, current portfolio site — not a stub.** It is your public landing page: a
green-phosphor CRT-terminal themed showcase with a homepage, five project detail pages
(mcp-openapi-proxy, mcp-gateway, open-swarm, open-hivemind, chatty-commander), a themed 404,
favicons, an Open Graph share card, robots.txt and a sitemap.

Its history matters for the "does it need work?" question:

- **May 2024** — created as a genuine stub: one hand-written page with four bare repo links
  (gpt-terminal-plus, discord-llm-bot, perplexica-aws, chatty-commander).
- **May 2024 → June 2026** — completely dormant. Zero commits for 25 months.
- **19–21 June 2026** — fully rebuilt in 20 commits: the CRT redesign, all five project pages,
  then a systematic hardening pass (mobile overflow, WCAG AA contrast, reduced-motion,
  OG metadata, accessibility landmarks, 404, sitemap).

So the stub problem was already solved two months ago. Your GitHub profile's website field
points here, making this the canonical front door of your online identity.

One structural caveat: every page is a **self-unpacking React bundle** (~1–2 MB of
gzip+base64 inside a single HTML file, ~8 MB unpacked in the browser). It works — verified
below — but it means content edits are done by patching bundles programmatically, not by
editing readable HTML. The repo README documents this trap correctly. This is the site's main
long-term maintenance liability.

## 2. What is live vs what the repo claims

**Live and repo are in perfect sync.** Every deployable file (all 6 HTML pages, 404,
robots.txt, sitemap.xml, avatar, OG card) was SHA-256 compared against the live URLs:
**byte-identical, all HTTP 200**. GitHub Pages reports the latest build succeeded at the
current HEAD commit (`489cddb`, 21 June 2026), HTTPS is enforced, and there are no open PRs
or unpushed changes.

A live browser check confirmed the README's quality claims are accurate, not aspirational:
the homepage boots and renders all sections, **zero console errors and zero failed network
requests**, project navigation works both ways, and `/some-missing-page` serves the themed
404 rather than GitHub's default. Nothing in the repo is stale relative to production, and
nothing in production is missing from the repo.

## 3. Broken pages, stale content, missing identity

Nothing on the site itself is broken. The defects are in what the site *points at*:

**Broken outbound links (the one real defect).** Three of the five showcased projects have
no public GitHub repo — `github.com/matthewhand/mcp-gateway`, `.../open-hivemind`, and
`.../chatty-commander` all return 404 (private or deleted). Concretely:

- `chatty-commander.html` contains **five direct links to the 404ing GitHub repo**. A visitor
  who clicks through from your flagship-adjacent project hits a dead end.
- The homepage's project cards imply repos (`~/mcp-gateway`, `~/open-hivemind`,
  `~/chatty-commander`) that a visitor can't find on your GitHub profile.
- Softening factors: the cards themselves link to local detail pages (safe);
  `chatty-commander`, `open-swarm` and `open-hivemind` all exist on **PyPI** (the
  `pip install chatty-commander` instruction works even though its GitHub link doesn't); and
  open-hivemind's live demo at open-hivemind.vercel.app responds fine. The
  `mcp-gateway.your-host.dev` URL on the gateway page is a config *example*, not a broken link.

**Staleness.** Mild, not alarming: content is frozen at 20 June 2026 (~2 months). The footer
year is computed dynamically so it never dates itself. The blog has two posts (Feb 2025 and
June 2026) plus WordPress's default **"Sample Page" still published** — small, but it reads
as scaffolding. The blog also has **no working HTTPS** (free-host limitation); the site
correctly links to it via `http://`, but visitors get a "not secure" browser flag on
click-through.

**Identity.** Coherent and intentional: consistent terminal aesthetic, stylized pixel-art
avatar (no photograph), "independent builder · building with AI" positioning, a
plus-addressed contact email (`matthewhand.au+github@gmail.com` — good practice), and a
text-only OG card. The GitHub profile shows no public email and the name "mhand", so the
site is deliberately the richer identity surface. The main identity gap is the
**claim/evidence mismatch** above: the site presents five projects as your body of work, but
only two (mcp-openapi-proxy, open-swarm) are publicly verifiable on GitHub.

## 4. The three highest-leverage next moves

**Verdict: KEEP. Do not rebuild, do not redirect.** The site is two months old, technically
sound, on-brand, and in perfect deploy sync. A rebuild would burn effort re-solving solved
problems; redirecting the canonical `.github.io` URL to an http-only free WordPress host
would be a downgrade in both trust and permanence.

1. **Close the claim/evidence gap on the three dark projects.** For each of mcp-gateway,
   open-hivemind and chatty-commander, either make the repo public, or patch the site to
   stop implying a public repo — point chatty-commander's five dead GitHub links at PyPI
   (`pypi.org/project/chatty-commander`) and open-hivemind at its live Vercel demo, and mark
   closed-source projects as such ("closed source · available for contract work" actually
   *strengthens* the open-or-closed positioning). This is the single change that most affects
   a visitor's trust, and it's the only thing on the site that is actually broken.

2. **Shift effort from site-polish to content cadence.** The engineering is done; the
   leverage is now proof-of-life. The pattern from June worked well — ship a release, write
   it up on the blog (the mcp-openapi-proxy 0.2.0 post is genuinely strong), link it from the
   site. Repeat that per release, and delete the WordPress "Sample Page" while you're in
   there. A portfolio that shows a pulse every month or two outperforms any further visual
   refinement.

3. **Keep the hub-and-spoke, and protect the hub's editability.** github.io stays the
   canonical hub (portfolio + identity); WordPress stays the spoke (write-ups), linked from
   the footer as it is now. Two guard-rails: (a) treat the README's bundle-editing notes as
   law — never hand-edit the HTML, and re-apply injected fixes after any re-export from the
   design tool; (b) if the blog's http-only status starts to grate, solve it by moving the
   blog to a TLS-capable host or folding write-ups into this repo — not by redirecting the
   hub at the blog.

## 5. What must not be published (verified clean today)

Checked the working tree, the live site, and the full git history (every file ever committed):

- **No house/camera stills or personal photos.** The only raster images are the stylized
  pixel-art avatar, favicons, a text-only OG card, and screenshots *of the site itself* in
  `docs/screenshots/`. Nothing else has ever been committed.
- **No hex dumps or memory/diagnostic artifacts** anywhere in the published pages.
- **No secrets.** Pattern scan (OpenAI/AWS/GitHub/Slack key formats, private-key blocks)
  found nothing; the only credential-shaped text is a deliberate `${MCP_GATEWAY_TOKEN}`
  environment-variable placeholder in an example config, which is correct practice.
- **Standing rule going forward:** everything on `main` is served publicly at
  matthewhand.github.io — including `docs/` (this file, if merged). Never commit smart-home
  captures, real photographs, tokens, or infrastructure hostnames/IPs to this repo, even
  "temporarily"; Pages deploys on push and git history is forever.

---

*No redesign was implemented: the site is neither broken nor empty, so per the brief this
run delivers direction only.*
