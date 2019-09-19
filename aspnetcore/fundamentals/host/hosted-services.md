---
title: ASP.NET Core でホステッド サービスを使用するバックグラウンド タスク
author: guardrex
description: ASP.NET Core でホステッド サービスを使用するバックグラウンド タスクの実装方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/03/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 1db3ee1a9bcc0d41edf24df55bcd8d54fb0e9724
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081776"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>ASP.NET Core でホステッド サービスを使用するバックグラウンド タスク

作成者: [Luke Latham](https://github.com/guardrex)

ASP.NET Core では、バックグラウンド タスクを*ホステッド サービス*として実装することができます。 ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。 このトピックでは、3 つのホステッド サービスの例について説明します。

* タイマーで実行されるバックグラウンド タスク。
* [スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)をアクティブ化するホステッド サービス。 スコープ サービスは依存関係の挿入を使用できます。
* 連続して実行される、キューに格納されたバックグラウンド タスク。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプル アプリには、2 つのバージョンが用意されています。

* Web ホスト &ndash; Web ホストは、Web アプリをホストするのに役立ちます。 このトピックで示すコード例は、サンプルの Web ホスト バージョンからのものです。 詳細については、[Web ホスト](xref:fundamentals/host/web-host)のトピックをご覧ください。
* 汎用ホスト &ndash; 汎用ホストは ASP.NET Core 2.1 の新機能です。 詳細については、[汎用ホスト](xref:fundamentals/host/generic-host)のトピックをご覧ください。

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>ワーカー サービス テンプレート

ASP.NET Core ワーカー サービス テンプレートは、実行時間が長いサービス アプリを作成する場合の出発点として利用できます。 ホステッド サービス アプリの基礎としてテンプレートを使用するには:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 新しいプロジェクトを作成します。
1. **[ASP.NET Core Web アプリケーション]** を選択します。 **[次へ]** を選択します。
1. **[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。 **[作成]** を選択します。
1. **[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.0]** が選択されていることを確認します。
1. **[ワーカー サービス]** テンプレートを選択します。 **[作成]** を選択します。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コマンド シェルから [dotnet new](/dotnet/core/tools/dotnet-new) コマンドと共にワーカー サービス (`worker`) テンプレートを使用します。 次の例では、`ContosoWorkerService` という名前のワーカー サービス アプリが作成されます。 このコマンドが実行されると、`ContosoWorkerService` アプリ用のフォルダーが自動的に作成されます。

```dotnetcli
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="package"></a>Package

[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージへのパッケージ参照を追加します。

## <a name="ihostedservice-interface"></a>IHostedService インターフェイス

ホステッド サービスでは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装します。 このインターフェイスは、ホストによって管理されるオブジェクトの 2 つのメソッドを定義します。

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` には、バックグラウンド タスクを開始するロジックが含まれています。 [Web ホスト](xref:fundamentals/host/web-host) を使用する場合は、サーバーが起動し、[IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) がトリガーされた後で、`StartAsync` が呼び出されます。 [汎用ホスト](xref:fundamentals/host/generic-host) を使用する場合は、`ApplicationStarted` がトリガーされる前に `StartAsync` が呼び出されます。

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; ホストが正常なシャットダウンを実行しているときにトリガーされます。 `StopAsync` には、バックグラウンド タスクを終了するロジックが含まれています。 アンマネージ リソースを破棄するには、<xref:System.IDisposable> と[ファイナライザー (デストラクター)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) を実装します。

  キャンセル トークンには、シャットダウン プロセスが正常に行われないことを示す、既定の 5 秒間のタイムアウトが含まれています。 キャンセルがトークンに要求された場合:

  * アプリで実行されている残りのバックグラウンド操作が中止します。
  * `StopAsync` で呼び出されたすべてのメソッドが速やかに戻ります。

  ただし、キャンセルが要求された後もタスクは破棄されません&mdash;呼び出し元がすべてのタスクの完了を待機します。

  アプリが予期せずシャットダウンした場合 (たとえば、アプリのプロセスが失敗した場合)、`StopAsync` は呼び出されないことがあります。 そのため、`StopAsync` で呼び出されたメソッドや行われた操作が実行されない可能性があります。

  既定の 5 秒のシャットダウン タイムアウトを延長するには、次を設定します。

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (汎用ホストを使用するとき) 詳細については、<xref:fundamentals/host/generic-host#shutdown-timeout> を参照してください。
  * シャットダウン タイムアウトのホスト構成設定 (Web ホストを使用するとき) 詳細については、<xref:fundamentals/host/web-host#shutdown-timeout> を参照してください。

ホステッド サービスは、アプリの起動時に一度アクティブ化され、アプリのシャットダウン時に正常にシャットダウンされます。 バックグラウンド タスクの実行中にエラーがスローされた場合、`StopAsync` が呼び出されていなくても `Dispose` を呼び出す必要があります。

## <a name="timed-background-tasks"></a>時間が指定されたバックグラウンド タスク

時間が指定されたバックグラウンド タスクは、[System.Threading.Timer](xref:System.Threading.Timer) クラスを利用します。 このタイマーはタスクの `DoWork` メソッドをトリガーします。 タイマーは `StopAsync` で無効になり、`Dispose` でサービス コンテナーが破棄されたときに破棄されます。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

サービスは、`AddHostedService` 拡張メソッドを使用して `Startup.ConfigureServices` に登録されます。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>バックグラウンド タスクでスコープ サービスを使用する

`IHostedService` 内で[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)を使用するには、スコープを作成します。 既定では、ホステッド サービスのスコープは作成されません。

バックグラウンド タスクのスコープ サービスには、バックグラウンド タスクのロジックが含まれています。 次の例では、<xref:Microsoft.Extensions.Logging.ILogger> がサービスに挿入されています。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

ホステッド サービスはスコープを作成してバックグラウンド タスクのスコープ サービスを解決し、`DoWork` メソッドを呼び出します。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

サービスは `Startup.ConfigureServices` に登録されています。 `IHostedService` の実装は、`AddHostedService` 拡張メソッドで登録されます。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>キューに格納されたバックグラウンド タスク

バックグラウンド タスク キューは、.NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([ASP.NET Core 3.0 用に暫定的に組み込まれる予定](https://github.com/aspnet/Hosting/issues/1280)) に基づいています。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

`QueueHostedService` では、キュー内のバックグラウンド タスクは、<xref:Microsoft.Extensions.Hosting.BackgroundService> としてデキューされ、実行されます。これは、長時間実行する `IHostedService` を構成するための基本クラスです。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

サービスは `Startup.ConfigureServices` に登録されています。 `IHostedService` の実装は、`AddHostedService` 拡張メソッドで登録されます。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

インデックス ページ モデル クラスで:

* `IBackgroundTaskQueue` がコンストラクターに挿入され、`Queue` に割り当てられます。
* <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> が挿入され、`_serviceScopeFactory` に割り当てられます。 ファクトリは、スコープ内でサービス作成するための <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> のインスタンス作成に使用されます。 スコープは、アプリの `AppDbContext` ([スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)) を使用し、データベース レコードを `IBackgroundTaskQueue` (シングルトン サービス) に書き込むために作成されます。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

[インデックス] ページで **[タスクの追加]** ボタンを選択すると、`OnPostAddTask` メソッドが実行されます。 `QueueBackgroundWorkItem` が呼び出され、作業項目がエンキューされます。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a>その他の技術情報

* [IHostedService と BackgroundService クラスを使ってマイクロサービスのバックグラウンド タスクを実装する](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
