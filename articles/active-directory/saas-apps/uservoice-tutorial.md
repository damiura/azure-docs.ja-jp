---
title: チュートリアル:Azure Active Directory と UserVoice の統合 | Microsoft Docs
description: Azure Active Directory と UserVoice の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: dbd7189b1761a9ea88ce32dae3d7b45a88301ff6
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905631"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>チュートリアル:Azure Active Directory と UserVoice の統合

このチュートリアルでは、UserVoice と Azure Active Directory (Azure AD) を統合する方法について説明します。
UserVoice と Azure AD の統合には、次の利点があります。

* UserVoice にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して UserVoice に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

UserVoice と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* UserVoice でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* UserVoice では、**SP** によって開始される SSO がサポートされます

## <a name="adding-uservoice-from-the-gallery"></a>ギャラリーからの UserVoice の追加

Azure AD への UserVoice の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に UserVoice を追加する必要があります。

**ギャラリーから UserVoice を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**UserVoice**」と入力し、結果ウィンドウで **[UserVoice]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の UserVoice](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、UserVoice で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと UserVoice 内の関連ユーザー間にリンク関係が確立されている必要があります。

UserVoice で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[UserVoice シングル サインオンの構成](#configure-uservoice-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[UserVoice のテスト ユーザーの作成](#create-uservoice-test-user)** - UserVoice で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

UserVoice で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **UserVoice** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[UserVoice のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<tenantname>.UserVoice.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[UserVoice クライアント サポート チーム](https://www.uservoice.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、**[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

6. **[SAML 署名証明書]** セクションで **[Thumbprint]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

7. **[UserVoice のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-uservoice-single-sign-on"></a>UserVoice シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、UserVoice 企業サイトに管理者としてサインインします。

2. 上部のツール バーの **[設定]** をクリックし、メニューから **[Web ポータル]** を選択します。
   
    ![アプリ側の [設定] セクション](./media/uservoice-tutorial/ic777519.png "設定")

3. **[Web ポータル]** タブの **[ユーザー認証]** セクションで、**[編集]** をクリックして **[ユーザー認証の編集]** ダイアログ ページを開きます。
   
    ![[Web ポータル] タブ](./media/uservoice-tutorial/ic777520.png "Web ポータル")

4. **[ユーザー認証の編集]** ダイアログ ページで、次の手順に従います。
   
    ![ユーザー認証の編集](./media/uservoice-tutorial/ic777521.png "Edit user authentication")
   
    a. **[シングル サインオン]** をクリックします。
 
    b. Azure portal からコピーした **[ログイン URL]** の値を **[SSO Remote Sign-In]\(SSO リモート サインイン\)** ボックスに貼り付けます。

    c. Azure portal からコピーした **[ログアウト URL]** の値を **[SSO Remote Sign-Out]\(SSO リモート サインアウト\) ボックス**に貼り付けます。
 
    d. Azure Portal からコピーした **[拇印]** の値を **[Current certificate SHA1 fingerprint]\(現在の証明書 SHA1 のフィンガープリント\)** ボックスに貼り付けます。
    
    e. **[認証設定の保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に UserVoice へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[UserVoice]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[UserVoice]** を選択します。

    ![アプリケーションの一覧の UserVoice リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-uservoice-test-user"></a>UserVoice のテスト ユーザーの作成

Azure AD ユーザーが UserVoice にサインインできるようにするには、そのユーザーを UserVoice にプロビジョニングする必要があります。 UserVoice の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. **UserVoice** テナントにサインインします。

2. **[設定]** に移動します。
   
    ![設定](./media/uservoice-tutorial/ic777811.png "Settings")

3. **[全般]** をクリックします。

4. **[エージェントとアクセス許可]** をクリックします。
   
    ![エージェントとアクセス許可](./media/uservoice-tutorial/ic777812.png "Agents and permissions")

5. **[管理者の追加]** をクリックします。
   
    ![管理者の追加](./media/uservoice-tutorial/ic777813.png "Add admins")

6. **[管理者の招待]** ダイアログで、次の手順を実行します。
   
    ![管理者の招待](./media/uservoice-tutorial/ic777814.png "Invite admins")
   
    a. [電子メール] ボックスに、プロビジョニングするアカウントの電子メール アドレスを入力し、**[追加]** をクリックします。
   
    b. **[招待]** をクリックします。

> [!NOTE]
> UserVoice から提供されている他の UserVoice ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [UserVoice] タイルをクリックすると、SSO を設定した UserVoice に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

