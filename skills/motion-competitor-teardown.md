---
name: motion-competitor-teardown
description: >-
  Tear down a competitor brand's live paid-social creative using Motion's Inspo ad library — resolve
  the brand, pull its running creatives and brand intelligence, and reverse-engineer its strategy,
  formats, hooks and testing cadence via the Motion MCP server.
api: Motion MCP
surface: mcp
endpoint: https://projects.motionapp.com/mcp
operations:
  - get_auth_context
  - get_workspace_competitors
  - search_brands
  - get_brand_by_domain
  - get_inspo_creatives
  - get_inspo_brand_context
  - get_glossary_values
generated: '2026-08-12'
method: generated
source: https://help.motionapp.com/en/articles/14315735-motion-mcp
---

# Motion — competitor teardown (Inspo)

Every tool name below is a real Motion MCP tool documented by Motion. Read-only.

## The one thing to get right

Motion's Inspo tools return **creative metadata only** for other brands — format, copy, headline,
CTA, launch date, status, days active, landing page and file URLs. They return **no performance
metrics**. ROAS, spend, CPA and every other performance number exist only for the authenticated
workspace's own ads. Any inference about how well a competitor's ad *performs* is an inference; label
it as one. This is the single easiest way to produce a confidently wrong teardown.

## Steps

1. **Establish context.** `get_auth_context` — organizations and workspaces. Confirm the workspace
   if there is more than one.
2. **Start from what the team already tracks.** `get_workspace_competitors` lists the competitor
   brands configured in Motion with names, domains, categories, follower counts and active ad
   counts. This is the intended starting point for competitive analysis.
3. **Resolve anything outside that list.** Inspo is not limited to tracked competitors.
   - Given a name: `search_brands` returns matching brands with IDs, logos and industry info.
   - Given a URL or domain: `get_brand_by_domain` resolves it directly. Prefer this when you have a
     link — it avoids the wrong-brand risk of a name match.
4. **Pull the ad library.** `get_inspo_creatives` for the resolved brand. Sort newest/oldest, filter
   by date or status, and apply AI tags. Note `days active` and `status` — a long-running active ad
   is the strongest available proxy for something working, and it is a proxy, not a metric.
5. **Pull the strategic layer.** `get_inspo_brand_context` returns positioning, voice, tone,
   messaging angles, product info and customer-voice analysis. Use this to explain *why* the
   creative looks the way it does, rather than describing what is on screen.
6. **Compare in the team's own vocabulary.** `get_glossary_values` gives the workspace's AI Tags.
   Map the competitor's creative into the same categories the team uses for its own ads — that is
   what makes a teardown actionable instead of interesting.
7. **Deliver a gap, not a catalogue.** State the formats and angles the competitor is running that
   this workspace is not, the testing cadence you can observe from launch dates, and the small
   number of tests worth running as a result.

## Rules

- Do not merge competitor metadata and own-account performance into one table.
- Availability is asymmetric: Inspo tools work immediately after signup, while the performance
  tools need the ad account to finish syncing (hours). A teardown is the useful thing to run on day
  one.
- Errors are JSON-RPC 2.0; HTTP 401 with `-32001` means re-authenticate.
- Motion publishes no rate limits and no pagination contract. Pull a bounded date window rather
  than an entire library.
