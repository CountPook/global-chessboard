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
- **Ad block**: <div class="node-advertisementOpportunity" data-id="<id>"></div>.
  CRITICAL LESSON (2026-07-16): For multi-ad issues, do NOT place fresh
  list_ad_offers ids. The ad network caps how many opportunities the
  publication can ACCEPT, and each fresh offer placed tries to accept a new
  one. With several opportunities already auto-accepted, placing 3 fresh offers
  fails with "can not accept any more opportunities." INSTEAD: call
  list_ad_opportunities(status="accepted") and embed the already-accepted
  opportunity UUIDs directly (these are pre-accepted inventory, so embedding
  them accepts nothing new). Rank accepted opportunities by payout_per_click,
  and DISCARD any whose send_by is at/before the target send timestamp. This
  succeeded placing all 3 slots on issue #4.
  - Also OMIT data-advertisement-kind. Setting kind="primary" on more than one
    ad fails with "No 'primary' ad slot is available in this post" because
    primary/secondary map to positional slots (only one primary slot exists),
    not just logo-on/off. Omitting it lets beehiiv auto-select per slot.
  - offer ids from list_ad_offers still ROTATE between calls; opportunity UUIDs
    from list_ad_opportunities are stable. Prefer opportunity UUIDs.
- **Direct sponsorship**: <div class="node-sponsorship" data-agreement-item-id="<uuid>"></div>
- **Merge tags**: {{current_date_full}} (date, "full" suffix), {{unsubscribe_url}},
  {{subscriber_preferences_url}}, {{first_name|there}} (with fallback).
- **recipients**: omit on save_post to default to free tier on web+email (matches
  this publication's audience).

## Brand tokens (do not restyle theme; apply via spans/sections)
gold #c9a84c; navy bg #0d1b2a; secondary bg #0a1520; card bg #0f2133;
body text #a0b4c8; muted #8a9bb0; heading accent #e8d5a3; faint #5a6a7a;
border #1e3a52; ad strip bg #0c1e30. Heading font Georgia serif; body Inter.
