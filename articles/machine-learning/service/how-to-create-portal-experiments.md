---
title: Portal での実験の作成と参照
titleSuffix: Azure Machine Learning service
description: portal で自動化された機械学習の実験を作成および管理する方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: a2a281fda9272fb794692becb0ca08f3cf791458
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65990147"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Azure portal で自動化された機械学習の実験を作成および参照する (プレビュー)

 この記事では、1 行もコードを書かずに、Azure portal 上で自動化された機械学習の実験を作成、実行、参照する方法について説明します。 自動化された機械学習では、機械学習モデルをすばやく生成できるように、特定のデータに使用する最適なアルゴリズムを選択するプロセスが自動化されます。 [自動化された機械学習の詳細については、こちらを参照してください](concept-automated-ml.md)。

 よりコードベースのエクスペリエンスを希望する場合は、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用して、[自動化された機械学習の実験を Python で構成する](how-to-configure-auto-train.md)こともできます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](https://aka.ms/AMLFree) を今日からお試しいただけます。

* Azure Machine Learning ワークスペース。 「[Create an Azure Machine Learning service workspace](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)」(Azure Machine Learning service ワークスペースを作成する) を参照してください。

## <a name="get-started"></a>作業開始

ワークスペースの左側のウィンドウに移動します。 [作成 (プレビュー)] セクションで、[自動 Machine Learning] を選択します。

![Azure portal ナビゲーション ウィンドウ](media/how-to-create-portal-experiments/nav-pane.png)

 自動 Machine Learning での実験を初めて行う場合は、以下のように表示されます。

![Azure portal の実験ランディング ページ](media/how-to-create-portal-experiments/landing-page.png)

それ以外の場合、SDK を使用して作成されたものを含む、すべての自動化された機械学習実験の概要を示す自動化された機械学習ダッシュボードが表示されます。 ここで、日付、実験名、実行状態に基づいて実行をフィルター処理し、参照できます。

![Azure portal の実験ダッシュボード](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>実験の作成

[Create Experiment]\(実験の作成\) ボタンを選択して以下のフォームに入力します。

![実験の作成フォーム](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. 実験名を入力します。

1. データ プロファイルとトレーニング ジョブのコンピューティングを選択します。 既存のコンピューティングの一覧は、ドロップダウン リストにあります。 新しいコンピューティングを作成するには、ステップ 3 の手順に従います。

1. [Create a new compute]\(新しいコンピューティングの作成\) ボタンを選択して以下のウィンドウを開き、この実験のコンピューティング コンテキストを構成します。

    ![実験用の新しいコンピューティングを作成する](media/how-to-create-portal-experiments/create-new-compute.png)

    フィールド|説明
    ---|---
    コンピューティング名| コンピューティング コンテキストを識別する一意名を入力します。
    仮想マシンのサイズ| コンピューティングの仮想マシン サイズを選択します。
    追加設定| *Min node (最小ノード)* : コンピューティングの最小ノード数を入力します。 AML コンピューティングの最小ノード数は 0 です。 データ プロファイルを有効にするには、1 つ以上のノードが必要です。 <br> *Max node (最大ノード)* : コンピューティングの最大ノード数を入力します。 AML コンピューティングの既定は 6 ノードです。

      新しいコンピューティングの作成を開始するには、 **[作成]** を選択します。 これには少し時間がかかります。

      >[!NOTE]
      > コンピューティング名は、選択または作成するコンピューティングで*プロファイルが有効*になっているかどうかを示します (データ プロファイルの詳細については、7b を参照してください)。

1. ご自身のデータのストレージ アカウントを選択します。 パブリック プレビューでは、ローカル ファイルのアップロードと Azure Blob Storage アカウントのみがサポートされています。

1. ストレージ コンテナーを選択します。

1. ストレージ コンテナーからデータ ファイルを選択します。または、ローカル コンピューターからコンテナーにファイルをアップロードします。

    ![実験用のデータ ファイルを選択する](media/how-to-create-portal-experiments/select-file.png)

1. プレビューとプロファイルのタブを使用して、この実験のデータをさらに構成します。

    1. [プレビュー] タブで、データにヘッダーが含まれるかどうかを指示し、それぞれの特徴の列で **[含む]** の切り替えボタンを使用して、トレーニング対象の特徴 (列) を選択します。

        ![データのプレビュー](media/how-to-create-portal-experiments/data-preview.png)

    1. [プロファイル] タブで、[データ プロファイル](#profile)を特徴別に、また同様に分布、型、概要の統計情報 (平均、中央値、最大/最小値など) 別に表示できます。

        ![データ プロファイルのタブ](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > ご使用のコンピューティング コンテキストでプロファイルが "**有効になっていない**" 場合は、次のエラー メッセージが表示されます: "*Data profiling is only available for compute targets that are already running*" (データ プロファイルは既に実行されているコンピューティング ターゲットにのみ使用できます)。

1. トレーニング ジョブの種類として、分類、回帰、予測のいずれかを選択します。

1. ターゲット列を選択します。 予測を実行する対象の列です。

1. 予測の場合:
    1. 時間列の選択:この列には、使用する時間データが含まれています。
    1. 予測期間を選択します。モデルで将来を予測できる時間単位 (分/時間/日/週/月/年) の数を示します。 モデルで将来を予測する期間が延びるほど、正確性が下がります。 [予測と予測期間の詳細については、こちらを参照してください](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment)。

1. (省略可能) 詳細設定: トレーニング ジョブをより細かく制御するのに使用できる追加の設定です。

    詳細設定|説明
    ------|------
    主要メトリック| モデルをスコアリングするために使用される主なメトリックです。 [モデルのメトリックの詳細については、こちらを参照してください](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics)。
    終了基準| これらの基準のどれかが満たされると、トレーニング ジョブは完全に完了する前に終了します。 <br> *Training job time (minutes) (トレーニング ジョブ時間 (分))* : トレーニング ジョブを実行できる時間の長さ。  <br> *イテレーションの最大数*: トレーニング ジョブでテストするパイプライン (イテレーション) の最大数。 ジョブは、指定したイテレーションの数より多く実行されることはありません。 <br> *Metric score threshold* (メトリック スコアのしきい値): すべてのパイプラインの最小メトリック スコアです。 これにより、達成目標のターゲット メトリックを定義した場合には、必要以上にトレーニング ジョブに時間を費やすことはなくなります。
    前処理| 自動化された機械学習によって行われる前処理を有効または無効にするように選択します。 前処理には、合成的特徴を生成するための自動データ クレンジング、準備、変換が含まれます。 [前処理の詳細については、こちらを参照してください](#preprocess)。
    検証| トレーニング ジョブで使用するクロス検証オプションをどれか選択します。 [クロス検証の詳細については、こちらを参照してください](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options)。
    コンカレンシー| マルチコア コンピューティングの使用時に使用するマルチコアの制限を選択します。
    Blocked algorithm (ブロックするアルゴリズム)| トレーニング ジョブから除外するアルゴリズムを選択します。

   ![詳細設定フォーム](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> フィールドの詳細については、情報ツール ヒントをクリックします。

<a name="profile"></a>

### <a name="data-profiling"></a>データ プロファイル

データ セットが ML 対応であるかどうかを確認するために、データ セット全体で幅広い概要統計情報を取得できます。 数値以外の列の場合、最小、最大、エラー数などの基本的な統計情報だけが含まれます。 数値列の場合は、その統計学的モーメントと、推定される分位点も確認できます。 具体的には、データ プロファイルには以下が含まれています。

* **Feature (特徴)** : 集約される対象の列の名前。

* **プロファイル**: 推定された型に基づいたインラインの視覚化。 たとえば、文字列、ブール値、日付には値の数が示される一方、10 進数 (数値) は近似されたヒストグラムが示されます。 これにより、データの分布を簡単に把握できます。

* **Type distribution (型の分布)** : 列内の型のインライン値カウント。 Null は独自の型であるため、この視覚化は変則値または欠損値を検出するために便利です。

* **型**: 推定された列の型。 使用可能な値には、文字列、ブール値、日付、10 進数が含まれます。

* **最小**: 列の最小値。 型に固有の順序 (ブール数など) がない特徴の場合、空白のエントリが表示されます。

* **最大**: 列の最大値。 "最小" と同じように、無関係な型を持つ特徴には空白のエントリが表示されます。

* **カウント**: 列内の欠落しているエントリと欠落していないエントリの合計数。

* **Not missing count (欠落していないカウント)** : 列内の欠落していないエントリの数。 空の文字列とエラーは値として扱われるため、"欠落していないカウント" には含まれないことに注意してください。

* **Quantiles (分位点)** (0.1、1、5、25、50、75、95、99、99.9% の間隔): 各分位点でのデータ分布の感覚を提供する近似値。 無関係な型を持つ特徴には空白のエントリが表示されます。

* **平均**: 列の算術平均。 無関係な型を持つ特徴には空白のエントリが表示されます。

* **標準偏差**: 列の標準偏差。 無関係な型を持つ特徴には空白のエントリが表示されます。

* **差異**: 列の差異。 無関係な型を持つ特徴には空白のエントリが表示されます。

* **Skewness (歪度)** : 列の歪度。 無関係な型を持つ特徴には空白のエントリが表示されます。

* **Kurtosis (尖度)** : 列の尖度。 無関係な型を持つ特徴には空白のエントリが表示されます。

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>詳細な前処理

実験を構成するときに、詳細設定の `Preprocess` を有効にすることができます。 そうすることで、以下のデータの前処理と特性付けの手順が自動的に実行されます。

|前処理手順&nbsp;| 説明 |
| ------------- | ------------- |
|高カーディナリティまたは差異なしの特徴の削除|これらをトレーニング セットおよび検証セットから削除します。まったく値が存在しない特徴、すべての行の値が同じである特徴、非常に高いカーディナリティ (ハッシュ、ID、GUID など) の特徴が含まれます。|
|欠損値の補完|数値特徴の場合、その列の平均値で補完します。<br/><br/>カテゴリ特徴の場合、出現回数が最も多い値で補完します。|
|その他の特徴の生成|DateTime の特徴:年、月、日、曜日、年の通算日、四半期、年の通算週、時間、分、秒。<br/><br/>テキストの特徴:ユニグラム、バイグラム、文字トライグラムに基づく期間の頻度。|
|変換とエンコード |一意の値がほとんどない数値特徴は、カテゴリ特徴に変換されます。<br/><br/>カーディナリティの低いカテゴリ型の場合、ワンホット エンコードが実行されます。カーディナリティが高い場合は、ワンホット ハッシュ エンコードです。|
|ワードの埋め込み|事前トレーニングされたモデルを使用してテキスト トークンのベクトルをセンテンス ベクトルに変換するテキスト特性化機能です。 ドキュメント内の各ワードの埋め込みベクトルは、ドキュメント特徴ベクトルを生成するためにまとめて集約されます。|
|ターゲット エンコード|カテゴリ特徴の場合、回帰の問題について各カテゴリを平均ターゲット値にマップします。分類の問題については、各クラスのクラス確率にマップします。 マッピングの過剰適合および疎データ カテゴリによって発生するノイズを削減するために、頻度ベースの重み付けと k フォールド クロス検証が適用されます。|
|テキスト ターゲット エンコード|テキスト入力の場合、bag-of-words を使用するスタック線形モデルは、各クラスの確率を生成するために使用されます。|
|証拠の重み (WoE)|ターゲット列に対するカテゴリ列の相関関係のメジャーとして、WoE を計算します。 それは、クラス内およびクラス外の確率に対する比率の対数として計算されます。 このステップでは、クラスごとに 1 つの数値特徴列を出力し、明示的に欠損値と外れ値の処理を補完する必要がなくなります。|
|クラスターの距離|k-means クラスタリング モデルを、すべての数値列を対象にトレーニングします。  k 個の新しい特徴 (クラスターごとに 1 つの新しい数値特徴) を出力します。各クラスターの重心までの各サンプルの距離を含みます。|

## <a name="run-experiment-and-view-results"></a>実験を実行して結果を表示

実験を実行するには、[開始] をクリックします。 実験の準備プロセスは数分かかります。

### <a name="view-experiment-details"></a>実験の詳細の表示

実験の準備フェーズが完了すると、[実行の詳細] 画面が表示されます。 これは、作成されたモデルの完全な一覧です。 既定では、パラメーターに基づいて最高スコアが与えられたモデルが、一覧の先頭になります。 トレーニング ジョブでその他のモデルを試みると、それらがイテレーションの一覧とグラフに追加されます。 イテレーション グラフを使用すると、これまでに生成されたモデルのメトリックを簡単に比較できます。

トレーニング ジョブで各パイプラインの実行が終了するまで、しばらくかかることがあります。

![詳細ダッシュボードの実行](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>トレーニング実行の詳細の表示

トレーニングのパフォーマンス メトリックと分布グラフと同様に、出力モデルのいずれかをドリル ダウンして、実行の詳細を表示します。 [グラフの詳細については、こちらを参照してください](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts)。

![イテレーションの詳細](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>モデルをデプロイする

最適なモデルを作成したら、Web サービスとしてデプロイして新しいデータで予測します。

自動化された ML は、コードを記述せずにモデルをデプロイするのに役立ちます。

1. デプロイ オプションはいくつかあります。 
    1. 実験に対して設定したメトリックの条件に基づいて最適なモデルをデプロイする必要がある場合は、 **[実行の詳細]** ページから **[Deploy Best Model]\(最適なモデルのデプロイ\)** を選択します。

        ![[Deploy model]\(モデルのデプロイ\) ボタン](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. 特定のモデル イテレーションをデプロイする必要がある場合は、そのモデルにドリルダウンして、特定の実行の詳細ページを開き、 **[Deploy Model]\(モデルのデプロイ\)** を選択します。

        ![[Deploy model]\(モデルのデプロイ\) ボタン](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. 最初にモデルをサービスに登録します。 [モデルの登録] を選択し、登録プロセスが完了するのを待ちます。

    ![[Deploy model]\(モデルのデプロイ\) ブレード](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. モデルが登録されると、デプロイ時に使用するスコアリング スクリプト (scoring.py) および環境スクリプト (condaEnv.yml) をダウンロードできます。

1. スコアリング スクリプトと環境スクリプトがダウンロードされたら、左側のナビゲーション ウィンドウの **[資産]** ブレードに移動し、 **[モデル]** を選択します。

    ![ナビゲーション ウィンドウのモデル](media/how-to-create-portal-experiments/nav-pane-models.png)

1. 登録したモデルを選択し、[イメージの作成] を選択します。

    モデルを特定するには、その説明を確認します。説明には実行 ID とイテレーション番号が *<Run_ID>_<Iteration_number>_Model* の形式で含まれています

    ![モデル:イメージを作成する](media/how-to-create-portal-experiments/model-create-image.png)

1. イメージの名前を入力します。 
1. [スコアリング ファイル] ボックスの横にある **[参照]** を選択して、前にダウンロードしたスコアリング ファイル (scoring.py) をアップロードします。

1. [Conda ファイル] ボックスの横にある **[参照]** を選択して、前にダウンロードした環境ファイル (condaEnv.yml) をアップロードします。

    独自のスコアリング スクリプトと conda ファイルを使用できるほか、追加ファイルをアップロードすることもできます。 [スコアリング スクリプトの詳細をご確認ください](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#script)。

      >[!Important]
      > ファイル名の文字数は 32 文字未満にする必要があります。先頭と末尾には英数字を使用してください。 先頭と末尾以外では、ダッシュ、アンダースコア、ピリオド、および英数字を使用できます。 スペースは使用できません。

    ![イメージを作成する](media/how-to-create-portal-experiments/create-image.png)

1. [作成] を選択して、イメージの作成を開始します。 この作業を完了するには数分かかります。完了すると、上部のバーにメッセージが表示されます。
1. [イメージ] タブに移動し、デプロイするイメージの横にあるチェックボックスをオンにして、[デプロイの作成] を選択します。 [デプロイの詳細をご確認ください](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)。

    デプロイには 2 つのオプションがあります。
     + Azure コンテナー インスタンス (ACI) - 大規模な運用デプロイではなく、テスト目的で使用されます。 "_CPU 予約容量_" については 1 つ以上のコア、"_メモリ予約容量_" については 1 ギガバイト (GB) 以上に対して値を入力します
     + Azure Kubernetes Service (AKS) - 大規模なデプロイ用のオプションです。 AKS ベースのコンピューティングを準備する必要があります。

     ![イメージ:Create deployment](media/how-to-create-portal-experiments/images-create-deployment.png)

1. 完了したら、 **[作成]** を選択します。 モデルのデプロイで各パイプラインの実行が完了するには、数分かかる場合があります。

1. これで完了です。 予測を生成するための運用 Web サービスが作成されました。

## <a name="next-steps"></a>次の手順

* [自動化された機械学習の詳細](concept-automated-ml.md)と Azure Machine Learning について学習します。
* [Web サービスを使用する方法を学習します](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)。
