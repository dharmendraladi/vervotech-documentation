
# Getting Started

> Vervotech Room Mapping offers high-precision, room-level mapping by leveraging proprietary algorithms and advanced machine learning models to accurately group equivalent room types across multiple suppliers. This ensures a consistent, deduplicated room experience, enabling cleaner displays and more informed decision-making for travelers.

---

## What is Room Mapping?

Travel and hospitality platforms typically aggregate inventory from many Suppliers, Bedbanks, GDSs, OTAs, and direct-connect partners. Each supplier describes the same physical room differently. One supplier may call a room **"Deluxe King City View"**, another may list it as **"DELUXE 1KNG CTY"**, and a third may simply send **"DLX city view 1 King Bed"**. Without a unifying layer, the same hotel room appears multiple times in search results, prices cannot be compared apples-to-apples, and travelers see noisy, confusing inventory.

**Room Mapping** is the process of grouping equivalent room descriptions from different suppliers into a single, standardized room. Vervotech's Room Mapping product performs this grouping automatically using proprietary algorithms and machine-learning models that interpret room names, descriptions, bedding, views, and other attributes, producing one canonical room record per unique room type at each property.

---

## Key benefits and use cases

Room Mapping unlocks measurable improvements across product, engineering, and commercial functions.

- **Cleaner search and booking experiences.** Travelers see one row per room instead of 5 to 10 near-duplicates, reducing decision fatigue and improving look-to-book ratios.
- **Best-rate selection.** Once equivalent rooms are mapped, platforms can confidently surface the cheapest rate per room across suppliers.
- **Higher conversion and ADR.** Cleaner displays correlate with better conversion; mapped inventory makes upsell paths (e.g., upgrading from Standard to Deluxe) clearer.
- **Faster supplier onboarding.** New suppliers plug into the same standardization layer without bespoke parsing logic per integration.
- **Reduced operational overhead.** Customer support tickets about "wrong room booked" or "duplicate listings" drop materially once a mapping layer is in place.
- **Analytics and reporting.** Mapped room IDs provide a stable key for revenue analytics, competitive pricing analysis, and supplier performance comparisons.

**Common use cases:**

| Use Case                             | Outcome                                             |
| ------------------------------------ | --------------------------------------------------- |
| OTA search results consolidation     | One row per unique room across all suppliers        |
| Meta-search rate comparison          | Comparable rates for the same room across suppliers |
| Tour operator and wholesaler portals | Consistent inventory presentation to B2B partners   |

---

## How it works

At a high level, Room Mapping ingests raw room data from one or more suppliers, applies standardization and grouping logic, and returns enriched, mapped room records.

### Workflow overview

1. **Input:** Your system sends room data (room name, room code, provider identifier, hotel identifier, and optional attributes such as bedding, view, refundability, and board basis) to Vervotech.
2. **Normalization:** Vervotech's algorithms parse the raw text, extract structured attributes (category, bed type, view, occupancy, amenities), and resolve aliases and abbreviations.
3. **Grouping:** Equivalent rooms across suppliers are grouped into a single standardized room record with a confidence score reflecting match strength.
4. **Enrichment** _(optional)_**:** Mapped rooms can be returned with merged or preferred-provider content (images, descriptions) based on your configuration.
5. **Output:** Your system receives a structured response containing standardized room names, attributes, group identifiers, and per-rate match scores.

![image.png](https://api.apidog.com/api/v1/projects/898598/resources/375992/image-preview)

### Integration modes

Vervotech offers multiple integration modes to fit different operational profiles. Choose based on your latency requirements, batch sizes, and whether you need real-time or scheduled mapping.

| Mode                                                   | Best For                                          | Notes                                           |
| ------------------------------------------------------ | ------------------------------------------------- | ----------------------------------------------- |
| **Synchronous Mapping API** (`Map Rooms`)              | Real-time mapping during search or shopping flows | Lowest latency; recommended payload sizes apply |
| **Asynchronous Mapping API** (`Map Large Rooms Async`) | Large batches that exceed sync payload limits     | Submit job, poll for results                    |
| **File-Based Room Mapping (Offline)**                  | Bulk historical mapping or scheduled backfills    | CSV in / CSV out via shared file location       |

> ℹ️ **Note:** Most production deployments use the synchronous API for live traffic.

---

## Quick-start prerequisites

Before integrating, ensure you have the following in place.

- **Account credentials:** an `accountId` and an authentication `token` provisioned by Vervotech while onboarding.
- **A short-list of supplier providers:** the supplier names you want mapped, matching the provider names your account is entitled to.
- **Hotel identifiers:** the `ProviderHotelId` for each property whose rooms you want mapped.
- **Per-room raw data:** at minimum `RoomName`, `Provider` and `ProviderHotelId`. These three are the only per-room fields a request is rejected for. Send the room code as well wherever you have it, as `code` on the request, together with any of `Description`, `Bed`, `View`, `Refundability` and `BoardBasis`, since these significantly improve match quality.

---

## Getting started

The fastest path from zero to first successful mapping call.

### Confirm access and download the Postman collection

Vervotech publishes a Postman collection that includes preconfigured requests for every Room Mapping endpoint. Import it, set your `accountId` and `token` as collection variables, and you can issue a working call within minutes.

Download the collection below, or paste its URL into Postman's **Import → Link** to import it directly.

<div align="center">
<a href="https://roommapping.vervotech.com/docs/vervotech-room-mapping.postman_collection.json">
  <img src="https://api.apidog.com/api/v1/projects/898598/resources/354277/image-preview" alt="Postman.jpg" width="150" height="37"/>
</a>
</div>

## Support

For onboarding questions, integration support, or feedback on mapping quality, reach out to **support@vervotech.com**.
