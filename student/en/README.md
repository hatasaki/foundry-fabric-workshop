# Student guide — Shipment decision for Hamamatsu Line 2

[Workshop home](../../README.md) · [Japanese](../jp/README.md)

At a fictional precision actuator manufacturer, vacuum brazing furnace `EQ-VBF-204` has an alert and magnet lot `LOT-MAG-088` is on quality hold. Find delivery risks that Sales missed in ERP. Explain who must approve the response, when the initial 8D quality reports are due, and which containment and substitution options to check. Use the **2026-05-14** snapshot.

The instructor provides a training account, an empty Fabric workspace, the parent Foundry resource, model deployments, and shared Azure AI Search. **Start at Lab 00 and complete the labs in order.** Create your `studentN` project in the Azure portal in Lab 00 and first open it in the Foundry portal in Lab 04. In Labs 01, 02, 04, and 05, create the items and connections specified in the steps. In Lab 03, update your Lab 02 ontology and configure its graph. In Lab 06, test the agent you created in Lab 05.

## Before you start

- Get your student number, training account, Fabric workspace, parent Foundry resource, model deployments, and shared Azure AI Search details from the instructor.
- Use **Code → Download ZIP** on GitHub or `git clone` to get a local copy of the [English data](data/README.md). In the `en` folder, use the 15 `.en.csv` files in Lab 01 and the eight `.en.md` files in Lab 04.
- Menu and button names are shown in English. In the new Foundry portal, **Tools**, **Knowledge**, and **Agents** are in the **Build** sidebar.

## Labs

| Lab | Exercise | Minutes |
| --- | --- | ---: |
| 00 | [Sign in and create your own project](lab-00-setup.en.md) | 10 |
| 01 | [Load data into a lakehouse](lab-01-data.en.md) | 20 |
| 02 | [Build the Fabric ontology](lab-02-ontology.en.md) | 25 |
| 03 | [Add business context and trace impact](lab-03-impact.en.md) | 30 |
| — | Break | 10 |
| 04 | [Upload files directly and configure Foundry IQ](lab-04-knowledge.en.md) | 25 |
| 05 | [Connect both evidence sources to the Foundry agent](lab-05-agent.en.md) | 30 |
| 06 | [Review the shipment decision](lab-06-review.en.md) | 30 |
| | **Standard** | **180** |

**Four-hour option:** Add the Lab 03 (15 min), Lab 05 (15 min), and Lab 06 (20 min) extensions and a second 10-minute break.

**Recovering from an error or interruption:** Refresh the item list in your workspace or project and check whether your last operation was saved. Continue with the next unfinished step. If a permissions error persists after you check the target and your sign-in, send the operation and error details to the instructor.

**Goal:** Verify retrieval by Fabric IQ and Foundry IQ in one Playground response. Explain which order changed the total and which policy changed the approval decision. Identify the record needed to calculate the deadline, then summarize recommendations and open questions in the decision memo.
