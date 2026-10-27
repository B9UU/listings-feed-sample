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
- `property_type`, `beds`, `baths`, `cars`, `land_m2`.
- `agency`: the advertising agency. For a sold property, the agency named on
  the sale record (the one that sold it).
- `is_project`: an off-the-plan unit in a development.
- `price_text`: the price exactly as advertised. `price_low` / `price_high`
  are parsed from it, and are empty when no figure is shown ("Contact Agent",
  plain "Auction").
- `advertised_as`: how the price is advertised: `fixed`, `range`,
  `offers_over`, `from`, `auction`, `contact_agent`, `eoi`, `no_price`
  (no price wording at all, e.g. just "For Sale"), `sold` (the advert
  itself says SOLD) or `unknown`.
- `under_offer`: true when a portal shows the "Under offer" badge. It's
  separate from `advertised_as`, so a listing can be `fixed` and under offer.
- `status`: `active`, `under_offer`, `sold` or `withdrawn`.
- `first_seen` / `last_seen`: when we first and last saw it advertised.
  `listed_date` is the portal's own listing date where it gives one.
- `days_on_market`: from the listing date (or first seen) to the sale, or
  to the last time we saw it. Empty for off-the-plan units (the portal gives
  the whole development's listing date) and for adverts that sold before we
  first saw them.
- `sold_date` / `sold_price` / `sold_price_text`: for sold listings. The
  price is empty when the agent withheld it on both portals.
- `price_history`: every change to the advertised price, with its date and
  whether it was a `drop`, a `rise` or a `text_change` (e.g. a guide added
  to "Auction"). A change both portals report the same day appears once.
- `status_history`: `listed`, `delisted`, `relisted`, `sold`, with dates.

## Sold history fields

One record per sale, merged across both portals: address, property facts,
`sold_date`, `sold_price`, `sale_method` (`auction`, `prior_to_auction`,
`private_treaty`, ...) and `agency`. `sold_price` is shown when either
portal published it, and `sold_price_text` is that portal's wording; when
both withheld it, the price is empty and the text says so. When we tracked
the property's listing before it sold, `campaign` adds the first and last
asking price, the number of price changes, days on market and
`sold_vs_asking_pct`: how the sale price compares with the last asking
figure, positive when it sold above it (e.g. `25` = 25% above guide).

## Notes

- Price change tracking started on 19 Oct 2026, so histories in this sample
  are short and `listed` events on that date mean "first seen". Histories
  build up daily from here. Sold history goes back years.
- Price text is as the portals publish it. Where REA and Domain spell one
  agency differently, REA's spelling is used.
- Coordinates come from the portals. A few listings have none on either
  portal; in the full feed these would be geocoded from the address.
