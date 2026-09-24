# Airbnb Superhost rating fragility and badge rates, 2026

Two measurements over Airbnb listings in 123 cities across 37 countries, built from
[Inside Airbnb](https://insideairbnb.com/get-the-data/)'s public June 2026 snapshots:

1. **Fragility.** Of listings whose rating clears Airbnb's 4.8 Superhost line, how many would drop
   below it after a single 1-star review?
2. **Badge vs rating.** How many listings at each all-time rating actually hold the Superhost badge?

Full write-up, charts and discussion:
**https://stellarreply.com/blog/airbnb-superhost-fragility-study.html**

## Headline figures

**Fragility** (871,667 listings with 10+ reviews)

- 62.1% have an all-time rating of 4.8 or above.
- Of those, **44.0% would fall below 4.8 after one 1-star review**, and 66.1% after two.
- 37.3% are at 4.9 or above.

**Badge vs rating** (871,140 listings with 10+ reviews and a recorded badge status)

- 51.1% of listings belong to a Superhost.
- Across all listings rated below 4.8, **25.1%** belong to a Superhost. Across all rated 4.8
  or above, **33.4%** do not.
- Superhost listings have a median of 18.0 reviews in the last 12 months, against 7.0 for
  the rest. Many non-Superhosts likely miss the 10-stay requirement rather than the rating.

⚠️ The band-level percentages in `superhost_by_rating_band.csv` (for example 39.7% for 4.70–4.79)
are **not** aggregates. Quote the 25.1% / 33.4% figures above when describing "below"
or "above" 4.8 as a whole.

## Files

| File | What it is |
|---|---|
| `data/fragility_by_city.csv` | Per-city fragility counts, with the exact Inside Airbnb snapshot URL used |
| `data/fragility_by_country.csv` | Per-country roll-up |
| `data/superhost_by_rating_band.csv` | Share of listings holding the badge, by all-time rating band |
| `data/superhost_by_rating_and_reviews.csv` | The same, crossed with review-count band (30 cells) |
| `data/superhost_by_city.csv` | Badge share above and below 4.8, per city |
| `data/fragility_full.json`, `data/superhost_reality_full.json` | Complete machine-readable results |
| `charts/*.png` | The two published charts |

### Key columns

- `eligible`: listings with at least 10 reviews and a published rating (Airbnb's own 10-stay floor).
- `above_superhost`: eligible listings with an all-time rating of 4.8 or above.
- `buffer_zero`: of those, how many fall below 4.8 on one additional 1-star review.
- `buffer_one_or_less`: of those, how many fall below 4.8 within two.
- `median_buffer`: median number of 1-star reviews a listing above 4.8 can absorb (capped at 20).
- `within_5_of_superhost`: listings below 4.8 that need five or fewer 5-star reviews to reach it.
- `pct_superhost`: share of listings whose host holds the badge (`host_is_superhost` on the snapshot).

## Method

Inputs are two fields from each city's `listings.csv.gz`: `review_scores_rating` and `number_of_reviews`
(plus `host_is_superhost` for the badge measure). Listings with fewer than 10 reviews are excluded.

A displayed rating is rounded, so it is consistent with a range of possible star totals. For each listing,
the total is taken as `round(rating × reviews)` and the reviews are reconstructed as the closest two-level
split of that total. The buffer is then the largest `j` such that `(S + j) / (N + j) ≥ 4.8`, where `S` is
the star total and `N` the review count; it is solved in closed form, not simulated.

## Limitations (read before citing)

- **All-time vs trailing 12 months.** Inside Airbnb publishes the all-time rating. Airbnb assesses
  Superhost on the trailing 12 months, which is not public. The two diverge for any host whose recent
  reviews differ from their history.
- **Quarterly lag.** June snapshots sit near the end of an assessment quarter, so badge status reflects
  the 1 April assessment while the rating includes everything since. Some of the rating/badge gap is that
  drift. Not yet tested with snapshots either side of an assessment date.
- **Unit is the listing, not the host.** Superhost is assessed per host; a host with several listings is
  counted once per listing.
- **Rating only.** Superhost also requires a 90% response rate, under 1% cancellations and 10 stays. The
  fragility measure covers the rating bar only.
- **No bookings or ADR.** Inside Airbnb's `estimated_occupancy_l365d` is derived from review counts, so
  any occupancy or booking comparison built on it would be circular. None is included.
- **Snapshot dates vary by city.** Each city's date is in `fragility_by_city.csv`.

## Licence and citation

CC BY 4.0, the same licence as the underlying Inside Airbnb data. Reuse freely with attribution:

> StellarReply (2026). *Airbnb Superhost rating fragility and badge rates, 2026.*
> https://stellarreply.com/blog/airbnb-superhost-fragility-study.html. Built from Inside Airbnb data (CC BY 4.0).

Please credit [Inside Airbnb](https://insideairbnb.com) as the original data source as well.
