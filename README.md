# Vacancy Scanner

An automated job-search scanner that runs on Google Apps Script, uses the Anthropic API (with web search) to find and fit-score vacancies, and emails a single ranked list twice a week.

## Why

Checking job boards, alert emails and company career pages by hand is slow and repetitive, and the same vacancy turns up in several places. The scanner does the sourcing, scoring and deduplication in one scheduled run, so the weekly review starts from a short, ranked list of roles that are not already in my application tracker.

## What it does

- **Collects vacancies from email**: reads job-alert digests that land in a dedicated Gmail label.
- **Finds more on the web**: calls the Anthropic API with web search enabled to look for roles matching my target profile.
- **Scores fit**: each vacancy gets a fit score against the target profile, so the list is ordered by relevance rather than by source.
- **Deduplicates**: results are checked against my job-application tracker (a Google Sheet, read via `SpreadsheetApp`) so roles I have already saved or applied to are excluded.
- **Emails the result**: one consolidated, ranked digest, sent every Monday and Thursday morning by a time-driven trigger.

## How it works

```
Time-driven trigger (Mon & Thu)
        │
        ▼
Apps Script ──► Gmail label (job-alert emails)
        │
        ├──► Anthropic API (web search) ──► candidate vacancies + fit scores
        │
        ├──► Google Sheet (application tracker) ──► exclude already-tracked roles
        │
        ▼
Ranked, deduplicated digest ──► email
```

## Design choices

- **Google Apps Script** instead of a Cloudflare Worker or a local cron job: no server to run, native access to Sheets and Gmail, and built-in scheduling.
- **Twice a week instead of daily**: keeps API cost low while still catching new postings in time to apply.

## Stack

Google Apps Script (JavaScript), Anthropic API, Google Sheets (`SpreadsheetApp`), Gmail (`MailApp` / `GmailApp`).

## Setup

1. Create a new Apps Script project and paste in the script.
2. Store the Anthropic API key in **Project Settings → Script properties** (never in the code).
3. Set the tracker spreadsheet ID and the recipient email address.
4. Add two time-driven triggers (Monday and Thursday, morning).
