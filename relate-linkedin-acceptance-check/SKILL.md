---
name: relate-linkedin-acceptance-check
description: >-
  Run Dan Thorpe's LinkedIn acceptance check for the Relate Group prospecting
  pipeline. Detect, from Dan's Spark email AND from his LinkedIn inbox (read live
  via Chrome, including the Sales Navigator messaging inbox), which pending LinkedIn connection requests have been accepted or
  have replied, update the Relate Group Monday board, and queue the next message
  for Dan to approve. Use whenever Dan says "run the acceptance
  check," "did anyone accept," "check LinkedIn accepts," "any connection
  accepts," "who connected back," "check for replies on my LinkedIn outreach,"
  or when reconciling the "Invite Pending" leads on the signal board against new
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
  the status ladder (New, Ready to Send, Invite Pending, Connected (No Note Yet), Followed Up (Awaiting Reply),
  plus the closing statuses). It must never set a lead to an earlier stage than
  it already holds. See the guardrail in Step 3.
- NEVER reply to, or queue or surface a templated message for, a contact who has
  already sent a real message. A canned first message on top of their personal
  reply reads as completely disjointed. Real replies are routed to Dan to answer
  himself (see Step 3, "MESSAGED"), and their full verbatim reply is ALWAYS posted as an UPDATE (comment) on the Monday item with create_update, for EVERY reply, every time. That comment is the system of record for the reply text; never leave the reply only in a text column.
- EMAIL IN A REPLY MEANS AUTO-TRIAGE. If a LinkedIn reply (regular inbox or Sales Navigator) contains the person's email address, then REGARDLESS of the lead's current status or group, immediately move the Monday item to the "Replied (Dan to Respond/Triage)" group (group_mm3w6sdc) with move_item_to_group, set Status (color_mm3vgcvp) to "Replied (Triage)", and post their FULL verbatim reply as an UPDATE (comment) on the item with create_update. Also write the exact email address they provided into the board's Email column (email_mm408nfj) with change_item_column_values, for example {"email_mm408nfj":{"email":"name@org.org","text":"name@org.org"}}. Sharing an email is a high-intent signal that Dan triages himself, so this placement overrides the normal status flow (it is an allowed forward move, never a regression).
- EVIDENCE-BOUND ONLY. Every acceptance or reply you log, and every status change
  you make, MUST map to a specific, real piece of evidence you actually located in
  one of the two sources of truth: (1) a Spark email in the danjthorpe@gmail.com
  mailbox, identified by sender, subject, date, and folder (Inbox / Archive /
  Spam); or (2) a specific message in Dan's LinkedIn inbox, viewed live via Chrome
  (Step 2B) or his Sales Navigator messaging inbox (Step 2C), identified by the sender's name and the message text and time.
  Record that pointer with the change (in the item update / LinkedIn Insights). If
  you cannot point to a specific email or a specific LinkedIn message, you may NOT
  change the lead's status and you may NOT surface a reply for it: leave it pending
  and move on. Never infer, assume, guess, or fabricate an acceptance or a reply,
  and never write or quote reply text that is not taken verbatim from a specific
  located email or LinkedIn message. No evidence, no write.

## Safeguards against false positives and negatives

These protect against the two failure modes that bit this pipeline before: a FALSE
POSITIVE (treating a non-acceptance as an acceptance, or re-queuing a message that
already went out) and a FALSE NEGATIVE (missing a real acceptance, or losing
data). Monday's activity log records every status change (who, when, old to new);
use it as the backup of record.

- CROSS-CHECK THE LOG BEFORE CHANGING A STATUS. Read the item's recent activity
  log (get_board_activity, or activity_logs for the item) and confirm its current
  state is real, not the residue of a prior bad change. If a status was recently
  flipped backward (for example Followed Up (Awaiting Reply) to Connected (No Note Yet)), treat the
  furthest-along stage as the truth, flag it for Dan, and restore it rather than
  re-queuing a message that already went out.
- FALSE-POSITIVE acceptances to reject (also in Step 2 and the Step 3 guardrail):
  a delivery confirmation ("Message accepted by <Name>"), an inbound invite ("I
  want to connect"), or any lead already past Invite Pending. Do not advance or
  surface a message for these.
- FALSE-NEGATIVE misses: an acceptance buried in Archive or Spam. Always check
  Inbox, Archive, AND Spam (Step 2) so a real acceptance is not missed.
- NO SILENT OVERWRITE. When you change a status, write the prior value and the
  reason into the lead's note (LinkedIn Insights), for example "was Followed Up (Awaiting Reply);
  first message already sent, do not resend." If you cannot tell whether a message
  already went out, STOP and flag for Dan rather than risk a double-send.

## What you need

- Monday board: `18415579805` ("Relate — Nonprofit Signal Leads", Main
  workspace). Key columns:
  - Status: `color_mm3vgcvp` (labels include "Invite Pending", "Connected (No Note Yet)",
    "Followed Up (Awaiting Reply)", "Not Interested", "Not a Fit", "Not Enough Info")
  - Outreach Drafts (long_text): `long_text_mm3xb1qd` — holds the CONNECTION REQUEST
    and FIRST MESSAGE for the lead (replaces the old Google Doc)
  - LinkedIn Profile: `link_mm3w4dpm`
  - Trigger Detail: `long_text_mm3v43dc`
  - LinkedIn Insights: `long_text_mm3wmhcw`
  - Action for Dan: `long_text_mm3wbvg2`
  - Dossier (link to the prospect dossier Google Doc): `link_mm40p4vh`
  - Email (email): `email_mm408nfj` (the email address a lead provides in a LinkedIn reply)
  - PPC (status, Yes/No): `color_mm4075sm` (Potential Paying Customer in next 90 days, from the dossier verdict)
- Spark Desktop running with agent access enabled. LinkedIn notification email
  lands in the **danjthorpe@gmail.com** mailbox (not dan@relate.consulting).

If Spark is not reachable, note that and stop. Do not error out.

## Step 1: Get the leads to check

On board `18415579805`, read the items whose Status (`color_mm3vgcvp`) is
"Invite Pending". Use `get_board_items_page` with a filter on that column
(call `get_board_info` first if you are unfamiliar with the board structure).

For each lead, note: item id, organization name (item name), the person's name
(from LinkedIn Profile `link_mm3w4dpm` or Trigger Detail), and the lead's Outreach
Drafts text (`long_text_mm3xb1qd`).

ONLY "Invite Pending" leads are in scope. Leads already at "Connected (No Note Yet),"
"Followed Up (Awaiting Reply)," or any closing status ("Not Interested," "Not a Fit," "Not Enough
Info") have already been accepted and advanced. Do not pull them into this run,
do not change their status, and do not re-surface their first message: it has
very likely already gone out. (A lead reaching "Followed Up (Awaiting Reply)" means the first
message was sent and followed up.)

If there are no "Invite Pending" leads, write a short summary saying so and
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

## Step 2B: Check Dan's LinkedIn inbox via Chrome (required)

Email notifications miss real replies (LinkedIn does not always email a message
reply, and digests get archived). So ALSO read Dan's actual LinkedIn inbox with
Chrome every run. This is the most reliable source for replies. CRITICAL: do NOT trust the "Unread" filter or unread badges. Dan reads messages on his phone, so genuine replies are very often already marked read by the time this check runs and will not show under Unread (this is exactly how real replies from Dan Leal and Marc Hurlbert were missed on a prior run). Always work from the FULL conversation list and judge each thread by the sender of its LAST message, regardless of read/unread state, scrolling back far enough to cover the whole outreach window (at least the last ~7 days, further after a big send).

1. With the Claude in Chrome tools, navigate to `https://www.linkedin.com/messaging/`
   and screenshot the conversation list. (Use an existing logged-in tab; the
   browser is read-tier for computer-use, so use the Chrome MCP, not pixel clicks
   on the browser chrome.)
2. Scan conversations updated in the last ~5 days. In the list preview, a thread
   whose last line starts with the OTHER person's name (for example
   "Kris: Thanks, Dan...") means THEY replied last. A thread whose last line starts
   with "You:" means Dan sent last and there is no new reply: skip it.
3. For each thread where the lead replied last, open it and read the full latest
   message so you have the exact wording. Capture a short VERBATIM quote and the
   approximate time. This is the evidence pointer for that lead (per the
   evidence-bound rule).
4. Match each replying person to a board lead by name and org. A reply can come
   from a lead at ANY active stage (Invite Pending, Connected (No Note Yet), or Followed Up (Awaiting Reply)):
   all of them are valid to elevate to Replied, because elevating to the Replied
   lane is a forward move, not a regression. Also DETECT ACCEPTANCES IN CHROME, not only from email: open My Network > Connections (https://www.linkedin.com/mynetwork/invite-connect/connections/) sorted by "Recently added" and read the most recent connections, and glance at the Notifications page for "accepted your invitation" items. Any "Invite Pending" lead who now appears in recently-added Connections has ACCEPTED even if no acceptance email arrived; the recently-added Connections list is the most reliable acceptance signal. Match by name and org and treat as ACCEPTED in Step 3.

Treat a LinkedIn-inbox reply exactly like a "MESSAGED" reply in Step 3: never queue
or surface a templated message on top of it, elevate it to Dan.

## Step 2C: Check Dan's Sales Navigator inbox via Chrome (required)

Most Alongside outreach goes to 2nd and 3rd degree leads as Sales Navigator messages and InMail, and their replies land in the SALES NAVIGATOR messaging inbox, not the regular LinkedIn inbox. LinkedIn's email notification for a Sales Nav reply can also point at a different, stale thread for the same person in the regular inbox, so email and the regular inbox alone will miss or misread these. ALWAYS check the Sales Navigator inbox every run.

1. With the Claude in Chrome tools, navigate to https://www.linkedin.com/sales/inbox/ and read the conversation list (get_page_text is reliable here).
2. Judge each thread by the sender of its LAST message, exactly as in Step 2B: a preview that is Dan's own outgoing copy (for example the Alongside script) means no reply yet, so skip it; a preview in the other person's voice means they replied last. Ignore unrelated inbound sales or spam threads where someone is pitching Dan.
3. For each lead who replied, open the thread, capture a short VERBATIM quote and the time, and match to a board lead by name and org. Treat it exactly like a MESSAGED reply in Step 3 (elevate to Dan, never queue a templated message), and post the full verbatim reply as a Monday comment per the Hard rules.

## Step 3: Match and update the board

Match each notification's person to a "Invite Pending" lead by person name and
org. Only act on matches to leads on the "Invite Pending" list. Note which
folder each matched notification came from (Inbox, Archive, or Spam) so Dan
knows whether LinkedIn mail is landing in Spam.

GUARDRAIL (no regression, no double-send): before changing any status, confirm
the lead's CURRENT status is "Invite Pending." If the matched person's lead is
already at "Connected (No Note Yet)," "Followed Up (Awaiting Reply)," or a closing status, STOP on that lead:
do not change its status (never move it backward) and do not surface its first
message, because it was already accepted and advanced and the message has very
likely already gone out. The one exception is a clearly NEW negative reply from
someone already Connected (No Note Yet) or Followed Up (Awaiting Reply) (a genuine "not interested" / "remove
me"): you may then set the appropriate closing status, since that is still a
forward move. If you ever see that a lead was recently knocked backward (for
example "Followed Up (Awaiting Reply)" changed to "Connected (No Note Yet)"), treat the furthest-along stage as
the truth, flag it for Dan, and restore it rather than re-queuing the message.

For leads correctly at "Invite Pending," update Status (`color_mm3vgcvp`) with
`change_item_column_values`, passing `createLabelsIfMissing: true`. In EVERY case
below, you must already have a specific backing email located in the
danjthorpe@gmail.com mailbox (sender, subject, date, folder), OR an equivalent Chrome-observed signal (a specific LinkedIn message, or a recently-added Connections-list entry or "accepted your invitation" notification viewed live). Record that pointer
in LinkedIn Insights with the status change. If there is no such backing evidence (email or Chrome signal) for a lead,
make NO change and surface nothing for it:

- ACCEPTED (no negative message): set Status "Connected (No Note Yet)". Read the lead's Outreach
  Drafts column (`long_text_mm3xb1qd`) and take the "FIRST MESSAGE" block to have
  ready for Dan. Do NOT send it.
- MESSAGED (a real inbound reply from the lead, positive or neutral): DO NOT queue
  or surface the templated first message; sending canned copy on top of their
  personal reply reads as completely disjointed. Instead ELEVATE it to Dan: set
  Status "Replied (Triage)", move the item to the "Replied (Dan to Respond/Triage)"
  group (group_mm3w6sdc) with move_item_to_group, capture in LinkedIn Insights
  (long_text_mm3wmhcw) a SHORT VERBATIM QUOTE of what they wrote taken directly
  from the located reply email (not a paraphrase or an invented gist) plus the
  email pointer, and send Dan a notification
  (create_notification: user_id "66543582", target_type "Project", target_id =
  the item id, text = a one-line "<Name> at <Org> replied on LinkedIn, respond
  personally"). ALWAYS post the lead's full verbatim reply as an UPDATE (a comment) on the Monday item with create_update. This is REQUIRED for EVERY reply, every time, no exceptions. The comment is the system of record for the reply text and must contain the complete verbatim quote (HTML body). Do NOT bury the reply in a text column: the verbatim quote goes in the comment, and LinkedIn Insights holds only a brief context or next-step note, never the full reply. Use an HTML body, e.g. <p><b>LinkedIn reply (Dan to respond personally):</b></p><p>...the verbatim quote...</p>. Dan writes the reply himself. Never auto-send anything here. Unless the reply is a clear negative or not-interested response, ALSO draft a dossier per Step 3B and log its Google Doc link in the Dossier column (link_mm40p4vh).
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

## Step 3B: Draft a prospect dossier (for interested leads)

Whenever a lead sends a real reply that is NOT a clear negative or not-interested response, draft a short dossier on their organization and on the person, and save it as a Google Doc. Run a dossier for every genuine reply (a question, a yes, a maybe, a warm acknowledgment, a deferral), and skip it ONLY for a clear no (a polite decline, "not interested", "remove me", "wrong person", or anything you would mark Not Interested or Not a Fit). This is in addition to elevating the reply to Dan in Step 3, not a replacement.

Keep it useful, not exhaustive (about one page). Cover:

1. The organization: what it does, its mission and core programs, location, EIN, 501(c) status, and founding or exempt date.
2. Financials from the most recent IRS Form 990s: total revenue, total expenses, net assets, and executive compensation (top officers' salaries). Use ProPublica Nonprofit Explorer (search the org name or EIN at https://projects.propublica.org/nonprofits/) plus the org's own site. If the org files only the 990-N e-postcard (gross receipts under 50,000 dollars) so no detailed 990 is public, say so plainly and note it signals an early-stage or micro-budget org. Never invent figures.
3. A short bio of the person replying: role, education and background, focus, and network or positioning, drawn from their LinkedIn profile (read live via Chrome) plus any other public info. Note mutual connections if visible.
4. A few talking points and next steps for Dan, tailored to what the lead asked for.
5. A clear Relate-fit verdict: a short section titled to the effect of "Paying customer in next 90 days?" that states a plain YES or NO and a one-paragraph rationale weighing genuine interest against ability to pay (budget signals from the 990s, the size of the ask, and urgency).

Create the Doc with the Google Drive create_file tool (title like "Dossier - <Org> (<Person>)", text content, which converts to a Google Doc). Then put the Doc link in the lead's Dossier column (link_mm40p4vh) with change_item_column_values, for example {"link_mm40p4vh":{"url":"<doc url>","text":"Dossier - <Org>"}}. Call out the dossier and its link in the Step 4 report. The evidence-bound rule still applies: state only facts you actually found, and never invent financials. Also set the lead's PPC column (color_mm4075sm), a Yes/No status, to match the dossier verdict: Yes only if a paid Relate Group engagement looks realistic within 90 days, otherwise No.

## Step 4: Report for approval

Write a concise summary (no em-dashes). Every accepted/replied entry must cite its
backing email (sender, subject, date, folder); if you cannot cite one, it does not
belong in the report as an acceptance or reply:

- Who accepted or replied since the last check (name and org), the backing email
  it maps to, the status you set, and for each now Connected (No Note Yet), the ready-to-send
  FIRST MESSAGE text (from the Outreach Drafts column) for Dan to review, with the
  firm named "Relate Group."
- Any leads who REPLIED and were elevated to the "Replied (Dan to Respond
  Personally)" group, with a short verbatim quote from their reply email (not a
  paraphrase). Call these out FIRST: they are the warmest and most time-sensitive,
  and Dan answers them himself.
- Any leads marked Not Interested or Not a Fit, with a one-line reason.
- Which leads are still pending (no email yet).
- Any lead you skipped or restored under the no-regression guardrail (for
  example an acceptance email for someone already Connected (No Note Yet) or Followed Up (Awaiting Reply)),
  so Dan knows it was intentionally not re-queued.
- Which folder each notification came from (Inbox, Archive, Spam), so Dan knows
  if LinkedIn mail is landing in Spam.

End with a line to the effect of: these messages are drafted and ready, nothing
was sent. To send any, Dan just says so while present and they go out through
Chrome with his approval.

## Owner, Channel, and silent accepts

On every status change in this run, also stamp two columns so the board stays self-describing. Owner (people column multiple_person_mm42a5bj): set it to Dan (person id 66543582) for any lead you move to "Connected (No Note Yet)" or "Replied (Triage)"; if a lead sits in the "With Emily" group it belongs to Emily (person id 101710184), so set or leave Owner as Emily and do not reassign it to Dan. Channel (status column color_mm42t2q9, labels "Connection Request" and "InMail", pass createLabelsIfMissing true): set "Connection Request" when the lead came from an accepted LinkedIn connection request (the normal case for an acceptance), or "InMail" when the outreach was a Sales Navigator InMail or direct message. Write Owner and Channel in the same change_item_column_values call as the Status change.

MB 1st-Degree at REPLY time: whenever you elevate a lead to "Replied (Triage)" and its MB 1st-Degree column (color_mm3w8mpp) is "Unknown", resolve it NOW; the sweep no longer runs this check (per Dan, June 6, 2026), so reply time is where it happens. Method: read the lead's degree badge relative to Dan. 3rd degree = "No" automatically (Michelle is one of Dan's 1st-degree connections, so anyone connected to Michelle shows to Dan as 2nd degree or closer). For 1st or 2nd degree, open the lead's OWN mutual-connections facepile list (NEVER a keyword search with a connectionOf filter; LinkedIn silently drops the filter and returns false positives) and scan the full list for "Michelle Boggs, MBA, CFRE" (GoFundMe / nonprofit fundraising). In the list = "Yes", otherwise "No". Call out every Yes in the report so Michelle can warm the live conversation.

Catch silent accepts every run, and check BOTH awaiting groups: "Outreach Sent (Awaiting)" (group_mm3w1p1z, signal leads) and "Alongside Campaign (Awaiting)" (group_mm422a7v, the cold Alongside campaign). Treat them identically for acceptance detection, silent-accept reconciliation, and reply routing. Some people accept the connection and never send a message, which produces no email and no inbox thread, so they sit at "Invite Pending" forever unless you reconcile against your actual connections. With the Claude in Chrome tools, open https://www.linkedin.com/mynetwork/invite-connect/connections/ (My Connections, sorted by Recently added) and capture the people added since the last run (the most recent 40 to 120 covers a daily cadence). Optionally also open https://www.linkedin.com/mynetwork/invitation-manager/sent/ to see which sent invitations are still pending; anyone no longer pending has accepted or expired. For each board lead still at "Invite Pending", check whether that person now appears in your recently-added connections, matching carefully by name and org. For each confirmed match who has NOT messaged (no thread, or a thread that shows only Dan's note with no reply), treat them exactly like an ACCEPTED lead in Step 3: set Status "Connected (No Note Yet)", set Owner to Dan, set Channel "Connection Request", take the FIRST MESSAGE from Outreach Drafts to have ready for Dan, and record the evidence pointer "appeared in recently-added connections on this date" in LinkedIn Insights. Never send anything. If a matched person also has a real reply in the inbox, route them as MESSAGED ("Replied (Triage)") per Step 3 instead of as a fresh accept.

## Engaged (Their Court): the reply ping pong

The board has a group "Engaged (Their Court)" (group id group_mm42dhz) and a matching Status label "Engaged (Their Court)" for leads Dan has already answered personally, where the ball is in the lead's court. Apply two rules on every run while reading the inboxes. First, if a lead currently at "Replied (Triage)" has a thread whose latest message is from DAN (he answered their reply personally), set Status "Engaged (Their Court)", move the item to group group_mm42dhz, and post a short update quoting the gist of Dan's reply with its date. Second, if a lead at "Engaged (Their Court)" gets a NEW message from the lead (they wrote back), bounce it back: set Status "Replied (Triage)", move it to the "Replied (Dan to Triage)" group (group_mm3w6sdc), post the verbatim reply as an update, and notify Dan, exactly like any other MESSAGED reply. A lead can ping pong between these two stages for the life of the conversation. Both directions are intended moves and do not violate the no-regression rule.

## SEND comments: Dan's outbound queue (the one sending exception)

Dan can queue an outbound LinkedIn message directly from the board: he adds an update/comment on a lead item that STARTS with "SEND:" (case insensitive). Everything after the marker is the exact message he wants delivered to that lead on LinkedIn. This is the ONLY exception to the never-send rule, because the SEND: comment is Dan's explicit, pre-written approval and pre-written copy. On every run: read recent updates on every lead in every group on the board (get_updates per item), with no exceptions: a SEND: comment is honored on any lead in any group, including Pipeline / Drafting, Outreach Sent (Awaiting), Replied (Dan to Triage), Engaged (Their Court), With Emily, Nurture / Later, and Not Pursuing. Collect SEND: comments that do not yet have a later "SENT via LinkedIn" confirmation comment on the same item. For each one, deliver the text VERBATIM: strip only the SEND: marker, never edit or add anything, never send to anyone but that lead, and never send the same comment twice. Use the lead's existing thread (regular LinkedIn DM for a 1st degree connection, or the existing Sales Navigator thread for an InMail lead) and verify the recipient's name in the compose header before sending. After it sends, post a confirmation update on the item, "SENT via LinkedIn on <date and time>:" followed by the full message text, then set Status "Engaged (Their Court)" and move the item to group group_mm42dhz, since Dan has now spoken last and the ball is in their court. If the message cannot be delivered (not connected, no open thread, or the recipient cannot be verified), send nothing, post a comment explaining why, and notify Dan (user id 66543582). Never deliver a SEND: message through email or any other channel, and never treat any other comment as sendable: comments without the SEND: marker are pipeline notes, including this automation's own logs, which post from the same Monday account as Dan.

## Running on a schedule

This skill is well suited to a recurring run (for example 9am, 11am, 1pm, 3pm,
and 5pm local). It only runs while the Claude app is open and the computer is
awake; if asleep at a scheduled time, it runs on next launch. It depends on
Spark Desktop running with agent access enabled.

When invoked as an automated scheduled run with no user present, execute
autonomously: make reasonable choices, take only the board-update writes this
skill describes, never send a LinkedIn message, honor the no-regression
guardrail in Step 3, and produce the report as the output.
