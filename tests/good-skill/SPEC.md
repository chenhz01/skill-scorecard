# SPEC: daily-report-scout

> Status: ACTIVE · Version: 1.2 · Owner: example
> canary: CANARY-ZS-EX-20260915-EXAMPLE

Worked, desensitized example: a scouting skill that fetches a public leaderboard once per run and produces an auditable triage report. Shows every section filled with falsifiable content.

## 1. Purpose (Why)

Turn a fast-moving public leaderboard (e.g. GitHub Trending) from "news to skim" into an adversarially verified triage artifact: every entry is checked against a primary source before it is graded. It is *not* meant to make investment or adoption decisions — it only grades and flags; humans decide.

## 2. Interface (What goes in, what comes out)

```
inputs:
  - type: image | url
    schema: leaderboard screenshot (names often OCR-garbled) or the canonical URL
outputs:
  - type: html_report
    schema: dated report file, one row per entry, grade in {P0, P1, P2, RED-LINE}
  - type: log_entry
    schema: appended to a dated log file, ≤200 chars, what ran and what failed
confidence: 1.0 when 100% of entries are verified against the primary source; drops one
  grade of confidence per unverified entry, and unverified entries are labeled inline
degraded: when the primary source is unreachable, emit the report with a "degraded:
  unverified" banner and zero grades — never invent entries
```

## 3. Procedure (Steps that cannot be skipped)

1. Fetch the canonical leaderboard page; extract the full entry list (count = N).
2. Cross-check each entry's name against the primary source API; correct OCR/name errors and log each correction.
3. For each entry fetch hard metrics: stars, license, last-push date, archived flag (two parallel batches, timeout 20s each).
4. Grade each entry against the rubric; entries matching any red-line criterion are graded RED-LINE regardless of metrics.
5. Render the report; verify every row carries: source-checked flag, license, and grade rationale.
6. Append the log entry; state coverage as `verified/total`.

## 4. Acceptance (Falsifiable)

- [ ] Report contains exactly N rows, where N equals the entry count fetched in step 1
- [ ] Coverage line states `verified/total`, e.g. `20/20 verified` — any value below total must show which entries are unverified and why
- [ ] Every grade cites at least one hard metric (stars / license / last-push)
- [ ] Every RED-LINE grade cites the exact criterion that triggered it
- [ ] Totals in the report summary match a recount from the raw fetched data (must match, else re-run step 1)
- [ ] License values come only from the primary source API field — never guessed from the README

## 5. Guards (Red lines)

- Outward-facing actions (posting, emailing, publishing the report): require explicit human confirmation — never automated.
- Must never auto-install, auto-purchase, or auto-execute anything a report entry describes.
- Must never present an unverified entry as verified, even when verification "obviously" would pass.
- On fetch failure: stop after 3 attempts, emit degraded report — never loop, never fabricate.

## 6. Honest limits

- Cannot verify claims made only in a project's own README (marketing text) — those are labeled as self-reported.
- OCR on screenshots misreads names; the correction step reduces but does not eliminate this, so every correction is logged.
- Grades encode the rubric's values (risk-averse, compliance-first); they are not universal quality scores.
- Evidence status: the pipeline has run 7 times in production; all counts in this spec are protocol-level descriptions of behavior, not benchmarks.
