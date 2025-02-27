---
title: Azure Container Instances での Azure Files ボリュームのマウント
description: Azure Files ボリュームをマウントして、Azure Container Instances で状態を保持する方法について説明します
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564023"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure Container Instances に Azure ファイル共有をマウントする

既定では、Azure Container Instances はステートレスです。 コンテナーがクラッシュまたは停止すると、すべての状態が失われます。 コンテナーの有効期間後も状態を保持するには、外部ストアからボリュームをマウントする必要があります。 この記事では、Azure Container Instances で使用できるように [Azure Files](../storage/files/storage-files-introduction.md) で作成された Azure ファイル共有をマウントする方法について説明します。 Azure Files はクラウドで、業界標準の Server Message Block (SMB) プロトコルを介してアクセスできる、完全に管理されたファイル共有を提供します。 Azure Container Instances で Azure ファイル共有を使用することで、 Azure 仮想マシンで Azure ファイル共有を使用するのと同様のファイル共有機能を提供します。

> [!NOTE]
> Azure ファイル共有のマウントは現在、Linux コンテナーに限定されています。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) で確認できます。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

Azure Container Instances で Azure ファイル共有を使用するには、まず、ファイル共有を作成する必要があります。 次のスクリプトを実行して、ファイル共有および共有自体をホストするためのストレージ アカウントを作成してください。 ストレージ アカウント名はグローバルに一意にする必要があるため、このスクリプトは、ベース文字列にランダムな値を追加します。

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>ストレージの資格情報の取得

Azure Container Instances で Azure ファイル共有をボリュームとしてマウントするには、ストレージ アカウント名、共有名、ストレージ アクセス キーの 3 つの値が必要です。

上記のスクリプトを使用した場合、ストレージ アカウント名は $ACI_PERS_STORAGE_ACCOUNT_NAME 変数に格納されました。 アカウント名を表示するには、次のように入力します。

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

共有名は既にわかっているため (上記のスクリプトでは *acishare* として定義)、あとはストレージ アカウント キーです。このキーを見つけるには、次のコマンドを使用します。

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>コンテナーのデプロイとボリュームのマウント

Azure ファイル共有をコンテナー内のボリュームとしてマウントするには、[az container create][az-container-create] でコンテナーを作成する際に、共有とボリュームのマウント ポイントを指定します。 前述の手順に従った場合、次のコマンドを使ってコンテナーを作成すれば、先ほど作成した共有をマウントすることができます。

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

`--dns-name-label` の値は、コンテナー インスタンスを作成する Azure リージョン内で一意である必要があります。 コマンドを実行したときに **DNS 名ラベル**のエラー メッセージが表示された場合は、前述のコマンドの値を更新してください。

## <a name="manage-files-in-mounted-volume"></a>マウントしたボリューム内のファイルの管理

コンテナーが起動したら、Microsoft [aci-hellofiles][aci-hellofiles] イメージ経由でデプロイされる単純な Web アプリを使用して、指定したマウント パスにある Azure ファイル共有内に小さいテキスト ファイルを作成できます。 [az container show][az-container-show] コマンドを使用して、Web アプリの完全修飾ドメイン名 (FQDN) を取得します。

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

[Azure Portal][portal] または [Microsoft Azure Storage Explorer][storage-explorer] などのツールを使用して、ファイル共有に書き込まれるファイルを取得および検査することができます。

## <a name="mount-multiple-volumes"></a>複数ボリュームのマウント

コンテナー インスタンスに複数のボリュームをマウントするには、[Azure Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)または YAML ファイルを使用してデプロイを行う必要があります。

テンプレートを使用するには、テンプレートの `properties` セクションで `volumes` 配列を設定することにより、共有の詳細を指定し、ボリュームを定義します。 たとえば、*share1* および *share2* という 2 つの Azure Files 共有をストレージ アカウント *myStorageAccount* に作成した場合、`volumes` 配列は次のようになります。

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

次に、ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。 たとえば、以下は、前に定義した 2 つのボリューム *myvolume1* と *myvolume2* をマウントします。

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Azure Resource Manager テンプレートによるコンテナー インスタンスのデプロイ例を見るには、[コンテナー グループのデプロイ](container-instances-multi-container-group.md)に関するページを参照してください。 YAML ファイルの使用例については、[YAML で複数コンテナー グループを展開する](container-instances-multi-container-yaml.md)に関するページを参照してください

## <a name="next-steps"></a>次の手順

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に emptyDir ボリュームをマウントする](container-instances-volume-emptydir.md)
* [Azure Container Instances に gitRepo ボリュームをマウントする](container-instances-volume-gitrepo.md)
* [Azure Container Instances にシークレット ボリュームをマウントする](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show