---
name: motion-creative-brief-from-data
description: >-
  Write the next round of creative briefs grounded in a Motion workspace's real performance, its
  saved report configurations, its brand context and its competitors' live creative — using the
  Motion MCP server.
api: Motion MCP
surface: mcp
endpoint: https://projects.motionapp.com/mcp
operations:
  - get_auth_context
  - get_workspace_brand
  - get_reports
  - get_creative_insights
  - get_glossary_values
  - get_creative_summary
  - get_inspo_creatives
generated: '2026-08-12'
method: generated
source: https://help.motionapp.com/en/articles/14315735-motion-mcp
---

# Motion — creative brief from real data

Every tool name below is a real Motion MCP tool documented by Motion. Read-only — this skill produces
briefs, it does not create anything inside Motion.

## Steps

1. **Establish context.** `get_auth_context`; confirm the workspace.
2. **Ground in the brand before generating anything.** `get_workspace_brand` returns the brand's
   positioning, voice and visual identity as configured in Motion. Motion's own guidance is to pull
   this *before* generating concepts or briefs. Skipping it is how you get on-trend, off-brand ideas.
3. **Reuse the team's existing measurement.** `get_reports` with no report ID lists the saved
   reports; with a report ID it returns the full configuration — filters, attribution windows,
   metrics and comparison settings, filterable by report type (top ads, creative comparison,
   sprints). Take the date range, attribution window and success metric from the report the team
   already trusts rather than choosing your own. If you deviate, say so explicitly.
4. **Pull the evidence.** `get_creative_insights` using the metric and window from step 3, then
   `get_creative_summary` on the specific winners to get the detected format, hook, headline and
   CTAs in Motion's own words.
5. **Speak the taxonomy.** `get_glossary_values` for the workspace's AI Tags. Write each brief
   against those categories — Asset Type, Visual Format, Messaging Angle, Hook Tactic, Headline
   Tactic, Intended Audience, Seasonality, Offer Type — so the resulting ads are taggable and
   measurable on the same axes as the ones you learned from.
6. **Add a diversity check.** Motion's stated reason for AI Tagging is creative *diversity*, not
   just iteration. Look at where the winners cluster in the tag space and brief deliberately into
   the gaps as well as into the proven pattern.
7. **Borrow structure, not assets.** `get_inspo_creatives` for a competitor's live library gives
   format, copy, headline, CTA and launch date. Use it for structural patterns and open angles.
   Remember there are no performance metrics on competitor creative — never brief "because it works
   for them".
8. **Write the brief.** For each concept: the hypothesis, the tag values it targets, the hook, the
   format, the audience, what evidence it came from (creative + metric + window), and how you will
   know it worked.

## Rules

- Every claim in a brief traces to a tool result. If it came from general knowledge rather than the
  workspace's data, mark it.
- Analytics tools (`get_creative_insights`, `get_creative_summary`) can return a `waiting` status
  while the report generates — retry, or narrow the query. Do not brief from an empty result.
- Motion's MCP covers Meta only today. If the team runs TikTok, YouTube or LinkedIn, those channels
  are not represented in anything you just read.
- Errors are JSON-RPC 2.0; HTTP 401 with `-32001` means re-authenticate.
