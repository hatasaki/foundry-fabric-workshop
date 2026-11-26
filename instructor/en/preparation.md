# Prepare the workshop — Resources, accounts, and permissions

[Instructor guide](README.md) · [日本語](../ja/preparation.md)

## 1. Provision the training environment

| Instructor provisions | Count | Configuration |
| --- | ---: | --- |
| Azure training resource group | 1 | Use a dedicated training subscription or resource group. Check the budget and approved regions. |
| Microsoft Entra training account | One per student | A member account in the same tenant as Fabric and Foundry. Do not share student accounts. |
| Microsoft Fabric capacity and workspaces | At least one capacity; one workspace per student | Use active paid Fabric capacity (F2 or higher). Size it for concurrent Spark, Ontology Agent, and Graph work. |
| Parent Microsoft Foundry resource | 1 | Enable project management and its system-assigned managed identity. Deploy the chat and embedding models in advance. |
| Dedicated Azure AI Search service | 1 | Enable role-based access control, a system-assigned managed identity, semantic ranker, and agentic retrieval. |

**Do not pre-create student Foundry projects, lakehouses, ontologies, knowledge sources, knowledge bases, or agents.** Do not stage documents in Blob Storage. Use only the bundled fictional data ([English](../../student/en/data/README.md) or [Japanese](../../student/jp/data/README.md)).

### Create training accounts

1. In the Microsoft Entra admin center, go to **Entra ID → Users → New user → Create new user**. Create a member account for each student, separate from instructor accounts. If you use a training security group, include only this cohort.
2. Assign a license that allows the student to create and use Fabric items. Verify first sign-in and MFA enrollment under the tenant's Conditional Access policies. Deliver initial credentials privately through an approved channel. Keep the roster, UPNs, real resource IDs, and credentials out of GitHub.
3. Assign distinct student numbers to participants and rehearsal accounts using the same Foundry parent. Privately record each number, Entra user object ID, assigned Fabric workspace, and expected project and ontology names. Example: `03` → `workshop-student03` → `student03` → `student03_mfg_ontology`. Do not give students tenant or subscription administrator roles.

### Prepare Fabric capacity and workspaces

1. Have the Fabric administrator confirm that the training region supports Lakehouse, Spark, Ontology (V2), Ontology Agent, and Graph. Check [Fabric region availability](https://learn.microsoft.com/en-us/fabric/admin/region-availability) and [Copilot and agent tenant settings](https://learn.microsoft.com/en-us/fabric/admin/service-admin-portal-copilot). Enable item creation and required AI features for the training group. The administrator must follow organizational policy for any cross-region processing.
2. In **Workspaces → New workspace**, create an empty workspace per student and assign it to active training capacity.
3. In each workspace's **Manage access**, grant only the assigned student **Contributor**. This is a [Fabric workspace role](https://learn.microsoft.com/en-us/fabric/fundamentals/roles-workspaces), not the Azure `Contributor` role.

### Prepare the parent Foundry resource

1. In the Azure portal, select **Create a resource → Microsoft Foundry** and use the training resource group. The parent is a `Microsoft.CognitiveServices/accounts` resource of kind `AIServices`. Enable project management (`allowProjectManagement`) and its system-assigned identity.
2. Check [Foundry regions and quotas](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/limits-quotas-regions). Deploy a chat model supported by the default Prompt Agent runtime (for example, `gpt-5.4`) and an embedding model for the file source (for example, `text-embedding-3-small`). Example deployment names are `workshop-chat` and `workshop-embedding`. Tell students the actual deployment names.
3. Share the parent resource name and the **parent resource's Overview link in the Azure portal**. In [Lab 00](../../student/en/lab-00-setup.en.md), each student uses **Resource Management → Projects → New** from that link to create their own `studentN`. Do not distribute a link to an existing Foundry project.

### Prepare Azure AI Search

1. Create Azure AI Search in the same Entra tenant. Confirm local [feature availability](https://learn.microsoft.com/en-us/azure/search/search-region-support) and [file knowledge source requirements](https://learn.microsoft.com/en-us/azure/search/agentic-knowledge-source-how-to-file). A file source creates an index. Check the [index limit](https://learn.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits) for all students and instructor tests. Free supports at most three indexes. Size embedding throughput for eight files per student when uploads overlap.
2. On the Search resource in Azure portal, set **Settings → Keys** to **Role-based access control**. Do not choose **API Key** or **Both**. Open **Identity → System assigned → On → Save** to enable its identity.
3. Verify semantic ranker and agentic retrieval are available. Use **Minimal** for file extraction and **Minimal** with **Extractive data** for the knowledge base. Do not add a separate query reasoning model.
4. Verify both training devices and Foundry can reach Search, and Search can reach the embedding deployment. Use an approved network path without weakening the organization's controls. Give students the service name so they can select the existing Search resource in Foundry. They do not need the URL or an API key.

## 2. Assign Azure RBAC

Identify the resource provisioning operator and a role administrator with `Microsoft.Authorization/roleAssignments/write` at the required scopes. Use only the **built-in roles** below for students. Do not assign students `Owner`, `Contributor`, or `User Access Administrator` on the subscription or resource group. Students do not deploy models and do not need quota visibility.

| Principal | Built-in role | Scope | Purpose and duration |
| --- | --- | --- | --- |
| Student | `Reader` | Training parent Foundry resource | See the assigned parent and deployed models without changing the parent. |
| Student | `Foundry Project Manager` | Training parent Foundry resource | **Only during child project creation in Lab 00.** Remove it afterward and do not restore it for Labs 04 or 05. |
| Student | `Foundry User` | Their own `studentN` project | Build, save, and test the agent in Playground. Assign after project creation and retain through the lab. |
| Student | `Foundry Project Manager` | Their own `studentN` project | Grant before Lab 04. It lets students create the Search connection in Lab 04 and the Fabric IQ and Foundry IQ connections in Lab 05 in their own child project. Remove it once you verify that both tool connections and the agent are saved. |
| Student | `Search Service Contributor` | Dedicated training Search service | Grant before Lab 04. It provides `Microsoft.Search/searchServices/read` for the resource picker and lets students create the File source and knowledge base. Once all eight files are **Ready** and the student connects and saves their knowledge base on the agent in Lab 05, remove this role. |
| `studentN` system-assigned identity | `Foundry User` | Training parent Foundry resource | Let the project use shared model deployments. The parent assignment is inherited by the student's project. |
| `studentN` system-assigned identity | `Search Index Data Reader` | Dedicated training Search service | Read-only Foundry IQ knowledge base retrieval. Assign after `studentN` exists. |
| Search system-assigned identity | `Cognitive Services OpenAI User` | Parent Foundry resource hosting the embedding deployment | Generate embeddings for file ingestion and retrieval. This is neither the student identity nor the project identity. |

- `Foundry User` cannot create a child project. Older UI may say **Azure AI Project Manager** or **Azure AI User**. See [Foundry roles and scopes](https://learn.microsoft.com/en-us/azure/foundry/concepts/rbac-foundry).
- `Search Service Contributor` includes service discovery. Do not add a redundant `Reader` assignment or subscription-wide read access for the picker. The project's `Search Index Data Reader` role is for agent retrieval, not resource discovery by the student.
- `Search Index Data Contributor` writes documents directly to search indexes. Do not assign it by default for a managed File source upload. If your preflight identifies a separate index-document write requirement, limit it to the [generated index](https://learn.microsoft.com/en-us/azure/search/search-security-rbac#grant-access-to-a-single-index) and the caller that uploads, then remove it. Do not broaden permissions based on a `403` alone.
- Fabric IQ uses the student's identity via **OAuth Identity Passthrough**. Do not grant Fabric workspace access to the Foundry project or Search identity. Check the required Entra consent or tenant policy.
- Parent `Foundry Project Manager` is inherited by child projects. `Search Service Contributor` can manage other objects and service settings on shared Search. A `studentN` name is not an access boundary. Use dedicated, fictional training data and time-limit both roles. If strict student isolation is required, use a separate Foundry parent and Search service per student.

### Apply the assignments

1. In Azure portal, open the resource and select **Access control (IAM) → Add → Add role assignment**. Check the scope against the table.
2. Select the role. Use **User, group, or service principal** for people or groups, and **Managed identity** for managed identities. Match object IDs rather than display names before selecting **Review + assign**.
3. After a student completes Lab 00, match their `studentN` to the intended parent. Get its system-assigned identity; the instructor enables it if necessary. Verify both the student's and project identity's assignments in **IAM → Check access**, and remove the parent `Foundry Project Manager` assignment. If you cannot open a child project's IAM page, use the [instructor's Azure CLI procedure](../shared/project-access.md).
4. Assign the remaining roles from the table during Labs 01–03 and verify before Lab 04 that the child project, shared Search, and project managed identity assignments have propagated. If the Search service is not in the picker, verify the student's tenant, effective access on the service, and role propagation, then recheck the list after signing in again.
5. After verifying that the Fabric IQ and Foundry IQ connections and the agent are saved in Lab 05, remove the student's child-project `Foundry Project Manager` and training Search `Search Service Contributor` roles. Keep the project managed identity's retrieval permissions. The instructor handles this cleanup without making it a prerequisite for the students' individual tests.

## 3. Prepare student handout details

| Detail | Example or required value |
| --- | --- |
| Student number and project name | `03` → `student03` |
| Training account and sign-in | The student's UPN and MFA instructions. Send initial credentials privately. |
| Fabric workspace | Their assigned workspace name or link |
| Parent Foundry resource | Resource name and link to the parent's **Overview** in the Azure portal |
| Models | Names of the chat and embedding deployments |
| Shared Search | Service name as shown in the resource picker. In Lab 04, students note the automatically assigned connection name to use in Lab 05. |
| Participant materials | [English labs](../../student/en/README.md) with [English data](../../student/en/data/README.md), or [日本語のLab](../../student/jp/README.md) with [Japanese data](../../student/jp/data/README.md). Each edition includes 15 CSVs and eight Markdown files. Use the same language for Labs 01 and 04. |

[Run the workshop and grade the results](README.md)
