| Column name               | Description                                          | LIDO Source                                                                      | Transformation / Logic                                                                                                                         |
| ------------------------- | ---------------------------------------------------- | -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **RecID**                 | Local museum record ID.                              | `<lido:lidoRecID lido:type="local">`                                             | Raw text.                                                                                                                                      |
| **Link**                  | Direct MUIS link for the object.                     | Constructed                                                                      | `https://www.muis.ee/museaalview/{RecID}`                                                                                                      |
| **Muuseumikogu**          | Museum collection (e.g. *numismaatika*).             | `<classification type="muuseumikogu">/term`                                      | Raw text.                                                                                                                                      |
| **Number**                | Full inventory number.                               | `<workID type="museaali täisnumber">`                                            | Raw text.                                                                                                                                      |
| **Nimetus**               | Object title (e.g. *killing*, *mängumärk*).          | `<titleSet type="museaali nimetus">/appellationValue`                            | Raw text.                                                                                                                                      |
| **Olemus**                | “Nature” of the object (usually *münt*).             | `<objectWorkType type="olemus">/term`                                            | Raw text.                                                                                                                                      |
| **Mintimise_dateering**   | Minting date or date range.                          | `<event>` where eventType contains “valmistamine” or “<valmistamine/tekkimine>”  | Extract earliest & latest dates → format: `start–end`, `start`, or `""`.                                                                       |
| **Kogumise_dateering**    | Collecting / finding date.                           | `<event>` where eventType contains “kogumine/leidmine” or “<kogumistegevus>”     | Same formatting rules as minting.                                                                                                              |
| **Arheoloogia_dateering** | **Year of archaeological excavation**.               | `<event>` where eventType contains “arheoloogilised kaevamised” or “arheoloogia” | Extract first 4-digit year from earliest/latest date.                                                                                          |
| **Mintimise_kommentaar**  | Comment related *specifically* to the minting event. | `<eventDescriptionSet>` inside the minting event                                 | Only included when label is **“sündmuse kommentaar”** or **“sündmuses osalenud objekti kommentaar”**. All descriptiveNoteValue joined by `; `. |
| **Originaal_tüüp**        | Whether the object is *originaal* or *koopia*.       | Any `<descriptiveNoteValue>`                                                     | If contains “koopia” (case-insensitive), → `"koopia"` else `"originaal"`.                                                                      |
| **Kommentaar**            | Object-level comments (global).                      | `<objectDescriptionSet>` where label="teksti tüüp" == "kommentaar"               | All descriptiveNoteValue joined by `; `.                                                                                                       |
| **Tekst_objektil**        | Inscriptions or text physically on the object.       | `<objectDescriptionSet>` where label="teksti tüüp" == "tekst objektil"           | If location exists, format: `"text (location)"`. Joined by `; `.                                                                               |
| **Seisund**               | Condition of the object.                             | `<displayStateEditionWrap>/<displayState>`                                       | Raw text.                                                                                                                                      |
| **Tehnika**               | Manufacturing technique(s).                          | `<termMaterialsTech type="tehnika">`                                             | Skip hierarchical terms: keep only specific ones (usually 3rd+). Deduplicated, joined by `; `.                                                 |
| **Materjal**              | Material(s) of the object.                           | `<termMaterialsTech type="materjal">`                                            | Keep last (most specific) term from each block. Deduplicated, joined by `; `.                                                                  |
| **Mõõdud**                | Object measurements.                                 | `<measurementType>`, `<measurementValue>`, `<measurementUnit>`                   | If all present → `"Type Value Unit"`. Else use only the numeric value.                                                                         |

Filtering Rules Applied After Extraction

After DataFrame is built, the following rows are removed:

1. Unwanted Nimetus values

(Exact match after strip().casefold())

rinnaleht
mängumärk
mängu märk
medal
ripats
žetoon
zetoon
vallimärk
spordiklubi zetoon
hotelli raha

2. Unwanted materials
paber
papp
| **Riik**                  | Country associated with the event.                   | First `<place politicalEntity="riik">/appellationValue`                          | First non-empty value, ignoring `"[]"`.                                                                                                        |
