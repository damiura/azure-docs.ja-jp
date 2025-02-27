---
title: Azure SQL Database のセキュリティ属性
description: Azure SQL Database を評価するためのセキュリティ属性のチェックリスト
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c495456a5a3295abe5460ff6b5586e41fab2d95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66001033"
---
# <a name="security-attributes-for-azure-sql-database"></a>Azure SQL Database のセキュリティ属性

この記事では、Azure SQL Database に組み込まれている一般的なセキュリティ属性について説明します。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Azure SQL Database には、[単一データベース](sql-database-single-index.yml)と[マネージド インスタンス](sql-database-managed-instance.md)の両方が含まれます。 以下のエントリは、明記されている場合を除き、両方のオファリングに適用されます。

## <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化:<ul><li>サーバー側暗号化</li><li>ユーザーが管理するキーによるサーバー側暗号化</li><li>その他の暗号化機能 (クライアント側や常に暗号化など)</ul>| はい | [Always Encrypted](sql-database-always-encrypted.md) に関する記事で説明されているように、"使用時の暗号化" と呼ばれます。 サービス側暗号化では、[Transparent Data Encryption](transparent-data-encryption-azure-sql.md) (TDE) が使われます。|
| 転送中の暗号化:<ul><li>ExpressRoute の暗号化</li><li>VNet 内の暗号化</li><li>VNet 間暗号化</ul>| はい | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | サービス マネージド キーとカスタマー マネージド キーの処理がどちらも提供されます (後者は を[Azure Key Vault](../key-vault/index.yml) によって)。 |
| 列レベルの暗号化 (Azure Data Services)| はい | [Always Encrypted](sql-database-always-encrypted.md) により。 |
| API 呼び出しの暗号化| はい | HTTPS/SSL を使用。 |

## <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | [単一データベース](sql-database-single-index.yml)のみに適用。 |
| VNet インジェクションのサポート| はい | [マネージド インスタンス](sql-database-managed-instance.md)のみに適用。 |
| ネットワークの分離とファイアウォールのサポート| はい | データベース レベルとサーバー レベル両方でのファイアウォール、[マネージド インスタンス](sql-database-managed-instance.md)のみに対するネットワーク分離 |
| 強制トンネリングのサポート| はい | [Azure ExpressRoute](../expressroute/index.yml) VPN による[マネージド インスタンス](sql-database-managed-instance.md) |

## <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [SQL 監査](sql-database-auditing.md)を介した [Azure Event Hubs](../event-hubs/index.yml) の統合により、Imperva (SecureSphere) からのサード パーティーの SIEM ソリューションもサポートされます。 |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | Azure Active Directory。 |
| Authorization| はい |  |


## <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | 一部のイベントについてのみ、はい。 |
| データ プレーンのログ記録と監査 | はい | [SQL 監査](sql-database-auditing.md)により。 |

## <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| いいえ  | | 

## <a name="additional-security-attributes-for-sql-database"></a>SQL Database の追加セキュリティ属性

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 予防: 脆弱性評価 | はい | 「[SQL の脆弱性評価サービスは、データベースの脆弱性を特定するのに役に立ちます](sql-vulnerability-assessment.md)」をご覧ください。 |
| 予防: データの検出と分類  | はい | 「[Azure SQL Database および SQL Data Warehouse のデータの検出と分類](sql-database-data-discovery-and-classification.md)」をご覧ください。 |
| 検出: 脅威の検出 | はい | 「[Azure SQL Database の Advanced Threat Protection](sql-database-threat-detection-overview.md)」をご覧ください。 |
