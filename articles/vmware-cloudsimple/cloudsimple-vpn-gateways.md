---
title: CloudSimple による VMware ソリューションでの VPN ゲートウェイ - Azure
description: CloudSimple サイト間 VPN とポイント対サイト VPN の概念について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c9689a468e8784eb4ec3590011e02a37d92d6b9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083393"
---
# <a name="vpn-gateways-overview"></a>VPN ゲートウェイの概要

VPN ゲートウェイは、オンプレミスの場所の CloudSimple 領域ネットワーク、またはパブリック インターネット経由のコンピューター間で、暗号化されたトラフィックを送信するために使用されます。  各リージョンでは、1 つだけの VPN ゲートウェイを持つことができます。 ただし、同一の VPN ゲートウェイに対して複数の接続を作成することができます。 同一の VPN ゲートウェイへの複数の接続を作成する場合、利用できるゲートウェイ帯域幅はすべての VPN トンネルによって共有されます。

CloudSimple には、2 種類の VPN ゲートウェイが用意されています。

* サイト間 VPN ゲートウェイ
* ポイント対サイト VPN ゲートウェイ

## <a name="site-to-site-vpn-gateway"></a>サイト間 VPN ゲートウェイ

サイト間 VPN ゲートウェイは、CloudSimple 領域ネットワークとオンプレミスのデータ センターの間で暗号化されたトラフィックの送信に使用されます。 この接続を使用して、オンプレミス ネットワークと CloudSimple リージョン ネットワークの間のネットワーク トラフィックのための、サブネット/CIDR の範囲を定義します。

VPN ゲートウェイでは、プライベート クラウド上のオンプレミスからのサービス、およびオンプレミス ネットワークからのプライベート クラウド上のサービスを利用できます。  CloudSimple は、オンプレミス ネットワークからの接続を確立するための、ポリシー ベースの VPN サーバーを提供します。

サイト間 VPN のユース ケースは次のとおりです。

* オンプレミス ネットワーク内の任意のワークステーションからの、プライベート クラウド vCenter のアクセシビリティ。
* VCenter ID ソースとして、オンプレミスの Active Directory Domain Services を使用します。
* オンプレミス リソースからプライベート クラウド vCenter への、VM テンプレート、ISO イメージ、その他のファイルの簡易な転送。
* プライベート クラウドで実行されているワークロードへの、オンプレミス ネットワークからのアクセシビリティ。

![サイト間 VPN 接続の図](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>暗号化パラメーター

サイト間 VPN 接続では、セキュリティで保護された接続を確立するために、次の既定の暗号化パラメーターを使用します。  オンプレミス VPN デバイスからの接続を作成するときには、パラメーターが一致している必要があります。

サイト間 VPN 接続では、セキュリティで保護された接続を確立するために、次の既定の暗号化パラメーターを使用します。  オンプレミス VPN デバイスからの接続を作成する場合は、オンプレミス VPN ゲートウェイでサポートされている次のパラメーターのいずれかを使用します。

#### <a name="phase-1-proposals"></a>フェーズ 1 の提案

| パラメーター | 提案 1 | 提案 2 | 提案 3 |
|-----------|------------|------------|------------|
| IKE のバージョン | IKEv1 | IKEv1 | IKEv1 |
| 暗号化 | AES 128 | AES 256 | AES 256 |
| ハッシュ アルゴリズム| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman グループ (DH グループ) | 2 | 2 | 2 |
| 有効期間 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| データ サイズ | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>フェーズ 2 の提案 

| パラメーター | 提案 1 | 提案 2 | 提案 3 |
|-----------|------------|------------|------------|
| 暗号化 | AES 128 | AES 256 | AES 256 |
| ハッシュ アルゴリズム| SHA 256 | SHA 256 | SHA 1 |
| Perfect Forward Secrecy グループ (PFS グループ) | なし | なし | なし |
| 有効期間 | 1,800 秒 | 1,800 秒 | 1,800 秒 |
| データ サイズ | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>ポイント対サイト VPN ゲートウェイ

ポイント対サイト VPN は、CloudSimple 領域ネットワークとクライアント コンピューターの間で暗号化されたトラフィックの送信に使用されます。  ポイント対サイト VPN は、プライベート クラウド vCenter およびワークロード VM を含む、プライベート クラウド ネットワークにアクセスする最も簡単な方法です。  プライベート クラウドをリモートで接続している場合は、ポイント対サイト VPN 接続を使用します。

## <a name="next-steps"></a>次の手順

* [VPN ゲートウェイの設定](https://docs.azure.cloudsimple.com/vpn-gateway/)