# Initial GTM Audit — Master Prompt

> Paste this entire file into Claude Code from a folder containing your `CLAUDE.md` (the business brief) and a `.env` (your API keys). Claude will run the audit end-to-end, produce a single consolidated report in markdown and Word formats, and save supporting data alongside.
>
> Built by Nishant Kapoor at EntireCommerce AI. Version 2, April 2026.

---

You are executing the Initial GTM Audit playbook for the brand whose `CLAUDE.md` sits in the current working directory. Follow these steps in order. Do not skip steps. Do not invent data. Honour the voice conventions in Step 8.

---

## Step 1. Read the business brief

Read `CLAUDE.md` in the current working directory. That file contains the brand's positioning, product, audience, competitors, pricing, tool stack, and goals. If any required field is blank or still contains placeholder text (anything inside `{curly braces}`), stop and ask the user to fill it in before proceeding.

Confirm you have at minimum:
- Brand name
- Primary URL
- What the brand sells (one sentence)
- Core buyer (one sentence)
- Three to five competitor URLs
- Ecommerce platform

Missing any of these? Ask the user. Do not guess.

---

## Step 2. Inventory tool access

Read the `.env` file in the current working directory. Print a credential inventory to the user in this format:

```
External tools (public data)
- DataForSEO: configured / not configured
- Serper: configured / not configured
- Keywords Everywhere: configured / not configured
- Ahrefs: configured / not configured
- Google PageSpeed Insights (GOOGLE_AI_KEY): configured / not configured

Internal tools (your brand's own data)
- Google Analytics 4: configured / not configured
- Google Search Console: configured / not configured
- Google Ads: configured / not configured
- Meta Ads: configured / not configured
- Google Merchant Center: configured / not configured
- Shopify Admin: configured / not configured
- Klaviyo / Brevo: configured / not configured
- Microsoft Clarity: configured / not configured
```

For each internal tool flagged "not configured", the corresponding section of the report will stub out honestly with a "grant access to populate" note. That is expected behaviour, not an error.

---

## Step 3. Detect the audit mode

Based on the `.env` inventory and the brand's data maturity (inferred from `CLAUDE.md` "Stage" field and any Shopify/GA4 history available), auto-detect the mode:

| Mode | Trigger |
|---|---|
| A1 | Established brand (3+ months trading), no internal tools configured |
| A2 | Established brand, 3+ internal tools configured |
| A2-thin | Established brand by calendar but fewer than 50 orders in last 90 days OR fewer than 20 GA4 `purchase` events |
| B1 | New / pre-launch brand, no internal tools |
| B2 | New / pre-launch brand, 3+ internal tools configured |

State the detected mode and ask the user to confirm or override. Record the mode in the report header.

---

## Step 4. Draft the ten sections

Produce all ten sections of the report in drafting mode. Do not write to disk yet. Each section is a self-contained methodology below.

### Section 1. Audience

Run the Audience Insights research prompt. This is mandatory on every run unless the operator has mined this exact audience in a prior engagement within the last six months (in which case link to the prior mining and proceed). See the inlined prompt at the end of Step 4 below (under "Audience Insights prompt").

Deliverable: a ≤500-word one-pager with nine sub-headings: Avatar, Core tension, What they tried and why it broke, Market gap, Recurring language, Three headlines, Top three objections and responses, Positioning angle, Belief shift. Plus a 15-quote verbatim appendix stored at `data/gtm-audit/{YYYY-MM-DD}/audience-voc-mining.md` and linked from the section.

### Section 2. Vision

Pull from `CLAUDE.md`. Capture: twelve-month revenue goal, positioning hypothesis, product origin, known constraints, the biggest-lever belief. If `CLAUDE.md` has these fields populated, assemble directly. If not, ask the founder a 30-minute interview's worth of questions to gather them.

When the founder's biggest-lever belief disagrees with the data surfaced in Section 10, flag the disagreement explicitly. That flag is the most valuable thing in the report.

### Section 3. Competitor Audit

For each of the 3 to 5 competitor URLs from `CLAUDE.md`, produce a full teardown with six subsections:

1. **Technical SEO.** Use WebFetch to scan the competitor's homepage. If DataForSEO is configured, run a light site-audit scan. Note Core Web Vitals if PageSpeed Insights is configured.
2. **Keyword footprint.** Top 100 ranking keywords, monthly organic traffic estimate, top landing pages. If DataForSEO is configured, pull `labs_ranked_keywords` per competitor domain. If not, WebSearch for "{competitor} ranks for" and synthesise from public SERPs.
3. **Content gap.** Keywords the competitor ranks for that the brand does not. **Do not use keyword-intersection mode** (known bug). Pull `labs_ranked_keywords` on the brand's domain and each competitor separately, then compute `competitor_keywords - brand_keywords` in Python. Cluster by intent.
4. **Meta Ad Library scan.** 30-day snapshot of active ads. **Do not use raw WebFetch against facebook.com/ads/library** (JS-rendered, returns empty). Preferred methods in order: Playwright headless-browser script, third-party Meta Ad Library indexes (adscan.ai), manual browser screenshots. If none available, note "Meta Ad Library: unable to verify; manual screenshot pass required" and add to the access-grant checklist.
5. **Google Ads intel.** If Serper is configured, pull paid SERP copy on the top 20 commercial queries for the category. Log headline patterns, description patterns, sitelink usage, offer language.
6. **Summary verdict.** Three things this competitor does better. Three things the brand can beat them on. Two paragraphs max.

After the per-competitor teardowns, produce a cross-competitor synthesis: whitespace map, shared weaknesses, pricing-and-positioning ladder, attribute-tuple gap analysis if the category is product-catalog (jewellery, apparel, accessories).

### Section 4. SEO

Four sub-sections:

- **4A Technical SEO and site health.** Crawl the brand's site. Meta tag audit, Core Web Vitals by template, schema markup validation, robots.txt + XML sitemap coverage, AI-bot allow-list check (must allow GPTBot, ChatGPT-User, PerplexityBot, ClaudeBot, anthropic-ai, Google-Extended, Bingbot), broken internal links, E-E-A-T signals.
- **4B Backlink and domain authority.** Serper brand-mention scan for unlinked mentions, DataForSEO Backlinks snapshot (referring domains, DR, new and lost links over 90 days). Competitor backlink comparison.
- **4C Keywords and content gaps.** Top 100 ranking keywords with 90-day trend. Lost keywords (dropped 10+ positions in 90 days). Content gap vs top 3 competitors (same set-difference workaround as Section 3.3). Branded vs non-branded traffic split via GSC if configured.
- **4D Opportunity patterns.** Check the GSC data (if configured) for each pattern: high impressions with low CTR, striking-distance queries (position 4-15), high impressions with zero clicks, accidental rankings, losing clicks over 90 days, brand query CTR below 50%. Each pattern has a specific action (see pattern table at the end of this step).

### Section 5. AEO (answer-engine optimisation)

- Test 10 to 20 commercial queries from the brand's category across ChatGPT, Claude, Perplexity, and Google AI Overview. Record: is the brand cited, which competitors are cited, does an AI Overview exist for this query.
- Apply the extractability checklist per priority page: clear definition in first paragraph, self-contained answer blocks, statistics with sources, comparison tables, FAQ section with natural-language questions, schema markup (FAQ, HowTo, Article, Product), expert attribution, updated within 6 months, heading structure matches query patterns, AI bots allowed in robots.txt.
- Apply the Princeton GEO visibility-boost table to the content backlog: cite sources (+40% visibility), add statistics (+37%), add quotations (+30%), authoritative tone (+25%), improve clarity (+20%), technical terms (+18%).
- Produce a whitespace map of commercial queries where the brand has no presence but 2+ competitors are cited.

### Section 6. Paid Ad Recommendations

- **If Meta Ads / Google Ads configured**: Account structure map, CTR/CPC/ROAS/frequency by campaign, creative fatigue scoring (Warning / Fatigued / Dead per creative), audience overlap analysis, Meta learning-phase diagnostic, Pixel and CAPI event health.
- **Always (external layer)**: Competitor creative intelligence from Section 3.4. 6-dimension analysis per competitor: hook patterns, messaging angles, ad formats, production style, CTA and offer strategy, creative volume and rotation. Whitespace map. "Steal this" list of 5 brief-ready ideas.
- **Starter campaigns (always produced)**: Three to five concrete campaigns the brand should run next, each with objective, audience, budget order-of-magnitude, creative brief, and expected KPI band. At least one campaign must be executable without internal access.

### Section 7. Email, Lifecycle, and CRM

Internal-heavy. If Klaviyo or Brevo is not configured, render the stub:

```
## 7. Email, Lifecycle, and CRM

Requires access. To populate this section, grant EntireCommerce access to your
Klaviyo or Brevo account. See the Access-grant checklist.

What you get once access is granted:
- Flow coverage audit (welcome, abandoned cart, browse, post-purchase, win-back)
- Revenue per flow, open rate, click rate, unsub rate
- Segmentation depth review
- Deliverability health (bounce rate, spam complaints, domain reputation)
- SMS layer check where applicable
- CRM hygiene: duplicates, missing attributes, mis-tagged records
```

If configured, run all six checks above against real data.

### Section 8. Conversion Optimisation

Internal-heavy with External fallback. Stub if Clarity not configured. Always run:
- Trust-signal audit on the live site (reviews, returns, shipping, payment icons, guarantees)
- Heuristic CRO review of PDP, category pages, checkout using WebFetch

If internal tools configured: session-recording review, rage-click and dead-click heatmaps, scroll-depth, funnel dropoff vs DTC benchmarks (PDP→ATC 5-10%, ATC→Checkout 40-60%, Checkout→Payment 70-85%, Payment→Order 90-95%), mobile-vs-desktop gap, top 5 exit pages.

### Section 9. Analytics and Tracking

Internal-heavy. Stub if GA4 not configured. Lightweight public check: view-source the homepage for pixel, GA, GTM snippets.

If configured: event coverage, conversion-tracking integrity (GA4 vs Shopify vs Meta vs ad platforms), UTM hygiene, cross-device stitching, attribution model review, dashboard readiness.

### Section 10. Prioritised Action Items

Convert every finding from Sections 3 through 9 into a candidate initiative. Score each on ICE:

- Impact (1-10): expected revenue or strategic weight
- Confidence (1-10): how sure the initiative works
- Effort (1-10, inverse): lower effort = higher score

Multiply for ICE score, rank descending, group into three buckets:

- **This week** (top 5 items). The work that starts immediately.
- **Next 30 days** (next 10 items). Pulled up as "This week" items complete.
- **Backlog** (everything else).

**Do not label as P0/P1/P2 in the output.** Use the bucket names only.

Open Section 10 with "The biggest lever":
- One sentence naming the single biggest lever holding revenue back right now.
- Three to five bullets of evidence pulling the strongest data points from Sections 3 through 9. Tag each as `[E]` (external data) or `[I]` (internal data).
- One or two sentences on the counter-move.
- Expected revenue impact, quantified with assumptions stated.
- Confidence: high / medium / low with a reason.

On Path B (new brand), replace "The biggest lever" with "The critical path hypothesis" framed as a testable experiment with a kill criterion.

---

### Audience Insights prompt (inlined for Section 1)

```
## ROLE
You are a senior DTC audience researcher. Mine public conversations for
authentic customer language. Compress findings into a one-page brief a
founder reviews in under 5 minutes and approves or redirects section by section.

## INPUTS (required — refuse if any missing)
1. Target audience (1 line)
2. Their #1 struggle (1 line)
3. What the brand sells and how it helps (1 line)

If any input is missing, return: "Unable to proceed without audience, struggle, and product."

## RESEARCH METHOD
- Sources: Reddit, Indie Hackers, Hacker News, X/Twitter long-form, Quora,
  YouTube comments, niche forums, blog comments. Prioritise long-form
  first-person accounts.
- Recency: last 12-24 months. Flag anything older.
- Collect 15 verbatim quotes that reveal: direct experience of the problem,
  emotional charge, specific solutions tried, exact phrases used, moments of
  realisation.
- For each quote log: platform, approx date, primary emotion, any phrase that
  recurs across multiple quotes.

## DELIVERABLE — THE ONE-PAGER
Keep the front page under 500 words.

1. Avatar (≤50 words). Who they are, daily reality, what sits on their mind.
2. Core tension (2 sentences). Surface pain they'd name, then the deeper
   psychological conflict driving behaviour. Both trace to quotes.
3. What they've tried and why it broke. 3-row table: Tried / Why it appealed
   / Why it failed.
4. Market gap (1 sentence). What existing solutions haven't delivered.
5. Recurring language (5-7 phrases). Exact words and short phrases that showed
   up across multiple quotes. Copywriting raw material.
6. Headlines (3). ≤15 words each. Must use language from section 5.
7. Top 3 objections and responses. Each objection quoted or paraphrased from
   research. Each response ≤25 words.
8. Positioning angle (1 sentence). How the brand uniquely answers the core
   tension.
9. Belief shift (1 sentence). "Many believe [old frame]. The evidence shows
   [new frame]." The mental-model update this audience needs to make before
   buying.

## APPENDIX — VERBATIM QUOTES (15)
Format: `#N "[verbatim]" — [platform], [date] — [primary emotion] — [recurring phrase if any]`
Save to data/gtm-audit/{YYYY-MM-DD}/audience-voc-mining.md

## RULES
- Every claim traces to a quote in the appendix. No speculation.
- No invented statistics or fabricated sources. If a platform search yields
  nothing, reduce quote count and say so.
- Use the audience's register in headlines and objection responses.
- Spelling: "ecommerce" one word.
- No em dashes. No AI clichés. No "X, not Y" contrasts. State what something IS.
```

---

### Opportunity-patterns reference (for Section 4D)

| Pattern | Threshold | Action |
|---|---|---|
| High impressions, low CTR | ≥100 impressions + CTR below 1% + position 1-10 | Rewrite title and meta |
| Striking distance | Position 4-15 + impressions ≥50 | Strengthen H1/H2, add direct-answer paragraph, expand thin sections, add internal links |
| High impressions, zero clicks | ≥200 impressions + 0 clicks + position >10 | Create a dedicated page if none exists |
| Accidental rankings | Query maps to homepage or unrelated post | Create dedicated page, link from accidental ranker |
| Losing clicks over 90 days | Drop of 30%+ on a query that was a top-10 click earner | Check SERP for AI Overview or featured snippet capture, re-optimise |
| Brand query CTR below 50% | Query contains brand name, CTR below 50% | Launch Brand Defence Google Ads campaign, tighten homepage title |

---

## Step 5. Synthesis pass (mandatory before writing to disk)

Do not write the report yet. Assemble the complete draft of all ten sections as a single document in your working context and read it end-to-end in one pass. Ask:

- What pattern emerges across three or more sections that no single section captures?
- What does a finding in Section X imply for recommendations in Section Y? (Audience language in Section 1 the Section 4 SEO cluster ignores. Competitor whitespace in Section 3 the Section 6 paid recs miss. Section 5 AEO gap that Section 4 content plan could fill.)
- Is there a strategic narrative connecting the data that the Executive Summary placeholder misses?
- Does any Section 10 action address multiple section findings at once? Promote it.
- Does any high-ranked Section 10 action solve only one problem? Consider demoting in favour of multi-leverage plays.

Produce 3 to 5 cross-cutting insight bullets. Each bullet:
- States the insight in one sentence.
- Names the sections it spans in brackets at the end.
- Is a synthesis, not a summary.

Then revise:
- **Executive Summary** to lead with the narrative these insights reveal (3-4 sentences total).
- **Section 10** to re-rank actions that hit multiple leverage points.
- Write the bullets into the **Cross-cutting themes** block immediately after the Executive Summary.
- Generate the **Key findings** block (5-7 one-sentence bullets, one per major section).
- Generate the **Key action items** block (top 5 from Section 10 pulled to the front with a one-line "why this first" tied back to cross-cutting themes).
- Draft the **Conclusion** block at the end (2-3 short paragraphs: what to do this week, what a paid engagement looks like if the founder wants this run for them, book-a-call CTA at https://entirecommerce.ai/book-a-call).

---

## Step 6. Voice enforcement gate (mandatory before writing to disk)

Before writing the file, run a mechanical voice-scan on the full draft. Grep for each banned pattern and rewrite every match:

| Banned | Grep for | Fix |
|---|---|---|
| Em-dash | `—` (U+2014) or `--` used as em-dash | Replace with period, comma, or colon |
| Negative parallelism | `, not `, ` rather than `, ` instead of `, `opposite of `, `not only ` | Rewrite to state the positive claim alone |
| Banned spelling | `e-commerce`, `E-commerce`, `E-Commerce` | Replace with `ecommerce` |
| AI clichés | `Here's the`, `Here's what`, `Here's why`, `Most people`, `The uncomfortable truth`, `The brutal truth`, `The breakthrough` | Rewrite without the cliché frame |
| Sub-four-word sentence | Sentences of 1 to 3 words standing alone | Merge into neighbour or expand |

`not` is permitted only when the negation is genuinely load-bearing and cannot be rewritten.

Shipping a report with more than zero banned-pattern matches is a spec violation. If you are running any sub-agents (for competitor teardowns, audience mining, etc.) they must run the same voice-scan on their returned output before you merge.

---

## Step 7. Write outputs

Report structure (fixed order):

```
# GTM Audit: {Brand Name} ({YYYY-MM-DD})

Mode: {A1 / A2 / A2-thin / B1 / B2}
Access level: {External only / External + Internal}

## Executive summary
(3-4 sentences leading with the narrative)

## Cross-cutting themes
(3-5 synthesis bullets from Step 5)

## Key findings
(5-7 one-line bullets across sections)

## Key action items (top 5 this week)
(top 5 from Section 10 pulled to the front with "why this first")

## 1. Audience
## 2. Vision
## 3. Competitor Audit
## 4. SEO
## 5. AEO
## 6. Paid Ad Recommendations
## 7. Email, Lifecycle, and CRM
## 8. Conversion Optimisation
## 9. Analytics and Tracking
## 10. Prioritised Action Items

## Access-grant checklist
(Only appears if any Internal-layer section is stubbed)

## Conclusion
(2-3 paragraphs)
```

Write the file to:

```
{cwd}/docs/gtm-audit/{YYYY-MM-DD}/gtm-audit-report.md
```

Then convert to Word doc:

```bash
pandoc {cwd}/docs/gtm-audit/{YYYY-MM-DD}/gtm-audit-report.md \
  -o {cwd}/docs/gtm-audit/{YYYY-MM-DD}/gtm-audit-report.docx \
  --toc --number-sections
```

If pandoc is not installed, tell the user: `brew install pandoc` (Mac) or equivalent. Do not skip the Word doc silently.

Save supporting data (JSON pulls, CSV exports, per-competitor crawl files, the 15-quote audience-voc-mining.md appendix) to:

```
{cwd}/data/gtm-audit/{YYYY-MM-DD}/
```

**Exactly one consolidated report file at the primary output path.** Do not produce per-section files. If sub-agents returned per-section drafts, merge them into the single file before writing.

---

## Step 8. Merge into actions.md

If the current working directory has an `actions.md` file, merge the five items from "Key action items (this week)" into the Next Actions section. Respect the existing priority caps (P0 max 5, P1 max 10, P2 max 15). If a tier is over cap, rescore by ICE and demote the weakest before adding.

---

## Step 9. Commit (optional)

If the folder is a git repo, commit the report, Word doc, data files, and updated actions.md. Commit message format:

```
GTM audit: {Brand Name} ({YYYY-MM-DD}) — {biggest lever in 6 words}
```

---

## Voice conventions (strict, applied throughout)

- No em-dashes. Use period, comma, or colon.
- No negative parallelisms. Never "X, not Y". State the positive.
- Four-word sentence floor. No one- or two-word sentences.
- Spelling: "ecommerce" one word.
- No AI clichés.
- Every finding traces to real data. No fabricated numbers. No invented sources.
- If a section is stubbed for missing access, state it plainly with the access path. Never pad with generic recommendations.

---

## Acceptance criteria

- End-to-end from this one prompt. No manual copy-paste between steps.
- Correct mode detection with user confirmation.
- Single consolidated report at the output path. Cross-cutting themes, Key findings, Key action items, Conclusion blocks populated by the synthesis pass.
- Word doc produced alongside the markdown via pandoc.
- No invented data. Missing internal tools stubbed honestly with access-grant path.
- Voice enforcement gate run before write. Zero banned-pattern matches in final output.

---

## What to do with the output

The top 5 items in "Key action items" go to work this week. The "Biggest lever" in Section 10 is your focus until the metric it targets moves. Then re-run the audit (quarterly is enough unless there's been a material shift) and the next biggest lever reveals itself.

Want this run for you on a live engagement with a weekly review cadence, a daily dashboard, and the thirty-plus downstream playbooks across every GTM function? Book a call at https://entirecommerce.ai/book-a-call.
