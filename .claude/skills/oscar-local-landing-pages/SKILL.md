---
name: oscar-local-landing-pages
description: "Build any landing page for Oscar's businesses — EstateDocPrep, Mandy's Laundry, CheckHouseValueOnline, Offer On Your Terms, SellMyHouseForTheMostMoney, MedicareReviewPro, Oscar's Real Estate Team / Home Buyers, or any future site. Covers two layers — programmatic local SEO pages (city/county/state x topic, 'pages for every city', a site's local-pages n8n pipeline) and high-conversion pages (webinar signups, VSLs, lead-gen, ambassador recruiting, valuation funnels). Trigger on: local landing page, city page, county page, neighborhood page, landing page, funnel, signup page, or 'build the local pages'/'set up the pipeline' for a site. See references/real-estate-page-templates.md for real estate neighborhood pages, references/conversion-copy-framework.md for conversion-focused pages."
---

# Oscar's Local Landing Page Framework

Oscar has said this explicitly: he's tired of re-explaining what goes into a local landing page every time a new site gets built. This skill is the answer — read it once per build, don't ask him to repeat requirements it already answers.

## Before touching anything: confirm four things

These four facts differ per site and getting any of them wrong wastes real work. Check them BEFORE writing any content-generation code.

1. **Which SEO plugin does this site run — Yoast or Rank Math?** The meta title/description field names are completely different (`_yoast_wpseo_title`/`_yoast_wpseo_metadesc` vs `rank_math_title`/`rank_math_description`). Writing to the wrong fields silently does nothing. See `references/seo-field-mapping.md`.
2. **Does a WordPress Application Password credential already exist in n8n for this site?** Check `list_credentials` first. If not, that's the actual blocker — send Oscar the setup steps (site URL, WordPress admin username, and an Application Password from Users → Profile → Application Passwords in wp-admin; if Wordfence is active, it may need "Disable Application Passwords" unchecked first).
3. **What's the compliance profile for this vertical?** A living-trust site (EDP) has UPL/LDA rules (no attorney-implying language, no dollar-figure income promises). A real-estate site has Fair Housing rules. A lending/insurance site has its own disclosures. Check for a `*-compliance` skill for this business first — if one exists, it overrides anything generic in this skill.
4. **Is this a flat city-topic page or does it need the deeper County → City → Neighborhood real estate structure?** Most sites (EDP, Mandy's, CHVO, OYOT, SMHFTM) are flat: one page per city × topic. Real estate sites (Home Buyers / Oscar's Real Estate Team) go deeper — see `references/real-estate-page-templates.md`.

## Required elements on every local landing page

This list came directly from Oscar and has been the consistent requirement across every site built so far. Don't skip any of these without him explicitly saying to:

- **Hero image** — relevant to the city/topic, not generic stock
- **Google Maps embed** — the actual city/area, not the business address alone
- **Internal links — minimum 5 per page.** Oscar set this as a hard floor. Link to sibling city pages, related topic pages, and the site's core service pages.
- **External links to authoritative sources** — but these must be FIXED, pre-verified, real URLs (e.g. specific .gov pages), never AI-guessed. A hallucinated or broken external link is worse than no external link. Build a small fixed source-list per topic/vertical and reuse it, rather than asking Claude to invent citations live.
- **FAQs** — real, topic-specific questions, not filler
- **CTA buttons — typically 3.** The exact CTAs vary by business (e.g. EDP: free webinar signup, check for in-person session, a third slot Oscar assigns per campaign; real estate: schedule a showing, get a home valuation, contact an agent). Confirm the 3 CTAs for this specific business before generating pages — don't assume EDP's set applies elsewhere.
- **Bilingual: English AND Spanish**, as two separate pages/rows, not one page with both languages mixed. Applies to every site so far — ask if a new site is meant to be English-only before assuming otherwise.

## The pipeline architecture (reuse this, don't reinvent it)

Every site's local-pages system has been built as the same family of n8n workflows, named with the site's short code as a prefix (e.g. `EDP_01_Draft_Generator`, `CHVO_02_Publisher`). This pattern is proven — build new sites by cloning it, not from scratch:

1. **`<SITE>_01_Draft_Generator`** — reads the next unprocessed row from the queue sheet, generates the page content (Claude), creates a WordPress DRAFT, marks the row `Drafted`.
2. **`<SITE>_05_Publish_Approval`** — batches Drafted rows into a single Telegram message for Oscar to approve. This is the human-in-the-loop gate — nothing publishes without it unless Oscar has explicitly said a site can auto-publish.
3. **`<SITE>_02_Publisher`** — takes Approved rows and actually flips WordPress status to `publish`, marks the row `Published`.
4. **`<SITE>_04_Internal_Linking_Engine`** — goes back through published pages and wires up the 5+ internal links (this is usually a separate pass, not done at draft time, since it needs other pages to already exist).
5. **`<SITE>_06_Error_Handler`** — every one of the above workflows should be wired to this as its error workflow. Build it once per site, reuse everywhere.
6. **`<SITE>_07_Blog_Generator`** — separate from the local-pages queue; a daily evergreen blog post generator, same Draft→Approve→Publish pattern.

**The queue is a Google Sheet, not Airtable or n8n's Data Table.** This was a deliberate cost/simplicity decision — stick with it unless Oscar says otherwise. Standard queue columns: city, topic, language, status (Drafted/Approved/Published), plus whatever page-type-specific fields the vertical needs.

See `references/pipeline-architecture.md` for the exact node-by-node pattern, including bugs already found and fixed in production (read this before building — it will save you from re-introducing the same bugs).

## Two different jobs a landing page can do — pick the right framework

Not every page in this skill's scope is the same kind of page, and using the wrong framework for the wrong page produces a worse result:

- **Organic/SEO pages** (a city×topic page meant to rank in search) — use the "Required elements" checklist above. The job is search discovery; content depth and internal/external linking matter more than a tight persuasion sequence.
- **Conversion/funnel pages** (a webinar signup, a VSL, an ambassador recruiting page, a valuation-funnel lead page) — use `references/conversion-copy-framework.md`. The job is getting someone who's already on the page to take one specific action; headline psychology, social proof placement, and value stacking matter more than keyword depth.

A page can be both (a local SEO page that also needs to convert well once someone lands on it) — in that case, build the SEO structure as the skeleton and layer the conversion principles (stronger headline, social proof placement, real FAQ-as-objection-handling) on top, rather than trying to cram the full 13-step funnel sequence into a page whose main job is ranking.

## Content quality bar

A page that's just "insert city name into a template" reads as thin, spammy content and won't rank. Every page needs something that's actually true and specific to that city/topic — a real local detail, a real statistic, a real reason someone searching from that city would care. If Claude can't find anything genuinely specific to say, that's a signal to do more research before generating, not to publish filler.

## When Oscar sends competitor research (screenshots, pasted pages, URLs)

Fold new findings into `references/` rather than just using them once and forgetting them — that's the whole point of this being a skill instead of a one-off conversation. If a competitor's page structure reveals something not already captured here (like the notable-homes/notable-residents pattern found from RubyHome), add it to the relevant reference file so the next build benefits too.
