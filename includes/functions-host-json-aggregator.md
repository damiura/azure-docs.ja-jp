---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: da0cbab59d9c801419ac4b3704fee3275f337fd9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181329"
---
[Application Insights のメトリックを計算する](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator)ときに集計される関数呼び出しの数を指定します。 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|プロパティ |既定値  | 説明 |
|---------|---------|---------| 
|batchSize|1000|集計する要求の最大数。| 
|flushTimeout|00:00:30|集計する最長期間。| 

2 つの制限のいずれかに最初に達した場合、関数呼び出しが集計されます。