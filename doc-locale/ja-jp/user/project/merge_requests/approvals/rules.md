---
stage: Create
group: Source Code
info: To determine the technical writer assigned to the Stage/Group associated with this page, see https://handbook.gitlab.com/handbook/product/ux/technical-writing/#assignments
description: Use approval rules to define the users or groups who should approve merge requests. Approvers can be optional or required.
title: マージリクエスト承認ルール
---

{{< details >}}

- プラン:Premium、Ultimate
- 提供:GitLab.com、GitLab Self-Managed、GitLab Dedicated

{{< /details >}}

承認ルールでは、マージリクエストをマージする前に必要な[承認](_index.md)の数と、承認を行うユーザーを定義します。これらを[コードオーナー](#code-owners-as-eligible-approvers)と組み合わせて使用することで、機能の保守を行うグループと、特定の監視領域を担当するすべてのグループによる変更のレビューを確実にできます。

承認ルールは、次のように定義できます。

- [プロジェクトのデフォルトとして](#add-an-approval-rule)。
- [マージリクエストごと](#edit-or-override-merge-request-approval-rules)。

承認ルールは、次のように Configure できます。

- [インスタンス全体に対して](../../../../administration/merge_requests_approvals.md)。

[デフォルトの承認ルール](#add-an-approval-rule)を定義しない場合、どのユーザーでもマージリクエストを承認できます。ルールを定義しない場合でも、プロジェクトの設定で[必要な承認者の最小数](settings.md)を適用できます。

フォークからアップストリームプロジェクトなど、別のプロジェクトを対象とするマージリクエストは、ソース（フォーク）ではなく、ターゲット（アップストリーム）プロジェクトからのデフォルトの承認ルールを使用します。

[ポリシー](../../../application_security/policies/_index.md)を使用して、すべてのプロジェクト（またはサブセット）に適用するようにマージリクエスト承認をグローバルに Configure できます。[マージリクエスト承認ポリシー](../../../application_security/policies/merge_request_approval_policies.md)では、よりきめ細かい設定オプションを使用して、柔軟性を高めることもできます。

## 承認ルールを追加

{{< history >}}

- すべての保護ブランチの承認ルールは、GitLab 15.3で導入されました。

{{< /history >}}

前提要件:

- プロジェクトのメンテナーロール以上を持っている必要があります。
- GitLab.comでグループを承認者として追加するには、そのグループのメンバーであるか、グループが公開されている必要があります。

マージリクエスト承認ルールを追加するには:

1. 左側のサイドバーで、**検索または移動**を選択して、プロジェクトを探します。
1. **設定 > マージリクエスト**を選択します。
1. **マージリクエストの承認**セクションの**承認ルール**セクションで、**承認ルールを追加**を選択します。
1. 右側のサイドバーで、次のフィールドに入力します:
   - **必要な承認**で、`0`の値は[ルールをオプション](#configure-optional-approval-rules)にし、`0`より大きい数値は必須ルールを作成します。必要な承認の最大数は`100`です。
   - **承認者を追加**から、[承認を受ける資格がある](#eligible-approvers)ユーザーまたはグループを選択します。GitLabは、マージリクエストによって変更されたファイルの以前の作成者に基づいて承認者を提案します。
1. **変更を保存**を選択します。[複数の承認ルール](#multiple-approval-rules)を追加できます。

承認ルールの上書きの設定によって、新しいルールが既存のマージリクエストに適用されるかどうかが決まります:

- [承認ルールの上書き](settings.md#prevent-editing-approval-rules-in-merge-requests)が許可されている場合、これらのデフォルトルールへの変更は、ルールの[ターゲットブランチ](#approvals-for-protected-branches)への変更を除き、既存のマージリクエストには適用されません。
- 承認ルールの上書きが許可されていない場合、デフォルトルールへのすべての変更が既存のマージリクエストに適用されます。承認ルールの上書きが許可されていた期間中に以前に手動で[上書き](#edit-or-override-merge-request-approval-rules)された承認ルールは、変更されません。

## 承認ルールを編集

前提要件:

- プロジェクトのメンテナーロール以上を持っている必要があります。
- GitLab.comでグループを承認者として追加するには、そのグループのメンバーであるか、グループが公開されている必要があります。

マージリクエスト承認ルールを編集するには:

1. 左側のサイドバーで、**検索または移動**を選択して、プロジェクトを探します。
1. **設定 > マージリクエスト**を選択します。
1. **マージリクエストの承認**セクションの**承認ルール**セクションで、編集するルールの横にある**編集**を選択します。
1. 右側のサイドバーで、フィールドを編集します:
   - **必要な承認**で、`0`の値は[ルールをオプション](#configure-optional-approval-rules)にし、`0`より大きい数値は必須ルールを作成します。必要な承認の最大数は`100`です。
   - ユーザーまたはグループを削除するには、削除するグループまたはユーザーを特定し、**削除**({{< icon name="remove" >}})を選択します。
1. **変更を保存**を選択します。

## 承認ルールを削除

前提要件:

- プロジェクトのメンテナーロール以上を持っている必要があります。

マージリクエスト承認ルールを削除するには:

1. 左側のサイドバーで、**検索または移動**を選択して、プロジェクトを探します。
1. **設定 > マージリクエスト**を選択します。
1. **マージリクエストの承認**セクションで、削除するルールの横にあるゴミ箱({{< icon name="remove" >}})を選択します。
1. **承認者を削除**を選択します。

## 複数の承認ルール

マージリクエストで複数の承認ルールを適用するには、プロジェクトに複数のデフォルト承認ルールを追加します。

[承認を受ける資格がある承認者](#eligible-approvers)がマージリクエストを承認すると、承認者が属するすべてのルールについて、残りの承認数（**承認**列）が減少します:

![マージリクエスト承認ウィジェット](img/mr_approvals_widget_v16_0.png)

<i class="fa fa-youtube-play youtube" aria-hidden="true"></i>概要については、[複数の承認者](https://www.youtube.com/watch?v=8JQJ5821FrA)を参照してください。

## 承認を受ける資格がある承認者

プロジェクトの承認者としての資格を得るには、ユーザーは次のいずれかのメンバーである必要があります:

- プロジェクト。
- プロジェクトの直属の親[グループ](#group-approvers)。
- プロジェクトと[共有](../../members/sharing_projects_groups.md)されているグループ。
- [承認者として追加されたグループ](#group-approvers)。

デベロッパーロールを持つユーザーは、次のいずれかに該当する場合、マージリクエストを承認できます:

- プロジェクトまたはマージリクエストレベルで承認者として追加されたユーザー。
- マージリクエストで変更されたファイルの[コードオーナー](#code-owners-as-eligible-approvers)であるユーザー。

レポーターロールを持つユーザーは、次の両方が該当する場合にのみ承認できます:

- ユーザーは、プロジェクトと[共有](../../members/sharing_projects_groups.md)されているグループの一員である必要があります。グループは、少なくともレポーターロールを持っている必要があります。
- グループは、マージリクエスト承認者として追加されている必要があります。

詳細な手順については、[マージリクエスト承認における職務分掌](#enable-approval-permissions-for-users-with-the-reporter-role)を参照してください。

マージリクエストのレビューに参加したユーザーを表示するために、マージリクエストの承認ウィジェットには、**コメント者**列が表示されます。この列には、マージリクエストにコメントした、承認を受ける資格がある承認者が一覧表示されます。これにより、作成者とレビュー担当者は、マージリクエストの内容に関する質問について誰に連絡すればよいかを特定できます。

必要な承認の数が割り当て済みの承認者の数よりも多い場合、プロジェクトで少なくともデベロッパーロールを持つ他のユーザーからの承認は、ユーザーが承認ルールに明示的にリストされていなくても、必要な承認数を満たすためにカウントされます。

### グループ承認者

ユーザーのグループを承認者として追加できます。このグループの**すべての直接のメンバー**は、ルールを承認できます。**継承されたメンバー**は、ルールを承認できません。

通常、グループは最上位のネームスペースのサブグループですが、外部グループとコラボレーションしている場合は除きます。別のグループとコラボレーションしている場合は、グループをグループ承認者として割り当てる前に、[プロジェクトへのアクセスを共有](../../members/sharing_projects_groups.md)する必要があります。

承認者グループにおけるユーザーのメンバーシップは、次の方法で個々の承認権限を決定します:

- 継承されたメンバーは、承認者とは見なされません。直接のメンバーのみがマージリクエストを承認できます。
- グループ承認者グループのユーザーが、後に個人承認者として_も_追加された場合、承認者は2人ではなく1人とカウントされます。
- マージリクエストの作成者は、デフォルトでは、自分のマージリクエストの承認を受ける資格がある承認者としてカウントされません。この動作を変更するには、[**作成者の承認を禁止**](settings.md#prevent-approval-by-author)プロジェクト設定を無効にします。
- マージリクエストへのコミッターは、マージリクエストを承認できます。この動作を変更するには、[**コミッターの承認を禁止**](settings.md#prevent-approvals-by-users-who-add-commits)プロジェクト設定を有効にします。

### 承認を受ける資格があるコードオーナー

[コードオーナー](../../codeowners/_index.md)をリポジトリに追加すると、ファイルのオーナーがプロジェクトで承認を受ける資格がある承認者になります。このマージリクエスト承認ルールを有効にするには:

1. 左側のサイドバーで、**検索または移動**を選択して、プロジェクトを探します。
1. **設定 > マージリクエスト**を選択します。
1. **マージリクエストの承認**セクションの**承認ルール**セクションで、**すべての対象ユーザー**ルールを見つけます。
1. **必要な承認**列に、必要な承認の数を入力します。

保護ブランチに対して[コードオーナーの承認を要求](../../repository/branches/protected.md#require-code-owner-approval-on-a-protected-branch)することもできます。

## レポーターロールを持つユーザーの承認権限を有効にする

レポーターロールを持つユーザーが保護ブランチにマージするには、マージリクエストを承認する権限を付与する必要がある場合があります。一部のユーザー（マネージャーなど）は、コードをプッシュまたはマージする権限を必要としない場合がありますが、提案された作業に対する監視は依然として必要です。

前提要件:

- この方法は**機能せず**、`All Branches`または`All protected branches`の設定で機能するため、特定のブランチを選択する必要があります。
- 追加されたユーザーがグループの一員であっても、共有グループは個々のユーザーではなく承認ルールに追加する必要があります。

プッシュアクセスを付与せずにこれらのユーザーの承認権限を有効にするには:

1. [保護ブランチを作成](../../repository/branches/protected.md)します
1. [新しいグループを作成](../../../group/_index.md#create-a-group)します。
1. [ユーザーをグループに追加](../../../group/_index.md#add-users-to-a-group)し、ユーザーのレポーターロールを選択します。[既知のイシュー](https://gitlab.com/gitlab-org/gitlab/-/issues/492467)のため、レポーターよりも高い権限を持つロールを割り当てないでください。より高いロールを割り当てると、予期しない動作が発生する可能性があります。
1. 少なくともレポーターロールで、[グループとプロジェクトを共有](../../members/sharing_projects_groups.md#invite-a-group-to-a-project)します。
1. 左側のサイドバーで、**検索または移動**を選択して、プロジェクトを探します。
1. **設定 > マージリクエスト**を選択します。
1. **マージリクエストの承認**セクションの**承認ルール**セクションで:
   - 新しいルールについては、**承認ルールを追加**を選択し、保護ブランチをターゲットにします。
   - 既存のルールについては、**編集**を選択し、保護ブランチをターゲットにします。
1. 右側のサイドバーの**承認者を追加**で、作成したグループを選択します。
1. **変更を保存**を選択します。

## マージリクエスト承認ルールを編集または上書きする

次のいずれかの方法で、プロジェクトのマージリクエスト承認ルールを上書きできます:

- 既存のマージリクエストを編集します。
- 新しいマージリクエストを作成します。

前提要件:

- 管理者アクセス権を持っているか、次のすべてに該当する必要があります:
  - 少なくともデベロッパーロールを持っているか、プロジェクトが外部メンバーからのコントリビュートを受け入れる必要があります。
  - マージリクエストの作成者である必要があります。
  - プロジェクト設定[承認ルールの編集を禁止](settings.md#prevent-editing-approval-rules-in-merge-requests)が無効になっています。

マージリクエストの承認者を上書きするには:

1. [新しいマージリクエストを作成](../creating_merge_requests.md)するときは、**承認ルール**セクションまでスクロールし、**マージリクエストを作成**を選択する前に、目的の承認ルールを追加または削除します。
1. 既存のマージリクエストを表示する場合:
   1. 左側のサイドバーで、**検索または移動**を選択して、プロジェクトを探します。
   1. **コード > マージリクエスト**を選択し、マージリクエストを見つけます。
   1. **編集**を選択します。
   1. **承認ルール**セクションまでスクロールします。
   1. 目的の承認ルールを追加または削除します。
   1. **変更を保存**を選択します。

管理者は、[マージリクエスト承認設定](settings.md#prevent-editing-approval-rules-in-merge-requests)を変更して、ユーザーがマージリクエストの承認ルールを上書きできないようにすることができます。

## ルールに必要な複数の承認を要求する

複数の承認を必要とする承認ルールを作成するには:

- [ルールを作成](#add-an-approval-rule)または[編集](#edit-an-approval-rule)するときは、**必要な承認**を`2`以上に設定します。

ルールに必要な複数の承認を要求するには、[APIを使用](../../../../api/merge_request_approvals.md#update-merge-request-rule)して、`approvals_required`属性を`2`以上に設定することもできます。

## オプションの承認ルールを Configure する

マージリクエストの承認は、承認は評価されるが必須ではないプロジェクトではオプションにできます。承認ルールをオプションにするには:

- [ルールを作成または編集する](#edit-an-approval-rule)ときは、**必要な承認**を`0`に設定します。

承認ルールをオプションにするには、[APIを使用](../../../../api/merge_request_approvals.md#update-merge-request-rule)して、`approvals_required`属性を`0`に設定することもできます。

## 保護ブランチの承認

{{< history >}}

- **すべての保護ブランチ**ターゲットブランチオプションは、GitLab 15.3で[導入](https://gitlab.com/gitlab-org/gitlab/-/issues/360930)されました。

{{< /history >}}

承認ルールは、[デフォルトブランチ](../../repository/branches/default.md)など、特定のブランチにのみ関連する場合がよくあります。特定のブランチの承認ルールを Configure するには:

1. [承認ルールを作成](#add-an-approval-rule)します。
1. プロジェクトに移動し、**設定 > マージリクエスト**を選択します。
1. **マージリクエストの承認**セクションで、**承認ルール**までスクロールします。
1. **ターゲットブランチ**の場合:
   - ルールをすべての保護ブランチに適用するには、**すべての保護ブランチ**を選択します。
   - ルールを特定のブランチに適用するには、リストからそれを選択します。
1. この設定を有効にするには、[保護ブランチでコードオーナーの承認を要求](../../repository/branches/protected.md#require-code-owner-approval-on-a-protected-branch)に従います。

## セキュリティ承認

{{< details >}}

- プラン:Ultimate
- 提供:GitLab.com、GitLab Self-Managed、GitLab Dedicated

{{< /details >}}

{{< history >}}

- セキュリティ承認がマージリクエスト承認設定に移動GitLab 15.0で[導入](https://gitlab.com/gitlab-org/gitlab/-/issues/357021)されました。
- 承認のボットコメントは、`security_policy_approval_notification`という名前の[フラグ付き](../../../../administration/feature_flags.md)のGitLab 16.2で[導入](https://gitlab.com/gitlab-org/gitlab/-/issues/411656)されました。デフォルトで有効。
- 承認のボットコメントは、GitLab 16.3で[一般提供](https://gitlab.com/gitlab-org/gitlab/-/merge_requests/130827)されました。機能フラグ`security_policy_approval_notification`が削除されました。

{{< /history >}}

[マージリクエスト承認ポリシー](../../../application_security/policies/merge_request_approval_policies.md#merge-request-approval-policy-editor)を使用すると、マージリクエストとデフォルトブランチの脆弱性の状態に基づいてセキュリティ承認を定義できます。各セキュリティポリシーの詳細は、マージリクエスト設定のセキュリティ承認セクションに表示されます。

セキュリティ承認ルールは、パイプラインが完了するまですべてのマージリクエストに適用されます。セキュリティ承認ルールの適用により、ユーザーはセキュリティスキャンが実行される前にコードをマージできなくなります。パイプラインが完了すると、セキュリティ承認ルールがチェックされ、セキュリティ承認がまだ必要かどうかが判断されます。パイプライン内のスキャナーがイシューを特定し、セキュリティ承認が必要な場合、マージリクエストにボットコメントが生成され、続行するために必要な手順が示されます。

![セキュリティ承認](img/security_approvals_v15_0.png)

これらのポリシーは、[セキュリティポリシーエディター](../../../application_security/policies/_index.md#policy-editor)で作成および編集されます。

## トラブルシューティング

### 承認ルール名は空白にできません

この検証エラーの回避策として、APIを使用して承認ルールを削除できます。

1. [プロジェクトのルールセットを取得](../../../../api/merge_request_approvals.md#get-all-approval-rules-for-project)します。
1. [ルールを削除](../../../../api/merge_request_approvals.md#delete-project-approval-rule)します。

この検証エラーの詳細については、[イシュー 285129](https://gitlab.com/gitlab-org/gitlab/-/issues/285129)をお読みください。

### グループは、プロジェクトに対する明示的または継承されたデベロッパーロールを必要とします

承認を処理するために作成されたグループは、レビューを必要とするプロジェクトとは異なるプロジェクト階層の領域に作成される場合があります。この場合、グループのメンバーは、アクセス権がないため、マージリクエストを承認する権限がない可能性があります。

次に例を示します:

以下のグループ構造では、プロジェクト1はサブグループ1に属し、サブグループ4にはユーザーがいます。

![シナリオの例 - プロジェクトとグループの階層](img/group_access_example_01_v16_8.png)

プロジェクト1は、プロジェクトの承認ルールを Configure しており、サブグループ4を承認者として割り当てています。マージリクエストが作成されると、サブグループ4からの承認者が、承認を受ける資格がある承認者リストに表示されます。ただし、サブグループ4のユーザーはマージリクエストを表示する権限がないため、`404`エラーが返されます。メンバーシップを付与するには、グループをプロジェクトメンバーとして招待する必要があります。これで、サブグループ4のユーザーが承認できるようになりました。

![プロジェクトメンバーページにサブグループ4がメンバーとして表示されています](img/group_access_example_02_v16_8.png)
