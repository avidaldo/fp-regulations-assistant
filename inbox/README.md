# The inbox

`inbox.jsonl` holds 360 synthetic messages that students of vocational training (FP) in Galicia might send to a teacher about the regulations, in Spanish and Galician, with a few in other languages. They were invented for this repository: no message comes from a real person, and the names and contact details in them are made up.

The file imitates an export from a real mailbox, problems included.

## Fields

One JSON object per line:

| Field | Content |
| --- | --- |
| `id` | the message identifier, `m0001` to `m0360` |
| `received_at` | when it arrived |
| `sender` | a pseudonymous identifier of the student, `s001` to `s140` |
| `subject` | the subject line, possibly empty |
| `body` | the text of the message |
| `expected_articles` | for labelled messages: the articles that answer the message; otherwise `null` |

## Expected output

120 messages are labelled with the articles that answer them: what the assistant should find for each. `expected_articles` covers only the two state norms of the first version of the corpus, Ley Orgánica 3/2022 and Real Decreto 659/2023: a question answered only by a Galician norm, or by no norm at all, has an empty list.

An article is identified by the norm's BOE identifier and the identifier the BOE consolidated-legislation API gives the block: `BOE-A-2023-16889/a1-37` is article 125 of Real Decreto 659/2023. Block identifiers are not article numbers.
