---
title: ゲスト構成ポリシー
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: ゲスト構成ポリシー
author: BrianBlanchard
ms.author: brblanch
ms.date: 05/10/2019
ms.topic: article
ms.service: cloud-adoption-framework
ms.subservice: operate
ms.openlocfilehash: c43d07c6cfdea0152559d7a13fec7dde148b1530
ms.sourcegitcommit: d19e026d119fbe221a78b10225230da8b9666fe1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71221570"
---
# <a name="guest-configuration-policy"></a>ゲスト構成ポリシー

Azure Policy の[ゲスト構成](/azure/governance/policy/concepts/guest-configuration)拡張機能では、仮想マシン内の構成設定を監査することができます。 ゲスト構成は、現在 Azure VM 上でのみサポートされます。

Azure Policy ポータル ページでカテゴリ "ゲスト構成" を検索すれば、ゲスト構成ポリシーの一覧が見つかります。 PowerShell ウィンドウでこのコマンドレットを実行しても、この一覧を見つけることができます。

```powershell
Get-AzPolicySetDefinition | where-object {$_.Properties.metadata.category -eq "Guest Configuration"}
```

> [!NOTE]
> ゲスト構成機能は、追加のポリシー セットをサポートするために定期的に更新されます。 新しいサポート対象ポリシーを定期的に確認し、ご自身のニーズに役立つかどうかを評価してください。

<!-- TODO: Update these links when available. 

By default, we recommend enabling the following policies:

- [Preview]: Audit to verify password security settings are set correctly inside Linux and Windows machines.
- Audit to verify that certificates are not nearing expiration on Windows VMs.

-->

## <a name="deployment"></a>Deployment

次の PowerShell スクリプトの例を使用して、これらのポリシーをデプロイできます。

- Windows および Linux コンピューターのパスワード セキュリティ設定が正しく設定されていることを確認します。
- Windows VM で証明書の有効期限が近づいていないことを確認します。

 このスクリプトを実行する前に、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.1.0) コマンドレットを使用してサインインする必要があります。 スクリプトを起動するときには、ポリシーを適用するサブスクリプションの名前を指定する必要があります。

```powershell

    #Assign Guest Configuration policy.
    param (
        [Parameter(Mandatory=$true)]
        [string]$SubscriptionName
    )

    $Subscription = Get-AzSubscription -SubscriptionName $SubscriptionName
    $scope = "/subscriptions/" + $Subscription.Id

    $PasswordPolicy = Get-AzPolicySetDefinition -Name "3fa7cbf5-c0a4-4a59-85a5-cca4d996d5a6"
    $CertExpirePolicy = Get-AzPolicySetDefinition -Name "b6f5e05c-0aaa-4337-8dd4-357c399d12ae"

    New-AzPolicyAssignment -Name "PasswordPolicy" -DisplayName "[Preview]: Audit that password security settings are set correctly inside Linux and Windows machines" -Scope $scope -PolicySetDefinition $PasswordPolicy -AssignIdentity -Location eastus

    New-AzPolicyAssignment -Name "CertExpirePolicy" -DisplayName "[Preview]: Audit that certificates are not expiring on Windows VMs" -Scope $scope -PolicySetDefinition $CertExpirePolicy -AssignIdentity -Location eastus

```

## <a name="next-steps"></a>次の手順

重要なファイル、サービス、ソフトウェア、レジストリの変更に対して[変更の追跡とアラートを有効にする](./enable-tracking-alerting.md)方法をご確認ください。

> [!div class="nextstepaction"]
> [重要な変更の追跡とアラートを有効にする](./enable-tracking-alerting.md)