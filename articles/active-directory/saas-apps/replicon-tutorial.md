---
title: チュートリアル:Azure Active Directory と Replicon の統合 | Microsoft Docs
description: Azure Active Directory と Replicon の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: be006cd49041a33c13b5cc5c459a1d54c46c3b47
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727609"
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>チュートリアル:Azure Active Directory と Replicon の統合

このチュートリアルでは、Replicon と Azure Active Directory (Azure AD) を統合する方法について説明します。
Replicon と Azure AD の統合には、次の利点があります。

* Replicon にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Replicon に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Replicon と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Replicon でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Replicon では、**SP** によって開始される SSO がサポートされます

## <a name="adding-replicon-from-the-gallery"></a>ギャラリーからの Replicon の追加

Azure AD への Replicon の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Replicon を追加する必要があります。

**ギャラリーから Replicon を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Replicon**」と入力し、結果パネルで **[Replicon]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Replicon](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Replicon で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Replicon 内の関連ユーザー間にリンク関係が確立されている必要があります。

Replicon で Azure AD のシングル サインオンを構成してテストするには、次のビルド ブロックを完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Replicon シングル サインオンの構成](#configure-replicon-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Replicon テスト ユーザーの作成](#create-replicon-test-user)** - Replicon で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Replicon で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Replicon** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Replicon のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://na2.replicon.com/<companyname>/saml2/sp-sso/post` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://global.replicon.com/<companyname>` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://global.replicon.com/!/saml2/<companyname>/sso/post` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Replicon クライアント サポート チーム](https://www.replicon.com/customerzone/contact-support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Replicon のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-replicon-single-sign-on"></a>Replicon シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Replicon 企業サイトに管理者としてサインインします。

2. SAML 2.0 を構成するには、次の手順に従います。

    ![Enable SAML authentication](./media/replicon-tutorial/ic777805.png "Enable SAML authentication")

    a. **[EnableSAML Authentication2]** ダイアログを表示するには、URL の企業キーの後に次のパスを追加します。`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * 完全な URL のスキーマを次に示します。`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. **+** をクリックして、 **[v20Configuration]** セクションを展開します。

   c. **+** をクリックして、 **[metaDataConfiguration]** セクションを展開します。

   d. **[Choose File]** をクリックして、ID プロバイダー メタデータ XML ファイルを選択し、 **[送信]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Replicon へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Replicon]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Replicon]** を選択します。

    ![アプリケーションの一覧の Replicon のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-replicon-test-user"></a>Replicon テスト ユーザーの作成

このセクションの目的は、Replicon で Britta Simon というユーザーを作成することです。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. Web ブラウザー ウィンドウで、Replicon 企業サイトに管理者としてサインインします。

2. **[Administration] \> [Users]** に移動します。

    ![ユーザー](./media/replicon-tutorial/ic777806.png "Users")

3. **[+Add User]** をクリックします。

    ![ユーザーの追加](./media/replicon-tutorial/ic777807.png "Add User")

4. **[User Profile]** セクションで、次の手順に従います。

    ![User profile](./media/replicon-tutorial/ic777808.png "User profile")

    a. **[Login Name]\(ログイン名\)** ボックスに、プロビジョニングする Azure AD ユーザーの Azure AD メール アドレスを入力します (例: `BrittaSimon\@contoso.com`)。

    b. **[Authentication Type]** として **[SSO]** を選択します。

    c. **[Department]** テキストボックスに、ユーザーの所属を入力します。

    d. **[Employee Type]** として **[Administrator]** を選択します。

    e. **[Save User Profile]** をクリックします。

> [!NOTE]
> Replicon から提供されている他の Replicon ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Replicon] タイルをクリックすると、SSO を設定した Replicon に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)