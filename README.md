**[Want us to run this for you? Book a call →](https://entirecommerce.ai/audit)**

# Initial GTM Audit Playbook

## A full marketing diagnostic you can run on your own brand in 20 to 40 minutes inside Claude Code. Produces one consolidated report covering audience, competitors, SEO, AEO, paid ads, email, CRO, analytics, and a ranked action list. Delivered as a Word doc plus the markdown source. Built by Nishant Kapoor at EntireCommerce AI.

---

## Who This Playbook Is For

This playbook is for three people.

**The founder-led DTC brand owner on Shopify** who's been flying blind. You're growing off a product that's clearly working, but you don't have a clear picture of where the binding constraint is right now. You've tried an agency, you've tried freelancers, you've tried doing it yourself. You want a diagnostic that tells you, plainly, in one document, what the single biggest thing holding revenue back is and what to do about it this week.

**The fractional CMO managing multiple DTC clients.** You need consistent output across brands and a repeatable diagnostic you can run in week one of every engagement. Your problem is consistency: every client gets a slightly different audit depending on which of your team members produces it. This playbook is the same process every time, with the same ten sections, the same voice, and the same action-ranking logic. Your team reads from one script.

**The in-house growth lead at a funded DTC brand.** You have GA4, you have Klaviyo, you have Meta Ads data. You don't have a single place where all of it gets pulled together with a cross-cutting narrative. You want the deliverable without paying £5,000 to an agency for a deck that takes three weeks. You want it in your GitHub, version-controlled, so next quarter you can re-run it and see what moved.

If you've been piecing together a marketing audit by hand, screenshotting Meta Ads Manager, copy-pasting search terms from Search Console, writing positioning frameworks in a Notion doc, this playbook collapses all of that into one consolidated output in a single Claude Code session.

---

## What The GTM Audit Actually Is

**The short version.** Ten sections in one consolidated report. Claude Code runs the diagnostic end-to-end: mines your audience, tears down your competitors, maps your SEO and AEO surface, scores your creative, audits your email and analytics (where access is granted), and hands back a ranked action list with the top five items to ship this week. Plus a dedicated synthesis pass that reads the whole draft end-to-end and surfaces three to five cross-cutting themes, the strategic narrative your business reveals when every finding is in view at once.

**Best for.** Premium DTC brands. AOV £500+ or 2x category median. Founder-led. US, UK, or English-speaking European markets. Established brands with three or more months of trading history, or pre-launch brands getting ready to ship.

**How it's different.** Three things most marketing audits skip.

First, the **synthesis pass**. Section-by-section analysis gives you a directory of findings. The synthesis pass gives you the story. A competitor whitespace in Section 3 might imply a positioning change in Section 1 which unlocks an SEO content cluster in Section 4 which compounds into AEO authority in Section 5. None of those connections surface from a section-at-a-time read. Claude does the cross-read explicitly, then revises the Executive Summary to lead with the narrative.

Second, **honest stubbing**. Sections 7, 8, and 9 require access to your Klaviyo, Clarity, and GA4. Without that access, they render with a clear access-grant path (what to share, with which service account, to unlock the section). No fabricated numbers. No filler paragraphs pretending to be findings.

Third, the **output format**. One Word doc you can share with your team, plus the markdown source in your GitHub repo. Version-controlled. Re-runnable quarterly. Integrated with your `actions.md` so the top five "this week" items merge straight into your team's operating plan.

**Skip this playbook if** you're a service business, a B2B SaaS, or a brand selling at the mass-market tier. The audit is tuned for premium DTC and the recommendations will be directionally off for other contexts.

---

## Setup (15 Minutes)

### Prerequisites

Before you start, make sure you have:

- **Claude Code** installed. Download from [claude.com/claude-code](https://claude.com/claude-code). Verify with `claude --version`.
- **pandoc** installed for the Word-doc export. On macOS: `brew install pandoc`. On Windows: [pandoc.org/installing.html](https://pandoc.org/installing.html). Verify with `pandoc --version`.
- **A project folder** on your machine. One per brand you audit. Name it after the brand.

### Step 1: Create the project folder

```bash
mkdir my-brand-audit && cd my-brand-audit
```

Drop the three files from this bundle (CLAUDE.md, gtm-audit.md, README.md) into the folder.

### Step 2: Set up your .env

Create a `.env` file in the project folder. Add the API keys you have. Minimum recommended:

```
DATAFORSEO_LOGIN=your_login
DATAFORSEO_PASSWORD=your_password
DATAFORSEO_AUTH_BASE64=your_base64_auth
SERPER_API_KEY=your_key
KEYWORDS_EVERYWHERE_API_KEY=your_key
GOOGLE_AI_KEY=your_google_cloud_api_key_for_pagespeed
AHREFS_API_KEY=your_key_if_you_have_one
```

**Gotcha I hit:** PageSpeed Insights quota runs out fast without an API key. Generate one free at [console.cloud.google.com](https://console.cloud.google.com) under Credentials, enable the PageSpeed Insights API, and add it as `GOOGLE_AI_KEY`. The unauthenticated tier exhausts within the first handful of pages on any serious audit.

**Gotcha #2:** Never commit your `.env` to a public repo. Add `.env` to your `.gitignore` before your first commit.

### Step 3: Populate CLAUDE.md (or let Claude interview you)

You have two options:

**Option A (recommended, fastest):** Leave `CLAUDE.md` as-is. When you run the master prompt in Step 5, Claude will interview you conversationally: brand overview, product, audience, positioning, tool stack, constraints, goals. Claude writes the file for you progressively as you answer. Takes about 10 minutes.

**Option B (DIY):** Open `CLAUDE.md` and replace every `{placeholder}` with your brand's specifics yourself before running the audit. Founder voice, one or two sentences per field.

Either option produces the same end state: a populated `CLAUDE.md` that grounds every playbook you run from this folder.

**Gotcha #3:** However you populate it, the three-to-five competitor URLs are load-bearing. The audit auto-pulls competitor keyword data via DataForSEO in Section 3. Without competitor URLs Claude has to auto-discover them from the SERPs, which usually works but your hand-picked list is always sharper.

### Step 4: Open Claude Code in the folder

```bash
claude
```

Claude Code loads `CLAUDE.md` automatically on session start. Confirm you see "Loaded CLAUDE.md" in the session log.

---

## The Three Files in This Bundle

### File 1: CLAUDE.md (business-briefing template)

**Path:** `{your-project-folder}/CLAUDE.md`
**What it does:** Claude Code loads this on every session start. It's where your brand's specifics live: product, audience, positioning, pricing, catalog, tool stack, constraints, and goals. Every playbook you run from this folder reads from here first.
**How to use it:** Replace every `{placeholder}` with your specifics. Keep it tight, one to two sentences per field beats a paragraph. Founder voice beats marketing copy.

### File 2: gtm-audit.md (the master prompt Claude runs)

**Path:** `{your-project-folder}/gtm-audit.md`
**What it does:** The prompt Claude executes end-to-end to produce the audit. Covers all ten sections plus the synthesis pass plus the voice-enforcement gate plus the pandoc Word-doc export.
**How to use it:** Paste its entire contents into Claude Code and hit enter. Don't edit it unless you know what you're doing, the structure is load-bearing.

### File 3: README.md

**Path:** `{your-project-folder}/README.md`
**What it does:** You're reading it. Setup, run, and troubleshooting reference. Safe to ignore once you've run the audit once.

---

## Your First Run

Five steps. Twenty to forty minutes.

1. **Open Claude Code in your project folder.** `cd my-brand-audit && claude`
2. **Paste the full contents of `gtm-audit.md` into the terminal.** Hit enter.
3. **Claude prints a credential inventory.** You'll see which External tools (public-data APIs) are wired and which Internal tools (your GA4, Shopify, Klaviyo) are missing. Internal tools can stay missing; sections 7-9 will stub honestly.
4. **Claude auto-detects the mode** (A1, A2, A2-thin, B1, or B2) based on your data maturity and access level. Confirm or override.
5. **Claude runs the audit.** Mines your audience via WebSearch, pulls keyword data via DataForSEO, tears down your competitors, checks your AEO presence, runs the synthesis pass, runs the voice gate, writes the report.

Output lands at:

```
{your-project-folder}/docs/gtm-audit/YYYY-MM-DD/gtm-audit-report.md
{your-project-folder}/docs/gtm-audit/YYYY-MM-DD/gtm-audit-report.docx
```

Plus supporting evidence (competitor keyword pulls, AEO SERP baseline, audience quote appendix) in `{your-project-folder}/data/gtm-audit/YYYY-MM-DD/`.

---

## What You'll Get

Ten high-value deliverables in one consolidated report.

1. **Executive summary with cross-cutting themes.** A 3-4 sentence narrative plus 3-5 strategic insights spanning multiple sections. Produced by a dedicated synthesis pass that reads the whole draft end-to-end. This is where the story of your business emerges as a strategic narrative threading through every finding.
2. **Key findings and top five action items at the top.** 5-7 skim-readable bullets and the top five actions to ship this week, front-loaded for 90-second reading.
3. **Audience one-pager.** Avatar, core tension, 5-7 recurring phrases, 3 headline options, top 3 objections with responses, positioning angle, and the belief shift your buyer must make. Built from 15 verbatim customer quotes mined from Reddit, X, Quora, YouTube, and niche forums.
4. **Founder vision capture.** 12-month revenue goal, positioning hypothesis, product origin, known constraints, and the single lever the founder believes will unlock growth. Cross-checked against the data in Section 10.
5. **Competitor teardown.** Full profiles of 3 to 5 competitors: technical SEO, top-100 ranking keywords, content gaps, 30-day Meta Ad Library scan, paid-SERP intel. Cross-competitor whitespace map.
6. **SEO opportunity map.** Technical site health, keyword footprint, content gaps, backlink profile, and opportunity patterns (striking-distance queries, low-CTR pages, cannibalisation). Ranked by estimated revenue impact.
7. **AEO presence audit.** Answer-engine citations across ChatGPT, Claude, Perplexity, and Google AI Overview on 10 to 20 commercial queries. Extractability checklist per priority page. Princeton GEO visibility-boost plays.
8. **Paid ad recommendations.** Creative fatigue scoring on active ads, competitor creative intelligence (hooks, angles, formats, rotation), and 3 to 5 starter campaigns with objective, audience, budget, and creative brief.
9. **Email, CRO, and Analytics diagnostics.** Flow coverage and revenue per flow, funnel dropoff versus DTC benchmarks, rage-click heatmaps, event coverage, attribution-model review. Populates where internal access is granted; stubs honestly with an access path otherwise.
10. **Prioritised action list and conclusion.** Every finding scored on Impact × Confidence × Effort and ranked into "This week", "Next 30 days", and "Backlog" buckets. Followed by a conclusion with next steps and an optional paid-engagement path.

**Format.** One consolidated Word doc (shareable with your team) plus the markdown source (version-controlled in your GitHub repo). Typically 4,000 to 8,000 words. Reading time around 25 minutes cover to cover, 90 seconds to skim via the Key Findings block at the top.

---

## Modes

The audit auto-detects which mode applies to your brand and adapts section depth accordingly. You don't set this manually.

| Mode | When it applies | What changes |
|---|---|---|
| A1 | Established brand, no internal-tool access shared | External-only analysis. Full competitor, SEO, AEO depth. Sections 7-9 stub. |
| A2 | Established brand, 3+ internal tools configured | Full audit. Everything populates. Monthly correlation analysis possible. |
| A2-thin | Established by calendar but fewer than 50 orders in the last 90 days | Same shape as A2 but framing shifts from performance teardown to targeting and readiness. |
| B1 | New or pre-launch brand, no internal access | Audience, vision, competitor intel carry the weight. Growth map is structural, built from public signals and qualitative analysis. |
| B2 | New or pre-launch, 3+ internal tools configured | Same as B1 plus instrumentation verification and pre-wired dashboard. |

---

## Honest Caveats

**The synthesis pass depends on Claude holding the whole draft in context simultaneously.** On very large reports (above 8,000 words), the cross-cutting read can lose depth. If the Cross-cutting Themes block feels thin, rerun the audit with three competitors to shrink the total report size.

**Meta Ad Library WebFetch fails more often than it succeeds.** The Meta Ad Library page is JS-rendered and WebFetch returns empty on most runs. The spec includes a fallback chain (Playwright, third-party ad-library indexes, manual screenshots) but the manual-screenshot path requires your time. Expect Section 3.4 to sometimes stub with "Meta Ad Library unavailable via WebFetch; manual screenshot pass required."

**Voice rules are strictly enforced.** Claude runs a mechanical grep-pass for em-dashes, negative parallelisms ("X, not Y"), banned spelling ("e-commerce"), and AI clichés ("Here's the...") before writing the report to disk. This catches most style drift but isn't foolproof. If you spot a banned pattern in the output, flag it and rerun the voice gate.

**Internal-tool access is the single biggest lever for audit depth.** Sections 7, 8, and 9 are legitimate stubs in A1 mode but they unlock real findings only when you share Klaviyo, Microsoft Clarity, GA4, and Shopify access. Plan on spending 30 to 45 minutes after the first run wiring those up if you want the full audit on the next pass.

**API costs are real but small.** A typical full run costs under $10 in DataForSEO credits plus a few cents of Serper. Budget $15 per run to be safe. Keywords Everywhere has a generous free tier and Ahrefs is optional. PageSpeed Insights is free under quota with a valid `GOOGLE_AI_KEY`.

**The playbook is tuned for premium DTC and will miss on edge cases.** If you sell B2B SaaS, run a service business, or target the mass market, the audit's recommendations will be directionally off. The methodology (competitor audit, SEO gap, AEO presence) still applies; the DTC-specific benchmarks (AOV thresholds, Meta creative formats, Shopify-specific checks) won't.

---

## Quick-Start Checklist

Work top-to-bottom. Items you'd run first are ordered first.

- [ ] Claude Code installed (`claude --version` works)
- [ ] pandoc installed (`pandoc --version` works)
- [ ] Project folder created for the first brand you want to audit
- [ ] CLAUDE.md from this bundle copied into the project folder
- [ ] gtm-audit.md from this bundle copied into the project folder
- [ ] DataForSEO account created and credentials added to `.env` (highest-leverage API)
- [ ] Serper API key added to `.env`
- [ ] Keywords Everywhere API key added to `.env` (free tier is fine)
- [ ] Google Cloud API key created and added to `.env` as `GOOGLE_AI_KEY` (for PageSpeed Insights)
- [ ] CLAUDE.md fully filled in, every placeholder replaced
- [ ] First run executed and Word doc produced at `docs/gtm-audit/{date}/`
- [ ] Report reviewed, top 5 "This week" action items identified
- [ ] Sections 7, 8, 9 stubs reviewed, decide whether to share internal access for the next run
- [ ] Re-run quarterly to track whether the biggest lever has moved
- [ ] Book a call if you'd rather we run this for you on a live engagement: [entirecommerce.ai/audit](https://entirecommerce.ai/audit)

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| "pandoc not found" at Word-doc export | pandoc not installed | `brew install pandoc` (macOS) or [pandoc.org/installing.html](https://pandoc.org/installing.html) |
| Section 4 (SEO) returns an empty gap table | `seo_toolkit.py keyword-research` gap-mode bug | Known. Playbook includes a workaround (compute set-difference manually in Python). Your run should handle this automatically. |
| Meta Ad Library scan fails | WebFetch against JS-rendered page returns empty | Use the fallback chain in the spec: Playwright → third-party index → manual screenshots |
| Report splits into per-section files instead of one consolidated file | Sub-agent split issue, confirmed on one dogfood run | Rerun. The spec explicitly bans per-section files; a clean run should consolidate. |
| Voice gate flags em-dashes or "X, not Y" patterns in output | Style drift in sub-agent output | Paste the banned-patterns list back into Claude and ask it to scan-and-fix the full file |
| Sections 7-9 stub even though you have Klaviyo/Clarity/GA4 | API keys missing from `.env` | Check `grep -iE 'klaviyo|clarity|ga4|shopify' .env`, if empty, add them and rerun |

---

## Credit

Built by Nishant Kapoor at EntireCommerce AI.

- **LinkedIn**: [linkedin.com/in/nishantkapoor1](https://www.linkedin.com/in/nishantkapoor1)
- **Email**: nishant@entirecommerce.co
- **Book a call**: [entirecommerce.ai/audit](https://entirecommerce.ai/audit)
- **Full playbook library** (weekly review, SEO audit, CRO review, competitor creative audit, and thirty-plus more): [entirecommerce.ai/playbooks](https://entirecommerce.ai/playbooks)

Use this playbook freely. Share it with other DTC founders. If you ship a case study running it on your brand, tag us. We'll link to it from the site.
