# Instructor guide — Fabric IQ × Foundry IQ × Foundry Agent

[Workshop home](../../README.md) · [日本語](../ja/README.md)

**180 minutes standard, 240 minutes with extensions.** Follow [workshop preparation](preparation.md) to provision the resources, student accounts, and built-in roles. Distribute the [student guide](../../student/en/README.md) separately.

## Preflight

Run the checks in order with two student-equivalent test accounts, empty workspaces, and project names not assigned to participants. Inspect inherited and group assignments in **IAM → Check access** to rule out elevated access.

1. **Lab 00:** Each account can open the assigned parent resource's Overview in the Azure portal and create a child project without entering an existing Foundry project.
2. **Labs 01–03:** Each account can create a lakehouse, notebook, and student-specific `studentN_mfg_ontology` in its empty Fabric workspace and apply the reviewed ontology draft.
3. **Lab 04:** Set the roles in the run of show below first. Verify that a student-equivalent account can open its project, pick the shared Search resource from the list, connect with the project managed identity, and upload eight files directly.
4. **Labs 05–06:** In two child projects under the same Foundry parent, verify that each student can connect their ontology from the **OneLake Catalog** under a distinct connection name without restoring the parent `Foundry Project Manager` role. Each endpoint points to the student's own item ID. Each student can configure and save the agent with the assigned model and their knowledge base. In preflight, also verify that the individual retrieval checks and integrated case work after removing the student's connection-creation roles. Check index counts, model quotas, embedding throughput, and role propagation against the class size.

If an operation fails under those roles, identify the caller, scope, and denied operation. Do not work around it by giving students `Owner` or resource group `Contributor`.

## Run of show

| When | Instructor action |
| --- | --- |
| Before Lab 00 | Grant temporary `Foundry Project Manager` on the training Foundry parent and distribute its Azure portal Overview link. Students create their own projects. |
| Immediately after Lab 00 and during Labs 01–03 | Match each `studentN` to its student and remove the temporary parent role. Give the student `Foundry User` and `Foundry Project Manager` on their child project and `Search Service Contributor` on training Search. Grant Foundry and Search access to the project identity and verify propagation before Lab 04. |
| Lab 04 | Students connect to Search and continue to Lab 05 after saving their knowledge base and seeing all eight files marked **Ready**. Help with permission errors or other issues students cannot resolve on their own. |
| Lab 05 | Do not grant additional access on the parent. Fabric IQ uses student OAuth, and Foundry IQ uses the project managed identity. Once you verify that both connections and the agent are saved, remove the student's child-project `Foundry Project Manager` and training Search `Search Service Contributor` roles. |
| Lab 05 individual tests and Lab 06 | Students start the individual tests as soon as the agent is saved. No notice about role removal is required. If the graph is still loading, record that separately and continue. |

If role propagation takes longer, delay Lab 04. Do not skip Lab 00 project creation or create projects on students' behalf.

## Pass criteria

| Check | Pass condition |
| --- | --- |
| Fabric ingestion and model | 15 tables; the ERP-only list shows two orders and JPY 80,500,000. Plan → Act saves 13 entities and 16 relationships. Separate `erp#Order` and `mes#Order` types are linked, and the unflagged risk rule is saved. |
| Fabric IQ retrieval | `SO-2026-004`, `SO-2026-009`, and `SO-2026-015`; three orders, JPY 99,700,000, and JPY 2,350,000/day in potential penalties. `SO-2026-015` is unflagged: ERP 2, linked `WO-2026-1019` at MES 30, and 15 good units out of 40 ordered. |
| Foundry IQ document retrieval | Each project has its own connection, File source with eight files, and knowledge base. The standalone document question succeeds with `knowledge_base_retrieve` and cites the policy and SLA. |
| Integrated decision | One **Response** contains successful `ask_ontology` and `knowledge_base_retrieve` calls, and the memo uses both outputs. Applying the JPY 90,000,000 threshold to JPY 99,700,000 in order value requires COO approval. Do not add the daily penalty to the order value. |
| Quality and reporting | Quarantine the affected work. Apply the above-175-ppm return threshold to the measured lot at 182 ppm and name qualified alternatives. Initial 8D: within 48 hours of detection for `CUS-001` and `CUS-002`; within 24 hours of the MES quality-hold entry for `CUS-003`. The standalone question must not give an exact deadline without that timestamp. |
| Graph (record separately) | Project 11 entity types and visualize the links. Record data loading and query execution separately. GQL is an extension. |

Use the [expected values](../resources/expected_oracle.json) only to grade; never paste them into the agent, chat, or knowledge source. View each tool's inputs and outputs under **Traces**. A tool list, retrieval from only one source, a SQL-only check, or an instructor demo does not pass integration.

## Handle issues and close the workshop

- **Duplicate Fabric IQ connection name:** Keep the existing ontology. Have the student open it in Fabric, select its title at the top, and change the **Name** field to `studentN_mfg_ontology`. Retry the Lab 05 connection with that name. Do not delete another project's connection or grant extra roles to work around the collision.
- `403`: Distinguish the user's, project's, and Search service's identities before checking roles. For an embedding `429`, wait for the interval shown and retry only failed files. Stagger uploads if necessary.
- If a Fabric creation dialog keeps loading, check the workspace list to see whether the item exists. Do not create duplicates.
- Remove training role assignments and clean up training accounts, projects, workspaces, Search sources, KBs, and indexes according to the approved retention period. Stop or delete capacity and model deployments only after confirming that they are dedicated to this workshop.
