# Preparing Your Request Data

Mapping quality is bounded by the quality of what you send. The service standardises supplier room names into structured attributes and then groups rooms that describe the same thing, so every field you supply is evidence, and every field you omit is evidence the service has to infer or do without.

This section covers what is required, what is silently ignored, and what materially improves your results.

## Required and optional fields

| Field | Required | Notes |
| --------- | ---------- | ---------- |
| `roomRates` | Yes | Must contain at least one room. |
| `index` | Yes | Must be unique within the request. Results are returned against this value. |
| `roomName` | Yes | The supplier room name, as the supplier gave it to you. |
| `provider` | Yes | Must be a name from the master provider list, and your account must be entitled to it. |
| `providerHotelId` | Yes | The supplier's own identifier for the property. |
| `code` | No | Not rejected when missing, but strongly recommended. Send the room code, not the rate plan code. |
| `description` | No | Frequently carries bedding, view and occupancy detail that the room name omits. |
| `beds`, `view`, `boardBasis`, `refundability` | No | Used as supporting evidence where present. |
| `attributes` | No | Structured room features. See the value rules below. |

## Send supplier room names unmodified

Send the room name exactly as the supplier gave it to you. Cleaning, title casing, truncating or reordering the name before you send it removes the very signals the service uses. Abbreviations, punctuation, duplicated whitespace, embedded HTML and common misspellings are already handled for you.

## Attribute values are matched against a fixed set

Attribute keys are matched case insensitively, so `HasBalcony` and `hasbalcony` behave identically. **Attribute values are not free text.** Only four values are recognised, case insensitively:

| Value sent | Effect |
| --------- | ---------- |
| `Yes` or `Allowed` | The attribute is applied. |
| `No` or `NotAllowed` | The negative form of the attribute is applied, where one exists. |
| Anything else | **Silently ignored.** |

This is the most common source of quietly missing attributes. `true`, `1`, `Y`, `enabled` and an empty string all fall into the last row. They are not rejected and no warning is returned. They simply have no effect, and the room is mapped as though you had never sent the attribute at all.

Not every attribute has a negative form. Where one does not exist, a `No` value is ignored rather than rejected. Use the master room attributes endpoint for the list of keys that are recognised.

## Header spelling matters more than it looks

On the file based path, column headers are matched exactly and letter case matters, so a header that your pipeline has helpfully normalised is read as an empty column rather than reported as an error.

The clearest illustration is on the output side. The occupancy column in the output file is named `MappedOcccupancy`, with three letter c characters. Any integration that corrects that spelling on ingest, or that assumes the obvious spelling, silently reads an empty column. Match header strings exactly in both directions, and check your header row against the input template linked in the File Based Room Mapping (Offline) section before every upload.

## Non-English content

When non-English text is detected in your room details, the group carries the warning code `NON_ENGLISH_CONTENT_DETECTED`. Treat that warning as a quality signal rather than noise. Token normalisation is skipped for that content and the room category falls back to the room name as a whole, so grouping is markedly less reliable than it is for English content. Send the `culture` header for the languages listed under References, and review any group that carries this warning.

## What makes a room description strong

Where several rooms in a group could serve as the reference, the best evidenced room is preferred. The signals below are listed in decreasing order of weight, and they are a good guide to what is worth sourcing from your suppliers:

* A room that matches a Vervotech master room, and a master room that has been verified
* A clear, non generic room category
* Occupancy, both the bed occupancy type and the guest count
* Bed types
* Room size
* View
* Bedroom count
* Additional room features

A name that can only be resolved to a suspect category counts against a room rather than for it. A bare name such as `Standard Room` is not itself penalised, but it carries no distinguishing detail, so it gives the service nothing to group on.

## Reading the scores

* `matchScore` and `cfs` are clamped at a floor of 60. A value of 60 therefore means "60 or below" rather than exactly 60, and neither value should be read as a percentage.
