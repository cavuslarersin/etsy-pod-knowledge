# Daily learnings — Etsy / Printify / Placeit

Dated, concise entries only when something genuinely new and actionable is found. Skip silently otherwise. Prune/consolidate if this grows past ~100 lines.

<!-- newest entries go at the bottom -->

## 2026-09-05
- **Etsy SEO 2026 shift: buyer-behavior signals now weighted heavily** — click-through rate, add-to-cart rate, and purchase-completion relative to search impressions matter more than before. Keyword relevance (title > tags > category/attributes) is still foundational, but engagement now compounds on top of it.
- **Title guidance**: front-load concrete traits (color/material/size/occasion) at the start of the title; move subjective filler words ("beautiful", "perfect") out of the title into description/tags instead.
- **Shipping-price visibility penalty (US domestic)**: listings with a shipping price above $6 see reduced search visibility. **Verification note (2026-09-05):** don't compare this to Printify's own displayed "USD X.XX" cost/shipping figures in its pricing table — those are Printify's internal fulfillment-cost estimate, a different number from the buyer-facing charge. Check the ACTUAL Etsy shipping profile's `primary_cost` (via `get_shipping_profiles`) converted at the real current USD/TRY rate instead. Checked on UpCreateDesign: its US-domestic profile charges 242 TRY (~$5.00 at ~48.4 TRY/USD that day) — safely under $6, no action needed there. Still worth checking on any other shop before assuming a problem.
- Customer-experience completeness (full About section, filled-out Shop Policies, review volume/quality) is an explicit ranking input, not just a trust nicety.
- Sources: mydesigns.io/blog/etsy-search-algorithm-update-2026, craftybase.com/blog/what-is-seo-on-etsy, insightagent.app/guides/etsy-seo-2026

## 2026-09-05 (later same day)
- **Free-shipping strategy implemented on UpCreateDesign's mug line as a pilot**: Etsy's "free shipping" badge is a real conversion/visibility lever, separate from the >$6 shipping-price penalty already logged above. Mechanism used: create a second Etsy shipping profile that's an exact clone of the shop's normal one except US-domestic `primary_cost=0` (keep EU/CA/rest-of-world rates unchanged, since only US free shipping is commonly worth it), then assign it to selected listings while raising item price just enough to keep true margin (item price minus product cost minus the now-absorbed shipping cost) at or above a 10% floor.
- **Gotcha: shipping-profile creation requires `origin_postal_code` and per-destination `min_delivery_days`/`max_delivery_days`** (not just carrier/mail_class) — Etsy's create-shipping-profile and add-destination endpoints reject requests missing these even though they're not obviously "required" from the docs. Copy them from an existing working profile via `GET /shops/{shop}/shipping-profiles/{id}`.
- **Gotcha: price cannot be changed via `PATCH /listings/{id}`** for listings that use the inventory/offerings model (which includes any listing with personalization) — that field is silently ignored. Price must be updated via `PUT /listings/{id}/inventory`, and the payload's `offerings` array must echo back each offering's existing `readiness_state_id` or Etsy rejects the whole request with "All offerings need readiness state." Safe pattern: `GET` the inventory first, map `offerings` to `{quantity, is_enabled, price: newPrice, readiness_state_id: o.readiness_state_id}`, then `PUT` it back.
- Sources: applied directly via Etsy Open API v3 (no blog source for this entry — implementation note from live work).
