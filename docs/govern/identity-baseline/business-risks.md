---
title: ID ベースライン規範の導入理由とビジネス リスク
description: クラウド ガバナンス戦略におけるお客様の ID ベースライン規範の一般的な導入例を提示して説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 370dfa7a040a41e22f23741ca30cf3a305de7f29
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83219888"
---
# <a name="motivations-and-business-risks-in-the-identity-baseline-discipline"></a>ID ベースライン規範の導入理由とビジネス リスク

この記事では、クラウド ガバナンス戦略でお客様が ID ベースラインの規範を一般的に採用する理由について説明します。 ポリシー ステートメントを追いやるビジネス リスクの例もいくつか示します。

<!-- markdownlint-disable MD026 -->

## <a name="relevance"></a>関連性

従来のオンプレミス ディレクトリは、企業が社内ネットワークおよびデータ センター内のユーザー、グループ、ロールに対するアクセス許可とポリシーを厳密に制御できるように設計されています。 通常、これらのディレクトリでは、オンプレミス環境内でのみ適用可能なサービスを使用して、シングル テナントの実装をサポートします。

クラウド ID サービスによって、組織の認証とアクセスの制御の機能がインターネットに拡大されます。 これらはマルチテナントをサポートしており、クラウド アプリケーションとデプロイ全体でユーザーとアクセス ポリシーを管理するために使用できます。 パブリック クラウド プラットフォームは、管理タスクとデプロイ タスクをサポートするクラウド ネイティブの ID サービスを備えており、既存のオンプレミスの ID ソリューションを使用して、[統合のレベルを変える](../../decision-guides/identity/index.md)ことができます。 これらすべての機能によって、従来のオンプレミス ソリューションが必要とする以上の複雑なクラウド ID ポリシーになる可能性があります。

クラウド デプロイにとっての ID ベースラインの規範の重要性は、チームの規模と、クラウド ベースの ID ソリューションを既存のオンプレミスの ID サービスと統合する必要性によって決まります。 初期テスト デプロイでは、ユーザーの組織や管理はそれほど必要ありませんが、クラウド資産が成長するにつれ、より複雑な組織の統合と一元管理のサポートが必要になる可能性があります。

## <a name="business-risk"></a>ビジネス リスク

ID ベースラインの規範では、ID サービスとアクセス制御に関連するコア ビジネスのリスクへの対処を試みます。 ビジネスとの連携により、これらのリスクを特定し、クラウドのデプロイを計画して実装したら、妥当性について各リスクを監視します。

リスクは組織によって異なりますが、クラウド ガバナンス チーム内でのディスカッションの開始点として使用できる一般的な ID 関連のリスクとして以下を利用できます。

- **未承認のアクセス。** 承認されていないユーザーによってアクセスされる可能性がある機密データおよびリソースは、データ漏えいやサービスの中断につながり、組織のセキュリティ境界の侵害や、ビジネスを危険にさらしたり法的責任を追及される可能性があります。
- **複数の ID ソリューションによる非効率性。** 複数の ID サービス テナントがある組織では、ユーザーのために複数のアカウントを要求できます。 これは、複数の資格情報のセットを覚える必要があるユーザーや、複数のシステムでアカウントを管理する IT にとって非効率になる場合があります。 スタッフ、チーム、およびビジネスの目標が変更になったときに、ユーザー アクセス権の割り当てが ID ソリューション全体で更新されない場合、クラウド リソースが未承認のアクセスに対して脆弱になったり、ユーザーが必要なリソースにアクセスできなくなる場合があります。
- **外部のパートナーとリソースを共有できない。** 外部のビジネス パートナーを既存の ID ソリューションに追加できないことにより、効率的なリソースの共有とビジネス コミュニケーションが妨げられる場合があります。
- **オンプレミスの ID の依存関係。** 従来の認証メカニズムまたはサード パーティの多要素認証は、刷新するワークロードを移行するか、クラウドにデプロイする ID サービスを追加する必要があり、クラウドで使用できない場合があります。 どちらの要件も移行を遅延させたり妨げる可能性があり、コストが増加します。

## <a name="next-steps"></a>次のステップ

[ID ベースライン規範テンプレート](./template.md)を使用して、現在のクラウド導入計画によって生じる可能性が高いビジネス リスクを文書化します。

現実的なビジネス リスクについての理解が確実に得られたら、リスクに対する企業の許容範囲と、その許容範囲を監視するための指標と主なメトリックについて文書化することが、次の手順となります。

> [!div class="nextstepaction"]
> [指標、メトリック、リスクの許容範囲について理解する](./metrics-tolerance.md)
