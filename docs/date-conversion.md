# Date to datetime conversion

`to_datetime(ts: date) -> datetime` publishes UTC midnight at the start of the
input date on each admitted input tick. It preserves publication positions,
including repeated dates, and produces no tick at skipped input positions.
Leading, interior and trailing silence remain silent.

| Input date | Output datetime |
| --- | --- |
| `@1969-12-31` | `@1969-12-31T00:00Z` |
| `@1970-01-01` | `@1970-01-01T00:00Z` |
| `@2024-02-29` | `@2024-02-29T00:00Z` |

The backend scalar helper `midnight(date) -> datetime` supplies the same pure
conversion. Neither conversion depends on the local time zone, the
wall clock or daylight-saving rules.

The [standard behaviour tests](../hgl/hgraph/tests/standard.hgl) cover the date
values above, repeated publications and skipped positions.
