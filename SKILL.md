---
name: upwork-scout
description: Scan fresh Upwork jobs in the user's own logged-in browser, screen them against her criteria and red flags, read the client's history, and bring back a short shortlist. The user always decides and writes every proposal herself. Use when the user says "upwork round", "סבב אפוורק", or asks to find Upwork jobs worth applying to.
---

# Upwork Scout — job round

You help a freelancer spend her Connects only on jobs worth them.
You search, screen and read. **She decides, writes and sends every proposal.**

## Before you start

1. Read `config.yaml`: who she is, what she does, the searches, the filters, the hard no's.
2. Read `job-log.csv` so you don't bring a job that was already reviewed.
3. Read the files in `examples/` to see how she judged past jobs.

## The round

1. **Search.** In the user's own browser session, run each search in `searches`, sorted by most recent, payment-verified only:
   `https://www.upwork.com/nx/search/jobs/?sort=recency&payment_verified=1&q=<query>`
   Add `&duration_v3=week,month` when `prefer_projects` is true.
2. **First screen from the list.** Drop anything older than `max_age_hours`, anything with a client rating at or below `min_client_rating`, anything with client spend at or below `min_client_spent`, and anything with 50+ proposals.
3. **Open the survivors** and read the full post and the "About the client" box.
4. **Screen each job:**
   - **Fit (yes/no):** can she honestly meet every "must have"? Does a preferred location or language exclude her? Is it a project, not a long-term role (if `prefer_projects`)?
   - **Interest:** is the work substantive and inside `interested_in`? Budget is not a filter unless `min_budget` is set.
   - **Competition:** skip if the client is already interviewing `max_interviewing`+ people.
   - **Hard no's:** skip anything in `never`.
   - **Red flags** (always skip, see below).
5. **Read the client's history** for every job that passes: past reviews to and from freelancers. Pull out what the client values and what went wrong before.
6. **Flexibility:** a job that is an especially strong fit but misses one numeric filter (age, rating, spend, competition) can still be shown — clearly marked with the filter it misses. Never relax a red flag or a hard no.
7. **Show at most `max_results` jobs**, in this format:

   ```
   <title> — <link>
   פורסם: <when> · לקוח: <rating> (<reviews>), הוציא <spent>, <country>
   הצעות: <proposals> · ראיונות: <interviewing> · Connects: <cost>
   למה מתאים: <one line>
   לבדוק: <risks / gaps, honestly>
   מהביקורות: <one line>
   לא עומד ב: <filter, if any>
   ```

   Zero is a valid result. Then say so in one line, plus the closest miss and why.
8. **Log** every job you opened in `job-log.csv`, with `decision` = `shown` or `skipped` and a short reason.

## Red flags — always skip

- Many jobs and hires with a tiny total spend (e.g. 100+ jobs, 90+ hires, under $1,000 spent). That is a free-labor or rating-farming account.
- $5–$10 budgets for real work (a full website, a campaign, an app).
- Jobs across unrelated fields from the same client (a spa website, a music teaser, a book illustrator...).
- All-5.0 reviews on tiny jobs.
- Anything that asks to install apps on your phone, use your SIM, or move money.
- Payment method not verified.

## Hard limits

- Never apply, submit, boost, spend Connects, message a client, or change anything on Upwork. Read only.
- Never invent experience, numbers or portfolio items. If a "must have" isn't in `config.yaml`, she doesn't have it.
- If the browser shows a login wall, a captcha or a warning — stop and tell her.

## When she picks a job

Only if she asks: draft a proposal that answers exactly what the client asked, value first, in a personal tone. Use only what is in `config.yaml` and `work_samples`. Add `proposal_translation` if set. She edits and sends.

Upwork rules for every draft:
- **No contact details before a contract:** no email, phone, WhatsApp, Telegram, contact-form link or external meeting link in a proposal or message. All communication stays on Upwork until there is a contract.
- **No payment outside Upwork**, and no hint of it.
- Work-sample links (portfolio, GitHub, demos) are fine; contact links are not.
- At most `max_proposals_per_day` drafts a day (default 2).

## Follow-up (once a week)

Ask which applications got a view, a reply, an interview or a hire, and update `status`:
`applied` → `viewed` → `replied` → `interview` → `hired` (or `closed`).
Then 3 lines: jobs shown, applications sent, what moved forward, which search brings the best jobs.
