---
title: ASP.NET Core SignalR でのログ記録と診断
author: anurse
description: ASP.NET Core SignalR アプリから診断情報を収集する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: c5bd2ac27f8ca486b0d75aed8439747f72448625
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652412"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>ASP.NET Core SignalR でのログ記録と診断

By [Andrew Stanton-看護師](https://twitter.com/anurse)

この記事では、ASP.NET Core SignalR アプリから診断情報を収集して問題のトラブルシューティングを行うためのガイダンスを提供します。

## <a name="server-side-logging"></a>サーバー側のログ記録

> [!WARNING]
> サーバー側のログには、アプリからの機密情報が含まれている場合があります。 運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。

SignalR は ASP.NET Core の一部であるため、ASP.NET Core ログシステムを使用します。 既定の構成では、SignalR のログはほとんどの情報が記録されませんが、設定することができます。 ASP.NET Core ログの構成の詳細については、 [ASP.NET Core のログ記録](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。

SignalR は、次の2つの logger カテゴリを使用します。

* ハブプロトコル、ハブのアクティブ化、メソッドの呼び出し、およびその他のハブ関連アクティビティに関連するログの &ndash; を `Microsoft.AspNetCore.SignalR` します。
* Websocket、長いポーリング、サーバー送信イベント、低レベル SignalR インフラストラクチャなどのトランスポートに関連するログの &ndash; を `Microsoft.AspNetCore.Http.Connections` します。

SignalR から詳細なログを有効にするには、`Logging`の `LogLevel` サブセクションに次の項目を追加して、前のプレフィックスの両方を*appsettings*ファイルの `Debug` レベルに構成します。

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

また、`CreateWebHostBuilder` メソッドのコードでこれを構成することもできます。

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

JSON ベースの構成を使用していない場合は、構成システムで次の構成値を設定します。

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

入れ子になった構成値を指定する方法については、構成システムのドキュメントを確認してください。 たとえば、環境変数を使用する場合、`:` の代わりに2つの `_` 文字 (たとえば、`Logging__LogLevel__Microsoft.AspNetCore.SignalR`) が使用されます。

アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することをお勧めします。 `Trace` レベルでは非常に低レベルの診断が生成されるため、アプリの問題を診断するためにはほとんど必要ありません。

## <a name="access-server-side-logs"></a>サーバー側ログへのアクセス

サーバー側のログにアクセスする方法は、を実行している環境によって異なります。

### <a name="as-a-console-app-outside-iis"></a>IIS の外部のコンソール アプリ

コンソールアプリでを実行している場合は、既定で[コンソール logger](xref:fundamentals/logging/index#console-provider)が有効になっている必要があります。 SignalR ログはコンソールに表示されます。

### <a name="within-iis-express-from-visual-studio"></a>Visual Studio から IIS Express 内

Visual Studio では、 **[出力]** ウィンドウにログ出力が表示されます。 **[ASP.NET Core Web サーバー]** ドロップダウンオプションを選択します。

### <a name="azure-app-service"></a>Azure App Service

Azure App Service ポータルの **[診断ログ]** セクションで **[アプリケーションログ (ファイルシステム)]** オプションを有効にし、`Verbose`に**レベル**を構成します。 ログは、**ログストリーミング**サービスおよび App Service のファイルシステムのログで使用できます。 詳細については、「 [Azure ログストリーミング](xref:fundamentals/logging/index#azure-log-streaming)」を参照してください。

### <a name="other-environments"></a>その他の環境

アプリが別の環境 (Docker、Kubernetes、または Windows サービスなど) にデプロイされている場合は、「<xref:fundamentals/logging/index>」を参照して、環境に適したログプロバイダーを構成する方法についての詳細を確認してください。

## <a name="javascript-client-logging"></a>JavaScript クライアントのログ記録

> [!WARNING]
> クライアント側のログには、アプリからの機密情報が含まれている場合があります。 運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。

JavaScript クライアントを使用する場合、`HubConnectionBuilder`の `configureLogging` メソッドを使用してログオプションを構成できます。

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

完全にログ記録を無効にするには、`configureLogging` 方法で `signalR.LogLevel.None` を指定します。

次の表は、JavaScript クライアントで使用できるログレベルを示しています。 これらの値のいずれかにログ レベルを設定すると、そのレベルとテーブル内のそれより上のすべてのレベルでログ記録が有効になります。

| Level | 説明 |
| ----- | ----------- |
| `None` | メッセージはログに記録されません。 |
| `Critical` | アプリ全体でエラーが発生したことを示すメッセージ。 |
| `Error` | 現在の操作でエラーが発生したことを示すメッセージ。 |
| `Warning` | 致命的ではない問題を示すメッセージ。 |
| `Information` | 情報メッセージ。 |
| `Debug` | デバッグに役立つ診断メッセージ。 |
| `Trace` | 特定の問題を診断するために設計された非常に詳細な診断メッセージ。 |

詳細度を構成すると、ブラウザーのコンソール (または NodeJS アプリの標準出力) にログが書き込まれます。

カスタムログシステムにログを送信する場合は、`ILogger` インターフェイスを実装する JavaScript オブジェクトを指定できます。 実装する必要のあるメソッドは、イベントのレベルとイベントに関連付けられているメッセージを取得する `log`です。 例 :

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET クライアントのログ記録

> [!WARNING]
> クライアント側のログには、アプリからの機密情報が含まれている場合があります。 運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。

.NET クライアントからログを取得するには、`HubConnectionBuilder`で `ConfigureLogging` メソッドを使用します。 これは `WebHostBuilder` と `HostBuilder`の `ConfigureLogging` メソッドと同じように動作します。 ASP.NET Core で使用するものと同じログ プロバイダーを構成できます。 ただし、手動でそれぞれのログ プロバイダーの NuGet パッケージをインストールし有効にする必要があります。

### <a name="console-logging"></a>[コンソールのログ記録]

コンソールのログ記録を有効にするには[、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)追加します。 次に、`AddConsole` メソッドを使用して、コンソールロガーを構成します。

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>デバッグ出力ウィンドウのログ記録

また、ログを構成して、Visual Studio の **[出力]** ウィンドウにアクセスすることもできます。 `AddDebug` メソッドを使用して、次のようにして[、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)インストールします。

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>その他のログプロバイダー

SignalR では、Serilog、Seq、NLog などの他のログ記録プロバイダーや、`Microsoft.Extensions.Logging`と統合されるその他のログ記録システムがサポートされています。 ログ記録システムに `ILoggerProvider`が提供されている場合は、`AddProvider`に登録できます。

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>詳細度のコントロール

アプリの他の場所からログを記録している場合は、既定のレベルを `Debug` に変更すると、詳細が表示されないことがあります。 フィルターを使用して、SignalR ログのログ記録レベルを構成することができます。 これは、サーバーとほぼ同じコードで行うことができます。

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>ネットワーク トレース

> [!WARNING]
> ネットワークトレースには、アプリによって送信されたすべてのメッセージの完全な内容が含まれています。 実稼働アプリから GitHub などのパブリックフォーラムに生のネットワークトレースを投稿**しない**でください。

問題が発生した場合、ネットワーク トレースが多くの役に立つ情報を提供する場合があります。 これは、問題の追跡ツールで問題をファイルする場合に特に便利です。

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Fiddler によるネットワーク トレースの収集 (推奨されるオプション)

この方法は、すべてのアプリに対して機能します。

Fiddler は、HTTP トレースを収集するための非常に強力なツールです。 [Telerik.com/fiddler](https://www.telerik.com/fiddler)からインストールして起動し、アプリを実行して問題を再現します。 Fiddler は Windows で使用でき、macOS および Linux 用のベータ バージョンがあります。

HTTPS を使用して接続する場合は、Fiddler が HTTPS トラフィックを復号化するためにいくつかの追加の手順が必要です。 詳細については、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)を参照してください。

トレースを収集したら、[ > **ファイル**] を選択し、メニューバーから [**すべてのセッション** > **保存**] を選択して、トレースをエクスポートできます。

![Fiddler からすべてのセッションをエクスポートしています](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Tcpdump によるネットワーク トレースの収集 (macOS および Linux のみ)

この方法は、すべてのアプリに対して機能します。

コマンドシェルから次のコマンドを実行して、tcpdump を使用して未加工の TCP トレースを収集できます。 アクセス許可のエラーが表示された場合は、コマンドの `root` またはプレフィックスを `sudo` にする必要があります。

```console
tcpdump -i [interface] -w trace.pcap
```

`[interface]` をキャプチャするネットワークインターフェイスに置き換えます。 通常は、`/dev/eth0` (標準イーサネットインターフェイスの場合) または `/dev/lo0` (localhost トラフィックの場合) のようになります。 詳細については、ホストシステムの `tcpdump` man ページを参照してください。

## <a name="collect-a-network-trace-in-the-browser"></a>ブラウザーでのネットワーク トレースの収集

この方法は、ブラウザー ベースのアプリにのみ機能します。

ほとんどのブラウザーの開発者ツールでは、ブラウザーとサーバー間のネットワーク アクティビティをキャプチャすることができる"Network"タブが存在します。 ただし、これらのトレースには、WebSocket と Server-Sent Events メッセージが含まれていません。 これらのトランスポートを使用している場合は、Fiddler または TcpDump (後述) などのツールを使用するほうがより優れたアプローチです。

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge および Internet Explorer

(この手順は、Edge と Internet Explorer の両方で同じです)。

1. F12 キーを押して開発ツールを開きます。
2. [Network] タブをクリックします。
3. ページを更新し(必要な場合)、問題を再現させます。
4. ツールバーの [保存] アイコンをクリックして、トレースを "HAR" ファイルとしてエクスポートします。

![Microsoft Edge 開発者ツールの [ネットワーク] タブの保存アイコン](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. F12 キーを押して開発ツールを開きます。
2. [Network] タブをクリックします。
3. ページを更新し(必要な場合)、問題を再現させます。
4. 要求の一覧の任意の場所を右クリックし、"Save as HAR with content" を選択します。

![Google Chrome Dev Tools のネットワーク タブの "Save as HAR with content" オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. F12 キーを押して開発ツールを開きます。
2. [Network] タブをクリックします。
3. ページを更新し(必要な場合)、問題を再現させます。
4. 要求の一覧の任意の場所を右クリックし、"Save All As HAR" を選択します。

![Mozilla Firefox 開発ツールのネットワーク タブの "Save All As HAR" オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>GitHub issue への診断ファイルの添付

GitHub の問題に診断ファイルを添付するには、名前を変更して `.txt` 拡張機能を設定し、問題にドラッグアンドドロップします。

> [!NOTE]
> GitHub issue にネットワーク トレースまたはログ ファイルの内容を貼り付ないでください。 これらのログとトレースは非常に大きくなる場合があり、GitHub は通常、これを切り捨てます。

![GitHub の issue にログ ファイルをドラッグします。](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>その他のリソース

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
