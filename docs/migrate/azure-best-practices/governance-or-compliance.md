---
title: ガバナンスまたはコンプライアンスの戦略
description: ガバナンスまたはコンプライアンスの戦略
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: b07ded7b1d2ede7bc354709a798180daedfdae3b
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83216165"
---
# <a name="governance-or-compliance-strategy"></a>ガバナンスまたはコンプライアンスの戦略

移行作業全体を通してガバナンスまたはコンプライアンスが要求されるとき、追加の範囲が必要になります。 以下のガイダンスでは、[Azure への移行に関するガイド](../azure-migration-guide/index.md)の範囲を拡大し、ガバナンスまたはコンプライアンスの要件に対処するためのさまざまなアプローチに対応します。

## <a name="general-scope-expansion"></a>全般的な範囲の拡大

ガバナンスまたはコンプライアンスが要求される場合、前提条件となるアクティビティが最も影響を受けます。 評価、移行、および最適化の際に追加の調整が必要になる場合があります。

## <a name="suggested-prerequisites"></a>推奨される前提条件

ガバナンスまたはコンプライアンスの要件を統合するときに、基本の Azure 環境の構成が大幅に変わることがあります。 前提条件がどのように変わるかを理解するには、要件の特徴を理解することが重要です。 ガバナンスまたはコンプライアンスが要求される移行を開始する前に、アプローチを選択し、それをクラウド環境に実装する必要があります。 移行時に一般的に見られるアプローチは以下のように大別されます。

**一般的なガバナンスのアプローチ:** [クラウド導入フレームワーク ガバナンス モデル](../../govern/guides/index.md)は、ほとんどの組織にとって十分なアプローチであり、実用最小限の製品 (MVP) の実装と、それに続く、導入計画において特定された具体的なリスクに対処するための、ガバナンス成熟度のターゲットを定めた反復によって構成されます。 このアプローチでは、一貫したガバナンスを確立するために必要な最小限のツールが提供されます。そのため、チームはツールを理解できます。 それから、ガバナンスの一般的な懸念事項に対処するために、それらのツールに拡張が行われます。

**ISO 27001 コンプライアンスのブループリント:** ISO コンプライアンス標準に準拠することが必要なお客様の場合、[ISO 27001 共有サービスのブループリントのサンプル](https://docs.microsoft.com/azure/governance/blueprints/samples/iso27001-shared)を、反復プロセスの早い段階でより充実したガバナンス制約を作成するための、より効果的な MVP として使用できます。 [ISO 27001 App Service Environment/SQL Database のサンプル](https://docs.microsoft.com/azure/governance/blueprints/samples/iso27001-ase-sql-workload)を使用すると、ブループリントを拡張してコントロールをマップし、アプリケーション環境用の共通アーキテクチャをデプロイできます。 その他のコンプライアンスのブループリントがリリースされたら、ここで紹介します。

<!-- TODO: Refactor VDC content below. -->
<!-- docsTest:ignore "Azure Virtual Datacenter" -->

**Azure 仮想データセンター;** より堅牢なガバナンスの開始点が必要になることがあります。 そのような場合は、[Azure 仮想データセンター (VDC)](../../reference/vdc.md) を検討してください。 このアプローチは通常、エンタープライズ規模の導入を行う場合、特に資産の数が 10,000 個を超える作業で推奨されます。 また、次のいずれかが要求される複雑なガバナンスのシナリオの場合は、既定の選択となります: 広範なサードパーティのコンプライアンス要件、ドメインに関する深い専門知識、または成熟した IT ガバナンス ポリシーやコンプライアンス要件との整合性。

### <a name="partnership-option-to-complete-prerequisites"></a>前提条件を完了するためのパートナーシップ オプション

**Microsoft サービス:** Microsoft サービスでは、確実に最も適切なガバナンスやコンプライアンス モデルになるように、クラウド導入フレームワーク ガバナンス モデル、コンプライアンスのブループリント、仮想データセンターのオプションと整合したソリューション オファリングが提供されます。 [セキュア クラウド分析情報 (SCI)](https://download.microsoft.com/download/C/7/C/C7CEA89D-7BDB-4E08-B998-737C13107361/Secure_Cloud_Insights_Datasheet_EN_US.pdf) ソリューション オファリングを使用して、Azure 上のお客様のデプロイについてデータドリブンな全体像を把握し、既存のデプロイ アーキテクチャの最適化を確認しながらお客様の Azure 実装の成熟度を検証して、ガバナンスのセキュリティと可用性のリスクを削減します。 お客様の分析情報に基づいて、次のアプローチにより主導します。

- **クラウド基盤:** [ハイブリッド クラウド基盤 (HCF)](https://download.microsoft.com/download/D/8/7/D872DFD0-1C46-4145-95E4-B5EAB2958B96/Hybrid_Cloud_Foundation_Datasheet_EN_US.pdf) ソリューション オファリングを使用して、お客様の中核となる Azure の設計、パターン、ガバナンス アーキテクチャを確立します。 お客様の要件を最も適切な参照アーキテクチャにマップします。 共有サービスと IaaS ワークロードで構成される実用最小限の製品を実装します。
- **クラウド最新化:** [クラウド最新化](https://download.microsoft.com/download/3/7/3/373F90E3-8568-44F3-B096-CD9C1CD28AB7/Cloud_Modernization_Datasheet_EN_US.pdf)ソリューション オファリングを、アプリケーション、データ、およびインフラストラクチャをエンタープライズ対応のクラウドに移行するための包括的なアプローチとして使用し、さらにクラウド デプロイ後の最適化と最新化にも使用します。
- **クラウドによるイノベーション:** 革新的な独自の[クラウド センター オブ エクセレンス (CCoE)](https://download.microsoft.com/download/F/8/B/F8BBE4BD-E5F8-4DFB-82F7-C0A4E17051BB/Cloud_Center_of_Excellence_Datasheet_EN_US.pdf) ソリューション アプローチを通してお客様と協力します。このアプローチでは、コントロールを維持しながら DevOps による大規模な機敏性を実現するように最新の IT 組織を構築します。 ビジネス要件を捉え、セキュリティ、コンプライアンス、サービス管理ポリシーに整合したデプロイ パッケージを再利用し、運用手順に整合した Azure プラットフォームを維持するためのアジャイル アプローチを導入します。

## <a name="assess-process-changes"></a>プロセス変更の評価

評価時、要求されるガバナンス アプローチとの整合のために、追加の意思決定が必要です。 ワークロード評価の前に、クラウド ガバナンス チームからクラウド導入チームのすべてのメンバーに、ポリシー ステートメント、アーキテクチャのガイダンス、またはガバナンスやコンプライアンスの要件が提供される必要があります。

### <a name="suggested-action-during-the-assess-process"></a>評価プロセス中に推奨されるアクション

ガバナンスおよびコンプライアンスの評価の要件はお客様に固有であるため、評価時に行う実際の手順に関する一般的なガイダンスを提供することはできません。 しかし、"コンプライアンスやガバナンスの要件との整合" のためのタスクと時間の割り当てをプロセスに含める必要があります。 これらの要件の詳細については、次のリンクを参照してください。

ガバナンスについての理解を深めるには、「[クラウド ガバナンスの 5 つの規範](../../govern/governance-disciplines.md)」の概要をご覧ください。 クラウド導入フレームワークのこのセクションには、5 つのセクションのそれぞれについてポリシー、ガイダンス、要件を文書化するためのテンプレートが含まれています。

- [コスト管理規範](../../govern/cost-management/template.md)
- [セキュリティ ベースライン規範](../../govern/security-baseline/template.md)
- [リソースの整合性規範](../../govern/resource-consistency/template.md)
- [ID ベースライン規範](../../govern/identity-baseline/template.md)
- [デプロイ高速化規範](../../govern/deployment-acceleration/template.md)

クラウド導入フレームワーク ガバナンス モデルに基づくガバナンス ガイダンスの作成の詳細については、[クラウド ガバナンス戦略の実装](../../govern/corporate-policy.md)に関する記事を参照してください。

## <a name="optimize-and-promote-process-changes"></a>最適化および昇格プロセスの変更

最適化と昇格プロセス中に、クラウド ガバナンス チームは、ガバナンスとコンプライアンスの標準への準拠をテストおよび検証するために時間を費やす必要があります。 さらに、この手順は、クラウド ガバナンス チームがテンプレートをキュレーションするためのプロセスを挿入する良い機会です。テンプレートによって、将来のプロジェクトに追加の[デプロイの加速規範](../../govern/deployment-acceleration/index.md)が提供される可能性があります。

### <a name="suggested-action-during-the-optimize-and-promote-process"></a>最適化および昇格プロセス中に推奨されるアクション

このプロセス中は、実稼働への昇格に対して計画されているワークロードごとに、クラウド ガバナンス チームによるコンプライアンス レビューを実行するための時間割り当てをプロジェクト計画に含める必要があります。

## <a name="next-steps"></a>次のステップ

[移行のベスト プラクティスのチェックリスト](./index.md)の最後の項目として、チェックリストに戻り、移行作業について、追加の範囲の要件があれば再評価します。

> [!div class="nextstepaction"]
> [移行のベスト プラクティスのチェックリスト](./index.md)
