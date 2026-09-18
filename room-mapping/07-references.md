# References

## Supported Languages

- Desired language for the response as a subset of BCP47 format that only uses hyphenated pairs of two-digit language and country codes. Use only ISO639-1 alpha 2 language codes and ISO3166-1 alpha 2 country codes.

- Only the values below are supported, and additional languages may be added in the future.

| Language code | Description             |
| ------------- | ----------------------- |
| en-US         | English (United States) |
| es-ES         | Spanish                 |
| tr-TR         | Turkish                 |
| fr-FR         | French                  |
| it-IT         | Italian                 |
| ar-SA         | Arabic                  |

`culture` is a request header, and it is read on every endpoint. The value is matched case insensitively, so `EN-us` is accepted. A value outside the list above is **not** rejected: it falls back to `en-US` silently and no warning is returned, so a typo shows up only as an unexpectedly English response.

The file based path is English only. `culture` has no effect there.

## API Status codes

| Status Code | Description                                                                   |
| ----------- | ----------------------------------------------------------------------------- |
| 200 or 1000 | Success                                                                       |
| 1001        | Internal Server Error                                                         |
| 1002        | 'Room Name field' cannot be null or empty for room with index {index}         |
| 1003        | 'Room Rates' field cannot be null or empty                                    |
| 1004        | Room Index field cannot be null or empty and invalid                          |
| 1005        | 'Provider' field cannot be null or empty for room index with {index}          |
| 1006        | 'Provider Hotel Id' field cannot be null or empty for room with index {index} |
| 1007        | 'Index' field should be unique                                                |
| 1009        | Incorrect 'Provider Content Type' value found.                                |
| 1010        | Invalid Request. Raised on the static rooms endpoints, not on room mapping    |
| 1011        | Unauthorized providers requested, listing the providers and your entitlements |
| 1012        | Payload limit or Per day limit exceeded                                       |
| 1013        | Static rooms are not present                                                  |
| 1014        | Board basis list cannot be null                                               |
| 1015        | Error fetching board basis standard                                           |
| 1016        | Translation service request timed out                                         |
| 1021        | Invalid accountId or token!                                                   |

Notes on the codes most often handled wrongly:

- **`1021`, not `403`, is the invalid credentials code.** An invalid `accountId` or `token` returns `1021` in the response body. A `403` is returned when the `correlationId` header is missing on `POST /api/2.0/mapping/rooms`, and on the daily quota check of the version 1 API.
- **`1011` covers three conditions:** an unentitled provider on a room, an unentitled provider in `ProviderContentPreference`, and an account with no providers configured at all. The message names which.
- **`1012` covers two conditions** that need different handling: a payload above your per request limit, and an exhausted daily quota. Read the message, not the code alone.
- Codes `1017` to `1020` exist for the log retrieval endpoints and are not raised by the mapping endpoints.

## Board Basis Mapping

- We support 6 different types of board basis as mentioned below. In order to identify them we support various different keywords which are either common for all providers data or specific to certain provider's data only.
- Board basis that matches none of the vocabularies below is returned as `Unknown`.
- The keywords listed are the common ones rather than the complete vocabulary, which is extended continuously from real supplier data.
- If you notice we have missed out anything, feel free to drop a mail to our support at support@vervotech.com.

| Standard Board Basis Name | Description                                                                        | Keywords                                                                                                                                                                                                     |
| ------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Room Only                 | This type states no board basis                                                    | no meals, no meal, without meals, only room, only-room, room-only, room only, accommodation-only, accommodation only, room and accommodation, sleep only, stay only, european plan, RO, ro, EP               |
| Bed and Breakfast         | This type of board basis states only breakfast                                     | bed with breakfast, bed and breakfast, bed & breakfast, bed-breakfast, continental breakfast, buffet breakfast, full breakfast, complimentary breakfast, breakfast only, breakfast included, b&b, bb, BB, RB |
| Half Board                | This type of board basis states either breakfast and lunch or breakfast and dinner | half board, half-board, hb, HB, breakfast & lunch, breakfast & dinner, breakfast and one meal, demi pension, semi board, modified american plan                                                              |
| Full Board                | This type of board basis states breakfast, lunch and dinner                        | full board, full-board, fb, FB, three meals, full meals, pension complete, american plan, AP                                                                                                                 |
| All Inclusive             | This type of board basis states breakfast, lunch, dinner and drinks                | all inclusive, all-inclusive, all meals, all-meals, ai, AI                                                                                                                                                   |
| Self Catering             | This type of board basis states a self catering arrangement                        | returned as `SelfCatering`                                                                                                                                                                                   |

`stay only` is matched as Room Only, but not where it is qualified, as in `minimum stay only` or `extended stay only`.
