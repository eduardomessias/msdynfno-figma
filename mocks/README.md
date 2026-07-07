# Mocks

Worked examples of D365FO Forms built with the [authoring workflow](../workflow/00-authoring-guide.md). Each mock is a real (or realistic) Smartbox Form, complete with:

- A short narrative of **what** and **why**.
- A reference to the **catalog pattern** the mock applies.
- A reference (or link) to the **Figma frame** for the mock.
- A filled **Form Spec** (using [`templates/form-spec.md`](../templates/form-spec.md)).
- An **ASCII frame description** for offline reading (devs + consultants without Figma access).
- A **handoff status** snapshot.

> Mocks are organised under the L1 business process areas of Microsoft's [Business Process Catalog](https://github.com/microsoft/dynamics365patternspractices). Look first under the area your form belongs to; if a similar mock exists, copy it as a starting point.

---

## Mocks by process area

| Process area (L1)          | Mocks                                                                      |
|-----------------------------|----------------------------------------------------------------------------|
| Acquire to dispose         | _none yet_                                                                  |
| Administer to operate      | _none yet_                                                                  |
| Case to resolution         | _none yet_                                                                  |
| Concept to market          | _none yet_                                                                  |
| Design to retire           | _none yet_                                                                  |
| Forecast to plan           | _none yet_                                                                  |
| Hire to retire             | _none yet_                                                                  |
| Inventory to deliver       | _none yet_                                                                  |
| **Order to cash**          | [Sales order — Experience](./order-to-cash/sales-order-experience.md)       |
| Plan to produce            | _none yet_                                                                  |
| Project to profit          | _none yet_                                                                  |
| Prospect to quote          | _none yet_                                                                  |
| Record to report           | _none yet_                                                                  |
| Service to deliver         | _none yet_                                                                  |
| Source to pay              | _none yet_                                                                  |

---

## Authoring a new mock

1. Pick the process area (L1). If none fits, raise to the architect — don't invent a new area.
2. Copy an existing mock as a template, or copy [`templates/form-spec.md`](../templates/form-spec.md) and start fresh.
3. Pick a pattern from [`catalog/README.md`](../catalog/README.md) and link to its catalog page.
4. Build the Figma frame in the corresponding pattern page of the Figma library.
5. Fill out the spec end-to-end, then write the narrative + ASCII frame description in the mock file.
6. Mark the mock **Draft** → **Ready for dev** → **In build** → **Built** → **Verified** as it progresses.
7. Add it to the table above and to the process area's `README.md`.
