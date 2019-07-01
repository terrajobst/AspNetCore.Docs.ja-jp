---
title: Azure App Service での ASP.NET Core のトラブルシューティング
author: guardrex
description: ASP.NET Core Azure App Service の配置に関する問題を診断する方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: d78499c1a82a011239f6b62b546f304a5d5017e2
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313755"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>Azure App Service での ASP.NET Core のトラブルシューティング

作成者: [Luke Latham](https://github.com/guardrex)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

この記事では、Azure App Service の診断ツールを使って ASP.NET Core アプリの起動時の問題を診断する方法の手順を説明します。 追加のトラブルシューティングのアドバイスについては、Azure ドキュメントの「[Azure App Service 診断の概要](/azure/app-service/app-service-diagnostics)」と「[方法: Azure App Service のアプリの監視](/azure/app-service/web-sites-monitor)」を参照してください。

その他のトラブルシューティング トピック:

* IIS では、アプリをホストするために [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)も使用されます。 IIS に特に関連するトラブルシューティング上のアドバイスについては、<xref:host-and-deploy/iis/troubleshoot> をご覧ください。
* <xref:fundamentals/error-handling> では、ローカル システム上で開発しているときに発生する ASP.NET Core アプリのエラーを処理する方法について説明します。
* [Visual Studio を使用したデバッグ方法の学習](/visualstudio/debugger/getting-started-with-the-debugger)に関するページでは、Visual Studio デバッガーの機能を紹介しています。
* [Visual Studio Code でのデバッグ](https://code.visualstudio.com/docs/editor/debugging)に関するページでは、Visual Studio Code に組み込まれているデバッグのサポートについて説明します。

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a>アプリの起動エラーのトラブルシューティング

### <a name="application-event-log"></a>アプリケーション イベント ログ

アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。

1. Azure portal の **[App Services]** でアプリを開きます。
1. **[問題の診断と解決]** を選択します。
1. **[診断ツール]** という見出しを選択します。
1. **[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。
1. **[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。

**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。

1. **[開発ツール]** 領域で **[高度なツール]** を開きます。 **[Go&rarr;]** ボタンを選びます。 新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。
1. ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。
1. **LogFiles** フォルダーを開きます。
1. *eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。
1. ログを調べます。 ログの末尾までスクロールし、最新のイベントを確認します。

### <a name="run-the-app-in-the-kudu-console"></a>Kudu コンソールでアプリを実行します。

多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。 [Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。

1. **[開発ツール]** 領域で **[高度なツール]** を開きます。 **[Go&rarr;]** ボタンを選びます。 新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。
1. ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。

#### <a name="test-a-32-bit-x86-app"></a>32 ビット (x86) アプリをテストする

##### <a name="current-release"></a>現在のリリース

1. `cd d:\home\site\wwwroot`
1. 次のようにアプリを実行します。
   * アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:

     ```console
     {ASSEMBLY NAME}.exe
     ```

エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>プレビュー リリース上で実行されているフレームワークに依存する展開

"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)
1. アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。

#### <a name="test-a-64-bit-x64-app"></a>64 ビット (x64) アプリをテストする

##### <a name="current-release"></a>現在のリリース

* アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:
  1. `cd D:\Program Files\dotnet`
  1. アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:
  1. `cd D:\home\site\wwwroot`
  1. アプリを実行します: `{ASSEMBLY NAME}.exe`

エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>プレビュー リリース上で実行されているフレームワークに依存する展開

"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)
1. アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET Core モジュールの stdout ログ

ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。 stdout ログを有効にして表示するには:

1. Azure portal で **[問題の診断と解決]** ブレードに移動します。
1. **[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。
1. **[Suggested Solutions]\(推奨される解決方法\)**  >  **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、ボタンをクリックして **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。
1. Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。 下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。
1. *web.config* ファイルの隣の鉛筆アイコンをクリックします。
1. **stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。
1. **[保存]** を選んで、更新した *web.config* ファイルを保存します。
1. アプリに対して要求します。
1. Azure portal に戻ります。 **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。 **[Go&rarr;]** ボタンを選びます。 新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。
1. ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。
1. **LogFiles** フォルダーを選びます。
1. **[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。
1. ログ ファイルが開くと、エラーが表示されます。

トラブルシューティングが完了したら、stdout ログを無効にします。

1. Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。 鉛筆アイコンを選んで **web.config** ファイルを再び開きます。
1. **stdoutLogEnabled** を `false` に設定します。
1. **[保存]** を選んでファイルを保存します。

> [!WARNING]
> stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。 ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。 stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。
>
> 起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。 詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a>ASP.NET Core モジュール デバッグ ログ

ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。 stdout ログを有効にして表示するには:

1. 強化された診断ログを有効にするには、次のいずれかを実行します。
   * 「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。 アプリを再デプロイします。
   * Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。
     1. **[開発ツール]** 領域で **[高度なツール]** を開きます。 **[Go&rarr;]** ボタンを選びます。 新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。
     1. ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。
     1. パス **site** > **wwwroot** へのフォルダーを開きます。 鉛筆アイコンを選択し、*web.config* ファイルを編集します。 「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。 **[保存]** ボタンを選択します。
1. **[開発ツール]** 領域で **[高度なツール]** を開きます。 **[Go&rarr;]** ボタンを選びます。 新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。
1. ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。
1. パス **site** > **wwwroot** へのフォルダーを開きます。 *aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。 パスを指定した場合、ログ ファイルの場所に移動します。
1. ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。

トラブルシューティングが完了したら、debug ログを無効にします。

1. 強化されたデバッグ ログを無効にするには、次のいずれかを実行します。
   * ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。
   * Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。 ファイルを保存します。

> [!WARNING]
> debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。 ログ ファイルのサイズに制限はありません。 debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。
>
> 起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。 詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。

::: moniker-end

## <a name="common-startup-errors"></a>起動時の一般的なエラー

以下を参照してください。<xref:host-and-deploy/azure-iis-errors-reference> このリファレンス トピックでは、アプリの起動を妨げる一般的な問題のほとんどが説明されています。

## <a name="slow-or-hanging-app"></a>アプリの速度低下またはハング

要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。

* [Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a>リモート デバッグ

次のトピックを参照してください。

* [「Visual Studio を使用した Azure App Service のトラブルシューティング」の「Web アプリのリモート デバッグ」セクション](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure ドキュメント)
* [Visual Studio 2017 を使用して Azure 内の IIS 上の ASP.NET Core をリモート デバッグする](/visualstudio/debugger/remote-debugging-azure) (Visual Studio ドキュメント)

## <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/) は、Azure App Service でホストされているアプリからのテレメトリを提供し、エラー ログやレポート機能を備えています。 Application Insights は、アプリのログ機能が使用可能になってアプリが起動した後で発生したエラーのみをレポートできます。 詳しくは、「[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)」をご覧ください。

## <a name="monitoring-blades"></a>監視ブレード

監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。 これらのブレードは、500 番台のエラーの診断に使用できます。

ASP.NET Core 拡張機能がインストールされていることを確認してください。 拡張機能がインストールされていない場合は、手動でインストールします。

1. **[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。
1. **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。
1. 拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。
1. 一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。
1. **[OK]** を選んで法的条項に同意します。
1. **[拡張機能の追加]** ブレードで **[OK]** を選びます。
1. 拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。

stdout ログが有効になっていない場合は、次の手順のようにします。

1. Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。 **[Go&rarr;]** ボタンを選びます。 新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。
1. ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。
1. パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。
1. *web.config* ファイルの隣の鉛筆アイコンをクリックします。
1. **stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。
1. **[保存]** を選んで、更新した *web.config* ファイルを保存します。

続けて診断ログをアクティブにします。

1. Azure portal で **[診断ログ]** ブレードを選びます。
1. **[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。 ブレードの上部にある **[保存]** ボタンを選びます。
1. 失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。
1. ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。
1. アプリに対して要求します。
1. ログ ストリーム データ内で、エラーの原因が示されます。

トラブルシューティングが完了したら、stdout ログを無効にしてください。 「[ASP.NET Core モジュールの stdout ログ](#aspnet-core-module-stdout-log)」セクションの説明をご覧ください。

失敗した要求のトレース ログ (FREB ログ) を見るには:

1. Azure portal で **[問題の診断と解決]** ブレードに移動します。
1. サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。

詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。

詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。

> [!WARNING]
> stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。 ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。
>
> ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。 詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
