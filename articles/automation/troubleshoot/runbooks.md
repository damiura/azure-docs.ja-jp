---
title: Azure Automation Runbook のエラーをトラブルシューティングする
description: Azure Automation Runbook のエラーをトラブルシューティングする方法を説明します。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 65de80004dd05e3eb29f3313bc17405c40450d7a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66397137"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook のエラーをトラブルシューティングする

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Azure Automation Runbook の使用時に発生する認証エラー

### <a name="sign-in-failed"></a>シナリオ:Azure アカウントにサインインできない

#### <a name="issue"></a>問題

`Add-AzureAccount` または `Connect-AzureRmAccount` コマンドレットを使用するときに、次のエラーを受け取ります。
:

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>原因

このエラーは、資格情報資産名が無効な場合に発生します。 このエラーは、Automation 資格情報資産のセットアップに使用したユーザー名とパスワードが無効な場合にも発生する可能性があります。

#### <a name="resolution"></a>解決策

次の手順で原因を突き止めます。  

1. 特殊文字が使用されていないことを確認します。 このような特殊文字とは、Azure に接続するために使用している Automation 資格情報資産名に含まれる **\@** 文字などです。  
2. ローカル PowerShell ISE エディターで、Azure Automation に保存されているユーザー名とパスワードを使用できることを確認します。 PowerShell ISE で次のコマンドレットを実行して、ユーザー名とパスワードが正しいことを確認できます。  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. ローカルで認証に失敗した場合、Azure Active Directory 資格情報が正しく設定されていないことになります。 Azure Active Directory アカウントを正しく設定する方法については、「 [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 」 (Azure Active Directory を使用して Azure を認証する) というブログ投稿を参照してください。  

4. 一時的なエラーと思われる場合は、認証をより強固にするために認証ルーチンに再試行ロジックを追加してみてください。

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>シナリオ:Azure サブスクリプションが見つからない

#### <a name="issue"></a>問題

`Select-AzureSubscription` または `Select-AzureRmSubscription` コマンドレットを使用するときに、次のエラーを受け取ります。

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

このエラーは、次の場合に発生する可能性があります。

* サブスクリプション名が有効ではない

* サブスクリプションの詳細を取得しようとしている Azure Active Directory ユーザーが、サブスクリプションの管理者として構成されていない。

#### <a name="resolution"></a>解決策

次の手順に従って、Azure で認証されていること、および選択しようとしているサブスクリプションにアクセスできることを確認します。  

1. スクリプトがスタンドアロンで機能することを確認するには、Azure Automation の外部でそれをテストします。
2. `Select-AzureSubscription` コマンドレットを実行する前に、必ず `Add-AzureAccount` コマンドレットを実行してください。 
3. Runbook の先頭に `Disable-AzureRmContextAutosave –Scope Process` を追加します。 このコマンドレットにより、すべての資格情報が現在の Runbook の実行にのみ適用されるようになります。
4. それでもエラー メッセージが表示される場合は、`Add-AzureAccount` コマンドレットの後に **-AzureRmContext** パラメーターを追加してコードを変更してから、コードを実行します。

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>シナリオ:多要素認証が有効になっているために Azure に対する認証が失敗した

#### <a name="issue"></a>問題

Azure のユーザー名とパスワードで Azure に対して認証するときに、次のエラーを受け取ります。

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>原因

Azure アカウントに多要素認証を設定している場合、Azure に対する認証に Azure Active Directory ユーザーを使うことはできません。 代わりに、証明書またはサービス プリンシパルを利用して Azure に対して認証する必要があります。

#### <a name="resolution"></a>解決策

Azure クラシック デプロイ モデルのコマンドレットで証明書を使用する方法については、[証明書を作成し、追加して Azure サービスを管理する](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)方法に関するページを参照してください。 Azure Resource Manager コマンドレットでサービス プリンシパルを使用する方法については、[Azure ポータルでサービス プリンシパルを作成する](../../active-directory/develop/howto-create-service-principal-portal.md)方法に関する記事と [Azure Resource Manager でサービス プリンシパルを認証する](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)方法に関する記事を参照してください。

## <a name="common-errors-when-working-with-runbooks"></a>Runbook の使用時に発生する一般的なエラー

### <a name="child-runbook-object"></a>出力ストリームに単純なデータ型以外のオブジェクトが含まれている場合、子 Runbook でエラーが返される

#### <a name="issue"></a>問題

`-Wait` スイッチを使用して子 Runbook を呼び出し、出力ストリームにオブジェクトが含まれていると、次のエラーを受け取ります。

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>原因

既知の問題として、出力ストリームにオブジェクトが含まれている場合、[Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) では出力ストリームを正しく処理できません。

#### <a name="resolution"></a>解決策

この問題を解決するには、代わりにポーリング ロジックを実装し、[Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) コマンドレットを使用してその出力を取得することをお勧めします。 このロジックのサンプルは、次の例で定義されています。

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>シナリオ:ジョブ ストリームで get_SerializationSettings メソッドについてのエラーが表示される

#### <a name="issue"></a>問題

ジョブ ストリームで、次のメッセージと共に Runbook のエラーが表示されます。

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type 
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>原因

このエラーは、Runbook で AzureRM と Az の両方のコマンドレットを使用することで発生します。 `AzureRM` のインポート前に `Az` をインポートすると発生します。

#### <a name="resolution"></a>解決策

Az と AzureRM のコマンドレットは同じ Runbook にインポートして使用できません。Azure Automation での Az のサポートに関する詳細については、[Azure Automation での Az モジュールのサポート](../az-modules.md)に関するページを参照してください。

### <a name="task-was-cancelled"></a>シナリオ:Runbook がエラーで失敗:タスクはキャンセルされました

#### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>原因

以前のバージョンの Azure モジュールを使用すると、このエラーが発生する可能性があります。

#### <a name="resolution"></a>解決策

このエラーは、Azure モジュールを最新のバージョンに更新すると解決できます。

Automation アカウントで、 **[モジュール]** をクリックし、 **[Azure モジュールの更新]** をクリックします。 この更新には、失敗した Runbook の再実行を完了してから約 15 分かかります。 モジュールの更新の詳細については、「[Azure Automation で Azure モジュールを更新する](../automation-update-azure-modules.md)」を参照してください。

### <a name="runbook-auth-failure"></a>シナリオ:複数のサブスクリプションを処理するときに Runbook が失敗する

#### <a name="issue"></a>問題

`Start-AzureRmAutomationRunbook` を使用して Runbook を実行したときに、Runbook が Azure リソースを管理できません。

#### <a name="cause"></a>原因

Runbook が、実行中に正しいコンテキストを使用していません。

#### <a name="resolution"></a>解決策

複数のサブスクリプションを使用している場合は、Runbook を呼び出したときにサブスクリプションのコンテキストが失われることがあります。 サブスクリプションのコンテキストが Runbook に渡されるようにするには、コマンドレットに `AzureRmContext` パラメーターを追加してそれにコンテキストを渡します。 また、使用する資格情報が現在の Runbook のみに使用されるように、**Process** スコープを指定して `Disable-AzureRmContextAutosave` コマンドレットを使用することもお勧めします。

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>シナリオ:不足しているコマンドレットにより、Runbook が失敗する

#### <a name="issue"></a>問題

Runbook が、次の例のようなエラーで失敗します。

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>原因

このエラーは、次のいずれかの理由により発生する可能性があります。

1. コマンドレットを含むモジュールが、Automation アカウントにインポートされていない
2. コマンドレットを含むモジュールはインポートされているが、最新ではない

#### <a name="resolution"></a>解決策

このエラーは、次のタスクのいずれかを実行して解決できる場合があります。

モジュールが Azure モジュールの場合、Automation アカウントでモジュールを更新する方法について、「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」を参照してください。

個別のモジュールの場合、Automation アカウントにモジュールがインポートされていることを確認します。

### <a name="job-attempted-3-times"></a>シナリオ:Runbook ジョブの開始を 3 回試行したが、開始できない

#### <a name="issue"></a>問題

Runbook がエラーで失敗します。

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>原因

このエラーは、次のいずれかの問題のために発生します。

1. メモリの制限。 サンドボックスに割り当てられるメモリの制限については、[Automation サービスの制限](../../azure-subscription-service-limits.md#automation-limits)に関するセクションを参照してください。 400 MB を超えるメモリを使用すると、ジョブが失敗することがあります。

2. ネットワーク ソケット。 「[Automation サービスの制限](../../azure-subscription-service-limits.md#automation-limits)」の説明のように、Azure サンド ボックスの同時ネットワーク ソケット数は 1,000 に制限されています。

3. モジュールに互換性がない。 このエラーは、モジュールの依存関係が正しくない場合に発生することがあります。この場合は通常、「Command not found (コマンドが見つかりません)」または「Cannot bind parameter (パラメーターをバインドできません)」というメッセージが Runbook から返されます。

4. Runbook で、Azure サンドボックス内で実行されている Runbook 内の実行可能ファイルまたはサブプロセスを呼び出そうとしました。 このシナリオは Azure サンドボックスではサポートされていません。

5. Runbook により、例外データの出力ストリームへの書き込みが過剰に試行されました。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。

* メモリの制限内で問題を解決するために推奨される方法としては、複数の Runbook 間でワークロードを分割する、メモリ内のデータと同量のデータを処理しない、Runbook からの不要な出力を書き込まない、PowerShell Workflow Runbook に書き込むチェックポイントの数を検討する、などがあります。 `$myVar.clear()` などの clear メソッドを使用して変数をクリアし、`[GC]::Collect()` を使用してガベージ コレクションをすぐに実行できます。 これにより、実行時の Runbook のメモリ専有領域が小さくなります。

* 「[Azure Automation の Azure PowerShell モジュールを更新する方法](../automation-update-azure-modules.md)」の手順に従って Azure モジュールを更新します。  

* 別の解決策は、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を実行することです。 ハイブリッド worker には、Azure サンドボックスのようなメモリとネットワークの制限はありません。

* Runbook 内のプロセス (.exe や subprocess.call など) を呼び出す必要がある場合は、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を実行する必要があります。

* ジョブ出力ストリームでは 1 MB の制限があります。 実行可能ファイルまたはサブプロセスへの呼び出しを try/catch ブロックで必ず囲みます。 例外がスローされる場合は、その例外から Automation 変数にメッセージを書き込みます。 これにより、ジョブ出力ストリームに書き込まれなくなります。

### <a name="fails-deserialized-object"></a>シナリオ:逆シリアル化されたオブジェクトであるため、Runbook が失敗する

#### <a name="issue"></a>問題

Runbook がエラーで失敗します。

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>原因

Runbook が PowerShell ワークフローの場合、ワークフローが中断された場合に Runbook の状態を維持できるように、複雑なオブジェクトが逆シリアル化形式で保存されます。

#### <a name="resolution"></a>解決策

次の 3 つの解決策のいずれでもこの問題は解決されます。

1. コマンドレット間で複雑なオブジェクトをパイプ処理する場合、これらのコマンドレットを InlineScript でラップします。
2. オブジェクト全体を渡すのではなく、複雑なオブジェクトから、必要な名前または値を渡します。
3. PowerShell ワークフロー Runbook ではなく PowerShell Runbook を使用します。

### <a name="runbook-fails"></a>シナリオ:Runbook が失敗するが、ローカルで実行すると動作する

#### <a name="issue"></a>問題

Runbook として実行すると失敗するスクリプトが、ローカルで実行したときは動作します。

#### <a name="cause"></a>原因

次のいずれかの理由により、Runbook として実行するとスクリプトが失敗する可能性があります。

1. 認証の問題
2. 必要なモジュールがインポートされていないか、または期限切れです。
3. スクリプトでユーザーの操作が求められている場合があります。
4. 一部のモジュールで、Windows コンピューター上にライブラリが存在することが想定されています。 これらのライブラリが、サンドボックスに存在しない可能性があります。
5. 一部のモジュールが、サンドボックスで使用できるものとは異なるバージョンの .NET に依存します。

#### <a name="resolution"></a>解決策

次のいずれかの解決策でこの問題を解決できます。

1. 適切に [Azure で認証されている](../manage-runas-account.md)ことを確認します。
2. [Azure モジュールがインポートされて最新の状態である](../automation-update-azure-modules.md)ことを確認します。
3. どのコマンドレットでも情報の入力が求められていないことを確認します。 この動作は、Runbook ではサポートされていません。
4. モジュールの一部が、モジュールに含まれていないものに依存しているかどうかを確認します。
5. Azure サンドボックスでは .NET Framework 4.7.2 が使用されており、それより後のバージョンを使用しているモジュールは動作しません。 この場合は、[Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) を使用する必要があります

これらの解決策のいずれでも問題が解決しない場合は、[ジョブのログ](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)で詳細を調べて、Runbook が失敗する原因を確認してください。

### <a name="quota-exceeded"></a>シナリオ:割り当てられたクォータを超えているために Runbook ジョブが失敗した

#### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>原因

ジョブの実行がアカウントの 500 分の無料クォータを超えるとこのエラーが発生します。 このクォータは、すべての種類のジョブ実行タスクに適用されます。 これらのタスクでは、ジョブのテスト、ポータルからのジョブの開始、Webhook を使用したジョブの実行、Azure portal またはデータセンターを使用して実行するジョブのスケジュール、などが行われている可能性があります。 Automation の料金については、「[Automation の料金](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。

#### <a name="resolution"></a>解決策

毎月 500 分以上の処理を使用する場合、サブスクリプションを Free レベルから Basic レベルに変更する必要があります。 次の手順で Basic レベルにアップグレードできます。  

1. Azure サブスクリプションにサインインします。  
2. アップグレードする Automation アカウントを選択します。  
3. **[設定]**  >  **[価格]** の順にクリックします。
4. ページ下部にある **[有効]** をクリックして、アカウントを **Basic** レベルにアップグレードします。

### <a name="cmdlet-not-recognized"></a>シナリオ:Runbook の実行時にコマンドレットが認識されない

#### <a name="issue"></a>問題

Runbook ジョブがエラーで失敗します。

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>原因

このエラーは、Runbook で使用しているコマンドレットを PowerShell エンジンが見つけられないときに発生します。 このエラーの原因としては、コマンドレットを含むモジュールがアカウントにない、Runbook 名に名前の競合がある、コマンドレットが別のモジュールにも存在し、Automation が名前を解決できないなどが考えられます。

#### <a name="resolution"></a>解決策

次の解決策のいずれでもこの問題は解決されます。  

* コマンドレット名を正しく入力していることを確認します。  
* Automation アカウントにコマンドレットが存在し、競合がないことを確認します。 コマンドレットの存在を確認するには、Runbook を編集モードで開き、ライブラリで見つけるコマンドレットを検索し、`Get-Command <CommandName>` を実行します。 コマンドレットがアカウントで利用できることと他のコマンドレットや Runbook と名前が競合しないことを確認したら、それをキャンバスに追加し、Runbook に設定されている有効なパラメーターを使用していることを確認します。  
* 名前が競合し、コマンドレットが 2 つの異なるモジュールで利用できる場合、コマンドレットの完全修飾名を利用することで、この問題を解決できます。 たとえば、**ModuleName\CmdletName** を使用できます。  
* ハイブリッド worker グループでオンプレミスの runbook を実行する場合は、モジュールとコマンドレットがハイブリッド worker をホストしているマシンにインストールされていることを確認します。

### <a name="long-running-runbook"></a>シナリオ:実行時間の長い Runbook が完了しない

#### <a name="issue"></a>問題

Runbook は、3 時間実行すると**停止**状態になります。 次のエラーを受け取る可能性もあります。

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

これは、Azure Automation 内のプロセスの "fair share" 監視のための、Azure サンドボックスでの設計による動作です。 時間が 3 時間を超えると、Runbook は fair share によって自動的に停止されます。 fair-share の時間の制限を超える Runbook の状態は、Runbook の種類によって異なります。 PowerShell Runbook と Python Runbook は、**停止**状態に設定されます。 PowerShell Workflow Runbook は、**失敗**に設定されます。

#### <a name="cause"></a>原因

Runbook が、Azure サンドボックスの fair share によって許可されている 3 時間の制限を超えて実行しました。

#### <a name="resolution"></a>解決策

推奨される解決策の 1 つは、[Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) で Runbook を実行することです。

Hybrid Worker には、Azure サンドボックスのような [fair share](../automation-runbook-execution.md#fair-share) による 3 時間の Runbook 制限はありません。 予期しないローカル インフラストラクチャの問題が発生した場合の再起動動作をサポートするには、Hybrid Runbook Worker 上で実行される Runbook を開発する必要があります。

もう 1 つのオプションは、[子 Runbook](../automation-child-runbooks.md) を作成して Runbook を最適化することです。 複数のリソースに対して同じ機能を実行する Runbook の場合は (複数のデータベースに対するデータベース操作など)、その機能を子 Runbook に移動することができます。 これらの各子 Runbook は別々のプロセスで並列に実行されます。 この動作によって、親 Runbook の完了までにかかる合計時間は減ります。

子 Runbook のシナリオを実現する PowerShell コマンドレットは次のとおりです。

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook): このコマンドレットを使用すると、Runbook を起動し、パラメーターを Runbook に渡すことができます

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob): 子 Runbook の完了後に実行する必要のある操作がある場合、このコマンドレットを使用して各子のジョブの状態を確認できます。

### <a name="expired webhook"></a>シナリオ:状態:Webhook を呼び出すときに 400 Bad Request が発生する

#### <a name="issue"></a>問題

Azure Automation Runbook の Webhook を呼び出そうとすると、次のエラーが表示されます。

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>原因

呼び出そうとしている Webhook が、無効または期限切れです。

#### <a name="resolution"></a>解決策

Webhook が無効な場合は、Azure portal から Webhook を再度有効にすることができます。 Webhook の期限が切れたら、Webhook を削除して再作成する必要があります。 Webhook がまだ期限切れでない場合にのみ、[Webhook を更新](../automation-webhooks.md#renew-webhook)できます。

### <a name="429"></a>シナリオ:429:The request rate is currently too large. Please try again (現在、要求レートが大きすぎます。もう一度実行してください)

#### <a name="issue"></a>問題

`Get-AzureRmAutomationJobOutput` コマンドレットを実行すると、次のエラー メッセージを受け取ります。

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>原因

このエラーは、多数の[冗長ストリーム](../automation-runbook-output-and-messages.md#verbose-stream)がある Runbook からジョブ出力を取得するときに発生する可能性があります。

#### <a name="resolution"></a>解決策

このエラーを解決するには、次の 2 つの方法があります。

* Runbook を編集し、出力されるジョブ ストリームの数を減らします。
* コマンドレットを実行するときに取得されるストリーム数を減らします。 この動作の後に、`Get-AzureRmAutomationJobOutput` コマンドレットに `-Stream Output` パラメーターを指定して出力ストリームのみを取得することができます。 

### <a name="cannot-invoke-method"></a>シナリオ:PowerShell ジョブが次のエラーで失敗する: メソッドを呼び出せません

#### <a name="issue"></a>問題

Azureで実行されている Runbook で PowerShell ジョブを開始しているときに、次のエラー メッセージが表示されます。

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>原因

Azure で実行された Runbook で PowerShell ジョブを開始すると、このエラーが発生することがあります。 この動作が発生するのは、Azure サンドボックスで実行された Runbook が[完全言語モード](/powershell/module/microsoft.powershell.core/about/about_language_modes)で実行されないことがあるためです。

#### <a name="resolution"></a>解決策

このエラーを解決するには、次の 2 つの方法があります。

* `Start-Job` の代わりに `Start-AzureRmAutomationRunbook` を使用して Runbook を開始する
* お使いの Runbook でこのエラー メッセージが表示された場合、それを Hybrid Runbook Worker で実行する

この動作や、Azure Automation Runbook の他の動作の詳細については、「[Runbook の動作](../automation-runbook-execution.md#runbook-behavior)」を参照してください。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
