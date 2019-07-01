---
title: IIS での ASP.NET Core のトラブルシューティング
author: guardrex
description: ASP.NET Core アプリのインターネット インフォメーション サービス (IIS) の展開に関する問題を診断する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313653"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>IIS での ASP.NET Core のトラブルシューティング

作成者: [Luke Latham](https://github.com/guardrex)

この記事では、[インターネット インフォメーション サービス (IIS)](/iis) を使用してホストしている場合に、ASP.NET Core アプリの起動時に関する問題を診断する方法について説明します。 この記事の情報は、Windows Server および Windows デスクトップ上の IIS でのホスティングに適用されます。

その他のトラブルシューティング トピック:

* Azure App Service では、アプリをホストするために [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と IIS も使用されています。 Azure App Service に特に関連するトラブルシューティング上のアドバイスについては、<xref:host-and-deploy/azure-apps/troubleshoot> をご覧ください。
* <xref:fundamentals/error-handling> では、ローカル システム上で開発しているときに発生する ASP.NET Core アプリのエラーを処理する方法について説明します。
* [Visual Studio を使用したデバッグ方法の学習](/visualstudio/debugger/getting-started-with-the-debugger)に関するページでは、Visual Studio デバッガーの機能を紹介しています。
* [Visual Studio Code でのデバッグ](https://code.visualstudio.com/docs/editor/debugging)に関するページでは、Visual Studio Code に組み込まれているデバッグのサポートについて説明します。

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a>アプリの起動エラーのトラブルシューティング

### <a name="enable-the-aspnet-core-module-debug-log"></a>ASP.NET Core モジュールのデバッグ ログを有効にする

ASP.NET Core モジュールのデバッグ ログを有効にするには、次のハンドラー設定をアプリの *web.config* ファイルに追加します。

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。

### <a name="application-event-log"></a>アプリケーション イベント ログ

アプリケーション イベント ログにアクセスします。

1. [スタート] メニューを開き、**イベント ビューアー**を検索し、**イベント ビューアー** アプリを選択します。
1. **イベント ビューアー**で **[Windows ログ]** ノードを開きます。
1. **[Application]** を選択して、アプリケーション イベント ログを開きます。
1. 失敗したアプリに関連するエラーを検索します。 エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。

### <a name="run-the-app-at-a-command-prompt"></a>コマンド プロンプトでアプリを実行する

多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。 ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。

#### <a name="framework-dependent-deployment"></a>フレームワークに依存する展開

アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:

1. コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。 コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。
1. エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。
1. アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。 既定のホストと post を使用して `http://localhost:5000/` に要求を行います。 アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。

#### <a name="self-contained-deployment"></a>自己完結型の展開

アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:

1. コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。 コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。
1. エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。
1. アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。 既定のホストと post を使用して `http://localhost:5000/` に要求を行います。 アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET Core モジュールの stdout ログ

stdout ログを有効にして表示するには:

1. ホスティング システム上のサイトの展開フォルダーに移動します。
1. *logs* フォルダーが存在しない場合は、フォルダーを作成します。 MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。
1. *web.config* ファイルを編集します。 **stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。 パスの `stdout` はログ ファイル名のプレフィックスです。 ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。 ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。
1. アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。
1. 更新した *web.config* ファイルを保存します。
1. アプリに対して要求します。
1. *logs* フォルダーに移動します。 最新の stdout ログを探して開きます。
1. ログのエラーを調べます。

> [!IMPORTANT]
> トラブルシューティングが完了したら、stdout ログを無効にします。

1. *web.config* ファイルを編集します。
1. **stdoutLogEnabled** を `false` に設定します。
1. ファイルを保存します。

> [!WARNING]
> stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。 ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。
>
> ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。 詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。

## <a name="enable-the-developer-exception-page"></a>開発者例外ページを有効にする

開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) に追加することができます。 アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。 トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。 *web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。

## <a name="common-startup-errors"></a>起動時の一般的なエラー

以下を参照してください。<xref:host-and-deploy/azure-iis-errors-reference> このリファレンス トピックでは、アプリの起動を妨げる一般的な問題のほとんどが説明されています。

## <a name="obtain-data-from-an-app"></a>アプリからデータを取得する

アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。 詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。

## <a name="create-a-dump"></a>ダンプを作成する

"*ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。

### <a name="app-crashes-or-encounters-an-exception"></a>アプリのクラッシュまたは例外の発生

[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。

1. `c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。 アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。
1. [EnableDumps PowerShell スクリプト](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) を実行します。
   * アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. クラッシュが発生する条件の下でアプリを実行します。
1. クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1)を実行します。
   * アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。

     ```console
     .\DisableDumps w3wp.exe
     ```

   * アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。

     ```console
     .\DisableDumps dotnet.exe
     ```

アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。 PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。

> [!WARNING]
> クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>アプリが起動時または正常な実行中にハングまたは失敗する

アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。

### <a name="analyze-the-dump"></a>ダンプを分析する

ダンプはいくつかの方法で分析できます。 詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。

## <a name="remote-debugging"></a>リモート デバッグ

Visual Studio ドキュメントの「[Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)」(Visual Studio 2017 でリモート IIS コンピューター上の ASP.NET Core をリモート デバッグする) を参照してください。

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/) は、IIS でホストされているアプリからのテレメトリを提供し、エラー ログやレポート機能を備えています。 Application Insights は、アプリのログ機能が使用可能になってアプリが起動した後で発生したエラーのみをレポートできます。 詳細については、「[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)」を参照してください。

## <a name="additional-advice"></a>その他の注意事項

開発コンピューター上の .NET Core SDK またはアプリ内のパッケージ バージョンのいずれかをアップグレードした直後に、機能しているアプリが失敗することがあります。 場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。 これらの問題のほとんどは、次の手順で解決できます。

1. *bin* フォルダーと *obj* フォルダーを削除します。
1. *%UserProfile%\\.nuget\\packages* と *%LocalAppData%\\Nuget\\v3-cache* のパッケージ キャッシュをクリアします。
1. プロジェクトを復元してリビルドします。
1. アプリを再展開する前に、サーバー上の以前の展開が完全に削除されていることを確認します。

> [!TIP]
> パッケージ キャッシュをクリアするには、コマンド プロンプトから `dotnet nuget locals all --clear` を実行する方法が便利です。
>
> パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。 *nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。

## <a name="additional-resources"></a>その他の技術情報

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
