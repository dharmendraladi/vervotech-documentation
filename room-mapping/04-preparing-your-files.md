# Preparing Your Files

Offline room mapping via file upload. Provide room information from your suppliers in CSV format and receive standardized room mappings in return.

---

### Preparing Your Input File

[Download input template](https://roommapping.vervotech.com/docs/file-based-room-mapping.csv)

Export a single CSV file containing one row for each supplier room, across every provider you want mapped together. Start from the template above rather than building the header row by hand.

**Requirements:**

| Requirement            | Value                                                                                                       |
| ---------------------- | ----------------------------------------------------------------------------------------------------------- |
| Format                 | CSV, comma (`,`) separated                                                                                  |
| Header row             | Required, and must be the first line of the file                                                            |
| Accepted content types | `text/csv`, `application/vnd.ms-excel`, `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` |

For how much you may send in one file, including the file size ceiling, the row ceiling and
the number of files per upload, see [Service Limits](06-service-limits.md).

- **Column headers are matched exactly, and letter case matters.** A header that is misspelled or cased differently is not reported as an error. The column is read as empty instead, and the file is processed as though you had sent no value at all. Check your header row against the template above before every upload.

- **The upload checks the content type, not the file extension.** A workbook sent with a spreadsheet content type is accepted at upload and then fails later during processing, because the file is always read as CSV. Export to CSV before uploading.

- Quote any value that contains a comma, such as a room description, so that it is not read as two columns.

- Save the file as UTF-8 so that accented characters in room names survive. No encoding is enforced, so a file saved in another encoding is accepted and may produce mangled room names.

- Leave a column empty when you have no value for it. Literal text such as `NULL` or `null` is treated as a value rather than as an empty cell.

- Do not insert values in JSON or array format, and do not include HTML markup.

- Do not include rate plan codes in the `RoomCode` column.

- `Provider` values are compared exactly, and case sensitively, against the provider list your account is entitled to. A file containing a provider your account is not authorised for is refused, and the message lists the unrecognised names. Note that this refusal is returned in the response body with an HTTP 200 status and no status code, so check the body rather than the HTTP status. The message is also always prefixed with the mandatory fields wording, even when an unauthorised provider is the only fault.

- **The file based path and the API paths check `provider` against two different account scoped lists.** A provider accepted in a file upload can still be rejected with status code `1011` on the real time or asynchronous path, and the reverse is also possible. Verify a provider on the path you intend to use rather than assuming one result carries over.

- Mandatory values are checked across the whole file rather than row by row. If any row has an empty `Provider`, `ProviderHotelId` or `RoomName`, the entire file is rejected and the message names the column, not the row, so correct every occurrence before resubmitting.

- 100,000 rooms are processed per batch by default. Records beyond this limit are skipped automatically and the run still finishes with status `Completed`, so always compare the row count of your output file against your input file.

- A `Completed` status does not by itself guarantee an output file. If the run succeeds but uploading the output fails, the status still reads `Completed`. Treat a missing output file as a run to raise with support rather than as an empty result.

- For volumes above the 10 MB upload limit, use the SFTP based submission instead. Contact the Vervotech mappings team to have it configured.

---

### Input File Columns

| Column               | Required    | Description                                                                                                                                                                                                                                                                                                       |
| -------------------- | ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **VervotechHotelId** | Recommended | The Vervotech ID assigned to the property. If omitted, the system attempts a lookup using Provider and ProviderHotelId. If lookup fails, an ID is auto-generated in the format `{Provider}_{ProviderHotelId}`. Without this value, rooms from different providers for the same hotel cannot be grouped correctly. |
| **Provider**         | Yes         | Supplier name. Must match the [master provider names](https://docs.vervotech.com/get-all-provider-names-16946072e0).                                                                                                                                                                                              |
| **ProviderHotelId**  | Yes         | The property ID assigned by the supplier.                                                                                                                                                                                                                                                                         |
| **RoomCode**         | No          | The room code from the supplier. Strongly recommended. Do not include rate plan codes.                                                                                                                                                                                                                            |
| **RoomName**         | Yes         | The room name from the supplier.                                                                                                                                                                                                                                                                                  |
| **Description**      | No          | Room description from the supplier.                                                                                                                                                                                                                                                                               |
| **Bed**              | No          | Bedding information from the supplier. Accepted, but it does not currently influence the mapping output.                                                                                                                                                                                                          |
| **View**             | No          | View information from the supplier. Accepted, but it does not currently influence the mapping output.                                                                                                                                                                                                             |
| **Refundability**    | No          | Refundability information from the supplier.                                                                                                                                                                                                                                                                      |
| **BoardBasis**       | No          | Board basis information from the supplier.                                                                                                                                                                                                                                                                        |

These are the columns the input template carries, listed in the order the template uses, and they are the only columns the service reads. Anything else in your file is ignored.

At upload time only **ProviderHotelId**, **Provider** and **RoomName** are checked for a non empty value. The remaining columns are validated no further, so a problem in any other column surfaces only as reduced mapping quality in the output.

---

### Output File Columns

[Download output template](https://roommapping.vervotech.com/docs/file-based-room-mapping-output.csv)

Columns are written in the order shown below.

| Column                 | Description                                                                                                                                                                 |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **VervotechId**        | The Vervotech ID of the **hotel**, repeated on every row for that hotel. This is not a per room identifier.                                                                 |
| **ProviderHotelId**    | The property ID assigned by the supplier.                                                                                                                                   |
| **Provider**           | Supplier name from the [master provider list](https://docs.vervotech.com/get-all-provider-names-16946072e0).                                                                |
| **GroupId**            | Identifies the group within this run, in the format `VervotechHotelId-index`. Not stable across runs. See the note below.                                                   |
| **RoomCode**           | Room code from the supplier.                                                                                                                                                |
| **RoomName**           | Room name from the supplier.                                                                                                                                                |
| **IsExclusive**        | Indicates that the group has a single provider. Reported per group, not per row. See the note below.                                                                        |
| **MappedStandardName** | Standardized room name generated by Vervotech algorithms.                                                                                                                   |
| **MappedMasterTitle**  | Room name from the official hotel website.                                                                                                                                  |
| **MasterRoomId**       | Master ID from Vervotech master data. Empty if no match is found.                                                                                                           |
| **MappedCategory**     | Room category extracted from the room name.                                                                                                                                 |
| **MappedLocation**     | Room location extracted from the room name. Examples: Highfloor, Oceanfront, Seaside.                                                                                       |
| **MappedView**         | View category extracted from the room name.                                                                                                                                 |
| **MappedBedType**      | Bed type extracted from the room name.                                                                                                                                      |
| **BedInfo**            | Display form of the bedding for the group. Example: 2 Queen.                                                                                                                |
| **MappedAttributes**   | Unique room features extracted from the room name.                                                                                                                          |
| **MappedOcccupancy**   | Bed occupancy category derived from the room name, such as `Single`, `Double`, `Triple` or `NA`. Note the spelling of this header, which carries three letter c characters. |
| **BoardBasis**         | Standardized board basis for the room rate.                                                                                                                                 |

Three of these columns are read wrongly often enough to be worth calling out.

- **`GroupId` is not a durable key.** The part after the hotel ID is a counter assigned within a single run, in the order the groups happened to be produced. Re-running the same file can give the same room a different `GroupId`. Do not persist it as a join key; join on the supplier's own `Provider` and `RoomCode` instead.
- **`IsExclusive` can under report.** It is derived from a provider set that is not always updated when groups are merged, so a group holding rates from more than one provider can still be marked exclusive. Treat it as a hint rather than a guarantee, and derive exclusivity yourself from the `Provider` column if it matters to you.
- **`MappedOcccupancy` is derived, not supplied.** It is read out of the room name and expressed as a category, not copied from any occupancy value you sent. Unlike the occupancy fields on the input side, this one does take part in grouping.
