---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181450"
---
### <a name="run-multiple-containers-on-the-same-host"></a>同じホスト上で複数のコンテナーを実行する

公開されているポートを使って複数のコンテナーを実行する予定の場合、必ず各コンテナーを別の公開されているポートで実行してください。 たとえば、最初のコンテナーをポート 5000 上で、2 番目のコンテナーを 5001 上で実行します。

このコンテナーと、別の Azure Cognitive Services コンテナーを HOST 上で同時に実行することができます。 同じ Cognitive Services コンテナーの複数のコンテナーを実行することもできます。
