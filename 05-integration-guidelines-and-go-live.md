# Integration Guidelines and Go Live

A practical sequence for integrating room mapping, and a checklist to run before you go live.

## 1. Credentials and headers

Every room mapping call carries these headers:

| Header          | Required | Purpose                                                                                                                        |
| --------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `accountId`     | Yes      | Your Vervotech room mapping account.                                                                                           |
| `token`         | Yes      | Your room mapping API token.                                                                                                   |
| `correlationId` | Yes      | A unique value per request, which you generate. Quote it when raising support requests, because it is how a request is traced. |
| `culture`       | No       | Response localisation. See [References](07-references.md) for the supported values.                                            |

Use `demoAccount` while you build. Contact the Vervotech mappings team for your own credentials.

## 2. Pull the reference data first

Before you send any mapping request, retrieve and cache the master reference data, and build your feed against it rather than against hardcoded strings:

- The master provider names, which are the only valid values for `provider`
- The master room attributes, which are the only keys recognised in `attributes`
- The master room amenities

## 3. Build the request

- One hotel per request on the real time and asynchronous paths.
- A unique `index` on every room. This is how results are matched back to your input.
- Send `code`, `description` and `attributes` wherever you have them. See [Preparing Your Request Data](02-preparing-your-request-data.md).

## 4. Choose the path

See [Choosing Your Integration Path](01-choosing-your-integration-path.md). In short: real time for live flows, asynchronous for a single hotel with a large room set, file based for bulk catalogue work.

## 5. Handle responses correctly

- **Check the response body, not only the HTTP status.** A rejected request can still return HTTP 200, with `success` set to `false` and a `statusCode` carrying the reason. Branch on `statusCode`.
- Status code `1012` covers two different rejections: a payload that exceeds your per request limit, and a daily call quota that has been exhausted. Those need different responses from your integration, so read the accompanying message rather than the code alone.
- Treat `1011` as a configuration problem rather than a transient one. It means a provider or content preference that your account is not entitled to.
- Back off and retry on transient transport failures. Do not retry validation failures, which will fail identically every time.
- Poll at a sensible interval. Neither the asynchronous path nor the file based path has a progress endpoint, so continuous polling adds load without returning an answer any sooner.

## 6. Pre go live checklist

- Every `provider` value is sourced from the master provider names endpoint and matches exactly.
- Every `attributes` key is sourced from the master room attributes endpoint, and every value is one of `Yes`, `Allowed`, `No` or `NotAllowed`.
- `index` is unique within each request, and results are matched back by it.
- Request sizes are checked against your account payload limit before sending, with the asynchronous path used above it.
- `correlationId` is generated per request and retained in your logs.
- Retry and back off behaviour is implemented and has been tested against a forced failure.
- Error handling branches on `statusCode`, including `1011` and `1012`, and not on the HTTP status alone.
- Room names are passed through unmodified.
- If you use the file based path, your header row is identical to the input template linked in the File Based Room Mapping (Offline) section, and your process compares the output row count against the input row count.

## 7. Support

Raise integration issues at https://support.vervotech.com/support/home, or write to customersuccess@vervotech.com. Always quote the `correlationId` of an affected request, together with the account and provider names involved.
