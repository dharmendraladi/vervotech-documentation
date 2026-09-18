# Choosing Your Integration Path

Room mapping is available through three paths. They share the same mapping engine, but they differ in how much you can send at once, how quickly you get an answer, and how much detail comes back. Pick the path that matches the job before you build against it, because changing path later means reworking your integration.

## Comparing the three paths

|                                        | Real time                                          | Asynchronous                                                                        | File based (offline)                                |
| -------------------------------------- | -------------------------------------------------- | ----------------------------------------------------------------------------------- | --------------------------------------------------- |
| Endpoint                               | `POST /api/2.0/mapping/rooms`                      | `POST /api/2.0/mapping/maproomsasync`, then `POST /api/2.0/mapping/getmappingasync` | `POST /api/MappingTool/FileBasedMapping`            |
| Hotels per submission                  | One                                                | One                                                                                 | Many                                                |
| Rooms per submission                   | Up to the payload limit configured on your account | Built for room sets above the real time limit                                       | 100,000 rows by default, and at most 10 MB per file |
| How you get the result                 | In the response                                    | Poll using the returned `traceId`                                                   | Email notification, then download the output file   |
| Confidence score (`matchScore`, `cfs`) | Yes                                                | Yes                                                                                 | No                                                  |
| Account mapping rule configuration     | Applied                                            | Applied                                                                             | Not applied                                         |
| Language (`culture`)                   | Yes                                                | Yes                                                                                 | English only                                        |

Both asynchronous calls are `POST`. `getmappingasync` takes the `traceId` as a query parameter rather than in a body.

## What the asynchronous response leaves out

The asynchronous result carries the same room groups and the same `matchScore` and `cfs` values as the real time response, but not every field. For accounts configured for accommodation detail, `occupancyType`, `accommodationType`, `category` and `bedInfo` are absent. Build against the real time response shape and treat these fields as optional.

## What you give up with file based mapping

File based mapping is the quickest way to get a large catalogue mapped without writing an integration, and it is the only path that accepts many hotels in one submission. Those conveniences come with real limitations. Every one of them is a behaviour to design around rather than discover in production.

- **Account mapping rule configuration is not applied to file based runs.** Offline runs are processed in a shared service context rather than in your account context, so mapping rules configured against your account do not take effect. The same rooms can therefore group differently in a file based run than they do through the API. If you depend on your own mapping rules, use the real time or asynchronous path.
- **No confidence scores.** The output file carries the mapping result only. There is no `matchScore` and no `cfs`, so you cannot rank or triage the results by confidence the way you can on the API.
- **Rows beyond the batch limit are skipped silently.** 100,000 rooms are processed per batch by default. Rows beyond that are skipped, and the run still finishes with status `Completed`. Always compare the row count of your output file against your input file.
- **Column headers are matched exactly, and letter case matters.** A misspelled or differently cased header is not reported as an error. The column is read as empty instead, and the file is processed as though you had sent no value at all.
- **A run is all or nothing.** If one hotel in the file cannot be processed, the whole run fails and no partial output is uploaded. You resubmit the entire file.
- **`Completed` does not guarantee an output file.** If the run succeeds but uploading the output fails, the status still reads `Completed`. Treat a missing output file as a run to raise with support rather than as an empty result.
- **There is no idempotency key.** Re-uploading the same file starts a new and independent run. Nothing detects or merges duplicate submissions.
- **There is no per row error report.** The only failure detail is a run level status and reason, so a problem with a single row is not attributed to that row.
- **Supply `VervotechHotelId` wherever you have it.** When a hotel cannot be resolved to a Vervotech ID, its rooms are grouped only among themselves and carry a placeholder identifier in the output, which cannot be joined to your Vervotech content.
- **File based runs are processed in English.** The `culture` option is not available on this path.

## Choosing

- Choose **real time** for live pricing and availability flows, for anything that needs a confidence score, and whenever your own mapping rules must apply.
- Choose **asynchronous** when a single hotel carries more rooms than the real time path accepts and you can tolerate polling for the result.
- Choose **file based** for one off or periodic bulk analysis across many hotels, for catalogue onboarding, and for evaluating coverage before you build an integration.
