---
title: ソフトウェア定義ネットワーク:ハブ アンド スポーク
description: Azure 向けクラウド導入フレームワークを使用して、ハブとスポークのネットワークで、ネットワーク インフラストラクチャを、接続された複数の仮想ネットワークに編成する方法を学習します。
author: rotycenh
ms.author: abuck
ms.date: 02/11/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: decision-guide
ms.custom: governance
ms.openlocfilehash: 9f77d4a13b83c6d22592f36c924c2dd11164e27b
ms.sourcegitcommit: 070e6a60f05519705828fcc9c5770c3f9f986de5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/24/2020
ms.locfileid: "83814360"
---
# <a name="software-defined-networking-hub-and-spoke"></a>ソフトウェア定義ネットワーク:ハブ アンド スポーク

ハブ アンド スポークのネットワーク モデルは、複数接続されている仮想ネットワークに、Azure ベースのクラウド ネットワーク インフラストラクチャを編成します。 このモデルを使用すると、一般的な通信やセキュリティの要件をより効率的に管理し、潜在的なサブスクリプションの制限に対処できます。

ハブ アンド スポークのモデルで、_ハブ_は、外部接続を管理したり、複数のワークロードによって使用されるサービスをホストしたりするための中心的な場所として機能する仮想ネットワークです。 _スポーク_は、ワークロードをホストし、[仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を介して中央のハブに接続する仮想ネットワークです。

ワークロード スポーク ネットワークを出入りするすべてのトラフィックは、ハブ ネットワークを介してルーティングされます。そのハブ ネットワークで、集中管理された IT 規則またはプロセスによってトラフィックをルーティング、検査、またはその他の方法で管理できます。

このモデルは、次の問題に対処することを目的としています。

- **コストの削減と管理の効率。** 複数のワークロードで共有できるサービス (ネットワーク仮想アプライアンス (NVA) や DNS サーバーなど) を 1 か所に集めることで、IT は複数のワークロードにわたって過剰なリソースと管理作業を最小限にすることができます。
- **サブスクリプションの制限の克服。** 大規模なクラウドベースのワークロードでは、単一の Azure サブスクリプション内で許可されるリソースよりも多くのリソースの使用が求められる場合があります。 さまざまなサブスクリプションから中央のハブへのワークロード仮想ネットワークのピアリングで、こうした制限を克服できます。 詳細については、[Azure のネットワークの制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)に関するページをご覧ください。
- **懸念事項の分離。** 中央の IT チームとワークロード チームの間で個々のワークロードをデプロイする機能。

次の図は、集中管理されたハイブリッド接続を含むハブ アンド スポークのアーキテクチャの例を示しています。

![ハブ アンド スポーク ネットワーク アーキテクチャ](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/images/hub-spoke.png)

ハブ アンド スポークのアーキテクチャは、ハイブリッド ネットワーク アーキテクチャと共によく使用され、複数のワークロード間で共有されるオンプレミス環境への集中管理された接続を提供します。 このシナリオでは、ワークロードとオンプレミスの間を移動するすべてのトラフィックはハブを通過します。そのハブで、トラフィックを管理および保護できます。

## <a name="hub-and-spoke-assumptions"></a>ハブ アンド スポークの前提条件

ハブ アンド スポークの仮想ネットワーク アーキテクチャの実装は、以下のことを前提とします。

- クラウドのデプロイには、開発、テスト、実稼働などの個別の作業環境でホストされるワークロードが含まれ、それらはすべて DNS やディレクトリ サービスなどの一連の共通サービスに依存します。
- ワークロードは相互に通信する必要はありませんが、共通の外部通信と共有サービスの要件があります。
- ワークロードには、単一の Azure サブスクリプション内で使用できるリソースよりも多くのリソースが必要です。
- ワークロード チームには、外部接続に対する集中的なセキュリティ管理を維持しながら、自身のリソースに対する委任管理権限を付与する必要があります。

## <a name="global-hub-and-spoke"></a>グローバルなハブ アンド スポーク

ハブ アンド スポークのアーキテクチャの実装は、通常、ネットワーク間の待機時間を最小限に抑えるために、同じ Azure リージョンにデプロイされた仮想ネットワークを使用して行われます。 世界規模で展開している大規模組織では、可用性、ディザスター リカバリー、または規制の要件に対応するために、複数のリージョンにわたってワークロードをデプロイすることが必要な場合があります。 ハブ アンド スポーク モデルでは、Azure の[グローバル仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を使用して、集中管理と共有サービスをリージョン全体に拡張し、世界中に分散されたワークロードをサポートできます。

## <a name="learn-more"></a>詳細情報

Azure 上にハブとスポークのネットワークを実装する方法を示す参照アーキテクチャについては、以下を参照してください。

- [ハブ アンド スポーク ネットワーク トポロジを Azure 上に実装する](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
- [共有サービスを含むハブ アンド スポーク ネットワーク トポロジを Azure 上に実装する](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services)
