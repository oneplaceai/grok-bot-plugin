---
name: property-search
description: Find homes for sale or for long-term rent in Europe with the One Place tools, show the best matches, and answer follow-up questions about one listing. Use this when the user wants to buy or rent an apartment, a house, land or commercial property, or asks about a listing that One Place returned.
---

# Property search with One Place

Use the three One Place tools in this order: `search_listings`, then `show_listings`, then
`get_listing` for follow-up questions.

## 1. Search with every criterion the user gave

Call `search_listings` once with all the criteria that the user stated. Do not add criteria that
the user did not state.

- `location`: the place as precise as the user gave it, with the country when you know it.
  Examples: "Lyon 3e, France", "Lisbon", "Costa Brava, Spain". Omit it only when the user named no place.
- `location_kind`: set it when you know what kind of place it is (city, district, region, street,
  natural feature). It helps to tell a town from a street with the same name.
- `radius_km`: set it only when the user gave a distance ("within 10 km of Annecy").
- `nearby`: true when the user says "near", "around" or "area" without a distance ("near Lyon"), so
  the search also covers the towns around the place.
- `ad_type`: `sell` to buy, `rent` to rent long-term. The default is `sell`.
- `housing_types`: the default is apartment and house. Set it when the user asks for something
  else, for example land or commercial property.
- `min_price` and `max_price`: for a rental, these are monthly rents. Set `currency` when the user
  gives a price in a currency other than euros.
- `min_bedrooms`, `min_rooms`, `min_surface_m2`, `max_surface_m2`: use them for numbers the user gave.
- `sort`: `relevance` unless the user asks for the cheapest (`price_asc`), the most expensive
  (`price_desc`) or the newest (`newest`) listings.
- `page`: ask for the next page only when the user wants more results and `has_more` is true.

### Filters, keywords or visual features

Use a filter when the user gives a number or a fixed choice: a price, a number of bedrooms, a size,
a property type, sale or rent.

Use `keywords` for facts that the listing text states, such as "garden", "renovated", "quiet
street" or "near the metro". The search matches keywords by meaning against the listing text, not
by exact words. Do not put the place or the price in `keywords`.

Use `visual_features` for anything the user wants to see in the home or its photos:

- floors: "patterned cement tile floor", "parquet"
- ceilings: "exposed wooden beams", "frescoed ceiling"
- "infinity pool", the colour of the walls, the kitchen appliances, the furniture, the style
- the view from the windows: "view of the Eiffel Tower"

An image model compares them with the photos of each listing, so they find homes whose text does
not mention them. Put the most important one first: the card photo shows the best match for it. Do
not repeat a visual feature in `keywords`.

Example: "a 2-bedroom apartment in Barcelona that still has its original patterned tile floors"
gives `visual_features` ["patterned cement tile floor"], not `keywords` ["patterned tiles"].

## 2. Handle a status that is not `ok`

When `status` is not `ok`, no search ran. Read `message` and act on it.

- `location_ambiguous`: show the names in `location_candidates` and ask the user which one they
  mean. Do not choose for the user. Search again with the name the user chooses.
- `location_outside_coverage`: tell the user that One Place has no listings there, and name the
  countries in `covered_countries`. Do not invent listings.
- `location_not_found`: ask the user for a town, a district or a region.

When `status` is `ok` but `listings` is empty, tell the user that nothing matches. Suggest one or
two criteria to relax, for example a higher price or a larger area.

## 3. Show the best matches

Choose the listings that answer the user best. Call `show_listings` with their ids (1 to 12 ids).
When the search had `visual_features`, pass the same `visual_features` to `show_listings`, so each
card shows the photo that best matches the first one. The cards show a photo, the price, the place,
the size and a link to one-place.com.

In your text answer, add a short summary of what you found. Use `total_count` when it is not null.

When you used `visual_features`, read `visual_features_applied`:

- `true`: say that One Place found these homes by their photos. When the result text says that a
  card photo is the photo that best matches a feature, you can say so. Do not say it otherwise.
- `false`: photo matching did not run. Say that the listings match the other criteria only, not
  the visual features. Do not say that the photos match.

`keywords_applied` works the same way for `keywords`.
Give the one-place.com `url` of each listing that you mention. The user sees all the photos and
contacts the seller on that page.

## 4. Answer follow-up questions with `get_listing`

When the user asks about one listing, call `get_listing` with its id. Answer from the result.

- Use only facts that the result contains. A fact that is absent from `facts` is unknown, not
  false. Say "the listing does not say" and do not guess.
- `description` is a summary that One Place wrote from the listing.
- `location` is approximate when `is_exact` is false. Do not present it as an address.
- Give the listing's one-place.com `url` again in the answer.

If `get_listing` says that no listing has the id, tell the user that the home may be sold,
rented or withdrawn, and offer a new search.

## Rules

- Prices are the asking prices that the listings state. They are not valuations. Do not say that a
  price is fair, a good deal or a bargain. You can compare it with the price per square metre and the
  price history in the listing data.
- Do not give financial, mortgage, tax or legal advice. Suggest a qualified adviser.
- One Place cannot book visits or make offers. Never say that you did. It contacts a seller only
  through `draft_contact_request` and `send_contact_request`, which exist only when the user
  connected a One Place account. Without these tools, give the listing's
  one-place.com link, where the user can contact the seller.
- One Place does not list holiday or short-term rentals. Say so when the user asks for one, and
  offer a long-term rental search instead.
