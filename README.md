# Listings feed sample

A sample of the daily Australian property listings feed: buy listings in
postcode 2026 (Bondi, Bondi Beach, North Bondi, Tamarama), taken from the
live database on 27 Oct 2026. Every listing is merged across
realestate.com.au and domain.com.au into one property.

| File | What's in it |
|---|---|
| `listings.json` | 20 properties, each with its full price and status history (the shape of a daily pull) |
| `listings.csv` | The same properties, one row each |
| `events.csv` | Every price and status change, one row per change, linked by `property_id` |
| `sold_history.json` / `.csv` | 15 sales, recent and older, one record per sale |

## Listing fields

- `property_id`: our property ID. One per physical property, shared by its
  REA and Domain listings, and it stays the same when the property is relisted.
- `sources`: which portals list it (`rea`, `domain` or both), with each
  portal's listing ID and a URL.
- `address`: the full address, plus `unit`, `number`, `street`, `suburb`,
  `state`, `postcode`, `lat`, `lng`.
- `property_type`, `beds`, `baths`, `cars`, `land_m2`, `agency`.
- `price_text`: the price exactly as advertised. `price_low` / `price_high`
  are parsed from it, and are empty when no figure is shown ("Contact Agent",
  plain "Auction").
- `advertised_as`: `fixed`, `range`, `offers_over`, `from`, `auction`,
  `contact_agent`, `eoi`, `under_offer` or `unknown`.
- `status`: `active`, `under_offer`, `sold` or `withdrawn`.
- `first_seen` / `last_seen`: when we first and last saw it advertised.
  `listed_date` is the portal's own listing date where it gives one.
- `days_on_market`: from the listing date (or first seen) to the sale, or
  to the last time we saw it.
- `sold_date` / `sold_price`: for sold listings. The price is empty when the
  agent withheld it.
- `price_history`: every change to the advertised price, with its date and
  whether it was a `drop`, a `rise` or a `text_change` (e.g. a guide added
  to "Auction"). A change both portals report the same day appears once.
- `status_history`: `listed`, `delisted`, `relisted`, `sold`, with dates.

## Sold history fields

One record per sale, merged across both portals: address, property facts,
`sold_date`, `sold_price` (empty when withheld; `sold_price_text` then says
so), `sale_method` (`auction`, `prior_to_auction`, `private_treaty`, ...),
and `agency`. When we tracked the property's listing before it sold,
`campaign` adds the first and last asking price, the number of price changes,
days on market and `discount_pct` (how far under the last asking figure it
sold).

## Notes

- Price change tracking started on 19 Oct 2026, so histories in this sample
  are short and `listed` events on that date mean "first seen". Histories
  build up daily from here. Sold history goes back years.
- Agency names, price text and suburb casing are as the portals publish them.
