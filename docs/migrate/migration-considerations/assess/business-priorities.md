---
title: 長期間の変換プロセス中にビジネスの優先順位を維持する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 長期間の変換プロセス中にビジネスの優先順位を維持します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/04/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 05d9441eb8867ff47ad23ccc44a9b58d8e010499
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70839093"
---
# <a name="business-priorities---maintaining-alignment"></a>ビジネスの優先順位 - 整合の維持

*変換*は多くの場合、劇的な、または自然発生的な変化として定義されます。 取締役会レベルでは、変化が劇的な変換のように見えることがあります。 ただし、組織の中で変化のプロセスを通して作業する従業員にとって、変換は少し誤解を招くような言葉です。 表面下では、変換は、一連の適切に実行されるある状態から別の状態への切り替えとしてより適切に説明されます。

ワークロードを合理化したり、切り替えたりするために必要な時間は、含まれる技術的な複雑さによって異なります。 ただし、このプロセスを 1 つのワークロードまたはアプリケーション グループにすばやく適用できる場合でも、ユーザー ベースの間に大きな変化が生まれるための時間がかかります。 それらの変化を既存のビジネス プロセスのさまざまなレイヤーにわたって伝播するには、より長い時間がかかります。 消費者の行動パターンを形成するために変換が期待される場合は、大きな結果を生むためにより長い時間がかかることがあります。

しかし残念ながら、市場はビジネスの切り替えを待ってはくれません。 消費者の行動パターンは、多くの場合は予期せずに、独自に変化します。 企業とその製品に対する市場の認識は、ソーシャル メディアや競合相手の位置付けによって影響を受ける場合があります。 急速で、かつ予期しない市場の変化に対して、企業は迅速に対応できる必要があります。

プロセスや技術的な切り替えを実行する能力には、一貫性のある、安定した取り組みが必要です。 市場の状態に対応するには、すばやい意思決定と迅速なアクションが必要です。 これらの 2 つは反目する条件であるため、容易に優先順位の整合が取れなくなります。 この記事では、移行作業中に移行の整合を維持するためのアプローチについて説明します。

<!-- markdownlint-disable MD026 -->

## <a name="how-can-business-and-technical-priorities-stay-aligned-during-a-migration"></a>移行中にビジネスの優先順位と技術的な優先順位をどのように整合させることができるか

クラウド導入チームとクラウド ガバナンス チームは、現在のイテレーションと現在のリリースの実行に重点を置いています。 イテレーションでは技術的な作業の安定した増分が提供されるため、そうでなければ移行作業の進捗を遅延させることになる費用のかかる中断が回避されます。 リリースでは、技術的な作業やエネルギーが、ワークロード移行のビジネス目標に引き続き重点を置くことが保証されます。 移行プロジェクトでは、期間が延長され、多数のリリースが必要になる場合があります。 それが完了するまでに、市場の状態が大きく変化している可能性があります。

同時に、クラウド戦略チームは、ビジネス変更計画の実行と次のリリースの準備に重点を置いています。 クラウド戦略チームは一般に、少なくとも 1 つのリリースを前もって調べ、変化する市場の状態を監視し、それに応じて移行バックログを調整します。 この変換の管理と計画の調整に重点を置くことによって、技術的な作業の周りに自然なピボットが生まれます。 ビジネスの優先順位が変化しても、導入は 1 つのリリース分遅れるだけであり、技術的な敏捷性とビジネスの敏捷性が生まれます。

## <a name="business-alignment-questions"></a>ビジネスの整合に関する質問

次の質問は、クラウド戦略チームが、変換作業が確実に現在のビジネス ニーズに最適に整合した状態になるようにするために、移行バックログを形成して優先順位付けするのに役立ちます。

- クラウド導入チームは、移行のために準備ができているワークロードの一覧を識別しましたか?
- クラウド導入チームは、そのワークロードの一覧から初期の移行として 1 つの候補を選択しましたか?
- クラウド導入チームとクラウド ガバナンス チームは、成功させようとしているワークロードやクラウド環境に関連した必要なすべてのデータを用意していますか?
- 候補のワークロードは、次のリリースでビジネスに最も関連する影響を与えることができますか?
- 移行のためのより適切な候補である他のワークロードは存在しますか?

## <a name="tangible-actions"></a>具体的なアクション

ビジネス変更計画の実行中に、クラウド戦略チームは肯定的な結果と否定的な結果を監視します。 これらの観察に技術的な変更が必要になった場合は、次のイテレーションで優先順位が付けられるリリース バックログに作業項目として調整が追加されます。

市場が変化すると、クラウド戦略チームは企業と協力して、それらの変化に最適に対応する方法を把握します。 その対応に移行の優先順位の変更が必要になった場合は、移行バックログが調整されます。 これにより、前に優先順位が低かったワークロードの優先順位が上がります。

## <a name="next-steps"></a>次の手順

ビジネスの優先順位が適切に整合されている場合、クラウド導入チームはアーキテクチャと移行計画を開発するために、[ワークロードの評価](./evaluate.md)を自信を持って開始できます。

> [!div class="nextstepaction"]
> [ワークロードを評価する](./evaluate.md)