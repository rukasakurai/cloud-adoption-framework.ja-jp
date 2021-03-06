---
title: 基本的なワークロードを Azure にデプロイする
description: クラウド インフラストラクチャのコア コンポーネントと基本的なワークロード (基本的な Web アプリ、単一の仮想マシン、仮想ネットワークなど) について説明します。
author: alexbuckgit
ms.author: abuck
ms.date: 12/31/2018
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: 0b4f0738e93d08be4f61183272b459eb7856aad1
ms.sourcegitcommit: bd9872320b71245d4e9a359823be685e0f4047c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862213"
---
# <a name="deploy-a-basic-workload-in-azure"></a>基本的なワークロードを Azure にデプロイする

"_ワークロード_" という用語は、通常、アプリケーションやサービスなどの機能の任意の単位と定義されています。 ワークロードを、サーバーにデプロイされているコード成果物、およびアプリケーションに固有のその他のサービスという観点で考えることができるようになります。 しかし、これはオンプレミスのアプリケーションやサービスには便利な定義ですが、クラウド アプリケーションには拡大する必要があります。

クラウドのワークロードには、すべての成果物だけではなく、クラウド リソースも含まれます。 クラウド リソースをこの定義に含める理由は、「コードとしてのインフラストラクチャ」と呼ばれる概念です。 [Azure のしくみ](../../get-started/what-is-azure.md)に関するページで確認したように、Azure ではリソースがオーケストレーター サービスによってデプロイされます。 このオーケストレーター サービスでは、機能は Web API を使用して公開され、この Web API は、PowerShell、Azure CLI、Azure portal など、複数のツールを使用して呼び出すことができます。 つまり、Azure リソースは、アプリケーションに関連付けられているコード成果物と共に格納できる、コンピューターが読み取り可能なファイルで指定できます。

これにより、コード成果物および必要なクラウド リソースという観点からワークロードを定義でき、さらにワークロードを分離することができるようになります。 ワークロードは、リソースが整理されている方法、ネットワーク トポロジ、またはその他の属性によって分離できます。 ワークロードを分離する目的は、ワークロード固有のリソースをチームに関連付け、これらのリソースのあらゆる側面をチームが個別に管理できるようにすることです。 こうすることで、複数のチームが Azure でリソース管理サービスを共有する際に、お互いのリソースを誤って削除または変更してしまうのを防ぐことができます。

また、この分離により、DevOps という概念も可能になります。 DevOps とは、上記のソフトウェア開発と IT 運用の両方を含む、可能な限り自動化が使用されたソフトウェアの開発プラクティスです。 DevOps の原則の 1 つは、継続的インテグレーションと継続的デリバリー (CI/CD) と呼ばれます。 継続的インテグレーションとは、開発者がコードの変更をコミットするたびに実行される自動化されたビルド プロセスを意味します。 継続的デリバリーは、開発環境 (テストの場合) や運用環境 (最終デプロイの場合) など、さまざまな環境へのこのコードの自動デプロイ プロセスを意味します。

## <a name="basic-workload"></a>基本的なワークロード

"_基本的なワークロード_" は、通常、単一の Web アプリケーション、または仮想マシン (VM) を含む仮想ネットワークと定義されます。

> [!NOTE]
> このガイドでは、アプリケーション開発については取り上げません。 Azure でのアプリケーション開発の詳細については、「[Azure アプリケーション アーキテクチャ ガイド](https://docs.microsoft.com/azure/architecture/guide)」を参照してください。

ワークロードが Web アプリケーションか VM かに関係なく、これらのデプロイには_リソース グループ_が必要です。 リソース グループを作成するアクセス許可を持つユーザーは、次の手順に従う前に、リソース グループを作成する必要があります。

## <a name="basic-web-application-paas"></a>基本的な Web アプリケーション (PaaS)

基本的な Web アプリケーションの場合、[Web Apps のドキュメント](https://docs.microsoft.com/azure/app-service)に記載されている 5 分間のクイック スタートのいずれかを選択し、その手順に従います。

> [!NOTE]
> 一部のクイック スタート ガイドでは、既定でリソース グループがデプロイされます。 その場合、明示的にリソース グループを作成する必要はありません。 それ以外の場合は、上記で作成したリソース グループに Web アプリケーションをデプロイします。

単純なワークロードをデプロイしたら、Azure への[基本的な Web アプリケーション](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/basic-web-app)のデプロイに関するベスト プラクティスの詳細を学習できます。

## <a name="single-windows-or-linux-vm-iaas"></a>単一の Windows または Linux VM (IaaS)

仮想マシンで実行される単純なワークロードの場合、最初のステップとして、仮想ネットワークをデプロイします。 仮想マシン、ロード バランサー、ゲートウェイなど、Azure のすべてのサービスとしてのインフラストラクチャ (IaaS) リソースには、仮想ネットワークが必要です。 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) について学習したら、[ポータルを使用して仮想ネットワークを Azure にデプロイする](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)手順に従います。 Azure portal で仮想ネットワークの設定を指定するときに、上記で作成したリソース グループの名前を必ず指定します。

次のステップでは、単一の Windows VM または Linux VM のどちらをデプロイするかを決定します。 Windows VM の場合は、[ポータルを使用して Windows VM を Azure にデプロイする](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)手順に従います。 ここでも、Azure portal で仮想マシンの設定を指定するときに、上記で作成したリソース グループの名前を指定します。

手順に従って VM をデプロイしたら、[Azure での Windows VM の実行に関するベスト プラクティス](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/single-vm)について学習できます。 Linux VM の場合は、[ポータルを使用して Linux VM を Azure にデプロイする](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)手順に従います。 [Azure での Linux VM の実行に関するベスト プラクティス](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-linux/single-vm)の詳細を参照することもできます。

## <a name="next-steps"></a>次のステップ

Azure クラウドのコア インフラストラクチャ コンポーネントの使用方法の詳細については、[アーキテクチャの意思決定ガイド](../../decision-guides/index.md)に関するページを参照してください。
