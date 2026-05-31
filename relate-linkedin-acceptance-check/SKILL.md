---
name: relate-linkedin-acceptance-check
description: >-
  Run Dan Thorpe's LinkedIn acceptance check for the Relate Group prospecting
  pipeline. Detect, FROM EMAIL, which pending LinkedIn connection requests have
  been accepted or have replied, update the Relate Group Monday board, and queue
  the next message for Dan to approve. Use whenever Dan says "run the acceptance
  check," "did anyone accept," "check LinkedIn accepts," "any connection
  accepts," "who connected back," "check for replies on my LinkedIn outreach,"
  or when reconciling the "Connection Sent" leads on the signal board against new
  LinkedIn notification emails. Detection and prep only: it NEVER sends a
  LinkedIn message. Dan approves and sends every message himself.
---

# Relate Group LinkedIn Acceptance Check

Detect which pending LinkedIn connection requests have been accepted or replied
to, update the Monday board, and have the next message ready for Dan to approve.
This is detection and prep only. Never send a LinkedIn message. Dan approves
every send himself, and sends go out through Chrome with him present.

## Hard rules

These rules matter on every run, so apply them to anything you read or write:

- Never use the em-dash character ("—") anywhere in output. Use periods, commas,
  colons, parentheses, or "and" / "but" instead.
- Always write the firm as "Relate Group," never just "Relate." If an older
  draft says "Relate," correct it to "Relate Group" when you surface it.
- NEVER move a lead backward. This skill only ever advances a lead forward along
  the status ladder (New, Ready to Send, Connection Sent, Connected, Followed Up,
  plus the closing statuses). It must never set a lead to an earlier stage than
  it already holds. See the guardrail in Step 3.
- NEVER reply to, or queue or surface a templated message for, a contact who has
  already sent a real message. A canned first message on top of their personal
  reply reads as completely disjointed. Real replies are routed to Dan to answer
  himself (see Step 3, "MESSAGED").

## Safeguards against false positives and negatives

These protect against the two failure modes that bit this pipeline before: a FALSE
POSITIVE (treating a non-acceptance as an acceptance, or re-queuing a message that
already went out) and a FALSE NEGATIVE (missing a real acceptance, or losing
data). Monday's activity log records every status change (who, when, old to new);
use it as the backup of record.

- CROSS-CHECK THE LOG BEFORE CHANGING A STATUS. Read the item's recent activity
  log (get_board_activity, or activity_logs for the item) and confirm its current
  state is real, not the residue of a prior bad change. If a status was recently
  flipped backward (for example Followed Up to Connected), treat the
  furthest-along stage as the truth, flag it for Dan, and restore it rather than
  re-queuing a message that already went out.
- FALSE-POSITIVE acceptances to reject (also in Step 2 and the Step 3 guardrail):
  a delivery confirmation ("Message accepted by <Name>"), an inbound invite ("I
  want to connect"), or any lead already past Connection Sent. Do not advance or
  surface a message for these.
- FALSE-NEGATIVE misses: an acceptance buried in Archive or Spam. Always check
  Inbox, Archive, AND Spam (Step 2) so a real acceptance is not missed.
- NO SILENT OVERWRITE. When you change a status, write the prior value and the
  reason into the lead's note (LinkedIn Insights), for example "was Followed Up;
  first message already sent, do not resend." If you cannot tell whether a message
  already went out, STOP and flag for Dan rather than risk a double-send.

## What you need

- Monday board: `18415579805` ("Relate — Nonprofit Signal Leads", Main
  workspace). Key columns:
  - Status: `color_mm3vgcvp` (labels include "Connection Sent", "Connected",
    "Followed Up", "Not Interested", "Not a Fit", "Not Enough Info")
  - LinkedIn Draft Doc (Google Doc URL): `link_mm3vnpss`
  - LinkedIn Profile: `link_mm3w4dpm`
  - Trigger Detail: `long_text_mm3v43dc`
  - LinkedIn Insights: `long_text_mm3wmhcw`
  - Action for Dan: `long_text_mm3wbvg2`
- Spark Desktop running with agent access enabled. LinkedIn notification email
  lands in the **danjthorpe@gmail.com** mailbox (not dan@relate.consulting).
- Google Drive read access, to open each lead's draft Doc.

If Spark is not reachable, note that and stop. Do not error out.

## Step 1: Get the leads to check

On board `18415579805`, read the items whose Status (`color_mm3vgcvp`) is
"Connection Sent". Use `get_board_items_page` with a filter on that column
(call `get_board_info` first if you are unfamiliar with the board structure).

For each lead, note: item id, organization name (item name), the person's name
(from LinkedIn Profile `link_mm3w4dpm` or Trigger Detail), and the LinkedIn
Draft Doc URL (`link_mm3vnpss`).

ONLY "Connection Sent" leads are in scope. Leads already at "Connected,"
"Followed Up," or any closing status ("Not Interested," "Not a Fit," "Not Enough
Info") have already been accepted and advanced. Do not pull them into this run,
do not change their status, and do not re-surface their first message: it has
very likely already gone out. (A lead reaching "Followed Up" means the first
message was sent and followed up.)

If there are no "Connection Sent" leads, write a short summary saying so and
stop.

## Step 2: Check email via Spark

LinkedIn email auto-archives quickly, so the acceptance you want is often in
**Archive**, not Inbox. It can also land in **Spam**. Check all three.

1. Call `accounts` to confirm the danjthorpe@gmail.com mailbox, and `folders`
   to get folder identifiers (Inbox, Archive, Spam).
2. Search recent LinkedIn notifications from the last ~2 days in each of Inbox,
   Archive, and Spam. A reliable filter is `from:linkedin.com newer_than:2d`,
   run once per folder via the `emails` tool (set `folder` each time).
3. LinkedIn senders to expect: `invitations@linkedin.com`,
   `messaging-digest-noreply@linkedin.com`, `notifications-noreply@linkedin.com`,
   and `hit-reply@linkedin.com`.

Identify two kinds of signal, and read the body when you need the wording:

- ACCEPTANCE: subjects like "<Name> accepted your invitation", "you are now
  connected", "is now a connection".
- MESSAGE / REPLY: "<Name> sent you a message", or an InMail / message reply.

Be careful to distinguish these false positives, which are NOT acceptances of
Dan's outreach:

- "I want to connect" from someone: that is an inbound invite TO Dan, not an
  acceptance of his.
- "Message accepted by <Name>": that is a delivery confirmation, not a reply.
- Job alerts, device-verification, and digest emails: ignore.

## Step 3: Match and update the board

Match each notification's person to a "Connection Sent" lead by person name and
org. Only act on matches to leads on the "Connection Sent" list. Note which
folder each matched notification came from (Inbox, Archive, or Spam) so Dan
knows whether LinkedIn mail is landing in Spam.

GUARDRAIL (no regression, no double-send): before changing any status, confirm
the lead's CURRENT status is "Connection Sent." If the matched person's lead is
already at "Connected," "Followed Up," or a closing status, STOP on that lead:
do not change its status (never move it backward) and do not surface its first
message, because it was already accepted and advanced and the message has very
likely already gone out. The one exception is a clearly NEW negative reply from
someone already Connected or Followed Up (a genuine "not interested" / "remove
me"): you may then set the appropriate closing status, since that is still a
forward move. If you ever see that a lead was recently knocked backward (for
example "Followed Up" changed to "Connected"), treat the furthest-along stage as
the truth, flag it for Dan, and restore it rather than re-queuing the message.

For leads correctly at "Connection Sent," update Status (`color_mm3vgcvp`) with
`change_item_column_values`, passing `createLabelsIfMissing: true`:

- ACCEPTED (no negative message): set Status "Connected". Open the lead's
  LinkedIn Draft Doc (`link_mm3vnpss`) and extract the "FIRST MESSAGE" block to
  have ready for Dan. Do NOT send it.
- MESSAGED (a real inbound reply from the lead, positive or neutral): DO NOT queue
  or surface the templated first message; sending canned copy on top of their
  personal reply reads as completely disjointed. Instead ELEVATE it to Dan: set
  Status "Replied", move the item to the "Replied (Dan to Respond Personally)"
  group (group_mm3w6sdc) with move_item_to_group, capture the gist of what they
  said in LinkedIn Insights (long_text_mm3wmhcw), and send Dan a notification
  (create_notification: user_id "66543582", target_type "Project", target_id =
  the item id, text = a one-line "<Name> at <Org> replied on LinkedIn, respond
  personally"). Dan writes the reply himself. Never auto-send anything here.
- Clearly NEGATIVE reply (polite no, "not interested", "remove me", "wrong
  person"): set Status "Not Interested" and note what they said in LinkedIn
  Insights (`long_text_mm3wmhcw`). Queue no message.
- Reply that makes clear the lead is not a fit: set Status "Not a Fit" with a
  one-line reason.

The closing statuses (Not Interested, Not a Fit, Not Enough Info) trigger a
Monday automation that moves the item into the "Not Pursuing" group, so set them
accurately. Do not invent a reply or judgment that is not clearly in the email.
When unsure, leave the lead as is and flag it for Dan.

When you surface any FIRST MESSAGE or reply text, make sure the firm reads as
"Relate Group" (correct it if an older draft says just "Relate") and that there
are no em-dashes.

## Step 4: Report for approval

Write a concise summary (no em-dashes):

- Who accepted or replied since the last check (name and org), the status you
  set, and for each now Connected, the ready-to-send FIRST MESSAGE text (plus the
  Doc link) for Dan to review, with the firm named "Relate Group."
- Any leads who REPLIED and were elevated to the "Replied (Dan to Respond
  Personally)" group, with the gist of what they said. Call these out FIRST: they
  are the warmest and most time-sensitive, and Dan answers them himself.
- Any leads marked Not Interested or Not a Fit, with a one-line reason.
- Which leads are still pending (no email yet).
- Any lead you skipped or restored under the no-regression guardrail (for
  example an acceptance email for someone already Connected or Followed Up),
  so Dan knows it was intentionally not re-queued.
- Which folder each notification came from (Inbox, Archive, Spam), so Dan knows
  if LinkedIn mail is landing in Spam.

End with a line to the effect of: these messages are drafted and ready, nothing
was sent. To send any, Dan just says so while present and they go out through
Chrome with his approval.

## Running on a schedule

This skill is well suited to a recurring run (for example 9am, 11am, 1pm, 3pm,
and 5pm local). It only runs while the Claude app is open and the computer is
awake; if asleep at a scheduled time, it runs on next launch. It depends on
Spark Desktop running with agent access enabled.

When invoked as an automated scheduled run with no user present, execute
autonomously: make reasonable choices, take only the board-update writes this
skill describes, never send a LinkedIn message, honor the no-regression
guardrail in Step 3, and produce the report as the output.
