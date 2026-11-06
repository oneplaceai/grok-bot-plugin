---
name: watch-listings
description: Follow a property search over time with One Place. Use this when the user wants to hear about new homes that match a search, wants a search to keep running, or asks the Bot to watch the market for them.
---

# Watch the market with One Place

One Place can watch a search for the user. Do not run the same search again and again on a
schedule to find new listings. Save the search once with `create_saved_search`, and One Place
emails the user the new matching listings.

## 1. Agree on the criteria first

Run `search_listings` with the user's criteria and show the best matches (see the
`property-search` skill). Ask the user if the results are what they want. Change the criteria
until the user agrees. Only then save the search.

## 2. Save the search

Call `create_saved_search` with the same criteria that the last `search_listings` call used, and
a short `name` that the user will recognise, for example "2-bed in Lisbon under 500k".

- `alerts` is true by default: One Place emails the user new matches. Set it to false only when
  the user says that they do not want emails.
- When `status` is not `created`, nothing was saved. Read `message` and act on it, as for
  `search_listings` (for example, ask which place the user means).
- When the tool says that a saved search with the same criteria already has alerts on, tell the
  user. Do not save a second copy.

## 3. Tell the user what happens next

Say that the search is saved in their One Place account and that One Place emails new matches.
Give the `manage_url`: the user changes, pauses or deletes saved searches on that page.

## Rules

- Save a search only when the user asked to follow it. Never save one on your own initiative.
- One Place sends the emails, not you. Do not promise a frequency or a time of day.
- The user disconnects One Place from their account settings on one-place.com, in the
  Connected apps section. That stops your access at once.
