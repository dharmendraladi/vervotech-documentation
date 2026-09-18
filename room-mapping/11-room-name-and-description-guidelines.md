# Room Name and Description Guidelines

This page is written for accommodation suppliers. Pass it on to the suppliers whose content you send to Vervotech, or apply it directly where you control the source content yourself.

It does not change how you send data to Vervotech. Room names you receive from a supplier should still be passed through unmodified, because embedded HTML, abbreviations and common misspellings are already handled for you on the way in. See [Preparing Your Request Data](02-preparing-your-request-data.md). What follows is about the content a supplier writes in the first place, which is where the detail either exists or does not. An abbreviation expanded later still cannot say which meaning the supplier intended by it.

The room name is read first and carries the most weight, so it should describe the physical room precisely, covering category, bedding, view and room location, and little else. Vague wording, or wording that really belongs to the rate, causes the same room to be split into several separate entries.

## What to keep out of the room name

**Promotional words.** Remove these entirely. They make the room category ambiguous, so the category can no longer be decided reliably from the name.

- Avoid: `Early Bird 20% Off`, `Best Deal`, `Special Offer`, `Last Minute`, `Member Rate`, `Save 15%`, `Hot Deal`, `Flash Sale`

**Stay length and dates.** These change over time while the room itself does not.

- Avoid: `3 Nights Minimum`, `Weekly Rate`, `Summer 2026`

**Marketing and emotive wording.** This is welcome in the description, but keep it out of the room name. It carries no attribute and only lengthens the name.

- Avoid: `sanctuary`, `escape`, `unforgettable`, `perfect for`, `best value`

**HTML tags and entities.** Send plain text only.

- Avoid: `<b>Deluxe</b> Room`, `Caf&eacute;`, `&nbsp;`, `&amp;`, `&#39;`
- Use: `Deluxe Room`

**Special and decorative signs.**

- Avoid: `★★★ Deluxe Room ★★★`, `Deluxe***Room`, `Deluxe|Room|Sea`, `Deluxe Room!!!`
- Use: `Deluxe Room, Sea View`

**Abbreviations.** Write full words. `SV` could mean sea view, side view or street view, and every abbreviation like it has to be guessed at.

- Avoid: `Sup. Dble Rm w/ SV`, `Dbl`, `NSMK`, `OV`, `CTV`
- Use: `Superior Double Room, Sea View`

**Mixed languages.** Keep the room name in one language, and tag which language it is. Do not mix two languages inside a single name.

- Avoid: `Chambre Double with Sea View`, `Habitación Doble Superior Room`, `Deluxe Zimmer with Balcony`
- Use: `Double Room, Sea View (tagged as English)`, `Chambre Double, Vue Mer (tagged as French)`

Integrators: on the Vervotech API the language of the response is selected with the `culture` header. See [References](07-references.md) for the values it supports.

**Occupancy is the exception, so please keep sending it.** Occupancy is needed and should not be stripped out. Values such as `Double Room (2 Adults + 1 Child)`, `Max 3 Pax`, `Room for 4 Guests` and `Single Use` are useful and should continue to be supplied.

## What the room name must state

Four attributes, each stated precisely. A vague value is worse than no value at all, because it cannot be classified, so the room is either grouped wrongly or not grouped at all.

**Category, always.** Use a defined tier: `Standard`, `Superior`, `Deluxe`, `Executive`, `Junior Suite`, `Suite`, `Studio`, `Apartment`, `Villa`, `Family Room`.

- Avoid: undefined tiers such as `Room`, `Type A`, `Category 3`, `Premium`, `Classic Plus`, `Comfort`. These cannot be ranked against anything.

These tiers are a recommended writing convention rather than a fixed list of values the service accepts.

**Bedding, always, and never blank.** Use `1 King Bed`, `1 Queen Bed`, `1 Double Bed`, `2 Single Beds`, `2 Double Beds`, `1 King Bed and 1 Sofa Bed`, `Bunk Beds`. Where a room genuinely sells as either configuration, `Double or Twin` is fine to send.

- Avoid: bedding left unstated, meaning an empty value, and `Bed Type on Request`, `Run of House`, `Various Bedding`.

Bedding left out of the room name is the single most common reason a room cannot be grouped.

**View, when one exists.** Use `Sea View`, `Garden View`, `City View`, `Pool View`, `Mountain View`, `Courtyard View`, `Lake View`, `No View`. Qualified values such as `Partial Sea View` are perfectly acceptable. What matters is following the same convention throughout: do not send `Sea View` for one room and `Ocean View` for another at the same hotel, because that variation causes extra grouping.

- Avoid: `View May Vary`, and abbreviations such as `SV` or `OV`.

**Room location, when it distinguishes one room from another.** Use `Ground Floor`, `High Floor`, `Top Floor`, `Main Building`, `Annexe`, `Tower Wing`, `Poolside`, `Beachfront`, `Corner`.

- Avoid: `Any Floor`, `Various Locations`, `Location Varies`, `Subject to Availability`.

## State bedding and view once, and separate them clearly

Where a room has more than one bed, join them with an explicit `or` for a choice, or `and` where both are present. If one bed type appears near the start of the name and another appears later, there is no way to tell which is meant.

- Unclear: `Queen Deluxe Room, Sea View, King Bed`
- Clear: `Deluxe Room, 1 Queen Bed or 1 King Bed, Sea View`
- Clear: `Deluxe Room, 1 King Bed and 1 Sofa Bed, Sea View`

The same applies to views.

- Unclear: `Sea View Deluxe Room, Garden View`
- Clear: `Deluxe Room, 1 King Bed, Sea or Garden View`

Bedding is never blank and never optional.

## Which features belong in the room name

**Include features specific to this room.** `Balcony`, `Terrace`, `Private Pool`, `Plunge Pool`, `Swim-Up`, `Jacuzzi`, `Sauna`, `Kitchenette`, `Full Kitchen`, `Two Bedrooms`, `Duplex`, `Loft`, `Separate Living Room`, `Accessible`, `Connecting`, `Garden Access`, `Direct Beach Access`, `Non-Smoking` where only some rooms are, and `Lounge Access`, `Butler Service` or `Club Level` where these are tied to that room alone.

**Exclude anything common to the whole hotel.** `Free Wi-Fi`, `Air Conditioning`, `Flat-Screen TV`, `Minibar`, `Safe`, `Hairdryer`, `Coffee Machine`, `Daily Housekeeping`, `Room Service`, `Free Parking`, `Gym Access`, `Spa Access`, `Pool Access`, `Breakfast Included`, `Airport Shuttle`, `Concierge`, `Laundry Service`, `Pets Allowed`.

Please do not send parking access, pool access or similar facilities in the room name unless the feature genuinely differentiates that room, or is unique to it: a private plunge pool, for example, or parking allocated to that room alone. Anything true of every room in the property belongs in the hotel record rather than the room name.

## Characters

**No HTML and no markup, in either field.** HTML tags (`<b>`, `<p>`, `<br/>`, `<li>`), HTML entities (`&nbsp;`, `&amp;`, `&eacute;`, `&#39;`) and markdown (`**bold**`, `_italic_`, `#`, `- list`) must not appear in the room name or the description.

Send the character itself rather than its entity, so `&` and not `&amp;`, `é` and not `&eacute;`.

Otherwise, use plain UTF-8 with single spaces: letters and digits, space, comma, full stop, hyphen, apostrophe, ampersand and parentheses. Accented letters are fine where the language needs them (`é`, `ñ`, `ü`, `å`, `ç`). Broken encoding is not (`Ã`, `Â`, `â€™`, `Ã©`).

Please do not use emoji or symbols (`★`, `✦`, `•`, `▶`, `→`, `≈`), decorative punctuation (`***`, `|`, `___`, `~~`, `!!!`, `>>`), or invisible characters, which means non-breaking space `U+00A0`, zero width space `U+200B`, soft hyphen `U+00AD`, tabs and line breaks. Use straight or curly quotes consistently, and keep one language per field with a language tag.

## The room description

The description should cover this room only, not the hotel, not the location, and not other rooms. Where digits are used, give an explicit unit, for example `32 sqm`.

Descriptive and marketing wording is welcome here, unlike in the room name. Put it after the facts rather than instead of them.

Keep one language per field, with a language tag, and avoid mixing languages within a field.
