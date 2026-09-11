# Pull Recipes: Bank Side and QBO Side

Mechanics for the two pulls in Steps 2 and 3. The call-budget rule, the
buffer definition, the ending-balance-comparison requirement, and the
stop-on-error conditions live in the SKILL.md body, not here.

## Bank-Side Pull (Step 2)

Nothing in this skill loops per transaction. Nothing in this skill re-pulls
a report. A connected source may paginate internally for a high-volume
account. Follow its pagination in that case, rather than assuming a single
page. That pagination is the source's own concern. It is not a reason for
this skill to make more top-level calls.

Use the source and the account that you resolved in Step 1. Pull every
transaction dated inside the period, plus the buffer defined in the body.

**Apply the connector's own status or completion filter before matching,
if its instructions define one.** For example, `financial-pulse-mercury.md`
requires a filter on `listTransactions` to the completed `sent` status. A
pending, failed, reversed, cancelled, or blocked Mercury transaction never
posts to QBO. Without the filter it becomes a false bank-only discrepancy,
or a spurious proposed match. Apply the equivalent completed or posted
filter for whichever connector you use, per its own documented statuses.

Capture these fields for each transaction. Capture the date, the amount,
and the payee or description. Capture a stable transaction ID or reference
if the source provides one. Capture whether the amount is signed as a
debit or withdrawal, or as a credit or deposit. Step 4 (see
`matching-rules.md`) explains why the sign matters before matching.

## QBO-Side Pull (Step 3)

Pull these for the same period with the QuickBooks Online MCP:

- **Bank register**: the QBO-side transactions posted to the bank account
  that you reconcile
- **Bank account balance**: the ending balance of the QBO bank account as
  of the period end date
- **Petty cash total**: the petty cash account balance and activity for
  the period, from the QBO ledger

Also get the **bank statement's ending balance** for the same period end
date. Ask the bookkeeper for it. Read it from the bank-side source instead
if that source states one.

Capture the date, the amount, the payee, and the account for each register
line.
