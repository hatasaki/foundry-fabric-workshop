# 開催準備 — 環境・アカウント・権限

[講師ガイド](README.md) · [English](../en/preparation.md)

## 1. 講師が用意するもの

| 事前に用意するもの | 数 | 設定 |
| --- | ---: | --- |
| 研修用 Azure リソースグループ | 1 | 研修専用のサブスクリプションまたはリソースグループを使い、予算と利用できるリージョンを確認します。 |
| Microsoft Entra 研修アカウント | 受講者ごとに1 | Fabric と Foundry が属する同じテナントのメンバー アカウント。共用アカウントは使いません。 |
| Microsoft Fabric 容量とワークスペース | 容量1以上、ワークスペースは受講者ごとに1 | 稼働中の有償 Fabric 容量（F2以上）。Spark、オントロジー エージェント、Graph を同時に利用できる規模を確保します。 |
| 親 Microsoft Foundry リソース | 1 | Project 管理を有効にし、システム割り当てマネージド ID を有効にします。応答モデルと埋め込みモデルを事前にデプロイします。 |
| 研修専用 Azure AI Search | 1 | **ロールベースのアクセス制御**、システム割り当てマネージド ID、セマンティック ランカーとエージェントによる取得を設定します。 |

**講師は、受講者の Foundry プロジェクト、Lakehouse、オントロジー、ナレッジソース、ナレッジベース、エージェントを作成しません。** Blob への文書の事前配置は不要です。作業データには同梱の架空データ（[日本語](../../student/jp/data/README.md)・[英語](../../student/en/data/README.md)）だけを使います。

### 研修アカウントを登録する

1. Microsoft Entra 管理センターで、**Entra ID → ユーザー → 新しいユーザー → 新しいユーザーの作成** を開き、受講者ごとのメンバー アカウントを作成します。講師用アカウントと分けます。研修用のセキュリティ グループを作る場合は、このクラスの受講者だけを入れます。
2. Fabric アイテムを作成・使用できるライセンスを割り当てます。初回サインインと、テナントの条件付きアクセスに従った MFA 登録ができることを確認します。初回資格情報は承認済みの経路で本人だけに渡します。名簿、UPN、実環境の ID、資格情報は GitHub に置きません。
3. 同じ親 Foundry リソースを使う本番とリハーサルで、受講者番号が重複しないように割り当てます。非公開名簿に、受講者番号・アカウントのオブジェクト ID・割り当てた Fabric ワークスペース・予定のプロジェクト名とオントロジー名を記録します。例：`03` → `workshop-student03` → `student03` → `student03_mfg_ontology`。受講者にテナント管理ロールや Azure サブスクリプションの管理権限を付与しません。

### Fabric 容量とワークスペースを用意する

1. Fabric 管理者は、研修リージョンで Lakehouse、Spark、Ontology（V2）、オントロジー エージェント、Graph を利用できることを確認します。[Fabric のリージョン](https://learn.microsoft.com/ja-jp/fabric/admin/region-availability)、[Copilot とエージェントのテナント設定](https://learn.microsoft.com/ja-jp/fabric/admin/service-admin-portal-copilot)を確認し、作成とAI機能を研修グループに許可します。リージョン外の処理を許可するかは、組織のポリシーに従って管理者が判断します。
2. **ワークスペース → 新しいワークスペース** で受講者ごとの空のワークスペースを作り、稼働中の研修容量へ割り当てます。
3. 各ワークスペースの **アクセスの管理** で、その受講者本人に **共同作成者** を割り当てます。他の受講者は追加しません。これは[Fabric のワークスペース ロール](https://learn.microsoft.com/ja-jp/fabric/fundamentals/roles-workspaces)であり、Azure の `Contributor` は付与しません。

### 親 Foundry リソースを用意する

1. Azure ポータルの **リソースの作成** から **Microsoft Foundry** を選び、研修用リソースグループに作成します。親は `Microsoft.CognitiveServices/accounts` の `AIServices` 型を使います。Project 管理（`allowProjectManagement`）とシステム割り当て ID が有効なことを確認します。
2. [Foundry のリージョンとクォータ](https://learn.microsoft.com/ja-jp/azure/foundry/agents/concepts/limits-quotas-regions)を確認します。既定ランタイムの Prompt Agent で使える応答モデル（例：`gpt-5.4`）と、検索文書の埋め込みモデル（例：`text-embedding-3-small`）を講師が配備します。例のデプロイ名は `workshop-chat` と `workshop-embedding` です。受講者には実際のデプロイ名を渡します。
3. 親リソース名と **Azure ポータルの親リソースの概要へのリンク** を配布します。[Lab 00](../../student/jp/lab-00-setup.ja.md) では、受講者がそのリンクから **リソース管理 → プロジェクト → 新規** を選び、自分の `studentN` を作成します。Foundry ポータルの既存プロジェクトへのリンクは配布しません。

### Azure AI Search を用意する

1. 同じ Entra テナントで Azure AI Search サービスを作成します。[リージョンでの機能提供状況](https://learn.microsoft.com/ja-jp/azure/search/search-region-support)と[ファイル ナレッジソースの要件](https://learn.microsoft.com/ja-jp/azure/search/agentic-knowledge-source-how-to-file)を確認します。File ソースにつき索引が1つ作られるため、受講者数と講師のテスト分を[索引数の上限](https://learn.microsoft.com/ja-jp/azure/search/search-limits-quotas-capacity#index-limits)に収めます。Free は最大3索引です。モデルの埋め込み処理は、8本×受講者数の同時登録を想定して容量を確保します。
2. Azure ポータルの Search リソースで、**設定 → キー** のデータ プレーン認証を **ロールベースのアクセス制御** にします。**API キー** や **両方** は選びません。**ID → システム割り当て → オン → 保存** で ID を有効にします。
3. セマンティック ランカーとエージェントによる取得を使用できることを確認します。ファイルは **最小** で抽出し、ナレッジベースは **最小** と **抽出データ** で構成します。ナレッジベース用に別の推論モデルは追加しません。
4. 研修端末と Foundry から Search に到達でき、Search から埋め込みデプロイを呼び出せることを確認します。組織のネットワーク設定は変更せず、許可された接続経路を使います。受講者は Foundry の一覧から既存の Search を選ぶため、配布するのはサービス名です。URL と API キーは不要です。

## 2. Azure RBAC を設定する

講師側で、リソースを作成できる担当者と、対象スコープで `Microsoft.Authorization/roleAssignments/write` を持つ権限担当者を決めます。受講者の割り当ては以下の**組み込みロールだけ**を使用します。サブスクリプションやリソースグループで `Owner`、`Contributor`、`User Access Administrator` を受講者に割り当てません。モデルを配備しない受講者にクォータ閲覧ロールも不要です。

| 誰に | 組み込みロール | スコープ | 目的と付与期間 |
| --- | --- | --- | --- |
| 受講者本人 | `Reader` | 研修用の親 Foundry リソース | 指定の親リソースと既存モデルを参照。親への変更権限は付けません。 |
| 受講者本人 | `Foundry Project Manager` | 研修用の親 Foundry リソース | **Lab 00 の子プロジェクト作成中のみ**。作成後は削除し、Lab 04・05 では付け直しません。 |
| 受講者本人 | `Foundry User` | 本人が作成した `studentN` プロジェクト | エージェントの作成・保存・プレイグラウンド テスト。プロジェクト作成後に付与し、研修終了まで保持します。 |
| 受講者本人 | `Foundry Project Manager` | 本人が作成した `studentN` プロジェクト | Lab 04 の開始前に付与。Lab 04 の Search 接続と、Lab 05 の Fabric IQ・Foundry IQ の接続を本人の子プロジェクトに作るために使います。両接続とエージェントの保存を確認したら削除します。 |
| 受講者本人 | `Search Service Contributor` | 研修専用 Search サービス | Lab 04 の開始前に付与。サービスを接続先一覧に表示するための `Microsoft.Search/searchServices/read` と、File ソース・ナレッジベースの作成に使用します。8本が **準備完了** になり、Lab 05 でナレッジベースをエージェントに接続して保存したら削除します。 |
| `studentN` のシステム割り当て ID | `Foundry User` | 研修用の親 Foundry リソース | プロジェクトから共用モデルを利用するための ID。親の割り当てが本人のプロジェクトに継承されます。 |
| `studentN` のシステム割り当て ID | `Search Index Data Reader` | 研修専用 Search サービス | Foundry IQ のナレッジベースからの取得専用。`studentN` 作成後に付与します。 |
| Search のシステム割り当て ID | `Cognitive Services OpenAI User` | 埋め込みモデルを配備した親 Foundry リソース | Search のファイル索引化と検索で埋め込みを生成。受講者やプロジェクトの ID と混同しません。 |

- `Foundry User` だけでは子プロジェクトを作成できません。`Foundry Project Manager` は旧表記 **Azure AI Project Manager**、`Foundry User` は **Azure AI User** の場合があります。[Foundry の組み込みロールとスコープ](https://learn.microsoft.com/ja-jp/azure/foundry/concepts/rbac-foundry)を参照してください。
- `Search Service Contributor` にサービスの参照権限が含まれるため、リソースの選択だけを目的に `Reader` を重ねたり、サブスクリプション全体の閲覧権限を付与したりしません。プロジェクト ID の `Search Index Data Reader` はエージェントの検索用であり、受講者本人の一覧表示権限ではありません。
- `Search Index Data Contributor` は、検索インデックスの文書を直接書き込むロールです。このラボの File ソースによる直接登録では常時付与しません。開催前の確認でインデックス文書への書き込みが別途必要と特定された場合だけ、対象の[生成済み索引](https://learn.microsoft.com/ja-jp/azure/search/search-security-rbac#grant-access-to-a-single-index)と登録担当者に限定し、登録後に外してください。`403` だけを理由に広い権限を追加しません。
- Foundry プレイグラウンド から Fabric IQ を照会する ID は **OAuth ID パススルー** の受講者本人です。プロジェクト ID や Search の ID に Fabric ワークスペースを共有しません。講師は個別の Entra 同意またはテナント ポリシーを確認します。
- 親スコープの `Foundry Project Manager` は子プロジェクトに継承されます。`Search Service Contributor` は共有 Search 上で他の索引の構成やサービス設定も管理できます。`studentN` という名前はアクセス境界になりません。共用構成は研修専用・架空データ専用とし、これらのロールを必要な作業中だけ付与します。受講者間の厳密な分離が必須なら、親 Foundry リソースと Search を受講者ごとに分けます。

### 割り当て操作

1. Azure ポータルで対象リソースを開き、**アクセス制御 (IAM) → 追加 → ロールの割り当ての追加** を選びます。スコープ欄が表のリソースを指していることを確認します。
2. ロールを選びます。ユーザーには **ユーザー、グループ、またはサービス プリンシパル**、サービスの ID には **マネージド ID** を選びます。プロジェクトと親リソースの ID 名が似ている場合は、表示名ではなくオブジェクト ID を照合してから **確認と割り当て** を選びます。
3. 受講者が Lab 00 を完了したら、本人の `studentN` と親リソースの組を確認します。プロジェクトのシステム割り当て ID を取得し、無効なら講師が有効にします。**アクセス制御 (IAM) → アクセスの確認** で、受講者とプロジェクト ID の両方を確認し、親の `Foundry Project Manager` を外します。子リソースの IAM が見つからない場合は[講師用の Azure CLI 手順](../shared/project-access.md)を使います。
4. Lab 01〜03 の間に表の残りのロールを割り当て、Lab 04 の開始前に、子プロジェクト、共通 Search、プロジェクトのマネージド ID に対する設定の反映を確認します。対象 Search が選択欄に出ないときは、受講者のテナント、対象サービスでの実効権限、反映状況を確認し、再サインイン後に一覧を確認します。
5. Lab 05 で Fabric IQ・Foundry IQ の接続とエージェントの保存を確認したら、受講者の子プロジェクトの `Foundry Project Manager` と研修 Search の `Search Service Contributor` を外します。プロジェクトのマネージド ID の取得権限は残します。この撤回は講師側で行い、受講者の個別テストの開始条件にしません。

## 3. 配布内容を用意する

| 配布項目 | 例または内容 |
| --- | --- |
| 自分の番号・プロジェクト名 | `03` → `student03` |
| 研修アカウントとサインイン方法 | 本人の UPN、MFA 手順。初回資格情報は個別に伝達。 |
| Fabric ワークスペース | 自分に割り当てた名前、または項目一覧へのリンク |
| Foundry の親リソース | リソース名と、Azure ポータルの親リソースの **概要** へのリンク |
| モデル | 応答と埋め込みのデプロイ名各1つ |
| 共通 Search | リソース選択欄に表示されるサービス名。Lab 04 で自動設定された接続名を控え、Lab 05 で使用します。 |
| 受講者教材 | [日本語のLab](../../student/jp/README.md)と[日本語データ](../../student/jp/data/README.md)、または [English labs](../../student/en/README.md) と[英語データ](../../student/en/data/README.md)。各言語にCSV 15本とMarkdown 8本を同梱。Lab 01 と Lab 04 は同じ言語で実施します。 |

[当日の進行・合格基準](README.md)
