---
title: リソースの名前付けとタグ付けの意思決定ガイド
description: Azure のクラウド導入フレームワークの一部として、クラウドベースのリソースを整理する際の名前付けとタグ付けの方法とオプションについて説明します。
author: alexbuckgit
ms.author: abuck
ms.date: 02/11/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: decision-guide
ms.custom: governance
ms.openlocfilehash: 2865f9a7ceb6d20828e76ad4da3a7ae5ebd81ba3
ms.sourcegitcommit: 9a84c2dfa4c3859fd7d5b1e06bbb8549ff6967fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83753491"
---
<!-- cSpell:ignore catalogsearch northamerica jsmith contactalias catsearchowners businessprocess businessimpact revenueimpact -->

# <a name="resource-naming-and-tagging-decision-guide"></a>リソースの名前付けとタグ付けの意思決定ガイド

デプロイが単純なものでない限り、クラウドベースのリソースの整理は IT 部門にとって重要なタスクです。 次の目的のために、名前付けとタグ付けの標準を使用してリソースを整理します。

- **リソース管理:** IT チームは、特定のワークロード、環境、所有権グループ、またはその他の重要な情報に関連付けられているリソースをすばやく見つける必要があります。 リソースの整理は、リソース管理に組織のロールとアクセス許可を割り当てるために不可欠です。
- **コスト管理と最適化:** ビジネス グループにクラウド リソースの消費量を認識してもらうために、IT 部門は、各チームが使用しているリソースとワークロードを把握する必要があります。 次のトピックがコスト関連のタグでサポートされています。

  - [クラウド会計モデル](../../strategy/cloud-accounting.md)
  - [ROI の計算](../../strategy/financial-models.md#return-on-investment)
  - [コスト管理](../../ready/azure-best-practices/track-costs.md)
  - [予算](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-acm-create-budgets?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)
  - [警告](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending?toc=/azure/cloud-adoption-framework/toc.json&bc=/azure/cloud-adoption-framework/_bread/toc.json)
  - [定期的な支出の追跡とレポート](../../govern/cost-management/compliance-processes.md)
  - [実装後の最適化](../../govern/cost-management/discipline-improvement.md#operate-and-post-implementation)
  - [コスト最適化戦術](../../govern/guides/complex/cost-management-improvement.md#incremental-improvement-of-the-best-practices)
- **運用管理:** 運用管理チームにとって、ビジネス コミットメントや SLA に対する可視性は、継続的な運用を行ううえで重要な側面です。 適切に管理するためには、[ミッション クリティカル度](../../manage/considerations/criticality.md)のタグ付けが必要です。
- **セキュリティ:** データとセキュリティへの影響を分類することは、違反や他のセキュリティの問題が発生したときに、チームにとって重要なデータ ポイントです。 安全に運用するには、[データ分類](../../govern/policy-compliance/data-classification.md)のタグ付けが必要です。
- **ガバナンスと規制に対するコンプライアンス:** リソース間の一貫性を維持することで、取り決められたポリシーとのずれを特定するのに役立ちます。 [このガバナンスの基礎に関する記事](../../govern/guides/complex/prescriptive-guidance.md#resource-tagging)では、ガバナンス プラクティスを展開するときに以下のいずれかのパターンがどのように役立つかを示します。 タグを使用して規制コンプライアンスを評価するのに類似のパターンを利用できます。
- **自動化:** 適切な方法で編成すると、IT 部門によるリソースの管理が簡単になるだけでなく、リソースの作成、運用の監視、および DevOps プロセスの作成の一部としてオートメーションを利用できるようになります。
- **ワークロードの最適化:** タグ付けは、パターンを特定し、広範な問題を解決するのに役立ちます。 また、タグは 1 つのワークロードをサポートするのに必要な資産を特定するのにも役立ちます。 各ワークロードに関連付けられているすべての資産にタグを付けることで、ミッション クリティカルなワークロードをより詳しく分析して、適切なアーキテクチャを決めることができます。

## <a name="tagging-decision-guide"></a>タグ付け決定ガイド

![タグ付けのオプションを最も簡単なものから最も複雑なものまでプロットし、その下に整合するジャンプ リンクを示す](../../_images/decision-guides/decision-guide-resource-tagging.png)

ジャンプ先:[ベースラインの名前付け規則](#baseline-naming-conventions) | [リソースのタグ付けパターン](#resource-tagging-patterns) | [詳細情報](#learn-more)

タグ付けアプローチは、クラウド ワークロードを管理する IT チームのサポートから、ビジネス全体のすべての側面に関連する情報の統合までの範囲の何に注目するかによって、簡単にも複雑にもなります。

ワークロード、アプリケーション、機能、または環境に基づくタグ付けのように、IT に合わせることに注目したタグ付けでは、資産を監視する複雑さは軽減され、機能と分類に基づく管理の決定が簡単になります。

会計、企業形態、ビジネス上の重要度など、ビジネスに合わせることに注目したタグ付けスキームでは、ビジネスの関心が反映されたタグ付け標準を作成し、そのような標準を長期間維持するために、より多くの時間をかけることが必要になる場合があります。 この投資によって、ビジネス全体に対する IT 資産のコストと価値の会計を向上させることができるタグ付けシステムが実現します。 資産の事業価値とその運用コストのこのような関連付けは、より幅広い組織内での IT のコスト センターとしての認識を変える最初のステップの 1 つです。

## <a name="baseline-naming-conventions"></a>ベースラインの名前付け規則

標準化された名前付け規則は、クラウドでホストされるリソースを編成するための開始点です。 適切な構造の名前付けシステムを作成すると、管理とアカウンティング両方の目的でリソースをすばやく識別できるようになります。 組織の他の部分に IT 名前付け規則が既にある場合は、クラウドの名前付け規則をそれらに揃えるかどうか、またはクラウドベースの標準を別に確立する必要があるかを検討します。

> [!NOTE]
> [名前付け規則と制限事項](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)は Azure リソースごとに変わります。 名前付け規則は、これらの規則に従っている必要があります。

## <a name="resource-tagging-patterns"></a>リソース タグ付けのパターン

一貫した名前付け規則だけで提供できるものより高度な組織のために、クラウド プラットフォームではリソースのタグ付けの機能がサポートされています。

タグは、リソースにアタッチされているメタデータ要素です。 タグは、キー/値の文字列のペアで構成されます。 これらのペアに含める値はユーザー次第ですが、包括的な名前付けとタグ付けのポリシーの一部として、一貫性のあるグローバルなタグのセットを適用することは、全体的なガバナンス ポリシーの重要な部分です。

計画プロセスの一環として、次の質問を使用して、リソース タグでサポートする必要がある情報の種類を判断してください。

- 名前付けポリシーとタグ付けポリシーを、社内の既存の名前付けポリシーや組織的ポリシーと統合する必要はありますか。
- チャージバックまたはショウバックのアカウンティング システムを実装しますか。 部署、ビジネス グループ、およびチームの会計情報に対して、単純なサブスクリプション レベルの内訳よりも細かくリソースを関連付ける必要がありますか。
- リソースの規制準拠要件のような詳細情報をタグ付けで表す必要がありますか 稼働時間の要件、パッチのスケジュール、セキュリティ要件など、運用に関する詳細はどうですか
- すべてのリソースに対して、一元化された IT ポリシーに基づくどのようなタグが必要になりますか。 どのようなタグを省略できますか。 個々のチームでの独自のカスタム タグ付けスキームの実装を許可しますか。

次に示す一般的なタグ付けパターンは、タグ付けを使用してクラウド資産を整理する方法の例を示しています。 これらのパターンは、1 つだけ使用する必要はなく、いくつかを同時に使用でき、会社のニーズに基づいて複数の方法で資産を整理できます。

<!-- markdownlint-disable MD033 -->
<!-- docsTest:disable -->

| タグの種類 | 例 | 説明 |
|-----|-----|-----|
| 機能 | app&nbsp;=&nbsp;catalogsearch1 <br> tier&nbsp;=&nbsp;web <br> webserver&nbsp;=&nbsp;apache <br> env&nbsp;=&nbsp;prod <br> env&nbsp;=&nbsp;staging <br> env&nbsp;=&nbsp;dev | ワークロード内での目的との関連、デプロイされている環境、または他の機能および運用に関する詳細で、リソースを分類します。 |
| 分類 | confidentiality&nbsp;=&nbsp;private <br> SLA&nbsp;=&nbsp;24hours | 使用方法および適用されるポリシーによって、リソースを分類します。 |
| 会計 | department&nbsp;=&nbsp;finance <br> program&nbsp;=&nbsp;business-initiative <br> region&nbsp;=&nbsp;northamerica | 課金を目的として、組織内の特定のグループにリソースを関連付けることができます。 |
| パートナーシップ | owner&nbsp;=&nbsp;jsmith <br> contactalias&nbsp;=&nbsp;catsearchowners <br> stakeholders&nbsp;=&nbsp;user1;user2;user3 | リソースに関連のある、またはそれ以外でリソースによって影響を受けるユーザー (IT 外) に関する情報を提供します。 |
| 目的 | businessprocess&nbsp;=&nbsp;support <br> businessimpact&nbsp;=&nbsp;moderate <br> revenueimpact&nbsp;=&nbsp;high | 投資に関する決定のサポートを向上させるため、リソースをビジネス機能と一致させます。 |

<!-- docsTest:enable -->
<!-- markdownlint-enable MD033 -->

## <a name="learn-more"></a>詳細情報

Azure での名前付けとタグ付けに関する詳細については、以下を参照してください。

- [Azure リソースの名前付け規則](../../ready/azure-best-practices/naming-and-tagging.md)。 Azure リソースの推奨される名前付け規則については、次のガイダンスを参照してください。
- [タグを使用して Azure リソースと整理階層を整理する](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources)。 Azure ではリソース グループと個々のリソースの両方のレベルでタグを適用でき、適用されたタグに基づいて柔軟な詳細さでアカウンティング レポートを作成できます。

## <a name="next-steps"></a>次のステップ

リソースのタグ付けは、クラウド導入プロセスでのアーキテクチャに関する意思決定で要求されるコア インストラクチャ コンポーネントの 1 つにすぎません。 [意思決定ガイドの概要](../index.md)を参照して、他の種類のインフラストラクチャの設計に関する決定を行うときに使用される代替パターンまたはモデルを確認してください。

> [!div class="nextstepaction"]
> [アーキテクチャの意思決定ガイド](../index.md)
