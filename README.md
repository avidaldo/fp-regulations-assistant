# fp-regulations-assistant

Students and teachers in Galician vocational training (FP) keep asking questions whose answers are written in regulations: how many sittings (*convocatorias*) a module has, how to renounce one, whether work experience exempts a student from the company-training period, what title a course leads to, when a deadline falls. The answers are spread over Spanish state laws and royal decrees (published in the BOE), Galician decrees and orders (published in the DOG), and yearly instructions. Many of these rules amend or repeal earlier ones, so a correct answer must cite the version that is **in force**: the Spanish Organic Law 3/2022 on vocational training was amended in 2024; Royal Decree 659/2023, which develops it, changed the number of yearly sittings of a module in 2024; and the Galician Decree 20/2026 replaced the decree of 2010.

This repository builds, step by step, an assistant for a teacher who receives students' messages. It reads each message, finds the articles that may answer it, and drafts a reply citing them, which the teacher approves before anything is sent.

## The data

### The norms

**The two state norms** that every FP question starts from are served by the [BOE open-data API for consolidated legislation](https://www.boe.es/datosabiertos/api/api.php) ([documentation](https://www.boe.es/datosabiertos/documentos/APIconsolidada.pdf)). The API serves the consolidated text of a norm (the text as amended by later norms) split into blocks, with every wording each block has had, and the norm's metadata, including whether it has been repealed.

| Norm | BOE identifier |
| --- | --- |
| Ley Orgánica 3/2022, de 31 de marzo, de ordenación e integración de la Formación Profesional | `BOE-A-2022-5139` |
| Real Decreto 659/2023, de 18 de julio, por el que se desarrolla la ordenación del Sistema de Formación Profesional | `BOE-A-2023-16889` |

**The Galician norms and the decrees of the specialisation course in AI and Big Data** answer many questions too. None of them is in the consolidated API: each exists only as published, in its own format.

| Norm | Format |
| --- | --- |
| Real Decreto 279/2021, de 20 de abril, by which the specialisation course in Artificial Intelligence and Big Data is established | BOE original publication (XML) |
| Real Decreto 497/2024, de 21 de mayo, which amends RD 279/2021 and other course decrees | BOE original publication (XML) |
| Decreto 20/2026, do 9 de marzo, ordenación xeral da formación profesional do sistema educativo de Galicia | DOG (HTML page) |
| Resolución do 3 de xullo de 2026: instructions for the academic year 2026/27 | DOG (HTML page) |
| Resolución do 1 de xullo de 2025: instructions on graos D and E, extended to 2026/27 | DOG (HTML page) |
| Orde do 12 de xullo de 2011: development, assessment and accreditation in FP | DOG (HTML page) |
| Orden de 5 de noviembre de 2010: FP for adults in distance and blended modes | DOG (HTML page, in Spanish) |

The DOG publishes each norm as one web page and marks no structure in it, and neither the DOG nor the original BOE publication says which parts were later amended or repealed.

### The inbox

[`inbox/`](inbox/README.md) holds **360 synthetic messages** that students might send to a teacher, in Spanish and Galician, with a few in other languages: an identifier, when it arrived, a pseudonymous sender, the subject and the body. The messages are invented, so no personal data is involved. The file is an export from a mailbox, with the problems such exports have.

**120 messages are labelled** with the articles that answer them: the expected output for those messages. The labels cover only the two state norms: a question that only a Galician norm answers, or that no norm answers, has an empty list.

## Requirements

1. **Find the articles.** For each message, the assistant finds the articles that may answer it, from a table of articles with the metadata each one needs: the norm, the article, its title and text, its dates, and whether it is in force.
2. **Cite only what is in force.** A repealed or superseded article is never presented as current. Every statement about whether an article is in force shows where it comes from, and the date on which it was decided.
3. **A person decides.** Replies cite the articles they rely on. Nothing is sent without the teacher's approval, and a message the assistant can't handle goes to a person.
4. **Resist manipulation.** A message that tries to make the assistant do something other than answer (prompt injection) must not succeed, and the evaluation tests it on purpose.
5. **Measured, not assumed.** Every component that can be wrong is measured on labelled messages: how often its output is invalid, how often it finds the right articles, and its cost and time per message.
6. **Runs on its own.** The assistant processes messages as they arrive, and never answers the same message twice, even when a step fails and is retried or a message arrives twice.
7. **A case for the school.** What it would cost per message and per month, how fast and how reliable it is, which steps should be fixed code rather than a model and why, and what happens when it is wrong.

## Constraints

- **Local and hosted models.** Every component that uses a language model runs both with a model on the user's own machine and with a hosted one, and switching between them is a change of settings, not of code. A local model costs nothing per message and never depends on a provider's quota; a hosted one is usually faster and may answer better. Real students' messages may go only to a service the school has an agreement with; the inbox is synthetic, so any provider can be used here.
- **A mock model.** Everything also runs with a mock model that returns fixed answers, so that the tests, and anyone who runs the project offline, never need a real one.
