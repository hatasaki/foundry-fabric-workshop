# Assign roles after project creation / プロジェクト作成後のロール割り当て

[日本語の準備手順](../ja/preparation.md) · [Preparation in English](../en/preparation.md)

講師用。受講者が `studentN` を作成した後に実行します。受講者のオブジェクト ID は非公開の名簿から取得します。 / For instructors. Run after the student creates `studentN`. Get the student's object ID from the private training roster.

この手順は、取得テストまで残すロールを設定します。Lab 04・05 用の一時ロールは準備手順の表に従い、Lab 04 の開始前に付与します。 / This procedure assigns the roles retained for retrieval testing. Use the preparation table to assign the temporary roles for Labs 04 and 05 before Lab 04 begins.

## Identify the scopes and principal / 対象スコープと ID を確認

Sign in to Azure CLI as the authorized instructor and replace the placeholders. / 権限を持つ講師として Azure CLI にサインインし、プレースホルダーを置換します。

```bash
SUBSCRIPTION_ID='<training-subscription-id>'
TRAINING_RG='<training-resource-group>'
FOUNDRY_NAME='<parent-foundry-resource>'
SEARCH_NAME='<training-search-service>'
PROJECT_NAME='student03'
STUDENT_OBJECT_ID='<student-entra-user-object-id>'

az account set --subscription "$SUBSCRIPTION_ID"

FOUNDRY_SCOPE="$(az cognitiveservices account show \
  --resource-group "$TRAINING_RG" --name "$FOUNDRY_NAME" --query id -o tsv)"
PROJECT_SCOPE="$(az cognitiveservices account project show \
  --resource-group "$TRAINING_RG" --name "$FOUNDRY_NAME" \
  --project-name "$PROJECT_NAME" --query id -o tsv)"
PROJECT_PRINCIPAL_ID="$(az cognitiveservices account project show \
  --resource-group "$TRAINING_RG" --name "$FOUNDRY_NAME" \
  --project-name "$PROJECT_NAME" --query identity.principalId -o tsv)"
SEARCH_SCOPE="$(az search service show \
  --resource-group "$TRAINING_RG" --name "$SEARCH_NAME" --query id -o tsv)"
```

`PROJECT_SCOPE` が `/accounts/<parent-foundry-resource>/projects/student03` で終わり、`PROJECT_PRINCIPAL_ID` が空でないことを確認します。ID がなければ先に[プロジェクトのマネージド ID](https://learn.microsoft.com/ja-jp/azure/foundry/agents/how-to/foundry-iq-connect#authentication-and-permissions)を有効にします。 / Confirm `PROJECT_SCOPE` ends with `/accounts/<parent-foundry-resource>/projects/student03` and `PROJECT_PRINCIPAL_ID` is nonempty. If no principal exists, enable the [project managed identity](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/foundry-iq-connect#authentication-and-permissions) first.

## Assign the three post-creation roles / 作成後に3つのロールを割り当て

前のブロックと同じシェルで実行します。既存の割り当ては追加不要です。 / Use the same shell as the previous block. Skip any existing assignments.

```bash
if [ -z "$STUDENT_OBJECT_ID" ] || [ -z "$PROJECT_PRINCIPAL_ID" ] ||
   [ -z "$FOUNDRY_SCOPE" ] || [ -z "$SEARCH_SCOPE" ] ||
   [ "$PROJECT_SCOPE" != "$FOUNDRY_SCOPE/projects/$PROJECT_NAME" ]; then
  printf '%s\n' 'Check the student, project identity, and target scopes before assigning roles.' >&2
else
  az role assignment create --assignee-object-id "$STUDENT_OBJECT_ID" \
    --assignee-principal-type User --role "Foundry User" --scope "$PROJECT_SCOPE"

  az role assignment create --assignee-object-id "$PROJECT_PRINCIPAL_ID" \
    --assignee-principal-type ServicePrincipal --role "Foundry User" --scope "$FOUNDRY_SCOPE"

  az role assignment create --assignee-object-id "$PROJECT_PRINCIPAL_ID" \
    --assignee-principal-type ServicePrincipal --role "Search Index Data Reader" --scope "$SEARCH_SCOPE"
fi
```

付与後、Azure ポータルの **アクセス制御 (IAM) → アクセスの確認** で本人とプロジェクト ID を確認し、Lab 00 用に設定した親の一時 `Foundry Project Manager` の割り当てだけを外します。

Verify the student and project identity in **Access control (IAM) → Check access**. Remove only the temporary parent `Foundry Project Manager` assignment from Lab 00.
