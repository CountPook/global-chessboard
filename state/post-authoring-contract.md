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

## Ad placement lessons (learned 2026-09-01, Issue #8)

- **Only ONE `primary` (with-logo) ad slot per post.** A second primary block is
  rejected with "No 'primary' ad slot is available". Make slot 1 primary and any
  further ads `secondary`.
- **The publication has an ad-opportunity ACCEPTANCE CAP.** When it already holds
  several accepted-but-unplaced opportunities, trying to place NEW offers from
  `list_ad_offers` (which auto-claims on placement) fails with "can not accept
  any more opportunities". Instead, embed ALREADY-ACCEPTED opportunities found
  via `list_ad_opportunities` (status defaults to active; only `accepted` ones
  are embeddable) using their opportunity id in the `data-id`. These need no new
  claim, so they bypass the cap.
- Still enforce expiry: discard any opportunity whose `send_by` is at/before the
  target send timestamp (8:00 AM PT = ~15:00 UTC), even if it is the
  highest-payout one.
- **Robust build pattern:** create the draft with placeholder ad blocks first
  (always succeeds), then insert ads with `edit_post_content` (replace the
  placeholder section by its block hash). That way ad-network limits can never
  block draft creation.
- `email_settings` keys are `email_subject_line` / `email_preview_text` (NOT
  `subject_line` / `preview_text`, which are silently dropped).
- `get_post_content` (editor_html) can exceed the tool output cap; it is saved to
  a file. Parse the JSON `content` field and read `data-node-hash="..."` on
  top-level blocks to get hashes for `edit_post_content`.

## Brand tokens (do not restyle theme; apply via spans/sections)
gold #c9a84c; navy bg #0d1b2a; secondary bg #0a1520; card bg #0f2133;
body text #a0b4c8; muted #8a9bb0; heading accent #e8d5a3; faint #5a6a7a;
border #1e3a52; ad strip bg #0c1e30. Heading font Georgia serif; body Inter.
