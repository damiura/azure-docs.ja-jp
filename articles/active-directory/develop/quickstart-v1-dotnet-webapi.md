---
title: 認証と承認のために Azure AD と連携する .NET Web API を構築する | Microsoft Docs
description: 認証と承認のために Azure AD と連携する .NET MVC Web API を構築する方法
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2eca253bc5d1495d26506e0e6f8a83762e8bc5
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001102"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>クイック スタート:認証と認可のために Azure AD と連携する .NET Web API を構築する

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

保護されているリソースにアクセスするアプリケーションを構築する場合、不正なアクセスからこれらのリソースを保護する方法を習得する必要があります。 Azure Active Directory (Azure AD) を使用すると、OAuth 2.0 ベアラー アクセス トークンを使用し、わずか数行のコードを追加するだけで、Web API の保護を簡単に支援することができます。

ASP.NET Web アプリでは、こうした保護を、.NET Framework 4.5 に含まれるコミュニティ駆動の OWIN ミドルウェアの Microsoft 実装を使用することにより実現できます。 ここでは、OWIN を使用して、次の処理を実行する "To Do List" Web API を構築します。

* 保護対象の API を指定する。
* Web API 呼び出しに有効なアクセス トークンが含まれているかどうかを検証する。

このクイック スタートでは、To Do List API を構築し、次の操作の方法を説明します。

1. アプリケーションを Azure AD に登録する。
2. OWIN 認証パイプラインを使用するようにアプリを設定する。
3. Web API を呼び出すようにクライアント アプリケーションを構成する。

## <a name="prerequisites"></a>前提条件

最初に、以下の前提条件を完了します。

* [アプリのスケルトンをダウンロード](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)する。 両方とも、Visual Studio 2013 ソリューションです。
* アプリケーションの登録先となる Azure AD テナントを持つ。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](quickstart-create-new-tenant.md)」を参照して取得してください。

## <a name="step-1-register-an-application-with-azure-ad"></a>手順 1:アプリケーションを Azure AD に登録する

アプリケーションのセキュリティ保護を支援するには、まず、テナントでアプリケーションを作成し、Azure AD にいくつかの重要な情報を提供する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ページ右上隅にあるアカウントを選択して Azure AD テナントを選択し、 **[ディレクトリの切り替え]** ナビゲーションを選択してから、適切なテナントを選択します。
    * アカウントの下の Azure AD テナントが 1 つのみの場合、または適切な Azure AD テナントを既に選択している場合は、この手順をスキップします。

3. 左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。
4. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
5. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの名前を入力します。
**[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
6. **[リダイレクト URI]** セクションで **[Web]** プラットフォームを選択し、値を `https://localhost:44321/` (Azure AD がトークンを返す先の場所) に設定します。
7. 終了したら、 **[登録]** を選択します。 アプリの **[概要]** ページで、 **[Application (client) ID]\(アプリケーション (クライアント) ID\)** の値を書き留めます。
6. **[API の公開]** を選択し、 **[設定]** をクリックしてアプリケーション ID URI を更新します。 テナント固有の識別子を入力します。 たとえば、「 `https://contoso.onmicrosoft.com/TodoListService`」のように入力します。
7. 構成を保存します。 今後の手順でクライアント アプリケーションも登録する必要があるため、ポータルは開いたままにします。

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>手順 2:OWIN 認証パイプラインを使用するようにアプリを設定する

受信した要求およびトークンを検証するために、アプリケーションを Azure AD と通信するように設定する必要があります。

1. まず、ソリューションを開き、パッケージ マネージャー コンソールを使用して、OWIN ミドルウェア NuGet パッケージを TodoListService プロジェクトに追加します。

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. OWIN Startup クラスを TodoListService プロジェクト ( `Startup.cs`) に追加します。  プロジェクトを右クリックし、 **[追加]、[新しい項目]** の順に選択して、**OWIN** を検索します。 アプリが起動すると、OWIN ミドルウェアは `Configuration(…)` メソッドを呼び出します。

3. クラスの宣言を `public partial class Startup` に変更します。 このクラスの部分は、別のファイルに実装済みです。 `Configuration(…)` メソッドで、Web アプリ用の認証を設定する `ConfgureAuth(…)` への呼び出しを作成します。

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. ファイル `App_Start\Startup.Auth.cs` を開いて、`ConfigureAuth(…)` メソッドを実装します。 `WindowsAzureActiveDirectoryBearerAuthenticationOptions` で指定するパラメーターは、アプリが Azure AD と通信するための調整役として機能します。 それらを使用するには、`System.IdentityModel.Tokens` 名前空間のクラスを使用する必要があります。

    ```csharp
    using System.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. 次に、`[Authorize]` 属性を使用して、JSON Web トークン (JWT) ベアラー認証によりコントローラーとアクションの保護を支援します。 認証タグを使用して `Controllers\TodoListController.cs` クラスを修飾します。これによってユーザーは、そのページにアクセスする前にサインインすることを強制されます。

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    承認呼び出し元が `TodoListController` API の 1 つを正常に呼び出すときに、呼び出し元についての情報にアクセスするアクションが必要な場合があります。 OWIN は `ClaimsPrincpal` オブジェクトを通して、ベアラー トークン内のクレームにアクセスできるようにします。  

6. Web API の共通する要件は、トークンで提示される "範囲" を検証して、To Do List Service へのアクセスに必要なアクセス許可に、ユーザーが同意していることを確認することです。

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. TodoListService プロジェクトのルートにある `web.config` ファイルを開いて、`<appSettings>` セクションに構成値を入力します。
    * `ida:Tenant` には、Azure AD テナントの名前 (contoso.onmicrosoft.com など) を指定します。
    * `ida:Audience` には、Azure Portal で入力したアプリケーションのアプリ ID URI を指定します。

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>手順 3:クライアント アプリケーションを構成してサービスを実行する

To Do List Service の動作を確認できるようにするには、AAD からトークンを取得してサービスを呼び出せるように To Do List クライアントを構成する必要があります。

1. [Azure Portal](https://portal.azure.com) に戻ります。
1. Azure AD テナントで新しいアプリケーション登録を作成します。  アプリケーションをユーザーに説明する **[名前]** を入力し、 **[リダイレクト URI]** の値として `http://TodoListClient/` と入力し、ドロップダウンから **[パブリック クライアント (モバイルとデスクトップ)]** を選択します。
1. 登録が完了すると、Azure AD によって、一意のアプリケーション ID がアプリに割り当てられます。 この値は次の手順で必要になるので、アプリケーション ページからコピーします。
1. **[API のアクセス許可]** 、 **[アクセス許可の追加]** の順に選択します。  To Do List Service を見つけて選択し、**user_impersonation Access TodoListService** アクセス許可を **[デリゲートされたアクセス許可]** の下に追加し、 **[アクセス許可の追加]** を選択します。
1. Visual Studio で、TodoListClient プロジェクトの `App.config` を開いて、`<appSettings>` セクションに構成値を入力します。

    * `ida:Tenant` には、Azure AD テナントの名前 (contoso.onmicrosoft.com など) を指定します。
    * `ida:ClientId` には、Azure Portal からコピーしたアプリ ID を指定します。
    * `todo:TodoListResourceId` には、Azure Portal で入力した To Do List Service のアプリ ID URI を指定します。

1. 各プロジェクトをクリーンアップし、ビルドして実行します。
1. *.onmicrosoft.com ドメインを使用して、テナントに新しいユーザーを作成します (作成していない場合)。
1. そのユーザーとして To Do List クライアントにサインインし、いくつかのタスクを To-Do リストに追加します。

## <a name="next-steps"></a>次の手順

* 参考として、[GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) から完全なサンプル (環境に応じた構成値を除く) をダウンロードできます。 ここからは、その他の ID シナリオに進むことができます。
