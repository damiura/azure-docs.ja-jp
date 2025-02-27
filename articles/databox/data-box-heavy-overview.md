---
title: Microsoft Azure Data Box Heavy の概要 | Microsoft Docs in data
description: 大量のデータを Azure に転送できるハイブリッド ソリューションである Azure Data Box について説明します
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: 0f71d9b4400041db50cb3e24940e922acde55edc
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991683"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Azure Data Box Heavy とは (プレビュー)

Azure Data Box Heavy では、信頼性が高く、迅速かつ安価な方法で、数百テラバイトのデータを Azure に送信できます。 1 PB のストレージ容量を持つ Data Box Heavy デバイスがお客様に出荷され、お客様がこのデバイスにデータを入力して Microsoft に返送することによって、データが Azure に転送されます。 デバイスは堅牢な筐体で保護され、転送中のデータはセキュリティで保護されます。

Data Box Heavy は現在、プレビュー段階です。 Azure portal からデバイスをリクエストするには、サインアップしてください。 データセンターでデバイスを受け取ったら、ローカル Web UI を使用して設定します。 データをサーバーからデバイスにコピーし、デバイスを Azure に返送します。 Azure データセンターで、お客様のデータがお客様の Azure Storage アカウントにアップロードされます。 お客様は Azure portal でエンドツーエンドのプロセス全体を追跡できます。


> [!IMPORTANT]
> - Data Box Heavy はプレビュー段階です。 このソリューションを展開する前に、[プレビューに関する Azure のサービス利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。
> - デバイスを要求するには、[Preview portal](https://aka.ms/azuredatabox) でサインアップします。
> - プレビュー期間中、Data Box Heavy は、米国および欧州連合のお客様に出荷できます。 詳細については、「[Region availability (利用可能なリージョン)](#region-availability)」をご覧ください。

## <a name="use-cases"></a>ユース ケース

Data Box Heavy は、ネットワーク接続では Azure にデータをアップロードするのに十分でない、数百テラバイト単位のデータ サイズに最適です。 データは 1 回だけ移動することも、定期的に移動することもできます。また、初期一括データ転送の後に定期的な転送を行うこともできます。 Data Box Heavy は、以下のようなさまざまなシナリオで、データ転送に使用できます。

 - **1 回限りの移行** - 大量のオンプレミス データを Azure に移動する場合。
     - オフライン テープのメディア ライブラリを Azure に移動し、オンライン メディア ライブラリを作成します。
     - VM ファーム、SQL Server、アプリケーションを Azure に移行します。
     - HDInsight を使用した詳細な分析やレポートのために、履歴データを Azure に移動します。

 - **初期一括転送** - Data Box Heavy (シード) を使用して初期一括転送が実行された後に、ネットワーク経由で増分転送を行う場合。
     - たとえば、Data Box Heavy とバックアップ ソリューション パートナーは、最初に大量の履歴バックアップを Azure に移動するために使用されます。 完了後のデータの増分は、ネットワーク経由で Azure ストレージに転送する。

 - **定期的なアップロード** - 定期的に生成される大量のデータを Azure に移動する必要がある場合。 たとえば、石油掘削装置や風力発電地帯でビデオ コンテンツが生成されるエネルギー探査でこれを実行します。

## <a name="benefits"></a>メリット

Data Box Heavy は、ネットワークにほとんどまたはまったく影響を与えずに大量のデータを Azure に移動することを目的としています。 このソリューションには次の利点があります。

- **速度** - Data Box Heavy は、高パフォーマンスの 40 Gbps ネットワーク インターフェイスを使用します。

- **セキュリティ** - Data Box Heavy には、デバイス、データ、サービスのセキュリティ保護が組み込まれています。
    - デバイスの筐体は堅牢で、不正開封防止ネジと不正開封防止ステッカーによってセキュリティ保護されています。
    - デバイスのデータは、AES 256 ビット暗号化によって常にセキュリティ保護されています。
    - デバイスは、Azure portal で提供されるパスワードでのみロックを解除できます。
    - このサービスは、Azure のセキュリティ機能によって保護されています。
    - データが Azure にアップロードされたら、NIST (アメリカ国立標準技術研究所) 800-88r1 規格に従って、デバイスのディスクが完全にワイプされます。


## <a name="features-and-specifications"></a>機能と仕様

このリリースの Data Box Heavy デバイスには、次の機能があります。

| 仕様                                          | 説明              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | 最大 500 ポンド                |
| Dimensions                                              | 幅:26 インチ 高さ:28 インチ 長さ:48 インチ |
| ラック スペース                                              | ラック マウント不可|
| 必要なケーブル                                         | 4 X 接地 120 V/10 A 電源コード (NEMA 5-15) 付属 <br> デバイスは最大 240 V 電源をサポートし、C-13 電源レセプタクルを備える <br> [Mellanox MCX314 A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) と互換性のあるネットワーク ケーブルを使用  |
|累乗                                                    | 両方のデバイス ノードで共有される 4 基の内蔵電源装置 (PSU)|
| ストレージの容量                                        | 最大 1 PB (ロー)、各 14 TB のディスク 70 台 <br> 使用可能な容量は 770 TB|
|ノードの数                                          | デバイスごとに 2 つの独立したノード (各 500 TB) |
| ノードあたりのネットワーク インターフェイス数                             | ノードあたり 4 つのネットワーク インターフェイス <br> MGMT、DATA3 <ul><li> 2 X 1 GbE インターフェイス </li><li> MGMT は管理用、ユーザー構成不可、初期セットアップに使用 </li><li> DATA3 はユーザー構成可能なデータ インターフェイス、既定では動的ホスト構成プロトコル (DHCP)</li><li>1 GbE ネットワーク インターフェイスは 10 GbE インターフェイスとしての構成も可能</li></ul>DATA1、DATA2 データ インターフェイス <ul><li>2 X 40 GbE インターフェイス </li><li> DHCP (既定) または静的でユーザー構成可能なデータ インターフェイス</li>|


## <a name="components"></a>コンポーネント

Data Box Heavy に含まれるコンポーネントを次に示します。

* **Data Box Heavy デバイス** - データを安全に保存する堅牢な外装を備えた物理的なデバイス。 このデバイスでは、770 TB のストレージ容量を使用できます。
    
* **Data Box サービス** - さまざまな地理的場所からアクセスできる Web インターフェイスから、Data Box Heavy デバイスを管理できる、Azure portal の拡張機能。 Data Box サービスを使用して Data Box Heavy デバイスを管理します。 サービス タスクには、注文の作成と管理、警告の表示と管理、共有の管理が含まれます。  

* **ローカル Web ユーザー インターフェイス** - デバイスの構成に使用する Web ベースの UI。この UI を使用して、ローカル ネットワークに接続し、デバイスを Data Box サービスに登録できます。 ローカル Web UI を使用すると、デバイスのシャット ダウンと再起動、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の送信も行うことができます。


## <a name="the-workflow"></a>ワークフロー

一般的なフローには次の手順が含まれます。

1. **注文** - Azure portal で注文を作成し、配送情報とデータのコピー先 Azure ストレージ アカウントを指定します。 デバイスが利用可能な場合は、Azure がデバイスを準備し、出荷追跡 ID が割り当てられたデバイスを出荷します。

2. **受領** - デバイスが到着したら、デバイスをネットワークに接続し、指定のケーブルを電源につなぎます。 電源を入れてデバイスに接続します。 デバイスのネットワークを構成し、データをコピーするホスト コンピューターに共有をマウントします。

3. **データのコピー** - Data Box Heavy の共有にデータをコピーします。

4. **返却** - デバイスを準備し、電源を切って Azure データセンターに返送します。

5. **アップロード** - デバイスから Azure にデータが自動的にコピーされます。 デバイスのディスクは、米国国立標準技術研究所 (NIST) のガイドラインに従って安全に消去されます。

このプロセス全体を通じて、状態のすべての変更について電子メールで通知されます。

## <a name="region-availability"></a>利用可能なリージョン

Data Box Heavy は、サービスが展開されているリージョン、デバイスが出荷される国/地域、データの転送対象となる Azure ストレージ アカウントに基づいてデータを転送できます。

- **サービスの可用性** - このリリースでは、Data Box Heavy は次のリージョンで利用できます。
    - 米国のすべてのパブリック クラウド リージョン - 米国中西部、米国西部 2、米国西部、米国中南部、米国中部、米国中北部、米国東部、米国東部 2。
    - 欧州連合 - 西ヨーロッパ、北ヨーロッパ。
    - 英国 - 英国南部、英国西部。
    - フランス - フランス中部、フランス南部。

- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、サービスが使用可能なすべての Azure リージョンで利用できます。

Data Box Heavy の提供状況に関するリージョン別の最新情報については、[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)を参照してください。

## <a name="sign-up"></a>サインアップ

Data Box Heavy はプレビュー段階であり、サインアップする必要があります。 Data Box Heavy にサインアップするには、次の手順を実行します。

1. Azure portal (https://aka.ms/azuredatabox) にサインインします。
2. **[+ リソースの作成]** をクリックして新しいリソースを作成します。 **Azure Data Box** を検索します。 **Azure Data Box** サービスを選択します。

    <!--![The Data Box Heavy sign up 1]()-->

3. **Create** をクリックしてください。

    <!--![The Data Box Heavy sign up 2]()-->

4. Data Box Heavy プレビューに使用するサブスクリプションを選択します。 Data Box Heavy リソースをデプロイするリージョンを選択します。 **[Data Box Heavy]** オプションで、 **[サインアップ]** をクリックします。

   <!--![The Data Box Heavy sign up 3]()-->

5. データ所在国/地域、時間枠、ターゲットの Azure サービス (データ転送、ネットワーク帯域幅、データ転送頻度) に関する質問に回答します。 プライバシーと利用規約を確認し、[Microsoft はお客様の電子メール アドレスを使用してお客様にご連絡いたします] のチェックボックスを選択します。

    <!--![The Data Box Heavy sign up 4]()-->

サインアップしてプレビューが有効になると、Data Box Heavy を注文できます。

    