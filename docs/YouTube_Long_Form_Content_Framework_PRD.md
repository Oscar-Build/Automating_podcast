# YouTube Factory — Product Requirements Document

**Official name:** YouTube Factory (repo: `youtube-longform-factory`)
**Related system:** BRF (B-Roll Factory) — the pantry feeding this factory its cutaway clips.

## 1. What This Is

An automated, daily podcast-style YouTube video factory. Pipeline:

```
Topic doc → NotebookLM (two-host AI audio) → Gamma API (slides) → FFmpeg (video assembly) → YouTube Data API (upload)
```

Shorts are cut from each long-form episode and drive traffic back to the long-form video.

## 2. Why

- **No key-person risk.** Content is brand-first (EstateDocPrep, not Oscar personally). Two AI hosts carry the show identity so the business — not one person — gets known.
- **6-month goal:** a YouTube lead machine feeding California estate planning seminars.

## 3. Channels

**12 channels planned at launch, now 13** (6 topics × EN/ES, plus Mortgage Protection EN/ES added later):

- EstateDocPrep living trusts (EN/ES)
- Real Estate — home buyers (EN/ES)
- Real Estate — home sellers (EN/ES)
- CashOffers (EN/ES)
- Final expense (EN/ES)
- Annuities (EN/ES)
- Mortgage Protection (EN/ES)

Channels are fully separate audiences/branding and are **never cross-promoted**. All run on one shared backend factory.

### Scaling architecture: one engine, N channels

Each channel is a single YAML config: cadence, theme wheel, prompts, links, compliance profile, Railway env prefix, social handles. Adding a new channel is a config-and-credentials job, not a new build.

## 4. Cadence

| Channel | Frequency |
|---|---|
| EDP (EN + ES) | Daily (7/week) |
| CashOffers | 1/week (one strong educational episode) |
| Buyers | 2/week |
| Sellers | 2/week |
| Annuities | 2/week |
| Final expense | 2–3/week |
| Other channels (initial) | 3/week |
| Non-EDP channels | mini theme wheels ("good/bad/ugly") built by the Brainstormer Agent |

**Breaking-news interrupt format:** a trending topic can bump the scheduled episode to the following week, max 1x/week.

## 5. EDP 7-Day Theme Wheel (approved)

Reused natively for the Spanish channel (same topics, not translated verbatim).

| Day | Theme |
|---|---|
| Mon | Famous & Regretful — celebrities who died without a trust |
| Tue | Famous & Protected |
| Wed | Everyday Probate Story |
| Thu | Watch-Outs |
| Fri | Real Questions |
| Sat | How It Works |
| Sun | Family Feuds |

## 6. Show Identity

**Approved line:**
> "The EstateDocPrep.com Podcast — helping you create a comprehensive living trust at a fraction of the cost of a law firm."

**Compliance-safe expanded identity:**
> "EstateDocPrep is living trust software backed by our Registered & Bonded LDA company in California — not attorneys, no legal advice, documents prepared at your direction."

**AI hosts:** Marco & Veronica (same names, both EN and ES shows).

**CTA / lead magnet:** create a free account in the EDP software. Episodes promote the next day's topic and a website page listing the weekly topic schedule.

## 7. Pricing Anchor (updated July 2026)

Comprehensive attorney-drafted living trusts in CA now run **$5,000–$15,000**:
- Attorneys start around $5,000
- ~$695 per additional funding account
- $695–$1,295 per add-on

EDP does not charge per funding account. *Note: the edp-compliance rulebook's older $3,000–$5,000 anchor is outdated and needs updating — Kiss is saving substantiation screenshots.*

## 8. Content Facts to Use

- HIPAA waivers expire
- Healthcare directives — hospitals prefer them under ~3 years old
- Powers of attorney become stale/unworkable over time
- All of the above position the EDP software as the way to keep documents updated

## 9. Visual Layer

Documentary style: slide backbone + b-roll cutaways every 20–30 seconds, sourced from the BRF clip bank + Pexels.

- **No avatar faces** for Marco & Veronica in v1 (cost, consistency, and uncanny-valley risk)
- Avatars are a **Phase 3 A/B test only**

## 10. Research & Topic Bank

- **Research Agent** pulls from Google Trends, keyword data, current events/news, and living-trust legislation to feed topics
- **Goal:** a 365-topic bank — roughly 100 "famous without a trust," 100 "famous with a trust," plus other themes

## 11. Community Q&A Engine

- Agent harvests YouTube comments into a Supabase `faq_bank`
- Each episode ends with a 3–4 minute segment answering 2–4 viewer questions (e.g., "[handle] from [area] asked...")
- Questions generalized to comply with LDA rules (no specific legal advice to a named individual)
- Questions asked 5+ times graduate to full episodes

## 12. Humanize Layer

- Comment replies
- Community polls
- Cultural calendar episodes
- Seminar locator CTA, fed from the GHL calendar

## 13. Syndication

**Rule:** all rails must be live for EDP EN+ES before Wave 2 (other channels) begins.

- YouTube
- Facebook (full video + Reels via Meta Graph API)
- Instagram Reels
- TikTok
- Podcast RSS — via Spotify for Creators → Spotify / Apple / Amazon / iHeart
- Transcript SEO pages
- FAQ page
- GHL weekly digest
- Google Business Profile posts

## 14. Description Template

Optimized description template includes a free-resources block and UTM-tagged links via a per-channel `links.json`.

## 15. Monetization Ladder (approved order)

1. Leads (primary goal)
2. YouTube Partner Program ad revenue
3. $129/yr update-plan promos on document-expiration episodes
4. Ambassador / referral recruitment

**No outside sponsors or financial products on EDP channels.**

## 16. Agent Roster (8 agents, approved)

1. **Research** — trends, keywords, news, legislation → topic feed
2. **Brainstormer** — builds mini theme wheels for non-EDP channels
3. **Producer** — runs the doc → audio → slides → video pipeline
4. **Compliance** — checks scripts/output against LDA/UPL rules
5. **Packaging** — thumbnails, titles, descriptions
6. **Distribution** — pushes to all syndication rails
7. **Community** — harvests comments, manages the FAQ bank and replies
8. **Analytics (learning loop)** — scores topics nightly, reweights theme wheels monthly, auto-commits a weekly dashboard `REPORT.md` to GitHub + GitHub Pages HTML, and sends a Telegram summary

**North-star KPI:** leads / seminar registrations per channel.

## 17. Outstanding Setup Items

- Step-by-step NotebookLM connection instructions (to be written)
- Master topic prompt template (to be written)
- Gamma Pro subscription (~$15–20/mo) — approved as the only new cost
