---
title: Azure のデプロイ高速化ツール
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Azure のデプロイ高速化ツール
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: d47162b3e1a6a303e8b346146948667bc42c2326
ms.sourcegitcommit: d19e026d119fbe221a78b10225230da8b9666fe1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71222679"
---
# <a name="deployment-acceleration-tools-in-azure"></a>Azure のデプロイ高速化ツール

[デプロイ高速化](./index.md)は、[5 つのクラウド ガバナンス規範](../governance-disciplines.md)のうちの 1 つです。 この規範は、資産の構成またはデプロイを管理するためのポリシーを確立する方法に焦点を当てています。 クラウド ガバナンスの 5 つの規範のうち、デプロイ高速化規範には、デプロイと構成の整合が含まれます。 これは手動のアクティビティによる場合と、完全に自動化された DevOps アクティビティによる場合があります。 どちらの場合も、関係しているポリシーはほとんど同じままです。

ガバナンスに関心があるクラウド管理人、クラウド保護者、およびクラウド アーキテクトはそれぞれ、複数のクラウド導入の取り組みに共通するポリシーと要件を体系化するデプロイ高速化規範に多くの時間を費やす可能性が高くなります。 このツールチェーンのツールはクラウド ガバナンス チームにとって重要であり、チームのラーニング パスでの優先度を高くする必要があります。

このガバナンス規範をサポートするポリシーとプロセスを成熟させるのに役立つ Azure ツールの一覧を次に示します。

|  | [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) | [Azure 管理グループ](https://docs.microsoft.com/azure/governance/management-groups) | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) | [Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/overview) | [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) | [Azure Cost Management](https://docs.microsoft.com/azure/cost-management) |
|---------|---------|---------|---------|---------|---------|---------|
|企業ポリシーを実装する     |はい |いいえ  |いいえ  |いいえ | いいえ |いいえ |
|サブスクリプション間でポリシーを適用する     |必須 |はい  |いいえ  |いいえ | いいえ |いいえ |
|定義済みリソースをデプロイする     |いいえ |いいえ  |はい  |いいえ | いいえ |いいえ |
|完全に準拠した環境を作成する      |必須 |必須  |必須  |はい | いいえ |いいえ |
|ポリシーを監査する      |はい |いいえ  |いいえ  |いいえ | いいえ |いいえ |
|Azure リソースをクエリする      |いいえ |いいえ  |いいえ  |いいえ |はい |いいえ |
|リソースのコストをレポートする      |いいえ |いいえ  |いいえ  |いいえ |いいえ |はい |

次に示すのは、特定のデプロイ高速化目標を達成するために必要な場合がある追加のツールです。 これらのツールはガバナンス チームの外側で使用されることが多いですが、それでも規範としてのデプロイ高速化の一側面とみなされています。

|  | [Azure Portal](https://azure.microsoft.com/features/azure-portal)  | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)  | [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) | [Azure DevOps](https://docs.microsoft.com/azure/devops/index) | [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) | [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) |
|---------|---------|---------|---------|---------|---------|---------|
|手動デプロイ (単一アセット)     | はい | はい  | いいえ  | 非効率的 | いいえ | はい |
|手動デプロイ (環境全体)     | 非効率的 | はい | いいえ  | 非効率的 | いいえ | はい |
|自動デプロイ (環境全体)     | いいえ  | はい  | いいえ  | はい  | いいえ | はい |
|単一資産の構成を更新する     | はい | はい | 非効率的 | 非効率的 | いいえ | はい - レプリケーション中 |
|環境全体の構成を更新する     | 非効率的 | はい | はい | はい  | いいえ | はい - レプリケーション中 |
|構成ドリフトを管理する     | 非効率的 | 非効率的 | はい  | はい  | いいえ | はい - レプリケーション中 |
|コードをデプロイして資産を構成するための自動化パイプラインを作成する (DevOps)     | いいえ | いいえ | いいえ | はい | いいえ | いいえ |

以上の Azure ネイティブ ツールとは別に、デプロイ高速化と DevOps デプロイを促進するために顧客がサード パーティ製のツールを使用することはよくあります。