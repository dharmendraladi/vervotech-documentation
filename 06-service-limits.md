# Service Limits

The limits below are the service limits and operational characteristics of the room mapping service. They describe what the service accepts and how it behaves.

**These are not availability, latency or throughput commitments.** Those are contractual rather than technical, and are covered under Service levels below.

## Request and file limits

| Limit | Value |
| --------- | ---------- |
| Rooms per real time request | The payload limit configured on your account. The general guidance is 1,000 rooms per request; above that, use the asynchronous path. |
| Hotels per real time or asynchronous request | One |
| Uploaded file size | 10 MB |
| Files per upload call | One |
| Rooms processed per file based batch | 100,000. Rows beyond this are skipped, and the run still finishes with status `Completed`. |
| Hotels per file based submission | Not limited, within the file size and row limits |
| Daily API calls | A per day call quota applies to your account. Exceeding it returns status code `1012`. |

For volumes above the 10 MB upload limit, an SFTP based submission is available. Contact the Vervotech mappings team to have it configured.

## Processing behaviour

* Real time requests are answered in the response.
* Asynchronous requests return a `traceId` immediately, and the result is retrieved by polling.
* File based runs are queued and processed in the background. There is no progress endpoint, so poll the status endpoint at a sensible interval, and treat a run as ready only once its status reads `Completed`.

## Service levels

Availability, throughput and response time commitments are contractual rather than technical, so they are set out in your agreement with Vervotech rather than in this specification. Contact customersuccess@vervotech.com for the service levels that apply to your subscription, or to discuss raising any of the limits above for a specific integration.
