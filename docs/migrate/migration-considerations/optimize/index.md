---
title: 移行されたワークロードの最適化
description: Azure のクラウド導入フレームワークを使用して、移行したワークロードと資産を運用環境に昇格させる準備をします。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 1e101a75d3b13cc8cbcb6512d6a0a8b29674d5aa
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83214210"
---
# <a name="release-workloads"></a>ワークロードのリリース

ワークロードのコレクションとそれをサポートしている資産をクラウドに移行した後は、リリースする前に準備を行う必要があります。 移行作業のこのフェーズでは、ワークロードのコレクションに対して負荷テストとビジネス テストを行います。 その後、それらを最適化して文書化します。 ビジネス チームと IT チームがワークロードのデプロイを確認してサイン オフすると、それらのワークロードをリリース、つまり継続的な運用のためにガバナンス チーム、セキュリティ チーム、運用チームに渡すことができます。

"ワークロードのリリース" の目標は、移行されたワークロードを運用環境での使用に昇格させるための準備を行うことです。

## <a name="definition-of-_done_"></a>"_完了_" の定義

最適化プロセスは、ワークロードが正常に構成され、サイズ設定され、本番環境にデプロイされたときに完了します。

## <a name="accountability-during-optimization"></a>最適化中の説明責任

最適化プロセス全体の説明責任は、クラウド導入チームにあります。 ただし、クラウド戦略チーム、クラウド運用チーム、およびクラウド ガバナンスチームも、このプロセス中に実行されるアクティビティに対して責任を負います。

## <a name="responsibilities-during-optimization"></a>最適化中の責任

概要レベルの説明責任に加え、個人またはグループが直接責任を負う必要があるアクションがあります。 責任者の割り当てを必要とするいくつかのアクティビティを次に示します。

- **ビジネス テスト。** ワークロードのクラウドへの移行完了を妨げている互換性の問題を解決します。
  - 移行されたワークロードのテストには、社内のパワー ユーザーが深く関与する必要があります。 試行された最適化の程度に応じて、複数のテスト サイクルが必要になる場合があります。
- **ビジネス変更プラン。** 移行作業の結果として発生する、ユーザーの適応、ビジネス プロセスの変更、およびビジネス KPI またはラーニング メトリックを開発します。
- **ベンチマークと最適化。** パフォーマンスのベンチマークを設定するためのビジネス テストと自動化テストを調査します。 利用状況に基づいて、クラウド導入チームが、本番環境で期待される要件に対するコストとパフォーマンスのバランスを取るように、デプロイされる資産のサイズを調整します。
- **本格運用するための準備。** ワークロードの本番環境での使用をサポートするように、ワークロードと環境を準備します。
- **昇格する。** 本番環境のトラフィックを、移行されて最適化されたワークロードにリダイレクトします。 このアクティビティは、リリース サイクルの完了を表します。

コア アクティビティに加え、特定の割り当てと実行プランを必要とする、並行して実行されるいくつかのアクティビティがあります。

- **使用停止。** 通常、移行によるコストの削減は、前の運用資産が使用停止され、正常に処分されたときに実現します。
- **振り返り。** すべてのリリースで、成長思考について深く学び、それを身に付ける機会が生まれます。 各リリース サイクルが完了したら、クラウド導入チームは、移行中に使用されたプロセスを評価して改善点を識別する必要があります。

## <a name="next-steps"></a>次のステップ

最適化プロセスをおおまかに理解したうえで、[候補となるワークロードのビジネス変更プランを確立する](./business-change-plan.md)ことで、このプロセスを開始できます。

> [!div class="nextstepaction"]
> [ビジネス変更プラン](./business-change-plan.md)
