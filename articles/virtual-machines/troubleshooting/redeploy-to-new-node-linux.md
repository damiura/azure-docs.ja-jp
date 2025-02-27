---
title: Azure での Linux 仮想マシンの再デプロイ | Microsoft Docs
description: SSH 接続の問題を軽減するために Azure で Linux 仮想マシンを再デプロイする方法。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 1c1ac1a20c1c06db10c1462c95f4d924c5ec09d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116949"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>新しい Azure ノードへの Linux 仮想マシンの再デプロイ
Azure の Linux 仮想マシン (VM) への SSH またはアプリケーション アクセスに関するトラブルシューティングで問題があるときは、VM の再デプロイが有効な場合があります。 VM を再デプロイするときは、Azure インフラストラクチャ内の新しいノードに VM を移動してから、電源をオンにします。 すべての構成オプションと関連するリソースが保持されます。 この記事では、Azure CLI または Azure ポータルを使用して VM を再デプロイする方法について説明します。

> [!NOTE]
> VM を再デプロイすると、一時ディスクが失われ、仮想ネットワーク インターフェイスに関連付けられている動的 IP アドレスが更新されます。 


## <a name="use-the-azure-cli"></a>Azure CLI の使用
最新の [Azure CLI](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインします。

[az vm redeploy](/cli/azure/vm) を使用して VM を再デプロイします。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM を再デプロイします。

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Azure クラシック CLI を使用する
[最新の Azure クラシック CLI](../../cli-install-nodejs.md) をインストールし、Azure アカウントにログインします。 リソース マネージャー モード (`azure config mode arm`) になっていることを確認します。

次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM を再デプロイします。

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>次の手順
VM への接続に関する問題が発生した場合は、[SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページまたは「[SSH の詳細なトラブルシューティング手順](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。 VM で実行されるアプリケーションにアクセスできない場合は、[アプリケーションの問題のトラブルシューティング](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページもご覧ください。


