---
title: Azure Data Factory の Mapping Data Flow のスキーマの誤差
description: スキーマの誤差がある Azure Data Factory 内での回復力のあるデータ フローの作成
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: aadab68185347dc0a12e0802f675efe13ecea545
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61262169"
---
# <a name="mapping-data-flow-schema-drift"></a>Mapping Data Flow のスキーマの誤差

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

スキーマの誤差の概念は、ソースのメタデータが頻繁に変更されるケースです。 フィールド、列、型などは、その場で追加、削除、または変更できます。 スキーマの誤差に対処しないと、アップストリームのデータ ソースの変更に対して、データ フローが脆弱になります。 通常、受信列およびフィールドが変更された場合、ETL パターンは、それらのソース名に関連している傾向があるため、失敗します。

スキーマの誤差の影響を受けないようにするには、データ エンジニアが、データ フロー ツールの機能で次のことができることが重要です。

* フィールド名、データ型、値、およびサイズを変更できるソースを定義する。
* フィールドおよび値をハードコーディングするのではなく、データのパターンを操作できる変換パラメーターを定義する。
* 名前付きフィールドを使用するのではなく、受信フィールドに一致するパターンを認識する式を定義する。

## <a name="how-to-implement-schema-drift"></a>スキーマの誤差を実装する方法

* ソース変換内で、[Allow Schema Drift]\(スキーマの誤差を許可\) を選択します。

<img src="media/data-flow/schemadrift001.png" width="400">

* このオプションを選択した場合は、データ フローを実行するたびに、すべての受信フィールドがソースから読み取られ、フロー全体を通じてシンクに渡されます。

* 必ず "自動マップ" を使用してシンク変換内のすべての新規フィールドをマップし、それらのフィールドが取得され、宛先に送信されるようにします。

<img src="media/data-flow/automap.png" width="400">

* [ソース] -> [シンク] (別名 [コピー]) の簡単なマッピングによって、新規フィールドがそのシナリオに取り込まれると、すべての処理が正常に実行されます。

* スキーマの誤差を処理するそのワークフロー内に変換を追加するには、パターン マッチングを使用して、名前、型、および値で列を一致させます。

* "スキーマの誤差" を認識する変換を作成する場合は、派生列または集計変換内で [Add Column Pattern]\(列パターンの追加\) をクリックします。

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> フロー全体を通じてスキーマの誤差を受け入れるには、データ フロー内でアーキテクチャの決定を行う必要があります。 これを行うと、ソースのスキーマの変更の影響を防ぐことができます。 ただし、データ フロー全体を通じて、列および型の早いバインディングは失われます。 Azure Data Factory では、スキーマの誤差のフローは、遅いバインディング フローとして扱われます。そのため、変換を作成する場合、フロー全体を通じて、スキーマ ビュー内で列名は使用できません。

<img src="media/data-flow/taxidrift1.png" width="400">

タクシー デモ サンプル データ フローの場合、下部のデータ フロー内に、TripFare ソースと共にスキーマの誤差のサンプルがあります。 集計変換では、集計フィールドに "列パターン" デザインが使用されていることに注意してください。 特定の列に名前を付けたり、位置で列を探したりする代わりに、データは変更される場合があり、各実行間で同じ順序で表示されない場合があるものと仮定します。

この例に示す Azure Data Factory のデータ フローのスキーマの誤差の処理では、データ ドメインに各トリップの価格が含まれることを理解したうえで、"double" 型の列をスキャンする集計を作成しました。 そのため、列の配置や列の名前にかかわらず、ソース内のすべての double 型のフィールド全体で集計数値計算を実行できます。

Azure Data Factory のデータ フローの構文の場合、一致するパターンから一致した各列を表すには、$$ を使用します。 複合文字列検索および正規表現関数を使用して、列名に基づいてマッチングを行うこともできます。 この場合、一致した "double" 型の各列に基づいて、新しい集計フィールド名を作成し、一致したそれらの各名前にテキスト ```_total``` を追加します。 

```concat($$, '_total')```

次に、一致したそれらの各列の値を丸めて合計します。

```round(sum ($$))```

これは、Azure Data Factory のデータ フローのサンプル "タクシー デモ" を使用してテストできます。 データ フロー デザイン サーフェイスの上部にあるデバッグ トグルを使用して、デバッグ セッションをオンにすると、結果を対話的に確認できます。

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>下流の新しい列にアクセスする

列パターンを使用して新しい列を生成すると、後から "byName" 式関数を使用してデータ フロー変換でそれらの新しい列にアクセスできます。

## <a name="next-steps"></a>次の手順

[データ フロー式言語](data-flow-expression-functions.md)には、"byName" や "byPosition" など、列パターンとスキーマ誤差用の追加機能があります。
