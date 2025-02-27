---
title: サンプルの Azure インフラストラクチャによるチュートリアル | Microsoft Docs
description: Azure でのサンプルのインフラストラクチャのデプロイに関する主要な設計と実装のガイドラインについて説明します。
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddbaed6704fd32f7fd4fe5a790424cbf829d2f1c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60540404"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Windows VM 用の サンプルの Azure インフラストラクチャによるチュートリアル
この記事では、サンプルのアプリケーション インフラストラクチャの構築について説明します。 ここでは、名前付け規則、可用性セット、仮想ネットワーク、およびロード バランサーに関するガイドラインと意思決定のすべてをまとめたシンプルなオンライン ストア向けインフラストラクチャを設計し、実際に仮想マシン (VM) をデプロイする方法について説明します。

## <a name="example-workload"></a>ワークロードの例
Adventure Works Cycles では、以下の項目で構成されるオンライン ストア アプリケーションを Azure に構築する必要があります。

* Web 層でクライアント フロントエンドを実行する 2 台の IIS サーバー
* アプリケーション層でデータと注文を処理する 2 台の IIS サーバー
* データベース層で製品のデータと注文を格納するための AlwaysOn 可用性グループ (2 つの SQL Server とマジョリティ ノード監視) を含む 2 つの Microsoft SQL Server インスタンス
* 認証層の顧客アカウントとサプライヤー用の 2 つの Active Directory ドメイン コントローラー
* すべてのサーバーは次の 2 つのサブネットに配置されています。
  * フロント エンド サブネット: Web サーバー用 
  * バック エンド サブネット: アプリケーション サーバー、SQL クラスター、およびドメイン コントローラー用

![アプリケーション インフラストラクチャのさまざまなレベルの図](./media/infrastructure-example/example-tiers.png)

セキュリティで保護された着信 Web トラフィックについては、顧客がオンライン ストアを閲覧する際に Web サーバー間で負荷を分散する必要があります。 Web サーバーからの HTTP 要求の形式での注文処理トラフィックについては、アプリケーション サーバー間で負荷を分散する必要があります。 さらに、インフラストラクチャは高可用性対応で設計する必要があります。

結果として得られる設計には、次のものが組み込まれる必要があります。

* Azure サブスクリプションとアカウント
* 単一リソース グループ
* Azure Managed Disks
* 2 つのサブネットを含む仮想ネットワーク
* 似たロールを持つ VM のための可用性セット
* 仮想マシン

これらすべてで、次の名前付け規則を使用します。

* Adventure Works Cycles は、プレフィックスとして **[IT ワークロード]-[場所]-[Azure リソース]** を使用します。
  * たとえば、"**azos**" (Azure Online Store) は IT ワークロード名であり、"**use**" (米国東部 2) は場所です。
* 仮想ネットワークは、AZOS-USE-VN **[番号]** を使用します。
* 可用性セットは、azos-use-as- **[ロール]** を使用します。
* 仮想マシン名は、azos-use-vm- **[仮想マシン名]** を使用します。

## <a name="azure-subscriptions-and-accounts"></a>Azure サブスクリプションとアカウント
Adventure Works Cycles は、Adventure Works Enterprise Subscription という名前のエンタープライズ サブスクリプションを使用して、この IT ワークロードに対する課金を行います。

## <a name="storage"></a>Storage
Adventure Works Cycles では、Azure Managed Disks を使用する必要があると決定しました。 VM を作成するときに、使用可能な両方のストレージ層が使用されます。

* Web サーバー、アプリケーション サーバー、ドメイン コントローラーとそれらのデータ ディスクの **Standard Storage**。
* SQL Server VM とそのデータ ディスクの **Premium Storage**。

## <a name="virtual-network-and-subnets"></a>仮想ネットワークとサブネット
仮想ネットワークを Adventure Work Cycles のオンプレミス ネットワークに常時接続している必要はないので、Adventure Work Cycles はクラウド専用の仮想ネットワークに決定しました。

Contoso は、Azure ポータルを使用して次の設定でクラウド専用仮想ネットワークを作成しました。

* 名前:AZOS-USE-VN01
* 場所:米国東部 2
* 仮想ネットワークのアドレス空間:10.0.0.0/8
* 1 番目のサブネット:
  * 名前:FrontEnd
  * アドレス空間:10.0.1.0/24
* 2 番目のサブネット:
  * 名前:BackEnd
  * アドレス空間:10.0.2.0/24

## <a name="availability-sets"></a>可用性セット
オンライン ストアの 4 つの階層すべてで高可用性を維持するため、Adventure Works Cycles は次の 4 つの可用性セットを採用しました。

* **azos-use-as-web** : Web サーバー用
* **azos-use-as-app** : アプリケーション サーバー用
* **azos-use-as-sql** : SQL Server 用
* **azos-use-as-dc** : ドメイン コントローラー用

## <a name="virtual-machines"></a>仮想マシン
Adventure Works Cycles は、各 Azure VM に対して次の名前を決定しました。

* **azos-use-vm-web01** : 1 番目の Web サーバー用
* **azos-use-vm-web02** : 2 番目の Web サーバー用
* **azos-use-vm-app01** : 1 番目のアプリケーション サーバー用
* **azos-use-vm-app02** : 2 番目のアプリケーション サーバー用
* **azos-use-vm-sql01** : クラスター内の 1 番目の SQL Server サーバー用
* **azos-use-vm-sql02** : クラスター内の 2 番目の SQL Server サーバー用
* **azos-use-vm-dc01** : 1 番目のドメイン コントローラー用
* **azos-use-vm-dc02** : 2 番目のドメイン コントローラー用

完成すると次のような構成になります。

![Azure にデプロイされた最終的なアプリケーション インフラストラクチャ](./media/infrastructure-example/example-config.png)

この構成には次のものが含まれます。

* クラウド専用仮想ネットワークと 2 つのサブネット (FrontEnd と BackEnd)
* Standard ディスクと Premium ディスクの両方のを含む Azure Managed Disks
* オンライン ストアの各階層に 1 つずつ、計 4 つの可用性セット
* 4 つの階層用の仮想マシン
* インターネットから Web サーバーへの HTTPS ベースの Web トラフィック用の外部負荷分散セット
* Web サーバーからアプリケーション サーバーへの暗号化されていない Web トラフィック用の内部負荷分散セット
* 単一リソース グループ
