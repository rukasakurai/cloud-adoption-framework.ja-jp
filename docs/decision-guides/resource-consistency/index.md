---
title: リソースの整合性の意思決定ガイド
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure の移行を計画する際のリソースの整合性について説明します。
author: rotycenh
ms.author: v-tyhopk
ms.date: 02/11/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: decision-guide
ms.custom: governance
ms.openlocfilehash: 04d0a1e2ed63145baf94010fdf071a271461e7d0
ms.sourcegitcommit: 443c28f3afeedfbfe8b9980875a54afdbebd83a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71023795"
---
# <a name="resource-consistency-decision-guide"></a>リソースの整合性の意思決定ガイド

Azure の[サブスクリプション設計](../subscriptions/index.md)では、組織の構造、会計実務、ワークロードの要件に関連してクラウド資産をどのように編成するかを定義します。 このレベルの構造に加え、クラウド資産全体にわたって組織のガバナンス ポリシー要件に対応するために、サブスクリプション内のリソースを一貫して整理、デプロイ、管理する機能が必要です。

![複雑さが最小から最大までのリソースの整合性オプションを表わした図 (対応するジャンプ リンクを下に掲載)](../../_images/decision-guides/decision-guide-resource-consistency.png)

ジャンプ先:[基本的なグループ化](#basic-grouping) | [デプロイの整合性](#deployment-consistency) | [ポリシーの整合性](#policy-consistency) | [階層的な整合性](#hierarchical-consistency)  | [整合性の自動化](#automated-consistency)

クラウド資産のリソース整合性要件のレベルに関する意思決定を左右する主な要因: 移行後のデジタル資産のサイズ、既存のサブスクリプション設計方法にうまく適合しないビジネスまたは環境の要件、リソースがデプロイされた後に時間の経過と共にガバナンスを適用する必要性。

これらの要因の重要度が増すにつれて、クラウド ベースのリソースを一貫してデプロイ、グループ化、管理することの利点がより重要になります。 増加する要件を満たすために、より高度なレベルのリソース整合性を実現するには、自動化、ツールの使用、整合性の適用により多くの作業が必要になり、その結果、変更管理と進捗管理に費やす時間が増えます。

## <a name="basic-grouping"></a>基本的なグループ化

Azure では、[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)はサブスクリプション内でリソースを論理的にグループ化するリソース編成のコア メカニズムです。

リソース グループは、共通のライフ サイクル、またはポリシーやロールベースのアクセス制御 (RBAC) の要件などの共有管理の制約があるリソースのコンテナーとして機能します。 リソース グループに別のリソース グループを入れ子にすることはできません。また、リソースは 1 つのリソース グループにのみ属することができます。 リソース グループのすべてのリソースに対して特定のアクションを適用できます。 たとえば、リソース グループを削除すると、そのグループ内のすべてのリソースが削除されます。 リソース グループを作成するには一般的なパターンがあり、通常、2 つのカテゴリに分類されます。

- **従来型 IT ワークロード:** ほとんどの場合、同じライフサイクル内の項目 (アプリケーションなど) でグループ化されます。 アプリケーションでグループ化すると、個々のアプリケーションの管理が可能になります。
- **アジャイルの IT ワークロード:** 外部顧客向けのクラウド アプリケーションに焦点を当てます。 多くの場合、こうしたリソース グループは、デプロイの機能レイヤー (Web 層、アプリケーション層など) と管理の機能レイヤーを反映します。

## <a name="deployment-consistency"></a>デプロイの整合性

基本的なリソース グループ化メカニズムの上に構築されている Azure プラットフォームは、テンプレートを使用して、クラウド環境にリソースをデプロイするためのシステムを提供します。 ワークロードのデプロイ時、テンプレートを使用して一貫性のある組織と名前付け規則を作成し、リソースのデプロイと管理の設計におけるこれらの側面を強化できます。

[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)を使用すると、事前に定義された構成とリソース グループの構造を使用して一貫した状態でリソースを繰り返しデプロイすることができます。 Resource Manager テンプレートを使用して、デプロイの基礎として標準のセットを定義できます。

たとえば、サーバー間のトラフィックを分散するために、ロード バランサーと組み合わせた Web サーバーとして 2 つの仮想マシンを含む Web サーバー ワークロードをデプロイするための標準テンプレートを作成できます。 この種類のワークロードが必要な場合はいつでも、このテンプレートを再利用して、関連するデプロイ名と IP アドレスを変更するだけで構造的に同一な一連の仮想マシンとロード バランサーを作成できます。

プログラムでこれらのテンプレートをデプロイして、CI/CD システムと統合することもできます。

## <a name="policy-consistency"></a>ポリシーの整合性

リソースが作成されるときにガバナンス ポリシーが確実に適用されるようにするために、リソース グループ設計の一部には、リソースをデプロイするときに共通の構成を使用することが含まれます。

リソース グループと標準化された Resource Manager テンプレートを組み合わせることで、デプロイに必要な設定の標準と、リソース グループまたはリソースそれぞれに適用される [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) ルールの標準を適用できます。

たとえば、サブスクリプション内にデプロイされたすべての仮想マシンが、中央の IT チームによって管理されている共通のサブネットに接続するという要件があるとします。 ワークロード VM をデプロイするための標準的なテンプレートを作成できます。このテンプレートにより、ワークロードの別のリソース グループが作成され、必要な VM がそこにデプロイされます。 このリソース グループには、共有サブネットに参加するリソース グループ内のネットワーク インターフェイスのみを許可するポリシー ルールがあります。

クラウド デプロイ内でのポリシーの決定の適用に関する詳細については、[ポリシーの適用](../policy-enforcement/index.md)をご覧ください。

## <a name="hierarchical-consistency"></a>階層的な整合性

リソース グループを使用すると、サブスクリプション内の組織内で追加の階層レベルをサポートでき、リソース グループ レベルで Azure Policy の規則とアクセス制御を適用できます。 ただし、クラウド資産のサイズ拡大に合わせて、Azure Enterprise Agreement の企業、部門、アカウント、サブスクリプションの各階層を使用してサポートできる、さらに複雑なサブスクリプション間ガバナンス要件のサポートが必要となる場合があります。

[Azure 管理グループ](https://docs.microsoft.com/azure/governance/management-groups)を使用すると、サブスクリプションをエンタープライズ契約の階層と異なる階層にグループ化することで、サブスクリプションをより高度な組織構造に編成することができます。 この代替階層では、複数のサブスクリプションと各サブスクリプションに含まれるリソースにまたがって、アクセス制御とポリシー実施のメカニズムを適用できます。 管理グループ階層を使用して、クラウド資産のサブスクリプションを運用やビジネス ガバナンスの要件に一致させることができます。 詳細については、「[サブスクリプション決定ガイド](../subscriptions/index.md)」を参照してください。

## <a name="automated-consistency"></a>整合性の自動化

大規模なクラウド デプロイでは、グローバル ガバナンスがさらに重要で複雑になります。 リソースをデプロイするときにガバナンスの要件を自動的に適用し、強制することは、既存のデプロイの更新された要件を満たすことと同様に非常に重要です。

[Azure Blueprints](https://docs.microsoft.com/azure/governance/blueprints/overview) を使用すると、組織で Azure 内の大規模なクラウド資産のグローバル ガバナンスをサポートできます。 Blueprints は、標準の Azure Resource Manager テンプレートで提供される機能を超えて、リソースのデプロイとポリシー規則の適用が可能な完全なデプロイ オーケストレーションを作成します。 Blueprints では、バージョン管理、ブループリントが使用されていたすべてのサブスクリプションを更新する機能、およびデプロイされたサブスクリプションをロックしてリソースの不正な作成および変更を回避する機能がサポートされています。

これらの展開パッケージを使用すると、IT チームと開発チームが、変化する組織のポリシー要件に準拠する新しいワークロードとネットワーク資産を迅速にデプロイできます。 Blueprints は CI/CD パイプラインに統合して、デプロイの更新時に、改訂されたガバナンス標準をそれらのデプロイに適用することもできます。

## <a name="next-steps"></a>次の手順

リソースの整合性は、クラウド導入プロセスでのアーキテクチャに関する意思決定で要求されるコア インストラクチャ コンポーネントの 1 つにすぎません。 [意思決定ガイドの概要](../index.md)を参照して、他の種類のインフラストラクチャの設計に関する決定を行うときに使用される代替パターンまたはモデルを確認してください。

> [!div class="nextstepaction"]
> [アーキテクチャの意思決定ガイド](../index.md)