---
title: .NET Core および ASP.NET Core でのログ記録
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet パッケージで提供されるログ記録フレームワークの使用方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 58e236ad7f0863b87907d5585e1cb6bf61d46e99
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78644450"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>.NET Core および ASP.NET Core でのログ記録

作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)

.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。 この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。

::: moniker range=">= aspnetcore-3.0"

この記事に記載されているほとんどのコード例は、ASP.NET Core アプリのものです。 これらのコード スニペットのログ記録固有の部分は、[汎用ホスト](xref:fundamentals/host/generic-host)を使用するすべての .NET Core アプリに適用されます。 非 Web コンソール アプリで汎用ホストを使用する方法の例については、*バックグラウンド タスクのサンプル アプリ* ([) の ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples)Program.cs<xref:fundamentals/host/hosted-services> ファイルを参照してください。

汎用ホストを使用しないアプリのログ記録コードは、[プロバイダーの追加](#add-providers)方法と[ロガーの作成](#create-logs)方法によって異なります。 ホスト以外のコードの例については、記事のこれらのセクションを参照してください。

::: moniker-end

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="add-providers"></a>プロバイダーを追加する

ログ プロバイダーによってログが表示または格納されます。 たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。 複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。

::: moniker range=">= aspnetcore-3.0"

汎用ホストを使用するアプリにプロバイダーを追加するには、`Add{provider name}`Program.cs*でプロバイダーの* 拡張メソッドを呼び出します。

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

ホスト コンソール以外のアプリでは、`Add{provider name}` を作成するときにプロバイダーの `LoggerFactory` 拡張メソッドを呼び出します。

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory` および `AddConsole` には、`using` 用に `Microsoft.Extensions.Logging` ステートメントが必要です。

既定の ASP.NET Core プロジェクト テンプレートからは <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> が呼び出されます。これにより、次のログ プロバイダーが追加されます。

* [コンソール](#console-provider)
* [Debug](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider) (Windows 上で実行されている場合のみ)

既定のプロバイダーを自分で選択したものと置き換えることができます。 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

プロバイダーを追加するには、`Add{provider name}`Program.cs*でプロバイダーの* 拡張メソッドを呼び出します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

上記のコードには、`Microsoft.Extensions.Logging` と `Microsoft.Extensions.Configuration` への参照が必要です。

既定のプロジェクト テンプレートでは、次のログ プロバイダーを追加する <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> が呼び出されます。

* Console
* デバッグ
* EventSource (ASP.NET Core 2.2 以降)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

`CreateDefaultBuilder` を使用する場合は、既定のプロバイダーを自分で選択したものと置き換えることができます。 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。

## <a name="create-logs"></a>ログを作成する

ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。 Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。 ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。

次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。 ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。 DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

次のホスト コンソール以外のアプリの例では、カテゴリが `LoggingConsoleApp.Program` のロガーを作成します。

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。 ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>Program クラスでログを作成する

ASP.NET Core アプリの `Program` クラスでログを書き込むには、ホストをビルドした後に DI から `ILogger` インスタンスを取得します。

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

ホストの構築時のログ記録は、直接サポートされていません。 ただし、別のロガーを使用することができます。 次の例では、[ でログを記録するために、](https://serilog.net/)Serilog`CreateHostBuilder` ロガーが使用されています。 `AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a>Startup クラスでログを作成する

ASP.NET Core アプリの `Startup.Configure` メソッドでログを書き込むには、メソッド シグネチャに `ILogger` パラメーターを含めます。

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

`Startup.ConfigureServices` メソッドでの DI コンテナーの設定が完了する前にログを書き込むことはサポートされていません。

* `Startup` コンストラクターへのロガーの挿入はサポートされていません。
* `Startup.ConfigureServices` メソッド シグネチャへのロガーの挿入はサポートされていません

この制限の理由は、ログ記録は DI と構成に依存しており、さらに構成は DI に依存しているためです。 DI コンテナーは、`ConfigureServices` が完了するまで設定されません。

ASP.NET Core の以前のバージョンでは Web ホスト用に別の DI コンテナーが作成されるため、ロガーの `Startup` へのコンストラクター挿入が機能します。 汎用ホストに対して 1 つのコンテナーのみが作成される理由については、[破壊的変更に関するお知らせ](https://github.com/aspnet/Announcements/issues/353)に関する記事を参照してください。

`ILogger<T>` に依存するサービスを構成する必要がある場合でも、コンストラクターの挿入を使用するか、ファクトリ メソッドを用意して行うことができます。 ファクトリ メソッドの方法は、他の選択肢がない場合にのみお勧めします。 たとえば、DI のサービスを使用してプロパティを設定する必要があるとします。

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

前の強調表示されているコードは、DI コンテナーで `Func` のインスタンスが初めて作成されるときに実行される `MyService` です。 この方法では、任意の登録済みサービスにアクセスできます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Startup でログを作成する

`Startup` クラスでログを作成するには、コンストラクター シグネチャに `ILogger` パラメーターを追加します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Program クラスでログを作成する

`Program` クラスでログを作成するには、DI から `ILogger` インスタンスを取得します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

ホストの構築時のログ記録は、直接サポートされていません。 ただし、別のロガーを使用することができます。 次の例では、[ でログを記録するために、](https://serilog.net/)Serilog`CreateWebHostBuilder` ロガーが使用されています。 `AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>非同期でないロガー メソッド

ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。 ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。 まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。 たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。 代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。 詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。

## <a name="configuration"></a>構成

ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。

* ファイル形式 (INI、JSON、および XML)。
* コマンド ライン引数。
* 環境変数。
* メモリ内 .NET オブジェクト。
* 暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。
* [Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。
* カスタム プロバイダー (インストール済みまたは作成済み)。

たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。 次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。

`LogLevel` の下の `Logging` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。 この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。

`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。 この例では、Console プロバイダーです。 プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。 プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。 プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。

`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。

ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。 ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。 たとえば、設定ファイルを読み取るために [ によって追加される ](xref:fundamentals/configuration/index#file-configuration-provider)ファイル構成プロバイダー`CreateDefaultBuilder`は、既定でログ構成を再読み込みします。 アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。

構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。

## <a name="sample-logging-output"></a>サンプルのログ記録の出力

前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。 コンソールの出力例は次のとおりです。

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。

Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApiSample" から始まります。 "Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。 ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApi" から始まります。 "Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。 ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。

::: moniker-end

以降、この記事では、ログ記録の詳細とオプションについて説明します。

## <a name="nuget-packages"></a>NuGet パッケージ

`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。

## <a name="log-category"></a>ログのカテゴリ

`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。 このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。 カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。

`ILogger<T>` を使用して、カテゴリとして `ILogger` の完全修飾型名が使用される `T` インスタンスを取得します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` は、`CreateLogger` の完全修飾型名を使用した `T` の呼び出しと同じです。

## <a name="log-level"></a>ログ レベル

すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。 ログ レベルは、重大度または重要度を示します。 たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが `Warning`404 Not Found*状態コードを返した場合は* ログを書き込む場合があります。

`Information` および `Warning` ログを作成するコードを次に示します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。 2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。 メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。

メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。 これらのメソッドでは、`Log` パラメーターを受け取る `LogLevel` メソッドが呼び出されます。 これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。 詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。

ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。

* Trace = 0

  通常はデバッグでのみ役立つ情報の場合。 これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。 *既定で無効です。*

* Debug = 1

  開発とデバッグで役立つ可能性がある情報の場合。 例: `Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。

* Information = 2

  アプリの一般的なフローを追跡する場合。 通常、これらのログには、長期的な値があります。 例 : `Request received for path /api/todo`

* Warning = 3

  アプリのフローで異常なイベントや予期しないイベントが発生した場合。 アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。 `Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。 例 : `FileNotFoundException for file quotes.txt.`

* Error = 4

  処理できないエラーと例外の場合。 これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。 ログ メッセージの例: `Cannot insert record due to duplicate key violation.`

* Critical = 5

  即時の注意が必要なエラーの場合。 例: データ損失のシナリオ、ディスク領域不足。

ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。 (例:

* 運用環境:
  * `Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。 コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。
  * `Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。 そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。
* 開発中:
  * コンソールに `Warning` から `Critical` のメッセージを記録します。
  * トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。

この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。

ASP.NET Core では、フレームワーク イベントのログが書き込まれます。 この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。 `Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a>ログ イベント ID

各ログで "*イベント ID*" を指定できます。 サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

イベント ID によって一連のイベントが関連付けられます。 たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。

ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。 Debug プロバイダーでイベント ID が表示されることはありません。 Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>ログ メッセージ テンプレート

各ログでメッセージ テンプレートが指定されます。 メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。 プレースホルダーには、数値ではなく名前を使用します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。 次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。

```text
Parameter values: parm1, parm2
```

ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。 書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。 この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。 たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。 クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。

## <a name="logging-exceptions"></a>ログ記録の例外

ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

プロバイダーごとに、例外情報の処理方法は異なります。 前述のコードの Debug プロバイダーの出力の例を次に示します。

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>ログのフィルター処理

特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。 最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。

すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。 `LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。

### <a name="create-filter-rules-in-configuration"></a>構成にフィルター規則を作成する

プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。 `CreateDefaultBuilder`この記事で既に説明`Logging`したように、[ メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。

次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。 `ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。

### <a name="filter-rules-in-code"></a>コードのフィルター規則

コードにフィルター規則を登録する方法を次の例に示します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。 1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。

### <a name="how-filtering-rules-are-applied"></a>フィルター規則を適用する方法

前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。 最初の 6 つは構成例、最後の 2 つはコード例のものです。

| number | プロバイダー      | 以下から始まるカテゴリ          | 最小ログ レベル |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | デバッグ         | すべてのカテゴリ                          | 情報       |
| 2      | Console       | Microsoft.AspNetCore.Mvc.Razor.Internal | 警告           |
| 3      | Console       | Microsoft.AspNetCore.Mvc.Razor.Razor    | デバッグ             |
| 4      | Console       | Microsoft.AspNetCore.Mvc.Razor          | エラー             |
| 5      | Console       | すべてのカテゴリ                          | 情報       |
| 6      | すべてのプロバイダー | すべてのカテゴリ                          | デバッグ             |
| 7      | すべてのプロバイダー | システム                                  | デバッグ             |
| 8      | デバッグ         | Microsoft                               | トレース             |

`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。 `ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。 使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。

特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。

* プロバイダーとそのエイリアスと一致するすべての規則が選択されます。 一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。
* 前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。 一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。
* 複数の規則が選択されている場合は、**最後**の 1 つが使用されます。
* 規則が選択されていない場合は、`MinimumLevel` が使用されます。

前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。

* Debug プロバイダーの場合、規則 1、6、8 が適用されます。 規則 8 が最も限定的なので、規則 8 が選択されます。
* Console プロバイダーの場合、規則 3、4、5、6 が適用されます。 規則 3 が最も限定的です。

作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。 `Debug` レベル以上のログが Console プロバイダーに送信されます。

### <a name="provider-aliases"></a>プロバイダーのエイリアス

各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。  組み込みのプロバイダーの場合は、次のエイリアスを使用してください。

* Console
* デバッグ
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>既定の最小レベル

指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。 最小レベルを設定する方法を次の例に示します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。

### <a name="filter-functions"></a>フィルター関数

フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。 関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。 (例:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a>システムのカテゴリとレベル

ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。

| カテゴリ                            | メモ |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | ASP.NET Core の一般的な診断。 |
| Microsoft.AspNetCore.DataProtection | どのキーが検討、検索、および使用されたか。 |
| Microsoft.AspNetCore.HostFiltering  | 許可されるホスト。 |
| Microsoft.AspNetCore.Hosting        | HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。 どのホスティング スタートアップ アセンブリが読み込まれたか。 |
| Microsoft.AspNetCore.Mvc            | MVC と Razor の診断。 モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。 |
| Microsoft.AspNetCore.Routing        | 一致する情報をルーティングします。 |
| Microsoft.AspNetCore.Server         | 接続の開始、停止、キープ アライブ応答。 HTTPS 証明書情報。 |
| Microsoft.AspNetCore.StaticFiles    | 提供されるファイル。 |
| Microsoft.EntityFrameworkCore       | Entity Framework Core の一般的な診断。 データベースのアクティビティと構成、変更の検出、移行。 |

## <a name="log-scopes"></a>ログのスコープ

 "*スコープ*" では、論理操作のセットをグループ化できます。 このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。 たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。

スコープは `IDisposable` メソッドから返される <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> の種類であり、破棄されるまで継続します。 ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

次のコードでは、Console プロバイダーのスコープを有効にしています。

*Program.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。
>
> 構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。

各ログ メッセージには、スコープ内の情報が含まれます。

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>組み込みのログ プロバイダー

ASP.NET Core には次のプロバイダーが付属しています。

* [コンソール](#console-provider)
* [Debug](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。

### <a name="console-provider"></a>Console プロバイダー

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。 

```csharp
logging.AddConsole();
```

コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Debug プロバイダー

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。

Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>イベント ソース プロバイダー

[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。 Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。

```csharp
logging.AddEventSourceLogger();
```

イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a>dotnet trace ツール

[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ツールは、実行中のプロセスの .NET Core のトレースのコレクションを有効にする、クロスプラットフォームの CLI グローバル ツールです。 このツールでは、<xref:Microsoft.Extensions.Logging.EventSource> を使用して <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> プロバイダー データを収集します。

次のコマンドを使用して、dotnet trace ツールをインストールします。

```dotnetcli
dotnet tool install --global dotnet-trace
```

dotnet trace ツールを使用して、アプリからトレースを収集します。

1. アプリで `CreateDefaultBuilder` を使ってホストがビルドされない場合は、[イベント ソース プロバイダー](#event-source-provider)をアプリのログ構成に追加します。

1. `dotnet run` コマンドを使用してアプリを実行します。

1. .NET Core アプリのプロセス識別子 (PID) を決定します。

   * Windows では、次の方法のいずれかを使用します。
     * タスク マネージャー (Ctrl + Alt + Del)
     * [tasklist コマンド](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process PowerShell コマンド](/powershell/module/microsoft.powershell.management/get-process)
   * Linux では、[pidof コマンド](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)を使用します。

   アプリのアセンブリと同じ名前を持つプロセスの PID を検索します。

1. `dotnet trace` コマンドを実行します。

   一般的なコマンド構文

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   PowerShell コマンド シェルを使用する場合は、`--providers` 値を単一引用符 (`'`) で囲みます。

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Windows 以外のプラットフォームでは、`-f speedscope` オプションを追加して、出力トレース ファイルの形式を `speedscope` に変更します。

   | キーワード | 説明 |
   | :-----: | ----------- |
   | 1       | `LoggingEventSource` に関するメタ イベントをログに記録します。 `ILogger` からのイベントは記録されません。 |
   | 2       | `Message` が呼び出されたときに、`ILogger.Log()` イベントをオンにします。 プログラムで (書式設定されずに) 情報が提供されます。 |
   | 4       | `FormatMessage` が呼び出されたときに、`ILogger.Log()` イベントをオンにします。 書式設定された文字列バージョンの情報が提供されます。 |
   | 8       | `MessageJson` が呼び出されたときに、`ILogger.Log()` イベントをオンにします。 引数の JSON 表現が提供されます。 |

   | イベント レベル | 説明     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs` と `{Logger Category}` の `{Event Level}` エントリは、追加のログ フィルター条件を表します。 セミコロン (`FilterSpecs`) で `;` エントリを区切ります。

   Windows コマンド シェルを使用した例 ( **値を囲む単一引用符**なし`--providers`):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   上記のコマンドにより次のことがアクティブになります。

   * エラー (`4`) に対して書式設定された文字列 (`2`) を生成するイベント ソース ロガー。
   * `Microsoft.AspNetCore.Hosting` ログ レベル (`Informational`) での `4` のログ記録。

1. Enter キーまたは Ctrl + C キーを押すことで、dotnet trace ツールを停止します。

   トレースは、*コマンドが実行されたフォルダーに*trace.nettrace`dotnet trace` という名前で保存されます。

1. [Perfview](#perfview) を使用してトレースを開きます。 *trace.nettrace* ファイルを開き、トレース イベントを調べます。

詳細については次を参照してください:

* [パフォーマンス分析ユーティリティのトレース (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core ドキュメント)
* [パフォーマンス分析ユーティリティのトレース (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub リポジトリ ドキュメント)
* [LoggingEventSource クラス](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API ブラウザー)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource 参照ソース (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; 別のバージョンの参照ソースを取得するには、分岐を `release/{Version}` に変更します。ここでは、`{Version}` は目的の ASP.NET Core のバージョンです。
* [Perfview](#perfview) &ndash; イベント ソース トレースの表示に役立ちます。

#### <a name="perfview"></a>Perfview

::: moniker-end

ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。 ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。

このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、`*Microsoft-Extensions-Logging`[追加プロバイダー]**の一覧に文字列** を追加します (文字列の先頭に忘れずにアスタリスクを付けてください)。

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog プロバイダー

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。

```csharp
logging.AddEventLog();
```

[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。 `null` または指定しない場合は、次の既定の設定が使用されます。

* `LogName` &ndash; "アプリケーション"
* `SourceName` &ndash; ".NET ランタイム"
* `MachineName` &ndash; ローカル コンピューター

[警告レベル以上](#log-level)のイベントがログに記録されます。 `Warning` より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。 たとえば、*appsettings.json* ファイルに次を追加します。

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>TraceSource プロバイダー

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。

このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。 このプロバイダーでは、サンプル アプリで使用されている [ など、さまざまな](/dotnet/framework/debug-trace-profile/trace-listeners)リスナー<xref:System.Diagnostics.TextWriterTraceListener>にメッセージをルーティングさせることができます。

### <a name="azure-app-service-provider"></a>Azure App Service プロバイダー

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

プロバイダー パッケージは、共有フレームワークに含まれていません。 プロバイダーを使用するには、プロバイダー パッケージをプロジェクトに追加します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

プロバイダー パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。 .NET Framework をターゲットとする場合、または `Microsoft.AspNetCore.App` を参照している場合は、プロバイダー パッケージをプロジェクトに追加します。 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> のオーバーロードによって <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> を渡すことができます。 設定オブジェクトで既定の設定をオーバーライドできます。たとえば、ログの出力テンプレート、BLOB 名、ファイル サイズの制限などです。 ("*出力テンプレート*" は、`ILogger` メソッドの呼び出しと共に指定されるものに加えて、すべてのログに適用されるメッセージ テンプレートです。)

::: moniker-end

アプリケーションを App Service アプリにデプロイすると、Azure portal の [[App Service]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) ページの **[App Service ログ]** セクションで指定された設定が適用されます。 次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。

* **[アプリケーション ログ (ファイル システム)]**
* **[アプリケーション ログ (BLOB)]**

ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。 既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。 既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。

このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。 プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。

#### <a name="azure-log-streaming"></a>Azure ログのストリーミング

Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。

* アプリ サーバー
* Web サーバー
* 失敗した要求のトレース

Azure ログのストリーミングを構成するには

* アプリのポータル ページから **[App Service ログ]** ページに移動します。
* **[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。
* ログ **[レベル]** を選択します。 この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。

**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。 これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights のトレース ログ

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。 Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。 このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。

ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。 このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。

ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。

詳細については、次のリソースを参照してください。

* [Application Insights の概要](/azure/application-insights/app-insights-overview)
* [Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。
* [ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。
* [Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。
* [Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。

## <a name="third-party-logging-providers"></a>サードパーティ製のログ プロバイダー

ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。

* [elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))

一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。

サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。

1. プロジェクトに NuGet パッケージを追加します。
1. ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。

詳細については、各プロバイダーのドキュメントをご覧ください。 サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/logging/loggermessage>
