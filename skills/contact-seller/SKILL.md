---
name: contact-seller
description: Contact the seller of a property listing with One Place. Use this when the user wants to ask a seller a question, ask whether a home is still available, or request a visit.
---

# Contact a seller with One Place

One Place sends the user's request to the seller of a listing, and the seller replies to the user
by email. Sending takes two tools, and the user approves between them.

## 1. Write the request with the user

Agree on the message with the user: what they want to know, or when they could visit. Keep their
words. Do not add links: the seller already has the listing.

Call `draft_contact_request` with the listing id and the message.

- Set `intent` to `schedule_visit` when the user wants to see the home. Otherwise leave
  `more_info`.
- Pass `phone` only when the user gave you a number for this request.
- When the tool says that the account has no name, ask the user which name the seller should see,
  then draft again with `name`.
- When the tool refuses (the limit, a listing already contacted, a switched-off connection), tell
  the user why and give the listing's `url`, where they can contact the seller instead.

## 2. Show the draft and ask

Show the user every field of the draft as the tool returned it: the listing, the name, the email,
the phone and the message. Ask whether to send it.

- If the user changes something, draft again. Never send an old draft.
- A draft expires after 15 minutes. Draft again if it expired.

## 3. Send only after a yes

Call `send_contact_request` with the `draft_id` only after the user said yes to that draft in this
conversation. Then give the user the `reference` and say that the seller replies by email. One
Place also emails the user a receipt.

## Rules

- Never send a request that the user did not approve in this conversation. Never send one from a
  scheduled task, a routine, or on your own initiative.
- One request per listing every 30 days, and at most 20 a day for each user.
- One Place cannot book visits or make offers. Say that the seller will reply, not that a visit is
  booked.
- The seller sees the user's name and the email of their One Place account. Say so if the user asks.
