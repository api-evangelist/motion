---
name: motion-creative-performance-review
description: >-
  Review a Motion workspace's paid-social creative performance over a date range and explain what is
  working and why — ranked creatives, the AI-tag patterns behind the winners, the hooks that open
  them, and the demographic split — using the Motion MCP server.
api: Motion MCP
surface: mcp
endpoint: https://projects.motionapp.com/mcp
operations:
  - get_auth_context
  - get_creative_insights
  - get_glossary_values
  - get_creative_summary
  - get_creative_transcript
  - get_demographic_breakdown
generated: '2026-08-12'
method: generated
source: https://help.motionapp.com/en/articles/14315735-motion-mcp
---

# Motion — creative performance review

Every tool name below is a real Motion MCP tool documented by Motion. The Motion MCP is **read-only**
— nothing in this skill changes a Motion account, an ad, or a report.

## Before you start

- The server is OAuth 2.0 gated at `https://projects.motionapp.com/mcp`. The user completes an
  authorization-code + PKCE flow against `https://projects.motionapp.com/api/auth`; the client can
  self-register at the advertised `registration_endpoint`. You never see their Motion credentials.
- Only **Owner**, **Admin** and **Collaborator** roles can authenticate. Guest and Partner-share
  users cannot — if authentication fails, that is the first thing to check.
- Motion's MCP currently returns **Meta data only**. TikTok, YouTube and LinkedIn are not yet
  available through the MCP even when they are connected in the Motion app. Say so rather than
  reporting a partial picture as a whole one.
- Performance tools read Motion's **cached, pre-tagged** copy of the ad data, not a live platform
  call. A newly connected ad account needs a few hours before it returns anything.

## Steps

1. **Establish context.** Call `get_auth_context` first — it is documented as the automatic first
   step of any session and returns the user's organizations and workspaces. If more than one
   workspace comes back, confirm which one before you analyse anything; workspace selection is
   conversational, not a required parameter, so guessing is a real failure mode.
2. **Learn the workspace's own vocabulary.** Call `get_glossary_values` to pull the AI Tags actually
   configured for this workspace — Asset Type, Visual Format, Messaging Angle, Hook Tactic, Headline
   Tactic, Intended Audience, Seasonality, Offer Type and any custom categories. Use these labels in
   your output. Do not invent creative categories when the team already has a taxonomy.
3. **Rank the creatives.** Call `get_creative_insights` with an explicit date range and an explicit
   sort metric. It sorts on spend, ROAS, purchases, CPA, CPC, CTR, hook rate, scaling status or
   purchase value, and filters by date, custom conversion metric and AI tag. Ask the user which
   metric decides "top" rather than defaulting silently — ROAS and spend produce different winners.
4. **Handle the async pattern.** `get_creative_insights` may return a `waiting` status while the
   report generates. That is expected: wait briefly and retry. If it keeps waiting, narrow the date
   range or drop filters. Do not report a `waiting` response as "no data".
5. **Go one level deeper on the outliers.** For each of the top and bottom creatives that matter,
   call `get_creative_summary` for the detected format, plain-language summary, best hook/headline
   and CTAs. For video, call `get_creative_transcript` — the first 1–3 seconds are the hook, and the
   tool returns timed segments plus language detection and English translation. Transcripts are
   video-only; do not call it on statics.
6. **Split the audience.** Call `get_demographic_breakdown` for age (18-24 through 65+) and gender
   across spend, ROAS, purchase value, cost per purchase and thumbstop ratio. It defaults to the
   **last 7 days** — pass your own range if you want it to match step 3, otherwise your demographic
   read and your performance read cover different periods.
7. **Report the pattern, not the list.** Group the winners by the AI Tags from step 2 and state the
   recurring hook, format and angle. Name what is underperforming and why. Quote the metric and the
   window on every claim.

## Rules

- Never present a competitor's creative next to your own performance numbers as if both were
  measured. Performance metrics exist **only** for the authenticated workspace's own ads — see
  `motion-competitor-teardown`.
- There is no idempotency key and no pagination contract on this surface; treat every call as a
  fresh read and state the date range you used so the analysis is reproducible.
- If a tool errors, the envelope is JSON-RPC 2.0. `-32001` with HTTP 401 means the token is missing
  or invalid — send the user back through the OAuth flow rather than retrying.
- Motion publishes no rate limits. Be conservative with wide date ranges and heavy filter
  combinations; the documented remedy for a stuck report is a smaller query.
