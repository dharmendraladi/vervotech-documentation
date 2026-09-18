
# Room Mapping Customizations

This document describes how the Room Mapping engine decides that two supplier rooms are equivalent, the rule types that govern grouping behavior, the configuration parameters available to integrators, and a worked case study showing how the rules combine on real input.

> ℹ️ **Who configures these rules?** During client onboarding, the Vervotech Support team configures rules based on each client's property portfolio and mapping requirements. Customers do not typically self-serve rule configuration, but understanding the rule taxonomy helps integrators interpret behavior and request adjustments accurately.

---

## 1. Overview of the rule engine

Room Mapping Rules define how room data from multiple sources, such as channel managers, OTAs and PMS systems, is normalized, grouped, and displayed to end users. The rules ensure that similar rooms across different suppliers are matched correctly and presented with a consistent, unified display name.

The engine processes each input room through three logical phases:

1. **Parse and normalize.** The engine reads the raw `RoomName`, `RoomCode`, `Description`, and any provided attributes (`Bed`, `View`, `Refundability`, `BoardBasis`). It expands abbreviations, resolves common synonyms, and extracts structured fields such as room category, bed type, view, and amenities.
2. **Apply mapping rules.** The rule types described below are evaluated. Rules are applied in rank order when multiple could match.
3. **Group and emit output.** Equivalent rooms across providers are placed under the same `Group` for the property. Each output record carries the standardized name, mapped attributes, group identifier, and a per-rate `matchScore` (confidence). Rooms that cannot be matched confidently are returned in a separate group rather than discarded.

**A room that cannot be matched is not discarded.** On the normal path every input room is represented in the output, mapped or unmapped, which is what makes the engine safe to place inline in a shopping flow. Identical rooms sent more than once in a request are deduplicated internally and re-expanded afterwards, so treat a response whose rate count differs from your input count as worth investigating rather than as impossible.

---

## 2. Mapping rules reference

The engine has five named rule types. Alongside them sit ten account level toggle rules that switch specific grouping behaviours on or off, such as disabling bed type grouping or enabling soft bed grouping. All of them are owned and configured by the Vervotech Support Team.

The four described in detail below are the ones integrators most often ask about. The fifth, the **Master Mapping Rule**, governs how a room is matched against Vervotech master data.

| #   | Rule Name                    | Description                                                                                                                  | Input                                                               | Expected Output                                                              | Rank                       |
| --- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- | ---------------------------------------------------------------------------- | -------------------------- |
| 1   | **Bedding Mapping Rule**     | Merge similar bed types into one group and set which bed type is preferred for display.                                      | Source bed types (e.g., `Double`, `Queen`, `King`)                  | Single bedding group with a preferred display label (e.g., "King preferred") | Configurable (1 = highest) |
| 2   | **Attribute Mapping Rule**   | Ignore specific room attributes during grouping so that rooms differing only by those attributes are still matched together. | List of attributes to ignore (e.g., `HasBalcony`, `SmokingAllowed`) | Rooms grouped under a single unified name despite the ignored attribute      | Configurable (1 = highest) |
| 3   | **Category Similarity Rule** | Group similar room categories under a single unified category name.                                                          | Source categories (e.g., `Standard`, `Classic`, `Basic`)            | Unified category display name (e.g., `Standard`)                             | Configurable (1 = highest) |
| 4   | **View Matching Rule**       | Map similar room view descriptions to a single unified display name.                                                         | Source views (e.g., `Sea View`, `Oceanfront`, `Partial Ocean View`) | Unified view name (e.g., `Ocean View`)                                       | Configurable (1 = highest) |

Rules carry a numeric `Rank` that sets evaluation order, with `1` evaluated first. Ranks below 1 are rejected. Toggle rules carry no rank.

Each rule is described in detail below.

---

### 2.1 Bedding Mapping Rule

**Purpose:** Merge similar bed types into a single group and set which bed type is preferred for display.

**When to use:** When a property receives room data with multiple bed type labels (e.g., "Double", "Queen", "King") that should be treated as equivalent for matching purposes.

**Worked example of the rule pattern:**

| Source Bed Type | Direction | Merged Group | Direction | Display Name    |
| --------------- | --------- | ------------ | --------- | --------------- |
| Double          | ➜         |              |           |                 |
| Queen           | ➜         | One Group    | ➜         | Queen preferred |
| King            | ➜         |              |           |                 |

**Configuration parameters:**

| Parameter          | Description                                     | Example               |
| ------------------ | ----------------------------------------------- | --------------------- |
| Source Bed Types   | List of bed types to merge into one group       | `Double, Queen, King` |
| Display Preference | The bed type label shown to end users           | `Queen preferred`     |
| Rank               | Rule evaluation order when multiple rules apply | `1` (highest)         |

**Best practices:**

- Always confirm the client's preferred display bed type before configuring.
- Group only genuinely interchangeable bed types. Do not merge Single with Queen.
- Set rank carefully when a room could match multiple bedding rules.

> ✅ **Tip:** A hotel lists rooms as "Double Room" on Supplier A and "Queen Room" on Supplier B. With this rule, both are grouped together and displayed as "Queen preferred" to the end user.

---

### 2.2 Attribute Mapping Rule

**Purpose:** Ignore specific room attributes during the grouping process so that rooms differing only by those attributes are still matched together.

**When to use:** When certain attributes (e.g., `HasBalcony`, `SmokingAllowed`) should not affect whether two rooms are considered the same type.

**Worked example of the rule pattern:**

| Room Source Data            | Ignored Attribute       | Grouped Result |
| --------------------------- | ----------------------- | -------------- |
| Deluxe Room with Balcony    | `HasBalcony`            | Deluxe Room    |
| Deluxe Room without Balcony | `No Attribute detected` | Deluxe Room    |
| Deluxe Room (Smoking)       | `SmokingAllowed`        | Deluxe Room    |

**Configuration parameters:**

| Parameter            | Description                                                                   | Example                      |
| -------------------- | ----------------------------------------------------------------------------- | ---------------------------- |
| Ignored Attributes   | Attributes excluded from matching logic                                       | `HasBalcony, SmokingAllowed` |
| AttributeMappingType | How the attribute is handled: `Ignore`, `Demerge`, or `IgnoreBasedOnSupplier` | `Ignore`                     |
| Rank                 | Rule evaluation order when multiple rules apply                               | `1` (highest)                |

The rule applies to the whole account. There is no per property variant.

**Best practices:**

- Only ignore attributes that the client confirms are irrelevant for their use case.

> ⚠️ **Warning:** Aggressive attribute-ignoring can collapse rooms that travelers actually want to distinguish (e.g., smoking vs. non-smoking). Confirm the attribute list explicitly before going live, because it takes effect across every property on the account.

---

### 2.3 Category Similarity Rule

**Purpose:** Group similar room categories under a single unified category name.

**When to use:** When different suppliers or channels use different category labels for what is essentially the same room tier.

**Worked example of the rule pattern:**

| Source Category | Direction | Unified Category |
| --------------- | --------- | ---------------- |
| Standard        | ➜         |                  |
| Classic         | ➜         | Standard         |
| Basic           | ➜         |                  |

**Configuration parameters:**

| Parameter         | Description                                   | Example                    |
| ----------------- | --------------------------------------------- | -------------------------- |
| Source Categories | List of category names to treat as equivalent | `Standard, Classic, Basic` |
| Unified Category  | The display name for the merged group         | `Standard`                 |

**Best practices:**

- Group only valid categories together, and review the grouping on a monthly or quarterly basis.
- Category matching is case insensitive, so variants such as `STANDARD` and `standard` resolve to the same category without needing separate entries.

---

### 2.4 View Matching Rule

**Purpose:** Map similar room view descriptions to a single unified display name.

**When to use:** When rooms across sources have different view labels that should resolve to the same view type for the end user.

**Worked example of the rule pattern:**

| Source View        | Direction | Unified View |
| ------------------ | --------- | ------------ |
| Sea View           | ➜         |              |
| Oceanfront         | ➜         | Ocean View   |
| Partial Ocean View | ➜         |              |

**Configuration parameters:**

| Parameter         | Description                            | Example                                    |
| ----------------- | -------------------------------------- | ------------------------------------------ |
| Source Views      | List of view names to merge            | `Sea View, Oceanfront, Partial Ocean View` |
| Unified View Name | The display name for all matched views | `Ocean View`                               |

**Best practices:**

- Confirm with the client whether partial views (e.g., "Partial Ocean View") should be grouped with full views or kept separate.
- View matching is always case insensitive. There is no setting to make it case sensitive, so variants such as `OCEANFRONT` and `oceanfront` never need separate entries.

> ✅ **Tip:** A beachfront resort lists rooms as "Sea View" on one channel, "Oceanfront" on another, and "Partial Ocean View" on a third. This rule unifies them all under "Ocean View."

---

## 3. Board-basis normalization

Board-basis strings are normalized to one of six standard values: **Room Only**, **Bed and Breakfast**, **Half Board**, **Full Board**, **All Inclusive** and **SelfCatering**. Anything the vocabulary does not recognise is returned as `Unknown`.

The full keyword vocabulary behind each value is on the References page, which is the single source for it.

---

## 4. Case study: mapping a multi-supplier hotel

The following case study walks through a representative example combining all four rule types. It uses a hypothetical hotel and three suppliers, but reflects the kinds of inputs platforms regularly send.

### 4.1 Scenario background

A mid-size online travel platform aggregates inventory from three suppliers, _Supplier A_, _Supplier B_ and _Supplier C_, for **Hotel Aurora**, a 180-room urban property. Each supplier returns the property's flagship "Deluxe King, Ocean View" room, but each describes it differently:

| Supplier   | RoomName                                             | RoomCode     | Bed          | View                 | Other       |
| ---------- | ---------------------------------------------------- | ------------ | ------------ | -------------------- | ----------- |
| Supplier A | `Deluxe King Room with Sea View, Balcony`            | `DLX-K-SV-B` | `1 King Bed` | `Sea View`           | Has balcony |
| Supplier B | `DLX 1QUEEN OCEANFRONT`                              | `DK-OF`      | `Queen`      | `Oceanfront`         | None        |
| Supplier C | `Classic Room, Double, Partial Ocean View (Smoking)` | `CLS-D-01`   | `Double`     | `Partial Ocean View` | Smoking     |

### 4.2 Challenge

Without mapping, the platform's search results show three separate rows for what is essentially the same room tier. Travelers see what looks like three distinct rooms with three different prices, when in reality only the price meaningfully differs. Two business problems follow:

- **Conversion friction.** Travelers spend extra time investigating, and room duplication makes the process more confusing, often leading them to switch to a different provider.
- **Unreliable rate comparison.** With the same room appearing three times under three descriptions, the platform cannot tell which supplier is genuinely cheapest for a given room, so it cannot surface a best rate with confidence.

### 4.3 Mapping rules applied

The engine processes the three records, applying each of the four rule types:

1. **Bedding Mapping Rule.** `Double`, `Queen`, and `King` are merged into one bedding group with display preference set to _Queen preferred_. Supplier A's "1 King Bed", Supplier B's "Queen", and Supplier C's "Double" all resolve to the same canonical bed group.
2. **Attribute Mapping Rule.** `HasBalcony` is configured as an ignored attribute for this client. Supplier A's balcony attribute is therefore not used to separate it from the others. `SmokingAllowed` is also ignored, so Supplier C's smoking variant is not split off.
3. **Category Similarity Rule.** `Deluxe` and `Classic` are configured as equivalent categories with unified display name _Deluxe_. Supplier C's "Classic Room" is therefore unified with Supplier A's and Supplier B's "Deluxe" rooms.
4. **View Matching Rule.** `Sea View`, `Oceanfront`, and `Partial Ocean View` are merged into the unified view _Ocean View_. Case-insensitive matching ensures variants like "OCEANFRONT" or "ocean view" all resolve correctly.

The cumulative effect: all three rooms are recognized as the same standardized room despite different bed labels, different view names, balcony/no-balcony differences, smoking/non-smoking differences, and different category strings.

### 4.4 Result / output

The mapping engine returns a single standardized group containing all three supplier rates. Conceptually:

```json
{
  "standardRooms": [
    {
      "standardName": "Deluxe, Queen, Ocean view",
      "mappedRates": [
        {
          "inputIndex": "1",
          "provider": "Supplier A",
          "roomCode": "DLX-K-SV-B",
          "matchScore": 97,
          "cfs": 94
        },
        {
          "inputIndex": "2",
          "provider": "Supplier B",
          "roomCode": "DK-OF",
          "matchScore": 92,
          "cfs": 88
        },
        {
          "inputIndex": "3",
          "provider": "Supplier C",
          "roomCode": "CLS-D-01",
          "matchScore": 86,
          "cfs": 81
        }
      ]
    }
  ]
}
```

Supplier A receives the highest `matchScore`, being the closest textual match to the canonical name. Supplier B scores slightly lower because its bed and view diverge textually but resolve via rules. Supplier C scores lowest among the three but is still confidently grouped because three of the four rule types contribute supporting evidence. `cfs` sits at or below `matchScore` on every rate, since it subtracts a further set of softer penalties. Both are penalty ledgers rather than percentages, and both are floored, so do not present either figure to travelers as a confidence percentage. See Preparing Your Request Data for how to read them.

### 4.5 Outcome and business value

After deploying mapping, the platform consolidates the three rows into a single search result for Hotel Aurora's Deluxe Queen, Ocean View, with the lowest-priced supplier surfaced as the headline rate. Three downstream effects follow:

- **Cleaner UX.** Travelers see one row per room type, not three. Decision fatigue drops.
- **Better margin awareness.** The platform can route bookings to whichever supplier offers the best rate per room without risk of misrouting due to ambiguous descriptions.
- **A stable key for analytics.** One standardized room per room type gives revenue reporting and supplier performance comparison something consistent to aggregate on, instead of three descriptions of the same room.

---

For rule configuration changes and integration support, contact **support@vervotech.com**.
