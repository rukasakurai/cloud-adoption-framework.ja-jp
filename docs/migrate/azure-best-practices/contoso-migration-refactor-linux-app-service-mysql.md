---
title: Azure App Service と Database for MySQL に Linux アプリをリファクターする
description: Azure 向けのクラウド導入フレームワークを使用し、Linux サービス デスク アプリを Azure App Service および Azure Database for MySQL にリファクターする方法について説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: migrate
ms.openlocfilehash: a19414ad0cb08a085139dc73d22e2e3a197cc32e
ms.sourcegitcommit: bd9872320b71245d4e9a359823be685e0f4047c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83861703"
---
<!-- cSpell:ignore WEBVM SQLVM contosohost vcenter contosodc OSTICKETWEB OSTICKETMYSQL osticket contosoosticket trafficmanager InnoDB binlog DBHOST DBUSER CNAME -->

# <a name="refactor-a-linux-app-to-multiple-regions-using-azure-app-service-traffic-manager-and-azure-database-for-mysql"></a>Azure App Service、Traffic Manager、および Azure Database for MySQL を使用して複数のリージョンに Linux アプリをリファクターする

この記事では、架空の会社である Contoso が、2 階層の Linux ベースの Apache MySQL PHP (LAMP) アプリをリファクターし、オンプレミスから Azure に移行して Azure App Service と GitHub の統合と、Azure Database for MySQL を使用する方法を示します。

この例で使用される osTicket (サービス デスク アプリ) は、オープン ソースとして提供されています。 独自のテスト目的に沿って使用する場合は、[GitHub の osTicket リポジトリ](https://github.com/osTicket/osTicket)からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らが何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応。** Contoso は成長し続けており、新しい市場に進出しつつあります。 そのため顧客サービス担当者を追加する必要があります。
- **スケール。** Contoso は、事業規模が拡大したときに、顧客サービス担当者を追加できるように、ソリューションを構築する必要があります。
- **回復性を向上させる。** 過去にシステムで発生した問題は、内部ユーザーのみに影響していました。 新しいビジネス モデルでは、外部ユーザーも影響を受けるため、Contoso は、アプリを常時稼働させる必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、最適な移行方法を決定するために、この移行の目標を明確にしました。

- アプリケーションは、現在のオンプレミスのキャパシティとパフォーマンスを越えるようにスケーリングする必要があります。 Contoso は、Azure のオンデマンド スケーリングを活用するためにアプリケーションを移行します。
- Contoso は、アプリのコード ベースを継続的デリバリー パイプラインに移したいと考えています。 アプリの変更が GitHub にプッシュされたときに、運用スタッフのタスクなしでその変更をデプロイしたいと考えています。
- アプリケーションは、成長とフェールオーバーに対応するための機能を備えた回復力のあるものにする必要があります。 Contoso は 2 つの異なる Azure リージョンにアプリをデプロイし、自動的にスケーリングするように設定したいと考えています。
- Contoso は、アプリケーションがクラウドに移された後、データベース管理タスクを最小限にしたいと考えています。

## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

## <a name="current-architecture"></a>現在のアーキテクチャ

- アプリは 2 つの VM (OSTICKETWEB および OSTICKETMYSQL) にわたって階層化されます。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。

![現在のアーキテクチャ](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png)

## <a name="proposed-architecture"></a>提案されたアーキテクチャ

次のアーキテクチャが提案されます。

- OSTICKETWEB 上の Web 層のアプリは、Azure App Service を 2 つの Azure リージョン内にビルドすることで移行されます。 Azure App Service for Linux は、PHP 7.0 Docker コンテナーを使用して実装されます。
- アプリ コードは GitHub に移動され、Azure App Service Web アプリが GitHub を使用した継続的デリバリー用に構成されます。
- Azure アプリ サーバーは、プライマリ リージョン (米国東部 2) とセカンダリ リージョン (米国中部) の両方にデプロイされます。
- 両方のリージョンで、2 つの Azure Web Apps の前に Traffic Manager が設定されます。
- Traffic Manager は、トラフィックを強制的に米国東部 2 を通過させるために、優先モードで構成されます。
- 米国東部 2 リージョン内の Azure アプリ サーバーがオフラインになった場合でも、ユーザーは、米国中部リージョンにフェールオーバーされたアプリにアクセスできます。
- アプリのデータベースは、Azure Database Migration Service (DMS) を使用して Azure Database for MySQL サービスに移行されます。 オンプレミスのデータベースはローカルでバックアップされ、Azure Database for MySQL に直接復元されます。
- このデータベースは、プライマリ リージョンである米国東部 2 で、運用ネットワーク (VNET-PROD-EUS2) のデータベース サブネット (PROD-DB-EUS2) に存在します。
- 運用ワークロードを移行しようとしているため、アプリの Azure リソースは、運用リソース グループ **ContosoRG** 内に存在することになります。
- Traffic Manager リソースは、Contoso のインフラストラクチャ リソース グループ **ContosoInfraRG** にデプロイされます。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png)

## <a name="migration-process"></a>移行プロセス

Contoso は、次のようにして移行プロセスを完了します。

1. 最初のステップとして、Contoso 管理者は、Azure インフラストラクチャをセットアップします。これには、Azure App Service のプロビジョニング、Traffic Manager の設定、および Azure Database for MySQL インスタンスのプロビジョニングが含まれます。
2. Azure インフラストラクチャを準備した後、Azure Database Migration Service (DMS) を使用してデータベースを移行します。
3. Azure でデータベースが実行されたら、継続的デリバリーで使用する Azure App Service 用の GitHub プライベート リポジトリを設定し、それを osTicket アプリで読み込みます。
4. Azure Portal で、Azure App Service を実行する Docker コンテナーに GitHub からアプリを読み込みます。
5. DNS の設定を調整し、アプリの自動スケーリングを構成します。

![移行プロセス](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure App Service](https://azure.microsoft.com/services/app-service) | このサービスでは、Web サイト向けの Azure PaaS サービスを使用してアプリケーションを実行およびスケーリングします。 | 価格は、インスタンスのサイズと必要な機能に基づきます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/app-service/windows)。
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager) | DNS を使用して、Azure、外部 Web サイト、またはサービスにユーザーを送るロード バランサー。 | 価格は、受信した DNS クエリの数と監視対象のエンドポイントの数に基づきます。 | [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/traffic-manager)。
[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Azure Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)に関する情報と、[Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration)に関する情報をご覧ください。
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql) | データベースは、オープン ソースの MySQL Server エンジンに基づいています。 これは、アプリの開発とデプロイ向けのフルマネージドのエンタープライズ対応コミュニティ MySQL データベースです。 | 価格は、コンピューティング、ストレージ、およびバックアップ要件に基づきます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/mysql)。

## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

<!-- markdownlint-disable MD033 -->

**必要条件** | **詳細**
--- | ---
**Azure サブスクリプション** | Contoso は、この記事シリーズの前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)を作成してください。 <br><br> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。 <br><br> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](./contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。

<!-- markdownlint-enable MD033 -->

## <a name="scenario-steps"></a>シナリオのステップ

Contoso は、次のようにして移行を完了します。

> [!div class="checklist"]
>
> - **ステップ 1:Azure App Service をプロビジョニングする。** Contoso 管理者は、プライマリ リージョンとセカンダリ リージョンに Web Apps をプロビジョニングします。
> - **手順 2:Traffic Manager を設定する。** トラフィックのルーティングと負荷分散を行う ために、Traffic Manager を Web アプリの前に設定します。
> - **ステップ 3:MySQL をプロビジョニングする。** Azure で、Azure Database for MySQL のインスタンスをプロビジョニングします。
> - **手順 4:データベースを移行する。** Azure Database Migration Service (DMS) を使用してデータベースを移行します。
> - **手順 5:GitHub を設定する。** アプリの Web サイト/コード用のローカル GitHub リポジトリを設定します。
> - **手順 6:Web アプリをデプロイする。** GitHub から Web アプリをデプロイします。

## <a name="step-1-provision-azure-app-service"></a>手順 1:Azure App Service をプロビジョニングする

Contoso 管理者は、Azure App Service を使用する 2 つの Web アプリを (各リージョンに 1 つずつ) プロビジョニングします。

1. Azure Marketplace からプライマリ リージョンである米国東部 2 内に Web App リソース (**osticket eus2**) を作成します。
2. 運用リソース グループ **ContosoRG** にリソースを配置します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png)

3. プライマリ リージョンで、標準サイズを使用する新しい App Service プラン (**APP-SVP-EUS2**) を作成します。

     ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png)

4. Contoso は、Docker コンテナーである PHP 7.0 ランタイム スタックを使用する Linux OS を選択します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png)

5. 米国中部リージョンで、2 つ目の Web アプリ (**osticket-cus**) と Azure App Service プランを作成します。

    ![Azure アプリ](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png)

**さらにサポートが必要な場合**

- [Azure App Service Web アプリ](https://docs.microsoft.com/azure/app-service/overview)に関する記事を参照します。
- 「[Azure App Service on Linux の概要](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)」を参照します。

## <a name="step-2-set-up-traffic-manager"></a>手順 2:Traffic Manager を設定する

Contoso 管理者は、受信 Web 要求を osTicket Web 層で実行中の Web Apps に送る Traffic Manager を設定します。

1. Azure Marketplace から Traffic Manager リソース (**osticket.trafficmanager.net**) を作成します。 米国東部 2 がプライマリ サイトになるように、優先順位によるルーティングを使用します。 そのリソースを、Contoso のインフラストラクチャ リソース グループ (**ContosoInfraRG**) 内に配置します。 Traffic Manager はグローバルであり、特定の場所にバインドされないことに注意してください。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png)

2. 次は Traffic Manager のエンドポイントを構成します。 米国東部 2 の Web アプリをプライマリ サイト (**osticket eus2**) として追加し、米国中部のアプリをセカンダリ (**osticket cu**) として追加します。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png)

3. エンドポイントを追加した後、それらを監視できます。

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**さらにサポートが必要な場合**

- 「[Traffic Manager の概要](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)」を参照します。
- [優先順位の高いエンドポイントへのトラフィックのルーティング](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method)に関する記事を参照します。

## <a name="step-3-provision-azure-database-for-mysql"></a>手順 3:Azure Database for MySQL をプロビジョニングする

Contoso 管理者は、MySQL データベース インスタンスを、プライマリの米国東部 2 リージョン内にプロビジョニングします。

1. Azure Portal で、Azure Database for MySQL のリソースを作成します。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Azure データベースの名前 **contosoosticket** を追加します。 運用リソース グループ **ContosoRG** にデータベースを追加し、そのための資格情報を指定します。
3. オンプレミスの MySQL データベースはバージョン 5.7 なので、互換性のためにこのバージョンが選択されています。 既定のサイズを使用しますが、それらはデータベースの要件に適合しています。

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. **バックアップ冗長オプション**については、**Geo 冗長**を使用することを選択しています。 このオプションでは、停止が発生した場合、セカンダリの米国中部リージョンでデータベースを復元できます。 このオプションは、データベースをプロビジョニングするときにのみ構成できます。

    ![冗長性](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

5. 接続のセキュリティを設定します。 データベースの **[接続のセキュリティ]** で、データベースによる Azure サービスへのアクセスを許可するファイアウォール ルールを設定します。

6. 開始 IP アドレスと終了 IP アドレスに、ローカル ワークステーションのクライアント IP アドレスを追加します。 これにより、Web アプリが MySQL データベースと、移行を実行するデータベース クライアントにアクセスできるようになります。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)

## <a name="step-4-migrate-the-database"></a>手順 4:データベースを移行する

MySQL データベースは、次の方法で移動できます。 それぞれのオプションで、ターゲットに対して Azure DB for MySQL インスタンスを作成する必要があります。 作成したら、次の 2 つのパスを使用して移行を実行できます。

- 4a:Azure Database Migration Service
- 4b:MySQL Workbench のバックアップと復元

### <a name="step-4a-migrate-the-database-azure-database-migration-service"></a>手順 4a: データベースの移行 (Azure Database Migration Service)

Contoso の管理者は、[ステップバイステップの移行チュートリアル](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)を使い、Azure Database Migration Service を使用してデータベースを移行します。 MySQL 5.6 または 5.7 を使用して、オンライン、オフライン、およびハイブリッド (プレビュー) の移行を実行できます。

> [!NOTE]
> MySQL 8.0 は Azure Database for MySQL でサポートされていますが、DMS ツールはまだこのバージョンをサポートしていません。

まとめると、次を実行する必要があります。

- 次のすべての移行の前提条件が満たされていることを確認します。
  - MySQL サーバーのソースは、Azure Database for MySQL でサポートされているバージョンと一致する必要があります。 Azure Database for MySQL は、MySQL Community Edition と InnoDB エンジンをサポートし、同じバージョンのソースとターゲット間の移行をサポートしています。
  - my.ini (Windows) または my.cnf (Unix) のバイナリ ログを有効にします。 この操作を行わないと、移行ウィザードの実行中に `Error in binary logging. Variable binlog_row_image has value 'minimal'. Please change it to 'full'. For more information, see https://go.microsoft.com/fwlink/?linkid=873009` が発生します。
  - ユーザーは `ReplicationAdmin` ロールを持っている必要があります。
  - 外部キーとトリガーを使用せずにデータベース スキーマを移行します。
- ExpressRoute または VPN を介してオンプレミス ネットワークに接続する仮想ネットワークを作成します。
- VNet に接続されている `Premium` SKU を持つ Azure Database Migration Service を作成します。
- Azure Database Migration Service が仮想ネットワーク経由で MySQL データベースにアクセスできることを確認します。 これには、仮想ネットワーク レベル、ネットワーク VPN、および MySQL をホストするマシンで、 Azure から MySQL にすべての受信ポートが許可されていることの確認が伴います。
- Azure Database Migration Service ツールを実行する:
  - **Premium SKU** に基づいて移行プロジェクトを作成します。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-new-project.png)

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-new-project-02.png)

  - ソース (オンプレミス データベース) を追加します。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-source.png)

  - ターゲットを選択します。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-target.png)

  - 移行するデータベースを選択します。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-databases.png)

  - 詳細設定を構成します。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-settings.png)

  - レプリケーションを開始し、エラーを解決します。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-monitor.png)
  
  - 最終的なカットオーバーを実行します。
  
    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-cutover.png)

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-cutover-complete.png)

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-cutover-complete-02.png)
  
  - 外部キーとトリガーをすべて復帰させます。

  - 新しいデータベースを使用するようにアプリケーションを変更します。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/migration-dms-cutover-apps.png)

### <a name="step-4b-migrate-the-database-mysql-workbench"></a>手順 4b: データベースの移行 (MySQL Workbench)

1. [MySQL Workbench の前提条件を確認してダウンロード](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)します。
2. [インストールの指示](https://dev.mysql.com/doc/workbench/en/wb-installing.html)に従って、MySQL Workbench for Windows をインストールします。 インストール先のマシンは、OSTICKETMYSQL VM と、インターネット経由で Azure にアクセス可能である必要があります。
3. MySQL Workbench で、OSTICKETMYSQL への MySQL 接続を作成します。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. データベースを **osticket** として、ローカルの自己完結型ファイルにエクスポートします。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. データベースがローカルにバックアップされたら、Azure Database for MySQL インスタンスへの接続を作成します。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. これで、自己完結型ファイルから、Azure Database for MySQL インスタンス内にあるデータベースをインポート (復元) できます。 このインスタンスのために新しいスキーマ (osticket) が作成されます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. データが復元されると Workbench を使用してクエリを実行でき、Azure Portal に表示されます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. 最後に、Web アプリ上のデータベース情報を更新する必要があります。 MySQL インスタンスで、 **[接続文字列]** を開きます。

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. 文字列の一覧で、Web アプリの設定を見つけ、それらを選択してコピーします。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. メモ帳ウィンドウを開き、新しいファイルに文字列を貼り付け、osticket データベース、MySQL インスタンス、および資格情報の設定と一致するように文字列を更新します。

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Azure portal で MySQL インスタンスの**概要**からサーバー名とのログインを確認できます。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)

## <a name="step-5-set-up-github"></a>手順 5:GitHub を設定する

Contoso 管理者は、新しいプライベート GitHub リポジトリを作成し、Azure Database for MySQL の osTicket データベースへの接続を設定します。 次に、Web アプリを Azure App Service に読み込みます。

1. OsTicket ソフトウェアのパブリック GitHub リポジトリを参照し、Contoso GitHub アカウントにフォークします。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. フォークしたら、**include** フォルダーに移動し、**ost-config.php** ファイルを検索します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)

3. ブラウザーでファイルを開いて編集します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. エディターで、データベースの詳細 (具体的には **DBHOST** と **DBUSER** のもの) を更新します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. 次に、変更をコミットします。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. 各 Web アプリ (**osticket-eus2** と **osticket-cus**) に対して、Azure portal で **[アプリケーション設定]** を変更します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. **osticket** という名前で接続文字列を入力し、メモ帳から**領域の値**に文字列をコピーします。 文字列の横のドロップダウン リストから **[MySQL]** を選択し、設定を保存します。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>手順 6:Web アプリを構成する

移行プロセスの最後のステップとして、Contoso 管理者は、Web アプリと osTicket Web サイトを構成します。

1. プライマリ Web アプリ (**osticket-eus2**) で **[デプロイ オプション]** を開き、ソースを **[GitHub]** に設定します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. デプロイ オプションを選択します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. オプションを設定すると、その構成が Azure Portal に保留中として表示されます。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. 構成が更新され、GitHub から Azure App Service を実行中の Docket コンテナーに osTicket Web アプリが読み込まれ、サイトがアクティブとして表示されます。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. セカンダリ Web アプリ (**osticket-cus**) に対して、上記のステップを繰り返します。
6. サイトが構成されたら、Traffic Manager プロファイルを使用してアクセスできます。 DNS 名は、osTicket アプリの新しい場所になります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)

7. Contoso では、覚えやすい DNS 名を求めています。 Traffic Manager の名前を指すエイリアス レコード (CNAME) `osticket.contoso.com` をドメイン コントローラーの DNS 内に作成します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. カスタム ホスト名を許可するように **osticket-eus2** と **osticket-cus** Web アプリの両方を構成します。

    ![アプリの構成](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>自動スケーリングを設定する

最後に、アプリの自動スケーリングを設定します。 これにより、エージェントがアプリを使用するときに、ビジネス ニーズに応じてアプリのインスタンスが増減することが保証されます。

1. App Service **APP-SRV-EUS2** で、 **[スケール ユニット]** を開きます。
2. 現在のインスタンスの CPU の割合が 10 分間 70% を超える場合はインスタンス数を 1 つ増加させるという単一のルールを持つ新しい自動スケーリング設定を構成します。

    ![自動スケール](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. 同じ設定を **APP-SRV-CUS** でも構成して、アプリがセカンダリ リージョンにフェールオーバーした場合に、同じ動作が適用されるようにします。 唯一の違いは、これは目的がフェールオーバーのみであるため、既定のインスタンスを 1 に設定することです。

   ![自動スケール](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了すると、osTicket アプリは、プライベート GitHub リポジトリを使用して継続的デリバリーを行う Azure App Service Web アプリで実行されるようにリファクタリングされます。 アプリが 2 つのリージョンで実行されることで、回復性が向上します。 PaaS プラットフォームへの移行後、OsTicket データベースは、MySQL 用の Azure データベースで実行されます。

クリーン アップのために、次の手順を実行する必要があります。

- VMware VM を vCenter インベントリから削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、新しい場所と IP アドレスを示します。
- こうしたオンプレミスの VM と対話しているリソースがないか確認し、新しい構成が反映されるように、関連する設定やドキュメントをすべて更新します。
- アプリが起動していることを追跡するために、osticket-trafficmanager.net URL をポイントするように監視を再構成します。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>Security

Contoso のセキュリティ チームは、アプリを再調査して、セキュリティの問題を特定します。 Contoso は、OsTicket アプリと MySQL データベース インスタンス間の通信が SSL 用に構成されていないことを識別しました。 データベース トラフィックをハッキングできないようにするため、これを行う必要があります。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/mysql/howto-configure-ssl)。

### <a name="backups"></a>バックアップ

- OsTicket web アプリには状態データが含まれていないため、バックアップは必要ありません。
- データベースのバックアップを構成する必要はありません。 Azure Database for MySQL は、サーバーのバックアップを自動的に作成して保存します。 彼らはデータベースのために geo 冗長性を使用することを選択したため、このデータベースは耐障害性があり、運用準備ができています。 バックアップを使用すると、サーバーを特定の時点に復元できます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/mysql/concepts-backup)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- PaaS のデプロイにライセンスの問題はありません。
- Contoso は [Azure Cost Management](https://azure.microsoft.com/services/cost-management) を使用して、IT リーダーが定めた予算内に確実に収まるようにします。
