# Example: full blueprint for a wholesale bakery's order changes

> **Fictional scenario.** Quarry Lane Bakery, its accounts, its numbers and its workflow are invented for this example. They are not drawn from any real company, client or employer. Any resemblance to a real business is coincidental.

This is a full ProductizeAI blueprint, run end to end on one workflow. The interesting parts are where the method says no: it recommends a process fix before any AI, turns down the agent the owner asked for, and leaves a forecasting idea on the shelf because the data can't support it yet.

---

## Input

What the user pasted, followed by "Productize this. Full blueprint."

> Quarry Lane Bakery is a wholesale bakery with one production site. We supply 60 cafés and small grocers with about 40 products (loaves, rolls, pastries). Every account has a standing order by weekday.
>
> Accounts change their orders all the time: more croissants for a weekend, skip Monday, a new product for a week. Changes arrive by email, by text to the office phone, by voicemail, and in a shared spreadsheet that about 15 accounts edit directly. We get roughly 90 change messages a week.
>
> The cut-off is 1 pm for the next morning's bake. Our office coordinator reads everything and keys the changes into the production sheet. The head baker prints it at 2 pm and mixing starts at 3 pm. The coordinator spends about 10 hours a week on this.
>
> Last month we reviewed four weeks of credit notes: 14 of roughly 360 changes went wrong. 6 were late changes handled inconsistently (sometimes accepted, sometimes not), 5 were missed or misheard texts and voicemails, and 3 were typos or overwritten cells in the shared spreadsheet.
>
> About 20 of our accounts are small cafés whose owners say they will keep texting or calling no matter what we build.
>
> What I want: an AI agent that reads every message and updates the bake plan automatically, so the coordinator can stop doing this.

---

## Recommendation

**Fix the intake before adding AI.** Replace the shared spreadsheet with a per-account change form that enforces the cut-off, and write down one late-change policy. Then, for the accounts that keep texting, emailing and calling, add AI-drafted changes that the coordinator approves in one click. Do not build the autonomous agent yet.

**Decision:** validate first, then a controlled pilot of the AI step.
**Confidence:** medium. The process fix rests on the supplied credit-note review, which ties 9 of 14 errors to causes a form and a policy remove. The AI step rests on an assumption that extraction accuracy will be good enough on short, informal messages. That has not been measured yet, and the first experiment exists to measure it.

---

## Brief and evidence

| | |
|---|---|
| **User** | Office coordinator (does the work); head baker (consumes the output); account owners (send the changes) |
| **Job** | Every morning, each account receives the quantities it last asked for, with the least coordinator time |
| **Friction** | Four intake channels, one person re-keying, an inconsistently applied cut-off |
| **Baseline** | Manual reading and keying, about 10 hours a week, about 1 error per 25 changes |
| **Business goal** | Fewer credit notes and remakes; coordinator time freed for account work |
| **Constraints** | Production sheet locks at 2 pm, and dough mixed at 3 pm can't be unmade |

**Supplied facts:** 60 accounts, about 40 products, about 90 change messages a week, 1 pm cut-off, 2 pm print, 10 coordinator hours a week, 14 errors in about 360 changes with the cause breakdown above, about 20 accounts that won't adopt a form.

**Inferences:** late-change errors (6) come from a missing policy, not from reading mistakes. Spreadsheet errors (3) come from many people editing one shared document. Neither needs a model.

**Assumptions:** the 20 non-adopting accounts send roughly a third of change volume. The four-week sample is representative, with no holiday weeks in it.

**Missing evidence:** average value of a credit note plus remake cost; coordinator's loaded hourly rate; the share of volume from the 20 non-adopting accounts; whether voicemail audio is retained.

---

## Before and after

**Before.** A café owner texts the office phone at 12:40: "can we do 18 croiss tmrw instead of 12, and no rye". The coordinator sees it at 1:15, after a run of emails. It's past cut-off. Last week a similar text was accepted; this one is keyed anyway, and the rye line is missed. The café gets 18 croissants and a rye loaf it didn't want, and asks for a credit.

**After, form accounts.** The account opens its change link, which is prefilled with tomorrow's standing order. The owner sets croissants to 18 and rye to 0. After 1 pm the form shows the next open bake date instead and says why. No one re-keys anything.

**After, text accounts.** The same text arrives. Within a minute the coordinator's review queue shows two proposed changes, each with the exact words it came from highlighted: *croissant 12 → 18 ("18 croiss tmrw instead of 12")* and *rye 1 → 0 ("no rye")*. Both are marked "before cut-off" because the message timestamp is 12:40. The coordinator clicks approve. The production sheet updates and the café gets a templated confirmation listing both lines.

**Removed:** the shared spreadsheet, manual keying for form accounts, case-by-case cut-off judgment.
**Retained:** coordinator approval of every unstructured change, the 2 pm lock, the head baker's printed sheet.
**Exception path:** anything the system can't place (an unknown product, an ambiguous date, unparsed text left in the message) appears in the queue as "needs a person", with the original message, and is never written automatically.

---

## Architecture

| Step | User value | Mechanism | Interaction | Autonomy | Context/tools | Why this beats the baseline |
|---|---|---|---|---|---|---|
| Change form for form accounts | Account sets exact quantities itself | Deterministic software | Form, prefilled | Customer acts directly | Catalog, standing orders | Removes keying and spreadsheet overwrites; no model needed |
| Cut-off enforcement | Same answer every time | Rules | Background | Execute and notify (late requests get a policy reply) | Clock, bake calendar | Removes the 6 inconsistent late-change errors by policy, not prediction |
| Collect texts, emails, voicemails | One queue instead of four inboxes | Deterministic + speech-to-text | Background | Execute and notify | Mail, SMS gateway, voicemail audio | Nothing gets missed because it arrived in the wrong place |
| Extract proposed changes | Coordinator reviews instead of keying | Generative AI | Draft | Draft | Message, account's standing order, catalog | Reads informal text ("croiss", "skip Mon"); rules can't |
| Validate each proposal | Bad proposals never reach approval | Deterministic software | Background | Execute and notify (flags only) | Catalog, bake calendar, quantity bounds | Checks the model's output in code instead of trusting it |
| Approve and write | Change lands on the sheet | Deterministic software | One-click approval | Execute with approval | Production sheet | Coordinator keeps the decision and loses the typing |
| Confirm to customer | Customer sees what was booked | Template | Sent on approval | Execute with approval (same click) | Approved change | Customer catches a misread before the bake |
| Holiday demand forecast | Suggest quantities before holidays | Predictive ML | Suggestion | **Not now** | Order history | One year of history covers each holiday once; retain current approach |

**Why not an agent.** The route never varies: collect, extract, validate, approve, write, confirm. That's a fixed pipeline with one model call in it, which is ordinary automation. An agent adds planning and tool choice that this workflow doesn't need, plus failure modes it doesn't have today.

---

## Design the first recommendation

**Step:** extract proposed changes from texts, emails and voicemail transcripts.

| | |
|---|---|
| **Trigger** | A new message in the unified queue from a known account's phone number or email address |
| **Inputs** | Message text (or transcript), timestamp, the account's standing order, the catalog |
| **Expected output** | Zero or more proposed changes: account, product, bake date, new quantity, and the exact source span from the message |
| **Permissions** | Read messages and standing orders. Write only to the review queue. No write access to the production sheet, and no outbound messages |
| **Human role** | Coordinator approves, edits or rejects each proposal |
| **Fallback** | Unknown sender, unknown product, ambiguous date or unparsed remaining text: "needs a person", with the original message |
| **Dependencies** | SMS and voicemail export, speech-to-text, catalog with the nicknames accounts really use ("croiss", "the dark one") |

**Validation in code, not by the model:**
- The source span must appear verbatim in the message. If it doesn't, the proposal is discarded. This stops the model inventing a change and citing it convincingly.
- The product must exist in that account's catalog, and the bake date must be an open bake date.
- A new quantity more than double, or less than half, the standing quantity is flagged for a closer look. This is the main defence against "fifteen" heard as "fifty".
- The same account, product and date proposed from two messages are merged and shown together, so an account that emails and then texts the same change isn't booked twice.

**Must never:** write to the production sheet without approval; change a locked bake date; create products or accounts; send a message the coordinator didn't approve.

**Auditability:** each sheet change records the source message, the proposal, the validator results, who approved it and when.

### The agent the owner asked for

The request was an agent that updates the bake plan automatically. The extraction error rate is unknown, and a wrong quantity becomes irreversible at 3 pm when the dough is mixed, so the blueprint stops at **Draft, then execute with approval**.

Moving one narrow class up to **execute and notify** would take evidence like this: single-product, absolute-quantity changes ("18 croissants") from email, for accounts with a clean history, with measured precision over a few hundred reviewed proposals at or above a threshold the owner sets. Voicemail stays on approval until the transcription error rate is known separately.

---

## Evidence plan

| Hypothesis | Baseline | System/model measure | User outcome | Business KPI | Guardrail | Proposed decision criterion |
|---|---|---|---|---|---|---|
| The form and late-change policy remove most intake errors | 14 errors in about 360 changes (supplied) | Share of change volume through the form | Accounts get what they asked for | Credit notes per 100 changes | Accounts dropping their standing orders | Proposed: late-change and spreadsheet errors near zero over 4 weeks |
| AI drafts cut coordinator time on unstructured messages | About 10 h/week total (supplied); share on unstructured messages unknown | Field-level precision and recall of proposals; share needing edits | Coordinator time per message | Coordinator hours a week | Missed-change rate must not exceed baseline | Proposed: recall at least as good as manual keying in shadow mode |
| Validation catches the model's bad proposals | None | Share of wrong proposals flagged before approval | Fewer wrong approvals | Credit notes from unstructured channels | Approval fatigue (time per approval rising) | Proposed: no unflagged quantity error over the pilot |
| Confirmations surface misreads early | None | Replies disputing a confirmation | Account fixes errors before the bake | Remakes | Confirmation volume annoying accounts | Proposed: track only; no threshold yet |

**Offline cases:** replay the four weeks of about 360 historical change messages, scored against the corrected production sheets.
**Adversarial and edge cases:** relative changes ("half as much rye"), several changes in one message, "Friday" sent on a Friday, product nicknames, a cancellation followed by a reversal, voicemail with background noise.
**User test:** the coordinator reviews a day of proposals and records time per approval and every edit.
**Pilot exposure:** two weeks of shadow mode, with the coordinator keying manually as today while the system proposes in parallel, then text and email accounts only.
**Rollback trigger:** any wrong quantity that reaches the production sheet without a validator flag returns the pilot to shadow mode.

---

## Economics

```
Monthly run cost = attempts × (transcription + inference)
                 + review hours × loaded hourly rate
                 + operations/support
```

| Input | Value | Status |
|---|---|---|
| Change messages | about 390/month | Supplied (90/week) |
| Share still unstructured after the form | about 1/3, so about 130/month | Assumption; measure in the first month |
| Attempts | messages × (1 + retry rate) | Retry rate unknown |
| Inference and transcription per message | Short messages; likely small next to review time | Unknown; get a vendor quote |
| Review time per proposal | Unknown | Measure in the user test |
| Loaded hourly rate | R | Missing |
| Cost of one error (credit + remake) | C | Missing |

**Net capacity benefit** = current keying time on unstructured messages − (review + correction + exception handling time). Time saved is capacity for account work, not a cash saving unless paid hours go down.

**Where the money is.** At this volume the model cost will almost certainly be small. Review time (R) and error cost (C) decide the case. Most of the error reduction (9 of 14) comes from the form and the policy, which need no model at all. The AI step has to earn its place on the remaining 5 errors and the remaining keying time.

**Sensitivity.** If the share of proposals needing an edit is high, review approaches keying time and the AI step stops paying for itself. Show cost per correctly booked change for manual keying and for AI draft plus approval, at edit rates of 10%, 30% and 50%, once R is known.

**Implementation, separately:** the change form, the unified inbox, the review queue and the production-sheet integration. Get an estimate before the pilot.

---

## First experiment

**Scope:** offline replay only. No customer exposure and no writes.
**Hypothesis:** AI extraction plus code validation reproduces the corrected production-sheet changes from the 360 historical messages with recall at least as good as manual keying, and flags every quantity error it makes.
**Comparator:** the manual baseline from the same four weeks (14 errors).
**Evidence needed:** field-level precision and recall; share of proposals flagged; share sent to "needs a person"; and separately, transcription error on the voicemails.
**Proposed pass/fail:** to be set with the owner before the replay runs, not after.
**Owner role:** operations lead, with the coordinator labelling disagreements.
**Dependencies:** message and voicemail export, corrected production sheets, catalog nicknames.
**Run the form first.** The form and policy can go live in parallel, because they don't depend on the replay result.

**The question that could reverse this.** How much change volume will the 20 hold-out accounts actually send once everyone else is on the form? If the form takes more than about 90% of volume, the remaining messages are cheaper to key by hand, and the AI step shouldn't be built at all.
