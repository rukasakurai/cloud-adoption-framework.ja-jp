---
title: 推奨されるロールベースのアクセス制御
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 推奨されるロールベースのアクセス制御
author: rotycenh
ms.author: brblanch
ms.date: 11/28/2018
ms.topic: guide
ms.service: cloud-adoption-framework
ms.subservice: ready
manager: BrianBlanchard
tags: azure-resource-manager
ms.custom: virtual-network
ms.openlocfilehash: 897b6f3f5d3c506cc79050dd3453e30b677382b1
ms.sourcegitcommit: a26c27ed72ac89198231ec4b11917a20d03bd222
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70839153"
---
# <a name="role-based-access-control"></a>ロールベースのアクセス制御

グループベースのアクセス権および特権を使用することをお勧めします。 個々のユーザーではなくグループを処理すれば、アクセス ポリシーの保守が簡素化され、チーム間に一貫したアクセス管理が提供され、構成エラーが削減されます。 適切なグループにユーザーを割り当てたり、グループからユーザーを削除することで、特定のユーザーの権限を最新に保つことができます。 Azure [ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/overview) では、ユーザー ロールごとに整理されているリソースのアクセスを詳細に管理できます。

ID およびセキュリティ戦略の一部として推奨される RBAC の方法の概要については、「[Azure の ID 管理とアクセス制御セキュリティのベスト プラクティス](/azure/security/azure-security-identity-management-best-practices#use-role-based-access-control)」を参照してください。

## <a name="overview-of-role-based-access-control"></a>ロールベースのアクセス制御の概要

[ロールベースのアクセス制御](/azure/role-based-access-control/overview)を使用すると、チーム内の職務を分離し、特定の Azure Active Directory (Azure AD) ユーザー、グループ、サービス プリンシパル、またはマネージド ID が自分の業務を実行するために必要なアクセス権のみを付与することができます。 すべてのユーザーに Azure サブスクリプションまたはリソースへの無制限のアクセス権を付与するのではなく、リソースのセットごとにアクセス権を制限することができます。

[RBAC ロール定義](/azure/role-based-access-control/role-definitions)には、そのロールに割り当てられているユーザーまたはグループに対して許可されている操作または許可されていない操作が一覧表示されています。 ロールの[スコープ](/azure/role-based-access-control/overview#scope)は、これらの定義されたアクセス許可がどのリソースに適用されるかを指定します。 スコープは、複数のレベル (管理グループ、サブスクリプション、リソース グループ、またはリソース) で指定できます。 スコープは親子関係で構造化されています。

![RBAC のスコープ階層](./images/rbac-scope.png)

ユーザーとグループを特定のロールに割り当て、ロールをスコープに割り当てる詳細な手順については、[RBAC を使用して Azure リソースへのアクセスを管理する](/azure/role-based-access-control/role-assignments-portal)ことに関する記事を参照してください。

アクセス制御戦略を計画するときは、ユーザーが作業を実行するために必要な権限のみを付与する最小特権のアクセス モデルを使用します。 次の図は、このアプローチを使用して RBAC を使用するための推奨パターンを示しています。

![RBAC を使用するための推奨パターン](./images/rbac-least-privilege.png)

> [!NOTE]
> 定義するアクセス許可が具体的または詳細なものになるほど、アクセス制御が複雑になり、管理が困難になる可能性が高くなります。 この傾向は、クラウド資産の規模が大きくなるにつれて顕著になります。 リソース固有のアクセス許可は使わないようにします。 代わりに、エンタープライズ全体のアクセス制御には[管理グループを使用し](/azure/governance/management-groups)、サブスクリプション内のアクセス制御には[リソース グループ](/azure/azure-resource-manager/resource-group-overview#resource-groups)を使用します。 また、ユーザー固有のアクセス許可は使わないようにします。 代わりに、[Azure AD 内のグループ](/azure/active-directory/fundamentals/active-directory-manage-groups)にアクセス権を割り当てます。

## <a name="using-built-in-rbac-roles"></a>組み込みの RBAC ロールの使用

Azure では多くの組み込みのロール定義が用意されており、アクセス権を提供する次の 3 つのコア ロールがあります。

- [所有者](/azure/role-based-access-control/built-in-roles#owner)ロールは、リソースへのアクセスを含め、すべてを管理できます。
- [共同作業者](/azure/role-based-access-control/built-in-roles#contributor)ロールは、リソースへのアクセス以外のすべてを管理できます。
- [閲覧者](/azure/role-based-access-control/built-in-roles#reader)ロールは、すべてを表示できますが、変更はできません。

これらのコア アクセス レベルから開始して、組み込みロールを追加することによって、特定のリソースの種類または Azure 機能にアクセスするためのより詳細なコントロールが提供されます。 たとえば、仮想マシンへのアクセスは、次の組み込みロールを使用して管理できます。

- [仮想マシンの管理者ログイン](/azure/role-based-access-control/built-in-roles#virtual-machine-administrator-login) ロールを使用すると、ポータルで仮想マシンを表示し、"_管理者_" としてサインインできます。
- [仮想マシン共同作業者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)ロールは仮想マシンを管理できますが、それらにアクセスしたり、接続先の仮想ネットワークまたはストレージ アカウントにアクセスすることはできません。
- [仮想マシンのユーザー ログイン](/azure/role-based-access-control/built-in-roles#virtual-machine-user-login) ロールを使用すると、ポータルで仮想マシンを表示し、通常ユーザーとしてサインインできます。

組み込みロールを使用して、特定の機能へのアクセスを管理する別の例については、「[事業単位、環境、プロジェクトをまたいでコストを追跡する](./track-costs.md#provide-the-right-level-of-cost-access)」の、コスト追跡機能へのアクセスを制御することに関する説明を参照してください。

使用可能な組み込みロールの完全な一覧については、「[Azure リソースの組み込みロール](/azure/role-based-access-control/built-in-roles)」をご覧ください。

## <a name="using-custom-roles"></a>カスタム ロールの使用

Azure に組み込まれたロールはさまざまなアクセス制御シナリオをサポートしますが、組織やチームのすべてのニーズを満たせるわけではありません。 たとえば、仮想マシンと Azure SQL Database リソースの管理を担当する単一のユーザー グループがある場合、必要なアクセス制御の管理を最適化するためにカスタム ロールを作成することができます。

Azure RBAC のドキュメントには、[ロール定義の機能](/azure/role-based-access-control/role-definitions)の詳細と併せて、[カスタム ロールの作成](/azure/role-based-access-control/custom-roles)に関する手順が記載されています。

## <a name="separation-of-responsibilities-and-roles-for-large-organizations"></a>大規模組織における責任とロールの分離

RBAC を使用すると、組織は大規模なクラウド資産内のさまざまな管理タスクにさまざまなチームを割り当てることができます。 これにより、中央の IT チームがコア アクセスとセキュリティ機能を制御しながら、ソフトウェア開発者や他のチームが、特定のワークロードやリソースのグループを大きく制御できるようにすることが可能です。

ほとんどのクラウド環境は、複数のロールを使用し、これらのロール間での責任の分離を強調するアクセス制御戦略からも恩恵を受けることができます。 このアプローチでは、リソースまたはインフラストラクチャを大幅に変更するには、完了するまでに複数のロールが関与する必要があり、複数のユーザーが変更を確認して承認する必要があります。 この責任の分離により、他のチーム メンバーが知らないうちに、1人の人物が機密データにアクセスしたり、脆弱性を導入したりする能力が制限されます。

次の表は、IT の責任を別々のカスタム ロールに分割するための一般的なパターンを示しています。

<!-- markdownlint-disable MD033 -->

| Group | 一般的なロール名 | 責任 |
| --- | --- | --- |
| セキュリティ運用担当者 | SecOps | 全般的なセキュリティ監視を行います。<br/><br/> 保存時の暗号化などのセキュリティ ポリシーを確立して適用します。<br/><br/> 暗号化キーを管理します。<br/><br/> ファイアウォール規則を管理します。 |
| ネットワーク運用担当者 | NetOps | ルートやピアリングなど、仮想ネットワーク内のネットワーク構成および運用を管理します。 |
| システム運用担当者 | SysOps | コンピューティングおよびストレージ インフラストラクチャのオプションを指定し、デプロイ済みのリソースを保守します。 |
| 開発、テスト、および運用担当者 | DevOps | ワークロード機能とアプリケーションを構築してデプロイします。<br/><br/> サービスレベル アグリーメント (SLA) やその他の品質基準を満たすように機能およびアプリケーションを運用します。 |

<!-- markdownlint-enable MD033 -->

これらの標準ロール内のアクションおよびアクセス許可の内訳は、これらのロールがさまざまなレベルでさまざまなユーザーによって実行されたとしても、多くの場合はアプリケーション、サブスクリプション、またはすべてのクラウド資産について同じです。 したがって、環境内のさまざまなスコープに適用するための共通の RBAC ロール定義のセットを作成できます。 その後、ユーザーおよびグループには共通のロールを割り当てることができますが、それらはユーザーおよびグループが管理する責任があるリソース、リソース グループ、サブスクリプション、または管理グループのスコープのみが対象となります。

たとえば、複数のサブスクリプションを含む[ハブ アンド スポーク ネットワーク構成](./hub-spoke-network-topology.md)では、ハブとすべてのワークロード スポークに対して共通のロール定義のセットを使用することができます。 ハブ サブスクリプションの NetOps ロールは、すべてのワークロードによって使用される共有サービスのためのネットワークの保守を担当している組織の中央の IT スタッフ メンバーに割り当てることができます。 ワークロード スポーク サブスクリプションの NetOps ロールを、その特定のワークロード チームのメンバーに割り当てることで、ワークロード要件を最も良くサポートするように、そのサブスクリプション内でネットワークを構成することができます。 同じロール定義が両方に使用されますが、スコープベースの割り当てにより、ユーザーは自分の業務を実行するために必要なアクセス権のみを持つようになります。