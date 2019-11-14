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
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963855"
---
# <a name="logging-and-diagnostics-in-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR でのログ記録と診断

By [Andrew Stanton-看護師](https://twitter.com/anurse)

この記事では、ASP.NET Core SignalR アプリから診断情報を収集して問題のトラブルシューティングを行うためのガイダンスを提供します。

## <a name="server-side-logging"></a>サーバー側のログ記録

> [!WARNING]
> サーバー側のログには、アプリからの機密情報が含まれている場合があります。 運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。

SignalR は ASP.NET Core の一部であるため、ASP.NET Core ログシステムを使用します。 既定の構成では、SignalR はごくわずかな情報をログに記録しますが、これは構成できます。 ASP.NET Core ログの構成の詳細については、 [ASP.NET Core のログ記録](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。

SignalR では、次の2つの logger カテゴリを使用します。

* ハブプロトコル、ハブのアクティブ化、メソッドの呼び出し、およびその他のハブ関連アクティビティに関連するログの &ndash; を `Microsoft.AspNetCore.SignalR` します。
* Websocket、長いポーリング、サーバー送信イベント、低レベル SignalR インフラストラクチャなどのトランスポートに関連するログの &ndash; を `Microsoft.AspNetCore.Http.Connections` します。

SignalRから詳細なログを有効にするには、`Logging`の `LogLevel` サブセクションに次の項目を追加して、前のプレフィックスの両方を*appsettings*ファイルの `Debug` レベルに構成します。

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

また、`CreateWebHostBuilder` メソッドのコードでこれを構成することもできます。

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

JSON ベースの構成を使用していない場合は、構成システムで次の構成値を設定します。

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

構成システムのドキュメントを参照して、入れ子になった構成値を指定する方法を確認してください。 たとえば、環境変数を使用する場合、`:` の代わりに2つの `_` 文字 (たとえば、`Logging__LogLevel__Microsoft.AspNetCore.SignalR`) が使用されます。

アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することをお勧めします。 `Trace` レベルでは非常に低レベルの診断が生成されるため、アプリの問題を診断するためにはほとんど必要ありません。

## <a name="access-server-side-logs"></a>サーバー側のログへのアクセス

サーバー側のログにアクセスする方法は、を実行している環境によって異なります。

### <a name="as-a-console-app-outside-iis"></a>IIS の外部のコンソールアプリとして

コンソールアプリでを実行している場合は、既定で[コンソール logger](xref:fundamentals/logging/index#console-provider)が有効になっている必要があります。 SignalR ログはコンソールに表示されます。

### <a name="within-iis-express-from-visual-studio"></a>Visual Studio から IIS Express 内

Visual Studio では、 **[出力]** ウィンドウにログ出力が表示されます。 **[ASP.NET Core Web サーバー]** ドロップダウンオプションを選択します。

### <a name="azure-app-service"></a>Azure App Service

Azure App Service ポータルの **診断ログ** セクションで **アプリケーション ログ (ファイルシステム)** オプションを有効化し、**レベル** を `Verbose` に設定します。 **ログのストリーミング** サービスと、App Service のファイル システムのログに記録します。 詳細については、[Azure ログのストリーミング](xref:fundamentals/logging/index#azure-log-streaming) を参照してください。

### <a name="other-environments"></a>その他の環境

アプリが別の環境 (Docker、Kubernetes、または Windows サービスなど) にデプロイされている場合は、「<xref:fundamentals/logging/index>」を参照して、環境に適したログプロバイダーを構成する方法についての詳細を確認してください。

## <a name="javascript-client-logging"></a>JavaScript クライアントのログ記録

> [!WARNING]
> クライアント側のログには、アプリからの機密情報が含まれている場合があります。 運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。

JavaScript クライアントを使用する場合、`HubConnectionBuilder`の `configureLogging` メソッドを使用してログオプションを構成できます。

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

完全にログ記録を無効にするには、`configureLogging` 方法で `signalR.LogLevel.None` を指定します。

次の表は、JavaScript クライアントで使用できるログレベルを示しています。 ログレベルをこれらの値のいずれかに設定すると、そのレベルおよびテーブル内のすべてのレベルでログ記録が有効になります。

| レベル | 説明 |
| ----- | ----------- |
| `None` | メッセージはログに記録されません。 |
| `Critical` | アプリ全体でエラーが発生したことを示すメッセージ。 |
| `Error` | 現在の操作でエラーが発生したことを示すメッセージ。 |
| `Warning` | 致命的ではない問題を示すメッセージ。 |
| `Information` | 情報メッセージ。 |
| `Debug` | デバッグに役立つ診断メッセージ。 |
| `Trace` | 特定の問題を診断するために設計された、非常に詳細な診断メッセージ。 |

詳細設定を構成すると、ログはブラウザーコンソール (または NodeJS アプリの標準出力) に書き込まれます。

カスタムログシステムにログを送信する場合は、`ILogger` インターフェイスを実装する JavaScript オブジェクトを指定できます。 実装する必要のあるメソッドは、イベントのレベルとイベントに関連付けられているメッセージを取得する `log`です。 (例:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET クライアントのログ記録

> [!WARNING]
> クライアント側のログには、アプリからの機密情報が含まれている場合があります。 運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。

.NET クライアントからログを取得するには、`HubConnectionBuilder`で `ConfigureLogging` メソッドを使用します。 これは `WebHostBuilder` と `HostBuilder`の `ConfigureLogging` メソッドと同じように動作します。 ASP.NET Core で使用するのと同じログプロバイダーを構成することができます。 ただし、個々のログプロバイダーに対して NuGet パッケージを手動でインストールして有効にする必要があります。

### <a name="console-logging"></a>コンソールのログ記録

コンソールのログ記録を有効にするには[、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)追加します。 次に、`AddConsole` メソッドを使用して、コンソールロガーを構成します。

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>デバッグ出力ウィンドウのログ記録

また、ログを構成して、Visual Studio の **[出力]** ウィンドウにアクセスすることもできます。 `AddDebug` メソッドを使用して、次のようにして[、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)インストールします。

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>その他のログプロバイダー

SignalR では、Serilog、Seq、NLog などの他のログ記録プロバイダーや、`Microsoft.Extensions.Logging`と統合されるその他のログ記録システムがサポートされています。 ログ記録システムに `ILoggerProvider`が提供されている場合は、`AddProvider`に登録できます。

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>コントロールの詳細度

アプリの他の場所からログを記録している場合は、既定のレベルを `Debug` に変更すると、詳細が表示されないことがあります。 フィルターを使用して、SignalR ログのログ記録レベルを構成することができます。 これは、サーバーの場合とほぼ同じ方法でコードで行うことができます。

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>ネットワークトレース

> [!WARNING]
> ネットワークトレースには、アプリによって送信されたすべてのメッセージの完全な内容が含まれています。 実稼働アプリから GitHub などのパブリックフォーラムに生のネットワークトレースを投稿**しない**でください。

問題が発生した場合は、ネットワークトレースを使用すると、役に立つ情報が得られることがあります。 これは、問題の追跡ツールで問題をファイルする場合に特に便利です。

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Fiddler を使用してネットワークトレースを収集する (推奨オプション)

この方法は、すべてのアプリで使用できます。

Fiddler は、HTTP トレースを収集するための非常に強力なツールです。 [Telerik.com/fiddler](https://www.telerik.com/fiddler)からインストールして起動し、アプリを実行して問題を再現します。 Fiddler は Windows で使用でき、macOS と Linux のベータ版があります。

HTTPS を使用して接続する場合は、Fiddler が HTTPS トラフィックを復号化するためにいくつかの追加の手順が必要です。 詳細については、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS) を参照してください。

トレースを収集したら、[ > **ファイル**] を選択し、メニューバーから [**すべてのセッション** > **保存**] を選択して、トレースをエクスポートできます。

![Fiddler からすべてのセッションをエクスポートしています](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Tcpdump を使用してネットワークトレースを収集する (macOS および Linux のみ)

この方法は、すべてのアプリで使用できます。

コマンドシェルから次のコマンドを実行して、tcpdump を使用して未加工の TCP トレースを収集できます。 アクセス許可のエラーが表示された場合は、コマンドの `root` またはプレフィックスを `sudo` にする必要があります。

```console
tcpdump -i [interface] -w trace.pcap
```

`[interface]` をキャプチャするネットワークインターフェイスに置き換えます。 通常は、`/dev/eth0` (標準イーサネットインターフェイスの場合) または `/dev/lo0` (localhost トラフィックの場合) のようになります。 詳細については、ホストシステムの `tcpdump` man ページを参照してください。

## <a name="collect-a-network-trace-in-the-browser"></a>ブラウザーでネットワークトレースを収集する

この方法は、ブラウザーベースのアプリに対してのみ機能します。

ほとんどのブラウザーには、ブラウザーとサーバー間のネットワークアクティビティをキャプチャできる [ネットワーク] タブがあり開発者ツール。 ただし、これらのトレースには、WebSocket およびサーバー送信のイベントメッセージは含まれません。 これらのトランスポートを使用している場合は、Fiddler や TcpDump などのツールを使用することをお勧めします。

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge および Internet Explorer

(この手順は、Edge と Internet Explorer の両方で同じです)。

1. F12 キーを押して開発ツールを開きます。
2. [ネットワーク] タブをクリックします。
3. 必要に応じてページを更新し、問題を再現します
4. ツールバーの [保存] アイコンをクリックして、トレースを "HAR" ファイルとしてエクスポートします。

![Microsoft Edge Dev Tools の [ネットワーク] タブの [保存] アイコン](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. F12 キーを押して開発ツールを開きます。
2. [ネットワーク] タブをクリックします。
3. 必要に応じてページを更新し、問題を再現します
4. 要求の一覧の任意の場所を右クリックし、[コンテンツと共に HAR として保存] を選択します。

![Google Chrome Dev ツールの [ネットワーク] タブの [HAR として保存] オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. F12 キーを押して開発ツールを開きます。
2. [ネットワーク] タブをクリックします。
3. 必要に応じてページを更新し、問題を再現します
4. 要求の一覧の任意の場所を右クリックし、[すべてを HAR として保存] を選択します。

![Mozilla Firefox Dev Tools [ネットワーク] タブの [すべてを HAR として保存] オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>診断ファイルを GitHub の問題にアタッチする

GitHub の問題に診断ファイルを添付するには、名前を変更して `.txt` 拡張機能を設定し、問題にドラッグアンドドロップします。

> [!NOTE]
> ログファイルやネットワークトレースの内容を GitHub の問題に貼り付けることは避けてください。 これらのログとトレースは非常に大きくなる可能性があり、GitHub は通常、これらを切り捨てます。

![GitHub の問題にログファイルをドラッグする](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>その他の技術情報

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
