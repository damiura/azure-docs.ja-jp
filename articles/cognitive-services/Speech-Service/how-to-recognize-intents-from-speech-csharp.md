---
title: チュートリアル:Speech SDK for C# を使用して音声から意図を認識する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Speech SDK for C# を使用して音声から意図を認識する方法を学習します。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 0e24f66369cf990f6b271b894a31dc8395068e17
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257345"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>チュートリアル:C# 用の Speech SDK を使用して音声の意図を認識する

Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) は [Language Understanding サービス (LUIS)](https://www.luis.ai/home) と統合して**意図認識**機能を提供します。 意図は、航空機の予約や天気のチェック、あるいは電話を掛けるなどのユーザーが実行したい行動です。 ユーザーは、自然だと思われるどのような用語でも使用できます。 LUIS は機械学習を使用して、定義されている意図にユーザーの要求をマップします。

> [!NOTE]
> LUIS アプリケーションでは、認識する意図およびエンティティを定義します。 これは Speech サービスを使用する C# アプリケーションとは別のものです。 この記事では、「アプリ」は LUIS アプリを意味し、「アプリケーション」は C# のコードを意味します。

このチュートリアルでは、Speech SDK を使用して、デバイスのマイクを通じたユーザーの発話から意図を引き出す C# コンソール アプリケーションを開発します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Speech SDK NuGet パッケージを参照する Visual Studio プロジェクトを作成する
> * 音声構成を作成して意図認識エンジンを取得する
> * LUIS アプリのモデルを取得して必要な意図を追加する
> * 音声認識の言語を指定する
> * ファイルから音声を認識する
> * 非同期のイベント ドリブンの継続的な認識を使用する

## <a name="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* LUIS アカウント。 [LUIS ポータル](https://www.luis.ai/home)から無料で取得できます。
* Visual Studio 2017 (任意のエディション)。

## <a name="luis-and-speech"></a>LUIS および音声認識

LUIS は音声から意図を認識するために Speech Services と統合しています。 Speech Services のサブスクリプションは不要で、LUIS だけでかまいません。

LUIS では 2 種類のキーを使用します。

|キーの種類|目的|
|--------|-------|
|オーサリング|LUIS アプリをプログラムで作成および変更可能|
|endpoint |特定の LUIS アプリへのアクセスを承認する|

エンドポイント キーは、このチュートリアルに必要な LUIS キーです。 このチュートリアルでは、[事前構築済みホーム オートメーション アプリの使用](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)に従って作成できるホーム オートメーション LUIS アプリのサンプルを使用します。 独自の LUIS アプリを作成した場合は、代わりにそのアプリを使用することができます。

LUIS アプリを作成するとき、テキスト クエリを使用してアプリをテストできるようにスタート キーが自動的に生成されます。 このキーによって Speech Services 統合は有効にならないため、このキーはこのチュートリアルでは機能しません。 Azure ダッシュボードで LUIS リソースを作成して LUIS アプリに割り当てる必要があります。 このチュートリアルでは無料のサブスクリプション階層を使用することができます。

Azure ダッシュ ボードで LUIS のリソースを作成した後、[LUIS ポータル](https://www.luis.ai/home)にログインし、[マイ アプリ] ページでアプリケーションを選択し、アプリの[Manage] (管理) ページに切り替えます。 最後に、サイドバーの **[Keys and endpoints]\(キーとエンドポイント\)** をクリックします。

![LUIS のポータル キーとエンドポイントの設定](media/sdk/luis-keys-endpoints-page.png)

[Keys and endpoints]\(キーとエンドポイント\) の設定ページで、次のようにします。

1. [Resources and Keys]\(リソースとキー\) セクションまでスクロールし、 **[Assign resource]\(リソースの割り当て\)** をクリックします。
1. **[アプリへのキーの割り当て]** ダイアログで、次を選択します。

    * [Tenant]\(テナント\) として Microsoft を選択します。
    * [Subscription Name]\(サブスクリプション名\) には、使用する LUIS リソースを含む Azure サブスクリプションを選択します。
    * [Key]\(キー\) にはアプリで使用したい LUIS リソースを選択します。

まもなく、ページの下部にあるテーブルに新しいサブスクリプションが表示されます。 キーの横にあるアイコンをクリックしてキーをクリップボードにコピーします。 (どちらのキーを使用してもかまいません。)

![LUIS アプリのサブスクリプション キー](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Visual Studio での Speech プロジェクトの作成

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>コードの追加

Visual Studio プロジェクトで `Program.cs` ファイルを開き、ファイルの先頭にある `using` ステートメントのブロックを次の宣言で置き換えます。

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

指定された `Main()` メソッドの内部に次のコードを追加します。

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

次に示すように空の非同期メソッド `RecognizeIntentAsync()` を作成します。

```csharp
static async Task RecognizeIntentAsync()
{
}
```

この新しいメソッドの本文に、次のコードを追加します。

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

このメソッド内のプレース ホルダーを、次のように LUIS サブスクリプション キー、リージョン、およびアプリ ID で置き換えます。

|プレースホルダー|置換後の文字列|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|LUIS エンドポイント キー。 前述のように、これを「スタート キー」ではなく Azure ダッシュ ボードから取得したキーにする必要があります。 [LUIS ポータル](https://www.luis.ai/home)の、アプリのキーとエンドポイントのページ ([Manage]\(管理\) の下) で見つけることができます。|
|`YourLanguageUnderstandingServiceRegion`|LUIS サブスクリプションが存在するリージョンの短い識別子で、たとえば米国西部の場合は `westus` です。 [リージョン](regions.md)を参照してください。|
|`YourLanguageUnderstandingAppId`|LUIS アプリ ID。 [LUIS ポータル](https://www.luis.ai/home)のアプリの [Settings]\(設定\) ページから検索できます。|

この変更を行ってから、チュートリアル アプリケーションをビルド (Control-Shift-B) および実行 (F5) できます。 入力を求められたら、PC のマイクに向かって「"Turn off the lights (照明を消して)」と言ってみてください。 結果がコンソール ウィンドウに表示されます。

次のセクションでには、コードの詳細について説明します。


## <a name="create-an-intent-recognizer"></a>意図認識エンジンを作成する

音声から意図を認識するための最初の手順は、LUIS のエンドポイント キーとリージョンから音声構成を作成することです。 音声構成は、Speech SDK のさまざまな機能のための認識エンジンを作成するために使用できます。 音声構成では、使用するサブスクリプションを指定する複数の方法があります。ここでは `FromSubscription` を使用し、この方法ではサブスクリプション キーとリージョンを受け取ります。

> [!NOTE]
> Speech Services サブスクリプションではなく LUIS サブスクリプションのキーおよびリージョンを使用してください。

次に、`new IntentRecognizer(config)` を使用して意図認識エンジンを作成します。 構成で使用するサブスクリプションは既にわかっているため、認識エンジンを作成するときにサブスクリプション キーとエンドポイントをもう一度指定する必要はありません。

## <a name="import-a-luis-model-and-add-intents"></a>LUIS モデルをインポートして意図を追加する

ここで、`LanguageUnderstandingModel.FromAppId()` を使用して LUIS アプリからモデルをインポートし、認識エンジンの `AddIntent()` メソッドを経由して認識する LUIS の意図を追加します。 これら 2 つの手順により、ユーザーが自身の要求で使用すると思われる単語を示すことで音声認識の精度が向上します。 アプリのすべての意図をアプリケーション内で認識されるようにする必要がない場合、アプリのすべての意図を追加する必要はありません。

意図を追加するには、LUIS モデル (先ほど作成して `model` という名前を付けたもの)、意図名、および意図 ID の 3 つの引数が必要です。 ID と名前の違いは次のとおりです。

|`AddIntent()` 引数|目的|
|--------|-------|
|intentName |LUIS アプリ内で定義される意図の名前。 LUIS の意図名と正確に一致する必要があります。|
|intentID    |Speech SDK によって認識された意図に割り当てられた ID。 好みのものを使用でき、LUIS アプリで定義されている意図名に対応させる必要はありません。 同じコードによって複数の意図が処理される場合、それらに対して同じ ID を使用できます。|

ホーム オートメーション LUIS アプリには、2 つの意図があります。1 つはデバイスをオンにするための意図、もう 1 つはデバイスをオフにするための意図です。 以下の行はこれらの意図を認識エンジンに追加します。 `RecognizeIntentAsync()` メソッド内の 3 つの `AddIntent` 行をこのコードで置換します。

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

個々の意図を追加する代わりに、`AddAllIntents` メソッドを使用して、モデル内のすべての意図を認識エンジンに追加することもできます。

## <a name="start-recognition"></a>認識を開始する

認識エンジンを作成して意図を追加したら、認識を開始できます。 Speech SDK は、単発の認識および継続的な認識の両方をサポートしています。

|認識モード|呼び出すメソッド|結果|
|----------------|-----------------|---------|
|単発|`RecognizeOnceAsync()`|1 回の発話後に認識された意図を返します (ある場合)。|
|継続的|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|複数の発話を認識します。 結果が使用可能な場合はイベントを出力します (例: `IntermediateResultReceived`)。|

チュートリアル アプリケーションでは単発モードが使用されるため、`RecognizeOnceAsync()` を呼び出して認識を開始します。 結果は、認識された意図に関する情報を含む `IntentRecognitionResult` オブジェクトです。 LUIS の JSON 応答は、次の式によって抽出されます。

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

チュートリアル アプリケーションは JSON の結果を解析せず、結果をコンソール ウィンドウに表示するだけです。

![LUIS 認識の結果](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>認識言語を指定する

既定では、LUIS は意図を米国英語 (`en-us`) で認識します。 音声構成の `SpeechRecognitionLanguage` プロパティにロケール コードを割り当てることで、意図を他の言語で認識することができます。 たとえば、認識エンジンを作成する前にチュートリアル アプリケーションに `config.SpeechRecognitionLanguage = "de-de";` を追加すると、意図はドイツ語で認識されます。 [サポートされている言語](language-support.md#speech-to-text)を参照してください。

## <a name="continuous-recognition-from-a-file"></a>ファイルからの継続的な認識

次のコードは、Speech SDK を使用した意図認識の 2 つの追加機能を示しています。 1 つ目は、前に述べた継続的な認識です。この機能では、結果が使用可能になると認識エンジンがイベントを出力します。 これらのイベントは、指定したイベント ハンドラーによって後で処理できます。 継続的な認識では、`RecognizeOnceAsync()` の代わりに認識エンジンの `StartContinuousRecognitionAsync()` を呼び出して認識を開始します。

もう 1 つの機能では、処理の対象となる音声を含むオーディオを WAV ファイルから読み取ります。 この機能では、意図認識エンジンを作成するときに使用できるオーディオ構成を作成する必要があります。 ファイルはサンプリング速度が 16 kHz の単一チャネル (モノラル) である必要があります。

これらの機能を試すには、`RecognizeIntentAsync()` メソッドの本体を次のコードに置き換えます。

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

コードを修正し、以前と同様に LUIS エンドポイント キー、リージョン、およびアプリ ID を含み、ホーム オートメーションの意図を追加します。 `whatstheweatherlike.wav` をオーディオ ファイルの名前に変更します。 その後、ビルドして実行します。

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
この記事のコードを samples/csharp/sharedcontent/console フォルダーから探します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [音声を認識する方法](how-to-recognize-speech-csharp.md)
