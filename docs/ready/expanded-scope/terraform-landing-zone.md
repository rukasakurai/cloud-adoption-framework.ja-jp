---
title: Terraform を使用してランディング ゾーンを構築する
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: Terraform を使用してランディング ゾーンを構築する方法を説明します。
author: arnaudlh
ms.author: arnaul
ms.date: 10/16/2019
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
ms.openlocfilehash: 51751ab0033505e34c02c17db363bc985b83e44d
ms.sourcegitcommit: e0a783dac15bc4c41a2f4ae48e1e89bc2dc272b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2019
ms.locfileid: "73058150"
---
# <a name="use-terraform-to-build-your-landing-zones"></a>Terraform を使用してランディング ゾーンを構築する

Azure には、ランディング ゾーンをデプロイするためのネイティブ サービスが用意されています。 他のサードパーティ製ツールも、この作業に役立ちます。 顧客やパートナーがランディング ゾーンのデプロイによく使用するツールの 1 つは、Hashicorp の Terraform です。 このセクションでは、プロトタイプのランディング ゾーンを使用して、Azure サブスクリプションに基本的なログ記録、アカウンティング、およびセキュリティ機能をデプロイする方法について説明します。

## <a name="purpose-of-the-landing-zone"></a>ランディング ゾーンの目的

Terraform のクラウド導入フレームワークのファンデーション ランディング ゾーンには、ログ記録、アカウンティング、およびセキュリティを適用するための、限られた責任と機能のセットがあります。 このランディング ゾーンは、Terraform モジュールと呼ばれる標準コンポーネントを使用して、環境にデプロイされるリソース間の一貫性が維持されるように設計されています。

## <a name="using-standard-modules"></a>標準モジュールの使用

コンポーネントの再利用は、コードとしてのインフラストラクチャの基本原則です。 モジュールは、環境内および環境間でのリソースのデプロイにおける標準と一貫性の定義に役立ちます。 この最初のランディング ゾーンをデプロイするために使用される一連のモジュールは、公式の [Terraform レジストリ](https://registry.terraform.io/search?q=aztfmod)で入手できます。

## <a name="architecture-diagram"></a>アーキテクチャ ダイアグラム

最初のランディング ゾーンでは、サブスクリプションに次のコンポーネントがデプロイされます。

![Terraform を使用したファンデーション ランディング ゾーン](../../_images/ready/foundations-terraform-landingzone.png)

## <a name="capabilities"></a>機能

デプロイされるコンポーネントとその目的は次のとおりです。

| コンポーネント | 担当 |
|---------|---------|
| リソース グループ | ファンデーションに必要なコア リソース グループ |
| アクティビティ ログ | サブスクリプションのすべてのアクティビティの監査とアーカイブ: </br> - ストレージ アカウント </br> - Event Hubs |  
| 診断ログ | 特定の日数だけ保持されるすべての操作ログ: </br> - ストレージ アカウント </br> - Event Hubs |
| Log Analytics | すべての操作ログの保存 </br> 詳細なアプリケーションのベスト プラクティスのレビューのための一般的なソリューションのデプロイ: </br> - NetworkMonitoring </br> - ADAssessment </br> - ADReplication </br> - AgentHealthAssessment </br> - DnsAnalytics </br> - KeyVaultAnalytics
| Security Center | セキュリティ検疫メトリックおよびメールと電話番号に送信されるアラート |

## <a name="use-this-blueprint"></a>このブループリントを使用する

クラウド導入フレームワークのファンデーション ランディング ゾーンを使用する前に、以下の前提条件、決定事項、実装ガイダンスを確認してください。

## <a name="assumptions"></a>前提条件

この初期のランディング ゾーンを定義するときに、以下の前提条件または制約が考慮されました。 これらの前提条件がご自分の環境の制約と一致する場合、このブループリントを使用して最初のランディング ゾーンを作成することができます。 このブループリントは、固有の制約を満たすランディング ゾーン ブループリントを作成するために拡張することもできます。

- **サブスクリプションの制限**: この導入作業では、[サブスクリプションの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits)を超えることはないと考えられます。 2 つの一般的な指標は、25,000 個の VM または 10,000 個の vCPU を超過することです。
- **コンプライアンス:** このランディング ゾーンでは、サードパーティのコンプライアンス要件は必要ありません。
- **アーキテクチャの複雑さ:** アーキテクチャの複雑さによって、追加の運用サブスクリプションが常に必要になるわけではありません。
- **共有サービス:** このサブスクリプションをハブアンドスポーク アーキテクチャのスポークのように扱うことを要求する既存の共有サービスは Azure にありません。

これらの前提条件が現在の環境と一致する場合、このブループリントは、ランディング ゾーンの作成を行うための適切な開始点となる可能性があります。

## <a name="design-decisions"></a>設計上の決定

Terraform ランディング ゾーンでは、次の決定事項が示されます。

| コンポーネント | 決定事項 | 他の方法 |
| --- | --- | --- |
|ログ記録と監視 | Azure Monitor Log Analytics ワークスペースが使用されます。 診断ストレージ アカウントとイベント ハブがプロビジョニングされます。 |         |
|ネットワーク | 該当なし - ネットワークは別のランディング ゾーンに実装されます。 |[ネットワーク関連の意思決定](../considerations/network-decisions.md) |
|ID | サブスクリプションは既に Azure Active Directory インスタンスに関連付けられていると想定されます。 | [ID 管理のベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices) |
| ポリシー | このランディング ゾーンでは現在、Azure ポリシーは適用されないものと想定されています。 | |
|サブスクリプション デザイン | 該当なし - 単一の運用サブスクリプション用に設計されています。 | [サブスクリプションのスケーリング](../considerations/scaling-subscriptions.md) |
| 管理グループ | 該当なし - 単一の運用サブスクリプション用に設計されています。 |[サブスクリプションのスケーリング](../considerations/scaling-subscriptions.md) |
| リソース グループ | 該当なし - 単一の運用サブスクリプション用に設計されています。 | [サブスクリプションのスケーリング](../considerations/scaling-subscriptions.md) |
| Data | 該当なし | 「[Azure SQL で適切なデプロイ オプションを選択する](https://docs.microsoft.com/azure/sql-database/sql-database-paas-vs-sql-server-iaas)」と [Azure データ ストア ガイダンス](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview) |
|Storage|該当なし|[Azure Storage ガイダンス](../considerations/storage-guidance.md) |
| 命名規則 | 環境が作成されるとき、一意のプレフィックスも作成されます。 グローバルな一意名 (ストレージ アカウントなど) を必要とするリソースでは、このプレフィックスが使用されます。 カスタム名に、ランダムなサフィックスが追加されます。 次の表で説明するように、タグを使用する必要があります。 | [名前付けとタグ付けのベスト プラクティス](../considerations/naming-and-tagging.md) |
| コスト管理 | 該当なし | [コストの追跡](../azure-best-practices/track-costs.md) |
| Compute | 該当なし | [コンピューティング オプション](../considerations/compute-decisions.md) |

### <a name="tagging-standards"></a>タグ付けの標準

すべてのリソースとリソース グループに、次の最小タグ セットが存在している必要があります。

| タグ名 | 説明 | Key | 値の例 |
|--|--|--|--|
| 事業単位 | リソースが属しているサブスクリプションまたはワークロードを所有する会社の最上位の部門。 | BusinessUnit | FINANCE、MARKETING、{Product Name}、CORP、SHARED |
| コスト センター | このリソースに関連付けられたアカウンティング コスト センター。| CostCenter | Number |
| ディザスター リカバリー | アプリケーション、ワークロード、またはサービスのビジネス上の重要度。 | DR | DR-ENABLED、NON-DR-ENABLED |
| 環境 | アプリケーション、ワークロード、またはサービスのデプロイ環境。 |  Env | Prod、Dev、QA、Stage、Test、Training |
| 所有者名 | このアプリケーション、ワークロード、またはサービスの所有者。| Owner | email |
| DeploymentType | リソースの管理方法を定義します。 | deploymentType | Manual、Terraform |
| Version | デプロイされるブループリントのバージョン | version | v0.1 |
| アプリケーション名 | リソースに関連付けられているアプリケーション、サービス、またはワークロードの名前。 | ApplicationName | "アプリ名" |

## <a name="customize-and-deploy-your-first-landing-zone"></a>最初のランディング ゾーンをカスタマイズしてデプロイする

[Terraform ファンデーション ランディング ゾーンを複製する](https://github.com/microsoft/CloudAdoptionFramework/tree/master/ready)ことができます。 Terraform の変数を変更することで、ランディング ゾーンを簡単に開始できます。 この例では **blueprint_foundations.sandbox.auto.tfvars** を使用するため、このファイルの値が Terraform によって自動的に設定されます。

さまざまな変数セクションを見てみましょう。

この最初のオブジェクトでは、"-hub-core-sec" および "-hub-core-sec" という名前の 2 つのリソース グループと、実行時に追加されるプレフィックスを、`southeastasia` リージョンに作成します。

```hcl
resource_groups_hub = {
    HUB-CORE-SEC    = {
        name = "-hub-core-sec"
        location = "southeastasia"
    }
    HUB-OPERATIONS  = {
        name = "-hub-operations"
        location = "southeastasia"
    }
}
```

次に、ファンデーションを設定できるリージョンを指定します。 ここでは、`southeastasia` を使用してすべてのリソースをデプロイします。

```hcl
location_map = {
    region1   = "southeastasia"
    region2   = "eastasia"
}
```

次に、操作ログと Azure サブスクリプション ログの保有期間を指定します。 このデータは、別のストレージ アカウントとイベント ハブに格納され、それらの名前は一意である必要があるためランダムに生成されます。

```hcl
azure_activity_logs_retention = 365
azure_diagnostics_logs_retention = 60
```

tags_hub では、作成されるすべてのリソースに適用されるタグの最小セットを指定します。

```hcl
tags_hub = {
    environment     = "DEV"
    owner           = "Arnaud"
    deploymentType  = "Terraform"
    costCenter      = "65182"
    BusinessUnit    = "SHARED"
    DR              = "NON-DR-ENABLED"
}
```

次に、デプロイの分析に使用されるログ分析名とソリューションのセットを指定します。 ここでは、ネットワーク監視、AD Assessment と Replication、DNS Analytics、および Key Vault Analytics を保持しました。

```hcl

analytics_workspace_name = "lalogs"

solution_plan_map = {
    NetworkMonitoring = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/NetworkMonitoring"
    },
    ADAssessment = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/ADAssessment"
    },
    ADReplication = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/ADReplication"
    },
    AgentHealthAssessment = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/AgentHealthAssessment"
    },
    DnsAnalytics = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/DnsAnalytics"
    },
    KeyVaultAnalytics = {
        "publisher" = "Microsoft"
        "product"   = "OMSGallery/KeyVaultAnalytics"
    }
}

```

次に、Azure Security Center のアラート パラメーターを構成しました。

```hcl
# Azure Security Center Configuration
security_center = {
    contact_email   = "joe@contoso.com"
    contact_phone   = "+6500000000"
}
```

## <a name="getting-started"></a>使用の開始

構成を確認した後、Terraform 環境をデプロイする場合と同様に、構成をデプロイできます。 ただし、rover を使用することをお勧めします。これは、Windows、Linux、または MacOS からデプロイできる Docker コンテナーです。 [rover GitHub リポジトリ](https://github.com/aztfmod/rover)で開始できます。

## <a name="next-steps"></a>次の手順

ファンデーション ランディング ゾーンは、複雑な環境の基礎が分解して構築されます。 このエディションでは、次の方法で拡張できる非常に単純な機能のセットが提供されています。

- ブループリントに他のモジュールを追加します。
- 上位に追加のランディング ゾーンを重ねます。

ランディング ゾーンを重ねることは、システムを分離し、使用している各コンポーネントのバージョンを管理し、コードとしてのインフラストラクチャのデプロイの迅速なイノベーションと安定を実現するための良い方法です。

今後の参照アーキテクチャでは、ハブとスポークのトポロジに関するこの概念を説明します。

> [!div class="nextstepaction"]
> [Terraform を使用したファンデーション ランディング ゾーンのサンプルを確認してください](https://github.com/microsoft/CloudAdoptionFramework/tree/master/ready)