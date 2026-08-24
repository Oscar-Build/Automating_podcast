# SEO Plugin Field Mapping

Different sites run different SEO plugins. Writing meta title/description to the wrong plugin's fields does nothing — WordPress silently ignores fields that don't belong to an installed plugin, so this fails quietly, not loudly. Always confirm before building.

## Yoast SEO (most sites so far)

Confirmed on: EstateDocPrep, CheckHouseValueOnline, Offer On Your Terms, Mandy's Laundry, Home Buyers / Oscar's Real Estate Team

Meta fields:
- Title: `_yoast_wpseo_title`
- Description: `_yoast_wpseo_metadesc`

## Rank Math (confirmed different)

Confirmed on: SellMyHouseForTheMostMoney (smhftm)

Meta fields:
- Title: `rank_math_title`
- Description: `rank_math_description`

Rank Math also has stronger native FAQ/schema/structured-data support than Yoast, which is relevant if a site is doing AEO/GEO-focused content (answer-engine and generative-engine optimization) — the FAQ block on a local page can map directly into Rank Math's FAQ schema type without extra work.

## How to confirm which one a new site uses

1. Check the site's installed plugins via a WordPress REST API call, or ask Oscar directly if he's just been in wp-admin.
2. If genuinely unsure, do NOT guess — writing to the wrong plugin's fields costs nothing to attempt but produces a page with no meta title/description at all, which is a real SEO problem that's easy to miss until someone checks a live page months later.

## Open decision (not yet resolved)

Oscar has asked more than once for "the best plugin covering SEO, AEO, and GEO" as a platform-wide standard, but hasn't formally decided. Rank Math is the leading candidate (better native FAQ/schema support), but this hasn't been confirmed as the standard to roll out everywhere — check with Oscar before assuming a new site should use Rank Math over Yoast for this reason alone.
