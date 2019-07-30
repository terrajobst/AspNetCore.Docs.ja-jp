---
title: ログ記録と ASP.NET Core SignalR での診断
author: anurse
description: ASP.NET Core SignalR アプリケーションから診断を収集する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 06/19/2019
uid: signalr/diagnostics
ms.openlocfilehash: 69dbd057b3dcadeb3ca5d94ede1234530fb447db
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313710"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>ログ記録と ASP.NET Core SignalR での診断

によって[Andrew Stanton-nurse](https://twitter.com/anurse)

この記事では、問題のトラブルシューティングに役立つ、ASP.NET Core SignalR アプリケーションから診断を収集するためのガイダンスを提供します。

## <a name="server-side-logging"></a>サーバー側のログ記録

> [!WARNING]
> サーバー側のログは、アプリからの機密情報を含めることができます。 **決して**未加工のログを実稼働アプリを GitHub のようなパブリック フォーラムに投稿します。

SignalR は ASP.NET Core の一部であるため、ASP.NET Core のログ記録システムを使用します。 既定の構成、構成された SignalR のログはほとんどの情報が、このことができます。 上のドキュメントを参照して[ASP.NET Core のログ記録](xref:fundamentals/logging/index#configuration)の ASP.NET Core のログ記録の構成の詳細について。

SignalR では、2 つのカテゴリのロガーを使用します。

* `Microsoft.AspNetCore.SignalR` &ndash; ハブのプロトコルに関連するログには、ハブ、メソッド、およびその他のハブに関連するアクティビティの呼び出しをアクティブにします。
* `Microsoft.AspNetCore.Http.Connections` &ndash; ログ用には、Websocket、長いポーリング Server-Sent イベントおよび低レベルの SignalR インフラストラクチャなどのトランスポートに関連します。

SignalR から詳細なログを有効にするには、前のプレフィックスの両方を構成、`Debug`レベル、 *appsettings.json*ファイルに次の項目を追加することで、`LogLevel`サブセクション内で`Logging`:

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

コード内で構成することもできます、`CreateWebHostBuilder`メソッド。

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

JSON ベースの構成を使用していない場合は、構成システムで、次の構成値を設定します。

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

入れ子になった構成値を指定する方法を決定する、構成システムのマニュアルを確認してください。 例では、環境変数を使用する場合、2 つ`_`の代わりに文字が使用されて、 `:` (たとえば、 `Logging__LogLevel__Microsoft.AspNetCore.SignalR`)。

使用をお勧め、`Debug`レベルのアプリの診断の詳細を収集するときにします。 `Trace`レベルが非常に低レベルの診断を生成し、アプリで問題を診断するために必要なことはほとんどありません。

## <a name="access-server-side-logs"></a>サーバー側ログにアクセスします。

サーバー側のログにアクセスする方法を実行している環境によって異なります。

### <a name="as-a-console-app-outside-iis"></a>IIS の外部のコンソール アプリとして

コンソール アプリケーションを実行する場合、[コンソール ロガー](xref:fundamentals/logging/index#console-provider)既定で有効にする必要があります。 SignalR のログは、コンソールに表示されます。

### <a name="within-iis-express-from-visual-studio"></a>Visual Studio から IIS Express 内

Visual Studio でのログ出力が表示されます、**出力**ウィンドウ。 選択、 **ASP.NET Core Web サーバー**オプションをドロップダウンします。

### <a name="azure-app-service"></a>Azure App Service

有効にする、**アプリケーション ログ (ファイルシステム)** オプション、**診断ログ**Azure App Service ポータルのセクションを構成して、**レベル**に`Verbose`します。 ログが表示されます、**ログのストリーミング**サービスと、App Service のファイル システムのログに記録します。 詳細については、次を参照してください。 [Azure ログのストリーミング](xref:fundamentals/logging/index#azure-log-streaming)します。

### <a name="other-environments"></a>その他の環境

(たとえば、Docker、Kubernetes、または Windows サービス) の別の環境にアプリを展開する場合は、次を参照してください。 <xref:fundamentals/logging/index> 、環境に適したログ プロバイダーを構成する方法の詳細。

## <a name="javascript-client-logging"></a>JavaScript クライアントのログ記録

> [!WARNING]
> クライアント側のログは、アプリからの機密情報を含めることができます。 **決して**未加工のログを実稼働アプリを GitHub のようなパブリック フォーラムに投稿します。

使用してログ記録オプションを構成するには、JavaScript クライアントを使用する場合、`configureLogging`メソッド`HubConnectionBuilder`:

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

完全ログ記録を無効にするには指定`signalR.LogLevel.None`で、`configureLogging`メソッド。

次の表では、JavaScript クライアントに利用可能なログ レベルを示します。 これらの値のいずれかに、ログ レベルを設定すると、そのレベルとテーブルの上にすべてのレベルでログ記録が有効になります。

| レベル | 説明 |
| ----- | ----------- |
| `None` | メッセージは記録されません。 |
| `Critical` | アプリ全体で障害を示すメッセージ。 |
| `Error` | 現在の操作の失敗を示すメッセージ。 |
| `Warning` | 致命的でない問題を示すメッセージ。 |
| `Information` | 情報メッセージ。 |
| `Debug` | 診断メッセージのデバッグに役立ちます。 |
| `Trace` | 非常に詳細な診断メッセージが特定の問題を診断するために設計されています。 |

詳細度を構成したら、ブラウザーのコンソール (または NodeJS アプリの標準出力) にログが書き込まれます。

実装する JavaScript オブジェクトを指定するには、カスタムのログ記録システムにログを送信する場合、`ILogger`インターフェイス。 実装する必要がある唯一の方法が`log`イベントのレベルを取得して、イベントに関連付けられているメッセージ。 例:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET クライアントのログ記録

> [!WARNING]
> クライアント側のログは、アプリからの機密情報を含めることができます。 **決して**未加工のログを実稼働アプリを GitHub のようなパブリック フォーラムに投稿します。

.NET クライアントからログを取得、使用することができます、`ConfigureLogging`メソッド`HubConnectionBuilder`します。 これは、機能と同様、`ConfigureLogging`メソッド`WebHostBuilder`と`HostBuilder`します。 ASP.NET Core で使用する同じログ プロバイダーを構成できます。 ただし、手動でインストールし、個々 のログ プロバイダーの NuGet パッケージを有効にする必要があります。

### <a name="console-logging"></a>コンソールのログ記録

コンソールのログ記録を有効にするには追加、 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)パッケージ。 次に、使用、`AddConsole`コンソール ロガーを構成する方法。

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>デバッグ出力ウィンドウのログ記録

移動するログを構成することも、**出力**Visual Studio のウィンドウ。 インストール、 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)パッケージ化し、使用、`AddDebug`メソッド。

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>その他のログ プロバイダー

SignalR など、Serilog、Seq、NLog、またはその他のログ記録システムと統合するには、その他のログ記録プロバイダーをサポートしている`Microsoft.Extensions.Logging`します。 ログ記録システムを提供する場合、`ILoggerProvider`に登録することができます`AddProvider`:

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>コントロールの詳細度

アプリで他の場所からログインする場合に既定のレベルを変更する`Debug`すぎる冗長になる可能性があります。 SignalR のログのログ記録レベルを構成するのにフィルターを使用することができます。 これ行う多くのコードで、サーバー上と同じ方法。

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>ネットワーク トレース

> [!WARNING]
> ネットワーク トレースには、アプリから送信されたすべてのメッセージの完全な内容が含まれています。 **決して**未加工のネットワーク トレースを実稼働アプリを GitHub のようなパブリック フォーラムに投稿します。

問題が発生した場合ネットワーク トレースが役に立つ情報の多くをする場合があります。 これは、問題の追跡ツールで問題を報告しようとしている場合に特に便利です。

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Fiddler (推奨されるオプション) でネットワーク トレースを収集します。

このメソッドは、すべてのアプリに対して機能します。

Fiddler は、HTTP トレースを収集するための非常に強力なツールです。 インストール[telerik.com/fiddler](https://www.telerik.com/fiddler)それを起動し、アプリを実行し、問題を再現します。 Fiddler は、Windows と macOS および Linux 用のベータ バージョンがあります。

HTTPS を使用してを接続する場合は、Fiddler は HTTPS トラフィックを復号化できることを確認する特別な手順です。 詳細については、次を参照してください。、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)します。

選択して、トレースをエクスポートするには、トレースを収集したら、**ファイル** > **保存** > **すべてのセッション**メニュー バーから。

![Fiddler からすべてのセッションをエクスポートします。](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Tcpdump (macOS および Linux のみ) とネットワーク トレースを収集します。

このメソッドは、すべてのアプリに対して機能します。

コマンド シェルから次のコマンドを実行して tcpdump を使用して、生の TCP トレースを収集することができます。 必要がある`root`コマンドにプレフィックスとしてまたは`sudo`アクセス許可エラーが発生した場合。

```console
tcpdump -i [interface] -w trace.pcap
```

置換`[interface]`でキャプチャするネットワーク インターフェイスを使用します。 これは通常、ようになります`/dev/eth0`(用、標準のイーサネット インターフェイス) または`/dev/lo0`(localhost トラフィック用)。 詳細については、次を参照してください。、 `tcpdump` 、ホスト システム上の man ページ。

## <a name="collect-a-network-trace-in-the-browser"></a>ブラウザーでネットワーク トレースを収集します。

このメソッドは、ブラウザー ベースのアプリにのみ機能します。

ほとんどのブラウザー開発者ツールでは、ブラウザーとサーバー間のネットワーク アクティビティをキャプチャすることを許可する"Network"タブが存在します。 ただし、これらのトレースには、WebSocket と Server-Sent イベント メッセージが含まれていません。 これらのトランスポートを使用している場合より優れたアプローチでは Fiddler または TcpDump (後述) などのツールを使用してます。

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge や Internet Explorer

(手順は、Edge や Internet Explorer の両方で同じは)

1. 開発ツールを開く F12 キーを押します
2. [ネットワーク] タブをクリックします。
3. (必要な) 場合は、ページを更新し、問題の再現
4. トレースを"HAR"ファイルとしてエクスポートするには、ツールバーの [保存] アイコンをクリックします。

![保存アイコンを Microsoft Edge 開発者ツールの [ネットワーク] タブ](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. 開発ツールを開く F12 キーを押します
2. [ネットワーク] タブをクリックします。
3. (必要な) 場合は、ページを更新し、問題の再現
4. 任意の場所の要求の一覧でを右クリックし、選択「の内容を HAR として保存」します。

![Google Chrome デベロッパー ツール ネットワーク タブで、"コンテンツを含む HAR として保存 オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. 開発ツールを開く F12 キーを押します
2. [ネットワーク] タブをクリックします。
3. (必要な) 場合は、ページを更新し、問題の再現
4. 任意の場所の要求の一覧でを右クリックし、"保存すべてとして HAR"を選択

![Mozilla Firefox デベロッパー ツール ネットワーク タブで HAR としてすべて"保存オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>GitHub の問題を診断ファイルを添付します。

GitHub の問題を診断ファイルをアタッチするにはため、名前を変更して、`.txt`拡張機能と、ドラッグ アンド ドロップし、問題にします。

> [!NOTE]
> くださいしないと、GitHub の問題にネットワーク トレースまたはログ ファイルの内容を貼り付けます。 これらのログとトレースは非常に大きく、でき、通常は GitHub で切り捨てます。

![GitHub の問題にログ ファイルをドラッグします。](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>その他の技術情報

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
