---
title: フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加の構成 | Microsoft Docs
description: フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加を構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 600d6b9f1eb8d8073e1658dd5b8196a3d8137e42
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733714"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>チュートリアル:フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加の構成

デバイスはユーザーと同様、保護の対象であり、時と場所を選ばずにリソースを保護するために使用したいもう 1 つの重要な ID です。 この目標は、次のいずれかの方法を使用してデバイスの ID を Azure AD に設定して管理することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD にデバイスを設定して、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護することもできます。

このチュートリアルでは、AD FS を使用してフェデレーション環境で AD ドメイン参加済みコンピューター デバイスのハイブリッド Azure AD 参加を構成する方法について説明します。

> [!NOTE]
> フェデレーション環境で AD FS ではなく ID プロバイダーを使用している場合、その ID プロバイダーで WS-Trust プロトコルがサポートされていることを確認する必要があります。 WS-Trust は、現在のハイブリッド Azure AD 参加済み Windows デバイスを Azure AD で認証するために必要です。 さらに、ハイブリッド Azure AD 参加が必要なダウンレベルの Windows デバイスがある場合、ID プロバイダーで WIAORMULTIAUTHN 要求がサポートされる必要があります。 


> [!div class="checklist"]
> * ハイブリッド Azure AD 参加の構成
> * ダウンレベルの Windows デバイスの有効化
> * 登録の確認
> * トラブルシューティング

## <a name="prerequisites"></a>前提条件

このチュートリアルは、次の事項を熟知していることを前提としています。

- [Azure Active Directory でのデバイス ID 管理の概要](../device-management-introduction.md)
- [ハイブリッド Azure Active Directory Join の実装を計画する方法](hybrid-azuread-join-plan.md)
- [Hybrid Azure AD Join の制御された検証を実行する方法](hybrid-azuread-join-control.md)

このチュートリアルのシナリオを構成するための要件を次に示します。

- Windows Server 2012 R2 と AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) バージョン 1.1.819.0 以降。

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 関連するウィザードを使用すると、次の操作を実行できます。

- デバイス登録のためのサービス接続ポイント (SCP) を構成する
- Azure AD 証明書利用者の信頼をバックアップする
- Azure AD 信頼のクレーム規則を更新する

この記事の構成手順は、このウィザードに基づいています。 以前のバージョンの Azure AD Connect がインストールされている場合は、1.1.819 以降にアップグレードする必要があります。 Azure AD Connect の最新バージョンをインストールすることができない場合は、[ハイブリッド Azure AD 参加を手動で構成する方法](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual)に関するページを参照してください。

ハイブリッド Azure AD 参加を使用するには、デバイスが組織のネットワーク内から次の Microsoft リソースにアクセスできる必要があります。  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- 組織の STS (フェデレーション ドメイン)
- [https://autologon.microsoftazuread-sso.com](`https://autologon.microsoftazuread-sso.com`) (シームレス SSO を使用している場合、または使用する予定の場合)

Windows 10 1803 以降、AD FS を使用したフェデレーション ドメインの即時的なハイブリッド Azure AD 参加が失敗した場合は、Azure AD Connect を利用して Azure AD のコンピューター オブジェクトを同期させます。これは後で、ハイブリッド Azure AD 参加のデバイス登録を完了するために使用されます。 Azure AD Connect が、Azure AD に参加するハイブリッド Azure AD にするデバイスのコンピューター オブジェクトを同期済みであることを確認します。 コンピューター オブジェクトが特定の組織単位 (OU) に属している場合、これらの OU を Azure AD Connect についても構成する必要があります。 Azure AD Connect を使用してコンピューター オブジェクトを同期する方法の詳細については、[Azure AD Connect を使用したフィルタリングの構成](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering)に関する記事を参照してください。

組織が送信プロキシを介してインターネットにアクセスする必要がある場合、Microsoft は、Windows 10 コンピューターを Azure AD にデバイス登録できるように [Web プロキシ自動発見 (WPAD) を実装](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10))することをお勧めします。 WPAD の構成と管理に関する問題が発生する場合は、「[Troubleshooting Automatic Detection (自動検出のトラブルシューティング)](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10))」を参照してください。 

Windows 10 1709 以降では、WPAD を使用しておらず、自分のコンピューター上でプロキシ設定を構成する必要がある場合、[グループ ポリシー オブジェクト (GPO) を使用して WinHTTP 設定を構成する](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)ことでそれを行えます。

> [!NOTE]
> WinHTTP 設定を使用してコンピューター上でプロキシ設定を構成すると、構成されるプロキシに接続できないコンピューターでインターネットへの接続が失敗します。

組織が認証された送信プロキシ経由でインターネットにアクセスする必要がある場合は、Windows 10 コンピューターが送信プロキシに対して正常に認証されることを確認する必要があります。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用して送信プロキシ認証を構成する必要があります。 構成要件については、送信プロキシ プロバイダーに確認してください。

## <a name="configure-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の構成

Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには、次のものが必要です。

- Azure AD テナントの全体管理者の資格情報。  
- 各フォレストのエンタープライズ管理者の資格情報。
- AD FS 管理者の資格情報。

**Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには:**

1. Azure AD Connect を起動し、 **[構成]** をクリックします。

   ![ようこそ](./media/hybrid-azuread-join-federated-domains/11.png)

1. **[追加のタスク]** ページで、 **[デバイス オプションの構成]** を選択し、 **[次へ]** をクリックします。

   ![追加のタスク](./media/hybrid-azuread-join-federated-domains/12.png)

1. **[概要]** ページで、 **[次へ]** をクリックします。

   ![概要](./media/hybrid-azuread-join-federated-domains/13.png)

1. **[Azure AD に接続]** ページで、Azure AD テナントの全体管理者の資格情報を入力し、 **[次へ]** をクリックします。

   ![Azure への接続](./media/hybrid-azuread-join-federated-domains/14.png)

1. **[デバイス オプション]** ページで、 **[ハイブリッド Azure AD 参加の構成]** を選択し、 **[次へ]** をクリックします。

   ![デバイス オプション](./media/hybrid-azuread-join-federated-domains/15.png)

1. **[SCP]** ページで、次の手順を実行し、 **[次へ]** をクリックします。

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. フォレストを選択します。
   1. 認証サービスを選択します。 組織が Windows 10 クライアントのみを使用していて、ユーザーがコンピューター/デバイスの同期を構成済みか組織で SeamlessSSO が使用されている場合を除き、AD FS を選択する必要があります。
   1. **[追加]** をクリックして、エンタープライズ管理者の資格情報を入力します。

1. **[デバイスのオペレーティング システム]** ページで、Active Directory 環境内のデバイスで使用されているオペレーティング システムを選択し、 **[次へ]** をクリックします。

   ![デバイスのオペレーティング システム](./media/hybrid-azuread-join-federated-domains/17.png)

1. **[フェデレーションの構成]** ページで、AD FS 管理者の資格情報を入力し、 **[次へ]** をクリックします。

   ![フェデレーションの構成](./media/hybrid-azuread-join-federated-domains/18.png)

1. **[構成の準備完了]** ページで、 **[構成]** をクリックします。

   ![構成の準備完了](./media/hybrid-azuread-join-federated-domains/19.png)

1. **[構成が完了しました]** ページで、 **[終了]** をクリックします。

   ![構成の完了](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>ダウンレベルの Windows デバイスの有効化

ドメイン参加済みデバイスの一部がダウンレベルの Windows デバイスである場合は、以下の操作が必要です。

- デバイスの登録用のローカル イントラネット設定の構成
- ダウンレベルの Windows コンピューターに対する Microsoft Workplace Join のインストール

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>デバイスの登録用のローカル イントラネット設定の構成

ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するため、およびデバイスが Azure AD で認証を受けるときに証明書の指定を求めるメッセージが表示されないようにするために、ドメイン参加済みデバイスにポリシーをプッシュして、以下の URL を Internet Explorer のローカル イントラネット ゾーンに追加することができます。

- `https://device.login.microsoftonline.com`
- 組織のセキュリティ トークン サービス (STS - フェデレーション ドメイン)
- `https://autologon.microsoftazuread-sso.com` (シームレス SSO の場合)。

さらに、ユーザーのローカル イントラネット ゾーンで **[スクリプトを介したステータス バーの更新を許可する]** を有効にする必要があります。

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>ダウンレベルの Windows コンピューターに対する Microsoft Workplace Join のインストール

ダウンレベルの Windows デバイスを登録するには、Microsoft ダウンロード センターで入手できる [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/download/details.aspx?id=53554) を組織がインストールする必要があります。

 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) などのソフトウェア配布システムを使用して、パッケージをデプロイできます。 パッケージは、quiet パラメーターを使用する、標準のサイレント インストール オプションをサポートしています。 Configuration Manager の Current Branch には、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。

インストーラーによって、ユーザー コンテキストで実行されるシステムにスケジュール済みタスクが作成されます。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 Azure AD によって認証が行われた後、ユーザーの資格情報を使ってサイレントにデバイスが Azure AD に登録されます。

## <a name="verify-the-registration"></a>登録の確認

Azure テナントのデバイス登録状態を確認するには、 **[Azure Active Directory PowerShell モジュール](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** の **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** コマンドレットを使用できます。

**Get-MSolDevice** コマンドレットを使用してサービスの詳細を確認する場合:

- Windows クライアントの ID と一致する**デバイス ID** を備えたオブジェクトが存在する必要があります。
- **DeviceTrustType** の値は **[ドメイン参加済み]** でなければなりません。 これは、Azure AD ポータルの [デバイス] ページの **[ハイブリッド Azure AD 参加済み]** 状態に相当します。
- 条件付きアクセスで使用されるデバイスの **Enabled** の値は **True**、**DeviceTrustLevel**の値は **Managed** でなければなりません。

**サービスの詳細を確認するには:**

1. **Windows PowerShell** を管理者として開きます。
1. 「`Connect-MsolService`」と入力して Azure テナントに接続します。  
1. 「 `get-msoldevice -deviceId <deviceId>`」と入力します。
1. **[有効]** が **[True]** に設定されていることを確認します。

## <a name="troubleshoot-your-implementation"></a>実装のトラブルシューティング

ドメイン参加済み Windows デバイスのハイブリッド Azure AD 参加を行うときに問題が発生した場合は、次のトピックを参照してください。

- [最新の Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)
- [ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>次の手順

- Azure AD ポータルでのデバイス ID の管理について詳しくは、[Azure portal を使用してデバイス ID を管理する方法](device-management-azure-portal.md)に関するページを参照してください。

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
