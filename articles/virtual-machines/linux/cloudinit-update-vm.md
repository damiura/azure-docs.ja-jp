---
title: Azure の Linux VM で cloud-init を使用してパッケージを更新およびインストールする | Microsoft Docs
description: Azure CLI による作成時に Linux VM で cloud-init を使用してパッケージを更新およびインストールする方法
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: d5f4dc7f4abc13f253a206a63e65faf1106f9c7c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60627743"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Azure の Linux VM で cloud-init を使用してパッケージを更新およびインストールする
この記事では、Azure でのプロビジョニング時に、[cloud-init](https://cloudinit.readthedocs.io) を使用して、Linux 仮想マシン (VM) または仮想マシン スケール セット (VMSS) でパッケージを更新する方法を示します。 この cloud-init スクリプトは、リソースが Azure によってプロビジョニングされた後の最初の起動時に実行されます。 cloud-init が Azure およびサポートされている Linux ディストリビューションでネイティブに動作する方法の詳細については、[cloud-init の概要](using-cloud-init.md)に関するページをご覧ください

## <a name="update-a-vm-with-cloud-init"></a>cloud-init で VM を更新する
セキュリティのために、初回起動時に最新の更新プログラムを適用するように VM を構成することが必要な場合があります。 cloud-init はさまざまな Linux ディストリビューションで動作するため、パッケージ マネージャーに `apt` または `yum` を指定する必要はありません。 代わりに、`package_upgrade` を定義し、cloud-init プロセスで、使用中のディストリビューションに適したメカニズムを判断します。 このワークフローでは、異なるディストリビューションで同じ cloud-init スクリプトを使用することができます。

実行中のアップグレード プロセスを表示するには、現在のシェルで *cloud_init_upgrade.txt* というファイルを作成し、次の構成を貼り付けます。 この例では、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 `sensible-editor cloud_init_upgrade.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 **nano** エディターを使用するには #1 を選びます。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

このイメージをデプロイする前に、[az group create](/cli/azure/group) コマンドを使用してリソース グループを作成する必要があります。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

ここで、[az vm create](/cli/azure/vm) で VM を作成し、次のように `--custom-data cloud_init_upgrade.txt` で cloud-init ファイルを指定します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

前述のコマンドからの出力に示すように、VM のパブリック IP アドレスに SSH 接続します。 実際の **publicIpAddress** を次のように入力します。

```bash
ssh <publicIpAddress>
```

パッケージ管理ツールを実行し、更新プログラムを確認します。

```bash
sudo yum update
```

cloud-init によって起動時に更新プログラムが確認され、インストールされるため、追加で適用する更新プログラムはありません。  更新プロセス、変更されたパッケージの数、`httpd` のインストールを確認するには、`yum history` を実行します。以下のような出力が表示されます。

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>次の手順
構成変更の cloud-init の他の例については、以下をご覧ください。
 
- [VM に他の Linux ユーザーを追加する](cloudinit-add-user.md)
- [初回起動時にパッケージ マネージャーを実行して既存のパッケージを更新する](cloudinit-update-vm.md)
- [VM のローカル ホスト名を変更する](cloudinit-update-vm-hostname.md) 
- [アプリケーション パッケージのインストール、構成ファイルの更新、キーの挿入](tutorial-automate-vm-deployment.md)
