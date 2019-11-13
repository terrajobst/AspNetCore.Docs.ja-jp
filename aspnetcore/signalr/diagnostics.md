---
title: ASP.NET Core SignalR でのログ記録と診断
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
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>ASP.NET Core SignalR でのログ記録と診断

作成者: [Andrew Stanton-Nurse](https://twitter.com/anurse)

この記事では、問題のトラブルシューティングに役立つ、ASP.NET Core SignalR アプリケーションから診断を収集するためのガイダンスを提供します。

## <a name="server-side-logging"></a>サーバー側のログ記録

> [!WARNING]
> サーバー側のログは、アプリからの機密情報を含めることができます。 実稼働アプリの未加工のログを GitHub のようなパブリック フォーラムに **決して** 投稿しないでください。

SignalR は ASP.NET Core の一部であるため、ASP.NET Core のログ記録システムを使用します。 既定の構成では、SignalR のログはほとんどの情報が記録されませんが、設定することができます。 ASP.NET Core のログ記録の構成の詳細については [ASP.NET Core のログ記録](xref:fundamentals/logging/index#configuration) に関するドキュメントを参照してください。

SignalR では、2 つのカテゴリのロガーを使用します。

* `Microsoft.AspNetCore.SignalR` &ndash; ハブ プロトコル、 ハブのアクティブ化、 ハブ メソッドの呼び出し、その他のハブに関連するアクティビティに関連するログ。
* `Microsoft.AspNetCore.Http.Connections` &ndash; WebSocket、ロングポーリング、Server-Sent Events および低レベルの SignalR インフラストラクチャなどのトランスポートに関連するログ。

SignalR から詳細なログを有効にするには、*appsettings.json*ファイルの `Logging`の `LogLevel`サブセクション内に次の項目を追加して、上記の両方のプレフィックスを `Debug`レベルに設定します :

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

`CreateWebHostBuilder`メソッド内のコードで設定することもできます :

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

JSON ベースの構成を使用していない場合は、構成システムで、次の構成値を設定します。

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

入れ子になった構成値を指定する方法については、構成システムのドキュメントを確認してください。 たとえば、環境変数を使用する場合は `:` の代わりに 2 つ `_ `文字が使用されます : (例: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`)。

アプリの診断の詳細を収集するときには、`Debug`レベルを使用することを推奨します。 `Trace`レベルは非常に低レベルの診断を生成し、アプリで問題を診断するために必要になることはほとんどありません。

## <a name="access-server-side-logs"></a>サーバー側ログへのアクセス

サーバー側のログにアクセスする方法を実行している環境によって異なります。

### <a name="as-a-console-app-outside-iis"></a>IIS の外部のコンソール アプリ

コンソール アプリケーションを実行する場合、[コンソール ロガー](xref:fundamentals/logging/index#console-provider) を既定で有効にする必要があります。 SignalR のログは、コンソールに表示されます。

### <a name="within-iis-express-from-visual-studio"></a>Visual Studio から IIS Express 内

Visual Studio は **出力** ウィンドウにログ出力を表示します。 ドロップダウンから **ASP.NET Core Web サーバー* *オプションを選択します。

### <a name="azure-app-service"></a>Azure App Service

Azure App Service ポータルの **診断ログ** セクションで **アプリケーション ログ (ファイルシステム)** オプションを有効化し、**レベル** を `詳細` に設定します。**ログのストリーミング** サービスと、App Service のファイル システムのログに記録します。 詳細については、[Azure ログのストリーミング](xref:fundamentals/logging/index#azure-log-streaming) を参照してください。

### <a name="other-environments"></a>その他の環境

 別の環境にアプリを展開する場合 (たとえば、Docker、Kubernetes、または Windows サービス) は、<xref:fundamentals/logging/index> より、環境に適したログ プロバイダーを構成する方法の詳細を参照してください。

## <a name="javascript-client-logging"></a>JavaScript クライアントのログ記録

> [!WARNING]
> クライアント側のログは、アプリからの機密情報を含めることができます。 実稼働アプリの未加工のログを GitHub のようなパブリック フォーラムに **決して** 投稿しないでください。

JavaScript クライアントを使用する場合、`HubConnectionBuilder` の `configureLogging`メソッド使用してログ記録オプションを設定できます :

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

ログ記録を完全に無効にするには、`configureLogging`メソッドで`signalR.LogLevel.None`を指定します。

次の表では、JavaScript クライアントに利用可能なログ レベルを示します。 これらの値のいずれかにログ レベルを設定すると、そのレベルとテーブル内のそれより上のすべてのレベルでログ記録が有効になります。

| レベル | 説明 |
| ----- | ----------- |
| `None` | メッセージは記録されません。 |
| `Critical` | アプリ全体で障害を示すメッセージ。 |
| `Error` | 現在の操作の失敗を示すメッセージ。 |
| `Warning` | 致命的でない問題を示すメッセージ。 |
| `Information` | 情報メッセージ。 |
| `Debug` | デバッグに役立つ診断メッセージ。 |
| `Trace` | 特定の問題を診断するために設計された非常に詳細な診断メッセージ。 |

詳細度を構成すると、ブラウザーのコンソール (または NodeJS アプリの標準出力) にログが書き込まれます。

カスタムのログ記録システムにログを送信する場合、`ILogger`インターフェイスを実装する JavaScript オブジェクトを指定します。実装する必要がある唯一のメソッドは`log`で、イベントのレベルとイベントに関連付けられているメッセージを受け取ります。 例:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET クライアントのログ記録

> [!WARNING]
> クライアント側のログは、アプリからの機密情報を含めることができます。 実稼働アプリの未加工のログを GitHub のようなパブリック フォーラムに **決して** 投稿しないでください。

.NET クライアントからログを取得するために、`HubConnectionBuilder`の`ConfigureLogging`メソッドを使用できます。 これは、`WebHostBuilder`と`HostBuilder`の`ConfigureLogging`メソッドと同様に機能します。 ASP.NET Core で使用するものと同じログ プロバイダーを構成できます。 ただし、手動でそれぞれのログ プロバイダーの NuGet パッケージをインストールし有効にする必要があります。

### <a name="console-logging"></a>コンソールのログ記録

コンソールのログ記録を有効にするには [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) パッケージを追加します。 次に、`AddConsole`メソッドを使用し、コンソール ロガーを構成します。

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>デバッグ出力ウィンドウのログ記録

Visual Studio の **出力** ウィンドウに出力されるようにログを構成することもできます。 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) パッケージをインストールし、`AddDebug`メソッドを使用します。

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>その他のログ プロバイダー

SignalR は Serilog、Seq、NLog、または `Microsoft.Extensions.Logging`と統合されるその他のログ記録システムなど、他のログ記録プロバイダーをサポートしています。 `ILoggerProvider`を提供するログ記録システムであれば、`AddProvider` で登録することができます。

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>詳細度のコントロール

アプリの他の場所からログを記録する場合、既定のレベルを`Debug`に変更すると冗長になる可能性があります。 SignalR のログのログ記録レベルを構成するのにフィルターを使用することができます。 これは、サーバーとほぼ同じコードで行うことができます。

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>ネットワーク トレース

> [!WARNING]
> ネットワーク トレースには、アプリから送信されたすべてのメッセージの完全な内容が含まれています。 実稼働アプリの加工のネットワーク トレースを GitHub のようなパブリック フォーラムに **決して** 投稿しないでください。

問題が発生した場合、ネットワーク トレースが多くの役に立つ情報を提供する場合があります。 これは、問題の追跡ツールで問題を報告しようとしている場合に特に便利です。

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Fiddler によるネットワーク トレースの収集 (推奨されるオプション) 

この方法は、すべてのアプリに対して機能します。

Fiddler は、HTTP トレースを収集するための非常に強力なツールです。 [telerik.com/fiddler](https://www.telerik.com/fiddler) からインストールしてそれを起動し、アプリを実行して問題を再現します。 Fiddler は Windows で使用でき、macOS および Linux 用のベータ バージョンがあります。

HTTPS を使用して接続する場合は、Fiddler が HTTPS トラフィックを復号化するためにいくつかの追加の手順が必要です。 詳細については、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS) を参照してください。

トレースを収集したら、メニュー バーから **File** > **Save** > **All Sessions** を選択して、トレースをエクスポートすることができます。

![Fiddler からすべてのセッションをエクスポートします。](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Tcpdump によるネットワーク トレースの収集 (macOS および Linux のみ)

この方法は、すべてのアプリに対して機能します。

コマンド シェルから次のコマンドを実行して tcpdump を使用して、生の TCP トレースを収集することができます。 アクセス許可エラーが発生した場合は `root` になるか、またはコマンドにプレフィックスとして`sudo` を付ける必要があります。

```console
tcpdump -i [interface] -w trace.pcap
```

`[interface]`をキャプチャするネットワーク インターフェイスに置き換えます。 通常、これは`/dev/eth0` (標準のイーサネット インターフェイス) や`/dev/lo0`(localhost トラフィック用) のようになります。 詳細については、ホスト システム上の `tcpdump` の man ページを参照してください。

## <a name="collect-a-network-trace-in-the-browser"></a>ブラウザーでのネットワーク トレースの収集

この方法は、ブラウザー ベースのアプリにのみ機能します。

ほとんどのブラウザーの開発者ツールでは、ブラウザーとサーバー間のネットワーク アクティビティをキャプチャすることができる"Network"タブが存在します。 ただし、これらのトレースには、WebSocket と Server-Sent Events メッセージが含まれていません。 これらのトランスポートを使用している場合は、Fiddler または TcpDump (後述) などのツールを使用するほうがより優れたアプローチです。

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge や Internet Explorer

(手順は、Edge や Internet Explorer の両方で同じは)

1. F12 キーを押して、開発者ツールを開きます。
2. [ネットワーク] タブをクリックします。
3. ページを更新し(必要な場合)、問題の再現させます。
4. トレースを"HAR"ファイルとしてエクスポートするには、ツールバーの [保存] アイコンをクリックします。

![Microsoft Edge 開発者ツールの [ネットワーク] タブの保存アイコン](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. F12 キーを押して Dev Tools を開きます。
2. [Network] タブをクリックします。
3. ページを更新し(必要な場合)、問題を再現させます。
4. 要求の一覧の任意の場所を右クリックし、"Save as HAR with content" を選択します。

![Google Chrome Dev Tools のネットワーク タブの "Save as HAR with content" オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. F12 キーを押して開発ツールを開きます。
2. [ネットワーク] タブをクリックします。
3. ページを更新し(必要な場合)、問題を再現させます。
4. 要求の一覧の任意の場所を右クリックし、"Save All As HAR" を選択します。

![Mozilla Firefox 開発ツールのネットワーク タブの "Save All As HAR" オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>GitHub issue への診断ファイルの添付

GitHub issue に診断ファイルを添付するには、拡張子が`.txt`になるように名前を変更して、issue にドラッグ アンド ドロップします。

> [!NOTE]
> GitHub issue にネットワーク トレースまたはログ ファイルの内容を貼り付ないでください。 これらのログとトレースは非常に大きくなる場合があり、GitHub は通常、これを切り捨てます。

![GitHub の issue にログ ファイルをドラッグします。](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>その他の技術情報

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
