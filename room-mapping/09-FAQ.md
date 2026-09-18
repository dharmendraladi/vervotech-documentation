# FAQ

This FAQ consolidates the questions Vervotech customers most frequently ask about Room Mapping, grouped by audience.

> ℹ️ **Note:** If your question isn't answered here reach out to **support@vervotech.com**.

---

### Q: What problem does Room Mapping solve at a product level?

**A:** Travel platforms aggregate inventory from many suppliers, and each supplier describes the same physical room differently. Without a unifying layer, search results show duplicates, comparing prices is unreliable, and travelers get confused. Room Mapping groups equivalent rooms across suppliers into a single standardized record so your platform can show one room per type with the best available rate, improving conversion and trust.

---

### Q: What does a "successful" Room Mapping outcome look like?

**A:** Two signals indicate Room Mapping is working as intended:

- **Reduced duplicates:** fewer near-identical rows in search and shopping results for the same hotel.
- **Higher confidence on matched rates:** most mapped rates carry a `MatchScore` more than 90, meaning the system is highly confident the supplier's room belongs to a known standardized room group.

---

### Q: Will any rooms be silently dropped from the response?

**A:** Rooms are not discarded for being hard to map. Rooms that match a known standardized group are mapped to that group, and rooms with insufficient signal to map confidently are returned in their own group rather than dropped, so an unmappable room still comes back to you.

One thing to be aware of when reconciling counts: identical rooms sent more than once within a request are deduplicated internally and re-expanded onto the response afterwards. Compare your input and output counts as a matter of routine, and treat any difference as worth raising with support rather than as expected behaviour.

---

### Q: How do we control which suppliers' content is used for mapped rooms?

**A:** Two configuration levers govern content assembly:

- **`ProviderContentType`:** `Merged` combines fields from multiple providers in the order you specify; `PreferredProvider` uses the first provider in your list that has matching content for the room; `None` skips provider content altogether. Any other value is rejected with status code `1009`. The value is matched case insensitively but is not trimmed, so `" Merged"` with a leading space is rejected.
- **`ProviderContentPreference`:** an ordered list of provider names. The system walks this list to find content (such as images and descriptions).

This lets you balance richness (Merged) against editorial control or commercial preference (PreferredProvider).

> ⚠️ **Casing matters here in a way it does not elsewhere.** Entitlement on `ProviderContentPreference` is checked case insensitively, but the content lookup that follows is case sensitive. A name such as `hotelbeds` can therefore pass validation and then silently match no content at all. Copy provider names from the provider names endpoint exactly as returned.

---

### Q: What inputs improve mapping quality the most?

**A:** Beyond the mandatory `RoomName`, `Provider` and `ProviderHotelId`, the highest-leverage fields are:

- **code:** the supplier's room code. Optional, but the single most valuable field to add.
- **Bed:** bedding configuration (e.g., "1 King", "2 Doubles")
- **View:** view designation (e.g., "City View", "Ocean View")
- **Description:** supplier room description
- **Attributes:** room attributes

---

### Q: Which endpoint should I use, sync or async?

**A:** Use the synchronous **Map Rooms** endpoint for real-time shopping flows where latency matters and your batch fits within the per-request payload limit. Use the asynchronous **Map Large Rooms Async** endpoint for large batches: submit the job, then poll **Get Async Room Mapping Response** for the result. Both asynchronous calls are `POST`. For bulk historical mapping or scheduled backfills, use the file-based offline workflow.

Note that the asynchronous response omits a few fields the synchronous one returns. See [Choosing Your Integration Path](01-choosing-your-integration-path.md) for the full list.

---

### Q: What fields are mandatory in a Map Rooms request?

**A:** Per room rate, the following are required:

| Field             | Purpose                                                                |
| ----------------- | ---------------------------------------------------------------------- |
| `index`           | Unique index for this room rate within the request                     |
| `provider`        | Supplier name, matching the provider names your account is entitled to |
| `providerHotelId` | Supplier's hotel identifier                                            |
| `roomName`        | Supplier's room name string                                            |

These four are the only per room fields a request is rejected for. `roomRates` (the array itself) and a valid `accountId` / `token` are also required at the request level, and `correlationId` is required as a header on this endpoint.

Everything else is optional, including the room code, which is sent as `code` rather than `RoomCode` on the request. Optional does not mean unimportant: `code`, `description` and `attributes` are the fields that most improve match quality.

One catch on `index`: it is a number and defaults to `0` when omitted, so two rooms that both leave it out collide on `0` and the request is rejected with status code `1007`.

---

### Q: What is the `MatchScore` (confidence score) and how should I use it?

**A:** The `MatchScore` is a numeric indicator of how strongly an input rate matches its assigned standardized room group. It starts at 100 and subtracts penalties for missing or conflicting evidence, drawing on attributes such as room category, bed type, view and other features. Higher values indicate stronger matches. Typical usage:

- **High scores:** display the mapped grouping confidently in production.
- **Low scores:** optionally route through a quality-review queue or fall back to a per-supplier display until confidence improves.

A second score, `cfs`, is returned alongside it. It continues from `matchScore` and subtracts a further set of softer penalties, so it is always at or below `matchScore` for the same rate.

Two things to know before you set a threshold. Both scores are clamped at a floor, and the floors differ and are configurable per account, so a value sitting at the floor means "at or below" rather than exactly that number. And neither is a percentage, so do not present either to travelers as a confidence percentage.

The exact threshold is up to your product, but most teams pick a value during validation and tune it from there.

---

### Q: What's the difference between `Merged` and `PreferredProvider` content types?

**A:**

| Mode                    | Behavior                                                                                                                                                                                                                                                |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`Merged`**            | Combines content (e.g., images, descriptions) from multiple providers in the order specified in `ProviderContentPreference`, filling in fields where any provider has data, producing a richer, more complete result.                                   |
| **`PreferredProvider`** | Uses content from the **first** provider in `ProviderContentPreference` that has matching room codes for that group. If that provider lacks content, the system falls through to the next provider in the list, but it does not merge across providers. |
| **`None`**              | Skips provider content entirely. Use it when you want the mapping result without images or descriptions.                                                                                                                                                |

Choose `Merged` for maximum richness. Choose `PreferredProvider` when you want a single, editorially consistent voice per room.

---

### Q: How do I get room images in the response?

**A:** Two request fields drive image inclusion:

- **`ProviderContentType`:** set to `Merged` or `PreferredProvider` (see above).
- **`ProviderContentPreference`:** ordered list of provider names whose content (including images) should be considered.

Images are returned only when a matching room code is found in one of the listed providers' content. If none of the listed providers has matching content for that room, content may still be drawn from another provider present in your request rather than omitted, so do not rely on an empty list as a guarantee that nothing will be returned.

Remember that the content lookup matches provider names case sensitively, so a name whose casing differs from the provider names endpoint will silently match nothing.

---

### Q: What's the recommended way to handle a `1011` (Unauthorized providers) error?

**A:** First, call **Get Provider Names** to confirm exactly which providers your account is entitled to map. That list is scoped to your account rather than being a global master list. Compare the names you are sending in the `provider` field: the check is case sensitive and does not trim, so a difference in casing or a stray leading or trailing space is enough to trigger `1011`. If a provider you expect is not in the list, contact Vervotech to enable it for your account before retrying.

`1011` also covers two conditions that are easy to mistake for an unentitled room provider: an unentitled name in `ProviderContentPreference`, and an account with no providers configured at all. The message says which applies.

Note as well that the file based path checks against a differently scoped list, so a provider accepted in a file upload can still return `1011` here.

---

### Q: Which languages are supported in the response?

**A:** Vervotech supports the following BCP-47 codes today: `en-US`, `es-ES`, `tr-TR`, `fr-FR`, `it-IT`, `ar-SA`. More may be added in future. Supply the desired language code in the appropriate request field (refer to the API reference for the exact parameter name in the endpoint you are calling).

---
