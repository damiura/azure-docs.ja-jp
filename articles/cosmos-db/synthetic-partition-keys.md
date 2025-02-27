---
title: データとワークロードを均等に分散するために Azure Cosmos DB に合成パーティション キーを作成します。
description: Azure Cosmos コンテナーで合成パーティション キーを使用する方法について説明します
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 1fd436746dcd2e93a1699ac5c68965213c74580e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978876"
---
# <a name="create-a-synthetic-partition-key"></a>合成パーティション キーの作成

数百から数千というように、多数の個別の値を備えたパーティション キーにすることをお勧めします。 目標は、これらのパーティション キー値に関連付けられている項目の間でデータとワークロードを均等に分散することです。 このようなプロパティがデータに存在しない場合、"*合成パーティション キー*" を構築できます。 このドキュメントでは、Cosmos コンテナーの合成パーティション キーを生成するためのいくつかの基本的な手法について説明します。

## <a name="concatenate-multiple-properties-of-an-item"></a>項目の複数のプロパティを連結する

複数のプロパティ値を結合して、単一の人工 `partitionKey` プロパティにすることで、1 つのパーティション キーを形成できます。 これらのキーは合成キーと呼ばれます。 たとえば、次のドキュメント例を考えてみましょう。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

上のドキュメントの場合、1 つのオプションはパーティション キーとして /deviceId または /date を設定することです。 デバイス ID または日付に基づいてコンテナーをパーティション分割する場合は、このオプションを使用します。 もう 1 つのオプションでは、これら 2 つの値を合成 `partitionKey` プロパティに連結し、パーティション キーとして使用します。

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

リアルタイムのシナリオでは、何千もの項目がデータベース内に存在することがあります。 合成キーを手動で追加する代わりに、値を連結して Cosmos コンテナー内の項目に合成キーを挿入するクライアント側ロジックを定義します。

## <a name="use-a-partition-key-with-a-random-suffix"></a>ランダムなサフィックスを持つパーティション キーを使用する

ワークロードをより均等に分散する別の方法では、パーティション キー値の末尾にランダムな数値を追加します。 この方法で項目を配布すると、パーティション間で書き込み操作を並列に実行できます。

たとえば、パーティション キーが日付を表すような場合です。 1 から 400 までのランダムな数値を選択し、日付のサフィックスとしてそれを連結できます。 このメソッドでは、 `2018-08-09.1`、`2018-08-09.2` のようなパーティション キー値が  `2018-08-09.400` まで続きます。 パーティション キーをランダム化しているため、それぞれの日のコンテナーに対する書き込み操作は、複数のパーティションに均等に分散されます。 このメソッドにより、並列処理が改善され、全体的なスループットは高くなります。

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>事前に計算されたサフィックスを持つパーティション キーを使用する 

ランダム サフィックス戦略を使用すると書き込みスループットは大幅に向上しますが、特定の項目の読み取りは困難です。 項目を書き込むときに使用されたサフィックスの値はわかりません。 個々の項目を読みやすくには、事前に計算されたサフィックスの戦略を使用します。 パーティション間に項目を配布するためにランダムな数値を使用する代わりに、クエリを実行することに基づいて計算する番号を使用します。

コンテナーでパーティション キーに日付を使用する、前の例を検討してください。 各項目にアクセスしたい  `Vehicle-Identification-Number` (`VIN`) 属性があるとします。 さらに、日付に加えて `VIN` で項目を検索するクエリを実行することがよくあるものとします。 アプリケーションで項目をコンテナーに書き込む前に、VIN に基づいてハッシュ サフィックスを計算し、パーティション キーの日付に追加できます。 計算では、1 から 400 の範囲に均等に分散した数値が生成されます。 この結果は、ランダム サフィックス戦略の方法によって生成される結果に似ています。 パーティション キー値は、計算結果と連結された日付になります。

この戦略では、書き込みは、パーティション キー値およびパーティション全体に均等に分散されます。 特定の `Vehicle-Identification-Number` のパーティション キー値を計算できるため、特定の項目と日付を簡単に読み取ることができます。 この方法の利点は、1 つのホット パーティション キー (つまり、すべてのワークロードを取るパーティション キー) の作成を回避できることです。 

## <a name="next-steps"></a>次の手順

パーティション分割の概念の詳細については、次の記事を参照してください。

* [論理パーティション](partition-data.md)の詳細を確認する。
* [Azure Cosmos のコンテナーとデータベースのスループットをプロビジョニングする](set-throughput.md)方法の詳細を確認する。
* [Azure Cosmos コンテナーのスループットをプロビジョニングする](how-to-provision-container-throughput.md)方法を確認する。
* [Azure Cosmos データベースのスループットをプロビジョニングする](how-to-provision-database-throughput.md)方法を確認する。
