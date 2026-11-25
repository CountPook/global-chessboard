# Cached beehiiv authoring contract (pub_995eb47b-b524-4e27-b1ea-daf929e01bd5)

Cached 2026-06-24 from learn_post_authoring. Re-fetch if beehiiv behavior changes.

## Key facts for building issues

- **Sections** are the styling primitive. ALL styling lives in `data-*` attrs on
  `<div class="node-section" ...>`; inline `style=""` on the section wrapper is
  dropped. A visible border needs both a width > 0 AND a color. Border widths,
  radii, padding, margin are integer px attrs (data-border-width-{top,right,bottom,left},
  data-border-{top-left,...}-radius, data-padding-{...}, data-margin-{...}).
  data-border-style = solid|dotted|dashed.
- **Inline text styling** uses `<span style="color:...; font-size:...; font-family:...; font-weight:...">`
  (textStyle mark). This DOES survive on inline spans inside paragraphs/headings.
- **Theme defaults** (light): paragraph #374151 / Inter 16px; h1/h2/h3 #111827
  Instrument Sans; link #0C4A6E. Because the brand is dark/navy, every content
  block must sit in a dark section with light-colored span text.
- **Headings**: <h2><span style="color:#c9a84c; font-size:1.1rem">...</span></h2>
  to force gold subheads (theme h2 is dark, would be invisible on navy).
- **Image**: <figure data-type="imageBlock" data-align="center" data-width="100%"
  data-src="..." data-alt="..." data-use-individual-border-radius="true"
  data-border-top-left-radius="4" ...><figcaption><span style="...">caption</span></figcaption></figure>
- **Button** (exact): <div class="node-button"><a data-type="button" href="..."
  data-alignment="center" data-size="normal" data-custom-background-color="#c9a84c"
  data-custom-text-color="#0d1b2a">Read More on Our Website</a></div>
- **Horizontal rule**: <div data-type="horizontalRule"><hr></div>
- **Columns** (2x2 = two columns blocks): <div data-type="columns" data-stack-on-mobile="true">
  <div data-type="column" data-position="0" data-width="50%">...</div>...</div>.
  A node-section card CAN be nested inside a column for per-cell card styling.
- **Ad block**: <div class="node-advertisementOpportunity" data-id="<offer_id>"
  data-advertisement-kind="primary"></div>. IMPORTANT: offer ids from
  list_ad_offers ROTATE between calls. Call list_ad_offers immediately before
  save_post and use the fresh id, or the save is rejected as "Unknown ad offer".
  data-advertisement-kind = primary (with logo) | secondary (no logo).
- **Direct sponsorship**: <div class="node-sponsorship" data-agreement-item-id="<uuid>"></div>
- **Merge tags**: {{current_date_full}} (date, "full" suffix), {{unsubscribe_url}},
  {{subscriber_preferences_url}}, {{first_name|there}} (with fallback).
- **recipients**: omit on save_post to default to free tier on web+email (matches
  this publication's audience).

## Brand tokens (do not restyle theme; apply via spans/sections)
gold #c9a84c; navy bg #0d1b2a; secondary bg #0a1520; card bg #0f2133;
body text #a0b4c8; muted #8a9bb0; heading accent #e8d5a3; faint #5a6a7a;
border #1e3a52; ad strip bg #0c1e30. Heading font Georgia serif; body Inter.

## Ad placement lessons (added 2026-11-25)

- **Only ONE `primary` (with-logo) ad slot per post.** Additional ad nodes must use
  `data-advertisement-kind="secondary"`. Placing a 2nd primary fails:
  "No 'primary' ad slot is available in this post for opportunity <id>".
- **The ad network caps simultaneously ACCEPTED opportunities (appears to be 3).**
  Embedding a numeric offer id in save_post ACCEPTS it (consumes a slot). If the cap is
  full you get "can not accept any more opportunities". Accepted opportunities that are
  already placed in a draft stay consumed until that draft is published or deleted, or
  the ad is removed from it.
- **An accepted opportunity already in a post cannot be reused**: "already placed in
  another post". Use list_ad_opportunities(status="accepted") to see what's placed where.
- **Before placing ads, check list_ad_opportunities** for already-accepted/placed items
  so you don't exhaust the cap or double-book. Only `accepted` opportunities embed.
- **IMPORTANT context:** This publication accumulates unpublished daily drafts that are
  never scheduled. Those drafts hold accepted ad opportunities and exhaust the cap, which
  blocks real ad placement on new drafts. If ads can't be placed, use the placeholder
  block and flag the draft backlog in the report rather than editing other drafts.
