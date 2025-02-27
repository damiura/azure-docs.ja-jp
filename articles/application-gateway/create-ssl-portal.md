---
title: チュートリアル - SSL 終了でアプリケーション ゲートウェイを構成する - Azure portal
description: このチュートリアルでは、Azure portal を使用して、アプリケーション ゲートウェイを構成し、SSL 終了の証明書を追加する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134496"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用して SSL 終了でアプリケーション ゲートウェイを構成する

Azure Portal を使用して、バックエンド サーバーに仮想マシンを使用する SSL 終了の証明書で、[アプリケーション ゲートウェイ](overview.md)を構成することができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 自己署名証明書の作成
> * 証明書でのアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成
> * アプリケーション ゲートウェイのテスト

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

このセクションでは、[New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) を使用して、自己署名証明書を作成します。 アプリケーション ゲートウェイのリスナーを作成するときに、証明書を Azure portal にアップロードします。

ローカル コンピューターでは、管理者として Windows PowerShell ウィンドウを開きます。 次のコマンドを実行して、証明書を作成します。

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

次のような応答が表示されます。

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

作成したリソース間の通信には仮想ネットワークが必要です。 この例では 2 つのサブネットが作成されます。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。

1. Azure portal の左上隅にある **[新規]** を選択します。
2. **[ネットワーク]** を選択し、注目のリストで **[Application Gateway]** を選択します。
3. アプリケーション ゲートウェイの名前として「*myAppGateway*」を、新しいリソース グループとして「*myResourceGroupAG*」を入力します。
4. 他の設定は既定値をそのまま使用し、**[OK]** を選択します。
5. **[仮想ネットワークの選択]**、**[新規作成]** の順に選択し、次の仮想ネットワークの値を入力します。

   - *myVNet* - 仮想ネットワークの名前です。
   - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
   - *myAGSubnet* - サブネットの名前です。
   - *10.0.0.0/24* - サブネットのアドレス空間です。

     ![Create virtual network](./media/create-ssl-portal/application-gateway-vnet.png)

6. **[OK]** を選択して、仮想ネットワークとサブネットを作成します。
7. **[パブリック IP アドレスの選択]**、**[新規作成]** の順に選択し、パブリック IP アドレスの名前を入力します。 この例では、パブリック IP アドレスの名前は *myAGPublicIPAddress* にします。 他の設定は既定値をそのまま使用し、**[OK]** を選択します。
8. リスナーのプロトコルとして **[HTTPS]** を選択し、ポートが **443** として定義されていることを確認します。
9. フォルダー アイコンを選択し、前に作成したアップロードする *appgwcert.pfx* 証明書に移動します。
10. 証明書の名前として「*mycert1*」を、パスワードとして「*Azure123456!*」 を入力し、**[OK]** を選択します。

    ![新しいアプリケーション ゲートウェイの作成](./media/create-ssl-portal/application-gateway-create.png)

11. 概要ページで設定を確認し、**[OK]** を選択して、ネットワーク リソースとアプリケーション ゲートウェイを作成します。 アプリケーション ゲートウェイの作成には数分かかる場合があります。デプロイが正常に終了するのを待ち、その後で次のセクションに進みます。

### <a name="add-a-subnet"></a>サブネットの追加

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で **[myVNet]** を選びます。
2. **[サブネット]** を選択し、**[サブネット]** を選択します。

    ![サブネットの作成](./media/create-ssl-portal/application-gateway-subnet.png)

3. サブネットの名前として「*myBackendSubnet*」と入力し、**[OK]** を選択します。

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。 また、IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. **[新規]** を選択します。
2. **[Compute]** をクリックし、おすすめのリストで **[Windows Server 2016 Datacenter]** を選択します。
3. 次の仮想マシンの値を入力します。

    - *myVM* - 仮想マシンの名前です。
    - *azureuser* - 管理者のユーザー名です。
    - *Azure123456!* パスワードです。
    - **[既存のものを使用]**、*[myResourceGroupAG]* の順に選択します。

4. **[OK]** を選択します。
5. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** を選択します。
6. 仮想ネットワークに対して **[myVNet]** が選択されていること、およびサブネットが **myBackendSubnet** であることを確認します。 
7. **[無効]** を選択して、ブート診断を無効にします。
8. **[OK]** を選択し、概要ページの設定を確認して、**[作成]** を選択します。

### <a name="install-iis"></a>IIS のインストール

1. 対話型シェルを開いて、**PowerShell** に設定されていることを確認します。

    ![カスタム拡張機能のインストール](./media/create-ssl-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 2 番目の仮想マシンを作成し、終了したばかりの手順を使用して、IIS をインストールします。 その名前および Set-AzVMExtension の VMName として「*myVM2*」を入力します。

### <a name="add-backend-servers"></a>バックエンド サーバーの追加

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。
1. **[バックエンド プール]** を選択します。 既定のプールがアプリケーション ゲートウェイで自動的に作成されます。 **[appGatewayBackendPool]** を選択します。
1. **[ターゲットの追加]** を選択して、作成した各仮想マシンをバックエンド プールに追加します。

    ![バックエンド サーバーの追加](./media/create-ssl-portal/application-gateway-backend.png)

1. **[保存]** を選択します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. **[すべてのリソース]** を選択し、**[myAGPublicIPAddress]** を選択します。

    ![アプリケーション ゲートウェイのパブリック IP アドレスの記録](./media/create-ssl-portal/application-gateway-ag-address.png)

2. パブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 自己署名証明書を使用した場合、セキュリティ警告を受け入れるには、[詳細] を選択し、Web ページに進みます。

    ![セキュリティに関する警告](./media/create-ssl-portal/application-gateway-secure.png)

    セキュリティで保護された IIS Web サイトは、次の例のように表示されます。

    ![アプリケーション ゲートウェイでのベース URL のテスト](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Application Gateway で実行できる操作の詳細を確認する](application-gateway-introduction.md)
