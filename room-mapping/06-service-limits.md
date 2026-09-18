# Service Limits

The limits below are the service limits and operational characteristics of the room mapping service. They describe what the service accepts and how it behaves.

**These are not availability, latency or throughput commitments.** Those are contractual rather than technical, and are covered under Service levels below.

## Request and file limits

| Limit                                        | Value                                                                                                                                 |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Rooms per real time request                  | The payload limit configured on your account. The general guidance is 1,000 rooms per request; above that, use the asynchronous path. |
| Hotels per real time or asynchronous request | One                                                                                                                                   |
| Uploaded file size                           | 10 MB                                                                                                                                 |
| Files per upload call                        | One                                                                                                                                   |
| Rooms processed per file based batch         | 100,000 by default. Rows beyond this are skipped, and the run still finishes with status `Completed`.                                 |
| Hotels per file based submission             | Not limited, within the file size and row limits                                                                                      |
| Daily API calls                              | A per day call quota applies to your account. Exceeding it returns status code `1012`.                                                |

The file based batch size is a configured default rather than a fixed ceiling, so confirm the figure that applies to your account before you size a submission against it.

For volumes above the 10 MB upload limit, an SFTP based submission is available. Contact the Vervotech mappings team to have it configured.

## Processing behaviour

- Real time requests are answered in the response.
- Asynchronous requests return a `traceId` immediately, and the result is retrieved by polling. Both asynchronous calls are `POST`.
- File based runs are queued and processed in the background. There is no progress endpoint, so poll the status endpoint at a sensible interval, and treat a run as ready only once its status reads `Completed`.
- A `Completed` status does not by itself guarantee that an output file was produced. If the run succeeds but uploading the output fails, the status still reads `Completed`. Treat a missing output file as a run to raise with support rather than as an empty result.

## Service levels

Availability, throughput and response time commitments are contractual rather than technical, so they are set out in your agreement with Vervotech rather than in this specification. Contact support@vervotech.com for the service levels that apply to your subscription, or to discuss raising any of the limits above for a specific integration.
