# References

## Supported Languages

- Desired language for the response as a subset of BCP47 format that only uses hyphenated pairs of two-digit language and country codes. Use only ISO639-1 alpha 2 language codes and ISO3166-1 alpha 2 country codes.

- Only the values below are supported, and additional languages may be added in the future.

| Language code | Description             |
| ------------- | ----------------------- |
| en-US         | English (United States) |
| es-ES         | Spanish                 |
| tr-TR         | Turkish                 |

## API Status codes

| Status Code | Description                                                                   |
| ----------- | ----------------------------------------------------------------------------- |
| 200 or 1000 | Success                                                                       |
| 403         | Invalid accountId or token!                                                   |
| 1001        | Internal Server Error                                                         |
| 1002        | 'Room Name field' cannot be null or empty for room with index {index}         |
| 1003        | 'Room Rates' field cannot be null or empty                                    |
| 1004        | Room Index field cannot be null or empty and invalid                          |
| 1005        | 'Provider' field cannot be null or empty for room index with {index}          |
| 1006        | 'Provider Hotel Id' field cannot be null or empty for room with index {index} |
| 1007        | 'Index' field should be unique                                                |
| 1008        | Invalid Provider Names found.                                                 |
| 1009        | Incorrect 'Provider Content Type' value found.                                |
| 1010        | Invalid Request.                                                              |
| 1011        | Unauthorized or Invalid providers requested.                                  |
| 1012        | Payload limit or Per day limit exceeded.                                      |

## Board Basis Mapping

- We support 5 different types of board basis as mentioned below. In order to identify them we support various different keywords which are either common for all providers data or specific to certain provider's data only.
- If you notice we have missed out anything, feel free to drop a mail to our support at customersuccess@vervotech.com.

| Standard Board Basis Name | Description                                                                        | Keywords                                                                                                                                        | Provider Specific Keywords |
| ------------------------- | ---------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- |
| Room Only                 | This type states no board basis                                                    | no meals,no meal,only room,only-room,room-only, room only, accommodation-only, accommodation only,RO,ro                                         |                            |
| Bed and Breakfast         | This type of board basis states only breakfast                                     | bed with breakfast, bed and breakfast, bed & breakfast, bed-breakfast,continental breakfast, breakfast only, breakfast included, b&b,B&B, bb,BB |                            |
| Half Board                | This type of board basis states either breakfast and lunch or breakfast and dinner | half board,half-board,hb,HB,breakfast & lunch, breakfast & dinner                                                                               |                            |
| Full Board                | This type of board basis states either breakfast, lunch and dinner                 | full board,full-board,fb,FB                                                                                                                     |                            |
| All Inclusive             | This type of board basis states breakfast, lunch, dinner and drinks                | all inclusive,all-inclusive,all meals,all-meals,ai,AI                                                                                           |                            |
