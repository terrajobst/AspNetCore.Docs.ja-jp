---
title: ASP.NET Core の正常性チェック
author: guardrex
description: アプリやデータベースなど、ASP.NET Core インフラストラクチャの正常性チェックを設定する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 11/13/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 4a4606a58178018f0d71d467d4c8b6c9982c09dc
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74115992"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="d4c73-103">ASP.NET Core の正常性チェック</span><span class="sxs-lookup"><span data-stu-id="d4c73-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="d4c73-104">作成者: [Luke Latham](https://github.com/guardrex) と [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="d4c73-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4c73-105">ASP.NET Core からは、アプリ インフラストラクチャ コンポーネントの正常性を報告するための正常性チェック ミドルウェアとライブラリが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="d4c73-106">正常性チェックは HTTP エンドポイントとしてアプリによって公開されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="d4c73-107">正常性チェック エンドポイントは、さまざまなリアルタイム監視シナリオに合わせて設定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="d4c73-108">正常性プローブは、アプリの状態を確認する目的でコンテナー オーケストレーターとロード バランサーによって使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="d4c73-109">たとえば、正常性チェックで問題が確認された場合、コンテナー オーケストレーターは実行中の展開を停止したり、コンテナーを再起動したりします。</span><span class="sxs-lookup"><span data-stu-id="d4c73-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="d4c73-110">ロード バランサーはアプリの異常に対処するため、問題のあるインスタンスから正常なインスタンスにトラフィック経路を変更することがあります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="d4c73-111">メモリ、ディスク、その他の物理サーバー リソースの使用を監視し、正常性の状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="d4c73-112">正常性チェックでは、データベースや外部サービス エンドポイントなど、アプリの依存関係をテストし、それらが利用できることと正常に機能していることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="d4c73-113">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d4c73-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d4c73-114">サンプル アプリには、このトピックで説明されているシナリオの例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="d4c73-115">所与のシナリオに対してサンプル アプリを実行するには、コマンド シェルでプロジェクトのフォルダーから [dotnet run](/dotnet/core/tools/dotnet-run) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="d4c73-116">サンプル アプリの詳しい使用方法については、サンプル アプリの *README.md* ファイルと本トピックのシナリオ説明をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4c73-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d4c73-117">Prerequisites</span></span>

<span data-ttu-id="d4c73-118">正常性チェックは通常、アプリの状態を確認する目的で、外部の監視サービスまたはコンテナー オーケストレーターと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="d4c73-119">正常性チェックをアプリに追加する前に、使用する監視システムを決定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="d4c73-120">監視システムからは、作成する正常性チェックの種類とそのエンドポイントの設定方法が指示されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="d4c73-121">[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) パッケージは、ASP.NET Core アプリに対して暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="d4c73-122">Entity Framework Core を使用して正常性チェックを実行するには、[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="d4c73-123">サンプル アプリからは、いくつかのシナリオで正常性チェックを実演するスタートアップ コードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="d4c73-124">[データベース プローブ](#database-probe) シナリオでは、[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) を使用して、データベース接続の正常性がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="d4c73-125">[DbContext プローブ](#entity-framework-core-dbcontext-probe) シナリオでは、EF Core `DbContext` を使用して、データベースがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="d4c73-126">データベース シナリオを探索するために、サンプル アプリでは次のことが行われます:</span><span class="sxs-lookup"><span data-stu-id="d4c73-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="d4c73-127">データベースを作成して、*appsettings.json* ファイルにその接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="d4c73-128">そのプロジェクト ファイルに次のパッケージ参照が含まれています:</span><span class="sxs-lookup"><span data-stu-id="d4c73-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="d4c73-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="d4c73-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="d4c73-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d4c73-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="d4c73-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="d4c73-132">もう 1 つの正常性チェックのシナリオでは、1 つの管理ポートに正常性チェックを絞り込む方法が実演されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="d4c73-133">このサンプル アプリでは、管理 URL と管理ポートを含む *Properties/launchSettings.json* ファイルを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="d4c73-134">詳細については、「[ポート別のフィルター処理](#filter-by-port)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="d4c73-135">基本的な正常性プローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-135">Basic health probe</span></span>

<span data-ttu-id="d4c73-136">多くのアプリでは、アプリが要求を処理できること (*活動性*) を報告する基本的な正常性チェック構成で十分にアプリの状態を検出できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="d4c73-137">基本の構成では、正常性チェック サービスを登録し、正常性チェック ミドルウェアを呼び出します。このミドルウェアが特定の URL エンドポイントにおける正常性を返します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="d4c73-138">既定では、特定の依存関係またはサブシステムをテストする正常性チェックは登録されていません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="d4c73-139">正常性エンドポイント URL で応答できる場合、そのアプリは正常であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="d4c73-140">既定の応答ライターによって、プレーンテキストの応答として状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) がクライアントに書き込まれます。このとき、状態として [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、または [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が示されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="d4c73-141">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c73-142">`Startup.Configure` で `MapHealthChecks` を呼び出して、正常性チェック エンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="d4c73-143">サンプル アプリでは、正常性チェック エンドポイントは `/health` で作成されます (*BasicStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="d4c73-144">サンプル アプリを使用して基本的な構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="d4c73-145">Docker の例</span><span class="sxs-lookup"><span data-stu-id="d4c73-145">Docker example</span></span>

<span data-ttu-id="d4c73-146">[Docker](xref:host-and-deploy/docker/index) からは、組み込み `HEALTHCHECK` ディレクティブが提供されます。これを使用し、基本的な正常性チェック構成を使用するアプリの状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="d4c73-147">正常性チェックを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c73-147">Create health checks</span></span>

<span data-ttu-id="d4c73-148">正常性チェックは <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="d4c73-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> メソッドによって、`Healthy`、`Degraded`、`Unhealthy` のいずれかの正常性を示す <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="d4c73-150">この結果が構成可能な状態コードと共にプレーンテキストの応答として書き込まれます (構成の説明は「[正常性チェック オプション](#health-check-options)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="d4c73-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> からは、任意のキーと値のペアを返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="d4c73-152">次の `ExampleHealthCheck` クラスからは、正常性チェックのレイアウトがどのようなものかがわかります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="d4c73-153">正常性チェックのロジックが `CheckHealthAsync` メソッドに配置されています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="d4c73-154">次の例では、ダミー変数 `healthCheckResultHealthy` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="d4c73-155">`healthCheckResultHealthy` の値が `false` に設定されている場合、[HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 状態が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="d4c73-156">正常性チェック サービスを登録する</span><span class="sxs-lookup"><span data-stu-id="d4c73-156">Register health check services</span></span>

<span data-ttu-id="d4c73-157">`Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> によって `ExampleHealthCheck` 型が正常性チェック サービスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="d4c73-158">次の例にある <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> オーバーロードでは、正常性チェックでエラーが報告されると、レポートにエラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="d4c73-159">エラー状態が `null` (既定) に設定された場合、[HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="d4c73-160">このオーバーロードはライブラリ作成者にとって便利です。この設定に正常性チェック実装が従う場合、正常性チェック エラーが発生したとき、ライブラリによって指示されるエラー状態がアプリによって適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="d4c73-161">*タグ*を使用し、正常性チェックをフィルター処理できます (詳細は「[正常性チェックをフィルター処理する](#filter-health-checks)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="d4c73-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> では、lambda 関数も実行できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="d4c73-163">次の例では、正常性チェック名に「`Example`」が指定されいます。このチェックでは状態として常に正常が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="d4c73-164"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> を呼び出して、正常性チェックの実装に引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arugments to a health check implementation.</span></span> <span data-ttu-id="d4c73-165">次の例では、`TestHealthCheckWithArgs` は <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> の呼び出し時に使用する整数と文字列を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="d4c73-166">`TestHealthCheckWithArgs` は実装に渡された整数と文字列を使用して `AddTypeActivatedCheck` を呼び出すことによって登録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="d4c73-167">正常性チェックのルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="d4c73-167">Use Health Checks Routing</span></span>

<span data-ttu-id="d4c73-168">`Startup.Configure` で、エンドポイントの URL または相対パスを使用して、エンドポイント ビルダーで `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="d4c73-169">ホストが必要</span><span class="sxs-lookup"><span data-stu-id="d4c73-169">Require host</span></span>

<span data-ttu-id="d4c73-170">`RequireHost` を呼び出して、正常性チェック エンドポイントに許可されている 1 つ以上のホストを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="d4c73-171">ホストは、punycode ではなく Unicode にする必要があります。また、ポートを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="d4c73-172">コレクションが指定されていない場合は、任意のホストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="d4c73-173">詳細については、「[ポート別のフィルター処理](#filter-by-port)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="d4c73-174">承認が必要</span><span class="sxs-lookup"><span data-stu-id="d4c73-174">Require authorization</span></span>

<span data-ttu-id="d4c73-175">`RequireAuthorization` を呼び出して、正常性チェック要求エンドポイントで承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="d4c73-176">`RequireAuthorization` のオーバーロードには 1 つ以上の承認ポリシーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="d4c73-177">ポリシーが指定されていない場合は、既定の承認ポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="d4c73-178">クロスオリジン要求 (CORS) の有効化</span><span class="sxs-lookup"><span data-stu-id="d4c73-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="d4c73-179">ブラウザーから手動で正常性チェックを実行することは一般的な使用シナリオではありませんが、正常性チェック エンドポイントで `RequireCors` を呼び出して CORS ミドルウェアを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="d4c73-180">`RequireCors` のオーバーロードには、CORS ポリシー ビルダーのデリゲート (`CorsPolicyBuilder`) またはポリシー名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="d4c73-181">ポリシーが指定されていない場合は、既定の CORS ポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="d4c73-182">詳細については、<xref:security/cors> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="d4c73-183">正常性チェック オプション</span><span class="sxs-lookup"><span data-stu-id="d4c73-183">Health check options</span></span>

<span data-ttu-id="d4c73-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> では、正常性チェックの動作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="d4c73-185">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="d4c73-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="d4c73-186">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d4c73-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="d4c73-187">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="d4c73-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="d4c73-188">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d4c73-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="d4c73-189">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="d4c73-189">Filter health checks</span></span>

<span data-ttu-id="d4c73-190">既定では、正常性チェック ミドルウェアでは、登録されている正常性チェックがすべて実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="d4c73-191">正常性チェックのサブセットを実行するには、<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> オプションにブール値を返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="d4c73-192">次の例では、関数の条件文にあるそのタグ (`bar_tag`) により `Bar` 正常性チェックが除外されます。`true` は、正常性チェックの <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> プロパティが `foo_tag` か `baz_tag` に一致した場合にのみ返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="d4c73-193">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="d4c73-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="d4c73-194">`Startup.Configure` では、`Predicate` によって 'Bar' 正常性チェックが除外されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="d4c73-195">Foo と Baz のみが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="d4c73-196">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d4c73-196">Customize the HTTP status code</span></span>

<span data-ttu-id="d4c73-197"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> を使用し、HTTP 状態コードに対する正常性状態のマッピングをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="d4c73-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="d4c73-198">次の <xref:Microsoft.AspNetCore.Http.StatusCodes> 代入はミドルウェアによって使用される既定値です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="d4c73-199">要件に合わせて状態コード値を変更します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="d4c73-200">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="d4c73-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="d4c73-201">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="d4c73-201">Suppress cache headers</span></span>

<span data-ttu-id="d4c73-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> によって、応答キャッシュを禁止する目的で、正常性チェック ミドルウェアによって HTTP ヘッダーがプローブ応答に追加されるかどうかが制御されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="d4c73-203">値が `false` (既定) の場合、応答キャッシュを禁止する目的で、ミドルウェアによってヘッダー `Cache-Control`、`Expires`、`Pragma` が設定またはオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="d4c73-204">値が `true` の場合、応答のキャッシュ ヘッダーがミドルウェアによって変更されることはありません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="d4c73-205">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="d4c73-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="d4c73-206">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d4c73-206">Customize output</span></span>

<span data-ttu-id="d4c73-207"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> オプションによって、応答の書き込みに使用される委任が取得または設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-207">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span>

<span data-ttu-id="d4c73-208">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="d4c73-208">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="d4c73-209">既定の委任では、文字列値 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) を含む、最小のプレーンテキスト応答が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-209">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="d4c73-210">次のカスタム デリゲート `WriteResponse` では、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-210">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
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

<span data-ttu-id="d4c73-211">正常性チェック システムには、複雑な JSON の戻り値の形式に関する組み込みのサポートが提供されていません。これは、形式が監視システムの選択に固有であるためです。</span><span class="sxs-lookup"><span data-stu-id="d4c73-211">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="d4c73-212">必要に応じて、前の例の `JObject` を自由にカスタマイズしてください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-212">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="d4c73-213">データベース プローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-213">Database probe</span></span>

<span data-ttu-id="d4c73-214">正常性チェックでは、データベースが通常どおり応答しているかどうかを示すブール値テストとして実行されるよう、データベース クエリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-214">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="d4c73-215">サンプル アプリでは、SQL Server データベース上で正常性のチェックを実行するために、ASP.NET Core アプリの正常性チェック ライブラリの [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-215">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="d4c73-216">`AspNetCore.Diagnostics.HealthChecks` によってデータベースに対して `SELECT 1` クエリが実行され、データベースへの接続が正常であることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-216">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="d4c73-217">クエリでデータベース接続を確認するとき、すぐに返されるクエリを選択します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-217">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="d4c73-218">このクエリ手法には、データベースをオーバーロードし、そのパフォーマンスを低下させるというリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-218">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="d4c73-219">ほとんどの場合、テスト クエリは実行する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-219">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="d4c73-220">データベースに正常に接続できれば十分です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-220">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="d4c73-221">クエリを実行する必要があれば、`SELECT 1` など、単純な SELECT クエリを選択してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-221">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="d4c73-222">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) へのパッケージ参照を含めます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-222">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="d4c73-223">サンプル アプリの *appsettings.json* ファイルに有効なデータベース接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-223">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="d4c73-224">このアプリでは `HealthCheckSample` という名前の SQL Server データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-224">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="d4c73-225">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-225">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c73-226">サンプル アプリでは、データベースの接続文字列 (*DbHealthStartup.cs*) を利用して `AddSqlServer` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-226">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4c73-227">正常性チェック エンドポイントを作成するには、`Startup.Configure` で `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-227">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="d4c73-228">サンプル アプリを使用してデータベース プローブ シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-228">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="d4c73-229">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-229">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="d4c73-230">Entity Framework Core DbContext プローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-230">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="d4c73-231">`DbContext` チェックでは、EF Core `DbContext` に対して構成されているデータベースとアプリとが通信できることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-231">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="d4c73-232">`DbContext` チェックは、次のようなアプリでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-232">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="d4c73-233">[Entity Framework (EF) Core を使用する](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-233">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="d4c73-234">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) へのパッケージ参照を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="d4c73-234">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="d4c73-235">`AddDbContextCheck<TContext>` によって `DbContext` の正常性チェックが登録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-235">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="d4c73-236">`DbContext` は `TContext` としてメソッドに指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-236">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="d4c73-237">オーバーロードはエラー状態、タグ、カスタム テスト クエリの設定に利用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-237">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="d4c73-238">既定では:</span><span class="sxs-lookup"><span data-stu-id="d4c73-238">By default:</span></span>

* <span data-ttu-id="d4c73-239">`DbContextHealthCheck` によって EF Core の `CanConnectAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-239">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="d4c73-240">`AddDbContextCheck` メソッドのオーバーロードを使用して正常性を確認するときに実行される操作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-240">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="d4c73-241">正常性チェックの名前は `TContext` 型の名前になります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-241">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="d4c73-242">サンプル アプリでは、`AppDbContext` が `AddDbContextCheck` に提供され、`Startup.ConfigureServices` でサービスとして登録されます (*DbContextHealthStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-242">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4c73-243">正常性チェック エンドポイントを作成するには、`Startup.Configure` で `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-243">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="d4c73-244">サンプル アプリを利用して `DbContext` プローブ シナリオを実行するには、接続文字列によって指定されるデータベースが SQL Server インスタンスに存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-244">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="d4c73-245">データベースが存在する場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-245">If the database exists, delete it.</span></span>

<span data-ttu-id="d4c73-246">コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-246">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="d4c73-247">アプリの実行後、ブラウザーで `/health` エンドポイントに要求することで正常性状態を確認します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-247">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="d4c73-248">データベースと `AppDbContext` は存在しません。そこで、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-248">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="d4c73-249">サンプル アプリにデータベースを作成させます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-249">Trigger the sample app to create the database.</span></span> <span data-ttu-id="d4c73-250">`/createdatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-250">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="d4c73-251">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-251">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4c73-252">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-252">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4c73-253">データベースとコンテキストが存在します。そのため、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-253">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="d4c73-254">サンプル アプリにデータベースを削除させます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-254">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="d4c73-255">`/deletedatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-255">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="d4c73-256">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-256">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4c73-257">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-257">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4c73-258">アプリから異常という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-258">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="d4c73-259">対応性と活動性に区分されるプローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-259">Separate readiness and liveness probes</span></span>

<span data-ttu-id="d4c73-260">一部のホスティング シナリオでは、2 つのアプリ状態を区別する一組の正常性チェックが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-260">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="d4c73-261">アプリは機能しているが、要求を受信する準備がまだできていない。</span><span class="sxs-lookup"><span data-stu-id="d4c73-261">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="d4c73-262">この状態がアプリの*対応性*です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-262">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="d4c73-263">アプリは機能しており、要求に応答する。</span><span class="sxs-lookup"><span data-stu-id="d4c73-263">The app is functioning and responding to requests.</span></span> <span data-ttu-id="d4c73-264">この状態がアプリの*活動性*です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-264">This state is the app's *liveness*.</span></span>

<span data-ttu-id="d4c73-265">対応性チェックは通常、より広範囲で時間のかかる一連のチェックが実行され、アプリのすべてのサブシステムとリソースが利用できるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-265">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="d4c73-266">活動性チェックでは、アプリが要求を処理できるかどうかを判断する簡易チェックのみが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-266">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="d4c73-267">アプリが対応性チェックに合格したら、活動性の確認のみを求める不経済な一連の活動性チェックをアプリに負わせる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-267">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="d4c73-268">サンプル アプリには、[ホステッド サービス](xref:fundamentals/host/hosted-services)の長時間実行スタートアップ タスクの完了を報告する正常性チェックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-268">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="d4c73-269">`StartupHostedServiceHealthCheck` によって `StartupTaskCompleted` というプロパティが公開されます。ホステッド サービスでは長時間実行タスクの完了時にこのプロパティを `true` に設定できます (*StartupHostedServiceHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-269">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="d4c73-270">長時間実行バックグラウンド タスクは[ホステッド サービス](xref:fundamentals/host/hosted-services)によって開始されます (*Services/StartupHostedService*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-270">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="d4c73-271">タスクが終わると、`StartupHostedServiceHealthCheck.StartupTaskCompleted` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-271">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="d4c73-272">ホステッド サービスと共に `Startup.ConfigureServices` で正常性チェックが <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に登録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-272">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="d4c73-273">ホステッド サービスでは正常性チェックにプロパティを設定する必要があるため、正常性チェックはサービス コンテナーにも登録されます (*LivenessProbeStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-273">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4c73-274">正常性チェック エンドポイントを作成するには、`Startup.Configure` で `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-274">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="d4c73-275">サンプル アプリでは、正常性チェック エンドポイントは次の場所に作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-275">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="d4c73-276">対応性チェックの場合は `/health/ready`。</span><span class="sxs-lookup"><span data-stu-id="d4c73-276">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="d4c73-277">対応性チェックでは、`ready` タグが与えられているものに正常性チェックが絞り込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-277">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="d4c73-278">活動性チェックの場合は `/health/live`。</span><span class="sxs-lookup"><span data-stu-id="d4c73-278">`/health/live` for the liveness check.</span></span> <span data-ttu-id="d4c73-279">活動性チェックでは、[HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) で `false` を返すことで `StartupHostedServiceHealthCheck` が除外されます (詳細については、「[正常性チェックをフィルター処理する](#filter-health-checks)」を参照してください)</span><span class="sxs-lookup"><span data-stu-id="d4c73-279">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="d4c73-280">次のコード例の内容:</span><span class="sxs-lookup"><span data-stu-id="d4c73-280">In the following example code:</span></span>

* <span data-ttu-id="d4c73-281">対応性チェックでは、登録されているすべてのチェックが 'ready' タグと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-281">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="d4c73-282">`Predicate` では、すべてのチェックが除外され、200-Ok が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-282">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="d4c73-283">サンプル アプリを使用して対応性/活動性構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-283">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="d4c73-284">ブラウザーで、15 秒が経過するまで `/health/ready` に数回アクセスします。</span><span class="sxs-lookup"><span data-stu-id="d4c73-284">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="d4c73-285">正常性チェックにより最初の 15 秒に対して "*異常*" が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-285">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="d4c73-286">15 秒後、エンドポイントから "*正常*" が報告されます。これは、ホステッド サービスによる長時間実行タスクが完了したことを反映しています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-286">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="d4c73-287">この例では、2 秒間の遅延で最初の対応性チェックを実行する正常性チェック パブリッシャー (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装) も作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-287">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="d4c73-288">詳細については、「[正常性チェック パブリッシャー](#health-check-publisher)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-288">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="d4c73-289">Kubernetes の例</span><span class="sxs-lookup"><span data-stu-id="d4c73-289">Kubernetes example</span></span>

<span data-ttu-id="d4c73-290">対応性チェックと活動性チェックを使い分けることは、[Kubernetes](https://kubernetes.io/) などの環境で便利です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-290">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="d4c73-291">Kubernetes では、要求を受け付ける前に、基礎をなすデータベースの可用性テストなど、時間のかかるスタートアップ作業の実行がアプリに要求されることがあります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-291">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="d4c73-292">別個のチェックを利用することで、アプリは機能しているがまだ準備ができていないか、あるいはアプリが起動に失敗したかをオーケストレーターは区別できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-292">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="d4c73-293">Kubernetes の対応性プローブと活動性プローブに関する詳細については、Kubernetes ドキュメントの「[Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)」 (活動性プローブと対応性プローブを設定する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-293">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="d4c73-294">次の例では、Kubernetes 対応性プローブの構成を確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-294">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="d4c73-295">カスタム応答ライターを含むメトリック ベースのプローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-295">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="d4c73-296">このサンプル アプリでは、カスタム応答ライターを含む、メモリ正常性チェックを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-296">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="d4c73-297">与えられたしきい値を超えるメモリがアプリで使用された場合、`MemoryHealthCheck` からは劣化状態が報告されます (このサンプル アプリでは 1 GB)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-297">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="d4c73-298"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> にはアプリのガベージ コレクター (GC) 情報が含まれています (*MemoryHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-298">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="d4c73-299">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-299">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c73-300"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に渡して正常性チェックを有効にする代わりに、`MemoryHealthCheck` がサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-300">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="d4c73-301"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> が登録されたサービスはすべて、正常性チェック サービスとミドルウェアで利用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-301">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="d4c73-302">正常性チェック サービスはシングルトン サービスとして登録することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d4c73-302">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="d4c73-303">サンプル アプリ (*CustomWriterStartup.cs*) の内容:</span><span class="sxs-lookup"><span data-stu-id="d4c73-303">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d4c73-304">正常性チェック エンドポイントを作成するには、`Startup.Configure` で `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-304">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="d4c73-305">`WriteResponse` 委任が `ResponseWriter` プロパティに与えられることで、正常性チェックの実行時に、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-305">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="d4c73-306">`WriteResponse` メソッドによって `CompositeHealthCheckResult` が書式設定されて JSON オブジェクトが生成され、正常性チェック応答のために JSON 出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-306">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="d4c73-307">サンプル アプリを使用してカスタム応答ライターを含むメトリックベースのプローブを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-307">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="d4c73-308">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、ディスク ストレージや最大値活動性のチェックなど、メトリックベースの正常性チェック シナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-308">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="d4c73-309">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-309">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="d4c73-310">ポート別のフィルター処理</span><span class="sxs-lookup"><span data-stu-id="d4c73-310">Filter by port</span></span>

<span data-ttu-id="d4c73-311">`MapHealthChecks` でポートを指定する URL パターンを使用して `RequireHost` を呼び出し、指定されたポートに正常性チェック要求を制限します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-311">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="d4c73-312">これは通常、サービスを監視するためのポートを公開する目的で、コンテナー環境で使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-312">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="d4c73-313">サンプル アプリによって、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を使用してポートが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-313">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d4c73-314">ポートは *launchSettings.json* ファイルに設定され、環境変数を介して構成プロバイダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-314">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="d4c73-315">管理ポートで要求を待つようにサーバーを設定する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-315">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="d4c73-316">サンプル アプリを使用し、管理ポート設定を実演するには、*Properties* フォルダーで *launchSettings.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-316">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="d4c73-317">サンプル アプリの次の *Properties/launchSettings.json* ファイルは、サンプル アプリのプロジェクト ファイルに含まれていないため、手動で作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-317">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="d4c73-318">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-318">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c73-319">`Startup.Configure` で `MapHealthChecks` を呼び出して、正常性チェック エンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-319">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="d4c73-320">サンプル アプリでは、`Startup.Configure` のエンドポイントで `RequireHost` を呼び出すと、構成の管理ポートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-320">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="d4c73-321">エンドポイントは、`Startup.Configure` のサンプル アプリで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-321">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="d4c73-322">次のコード例の内容:</span><span class="sxs-lookup"><span data-stu-id="d4c73-322">In the following example code:</span></span>

* <span data-ttu-id="d4c73-323">対応性チェックでは、登録されているすべてのチェックが 'ready' タグと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-323">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="d4c73-324">`Predicate` では、すべてのチェックが除外され、200-Ok が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-324">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="d4c73-325">管理ポートをコードに明示的に設定することで、サンプル アプリで *launchSettings.json* ファイルを作成することを回避できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-325">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="d4c73-326"><xref:Microsoft.Extensions.Hosting.HostBuilder> が作成される *Program.cs* で、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> の呼び出しを追加し、アプリの管理ポート エンドポイントを用意します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-326">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="d4c73-327">*ManagementPortStartup.cs* の `Configure` で、`RequireHost` を使用して管理ポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-327">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="d4c73-328">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d4c73-328">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="d4c73-329">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d4c73-329">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="d4c73-330">サンプル アプリを使用して管理ポート構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-330">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="d4c73-331">正常性チェック ライブラリを配布する</span><span class="sxs-lookup"><span data-stu-id="d4c73-331">Distribute a health check library</span></span>

<span data-ttu-id="d4c73-332">正常性チェックをライブラリとして配布するには:</span><span class="sxs-lookup"><span data-stu-id="d4c73-332">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="d4c73-333">スタンドアロン クラスとして <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装する正常性チェックを記述します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-333">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="d4c73-334">このクラスは、設定データにアクセスする目的で[依存関係挿入 (DI)](xref:fundamentals/dependency-injection)、型のアクティブ化、[名前付きオプション](xref:fundamentals/configuration/options)に依存できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-334">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="d4c73-335">`CheckHealthAsync` の正常性チェックのロジックでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-335">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="d4c73-336">`data1` と `data2` は、プローブの正常性チェック ロジックを実行するためにメソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-336">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="d4c73-337">`AccessViolationException` が処理されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-337">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="d4c73-338"><xref:System.AccessViolationException> が発生すると、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> とともに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> が返され、ユーザーは正常性チェックの失敗状態を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-338">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="d4c73-339">拡張メソッドを記述します。拡張メソッドを使用するアプリがその `Startup.Configure` メソッドで呼び出すパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-339">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="d4c73-340">次の例では、次の正常性チェック メソッド シグネチャを想定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-340">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="d4c73-341">先のシグネチャは、正常性チェック プローブ ロジックを処理する目的で `ExampleHealthCheck` に追加データが要求されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-341">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="d4c73-342">正常性チェックが拡張メソッドに登録されたときに正常性チェック インスタンスを作成するための委任にデータが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-342">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="d4c73-343">次の例では、呼び出し元によって次が任意で指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-343">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="d4c73-344">正常性チェック名 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-344">health check name (`name`).</span></span> <span data-ttu-id="d4c73-345">`null` の場合、`example_health_check` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-345">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="d4c73-346">正常性チェックの文字列データ ポイント (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-346">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="d4c73-347">正常性チェックの整数データ ポイント (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-347">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="d4c73-348">`null` の場合、`1` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-348">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="d4c73-349">エラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-349">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="d4c73-350">既定値は、`null` です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-350">The default is `null`.</span></span> <span data-ttu-id="d4c73-351">`null` の場合、エラー状態に対して [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-351">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="d4c73-352">タグ (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-352">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="d4c73-353">正常性チェック パブリッシャー</span><span class="sxs-lookup"><span data-stu-id="d4c73-353">Health Check Publisher</span></span>

<span data-ttu-id="d4c73-354">サービス コンテナーに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> が追加されると、正常性チェック システムにより定期的に正常性チェックが実行され、結果と共に `PublishAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-354">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="d4c73-355">これは、正常性を判断する目的で監視システムを定期的に呼び出すことを各プロセスに求めるプッシュベースの監視システム シナリオで便利です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-355">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="d4c73-356"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インターフェイスには単一メソッドが与えられます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-356">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="d4c73-357"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> を使うと次を設定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-357"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="d4c73-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; アプリが起動した後、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスを実行する前に適用される初期遅延です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4c73-359">遅延はスタートアップ時に一度だけ適用され、以降の繰り返しには適用されません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-359">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="d4c73-360">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-360">The default value is five seconds.</span></span>
* <span data-ttu-id="d4c73-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> を実行する期間です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="d4c73-362">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-362">The default value is 30 seconds.</span></span>
* <span data-ttu-id="d4c73-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> が `null` (既定値) の場合、正常性チェック パブリッシャー サービスにより登録済みのすべての正常性チェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="d4c73-364">正常性チェックのサブセットを実行するには、チェックのセットをフィルター処理する関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-364">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="d4c73-365">述語は期間ごとに評価されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-365">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="d4c73-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; すべての <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスに対する正常性チェックの実行のタイムアウトです。</span><span class="sxs-lookup"><span data-stu-id="d4c73-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4c73-367">タイムアウトなしで実行するには <xref:System.Threading.Timeout.InfiniteTimeSpan> を使います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-367">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="d4c73-368">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-368">The default value is 30 seconds.</span></span>

<span data-ttu-id="d4c73-369">サンプル アプリでは、`ReadinessPublisher` が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-369">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="d4c73-370">正常性チェックの状態は、チェックごとに以下のログ レベルでログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-370">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="d4c73-371">情報 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>): 正常性チェックの状態が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c73-371">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="d4c73-372">エラー (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) 状態が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> または <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c73-372">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="d4c73-373">サンプル アプリの `LivenessProbeStartup` の例では、対応性チェック `StartupHostedService` にはスタートアップの遅延が 2 秒間あり、30 秒ごとにチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-373">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="d4c73-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装をアクティブ化するために、サンプルでは `ReadinessPublisher` をシングルトン サービスとして[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーに登録しています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-374">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="d4c73-375">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、[Application Insights](/azure/application-insights/app-insights-overview) など、いくつかのシステムのパブリッシャーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-375">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="d4c73-376">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-376">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="d4c73-377">MapWhen で正常性チェックを制限する</span><span class="sxs-lookup"><span data-stu-id="d4c73-377">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="d4c73-378"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> を使用して、正常性チェック エンドポイントの要求パイプラインを条件付きで分岐します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-378">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="d4c73-379">次の例では、`api/HealthCheck` エンドポイントに対して GET 要求が受信された場合に、`MapWhen` が要求パイプラインを分岐して正常性チェック ミドルウェアをアクティブ化します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-379">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="d4c73-380">詳細については、<xref:fundamentals/middleware/index#use-run-and-map> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-380">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4c73-381">ASP.NET Core からは、アプリ インフラストラクチャ コンポーネントの正常性を報告するための正常性チェック ミドルウェアとライブラリが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-381">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="d4c73-382">正常性チェックは HTTP エンドポイントとしてアプリによって公開されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-382">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="d4c73-383">正常性チェック エンドポイントは、さまざまなリアルタイム監視シナリオに合わせて設定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-383">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="d4c73-384">正常性プローブは、アプリの状態を確認する目的でコンテナー オーケストレーターとロード バランサーによって使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-384">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="d4c73-385">たとえば、正常性チェックで問題が確認された場合、コンテナー オーケストレーターは実行中の展開を停止したり、コンテナーを再起動したりします。</span><span class="sxs-lookup"><span data-stu-id="d4c73-385">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="d4c73-386">ロード バランサーはアプリの異常に対処するため、問題のあるインスタンスから正常なインスタンスにトラフィック経路を変更することがあります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-386">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="d4c73-387">メモリ、ディスク、その他の物理サーバー リソースの使用を監視し、正常性の状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-387">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="d4c73-388">正常性チェックでは、データベースや外部サービス エンドポイントなど、アプリの依存関係をテストし、それらが利用できることと正常に機能していることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-388">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="d4c73-389">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d4c73-389">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d4c73-390">サンプル アプリには、このトピックで説明されているシナリオの例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-390">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="d4c73-391">所与のシナリオに対してサンプル アプリを実行するには、コマンド シェルでプロジェクトのフォルダーから [dotnet run](/dotnet/core/tools/dotnet-run) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-391">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="d4c73-392">サンプル アプリの詳しい使用方法については、サンプル アプリの *README.md* ファイルと本トピックのシナリオ説明をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-392">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4c73-393">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d4c73-393">Prerequisites</span></span>

<span data-ttu-id="d4c73-394">正常性チェックは通常、アプリの状態を確認する目的で、外部の監視サービスまたはコンテナー オーケストレーターと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-394">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="d4c73-395">正常性チェックをアプリに追加する前に、使用する監視システムを決定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-395">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="d4c73-396">監視システムからは、作成する正常性チェックの種類とそのエンドポイントの設定方法が指示されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-396">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="d4c73-397">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-397">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="d4c73-398">サンプル アプリからは、いくつかのシナリオで正常性チェックを実演するスタートアップ コードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-398">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="d4c73-399">[データベース プローブ](#database-probe) シナリオでは、[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) を使用して、データベース接続の正常性がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-399">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="d4c73-400">[DbContext プローブ](#entity-framework-core-dbcontext-probe) シナリオでは、EF Core `DbContext` を使用して、データベースがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-400">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="d4c73-401">データベース シナリオを探索するために、サンプル アプリでは次のことが行われます:</span><span class="sxs-lookup"><span data-stu-id="d4c73-401">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="d4c73-402">データベースを作成して、*appsettings.json* ファイルにその接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-402">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="d4c73-403">そのプロジェクト ファイルに次のパッケージ参照が含まれています:</span><span class="sxs-lookup"><span data-stu-id="d4c73-403">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="d4c73-404">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="d4c73-404">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="d4c73-405">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d4c73-405">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="d4c73-406">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-406">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="d4c73-407">もう 1 つの正常性チェックのシナリオでは、1 つの管理ポートに正常性チェックを絞り込む方法が実演されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-407">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="d4c73-408">このサンプル アプリでは、管理 URL と管理ポートを含む *Properties/launchSettings.json* ファイルを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-408">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="d4c73-409">詳細については、「[ポート別のフィルター処理](#filter-by-port)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-409">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="d4c73-410">基本的な正常性プローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-410">Basic health probe</span></span>

<span data-ttu-id="d4c73-411">多くのアプリでは、アプリが要求を処理できること (*活動性*) を報告する基本的な正常性チェック構成で十分にアプリの状態を検出できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-411">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="d4c73-412">基本の構成では、正常性チェック サービスを登録し、正常性チェック ミドルウェアを呼び出します。このミドルウェアが特定の URL エンドポイントにおける正常性を返します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-412">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="d4c73-413">既定では、特定の依存関係またはサブシステムをテストする正常性チェックは登録されていません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-413">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="d4c73-414">正常性エンドポイント URL で応答できる場合、そのアプリは正常であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-414">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="d4c73-415">既定の応答ライターによって、プレーンテキストの応答として状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) がクライアントに書き込まれます。このとき、状態として [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、または [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が示されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-415">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="d4c73-416">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-416">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c73-417">`Startup.Configure` の要求処理パイプラインで、<xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を使用して正常性チェック ミドルウェアのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-417">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="d4c73-418">サンプル アプリでは、正常性チェック エンドポイントは `/health` で作成されます (*BasicStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-418">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="d4c73-419">サンプル アプリを使用して基本的な構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-419">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="d4c73-420">Docker の例</span><span class="sxs-lookup"><span data-stu-id="d4c73-420">Docker example</span></span>

<span data-ttu-id="d4c73-421">[Docker](xref:host-and-deploy/docker/index) からは、組み込み `HEALTHCHECK` ディレクティブが提供されます。これを使用し、基本的な正常性チェック構成を使用するアプリの状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-421">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="d4c73-422">正常性チェックを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c73-422">Create health checks</span></span>

<span data-ttu-id="d4c73-423">正常性チェックは <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-423">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="d4c73-424"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> メソッドによって、`Healthy`、`Degraded`、`Unhealthy` のいずれかの正常性を示す <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-424">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="d4c73-425">この結果が構成可能な状態コードと共にプレーンテキストの応答として書き込まれます (構成の説明は「[正常性チェック オプション](#health-check-options)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-425">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="d4c73-426"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> からは、任意のキーと値のペアを返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-426"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="d4c73-427">正常性チェックの例</span><span class="sxs-lookup"><span data-stu-id="d4c73-427">Example health check</span></span>

<span data-ttu-id="d4c73-428">次の `ExampleHealthCheck` クラスからは、正常性チェックのレイアウトがどのようなものかがわかります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-428">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="d4c73-429">正常性チェックのロジックが `CheckHealthAsync` メソッドに配置されています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-429">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="d4c73-430">次の例では、ダミー変数 `healthCheckResultHealthy` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-430">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="d4c73-431">`healthCheckResultHealthy` の値が `false` に設定されている場合、[HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 状態が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-431">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
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

### <a name="register-health-check-services"></a><span data-ttu-id="d4c73-432">正常性チェック サービスを登録する</span><span class="sxs-lookup"><span data-stu-id="d4c73-432">Register health check services</span></span>

<span data-ttu-id="d4c73-433">`ExampleHealthCheck` 型は、`Startup.ConfigureServices` で <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> を使用して正常性チェック サービスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-433">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="d4c73-434">次の例にある <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> オーバーロードでは、正常性チェックでエラーが報告されると、レポートにエラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-434">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="d4c73-435">エラー状態が `null` (既定) に設定された場合、[HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-435">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="d4c73-436">このオーバーロードはライブラリ作成者にとって便利です。この設定に正常性チェック実装が従う場合、正常性チェック エラーが発生したとき、ライブラリによって指示されるエラー状態がアプリによって適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-436">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="d4c73-437">*タグ*を使用し、正常性チェックをフィルター処理できます (詳細は「[正常性チェックをフィルター処理する](#filter-health-checks)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-437">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="d4c73-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> では、lambda 関数も実行できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="d4c73-439">次の `Startup.ConfigureServices` の例では、正常性チェック名に `Example` が指定されています。このチェックでは状態として常に正常が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-439">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="d4c73-440">正常性チェック ミドルウェアを使用する</span><span class="sxs-lookup"><span data-stu-id="d4c73-440">Use Health Checks Middleware</span></span>

<span data-ttu-id="d4c73-441">`Startup.Configure` で、エンドポイント URL または相対パスを利用し、処理パイプラインで <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-441">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="d4c73-442">正常性チェックに特定のポートで待機させる場合、<xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> のオーバーロードを使用してポートを設定します ([詳細は「ポート別のフィルター処理」セクションにあります](#filter-by-port))。</span><span class="sxs-lookup"><span data-stu-id="d4c73-442">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="d4c73-443">正常性チェック オプション</span><span class="sxs-lookup"><span data-stu-id="d4c73-443">Health check options</span></span>

<span data-ttu-id="d4c73-444"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> では、正常性チェックの動作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-444"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="d4c73-445">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="d4c73-445">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="d4c73-446">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d4c73-446">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="d4c73-447">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="d4c73-447">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="d4c73-448">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d4c73-448">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="d4c73-449">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="d4c73-449">Filter health checks</span></span>

<span data-ttu-id="d4c73-450">既定では、正常性チェック ミドルウェアでは、登録されている正常性チェックがすべて実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-450">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="d4c73-451">正常性チェックのサブセットを実行するには、<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> オプションにブール値を返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-451">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="d4c73-452">次の例では、関数の条件文にあるそのタグ (`bar_tag`) により `Bar` 正常性チェックが除外されます。`true` は、正常性チェックの <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> プロパティが `foo_tag` か `baz_tag` に一致した場合にのみ返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-452">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="d4c73-453">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d4c73-453">Customize the HTTP status code</span></span>

<span data-ttu-id="d4c73-454"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> を使用し、HTTP 状態コードに対する正常性状態のマッピングをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="d4c73-454">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="d4c73-455">次の <xref:Microsoft.AspNetCore.Http.StatusCodes> 代入はミドルウェアによって使用される既定値です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-455">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="d4c73-456">要件に合わせて状態コード値を変更します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-456">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="d4c73-457">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="d4c73-457">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="d4c73-458">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="d4c73-458">Suppress cache headers</span></span>

<span data-ttu-id="d4c73-459"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> によって、応答キャッシュを禁止する目的で、正常性チェック ミドルウェアによって HTTP ヘッダーがプローブ応答に追加されるかどうかが制御されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-459"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="d4c73-460">値が `false` (既定) の場合、応答キャッシュを禁止する目的で、ミドルウェアによってヘッダー `Cache-Control`、`Expires`、`Pragma` が設定またはオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-460">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="d4c73-461">値が `true` の場合、応答のキャッシュ ヘッダーがミドルウェアによって変更されることはありません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-461">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="d4c73-462">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="d4c73-462">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="d4c73-463">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d4c73-463">Customize output</span></span>

<span data-ttu-id="d4c73-464"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> オプションによって、応答の書き込みに使用される委任が取得または設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-464">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="d4c73-465">既定の委任では、文字列値 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) を含む、最小のプレーンテキスト応答が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-465">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="d4c73-466">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="d4c73-466">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="d4c73-467">既定の委任では、文字列値 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) を含む、最小のプレーンテキスト応答が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-467">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="d4c73-468">次のカスタム デリゲート `WriteResponse` では、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-468">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
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

<span data-ttu-id="d4c73-469">正常性チェック システムには、複雑な JSON の戻り値の形式に関する組み込みのサポートが提供されていません。これは、形式が監視システムの選択に固有であるためです。</span><span class="sxs-lookup"><span data-stu-id="d4c73-469">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="d4c73-470">必要に応じて、前の例の `JObject` を自由にカスタマイズしてください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-470">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="d4c73-471">データベース プローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-471">Database probe</span></span>

<span data-ttu-id="d4c73-472">正常性チェックでは、データベースが通常どおり応答しているかどうかを示すブール値テストとして実行されるよう、データベース クエリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-472">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="d4c73-473">サンプル アプリでは、SQL Server データベース上で正常性のチェックを実行するために、ASP.NET Core アプリの正常性チェック ライブラリの [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-473">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="d4c73-474">`AspNetCore.Diagnostics.HealthChecks` によってデータベースに対して `SELECT 1` クエリが実行され、データベースへの接続が正常であることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-474">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="d4c73-475">クエリでデータベース接続を確認するとき、すぐに返されるクエリを選択します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-475">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="d4c73-476">このクエリ手法には、データベースをオーバーロードし、そのパフォーマンスを低下させるというリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-476">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="d4c73-477">ほとんどの場合、テスト クエリは実行する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-477">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="d4c73-478">データベースに正常に接続できれば十分です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-478">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="d4c73-479">クエリを実行する必要があれば、`SELECT 1` など、単純な SELECT クエリを選択してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-479">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="d4c73-480">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) へのパッケージ参照を含めます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-480">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="d4c73-481">サンプル アプリの *appsettings.json* ファイルに有効なデータベース接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-481">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="d4c73-482">このアプリでは `HealthCheckSample` という名前の SQL Server データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-482">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="d4c73-483">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-483">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c73-484">サンプル アプリでは、データベースの接続文字列 (*DbHealthStartup.cs*) を利用して `AddSqlServer` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-484">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4c73-485">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-485">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="d4c73-486">サンプル アプリを使用してデータベース プローブ シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-486">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="d4c73-487">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-487">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="d4c73-488">Entity Framework Core DbContext プローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-488">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="d4c73-489">`DbContext` チェックでは、EF Core `DbContext` に対して構成されているデータベースとアプリとが通信できることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-489">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="d4c73-490">`DbContext` チェックは、次のようなアプリでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-490">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="d4c73-491">[Entity Framework (EF) Core を使用する](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-491">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="d4c73-492">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) へのパッケージ参照を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="d4c73-492">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="d4c73-493">`AddDbContextCheck<TContext>` によって `DbContext` の正常性チェックが登録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-493">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="d4c73-494">`DbContext` は `TContext` としてメソッドに指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-494">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="d4c73-495">オーバーロードはエラー状態、タグ、カスタム テスト クエリの設定に利用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-495">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="d4c73-496">既定では:</span><span class="sxs-lookup"><span data-stu-id="d4c73-496">By default:</span></span>

* <span data-ttu-id="d4c73-497">`DbContextHealthCheck` によって EF Core の `CanConnectAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-497">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="d4c73-498">`AddDbContextCheck` メソッドのオーバーロードを使用して正常性を確認するときに実行される操作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-498">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="d4c73-499">正常性チェックの名前は `TContext` 型の名前になります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-499">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="d4c73-500">サンプル アプリでは、`AppDbContext` が `AddDbContextCheck` に提供され、`Startup.ConfigureServices` でサービスとして登録されます (*DbContextHealthStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-500">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4c73-501">サンプル アプリで、`UseHealthChecks` によって `Startup.Configure` に正常性チェック ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-501">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="d4c73-502">サンプル アプリを利用して `DbContext` プローブ シナリオを実行するには、接続文字列によって指定されるデータベースが SQL Server インスタンスに存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-502">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="d4c73-503">データベースが存在する場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-503">If the database exists, delete it.</span></span>

<span data-ttu-id="d4c73-504">コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-504">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="d4c73-505">アプリの実行後、ブラウザーで `/health` エンドポイントに要求することで正常性状態を確認します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-505">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="d4c73-506">データベースと `AppDbContext` は存在しません。そこで、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-506">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="d4c73-507">サンプル アプリにデータベースを作成させます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-507">Trigger the sample app to create the database.</span></span> <span data-ttu-id="d4c73-508">`/createdatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-508">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="d4c73-509">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-509">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4c73-510">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-510">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4c73-511">データベースとコンテキストが存在します。そのため、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-511">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="d4c73-512">サンプル アプリにデータベースを削除させます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-512">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="d4c73-513">`/deletedatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-513">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="d4c73-514">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-514">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d4c73-515">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-515">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d4c73-516">アプリから異常という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-516">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="d4c73-517">対応性と活動性に区分されるプローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-517">Separate readiness and liveness probes</span></span>

<span data-ttu-id="d4c73-518">一部のホスティング シナリオでは、2 つのアプリ状態を区別する一組の正常性チェックが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-518">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="d4c73-519">アプリは機能しているが、要求を受信する準備がまだできていない。</span><span class="sxs-lookup"><span data-stu-id="d4c73-519">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="d4c73-520">この状態がアプリの*対応性*です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-520">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="d4c73-521">アプリは機能しており、要求に応答する。</span><span class="sxs-lookup"><span data-stu-id="d4c73-521">The app is functioning and responding to requests.</span></span> <span data-ttu-id="d4c73-522">この状態がアプリの*活動性*です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-522">This state is the app's *liveness*.</span></span>

<span data-ttu-id="d4c73-523">対応性チェックは通常、より広範囲で時間のかかる一連のチェックが実行され、アプリのすべてのサブシステムとリソースが利用できるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-523">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="d4c73-524">活動性チェックでは、アプリが要求を処理できるかどうかを判断する簡易チェックのみが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-524">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="d4c73-525">アプリが対応性チェックに合格したら、活動性の確認のみを求める不経済な一連の活動性チェックをアプリに負わせる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-525">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="d4c73-526">サンプル アプリには、[ホステッド サービス](xref:fundamentals/host/hosted-services)の長時間実行スタートアップ タスクの完了を報告する正常性チェックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-526">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="d4c73-527">`StartupHostedServiceHealthCheck` によって `StartupTaskCompleted` というプロパティが公開されます。ホステッド サービスでは長時間実行タスクの完了時にこのプロパティを `true` に設定できます (*StartupHostedServiceHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-527">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="d4c73-528">長時間実行バックグラウンド タスクは[ホステッド サービス](xref:fundamentals/host/hosted-services)によって開始されます (*Services/StartupHostedService*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-528">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="d4c73-529">タスクが終わると、`StartupHostedServiceHealthCheck.StartupTaskCompleted` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-529">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="d4c73-530">ホステッド サービスと共に `Startup.ConfigureServices` で正常性チェックが <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に登録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-530">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="d4c73-531">ホステッド サービスでは正常性チェックにプロパティを設定する必要があるため、正常性チェックはサービス コンテナーにも登録されます (*LivenessProbeStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-531">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d4c73-532">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-532">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="d4c73-533">サンプル アプリで、対応性チェックのための正常性チェック エンドポイントが `/health/ready` で作成され、活動性チェックのための正常性チェック エンドポイントが `/health/live` で作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-533">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="d4c73-534">対応性チェックでは、`ready` タグが与えられているものに正常性チェックが絞り込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-534">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="d4c73-535">活動性チェックでは、[HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) で `false` を返すことで `StartupHostedServiceHealthCheck` が除外されます (詳細については、「[正常性チェックをフィルター処理する](#filter-health-checks)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-535">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="d4c73-536">サンプル アプリを使用して対応性/活動性構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-536">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="d4c73-537">ブラウザーで、15 秒が経過するまで `/health/ready` に数回アクセスします。</span><span class="sxs-lookup"><span data-stu-id="d4c73-537">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="d4c73-538">正常性チェックにより最初の 15 秒に対して "*異常*" が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-538">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="d4c73-539">15 秒後、エンドポイントから "*正常*" が報告されます。これは、ホステッド サービスによる長時間実行タスクが完了したことを反映しています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-539">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="d4c73-540">この例では、2 秒間の遅延で最初の対応性チェックを実行する正常性チェック パブリッシャー (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装) も作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-540">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="d4c73-541">詳細については、「[正常性チェック パブリッシャー](#health-check-publisher)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-541">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="d4c73-542">Kubernetes の例</span><span class="sxs-lookup"><span data-stu-id="d4c73-542">Kubernetes example</span></span>

<span data-ttu-id="d4c73-543">対応性チェックと活動性チェックを使い分けることは、[Kubernetes](https://kubernetes.io/) などの環境で便利です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-543">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="d4c73-544">Kubernetes では、要求を受け付ける前に、基礎をなすデータベースの可用性テストなど、時間のかかるスタートアップ作業の実行がアプリに要求されることがあります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-544">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="d4c73-545">別個のチェックを利用することで、アプリは機能しているがまだ準備ができていないか、あるいはアプリが起動に失敗したかをオーケストレーターは区別できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-545">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="d4c73-546">Kubernetes の対応性プローブと活動性プローブに関する詳細については、Kubernetes ドキュメントの「[Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)」 (活動性プローブと対応性プローブを設定する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-546">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="d4c73-547">次の例では、Kubernetes 対応性プローブの構成を確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-547">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="d4c73-548">カスタム応答ライターを含むメトリック ベースのプローブ</span><span class="sxs-lookup"><span data-stu-id="d4c73-548">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="d4c73-549">このサンプル アプリでは、カスタム応答ライターを含む、メモリ正常性チェックを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-549">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="d4c73-550">与えられたしきい値を超えるメモリがアプリで使用された場合 (このサンプル アプリでは 1 GB)、`MemoryHealthCheck` から異常状態が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-550">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="d4c73-551"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> にはアプリのガベージ コレクター (GC) 情報が含まれています (*MemoryHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-551">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="d4c73-552">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-552">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c73-553"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に渡して正常性チェックを有効にする代わりに、`MemoryHealthCheck` がサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-553">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="d4c73-554"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> が登録されたサービスはすべて、正常性チェック サービスとミドルウェアで利用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-554">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="d4c73-555">正常性チェック サービスはシングルトン サービスとして登録することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d4c73-555">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="d4c73-556">サンプル アプリ (*CustomWriterStartup.cs*) の内容:</span><span class="sxs-lookup"><span data-stu-id="d4c73-556">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d4c73-557">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-557">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="d4c73-558">`WriteResponse` 委任が `ResponseWriter` プロパティに与えられることで、正常性チェックの実行時に、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-558">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="d4c73-559">`WriteResponse` メソッドによって `CompositeHealthCheckResult` が書式設定されて JSON オブジェクトが生成され、正常性チェック応答のために JSON 出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-559">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="d4c73-560">サンプル アプリを使用してカスタム応答ライターを含むメトリックベースのプローブを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-560">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="d4c73-561">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、ディスク ストレージや最大値活動性のチェックなど、メトリックベースの正常性チェック シナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-561">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="d4c73-562">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-562">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="d4c73-563">ポート別のフィルター処理</span><span class="sxs-lookup"><span data-stu-id="d4c73-563">Filter by port</span></span>

<span data-ttu-id="d4c73-564">ポートを指定して <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を呼び出すと、正常性チェック要求は指定されたポートに制限されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-564">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="d4c73-565">これは通常、サービスを監視するためのポートを公開する目的で、コンテナー環境で使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-565">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="d4c73-566">サンプル アプリによって、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を使用してポートが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-566">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d4c73-567">ポートは *launchSettings.json* ファイルに設定され、環境変数を介して構成プロバイダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-567">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="d4c73-568">管理ポートで要求を待つようにサーバーを設定する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-568">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="d4c73-569">サンプル アプリを使用し、管理ポート設定を実演するには、*Properties* フォルダーで *launchSettings.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-569">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="d4c73-570">サンプル アプリの次の *Properties/launchSettings.json* ファイルは、サンプル アプリのプロジェクト ファイルに含まれていないため、手動で作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-570">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="d4c73-571">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-571">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4c73-572"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> の呼び出しによって管理ポートが指定されます (*ManagementPortStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-572">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="d4c73-573">URL と管理ポートをコードに明示的に設定することで、サンプル アプリで *launchSettings.json* ファイルを作成することを回避できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-573">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="d4c73-574"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が作成される *Program.cs* で、<xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> の呼び出しを追加し、アプリの通常の応答エンドポイントと管理ポート エンドポイントを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-574">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="d4c73-575"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> が呼び出される *ManagementPortStartup.cs* で、管理ポートを明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-575">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="d4c73-576">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d4c73-576">*Program.cs*:</span></span>
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
> <span data-ttu-id="d4c73-577">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d4c73-577">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="d4c73-578">サンプル アプリを使用して管理ポート構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-578">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="d4c73-579">正常性チェック ライブラリを配布する</span><span class="sxs-lookup"><span data-stu-id="d4c73-579">Distribute a health check library</span></span>

<span data-ttu-id="d4c73-580">正常性チェックをライブラリとして配布するには:</span><span class="sxs-lookup"><span data-stu-id="d4c73-580">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="d4c73-581">スタンドアロン クラスとして <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装する正常性チェックを記述します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-581">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="d4c73-582">このクラスは、設定データにアクセスする目的で[依存関係挿入 (DI)](xref:fundamentals/dependency-injection)、型のアクティブ化、[名前付きオプション](xref:fundamentals/configuration/options)に依存できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-582">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="d4c73-583">`CheckHealthAsync` の正常性チェックのロジックでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d4c73-583">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="d4c73-584">`data1` と `data2` は、プローブの正常性チェック ロジックを実行するためにメソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-584">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="d4c73-585">`AccessViolationException` が処理されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-585">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="d4c73-586"><xref:System.AccessViolationException> が発生すると、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> とともに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> が返され、ユーザーは正常性チェックの失敗状態を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-586">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

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
   ```

1. <span data-ttu-id="d4c73-587">拡張メソッドを記述します。拡張メソッドを使用するアプリがその `Startup.Configure` メソッドで呼び出すパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-587">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="d4c73-588">次の例では、次の正常性チェック メソッド シグネチャを想定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-588">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="d4c73-589">先のシグネチャは、正常性チェック プローブ ロジックを処理する目的で `ExampleHealthCheck` に追加データが要求されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-589">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="d4c73-590">正常性チェックが拡張メソッドに登録されたときに正常性チェック インスタンスを作成するための委任にデータが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-590">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="d4c73-591">次の例では、呼び出し元によって次が任意で指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-591">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="d4c73-592">正常性チェック名 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-592">health check name (`name`).</span></span> <span data-ttu-id="d4c73-593">`null` の場合、`example_health_check` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-593">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="d4c73-594">正常性チェックの文字列データ ポイント (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-594">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="d4c73-595">正常性チェックの整数データ ポイント (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-595">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="d4c73-596">`null` の場合、`1` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-596">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="d4c73-597">エラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-597">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="d4c73-598">既定値は、`null` です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-598">The default is `null`.</span></span> <span data-ttu-id="d4c73-599">`null` の場合、エラー状態に対して [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-599">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="d4c73-600">タグ (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="d4c73-600">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="d4c73-601">正常性チェック パブリッシャー</span><span class="sxs-lookup"><span data-stu-id="d4c73-601">Health Check Publisher</span></span>

<span data-ttu-id="d4c73-602">サービス コンテナーに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> が追加されると、正常性チェック システムにより定期的に正常性チェックが実行され、結果と共に `PublishAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-602">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="d4c73-603">これは、正常性を判断する目的で監視システムを定期的に呼び出すことを各プロセスに求めるプッシュベースの監視システム シナリオで便利です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-603">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="d4c73-604"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インターフェイスには単一メソッドが与えられます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-604">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="d4c73-605"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> を使うと次を設定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-605"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="d4c73-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; アプリが起動した後、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスを実行する前に適用される初期遅延です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4c73-607">遅延はスタートアップ時に一度だけ適用され、以降の繰り返しには適用されません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-607">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="d4c73-608">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-608">The default value is five seconds.</span></span>
* <span data-ttu-id="d4c73-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> を実行する期間です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="d4c73-610">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-610">The default value is 30 seconds.</span></span>
* <span data-ttu-id="d4c73-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> が `null` (既定値) の場合、正常性チェック パブリッシャー サービスにより登録済みのすべての正常性チェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="d4c73-612">正常性チェックのサブセットを実行するには、チェックのセットをフィルター処理する関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-612">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="d4c73-613">述語は期間ごとに評価されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-613">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="d4c73-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; すべての <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスに対する正常性チェックの実行のタイムアウトです。</span><span class="sxs-lookup"><span data-stu-id="d4c73-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d4c73-615">タイムアウトなしで実行するには <xref:System.Threading.Timeout.InfiniteTimeSpan> を使います。</span><span class="sxs-lookup"><span data-stu-id="d4c73-615">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="d4c73-616">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-616">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="d4c73-617">ASP.NET Core 2.2 のリリースでは、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> を設定しても <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装により無視されます。<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> の値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-617">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="d4c73-618">この問題は ASP.NET Core 3.0 で解決しています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-618">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="d4c73-619">サンプル アプリでは、`ReadinessPublisher` が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装です。</span><span class="sxs-lookup"><span data-stu-id="d4c73-619">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="d4c73-620">正常性チェックの状態は、チェックごとに以下のいずれかでログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-620">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="d4c73-621">情報 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>): 正常性チェックの状態が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c73-621">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="d4c73-622">エラー (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) 状態が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> または <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c73-622">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="d4c73-623">サンプル アプリの `LivenessProbeStartup` の例では、対応性チェック `StartupHostedService` にはスタートアップの遅延が 2 秒間あり、30 秒ごとにチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-623">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="d4c73-624"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装をアクティブ化するために、サンプルでは `ReadinessPublisher` をシングルトン サービスとして[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーに登録しています。</span><span class="sxs-lookup"><span data-stu-id="d4c73-624">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="d4c73-625">1 つ以上の他のホステッド サービスが既にアプリに追加されている場合、次の回避策により <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスをサービス コンテナーに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-625">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="d4c73-626">ASP.NET Core 3.0 では、この回避策は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-626">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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

> [!NOTE]
> <span data-ttu-id="d4c73-627">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、[Application Insights](/azure/application-insights/app-insights-overview) など、いくつかのシステムのパブリッシャーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c73-627">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="d4c73-628">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="d4c73-628">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="d4c73-629">MapWhen で正常性チェックを制限する</span><span class="sxs-lookup"><span data-stu-id="d4c73-629">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="d4c73-630"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> を使用して、正常性チェック エンドポイントの要求パイプラインを条件付きで分岐します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-630">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="d4c73-631">次の例では、`api/HealthCheck` エンドポイントに対して GET 要求が受信された場合に、`MapWhen` が要求パイプラインを分岐して正常性チェック ミドルウェアをアクティブ化します。</span><span class="sxs-lookup"><span data-stu-id="d4c73-631">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="d4c73-632">詳細については、<xref:fundamentals/middleware/index#use-run-and-map> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c73-632">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
