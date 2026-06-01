---
name: relate-nonprofit-leads
description: >-
  Find, score, and log nonprofit fundraising prospecting leads for Relate Group,
  then draft a personalized LinkedIn outreach message for each into the lead's
  Outreach Drafts column on the board, verify the contact, set the Michelle
  1st-degree warm-intro flag, and advance
  fully prepped leads to "Ready to Send" for Dan's approval. Use whenever Dan
  wants to run the lead sweep, find new nonprofit prospects, add leads to the
  signal board, or generate outreach drafts ("run the sweep," "find leads," "add
  more leads," "any new prospects"), or as the scheduled daily run. Covers
  development or campaign RFPs, open or vacated development leadership roles,
  departures, financial distress, capital campaigns, CRM migrations, strategic
  plans or mergers, newly hired CDOs / CEOs / EDs, and capacity grants. Research,
  verification, and drafting only. It logs to the Monday board and writes the
  drafts into each lead's Outreach Drafts column (no Google Docs). It never sends
  a message or contacts anyone; Dan gives the final
  word and sends through Chrome with him present.
---

# Relate Group Nonprofit Signal Leads (canonical)

This is the single source of truth for the Relate Group lead sweep. It runs both
on demand ("run the sweep") and as the scheduled daily task. Both entry points
share this workflow, the same Monday board, and the same Drive folder; dedupe
protects against overlap. Do not keep a second, separate sweep skill: this file
supersedes the older "relate-nonprofit-signal-leads" and "relate-nonprofit-leads"
versions.

The job: turn public nonprofit "trigger" signals into a reviewed prospecting
pipeline, putting warm, well-timed leads in front of Dan with a verified contact
and a ready-to-edit outreach draft. Nothing is ever sent automatically. Surfacing
the lead, verifying the person, and writing the draft is the job; Dan owns the
send.

## Hard writing rule (non-negotiable)

Never use em-dashes. Do not use the "—" character anywhere: not in board fields,
Google Docs, or summaries. Use periods, commas, colons, parentheses, or "and" /
"but" instead. Always call the firm "Relate Group," never just "Relate." Re-read
every draft before saving to confirm both.

## Safeguards against false positives and negatives (every run)

Leads can go wrong two ways: a FALSE POSITIVE (acting on a wrong-person match or a
bad status, for example sending to the wrong contact, or re-sending because a
status was flipped) or a FALSE NEGATIVE (dropping a real lead, or losing good
data). Monday keeps a full activity log of every column change (who, when, old
value to new value); treat it as the backup of record. Apply all three rules:

1. CROSS-CHECK BEFORE CONSEQUENTIAL ACTIONS. Before advancing or regressing a
   Status, retiring a lead, changing a verified contact (link_mm3w4dpm), or
   anything a downstream send depends on, read that item's recent activity log
   (get_board_activity, or activity_logs via the API, filtered to the item). If
   the log shows a backward status move (for example Followed Up to Connected), a
   contact or field that changed unexpectedly, or a conflict with what you are
   about to do, STOP. Do not act on the suspect state: flag it to Dan, restore the
   furthest-along true value, and note what you found. Never move a lead backward.

2. VERIFICATION CONFIDENCE, AND FLAG-DO-NOT-DROP. Only advance a lead to Ready to
   Send on a CONFIDENT contact match (verified person plus org and title,
   corroborated by the source AND the LinkedIn profile). A shaky match (common
   name, org not yet on the profile, a single weak source) does NOT auto-advance:
   leave it New and flag what is unconfirmed in Action for Dan. If a real
   decision-maker exists but cannot be confirmed, flag it "needs confirmation" and
   KEEP it; do not silently drop it or mark Not Enough Info unless no one can be
   identified at all.

3. NO SILENT OVERWRITE. Whenever you change a Status or a verified contact, write
   the PRIOR value and the reason into the lead's note (Action for Dan or LinkedIn
   Insights), for example "was Connected; set to Followed Up on <date> because
   <reason>". That puts a one-glance backup on the card on top of Monday's log.
   This applies to manual corrections too: if you are fixing a value, record what
   it was.

## Configuration (current Relate Group setup)

If the board is rebuilt or columns change, update these IDs (call get_board_info
to re-map).

- Monday board: `18415579805` ("Relate — Nonprofit Signal Leads", Main workspace)
- Groups: main "Leads" (`topics`); "Active Asks (Fractional / Interim / Contract)"
  (`group_mm3wstq7`); "Not Pursuing (Declined / Not a Fit / No Info)" (`group_mm3w5j3z`)
- Column IDs:
  - Trigger Detail (long_text): `long_text_mm3v43dc`
  - Source (link): `link_mm3vj8yb`
  - Date Detected (date): `date_mm3vbbqb`
  - Location (text): `text_mm3vf40b`
  - Signal Type (status): `color_mm3vbsjy`
  - Sector (dropdown): `dropdown_mm3vj3nx`
  - Fit Priority (status): `color_mm3vjg41`
  - Status (status): `color_mm3vgcvp`
  - LinkedIn Profile (link): `link_mm3w4dpm`
  - LinkedIn Insights (long_text): `long_text_mm3wmhcw`
  - Outreach Drafts (long_text): `long_text_mm3xb1qd` — the connection note + first message live here (no Google Doc)
  - LinkedIn Draft (link): `link_mm3vnpss` — DEPRECATED, leave blank (replaced by Outreach Drafts)
  - MB 1st-Degree? (status: Yes / No / Unknown): `color_mm3w8mpp`
  - Action for Dan (long_text): `long_text_mm3wbvg2`
  - Msg Variant (status: "A (Current)" / "B (Alongside)"): `color_mm3whszp`

## Relate Group positioning (for the outreach voice)

- Core vibe: Relate Group has industry experts ready to come alongside you
  wherever you are in your journey. Strategy AND execution, not advisory-only.
- Dan: 20+ years in development leadership, has been in the seat as CDO, CMO, and
  National SVP. Relate Group offers project-based and retainer-based engagements,
  including fractional CDO and interim development leadership.
- Sector bench (honest hooks only when truly relevant): rare disease
  (CDCN/Castleman, FAST/Angelman), faith-based, food security, children's and
  family services, health, education, Florida-local (Orlando-based).
- Warm-intro employers: Dan worked at American Heart Association (AHA), American
  Cancer Society (ACS), and University of Miami. If a contact's public LinkedIn
  history shows any of these, Dan is a likely former colleague, a strong rapport
  angle worth flagging and (if clearly visible) referencing.
- Warm-intro partner: Michelle Boggs is Dan's business partner and Relate Group
  co-owner. If a contact is a 1st-degree LinkedIn connection of Michelle, she can
  make a personal intro. This is the MB 1st-Degree check below.

## Inputs (use sensible defaults, do not block)

Default to: as many solid leads as surface (usually 3 to 10), signals from the
last 24 to 48 hours (or the window Dan names), national scope, all U.S. nonprofit
sectors. If Dan already specified a number, window, or sector, use it. On a
scheduled autonomous run, proceed with defaults and note choices in the summary.

## Workflow

### Step 1: Find signals (tiered by buying intent)

Run 6 to 9 web searches, varying queries each run and appending the current month
and year. Cover these signal types, prioritizing Tier 1.

TIER 1 (highest intent, lead with these):
1. RFP / Seeking Consultant: a nonprofit issues an RFP or publicly seeks a
   development, capital campaign, advancement, or fundraising consultant. The
   single strongest signal: the org is literally shopping for what Relate Group
   sells. Check RFP aggregators, state nonprofit association boards, foundation
   and philanthropy job/RFP portals.
2. Open Dev Role / Departure: an open or newly vacated development leadership role
   (Director or VP of Development, CDO, Chief Advancement Officer), OR a
   development leader announcing a departure or retirement. An empty seat maps
   straight to interim and fractional leadership. Check Idealist, LinkedIn Jobs,
   Work for Good, and press ("stepping down," "to retire," "departing for").
3. Financial Distress: a nonprofit reporting a deficit, fundraising shortfall,
   budget gap, layoffs, or reserve draw. High urgency, strong fit for fractional
   leadership plus turnaround.

TIER 2 (solid):
4. Capital Campaign (early or stalled): a nonprofit exploring or announcing a
   feasibility study or campaign, OR a campaign reported behind goal or stalled.
   Prioritize pre-campaign and struggling, not ones already well underway with a
   firm engaged.
5. New CEO/ED: a new chief executive or executive director named. Strongest when
   the org has a visible development gap or a stated growth or fundraising mandate.
6. CRM Migration: a nonprofit announcing or hiring for a CRM migration or
   implementation (Virtuous, Bloomerang, Salesforce NPSP, Blackbaud).
7. Strategic Plan / Merger / Milestone: a new strategic plan, a merger, or a
   milestone or anniversary implying a funding push.

TIER 3 (context, lighter weight):
8. New CDO/Dev Lead: a new CDO, VP, or Director of Development named. Nuance: a
   just-filled seat LOWERS demand for fractional leadership but RAISES demand for
   project and advisory work (campaign, CRM cleanup, dashboards, coaching). Treat
   as project and advisory prospects.
9. Capacity Grant / Funding Cliff: a capacity-building or matching grant, OR a
   major grant ending or lost. A plain large grant win is LOW intent and usually
   skippable.

Do NOT treat a generic new board chair as a primary signal unless the new chair
arrives with a stated turnaround or fundraising mandate. Skip 990 revenue analysis
(it lags one to two years).

### Step 1B: LinkedIn / Sales Navigator job-change pass (run alongside web search)

A nonprofit leader's own LinkedIn job-change is the earliest, warmest version of
the New CEO/ED and New CDO/Dev Lead signals: they announce it themselves, before
press, in a celebratory moment ideal for a congratulations-led connect. Run this
in Dan's logged-in Sales Navigator (Chrome) every sweep, as a complement to web
search (web search casts the wider net across all US nonprofits; this is timelier
and warmer but skews to Dan's network).

- Saved search: Dan has a saved Sales Nav lead search (id 1995767946) with email
  alerts ON (weekly). It combines: the "Changed jobs" spotlight (Recent updates
  section), Current title = Chief Development Officer OR Chief Executive Officer
  OR Executive Director (add Chief Advancement Officer / VP of Development when the
  taxonomy allows), and Industry = "Non-profit Organizations". Open it from Saved
  searches, or rebuild those filters.
- 90-DAY vs 30-DAY: the "Changed jobs" spotlight is hardwired to the past 90 days
  (no 30-day toggle; the "years in role" filter only goes as granular as "less
  than 1 year"). To get a true ~30-day window, READ each result card's tenure
  ("X months in role | X months in company") and KEEP ONLY leads showing about 1
  month or less in BOTH role and company. This also strips noise: it drops
  internal promotions (low "in role" but high "in company") and the occasional
  stale "Recently hired" tag on someone who has actually been in the seat for
  years.
- Scale note: across all leadership titles this spotlight returns thousands over
  90 days, so it is NOT a log-everything list. Lean on the weekly alert email (it
  surfaces only NEW changes since last check, a small delta) and the ~1-month
  tenure filter, then prioritize by Relate Group fit (sector bench, Florida /
  Southeast, stacked signals).
- For each kept lead: a new chief executive or executive director is a "New
  CEO/ED" signal; a new CDO / VP / Director of Development is a "New CDO/Dev Lead"
  signal (project and advisory framing, not fractional). Then run the normal
  pipeline from Step 3 (dedupe) onward: log, auto-verify the profile, Michelle
  check, draft, and advance to Ready to Send.

### Step 2: Filter, with a required DEADLINE CHECK

U.S.-based nonprofits only. Skip for-profits, the grant-making foundation itself
(the recipient is the lead), government, and university central offices (a
university foundation or advancement office is fine). Only keep items with a real
org name and a working source URL. Never fabricate a lead to hit a number.
Quality over volume: 3 to 10 solid leads is normal; zero is fine on a slow day.

HARD EXCLUSION (large national orgs, never log, never surface): Do NOT log,
draft, or surface ANY lead (any title, any source, web search or Sales Navigator)
whose CURRENT employer is a large national federated health/disease charity or a
comparable national umbrella nonprofit that runs an in-house development team or
retains a national fundraising firm. Drop them silently at this step: do not log
them even as Low or Not a Fit. Seed exclusion list (extend as new ones appear):
American Heart Association, American Cancer Society, Blood Cancer United (formerly
Leukemia & Lymphoma Society), St. Jude Children's Research Hospital / ALSAC,
Alzheimer's Association, American Diabetes Association, American Lung Association,
Susan G. Komen, Cystic Fibrosis Foundation, Muscular Dystrophy Association, March
of Dimes, National Kidney Foundation, National Multiple Sclerosis Society, ALS
Association, JDRF / Breakthrough T1D, Crohn's and Colitis Foundation, Arthritis
Foundation, Epilepsy Foundation, American Red Cross, United Way Worldwide, Feeding
America (national office), Make-A-Wish America, plus similar national
disease/health charities and national umbrella nonprofits. The exclusion covers
the national org AND its regional divisions, chapters, and affiliates (for
example "Blood Cancer United Georgia-South Carolina").
NUANCE 1 (independent locals stay in scope): an independent local organization is
NOT excluded just because it belongs to a national network. A local food bank
that is a Feeding America member, an independent community health clinic, or a
local YMCA / United Way chapter that operates independently is still a valid
target. Exclude only the national org's own headquarters, divisions, and chapters,
not independent member organizations.
NUANCE 2 (target vs. warm angle): this excludes the org as a TARGET, meaning the
lead's CURRENT employer. It does NOT change the warm-intro rule. A contact at a
different, in-scope nonprofit whose PAST history includes American Heart
Association, American Cancer Society, or University of Miami is still a valued
rapport hook for Dan. Exclude on current employer; keep past employer as a warm
angle.

HARD EXCLUSION 2 (category exclusions, never log, never surface): Also drop
silently, regardless of fit, warmth, or signal strength:
- Pride / LGBTQ organizations of any kind (Pride chambers, Pride centers, LGBTQ
  advocacy or community orgs, etc.). Per Dan, this is a hard category exclusion.
- Chambers of commerce and trade / membership / professional associations. These
  are membership-funded business orgs, not donor-fundraising nonprofits, even when
  the contact holds a CFRE or a development title. (Examples already dropped:
  Powder Coating Institute, Tennessee Pride Chamber.)

DEADLINE CHECK (required, before logging): verify the date of the underlying
signal and any stated deadline. For RFPs and job/contract postings, find the
proposal due date, application close date, or posting date, and confirm the YEAR.
Web search frequently surfaces OLD cached RFPs and postings that read as current,
so do not trust freshness from the snippet alone. SIFT OUT and DO NOT LOG any
lead whose only path to engagement has already closed: a passed RFP or
application deadline, a contract already started or ended, or a posting more than
~60 days old with no stated deadline (treat as likely closed). The one exception
is a standing business-development angle that survives the deadline: if Relate
Group can pitch fractional or project support directly to the decision-maker
whether or not they hire internally, you may log it, but say so in Trigger Detail
and Action for Dan and flag that the posting has closed. When you cannot confirm
whether a deadline has passed, note the source date and the uncertainty in Action
for Dan rather than assuming it is open. New-leader signals (new CDO / CEO / ED)
and financial-distress signals are relationship windows, not deadline-bound, and
are exempt unless the news itself is stale (older than ~90 days).

### Step 3: Dedupe (and retire expired leads already on the board)

Read existing board items first (get_board_items_page or get_full_board_data on
board `18415579805`). Skip any org already on the board with the same signal type.
A genuinely different, newer signal for an existing org may be added (see the
stacked-signal rule). Each run, also re-check still-open RFP and posting leads
already on the board and retire any whose deadline has since passed (set Status
"Not a Fit" and note the passed deadline in Action for Dan; the Monday automation
moves it to the Not Pursuing group). Never move a lead backward along the pipeline.

### Step 4: Log each lead

create_item with name = organization name. Then change_item_column_values with
createLabelsIfMissing set to true.

COMPLETENESS GATE (required): every newly logged lead must have ALL of the
at-a-glance board columns filled, not just the deep text fields. These columns are
what shows on the board face, so a lead with only Trigger Detail / Insights /
Action filled reads as "almost blank." Before moving on from a lead, confirm each
of these is set: Signal Type, Source, Date Detected, Sector, Location, Fit
Priority, Status, MB 1st-Degree?, plus Trigger Detail, LinkedIn Profile, Outreach
Drafts, LinkedIn Insights, and Action for Dan. Do not leave any of them blank.

NEVER TOUCH THE PEOPLE COLUMN (`multiple_person_mm3wr7h`). Do not add, set, or
change it on any lead, ever. Dan owns and uses that column himself after
screening; leave whatever is there exactly as you found it. It is not part of the
completeness gate.

Field guide:

- Trigger Detail (`long_text_mm3v43dc`): 1 to 3 sentences. What happened, the
  person and role if known, and why it fits Relate Group. Name both signals if
  more than one is present.
- Source (`link_mm3vj8yb`): `{"url":"<url>","text":"<short label>"}`
- Date Detected (`date_mm3vbbqb`): `{"date":"<today YYYY-MM-DD>"}`
- Location (`text_mm3vf40b`): "City, ST"
- Signal Type (`color_mm3vbsjy`), one of: "RFP / Seeking Consultant",
  "Open Dev Role / Departure", "Financial Distress", "Capital Campaign",
  "New CEO/ED", "CRM Migration", "Strategic Plan / Merger / Milestone",
  "New CDO/Dev Lead", "Capacity Grant / Funding Cliff". If two apply, label the
  highest-intent one and note the other in Trigger Detail. (Board label note: open
  job postings have historically been labeled "Dev Job Posting" and public asks
  "Active Ask (Fractional/Interim)"; reuse existing labels.)
- Sector (`dropdown_mm3vj3nx`): a short vertical (Health, Health / Rare Disease,
  Education, Faith-Based / Human Services, Arts & Culture, Human Services, Human
  Services / Food Security, Animal Welfare, Disaster Relief / Human Services,
  Environment, International). Reuse existing labels where possible.
- Fit Priority (`color_mm3vjg41`): "High", "Medium", or "Low" (scoring below).
- Status (`color_mm3vgcvp`): "New" for a freshly logged lead (pipeline below).
- Action for Dan (`long_text_mm3wbvg2`): REQUIRED on every item, every run, no
  blanks. One or two sentences naming the channel and the one concrete next step
  (e.g. "Connect with <name> on LinkedIn using the drafted note," "Email
  <address> per the posting; Spark draft ready," "No action: RFP deadline
  (<date>) passed, archived"). You do the posting review; never write a vague "go
  review the posting." The message content lives in the Outreach Drafts column, so
  keep this field to the action.
- MB 1st-Degree? (`color_mm3w8mpp`): REQUIRED on every item, every run. Set in
  Step 5C. If the lead has a named person with a verified profile, this MUST be a
  definite Yes or No (run the check, never punt to Unknown). Use Unknown ONLY for an
  org-only lead with no named individual to check. Never leave blank.

Fit Priority scoring:
- High: ANY Tier 1 signal (RFP, Open Dev Role / Departure, Financial Distress),
  OR a STACKED signal (two or more triggers at one org), OR Florida / Southeast,
  OR a sector matching Relate Group's bench (health and rare disease, faith-based,
  education, children's and family services, food security).
- Medium: a single Tier 2 or Tier 3 signal at a relevant org that is larger, out
  of region, or a generic sector.
- Low: very large national institutions with in-house teams or retained national
  firms, interim appointments, or weak or indirect signals.

Stacked-signal rule: two or more signal types at one org = log ONE item, name both
in Trigger Detail, score it High.

### Pipeline statuses (`color_mm3vgcvp`)

- New: freshly logged, prep not yet complete.
- Ready to Send: fully prepped and queued, awaiting Dan's go (see Step 7).
- Connection Sent: connection request sent, awaiting acceptance.
- Connected: invitation accepted, ready for the first message.
- Followed Up: first message sent.
- Not Interested: person replied negatively or asked to stop.
- Not a Fit: Claude or Dan judges the prospect not a match (poor sector, very
  large institution, interim, expired/stale lead).
- Not Enough Info: no identifiable or verifiable decision-maker to pursue.

The three closing statuses (Not Interested, Not a Fit, Not Enough Info) trigger a
Monday automation that moves the item into the Not Pursuing group, so set them
accurately and promptly. Never move a lead backward (e.g. from Followed Up back to
Connected).

### Step 5: Identify and AUTO-VERIFY the contact (do the work yourself)

Every lead gets an identified, verified decision-maker. Do NOT park a lead as
unverified for Dan to confirm later. Take these steps before any fallback:

1. Identify the person. For a named-contact lead (new hire, or the poster), that
   is the named person. For an org-level lead (job posting, RFP, active ask, or a
   campaign with no named person), identify the org's current decision-maker (ED /
   CEO / President, or the relevant Chief Development / Advancement Officer) via
   WebSearch plus the org's website or leadership page. Confirm the CURRENT holder;
   titles change, so do not trust a stale search snippet (for example a former CEO
   now serving as CFO).
2. Verify the profile in Chrome. Load the Chrome tools (ToolSearch), open LinkedIn
   on Dan's logged-in browser (list_connected_browsers / select_browser, then
   tabs_context_mcp), search the person's name plus org, open the candidate
   profile, and confirm it is the same person: current org and title match, and
   career history or location is consistent with the signal. Use Chrome rather
   than WebSearch alone, because many profiles are not exposed to web search.
3. Set LinkedIn Profile (`link_mm3w4dpm`) only with a profile you CONFIDENTLY
   confirmed: `{"url":"<profile url>","text":"<person name>"}`. Disambiguate
   same-name people by org/title/location; choose only on an unambiguous match.
   Correct any name or spelling to match the verified profile, and update Trigger
   Detail / Action for Dan with what you confirmed.
4. Fallbacks, only after a genuine attempt: if no individual can be confirmed,
   leave LinkedIn Profile blank and set MB 1st-Degree "Unknown"; the lead stays
   "New" (it will not qualify for Ready to Send). Set Status "Not Enough Info"
   only when no decision-maker can be identified or verified at all.

#### Step 5B: LinkedIn Insights (rapport hooks)

In LinkedIn Insights (`long_text_mm3wmhcw`), from the public profile, do the
SHARED-EMPLOYER CHECK first: scan career history for American Heart Association,
American Cancer Society, or University of Miami. Any match makes Dan a likely
former colleague, a strong warm-intro angle, so state it plainly. Also note other
hooks: notable prior roles, a location match, shared sector, a recent post worth
referencing. Leave blank if nothing relevant.

#### Step 5C: Michelle Boggs 1st-Degree check (via Chrome, every lead)

Set MB 1st-Degree (`color_mm3w8mpp`). Whenever the lead has a NAMED person with a
verified LinkedIn profile (Step 5A), you MUST resolve this to a definite Yes or No.
"Unknown" is allowed ONLY when the lead is an organization with no named individual
at all (no person to check). A dropped filter, a private connection list, or a
3rd-degree profile are NOT reasons to use Unknown: the method below still returns a
definite answer, so use it.

A Yes means Michelle can make a warm intro. With the contact's profile open in
Dan's logged-in Chrome:
1. Run the shared-connections search built around DAN's network, not the lead's:
   navigate to a People search with keyword "Michelle Boggs", `network=["F"]`, and
   `connectionOf=["<the lead's profile URN>"]`. (Get the lead URN from the profile
   page.) The `network=["F"]` facet is REQUIRED: it constrains results to Dan's own
   1st-degree connections (the mutual-connections view) and is what makes the
   filter actually render. Without it LinkedIn silently drops the connectionOf
   filter and returns unfiltered junk, which is the false-read trap that produced
   bad "Unknown" results before. Confirm the "1st" facet is active on the results
   page before trusting them.
2. Because Michelle is one of Dan's 1st-degree connections, if Michelle is also
   connected to the lead she WILL appear in this search. So: if "Michelle Boggs"
   returns as a result, set "Yes"; if the filtered search (1st facet active)
   returns "No results found", set "No". A clean "No results" with the facet active
   is a definite No, not an Unknown.
This is read-only profile review: never send or accept anything here. Call out
every "Yes" in the summary so Michelle can be looped in.

### Step 6: Pick the channel, set the action, then draft (High and Medium only)

PICK THE CHANNEL FROM THE SOURCE. Read the posting for how the org wants to be
contacted. If they specify an email or application portal, that is the channel for
an APPLICATION. But for a business-development pitch to a decision-maker (the usual
case here), a LinkedIn connection request to the verified ED / CEO / CDO is right
even when the posting's apply-instructions target job applicants; use judgment.
Record the concrete next step in Action for Dan. If the channel is email, also
draft it in Dan's Spark (Spark draft tool, dan@relate.consulting) so it is ready
to review, and note in Action for Dan that a Spark draft is ready. Never send
email automatically.

Skip drafting for Low-fit leads (leave Outreach Drafts blank). For each High or
Medium lead, write as Dan, like a sharp marketing pro:
- Peer to peer, warm, specific to the trigger. No buzzwords, no hard sell. Soft
  CTA only (connect, open a conversation, a quick call). Always name "Relate Group".
- Match the offer to the signal: RFP / open seat / departure / financial distress
  -> interim and fractional development leadership. New CDO/Dev Lead -> project and
  advisory help (campaign, CRM, dashboards, coaching), not fractional. Capital
  campaign -> feasibility, planning, execution. CRM migration -> development
  operations and CRM optimization.
- If the profile shows a shared employer (AHA, ACS, University of Miami), you may
  warmly reference it ("fellow AHA alum"), only when clearly visible, kept natural.

A/B TEST (alternate two message variants, 50/50): every draft is one of two
variants so Relate Group can test which performs better. ASSIGNMENT: before
drafting, read the board's "Msg Variant" column (`color_mm3whszp`) across leads
that already have a variant, and assign whichever variant has FEWER so far (tie
goes to A); keep alternating within the run. Then TAG the lead: set
`color_mm3whszp` to "A (Current)" or "B (Alongside)" (createLabelsIfMissing:true),
and add the matching "Variant:" line in the Outreach Drafts column (per the format
in the save step below). BOTH variants must be fully personalized: insert the verified First Name,
exact title, and org. Never leave a [bracket] in a Ready-to-Send draft. Both are
em-dash free and say "Relate Group".

VARIANT A (current, matched to the signal):
A) CONNECTION REQUEST: about 300 characters max. Reference the trigger, establish
   Dan as a senior operator who has been in their seat, soft connect ask.
B) FIRST MESSAGE (after they accept): 3 to 5 sentences. Name the moment they are
   in, offer the matched Relate Group help (per the offer-to-signal mapping
   above), soft CTA.
For new-hire signals, address the named person. For job-posting / RFP / org-level
signals, address the verified ED / CEO. If you truly cannot identify the person,
use "[Name]" with a top line "Contact: confirm the ED/CEO name before sending"
(and the lead stays New, not Ready to Send).

VARIANT B (Alongside peer-group offering): What Alongside is, a free monthly peer
group Relate Group hosts on Zoom for nonprofit leaders (45 minutes once a month,
facilitated with the Gestalt method, fully confidential, cohorts for CEOs/EDs,
CDOs, and Peer-to-Peer National Leaders). Fill [First Name] and [title] from the
verified data; keep the connection note under 300 characters (trim the role line
if a long title pushes it over). Use these templates close to verbatim, changing
only the personalization:
A) CONNECTION REQUEST (about 300 characters max):
Hi [First Name], congratulations on your new role as [title]! I'm Dan, co-founder
of Relate Group. We host free monthly "Alongside" peer groups on Zoom for leaders
in your seat, to think through what's on your mind alongside people who've walked
it. Would love to connect.
B) FIRST MESSAGE (after they accept):
Thanks for connecting, [First Name], and again, congratulations on the new role.
I wanted to share a bit more about Alongside, a peer group Relate Group hosts for
leaders like you. We run cohorts specifically for CEOs/Executive Directors, Chief
Development Officers, and Peer-to-Peer National Leaders, so you're always sitting
with people facing the same realities you are.
Here's how it works:
- 45 minutes, once a month, on Zoom. That's the entire commitment.
- Facilitated by our team using the Gestalt method. Each meeting, a member shares
  what's keeping them up at night, the group asks clarifying questions, and then
  peers reflect on similar experiences they've navigated. You leave with
  perspective from people who actually get it.
- Fully confidential. What's said in the group stays in the group.
If you'd like to join the next session, just reply here or email me your preferred
email address at dan@relate.group and I'll send the calendar invite.
I'd genuinely enjoy having you in the room.
Warmly,
Dan Thorpe
Co-Founder, Relate Group
(Email note: dan@relate.group is per Dan's finalized copy; confirm vs.
dan@relate.consulting and use whichever Dan confirms.)

Save the draft into the OUTREACH DRAFTS column on the item, NOT a Google Doc. (We
no longer generate Docs; the draft lives on the lead's row.) Set Outreach Drafts
(`long_text_mm3xb1qd`) with `change_item_column_values` to exactly this format:

```
=== OUTREACH DRAFTS ===
Variant: <A (Current) | B (Alongside)>

CONNECTION REQUEST (max ~300 chars):
<the connection note text>

FIRST MESSAGE (after they accept):
<the first message text>
```

Keep the "=== OUTREACH DRAFTS ===" header line verbatim: the send and acceptance
flows locate the draft by it. Leave the old LinkedIn Draft link column
(`link_mm3vnpss`) blank for new leads; it is deprecated and no Doc is created.

### Step 7: Advance qualifying leads from "New" to "Ready to Send"

After a lead is fully logged and prepped, do not leave it at "New". Take every
remaining prep action and advance it to Status "Ready to Send", so Dan only has to
review and give the word. NOTHING is sent here: "Ready to Send" means "fully
prepped, awaiting Dan's go." Dan reviews and gives the final word before any
connection request goes out (sends happen through Chrome with him present).

A lead QUALIFIES for Ready to Send only when ALL are true:
- Fit Priority is High or Medium (Low never advances; it has no draft).
- A VERIFIED individual contact in LinkedIn Profile (a confirmed person, not an
  org page and not a "[Name]" placeholder).
- The Outreach Drafts column filled with a real, sendable connection request and
  first message (no "[Name]", names "Relate Group", em-dash free).
- Action for Dan is filled.
- MB 1st-Degree is set to Yes / No / Unknown.
- The signal is current (passed the Step 2 deadline check).
When it qualifies, set `color_mm3vgcvp` to "Ready to Send" and update Action for
Dan to a ready-to-go instruction. If anything above is missing, leave it "New" and
say what is missing in Action for Dan. Closing statuses and Low fit never advance.
Never move a lead backward.

### Step 8: Summary

Concise summary (no em-dashes): leads added, breakdown by signal type and tier,
High-priority and stacked-signal leads by name, drafts created, profiles verified,
any shared-employer (AHA / ACS / University of Miami) angles, every MB 1st-Degree
"Yes" so Michelle can be looped in, leads sifted out for passed deadlines, and any
set to Not a Fit or Not Enough Info. State which leads advanced to "Ready to Send"
and which are still "New" and why. Report the A/B split this run (how many got
Variant A vs Variant B) and the running board-wide A/B totals. Remind Dan nothing
was sent: Ready to Send leads are queued and awaiting his final word.

## Add-on sweep: Active Asks (public LinkedIn posts and demand signals)

Optional sweep for nonprofit leaders publicly ASKING for fractional, interim, or
development help, the warmest signal of all. Results go into the "Active Asks"
group (`group_mm3wstq7`), not the main group.

Honest constraint: LinkedIn content search is noisy (dominated by consultants
advertising their own services). Quality over quantity; careful classification is
the whole game. Logged-in browser required.

1. Run LinkedIn CONTENT searches (linkedin.com/search/results/content/) for
   buyer-intent phrasing, filtering "From member" to 1st connections where
   possible. Query ideas (vary): "anyone recommend" interim development; "we are
   between development directors"; "seeking recommendations" fundraising; "our
   development director is leaving"; "looking to bring on" fractional; hashtags
   #fractionalleadership, #interimleadership, #nonprofitfundraising.
2. For EACH candidate, READ and CLASSIFY the poster. LOG only if the poster is a
   nonprofit ED, CEO, CDO, VP of Development, or board member genuinely asking for
   help (or saying their org needs it). SKIP consultants advertising services,
   generic hiring posts, and seller content.
3. Parallel demand sweep of job/RFP boards (Idealist, Work for Good, Chronicle of
   Philanthropy Jobs) filtered to interim / fractional / contract / part-time
   development roles. RECENCY CHECK is critical: only log postings still OPEN
   (check the deadline and that the listing is live, not unpublished). Expired or
   pulled postings are not pursued: mark Not a Fit, note why in Action for Dan, and
   they move to the Not Pursuing group.

For each genuine ask, create_item in the Active Asks group with: name; Trigger
Detail (one line plus a short quote of the ask); Source; Signal Type "Active Ask
(Fractional/Interim)"; Location; Fit Priority usually High; Status New; verified
LinkedIn Profile; Insights (role, org, why it is warm, plus the MB 1st-degree
check); Action for Dan; then write a warm reply referencing their exact ask
(not a generic pitch) into the Outreach Drafts column. Never comment or message automatically; draft
only, then advance to Ready to Send per Step 7.

## Consolidation note

This canonical file replaces both prior sweep skills. Keep ONE sweep skill using
this content and retire the duplicate so the instructions stop diverging. Point
the scheduled daily task at this same skill. The acceptance-check workflow
(detecting LinkedIn acceptances and queueing first messages) stays a SEPARATE
skill ("relate-linkedin-acceptance-check") and is not merged here.
