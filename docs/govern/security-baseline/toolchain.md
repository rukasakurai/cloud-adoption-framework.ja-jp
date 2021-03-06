---
title: Azure でのセキュリティ ベースライン ツール
description: セキュリティ ベースライン規範をサポートするポリシーとプロセスを成熟させるのに、Azure ネイティブ ツールがどのように役立つかについて説明します。
author: BrianBlanchard
ms.author: brblanch
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: cloud-adoption-framework
ms.subservice: govern
ms.custom: governance
ms.openlocfilehash: 51e0a253a7e66f70ec440dd7be969f324afb795c
ms.sourcegitcommit: 60d8b863d431b5d7c005f2f14488620b6c4c49be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83217678"
---
# <a name="security-baseline-tools-in-azure"></a>Azure でのセキュリティ ベースライン ツール

[セキュリティ ベースライン規範](./index.md)は、[クラウド ガバナンスの 5 つの規範](../governance-disciplines.md)のうちの 1 つです。 この規範は、ネットワーク、資産、および最も重要なものとしてクラウド プロバイダーのソリューションに存在するデータを保護するポリシーを確立する方法に重点を置いています。 クラウド ガバナンスの 5 つの規範のうち、セキュリティ ベースライン規範にはデジタル資産とデータの分類が含まれます。 また、リスク、ビジネスの許容度、およびデータ、資産、ネットワークのセキュリティに関連付けられた軽減戦略のドキュメントも含まれます。 技術的な観点から、この規範は、[暗号化](../../decision-guides/encryption/index.md)、[ネットワーク要件](../../decision-guides/software-defined-network/index.md)、[ハイブリッド ID 戦略](../../decision-guides/identity/index.md)、および[リソース グループ](../../decision-guides/resource-consistency/index.md)全体でセキュリティ ポリシーの[適用を自動化](../../decision-guides/policy-enforcement/index.md)するツールに関する決定にも関わります。

Azure ツールの次の一覧は、この規範をサポートするポリシーとプロセスを成熟させるのに役立ちます。

| ツール | [Azure portal](https://azure.microsoft.com/features/azure-portal) と [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)  | [Azure Key Vault](https://docs.microsoft.com/azure/key-vault)  | [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) | [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) | [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) | [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) |
|------------------------------------------------------------|---------------------------------|-----------------|----------|--------------|-----------------------|---------------|
| アクセスの制御をリソースとリソースの作成に適用する   | はい                             | いいえ              | はい      | いいえ           | いいえ                    | いいえ            |
| 仮想ネットワークをセキュリティで保護する                                    | はい                             | いいえ              | いいえ       | はい          | いいえ                    | いいえ            |
| 仮想ドライブを暗号化する                                     | いいえ                              | はい             | いいえ       | いいえ           | いいえ                    | いいえ            |
| PaaS ストレージとデータベースを暗号化する                         | いいえ                              | はい             | いいえ       | いいえ           | いいえ                    | いいえ            |
| ハイブリッドの ID サービスを管理する                            | いいえ                              | いいえ              | はい      | いいえ           | いいえ                    | いいえ            |
| リソースの許可される型を制限する                         | いいえ                              | いいえ              | いいえ       | はい          | いいえ                    | いいえ            |
| geo リージョンの制限を適用する                          | いいえ                              | いいえ              | いいえ       | はい          | いいえ                    | いいえ            |
| ネットワークとリソースのセキュリティ正常性を監視する          | いいえ                              | いいえ              | いいえ       | いいえ           | はい                   | はい           |
| 悪意のあるアクティビティを検出する                                  | いいえ                              | いいえ              | いいえ       | いいえ           | はい                   | はい           |
| 事前に脆弱性を検出する                        | いいえ                              | いいえ              | いいえ       | いいえ           | はい                   | いいえ            |
| バックアップとディザスター リカバリーを構成する                     | はい                             | いいえ              | いいえ       | いいえ           | いいえ                    | いいえ            |

セキュリティ ツールとサービスの完全なリストについては、「[Azure で利用できるセキュリティ サービスとテクノロジ](https://docs.microsoft.com/azure/security/fundamentals/services-technologies)」を参照してください。

お客様は、一般にサードパーティのツールを使用して、セキュリティ ベースライン規範のアクティビティを有効にします。 詳細については、「[Azure Security Center でのセキュリティ ソリューションの統合](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)」の記事を参照してください。

セキュリティ ツールに加えて、[Microsoft Trust Center](https://www.microsoft.com/microsoft-365/business/compliance-solutions#office-KeyMessages-k3j63yo) には、広範なガイダンス、レポート、および移行計画のプロセスの一環として、リスク評価の実行に役立つ関連するドキュメントが含まれます。
