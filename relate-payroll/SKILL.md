---
name: Relate Payroll
description: >-
  Run Relate Group's biweekly payroll close in the "Relate Payroll" Google Sheet.
  Pulls hours for the hourly team (Emily, Alicia, Lila, Bailey) from their
  individual "Time Log" Google Sheets, carries the salaried team (Michael,
  Dawson, Amy, Fuad) forward at their set amounts, adds a new pay-period tab,
  and fills it in. Use this whenever Dan says it's a new pay period, asks to
  "run payroll," "do the payroll," "start the new pay period," "add a payroll
  tab," "update the payroll sheet," "carry over the salaries," "pull everyone's
  hours," or wants payroll hours summarized — even if he doesn't name the
  files. Also trigger on rate changes ("update so-and-so's rate") and on
  "how many hours did the team work this pay period."
---

# Relate Payroll

This skill runs the biweekly payroll close for Relate Group. It reads time logs,
builds a fresh pay-period tab in the master payroll sheet, and fills in hours and
amounts. Reading is done with the Google Drive connector; **writing must be done
in the browser (Claude in Chrome)** because the Drive connector can read Google
Sheets but cannot add tabs or edit cells.

> Note: This public copy is redacted. Actual pay rates, salary amounts, bonus
> figures, and Google Drive file IDs are intentionally omitted. Pull live values
> from the most recent tab of the master Relate Payroll sheet at run time.

## The cast (verify against the sheet each run — people and rates change)

Salaried (paid a fixed amount each period, entered as Rate with Hours = 1):

- **Michael** — set biweekly amount
- **Dawson** — set biweekly amount
- **Amy** — set biweekly amount
- **Fuad** — set biweekly amount

Hourly (paid Rate × Hours worked):

- **Emily** — hourly rate
- **Alicia** — hourly rate
- **Lila** — hourly rate
- **Bailey** — hourly rate

The master sheet's most recent tab is always the source of truth for current
rates and salaries — read it before assuming anything.

## Key files

All live in Dan's Google Drive (owner dan@relate.consulting). Find them with the
Drive connector's `search_files` by title; capture the file IDs at run time.

| File | Purpose |
| --- | --- |
| Relate Payroll | Master payroll workbook, one tab per pay period |
| Emily - Time Log (2025-26) | Emily's hours |
| Alicia - Time Log (2025-26) | Alicia's hours |
| Bailey - Time Log (2025-26) | Bailey's hours |
| Lila - Time Log (2025-26) | Lila's hours |

## How the pay calendar works

Pay periods are **two weeks (biweekly)**, running Monday-to-Sunday and ending on a
Sunday. The **pay date is the Friday five days after the period ends** (e.g., a
period ending a Sunday pays the following Friday). When a new period starts, the
period you're closing is the one that **just ended** (yesterday, if it's the first
day of the new period).

## How the Time Log sheets are laid out

Each person's Time Log is a single Google Sheet with stacked blocks, **newest at
the top**. For every pay period there is a summary header:

```
| <Name>                       |                   | Total Hours: |
| Dates: <start> to <end>      | Pay Date: <date>  |   <total>    |
| Dates: <next start> to <end> | Pay Date: <date>  |   <total>    |
```

…followed by a daily table (`Date | # of Hours | Description`). To get someone's
hours for the period you're closing, read the **Total Hours** value on the
summary row whose `Dates:` range matches the period. It's worth a quick glance at
the daily rows to confirm the total (sum the `# of Hours` column), since the
summary is hand-keyed. Blank or zero means they logged no time — enter 0.

## How the master Relate Payroll sheet is laid out

One worksheet **tab per pay period**, and **tabs are named by the pay date**
(e.g., `M-DD-YY`). Columns:

| A Name | B Rate | C Hours | D Amount | E Bonus | F Payroll |

- **D (Amount) is a formula:** `=B*C` (Rate × Hours).
- **F (Payroll) is a formula:** `=D+E` (Amount + Bonus).
- The totals row holds column sums of Amount, Bonus, Payroll.

Because D and F are formulas, you only ever type into **B (Rate)**, **C (Hours)**,
and **E (Bonus)** — the rest recalculates.

Salaried people are entered as Rate = their set biweekly amount with Hours = 1, so
Amount equals their salary. Hourly people are entered as Rate = hourly rate with
Hours = hours worked.

## The workflow

### 1. Figure out the period being closed
Identify the two-week period that just ended and its pay date (period end + 5 days,
landing on a Friday). This pay date becomes the new tab name.

### 2. Pull the hourly hours
With the Drive connector, `read_file_content` on each of the four Time Log files.
For each person, read the Total Hours for the matching `Dates:` range; sanity-check
against the daily rows. Record Emily, Alicia, Lila, Bailey.

If Dan only wants a summary of hours, stop here and report a by-name list plus the
total. Otherwise continue.

### 3. Open the master sheet in the browser
The Drive connector can't write, so open the Relate Payroll sheet with Claude in
Chrome. Read the most recent tab to confirm current rates, bonuses, and the column
layout before changing anything.

### 4. Duplicate the latest tab and rename it
Right-click the most recent tab → **Duplicate**. Double-click the new
"Copy of …" tab and rename it to the **new pay date**. Duplicating preserves the
`=B*C` and `=D+E` formulas so you only edit inputs.

### 5. Carry the salaried team forward with NO bonus
Leave the salaried Rates and Hours (=1) as carried over. **Zero out every Bonus
cell (column E)** unless Dan explicitly says someone is owed a bonus this period —
bonuses do not repeat automatically and a duplicated tab still shows last period's.
This is the single easiest thing to get wrong, so always confirm column E is $0
across the board after the carry-over.

### 6. Weave in the hourly hours
Type each hourly person's hours into column C (Emily, Alicia, Lila, Bailey).
Amount and Payroll update themselves. Leave hourly Rates as-is unless told to
change them.

### 7. Verify
Check that the totals row recomputed, every Bonus is $0 (or matches an explicit
instruction), and each hourly Amount equals Rate × Hours. Spot-check one salaried
row equals its set amount with no bonus.

## Rate changes

When Dan says to update someone's "ongoing" rate, edit column B on the **current**
(newest) tab. That carries forward to future periods automatically since each new
tab is duplicated from the latest one. **Do not** touch historical tabs — they must
keep the rate that was in effect for their period. Mention the prior tab still shows
the old rate so Dan knows it was left intentionally.

## Reminders

- Reading is via the Google Drive connector; writing tabs/cells requires Claude in
  Chrome. Don't try to edit a Google Sheet through the Drive connector.
- Bonuses never carry. Default every Bonus to $0 and add one only on explicit
  instruction. Flag any bonus you cleared so Dan can re-add it if still owed.
- The newest master tab is the source of truth for current rates — read before you
  assume.
- Confirm before committing irreversible-looking edits, and never change sharing,
  delete tabs, or move money.
