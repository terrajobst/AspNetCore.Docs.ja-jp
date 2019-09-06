---
title: ASP.NET Core の正常性チェック
author: guardrex
description: アプリやデータベースなど、ASP.NET Core インフラストラクチャの正常性チェックを設定する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: cc2ee50cd887a14fba2141bee13d65e777c16232
ms.sourcegitcommit: 4b00e77f9984ce76356e829cfe7f75f0f61a7a8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70145760"
---
# <a name="health-checks-in-aspnet-core"></a>ASP.NET Core の正常性チェック

作成者: [Luke Latham](https://github.com/guardrex) と [Glenn Condron](https://github.com/glennc)

ASP.NET Core からは、アプリ インフラストラクチャ コンポーネントの正常性を報告するための正常性チェック ミドルウェアとライブラリが提供されます。

正常性チェックは HTTP エンドポイントとしてアプリによって公開されます。 正常性チェック エンドポイントは、さまざまなリアルタイム監視シナリオに合わせて設定できます。

* 正常性プローブは、アプリの状態を確認する目的でコンテナー オーケストレーターとロード バランサーによって使用できます。 たとえば、正常性チェックで問題が確認された場合、コンテナー オーケストレーターは実行中の展開を停止したり、コンテナーを再起動したりします。 ロード バランサーはアプリの異常に対処するため、問題のあるインスタンスから正常なインスタンスにトラフィック経路を変更することがあります。
* メモリ、ディスク、その他の物理サーバー リソースの使用を監視し、正常性の状態を確認できます。
* 正常性チェックでは、データベースや外部サービス エンドポイントなど、アプリの依存関係をテストし、それらが利用できることと正常に機能していることを確認できます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプル アプリには、このトピックで説明されているシナリオの例が含まれています。 所与のシナリオに対してサンプル アプリを実行するには、コマンド シェルでプロジェクトのフォルダーから [dotnet run](/dotnet/core/tools/dotnet-run) コマンドを使用します。 サンプル アプリの詳しい使用方法については、サンプル アプリの *README.md* ファイルと本トピックのシナリオ説明をご覧ください。

## <a name="prerequisites"></a>必須コンポーネント

正常性チェックは通常、アプリの状態を確認する目的で、外部の監視サービスまたはコンテナー オーケストレーターと共に使用されます。 正常性チェックをアプリに追加する前に、使用する監視システムを決定します。 監視システムからは、作成する正常性チェックの種類とそのエンドポイントの設定方法が指示されます。

[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) パッケージへのパッケージ参照を追加します。

サンプル アプリからは、いくつかのシナリオで正常性チェックを実演するスタートアップ コードが提供されます。 [データベース プローブ](#database-probe) シナリオでは、[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) を使用して、データベース接続の正常性がチェックされます。 [DbContext プローブ](#entity-framework-core-dbcontext-probe) シナリオでは、EF Core `DbContext` を使用して、データベースがチェックされます。 データベース シナリオを探索するために、サンプル アプリでは次のことが行われます:

* データベースを作成して、*appsettings.json* ファイルにその接続文字列を指定します。
* そのプロジェクト ファイルに次のパッケージ参照が含まれています:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は [BeatPulse](https://github.com/xabaril/beatpulse) の 1 つのポートであり、マイクロソフトによる保守やサポートは行われません。

もう 1 つの正常性チェックのシナリオでは、1 つの管理ポートに正常性チェックを絞り込む方法が実演されます。 このサンプル アプリでは、管理 URL と管理ポートを含む *Properties/launchSettings.json* ファイルを作成する必要があります。 詳細については、「[ポート別のフィルター処理](#filter-by-port)」セクションを参照してください。

## <a name="basic-health-probe"></a>基本的な正常性プローブ

多くのアプリでは、アプリが要求を処理できること (*活動性*) を報告する基本的な正常性チェック構成で十分にアプリの状態を検出できます。

基本の構成では、正常性チェック サービスを登録し、正常性チェック ミドルウェアを呼び出します。このミドルウェアが特定の URL エンドポイントにおける正常性を返します。 既定では、特定の依存関係またはサブシステムをテストする正常性チェックは登録されていません。 正常性エンドポイント URL で応答できる場合、そのアプリは正常であると見なされます。 既定の応答ライターによって、プレーンテキストの応答として状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) がクライアントに書き込まれます。このとき、状態として [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、または [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が示されます。

正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。 `Startup.Configure` の要求処理パイプラインに正常性チェック ミドルウェアと <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を追加します。

サンプル アプリでは、正常性チェック エンドポイントは `/health` で作成されます (*BasicStartup.cs*)。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

サンプル アプリを使用して基本的な構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a>Docker の例

[Docker](xref:host-and-deploy/docker/index) からは、組み込み `HEALTHCHECK` ディレクティブが提供されます。これを使用し、基本的な正常性チェック構成を使用するアプリの状態を確認できます。

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>正常性チェックを作成する

正常性チェックは <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装することで作成されます。 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> メソッドによって、`Healthy`、`Degraded`、`Unhealthy` のいずれかの正常性を示す `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>` が返されます。 この結果が構成可能な状態コードと共にプレーンテキストの応答として書き込まれます (構成の説明は「[正常性チェック オプション](#health-check-options)」セクションにあります)。 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> からは、任意のキーと値のペアを返すこともできます。

### <a name="example-health-check"></a>正常性チェックの例

次の `ExampleHealthCheck` クラスからは、正常性チェックのレイアウトがどのようなものかがわかります。

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public ExampleHealthCheck()
    {
        // Use dependency injection (DI) to supply any required services to the
        // health check.
    }

    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Execute health check logic here. This example sets a dummy
        // variable to true.
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>正常性チェック サービスを登録する

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> によって `ExampleHealthCheck` 型が正常性チェック サービスに追加されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

次の例にある <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> オーバーロードでは、正常性チェックでエラーが報告されると、レポートにエラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) が設定されます。 エラー状態が `null` (既定) に設定された場合、[HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。 このオーバーロードはライブラリ作成者にとって便利です。この設定に正常性チェック実装が従う場合、正常性チェック エラーが発生したとき、ライブラリによって指示されるエラー状態がアプリによって適用されます。

*タグ*を使用し、正常性チェックをフィルター処理できます (詳細は「[正常性チェックをフィルター処理する](#filter-health-checks)」セクションにあります)。

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> では、lambda 関数も実行できます。 次の例では、正常性チェック名に「`Example`」が指定されいます。このチェックでは状態として常に正常が返されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () =>
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a>正常性チェック ミドルウェアを使用する

`Startup.Configure` で、エンドポイント URL または相対パスを利用し、処理パイプラインで <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を呼び出します。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

正常性チェックに特定のポートで待機させる場合、<xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> のオーバーロードを使用してポートを設定します ([詳細は「ポート別のフィルター処理」セクションにあります](#filter-by-port))。

```csharp
app.UseHealthChecks("/health", port: 8000);
```

正常性チェック ミドルウェアは、アプリの要求処理パイプラインの*ターミナル ミドルウェア*です。 最初に現れた、要求 URL にぴったり一致する正常性チェック エンドポイントでチェックが実行され、ミドルウェア パイプラインの残りは回避されます。 回避になると、一致した正常性チェックの後、ミドルウェアは実行されません。

## <a name="health-check-options"></a>正常性チェック オプション

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> では、正常性チェックの動作をカスタマイズできます。

* [正常性チェックをフィルター処理する](#filter-health-checks)
* [HTTP 状態コードをカスタマイズする](#customize-the-http-status-code)
* [キャッシュ ヘッダーを非表示にする](#suppress-cache-headers)
* [出力をカスタマイズする](#customize-output)

### <a name="filter-health-checks"></a>正常性チェックをフィルター処理する

既定では、正常性チェック ミドルウェアでは、登録されている正常性チェックがすべて実行されます。 正常性チェックのサブセットを実行するには、<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> オプションにブール値を返す関数を指定します。 次の例では、関数の条件文にあるそのタグ (`bar_tag`) により `Bar` 正常性チェックが除外されます。`true` は、正常性チェックの <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> プロパティが `foo_tag` か `baz_tag` に一致した場合にのみ返されます。

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // Filter out the 'Bar' health check. Only Foo and Baz execute.
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>HTTP 状態コードをカスタマイズする

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> を使用し、HTTP 状態コードに対する正常性状態のマッピングをカスタマイズします。 次の <xref:Microsoft.AspNetCore.Http.StatusCodes> 代入はミドルウェアによって使用される既定値です。 要件に合わせて状態コード値を変更します。

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The following StatusCodes are the default assignments for
        // the HealthStatus properties.
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
}
```

### <a name="suppress-cache-headers"></a>キャッシュ ヘッダーを非表示にする

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> によって、応答キャッシュを禁止する目的で、正常性チェック ミドルウェアによって HTTP ヘッダーがプローブ応答に追加されるかどうかが制御されます。 値が `false` (既定) の場合、応答キャッシュを禁止する目的で、ミドルウェアによってヘッダー `Cache-Control`、`Expires`、`Pragma` が設定またはオーバーライドされます。 値が `true` の場合、応答のキャッシュ ヘッダーがミドルウェアによって変更されることはありません。

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The default value is false.
        AllowCachingResponses = false
    });
}
```

### <a name="customize-output"></a>出力をカスタマイズする

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> オプションによって、応答の書き込みに使用される委任が取得または設定されます。 既定の委任では、文字列値 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) を含む、最小のプレーンテキスト応答が書き込まれます。

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // WriteResponse is a delegate used to write the response.
        ResponseWriter = WriteResponse
    });
}

private static Task WriteResponse(HttpContext httpContext,
    HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

## <a name="database-probe"></a>データベース プローブ

正常性チェックでは、データベースが通常どおり応答しているかどうかを示すブール値テストとして実行されるよう、データベース クエリを指定できます。

サンプル アプリでは、SQL Server データベース上で正常性のチェックを実行するために、ASP.NET Core アプリの正常性チェック ライブラリの [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 使用します。 `AspNetCore.Diagnostics.HealthChecks` によってデータベースに対して `SELECT 1` クエリが実行され、データベースへの接続が正常であることが確認されます。

> [!WARNING]
> クエリでデータベース接続を確認するとき、すぐに返されるクエリを選択します。 このクエリ手法には、データベースをオーバーロードし、そのパフォーマンスを低下させるというリスクがあります。 ほとんどの場合、テスト クエリは実行する必要がありません。 データベースに正常に接続できれば十分です。 クエリを実行する必要があれば、`SELECT 1` など、単純な SELECT クエリを選択してください。

[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) へのパッケージ参照を含めます。

サンプル アプリの *appsettings.json* ファイルに有効なデータベース接続文字列を指定します。 このアプリでは `HealthCheckSample` という名前の SQL Server データベースが使用されます。

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。 サンプル アプリでは、データベースの接続文字列 (*DbHealthStartup.cs*) を利用して `AddSqlServer` メソッドを呼び出します。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

サンプル アプリを使用してデータベース プローブ シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。

```console
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は [BeatPulse](https://github.com/xabaril/beatpulse) の 1 つのポートであり、マイクロソフトによる保守やサポートは行われません。

## <a name="entity-framework-core-dbcontext-probe"></a>Entity Framework Core DbContext プローブ

`DbContext` チェックでは、EF Core `DbContext` に対して構成されているデータベースとアプリとが通信できることが確認されます。 `DbContext` チェックは、次のようなアプリでサポートされています。

* [Entity Framework (EF) Core を使用する](/ef/core/)。
* [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) へのパッケージ参照を含んでいる。

`AddDbContextCheck<TContext>` によって `DbContext` の正常性チェックが登録されます。 `DbContext` は `TContext` としてメソッドに指定されます。 オーバーロードはエラー状態、タグ、カスタム テスト クエリの設定に利用できます。

既定では:

* `DbContextHealthCheck` によって EF Core の `CanConnectAsync` メソッドが呼び出されます。 `AddDbContextCheck` メソッドのオーバーロードを使用して正常性を確認するときに実行される操作をカスタマイズできます。
* 正常性チェックの名前は `TContext` 型の名前になります。

サンプル アプリでは、`AppDbContext` が `AddDbContextCheck` に提供され、`Startup.ConfigureServices` でサービスとして登録されます。

*DbContextHealthStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

サンプル アプリで、`UseHealthChecks` によって `Startup.Configure` に正常性チェック ミドルウェアが追加されます。

*DbContextHealthStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

サンプル アプリを利用して `DbContext` プローブ シナリオを実行するには、接続文字列によって指定されるデータベースが SQL Server インスタンスに存在しないことを確認します。 データベースが存在する場合、それを削除します。

コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。

```console
dotnet run --scenario dbcontext
```

アプリの実行後、ブラウザーで `/health` エンドポイントに要求することで正常性状態を確認します。 データベースと `AppDbContext` は存在しません。そこで、アプリによって次の応答が返されます。

```
Unhealthy
```

サンプル アプリにデータベースを作成させます。 `/createdatabase` に要求を行います。 アプリからの応答は次のようになります。

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

`/health` エンドポイントに要求を行います。 データベースとコンテキストが存在します。そのため、アプリによって次の応答が返されます。

```
Healthy
```

サンプル アプリにデータベースを削除させます。 `/deletedatabase` に要求を行います。 アプリからの応答は次のようになります。

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

`/health` エンドポイントに要求を行います。 アプリから異常という応答が返されます。

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>対応性と活動性に区分されるプローブ

一部のホスティング シナリオでは、2 つのアプリ状態を区別する一組の正常性チェックが使用されます。

* アプリは機能しているが、要求を受信する準備がまだできていない。 この状態がアプリの*対応性*です。
* アプリは機能しており、要求に応答する。 この状態がアプリの*活動性*です。

対応性チェックは通常、より広範囲で時間のかかる一連のチェックが実行され、アプリのすべてのサブシステムとリソースが利用できるかどうかが判断されます。 活動性チェックでは、アプリが要求を処理できるかどうかを判断する簡易チェックのみが実行されます。 アプリが対応性チェックに合格したら、活動性の確認のみを求める不経済な一連の活動性チェックをアプリに負わせる必要はありません。

サンプル アプリには、[ホステッド サービス](xref:fundamentals/host/hosted-services)の長時間実行スタートアップ タスクの完了を報告する正常性チェックが含まれています。 `StartupHostedServiceHealthCheck` によって `StartupTaskCompleted` というプロパティが公開されます。ホステッド サービスでは長時間実行タスクの完了時にこのプロパティを `true` に設定できます (*StartupHostedServiceHealthCheck.cs*)。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

長時間実行バックグラウンド タスクは[ホステッド サービス](xref:fundamentals/host/hosted-services)によって開始されます (*Services/StartupHostedService*)。 タスクが終わると、`StartupHostedServiceHealthCheck.StartupTaskCompleted` が `true` に設定されます。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

ホステッド サービスと共に `Startup.ConfigureServices` で正常性チェックが <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に登録されます。 ホステッド サービスでは正常性チェックにプロパティを設定する必要があるため、正常性チェックはサービス コンテナーにも登録されます (*LivenessProbeStartup.cs*)。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。 サンプル アプリで、対応性チェックのための正常性チェック エンドポイントが `/health/ready` で作成され、活動性チェックのための正常性チェック エンドポイントが `/health/live` で作成されます。 対応性チェックでは、`ready` タグが与えられているものに正常性チェックが絞り込まれます。 活動性チェックでは、[HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) で `false` を返すことで `StartupHostedServiceHealthCheck` が除外されます (詳細については、「[正常性チェックをフィルター処理する](#filter-health-checks)」を参照してください)。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

サンプル アプリを使用して対応性/活動性構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。

```console
dotnet run --scenario liveness
```

ブラウザーで、15 秒が経過するまで `/health/ready` に数回アクセスします。 正常性チェックにより最初の 15 秒に対して "*異常*" が報告されます。 15 秒後、エンドポイントから "*正常*" が報告されます。これは、ホステッド サービスによる長時間実行タスクが完了したことを反映しています。

この例では、2 秒間の遅延で最初の対応性チェックを実行する正常性チェック パブリッシャー (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装) も作成されます。 詳細については、「[正常性チェック パブリッシャー](#health-check-publisher)」セクションをご覧ください。

### <a name="kubernetes-example"></a>Kubernetes の例

対応性チェックと活動性チェックを使い分けることは、[Kubernetes](https://kubernetes.io/) などの環境で便利です。 Kubernetes では、要求を受け付ける前に、基礎をなすデータベースの可用性テストなど、時間のかかるスタートアップ作業の実行がアプリに要求されることがあります。 別個のチェックを利用することで、アプリは機能しているがまだ準備ができていないか、あるいはアプリが起動に失敗したかをオーケストレーターは区別できます。 Kubernetes の対応性プローブと活動性プローブに関する詳細については、Kubernetes ドキュメントの「[Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)」 (活動性プローブと対応性プローブを設定する) を参照してください。

次の例では、Kubernetes 対応性プローブの構成を確認できます。

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>カスタム応答ライターを含むメトリック ベースのプローブ

このサンプル アプリでは、カスタム応答ライターを含む、メモリ正常性チェックを確認できます。

与えられたしきい値を超えるメモリがアプリで使用された場合 (このサンプル アプリでは 1 GB)、`MemoryHealthCheck` から異常状態が報告されます。 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> にはアプリのガベージ コレクター (GC) 情報が含まれています (*MemoryHealthCheck.cs*)。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に渡して正常性チェックを有効にする代わりに、`MemoryHealthCheck` がサービスとして登録されます。 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> が登録されたサービスはすべて、正常性チェック サービスとミドルウェアで利用できます。 正常性チェック サービスはシングルトン サービスとして登録することをお勧めします。

*CustomWriterStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。 `WriteResponse` 委任が `ResponseWriter` プロパティに与えられることで、正常性チェックの実行時に、カスタム JSON 応答が出力されます。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

`WriteResponse` メソッドによって `CompositeHealthCheckResult` が書式設定されて JSON オブジェクトが生成され、正常性チェック応答のために JSON 出力が生成されます。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

サンプル アプリを使用してカスタム応答ライターを含むメトリックベースのプローブを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。

```console
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、ディスク ストレージや最大値活動性のチェックなど、メトリックベースの正常性チェック シナリオが含まれます。
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は [BeatPulse](https://github.com/xabaril/beatpulse) の 1 つのポートであり、マイクロソフトによる保守やサポートは行われません。

## <a name="filter-by-port"></a>ポート別のフィルター処理

ポートを指定して <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を呼び出すと、正常性チェック要求は指定されたポートに制限されます。 これは通常、サービスを監視するためのポートを公開する目的で、コンテナー環境で使用されます。

サンプル アプリによって、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を使用してポートが設定されます。 ポートは *launchSettings.json* ファイルに設定され、環境変数を介して構成プロバイダーに渡されます。 管理ポートで要求を待つようにサーバーを設定する必要もあります。

サンプル アプリを使用し、管理ポート設定を実演するには、*Properties* フォルダーで *launchSettings.json* ファイルを作成します。

次の *launchSettings.json* ファイルはサンプル アプリのプロジェクト ファイルに含まれません。手動で作成する必要があります。

*Properties/launchSettings.json*:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> の呼び出しによって管理ポートが指定されます (*ManagementPortStartup.cs*)。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> URL と管理ポートをコードに明示的に設定することで、サンプル アプリで *launchSettings.json* ファイルを作成することを回避できます。 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が作成される *Program.cs* で、<xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> の呼び出しを追加し、アプリの通常の応答エンドポイントと管理ポート エンドポイントを指定します。 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> が呼び出される *ManagementPortStartup.cs* で、管理ポートを明示的に指定します。
>
> *Program.cs*:
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

サンプル アプリを使用して管理ポート構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>正常性チェック ライブラリを配布する

正常性チェックをライブラリとして配布するには:

1. スタンドアロン クラスとして <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装する正常性チェックを記述します。 このクラスは、設定データにアクセスする目的で[依存関係挿入 (DI)](xref:fundamentals/dependency-injection)、型のアクティブ化、[名前付きオプション](xref:fundamentals/configuration/options)に依存できます。

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   // Health check logic
                   //
                   // data1 and data2 are used in the method to
                   // run the probe's health check logic.

                   // Assume that it's possible for this health check
                   // to throw an AccessViolationException.

                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. 拡張メソッドを記述します。拡張メソッドを使用するアプリがその `Startup.Configure` メソッドで呼び出すパラメーターを指定します。 次の例では、次の正常性チェック メソッド シグネチャを想定します。

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   先のシグネチャは、正常性チェック プローブ ロジックを処理する目的で `ExampleHealthCheck` に追加データが要求されることを示しています。 正常性チェックが拡張メソッドに登録されたときに正常性チェック インスタンスを作成するための委任にデータが提供されます。 次の例では、呼び出し元によって次が任意で指定されます。

   * 正常性チェック名 (`name`)。 `null` の場合、`example_health_check` が使用されます。
   * 正常性チェックの文字列データ ポイント (`data1`)。
   * 正常性チェックの整数データ ポイント (`data2`)。 `null` の場合、`1` が使用されます。
   * エラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。 既定値は、`null` です。 `null` の場合、エラー状態に対して [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。
   * タグ (`IEnumerable<string>`)。

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string NAME = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? NAME,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>正常性チェック パブリッシャー

サービス コンテナーに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> が追加されると、正常性チェック システムにより定期的に正常性チェックが実行され、結果と共に `PublishAsync` が呼び出されます。 これは、正常性を判断する目的で監視システムを定期的に呼び出すことを各プロセスに求めるプッシュベースの監視システム シナリオで便利です。

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インターフェイスには単一メソッドが与えられます。

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> を使うと次を設定できます。

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; アプリが起動した後、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスを実行する前に適用される初期遅延です。 遅延はスタートアップ時に一度だけ適用され、以降の繰り返しには適用されません。 既定値は 5 秒です。
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> を実行する期間です。 既定値は 30 秒です。
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> が `null` (既定値) の場合、正常性チェック パブリッシャー サービスにより登録済みのすべての正常性チェックが実行されます。 正常性チェックのサブセットを実行するには、チェックのセットをフィルター処理する関数を指定します。 述語は期間ごとに評価されます。
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; すべての <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスに対する正常性チェックの実行のタイムアウトです。 タイムアウトなしで実行するには <xref:System.Threading.Timeout.InfiniteTimeSpan> を使います。 既定値は 30 秒です。

::: moniker range="= aspnetcore-2.2"

> [!WARNING]
> ASP.NET Core 2.2 のリリースでは、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> を設定しても <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装により無視されます。<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> の値が設定されます。 この問題は、ASP.NET Core 3.0 で修正される予定です。 詳細については、「[HealthCheckPublisherOptions.Period sets the value of .Delay (HealthCheckPublisherOptions.Period により .Delay の値が設定される)](https://github.com/aspnet/Extensions/issues/1041)」をご覧ください。

::: moniker-end

サンプル アプリでは、`ReadinessPublisher` が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装です。 正常性チェックの状態は `Entries` に記録され、チェックごとにログに記録されます。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=20,22-23)]

サンプル アプリの `LivenessProbeStartup` の例では、対応性チェック `StartupHostedService` にはスタートアップの遅延が 2 秒間あり、30 秒ごとにチェックが実行されます。 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装をアクティブ化するために、サンプルでは `ReadinessPublisher` をシングルトン サービスとして[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーに登録しています。

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

::: moniker range="= aspnetcore-2.2"

> [!NOTE]
> 1 つ以上の他のホステッド サービスが既にアプリに追加されている場合、次の回避策により <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスをサービス コンテナーに追加することができます。 この回避策は、ASP.NET Core 3.0 のリリースと共に必要なくなります。 詳細については、「 https://github.com/aspnet/Extensions/issues/639 」を参照してください。
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

::: moniker-end

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、[Application Insights](/azure/application-insights/app-insights-overview) など、いくつかのシステムのパブリッシャーが含まれます。
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は [BeatPulse](https://github.com/xabaril/beatpulse) の 1 つのポートであり、マイクロソフトによる保守やサポートは行われません。

## <a name="restrict-health-checks-with-mapwhen"></a>MapWhen で正常性チェックを制限する

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> を使用して、正常性チェック エンドポイントの要求パイプラインを条件付きで分岐します。

次の例では、`api/HealthCheck` エンドポイントに対して GET 要求が受信された場合に、`MapWhen` が要求パイプラインを分岐して正常性チェック ミドルウェアをアクティブ化します。

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

詳細については、<xref:fundamentals/middleware/index#use-run-and-map> を参照してください。
