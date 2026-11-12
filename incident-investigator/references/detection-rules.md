# Detection Rules

Apply these rules only to user-provided synthetic CSV logs. They produce justified alerts for review, not forensic conclusions.

## Required conventions

- Check the CSV columns and timestamp format before analysis. Rules use `timestamp`, `ip`, `user`, `event`, and `result`; token checks also use `detail`.
- Parse timestamps and sort records chronologically before evaluating time windows. Preserve original CSV row numbers for evidence; the header is row 1.
- Time windows are inclusive at both ends: elapsed time must be `<= 5 minutes`.
- Match `event` and `result` values exactly as written below. Do not count other event types as login failures.
- Do not silently remove duplicate records. If duplicates cannot be identified unequivocally, report that limitation rather than inventing a deduplication rule.
- Never access the network or look up IP addresses. These exercise logs use documentation-only addresses; do not treat them as real infrastructure.

## Rules

### Probable brute force

Alert when at least 6 records have `event=login` and `result=failed` from the same IP toward the same user within a 5-minute window. Cite the rows that meet the threshold.

Separately note whether a `login/success` from that IP toward that user appears within the 5 minutes after the qualifying failed-login sequence. Cite its row and timestamp. Do not imply that the success was caused by the failed attempts.

### Probable password spraying

Alert when records with `event=login` and `result=failed` from one IP target at least 5 distinct users within a 5-minute window. Cite the failed-login rows and identify the distinct users without treating the pattern as proof of intent.

### Potentially exposed token

Alert when a `detail` value contains `token=`. Cite the original row, but never print the token value. Replace it with `[REDACTED]` in any excerpt, table, or summary.

### Unapproved role change in the log

Alert when a record has `event=role_change`, `result=success`, and `approved=false`. Cite the original row. Describe only what the record says; do not infer who was responsible or whether the change was malicious.

## False positives and non-matches

- Three isolated failed logins do not meet the probable brute-force threshold.
- A `healthcheck` is not a `login/failed` event and must not count toward either login threshold.
- A record that does not meet the exact event, result, identity, distinct-user, or time-window criteria is not a match for that rule.

For every report, distinguish observed records from interpretation and disclose relevant data-quality or duplicate-record limitations.
