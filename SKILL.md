---
name: google-reviews
description: Answer Google reviews, schedule Google posts and read Google Business Profile numbers (views on Search and Maps, calls, website clicks, directions) through Workyol's MCP tools and Google's official Business Profile API. Load this before calling any google_ tool of Workyol. Use when the person asks to see, summarise or reply to their Google reviews, handle a bad review, ask customers for reviews, post an offer or update on their Google profile, or know how many people found them on Google Maps, for one business or several locations or clients.
---

# Google reviews (with Workyol)

This skill works with **Workyol**, a program that runs on the person's computer and connects to their Google Business Profile with Google's official Business Profile API. It tells you how to use Workyol's Google tools and how to write good replies.

## Requirement

1. Workyol 1.14 or later is installed and **running** (it opens at `localhost:4747`). Free 7-day trial: https://workyol.com/?ref=skill-google-reviews
2. Workyol's MCP server is connected to this assistant (in Workyol: Connections, AI assistants, Connect next to the app).
3. In Workyol, **Google reviews → Connect Google Business Profile**, with the Google account that owns or manages the business on business.google.com.

**First step:** call `google_reviews_list`. If the tool does not exist or answers "Workyol is not running", tell the person to start Workyol and connect the MCP server; do not guess. If it answers `connected: false`, give them its `next_step`.

Agencies: each client is a Workyol account with its own Google connection. Call `list_accounts`, then pass `account` (the client's name) to every tool.

## Tools

- `google_reviews_list`: reviews of one location, newest first: `review_id`, stars, author, date, text, `google_translation`, the business's `reply`. Parameters: `location` (id or part of the name; default the first), `filter` (`unanswered` default, `all`, `negative` = 1 to 3 stars). Also returns `location_id`, `other_locations`, `average_rating`, `total_reviews`, `review_link_for_customers`, `more_on_google`.
- `google_reviews_reply`: post or replace the public reply under one review. Parameters: `review_id`, `text` (plain text), `confirm`. Without `confirm` it only returns a preview.
- `google_posts_schedule`: an update on the profile (Google Search and Maps). Parameters: `text` (max 1,500 characters), `at` (ISO 8601 with time zone, e.g. `2026-11-14T09:00:00+01:00`), `locations` (ids; empty = every location), `button` (`LEARN_MORE`, `BOOK`, `ORDER`, `SHOP`, `SIGN_UP` need `button_url`; `CALL` calls the number on the profile), `confirm`. Workyol must be running at that time. A photo can be added in Workyol's screen (Posts → Google posts), not through this tool.
- `google_posts_list`: scheduled, published and failed posts, with links and Google's reason for a rejection.
- `google_insights`: per location, for `from` to `to` (YYYY-MM-DD; default the last 30 days): views on Search and Maps, calls, website clicks, direction requests, messages, bookings, plus the reviews written in the period (count, average, answered) and the all-time average.

Nobody can edit or delete a customer's review. A review that breaks Google's rules can only be reported on Google Maps (the flag on the review); say so if asked.

## Workflows

### Answer the new reviews
1. `google_reviews_list` (unanswered). For several locations, call it again for each name in `other_locations`.
2. Draft one reply per review (rules below). Stars-only reviews get a one-line thank you.
3. Show all drafts in one table: location, author, stars, review (short), reply.
4. Post each approved reply with `google_reviews_reply` and `confirm: true`. If the person changes a draft, post their version. Report what was posted.

### Handle a bad review (1 to 3 stars)
1. `google_reviews_list` with `filter: "negative"`.
2. Ask the person what really happened if you do not know; do not guess facts.
3. Draft a calm public reply (rules below) and, separately, a private note for the person on what to fix.

### Ask customers for more reviews
1. Take `review_link_for_customers` from `google_reviews_list`.
2. Write a short request with that link. The person shares it with **every** customer the same way (receipt, counter, e-mail, a WhatsApp template to an opted-in list).
3. Never offer a discount, gift or entry for a review, never ask only the happy customers, never write reviews for the business. Google forbids all three and can remove reviews or suspend the profile.

### Schedule Google posts
1. `google_reviews_list` for the location ids, `google_posts_list` for what is waiting.
2. Write each post: the offer or news first, one clear call to action, no phone number in the text (Google rejects it: use the `CALL` button), no link shorteners.
3. Show every post with its time and locations. After a yes, `google_posts_schedule` with `confirm: true`. Remind the person that Workyol must be running at those times.

### Monthly Google numbers
1. `google_insights` with the month's `from` and `to`.
2. Report per location: views (Search + Maps), calls, website clicks, directions, new reviews, how many answered, average stars. Compare with the month before if asked (call it twice). Google gives the numbers about 3 days late, so the last days can be 0. For an Excel file, the person uses Reports in Workyol.

## How to write a reply

- **Same language as the review**, even a dialect (for example Tunisian Arabic gets Tunisian Arabic). Use `google_translation` only to understand it.
- Short: 1 to 3 sentences for good reviews, up to 5 for bad ones.
- Personal: the first name when there is one, and one detail the customer wrote (a dish, a service, a staff member).
- Signed as the business ("the team at …") if the person wants a signature; ask once.
- Bad reviews: thank them, apologise for the experience without admitting legal fault, no arguing, no excuses, no customer details (order numbers, dates of visit, health information), and invite them to contact the business directly (use the contact the person gives you).
- Never invent facts, offers, refunds, opening hours or promises. If a reply needs one, ask the person.
- No keyword stuffing, no links unless the person asks, no copy-paste: each reply different.
- Replies are public under the review on Google Maps and Search, and can be replaced later with `google_reviews_reply`.

## Safety rules (follow them always)

- **Nothing is posted until the person says yes.** Call `google_reviews_reply` and `google_posts_schedule` without `confirm` first, show the result, and pass `confirm: true` only after a clear yes to that exact text.
- You never see Google passwords or access tokens. Do not ask for them.
- No fake reviews, no paid or rewarded reviews, no review gating (asking only happy customers).
- Workyol uses Google's official API; do not say that Google endorses or partners with Workyol.
- Do not promise rankings or results.

## Example prompts

- "Show my unanswered Google reviews and draft a reply to each, in the reviewer's language."
- "We got a 2-star review about waiting time. Help me answer it."
- "Write a short message with our Google review link that I can put on receipts."
- "Schedule a Google post for all my locations on Friday at 09:00: 20% off this weekend, with a Call now button."
- "How many people found us on Google Maps last month, and how many called or asked for directions?"
- "For my client's bakery account, list the reviews of the last month and their average stars."
