# Integration Guidelines and Go Live

A practical sequence for integrating room mapping, and a checklist to run before you go live.

## 1. Credentials and headers

Every room mapping call carries these headers:

| Header          | Required                             | Purpose                                                                                                                        |
| --------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| `accountId`     | Yes                                  | Your Vervotech room mapping account.                                                                                           |
| `token`         | Yes                                  | Your room mapping API token.                                                                                                   |
| `correlationId` | Yes on `POST /api/2.0/mapping/rooms` | A unique value per request, which you generate. Quote it when raising support requests, because it is how a request is traced. |
| `culture`       | No                                   | Response localisation. See [References](07-references.md) for the supported values.                                            |

`correlationId` is enforced only on `POST /api/2.0/mapping/rooms`, where omitting it returns HTTP 403. Send it on every call regardless, because it is what makes a request traceable when you raise it with support. A `runId` header is accepted as an alias, and when neither is sent a value is generated for you, which means a request you did not tag is far harder to find later.

This applies to the file based path too. `POST /api/MappingTool/FileBasedMapping`, `GET /api/MappingTool/GetOfflineMapping` and `GET /api/MappingTool/DownloadFile` all require `accountId` and `token`, and each one answers only for the account those headers name. Asking for another account's run history, or for a `runId` that belongs to another account, is refused with the same error as an invalid token.

Use `demoAccount` while you build. Contact the Vervotech mappings team for your own credentials.

## 2. Pull the reference data first

Before you send any mapping request, retrieve and cache the master reference data, and build your feed against it rather than against hardcoded strings:

- The provider names your account is entitled to, which are the only valid values for `provider`
- The room attributes endpoint, which returns the recognised keys for `attributes` with any your account ignores already removed
- The master room amenities

## 3. Build the request

- Send one hotel per request on the real time and asynchronous paths. This is a requirement, not something the API checks for you. If rooms for more than one hotel are sent together, the request is accepted and the additional hotel identifiers are discarded silently, so the rooms are mapped against a single resolved hotel and no error is returned.
- A unique `index` on every room. This is how results are matched back to your input. Note that `index` is a number and defaults to `0` when omitted, so two rooms that both leave it out collide and the request is rejected with status code `1007`.
- Send `code`, `description` and `attributes` wherever you have them. See [Preparing Your Request Data](02-preparing-your-request-data.md).

## 4. Choose the path

See [Choosing Your Integration Path](01-choosing-your-integration-path.md). In short: real time for live flows, asynchronous for a single hotel with a large room set, file based for bulk catalogue work.

## 5. Handle responses correctly

- **Check the response body, not only the HTTP status.** A rejected request can still return HTTP 200, with `success` set to `false` and a `statusCode` carrying the reason. Branch on `statusCode`.
- The HTTP status is not a reliable signal on its own, and it is not consistent across rejections. Most validation failures return HTTP 200 with the reason in the body, but some rejections return HTTP 400 carrying the same shape of body. Read `statusCode` in both cases.
- Status code `1012` covers two different rejections: a payload that exceeds your per request limit, and a daily call quota that has been exhausted. Those need different responses from your integration, so read the accompanying message rather than the code alone. They also differ in HTTP status, the payload limit returning 200 and the quota returning 400, which is another reason not to branch on it.
- Treat `1011` as a configuration problem rather than a transient one. It means a provider or content preference that your account is not entitled to.
- Back off and retry on transient transport failures. Do not retry validation failures, which will fail identically every time.
- Poll at a sensible interval. Neither the asynchronous path nor the file based path has a progress endpoint, so continuous polling adds load without returning an answer any sooner.

## 6. Pre go live checklist

- Every `provider` value is sourced from the provider names endpoint and matches exactly, including its casing, with no leading or trailing spaces.
- Every `attributes` key is sourced from the master room attributes endpoint, and every value is one of `Yes`, `Allowed`, `No` or `NotAllowed`.
- `index` is unique within each request, and results are matched back by it.
- Request sizes are checked against your account payload limit before sending, with the asynchronous path used above it.
- `correlationId` is generated per request and retained in your logs.
- Retry and back off behaviour is implemented and has been tested against a forced failure.
- Error handling branches on `statusCode`, including `1011` and `1012`, and not on the HTTP status alone.
- Room names are passed through unmodified.
- If you use the file based path, your header row is identical to the input template linked in the File Based Room Mapping (Offline) section, and your process compares the output row count against the input row count.

## 7. Support

Raise integration issues at [the Vervotech support portal](https://support.vervotech.com/support/home), or write to support@vervotech.com. Always quote the `correlationId` of an affected request, together with the account and provider names involved.
