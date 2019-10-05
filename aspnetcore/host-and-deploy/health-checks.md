---
title: ASP.NET Core の正常性チェック
author: guardrex
description: アプリやデータベースなど、ASP.NET Core インフラストラクチャの正常性チェックを設定する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 09/27/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: e4b2a577815335078f7e0c9128144a514e42a6c3
ms.sourcegitcommit: 5d25a7f22c50ca6fdd0f8ecd8e525822e1b35b7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2019
ms.locfileid: "71482051"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="590eb-103">ASP.NET Core の正常性チェック</span><span class="sxs-lookup"><span data-stu-id="590eb-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="590eb-104">作成者: [Luke Latham](https://github.com/guardrex) と [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="590eb-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="590eb-105">ASP.NET Core からは、アプリ インフラストラクチャ コンポーネントの正常性を報告するための正常性チェック ミドルウェアとライブラリが提供されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="590eb-106">正常性チェックは HTTP エンドポイントとしてアプリによって公開されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="590eb-107">正常性チェック エンドポイントは、さまざまなリアルタイム監視シナリオに合わせて設定できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="590eb-108">正常性プローブは、アプリの状態を確認する目的でコンテナー オーケストレーターとロード バランサーによって使用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="590eb-109">たとえば、正常性チェックで問題が確認された場合、コンテナー オーケストレーターは実行中の展開を停止したり、コンテナーを再起動したりします。</span><span class="sxs-lookup"><span data-stu-id="590eb-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="590eb-110">ロード バランサーはアプリの異常に対処するため、問題のあるインスタンスから正常なインスタンスにトラフィック経路を変更することがあります。</span><span class="sxs-lookup"><span data-stu-id="590eb-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="590eb-111">メモリ、ディスク、その他の物理サーバー リソースの使用を監視し、正常性の状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="590eb-112">正常性チェックでは、データベースや外部サービス エンドポイントなど、アプリの依存関係をテストし、それらが利用できることと正常に機能していることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="590eb-113">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="590eb-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="590eb-114">サンプル アプリには、このトピックで説明されているシナリオの例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="590eb-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="590eb-115">所与のシナリオに対してサンプル アプリを実行するには、コマンド シェルでプロジェクトのフォルダーから [dotnet run](/dotnet/core/tools/dotnet-run) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="590eb-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="590eb-116">サンプル アプリの詳しい使用方法については、サンプル アプリの *README.md* ファイルと本トピックのシナリオ説明をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="590eb-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="590eb-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="590eb-117">Prerequisites</span></span>

<span data-ttu-id="590eb-118">正常性チェックは通常、アプリの状態を確認する目的で、外部の監視サービスまたはコンテナー オーケストレーターと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="590eb-119">正常性チェックをアプリに追加する前に、使用する監視システムを決定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="590eb-120">監視システムからは、作成する正常性チェックの種類とそのエンドポイントの設定方法が指示されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="590eb-121">[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="590eb-121">Add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span> <span data-ttu-id="590eb-122">Entity Framework Core を使用して正常性チェックを実行するには、[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="590eb-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="590eb-123">サンプル アプリからは、いくつかのシナリオで正常性チェックを実演するスタートアップ コードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="590eb-124">[データベース プローブ](#database-probe) シナリオでは、[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) を使用して、データベース接続の正常性がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="590eb-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="590eb-125">[DbContext プローブ](#entity-framework-core-dbcontext-probe) シナリオでは、EF Core `DbContext` を使用して、データベースがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="590eb-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="590eb-126">データベース シナリオを探索するために、サンプル アプリでは次のことが行われます:</span><span class="sxs-lookup"><span data-stu-id="590eb-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="590eb-127">データベースを作成して、*appsettings.json* ファイルにその接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="590eb-128">そのプロジェクト ファイルに次のパッケージ参照が含まれています:</span><span class="sxs-lookup"><span data-stu-id="590eb-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="590eb-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="590eb-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="590eb-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="590eb-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="590eb-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="590eb-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="590eb-132">もう 1 つの正常性チェックのシナリオでは、1 つの管理ポートに正常性チェックを絞り込む方法が実演されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="590eb-133">このサンプル アプリでは、管理 URL と管理ポートを含む *Properties/launchSettings.json* ファイルを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590eb-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="590eb-134">詳細については、「[ポート別のフィルター処理](#filter-by-port)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="590eb-135">基本的な正常性プローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-135">Basic health probe</span></span>

<span data-ttu-id="590eb-136">多くのアプリでは、アプリが要求を処理できること (*活動性*) を報告する基本的な正常性チェック構成で十分にアプリの状態を検出できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="590eb-137">基本の構成では、正常性チェック サービスを登録し、正常性チェック ミドルウェアを呼び出します。このミドルウェアが特定の URL エンドポイントにおける正常性を返します。</span><span class="sxs-lookup"><span data-stu-id="590eb-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="590eb-138">既定では、特定の依存関係またはサブシステムをテストする正常性チェックは登録されていません。</span><span class="sxs-lookup"><span data-stu-id="590eb-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="590eb-139">正常性エンドポイント URL で応答できる場合、そのアプリは正常であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="590eb-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="590eb-140">既定の応答ライターによって、プレーンテキストの応答として状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) がクライアントに書き込まれます。このとき、状態として [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、または [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が示されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="590eb-141">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="590eb-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="590eb-142">`Startup.Configure` で `MapHealthChecks` を呼び出して、正常性チェック エンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="590eb-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="590eb-143">サンプル アプリでは、正常性チェック エンドポイントは `/health` で作成されます (*BasicStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="590eb-144">サンプル アプリを使用して基本的な構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="590eb-145">Docker の例</span><span class="sxs-lookup"><span data-stu-id="590eb-145">Docker example</span></span>

<span data-ttu-id="590eb-146">[Docker](xref:host-and-deploy/docker/index) からは、組み込み `HEALTHCHECK` ディレクティブが提供されます。これを使用し、基本的な正常性チェック構成を使用するアプリの状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="590eb-147">正常性チェックを作成する</span><span class="sxs-lookup"><span data-stu-id="590eb-147">Create health checks</span></span>

<span data-ttu-id="590eb-148">正常性チェックは <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="590eb-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> メソッドによって、`Healthy`、`Degraded`、`Unhealthy` のいずれかの正常性を示す <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="590eb-150">この結果が構成可能な状態コードと共にプレーンテキストの応答として書き込まれます (構成の説明は「[正常性チェック オプション](#health-check-options)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="590eb-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="590eb-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> からは、任意のキーと値のペアを返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="590eb-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="590eb-152">次の `ExampleHealthCheck` クラスからは、正常性チェックのレイアウトがどのようなものかがわかります。</span><span class="sxs-lookup"><span data-stu-id="590eb-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="590eb-153">正常性チェックのロジックが `CheckHealthAsync` メソッドに配置されています。</span><span class="sxs-lookup"><span data-stu-id="590eb-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="590eb-154">次の例では、ダミー変数 `healthCheckResultHealthy` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="590eb-155">`healthCheckResultHealthy` の値が `false` に設定されている場合、[HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 状態が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="590eb-156">正常性チェック サービスを登録する</span><span class="sxs-lookup"><span data-stu-id="590eb-156">Register health check services</span></span>

<span data-ttu-id="590eb-157">`Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> によって `ExampleHealthCheck` 型が正常性チェック サービスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="590eb-158">次の例にある <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> オーバーロードでは、正常性チェックでエラーが報告されると、レポートにエラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) が設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="590eb-159">エラー状態が `null` (既定) に設定された場合、[HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="590eb-160">このオーバーロードはライブラリ作成者にとって便利です。この設定に正常性チェック実装が従う場合、正常性チェック エラーが発生したとき、ライブラリによって指示されるエラー状態がアプリによって適用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="590eb-161">*タグ*を使用し、正常性チェックをフィルター処理できます (詳細は「[正常性チェックをフィルター処理する](#filter-health-checks)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="590eb-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="590eb-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> では、lambda 関数も実行できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="590eb-163">次の例では、正常性チェック名に「`Example`」が指定されいます。このチェックでは状態として常に正常が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="590eb-164">正常性チェックのルーティングを使用する</span><span class="sxs-lookup"><span data-stu-id="590eb-164">Use Health Checks Routing</span></span>

<span data-ttu-id="590eb-165">`Startup.Configure` で、エンドポイントの URL または相対パスを使用して、エンドポイント ビルダーで `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-165">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="590eb-166">ホストが必要</span><span class="sxs-lookup"><span data-stu-id="590eb-166">Require host</span></span>

<span data-ttu-id="590eb-167">`RequireHost` を呼び出して、正常性チェック エンドポイントに許可されている 1 つ以上のホストを指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-167">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="590eb-168">ホストは、punycode ではなく Unicode にする必要があります。また、ポートを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="590eb-168">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="590eb-169">コレクションが指定されていない場合は、任意のホストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-169">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="590eb-170">詳細については、「[ポート別のフィルター処理](#filter-by-port)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-170">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="590eb-171">承認が必要</span><span class="sxs-lookup"><span data-stu-id="590eb-171">Require authorization</span></span>

<span data-ttu-id="590eb-172">`RequireAuthorization` を呼び出して、正常性チェック要求エンドポイントで承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-172">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="590eb-173">`RequireAuthorization` のオーバーロードには 1 つ以上の承認ポリシーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-173">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="590eb-174">ポリシーが指定されていない場合は、既定の承認ポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-174">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="590eb-175">クロスオリジン要求 (CORS) の有効化</span><span class="sxs-lookup"><span data-stu-id="590eb-175">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="590eb-176">ブラウザーから手動で正常性チェックを実行することは一般的な使用シナリオではありませんが、正常性チェック エンドポイントで `RequireCors` を呼び出して CORS ミドルウェアを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="590eb-176">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="590eb-177">`RequireCors` のオーバーロードには、CORS ポリシー ビルダーのデリゲート (`CorsPolicyBuilder`) またはポリシー名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-177">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="590eb-178">ポリシーが指定されていない場合は、既定の CORS ポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-178">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="590eb-179">詳細については、<xref:security/cors> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-179">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="590eb-180">正常性チェック オプション</span><span class="sxs-lookup"><span data-stu-id="590eb-180">Health check options</span></span>

<span data-ttu-id="590eb-181"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> では、正常性チェックの動作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="590eb-181"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="590eb-182">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="590eb-182">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="590eb-183">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="590eb-183">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="590eb-184">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="590eb-184">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="590eb-185">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="590eb-185">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="590eb-186">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="590eb-186">Filter health checks</span></span>

<span data-ttu-id="590eb-187">既定では、正常性チェック ミドルウェアでは、登録されている正常性チェックがすべて実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-187">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="590eb-188">正常性チェックのサブセットを実行するには、<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> オプションにブール値を返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-188">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="590eb-189">次の例では、関数の条件文にあるそのタグ (`bar_tag`) により `Bar` 正常性チェックが除外されます。`true` は、正常性チェックの <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> プロパティが `foo_tag` か `baz_tag` に一致した場合にのみ返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-189">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="590eb-190">`Startup.ConfigureServices`の場合:</span><span class="sxs-lookup"><span data-stu-id="590eb-190">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="590eb-191">`Startup.Configure` では、`Predicate` によって 'Bar' 正常性チェックが除外されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-191">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="590eb-192">Foo と Baz のみが実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-192">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="590eb-193">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="590eb-193">Customize the HTTP status code</span></span>

<span data-ttu-id="590eb-194"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> を使用し、HTTP 状態コードに対する正常性状態のマッピングをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="590eb-194">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="590eb-195">次の <xref:Microsoft.AspNetCore.Http.StatusCodes> 代入はミドルウェアによって使用される既定値です。</span><span class="sxs-lookup"><span data-stu-id="590eb-195">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="590eb-196">要件に合わせて状態コード値を変更します。</span><span class="sxs-lookup"><span data-stu-id="590eb-196">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="590eb-197">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="590eb-197">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="590eb-198">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="590eb-198">Suppress cache headers</span></span>

<span data-ttu-id="590eb-199"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> によって、応答キャッシュを禁止する目的で、正常性チェック ミドルウェアによって HTTP ヘッダーがプローブ応答に追加されるかどうかが制御されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-199"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="590eb-200">値が `false` (既定) の場合、応答キャッシュを禁止する目的で、ミドルウェアによってヘッダー `Cache-Control`、`Expires`、`Pragma` が設定またはオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="590eb-200">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="590eb-201">値が `true` の場合、応答のキャッシュ ヘッダーがミドルウェアによって変更されることはありません。</span><span class="sxs-lookup"><span data-stu-id="590eb-201">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="590eb-202">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="590eb-202">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="590eb-203">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="590eb-203">Customize output</span></span>

<span data-ttu-id="590eb-204"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> オプションによって、応答の書き込みに使用される委任が取得または設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-204">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span>

<span data-ttu-id="590eb-205">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="590eb-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="590eb-206">既定の委任では、文字列値 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) を含む、最小のプレーンテキスト応答が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-206">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="590eb-207">次のカスタム デリゲート `WriteResponse` では、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-207">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="590eb-208">正常性チェック システムには、複雑な JSON の戻り値の形式に関する組み込みのサポートが提供されていません。これは、形式が監視システムの選択に固有であるためです。</span><span class="sxs-lookup"><span data-stu-id="590eb-208">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="590eb-209">必要に応じて、前の例の `JObject` を自由にカスタマイズしてください。</span><span class="sxs-lookup"><span data-stu-id="590eb-209">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="590eb-210">データベース プローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-210">Database probe</span></span>

<span data-ttu-id="590eb-211">正常性チェックでは、データベースが通常どおり応答しているかどうかを示すブール値テストとして実行されるよう、データベース クエリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-211">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="590eb-212">サンプル アプリでは、SQL Server データベース上で正常性のチェックを実行するために、ASP.NET Core アプリの正常性チェック ライブラリの [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 使用します。</span><span class="sxs-lookup"><span data-stu-id="590eb-212">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="590eb-213">`AspNetCore.Diagnostics.HealthChecks` によってデータベースに対して `SELECT 1` クエリが実行され、データベースへの接続が正常であることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-213">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="590eb-214">クエリでデータベース接続を確認するとき、すぐに返されるクエリを選択します。</span><span class="sxs-lookup"><span data-stu-id="590eb-214">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="590eb-215">このクエリ手法には、データベースをオーバーロードし、そのパフォーマンスを低下させるというリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="590eb-215">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="590eb-216">ほとんどの場合、テスト クエリは実行する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="590eb-216">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="590eb-217">データベースに正常に接続できれば十分です。</span><span class="sxs-lookup"><span data-stu-id="590eb-217">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="590eb-218">クエリを実行する必要があれば、`SELECT 1` など、単純な SELECT クエリを選択してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-218">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="590eb-219">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) へのパッケージ参照を含めます。</span><span class="sxs-lookup"><span data-stu-id="590eb-219">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="590eb-220">サンプル アプリの *appsettings.json* ファイルに有効なデータベース接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-220">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="590eb-221">このアプリでは `HealthCheckSample` という名前の SQL Server データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-221">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="590eb-222">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="590eb-222">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="590eb-223">サンプル アプリでは、データベースの接続文字列 (*DbHealthStartup.cs*) を利用して `AddSqlServer` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-223">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="590eb-224">正常性チェック エンドポイントを作成するには、`Startup.Configure` で `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-224">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="590eb-225">サンプル アプリを使用してデータベース プローブ シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-225">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="590eb-226">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="590eb-226">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="590eb-227">Entity Framework Core DbContext プローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-227">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="590eb-228">`DbContext` チェックでは、EF Core `DbContext` に対して構成されているデータベースとアプリとが通信できることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-228">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="590eb-229">`DbContext` チェックは、次のようなアプリでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="590eb-229">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="590eb-230">[Entity Framework (EF) Core を使用する](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="590eb-230">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="590eb-231">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) へのパッケージ参照を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="590eb-231">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="590eb-232">`AddDbContextCheck<TContext>` によって `DbContext` の正常性チェックが登録されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-232">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="590eb-233">`DbContext` は `TContext` としてメソッドに指定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-233">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="590eb-234">オーバーロードはエラー状態、タグ、カスタム テスト クエリの設定に利用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-234">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="590eb-235">既定では:</span><span class="sxs-lookup"><span data-stu-id="590eb-235">By default:</span></span>

* <span data-ttu-id="590eb-236">`DbContextHealthCheck` によって EF Core の `CanConnectAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-236">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="590eb-237">`AddDbContextCheck` メソッドのオーバーロードを使用して正常性を確認するときに実行される操作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="590eb-237">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="590eb-238">正常性チェックの名前は `TContext` 型の名前になります。</span><span class="sxs-lookup"><span data-stu-id="590eb-238">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="590eb-239">サンプル アプリでは、`AppDbContext` が `AddDbContextCheck` に提供され、`Startup.ConfigureServices` でサービスとして登録されます (*DbContextHealthStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-239">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="590eb-240">正常性チェック エンドポイントを作成するには、`Startup.Configure` で `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-240">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="590eb-241">サンプル アプリを利用して `DbContext` プローブ シナリオを実行するには、接続文字列によって指定されるデータベースが SQL Server インスタンスに存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="590eb-241">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="590eb-242">データベースが存在する場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="590eb-242">If the database exists, delete it.</span></span>

<span data-ttu-id="590eb-243">コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-243">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="590eb-244">アプリの実行後、ブラウザーで `/health` エンドポイントに要求することで正常性状態を確認します。</span><span class="sxs-lookup"><span data-stu-id="590eb-244">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="590eb-245">データベースと `AppDbContext` は存在しません。そこで、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-245">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="590eb-246">サンプル アプリにデータベースを作成させます。</span><span class="sxs-lookup"><span data-stu-id="590eb-246">Trigger the sample app to create the database.</span></span> <span data-ttu-id="590eb-247">`/createdatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="590eb-247">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="590eb-248">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590eb-248">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="590eb-249">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="590eb-249">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="590eb-250">データベースとコンテキストが存在します。そのため、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-250">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="590eb-251">サンプル アプリにデータベースを削除させます。</span><span class="sxs-lookup"><span data-stu-id="590eb-251">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="590eb-252">`/deletedatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="590eb-252">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="590eb-253">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590eb-253">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="590eb-254">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="590eb-254">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="590eb-255">アプリから異常という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-255">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="590eb-256">対応性と活動性に区分されるプローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-256">Separate readiness and liveness probes</span></span>

<span data-ttu-id="590eb-257">一部のホスティング シナリオでは、2 つのアプリ状態を区別する一組の正常性チェックが使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-257">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="590eb-258">アプリは機能しているが、要求を受信する準備がまだできていない。</span><span class="sxs-lookup"><span data-stu-id="590eb-258">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="590eb-259">この状態がアプリの*対応性*です。</span><span class="sxs-lookup"><span data-stu-id="590eb-259">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="590eb-260">アプリは機能しており、要求に応答する。</span><span class="sxs-lookup"><span data-stu-id="590eb-260">The app is functioning and responding to requests.</span></span> <span data-ttu-id="590eb-261">この状態がアプリの*活動性*です。</span><span class="sxs-lookup"><span data-stu-id="590eb-261">This state is the app's *liveness*.</span></span>

<span data-ttu-id="590eb-262">対応性チェックは通常、より広範囲で時間のかかる一連のチェックが実行され、アプリのすべてのサブシステムとリソースが利用できるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-262">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="590eb-263">活動性チェックでは、アプリが要求を処理できるかどうかを判断する簡易チェックのみが実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-263">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="590eb-264">アプリが対応性チェックに合格したら、活動性の確認のみを求める不経済な一連の活動性チェックをアプリに負わせる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="590eb-264">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="590eb-265">サンプル アプリには、[ホステッド サービス](xref:fundamentals/host/hosted-services)の長時間実行スタートアップ タスクの完了を報告する正常性チェックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="590eb-265">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="590eb-266">`StartupHostedServiceHealthCheck` によって `StartupTaskCompleted` というプロパティが公開されます。ホステッド サービスでは長時間実行タスクの完了時にこのプロパティを `true` に設定できます (*StartupHostedServiceHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-266">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="590eb-267">長時間実行バックグラウンド タスクは[ホステッド サービス](xref:fundamentals/host/hosted-services)によって開始されます (*Services/StartupHostedService*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-267">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="590eb-268">タスクが終わると、`StartupHostedServiceHealthCheck.StartupTaskCompleted` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-268">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="590eb-269">ホステッド サービスと共に `Startup.ConfigureServices` で正常性チェックが <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に登録されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-269">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="590eb-270">ホステッド サービスでは正常性チェックにプロパティを設定する必要があるため、正常性チェックはサービス コンテナーにも登録されます (*LivenessProbeStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-270">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="590eb-271">正常性チェック エンドポイントを作成するには、`Startup.Configure` で `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-271">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="590eb-272">サンプル アプリでは、正常性チェック エンドポイントは次の場所に作成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-272">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="590eb-273">対応性チェックの場合は `/health/ready`。</span><span class="sxs-lookup"><span data-stu-id="590eb-273">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="590eb-274">対応性チェックでは、`ready` タグが与えられているものに正常性チェックが絞り込まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-274">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="590eb-275">活動性チェックの場合は `/health/live`。</span><span class="sxs-lookup"><span data-stu-id="590eb-275">`/health/live` for the liveness check.</span></span> <span data-ttu-id="590eb-276">活動性チェックでは、[HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) で `false` を返すことで `StartupHostedServiceHealthCheck` が除外されます (詳細については、「[正常性チェックをフィルター処理する](#filter-health-checks)」を参照してください)</span><span class="sxs-lookup"><span data-stu-id="590eb-276">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="590eb-277">次のコード例の内容:</span><span class="sxs-lookup"><span data-stu-id="590eb-277">In the following example code:</span></span>

* <span data-ttu-id="590eb-278">対応性チェックでは、登録されているすべてのチェックが 'ready' タグと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-278">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="590eb-279">`Predicate` では、すべてのチェックが除外され、200-Ok が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-279">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="590eb-280">サンプル アプリを使用して対応性/活動性構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-280">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="590eb-281">ブラウザーで、15 秒が経過するまで `/health/ready` に数回アクセスします。</span><span class="sxs-lookup"><span data-stu-id="590eb-281">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="590eb-282">正常性チェックにより最初の 15 秒に対して "*異常*" が報告されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-282">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="590eb-283">15 秒後、エンドポイントから "*正常*" が報告されます。これは、ホステッド サービスによる長時間実行タスクが完了したことを反映しています。</span><span class="sxs-lookup"><span data-stu-id="590eb-283">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="590eb-284">この例では、2 秒間の遅延で最初の対応性チェックを実行する正常性チェック パブリッシャー (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装) も作成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-284">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="590eb-285">詳細については、「[正常性チェック パブリッシャー](#health-check-publisher)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="590eb-285">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="590eb-286">Kubernetes の例</span><span class="sxs-lookup"><span data-stu-id="590eb-286">Kubernetes example</span></span>

<span data-ttu-id="590eb-287">対応性チェックと活動性チェックを使い分けることは、[Kubernetes](https://kubernetes.io/) などの環境で便利です。</span><span class="sxs-lookup"><span data-stu-id="590eb-287">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="590eb-288">Kubernetes では、要求を受け付ける前に、基礎をなすデータベースの可用性テストなど、時間のかかるスタートアップ作業の実行がアプリに要求されることがあります。</span><span class="sxs-lookup"><span data-stu-id="590eb-288">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="590eb-289">別個のチェックを利用することで、アプリは機能しているがまだ準備ができていないか、あるいはアプリが起動に失敗したかをオーケストレーターは区別できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-289">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="590eb-290">Kubernetes の対応性プローブと活動性プローブに関する詳細については、Kubernetes ドキュメントの「[Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)」 (活動性プローブと対応性プローブを設定する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-290">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="590eb-291">次の例では、Kubernetes 対応性プローブの構成を確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-291">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="590eb-292">カスタム応答ライターを含むメトリック ベースのプローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-292">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="590eb-293">このサンプル アプリでは、カスタム応答ライターを含む、メモリ正常性チェックを確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-293">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="590eb-294">与えられたしきい値を超えるメモリがアプリで使用された場合、`MemoryHealthCheck` からは劣化状態が報告されます (このサンプル アプリでは 1 GB)。</span><span class="sxs-lookup"><span data-stu-id="590eb-294">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="590eb-295"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> にはアプリのガベージ コレクター (GC) 情報が含まれています (*MemoryHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-295">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="590eb-296">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="590eb-296">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="590eb-297"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に渡して正常性チェックを有効にする代わりに、`MemoryHealthCheck` がサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-297">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="590eb-298"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> が登録されたサービスはすべて、正常性チェック サービスとミドルウェアで利用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-298">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="590eb-299">正常性チェック サービスはシングルトン サービスとして登録することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="590eb-299">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="590eb-300">サンプル アプリ (*CustomWriterStartup.cs*) の内容:</span><span class="sxs-lookup"><span data-stu-id="590eb-300">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="590eb-301">正常性チェック エンドポイントを作成するには、`Startup.Configure` で `MapHealthChecks` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-301">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="590eb-302">`WriteResponse` 委任が `ResponseWriter` プロパティに与えられることで、正常性チェックの実行時に、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-302">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="590eb-303">`WriteResponse` メソッドによって `CompositeHealthCheckResult` が書式設定されて JSON オブジェクトが生成され、正常性チェック応答のために JSON 出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-303">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="590eb-304">サンプル アプリを使用してカスタム応答ライターを含むメトリックベースのプローブを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-304">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="590eb-305">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、ディスク ストレージや最大値活動性のチェックなど、メトリックベースの正常性チェック シナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-305">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="590eb-306">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="590eb-306">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="590eb-307">ポート別のフィルター処理</span><span class="sxs-lookup"><span data-stu-id="590eb-307">Filter by port</span></span>

<span data-ttu-id="590eb-308">`MapHealthChecks` でポートを指定する URL パターンを使用して `RequireHost` を呼び出し、指定されたポートに正常性チェック要求を制限します。</span><span class="sxs-lookup"><span data-stu-id="590eb-308">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="590eb-309">これは通常、サービスを監視するためのポートを公開する目的で、コンテナー環境で使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-309">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="590eb-310">サンプル アプリによって、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を使用してポートが設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-310">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="590eb-311">ポートは *launchSettings.json* ファイルに設定され、環境変数を介して構成プロバイダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-311">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="590eb-312">管理ポートで要求を待つようにサーバーを設定する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="590eb-312">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="590eb-313">サンプル アプリを使用し、管理ポート設定を実演するには、*Properties* フォルダーで *launchSettings.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="590eb-313">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="590eb-314">サンプル アプリの次の *Properties/launchSettings.json* ファイルは、サンプル アプリのプロジェクト ファイルに含まれていないため、手動で作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590eb-314">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="590eb-315">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="590eb-315">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="590eb-316">`Startup.Configure` で `MapHealthChecks` を呼び出して、正常性チェック エンドポイントを作成します。</span><span class="sxs-lookup"><span data-stu-id="590eb-316">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="590eb-317">サンプル アプリでは、`Startup.Configure` のエンドポイントで `RequireHost` を呼び出すと、構成の管理ポートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-317">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="590eb-318">エンドポイントは、`Startup.Configure` のサンプル アプリで作成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-318">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="590eb-319">次のコード例の内容:</span><span class="sxs-lookup"><span data-stu-id="590eb-319">In the following example code:</span></span>

* <span data-ttu-id="590eb-320">対応性チェックでは、登録されているすべてのチェックが 'ready' タグと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-320">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="590eb-321">`Predicate` では、すべてのチェックが除外され、200-Ok が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-321">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="590eb-322">管理ポートをコードに明示的に設定することで、サンプル アプリで *launchSettings.json* ファイルを作成することを回避できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-322">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="590eb-323"><xref:Microsoft.Extensions.Hosting.HostBuilder> が作成される *Program.cs* で、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> の呼び出しを追加し、アプリの管理ポート エンドポイントを用意します。</span><span class="sxs-lookup"><span data-stu-id="590eb-323">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="590eb-324">*ManagementPortStartup.cs* の `Configure` で、`RequireHost` を使用して管理ポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-324">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="590eb-325">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="590eb-325">*Program.cs*:</span></span>
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
> <span data-ttu-id="590eb-326">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="590eb-326">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="590eb-327">サンプル アプリを使用して管理ポート構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-327">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="590eb-328">正常性チェック ライブラリを配布する</span><span class="sxs-lookup"><span data-stu-id="590eb-328">Distribute a health check library</span></span>

<span data-ttu-id="590eb-329">正常性チェックをライブラリとして配布するには:</span><span class="sxs-lookup"><span data-stu-id="590eb-329">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="590eb-330">スタンドアロン クラスとして <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装する正常性チェックを記述します。</span><span class="sxs-lookup"><span data-stu-id="590eb-330">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="590eb-331">このクラスは、設定データにアクセスする目的で[依存関係挿入 (DI)](xref:fundamentals/dependency-injection)、型のアクティブ化、[名前付きオプション](xref:fundamentals/configuration/options)に依存できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-331">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="590eb-332">`CheckHealthAsync` の正常性チェックのロジックでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590eb-332">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="590eb-333">`data1` と `data2` は、プローブの正常性チェック ロジックを実行するためにメソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-333">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="590eb-334">`AccessViolationException` が処理されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-334">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="590eb-335"><xref:System.AccessViolationException> が発生すると、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> とともに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> が返され、ユーザーは正常性チェックの失敗状態を構成できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-335">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="590eb-336">拡張メソッドを記述します。拡張メソッドを使用するアプリがその `Startup.Configure` メソッドで呼び出すパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-336">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="590eb-337">次の例では、次の正常性チェック メソッド シグネチャを想定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-337">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="590eb-338">先のシグネチャは、正常性チェック プローブ ロジックを処理する目的で `ExampleHealthCheck` に追加データが要求されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="590eb-338">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="590eb-339">正常性チェックが拡張メソッドに登録されたときに正常性チェック インスタンスを作成するための委任にデータが提供されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-339">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="590eb-340">次の例では、呼び出し元によって次が任意で指定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-340">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="590eb-341">正常性チェック名 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="590eb-341">health check name (`name`).</span></span> <span data-ttu-id="590eb-342">`null` の場合、`example_health_check` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-342">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="590eb-343">正常性チェックの文字列データ ポイント (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="590eb-343">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="590eb-344">正常性チェックの整数データ ポイント (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="590eb-344">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="590eb-345">`null` の場合、`1` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-345">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="590eb-346">エラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="590eb-346">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="590eb-347">既定値は、`null` です。</span><span class="sxs-lookup"><span data-stu-id="590eb-347">The default is `null`.</span></span> <span data-ttu-id="590eb-348">`null` の場合、エラー状態に対して [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-348">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="590eb-349">タグ (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="590eb-349">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="590eb-350">正常性チェック パブリッシャー</span><span class="sxs-lookup"><span data-stu-id="590eb-350">Health Check Publisher</span></span>

<span data-ttu-id="590eb-351">サービス コンテナーに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> が追加されると、正常性チェック システムにより定期的に正常性チェックが実行され、結果と共に `PublishAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-351">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="590eb-352">これは、正常性を判断する目的で監視システムを定期的に呼び出すことを各プロセスに求めるプッシュベースの監視システム シナリオで便利です。</span><span class="sxs-lookup"><span data-stu-id="590eb-352">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="590eb-353"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インターフェイスには単一メソッドが与えられます。</span><span class="sxs-lookup"><span data-stu-id="590eb-353">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="590eb-354"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> を使うと次を設定できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-354"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="590eb-355"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; アプリが起動した後、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスを実行する前に適用される初期遅延です。</span><span class="sxs-lookup"><span data-stu-id="590eb-355"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="590eb-356">遅延はスタートアップ時に一度だけ適用され、以降の繰り返しには適用されません。</span><span class="sxs-lookup"><span data-stu-id="590eb-356">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="590eb-357">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="590eb-357">The default value is five seconds.</span></span>
* <span data-ttu-id="590eb-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> を実行する期間です。</span><span class="sxs-lookup"><span data-stu-id="590eb-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="590eb-359">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="590eb-359">The default value is 30 seconds.</span></span>
* <span data-ttu-id="590eb-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> が `null` (既定値) の場合、正常性チェック パブリッシャー サービスにより登録済みのすべての正常性チェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="590eb-361">正常性チェックのサブセットを実行するには、チェックのセットをフィルター処理する関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-361">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="590eb-362">述語は期間ごとに評価されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-362">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="590eb-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; すべての <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスに対する正常性チェックの実行のタイムアウトです。</span><span class="sxs-lookup"><span data-stu-id="590eb-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="590eb-364">タイムアウトなしで実行するには <xref:System.Threading.Timeout.InfiniteTimeSpan> を使います。</span><span class="sxs-lookup"><span data-stu-id="590eb-364">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="590eb-365">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="590eb-365">The default value is 30 seconds.</span></span>

<span data-ttu-id="590eb-366">サンプル アプリでは、`ReadinessPublisher` が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装です。</span><span class="sxs-lookup"><span data-stu-id="590eb-366">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="590eb-367">正常性チェックの状態は、チェックごとに以下のログ レベルでログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-367">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="590eb-368">情報 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>): 正常性チェックの状態が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> の場合。</span><span class="sxs-lookup"><span data-stu-id="590eb-368">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="590eb-369">エラー (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) 状態が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> または <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> の場合。</span><span class="sxs-lookup"><span data-stu-id="590eb-369">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="590eb-370">サンプル アプリの `LivenessProbeStartup` の例では、対応性チェック `StartupHostedService` にはスタートアップの遅延が 2 秒間あり、30 秒ごとにチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-370">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="590eb-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装をアクティブ化するために、サンプルでは `ReadinessPublisher` をシングルトン サービスとして[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーに登録しています。</span><span class="sxs-lookup"><span data-stu-id="590eb-371">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="590eb-372">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、[Application Insights](/azure/application-insights/app-insights-overview) など、いくつかのシステムのパブリッシャーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-372">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="590eb-373">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="590eb-373">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="590eb-374">MapWhen で正常性チェックを制限する</span><span class="sxs-lookup"><span data-stu-id="590eb-374">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="590eb-375"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> を使用して、正常性チェック エンドポイントの要求パイプラインを条件付きで分岐します。</span><span class="sxs-lookup"><span data-stu-id="590eb-375">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="590eb-376">次の例では、`api/HealthCheck` エンドポイントに対して GET 要求が受信された場合に、`MapWhen` が要求パイプラインを分岐して正常性チェック ミドルウェアをアクティブ化します。</span><span class="sxs-lookup"><span data-stu-id="590eb-376">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="590eb-377">詳細については、<xref:fundamentals/middleware/index#use-run-and-map> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-377">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="590eb-378">ASP.NET Core からは、アプリ インフラストラクチャ コンポーネントの正常性を報告するための正常性チェック ミドルウェアとライブラリが提供されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-378">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="590eb-379">正常性チェックは HTTP エンドポイントとしてアプリによって公開されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-379">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="590eb-380">正常性チェック エンドポイントは、さまざまなリアルタイム監視シナリオに合わせて設定できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-380">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="590eb-381">正常性プローブは、アプリの状態を確認する目的でコンテナー オーケストレーターとロード バランサーによって使用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-381">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="590eb-382">たとえば、正常性チェックで問題が確認された場合、コンテナー オーケストレーターは実行中の展開を停止したり、コンテナーを再起動したりします。</span><span class="sxs-lookup"><span data-stu-id="590eb-382">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="590eb-383">ロード バランサーはアプリの異常に対処するため、問題のあるインスタンスから正常なインスタンスにトラフィック経路を変更することがあります。</span><span class="sxs-lookup"><span data-stu-id="590eb-383">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="590eb-384">メモリ、ディスク、その他の物理サーバー リソースの使用を監視し、正常性の状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-384">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="590eb-385">正常性チェックでは、データベースや外部サービス エンドポイントなど、アプリの依存関係をテストし、それらが利用できることと正常に機能していることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-385">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="590eb-386">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="590eb-386">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="590eb-387">サンプル アプリには、このトピックで説明されているシナリオの例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="590eb-387">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="590eb-388">所与のシナリオに対してサンプル アプリを実行するには、コマンド シェルでプロジェクトのフォルダーから [dotnet run](/dotnet/core/tools/dotnet-run) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="590eb-388">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="590eb-389">サンプル アプリの詳しい使用方法については、サンプル アプリの *README.md* ファイルと本トピックのシナリオ説明をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="590eb-389">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="590eb-390">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="590eb-390">Prerequisites</span></span>

<span data-ttu-id="590eb-391">正常性チェックは通常、アプリの状態を確認する目的で、外部の監視サービスまたはコンテナー オーケストレーターと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-391">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="590eb-392">正常性チェックをアプリに追加する前に、使用する監視システムを決定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-392">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="590eb-393">監視システムからは、作成する正常性チェックの種類とそのエンドポイントの設定方法が指示されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-393">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="590eb-394">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="590eb-394">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="590eb-395">サンプル アプリからは、いくつかのシナリオで正常性チェックを実演するスタートアップ コードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-395">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="590eb-396">[データベース プローブ](#database-probe) シナリオでは、[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) を使用して、データベース接続の正常性がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="590eb-396">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="590eb-397">[DbContext プローブ](#entity-framework-core-dbcontext-probe) シナリオでは、EF Core `DbContext` を使用して、データベースがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="590eb-397">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="590eb-398">データベース シナリオを探索するために、サンプル アプリでは次のことが行われます:</span><span class="sxs-lookup"><span data-stu-id="590eb-398">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="590eb-399">データベースを作成して、*appsettings.json* ファイルにその接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-399">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="590eb-400">そのプロジェクト ファイルに次のパッケージ参照が含まれています:</span><span class="sxs-lookup"><span data-stu-id="590eb-400">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="590eb-401">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="590eb-401">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="590eb-402">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="590eb-402">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="590eb-403">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="590eb-403">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="590eb-404">もう 1 つの正常性チェックのシナリオでは、1 つの管理ポートに正常性チェックを絞り込む方法が実演されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-404">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="590eb-405">このサンプル アプリでは、管理 URL と管理ポートを含む *Properties/launchSettings.json* ファイルを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590eb-405">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="590eb-406">詳細については、「[ポート別のフィルター処理](#filter-by-port)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-406">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="590eb-407">基本的な正常性プローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-407">Basic health probe</span></span>

<span data-ttu-id="590eb-408">多くのアプリでは、アプリが要求を処理できること (*活動性*) を報告する基本的な正常性チェック構成で十分にアプリの状態を検出できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-408">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="590eb-409">基本の構成では、正常性チェック サービスを登録し、正常性チェック ミドルウェアを呼び出します。このミドルウェアが特定の URL エンドポイントにおける正常性を返します。</span><span class="sxs-lookup"><span data-stu-id="590eb-409">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="590eb-410">既定では、特定の依存関係またはサブシステムをテストする正常性チェックは登録されていません。</span><span class="sxs-lookup"><span data-stu-id="590eb-410">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="590eb-411">正常性エンドポイント URL で応答できる場合、そのアプリは正常であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="590eb-411">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="590eb-412">既定の応答ライターによって、プレーンテキストの応答として状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) がクライアントに書き込まれます。このとき、状態として [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、または [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が示されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-412">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="590eb-413">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="590eb-413">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="590eb-414">`Startup.Configure` の要求処理パイプラインで、<xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を使用して正常性チェック ミドルウェアのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="590eb-414">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="590eb-415">サンプル アプリでは、正常性チェック エンドポイントは `/health` で作成されます (*BasicStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-415">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="590eb-416">サンプル アプリを使用して基本的な構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-416">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="590eb-417">Docker の例</span><span class="sxs-lookup"><span data-stu-id="590eb-417">Docker example</span></span>

<span data-ttu-id="590eb-418">[Docker](xref:host-and-deploy/docker/index) からは、組み込み `HEALTHCHECK` ディレクティブが提供されます。これを使用し、基本的な正常性チェック構成を使用するアプリの状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-418">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="590eb-419">正常性チェックを作成する</span><span class="sxs-lookup"><span data-stu-id="590eb-419">Create health checks</span></span>

<span data-ttu-id="590eb-420">正常性チェックは <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-420">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="590eb-421"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> メソッドによって、`Healthy`、`Degraded`、`Unhealthy` のいずれかの正常性を示す <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-421">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="590eb-422">この結果が構成可能な状態コードと共にプレーンテキストの応答として書き込まれます (構成の説明は「[正常性チェック オプション](#health-check-options)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="590eb-422">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="590eb-423"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> からは、任意のキーと値のペアを返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="590eb-423"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="590eb-424">正常性チェックの例</span><span class="sxs-lookup"><span data-stu-id="590eb-424">Example health check</span></span>

<span data-ttu-id="590eb-425">次の `ExampleHealthCheck` クラスからは、正常性チェックのレイアウトがどのようなものかがわかります。</span><span class="sxs-lookup"><span data-stu-id="590eb-425">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="590eb-426">正常性チェックのロジックが `CheckHealthAsync` メソッドに配置されています。</span><span class="sxs-lookup"><span data-stu-id="590eb-426">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="590eb-427">次の例では、ダミー変数 `healthCheckResultHealthy` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-427">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="590eb-428">`healthCheckResultHealthy` の値が `false` に設定されている場合、[HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 状態が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-428">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="590eb-429">正常性チェック サービスを登録する</span><span class="sxs-lookup"><span data-stu-id="590eb-429">Register health check services</span></span>

<span data-ttu-id="590eb-430">`ExampleHealthCheck` 型は、`Startup.ConfigureServices` で <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> を使用して正常性チェック サービスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-430">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="590eb-431">次の例にある <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> オーバーロードでは、正常性チェックでエラーが報告されると、レポートにエラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) が設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-431">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="590eb-432">エラー状態が `null` (既定) に設定された場合、[HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-432">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="590eb-433">このオーバーロードはライブラリ作成者にとって便利です。この設定に正常性チェック実装が従う場合、正常性チェック エラーが発生したとき、ライブラリによって指示されるエラー状態がアプリによって適用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-433">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="590eb-434">*タグ*を使用し、正常性チェックをフィルター処理できます (詳細は「[正常性チェックをフィルター処理する](#filter-health-checks)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="590eb-434">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="590eb-435"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> では、lambda 関数も実行できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-435"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="590eb-436">次の `Startup.ConfigureServices` の例では、正常性チェック名に `Example` が指定されています。このチェックでは状態として常に正常が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-436">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="590eb-437">正常性チェック ミドルウェアを使用する</span><span class="sxs-lookup"><span data-stu-id="590eb-437">Use Health Checks Middleware</span></span>

<span data-ttu-id="590eb-438">`Startup.Configure` で、エンドポイント URL または相対パスを利用し、処理パイプラインで <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-438">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="590eb-439">正常性チェックに特定のポートで待機させる場合、<xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> のオーバーロードを使用してポートを設定します ([詳細は「ポート別のフィルター処理」セクションにあります](#filter-by-port))。</span><span class="sxs-lookup"><span data-stu-id="590eb-439">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="590eb-440">正常性チェック オプション</span><span class="sxs-lookup"><span data-stu-id="590eb-440">Health check options</span></span>

<span data-ttu-id="590eb-441"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> では、正常性チェックの動作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="590eb-441"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="590eb-442">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="590eb-442">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="590eb-443">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="590eb-443">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="590eb-444">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="590eb-444">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="590eb-445">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="590eb-445">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="590eb-446">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="590eb-446">Filter health checks</span></span>

<span data-ttu-id="590eb-447">既定では、正常性チェック ミドルウェアでは、登録されている正常性チェックがすべて実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-447">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="590eb-448">正常性チェックのサブセットを実行するには、<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> オプションにブール値を返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-448">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="590eb-449">次の例では、関数の条件文にあるそのタグ (`bar_tag`) により `Bar` 正常性チェックが除外されます。`true` は、正常性チェックの <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> プロパティが `foo_tag` か `baz_tag` に一致した場合にのみ返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-449">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="590eb-450">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="590eb-450">Customize the HTTP status code</span></span>

<span data-ttu-id="590eb-451"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> を使用し、HTTP 状態コードに対する正常性状態のマッピングをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="590eb-451">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="590eb-452">次の <xref:Microsoft.AspNetCore.Http.StatusCodes> 代入はミドルウェアによって使用される既定値です。</span><span class="sxs-lookup"><span data-stu-id="590eb-452">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="590eb-453">要件に合わせて状態コード値を変更します。</span><span class="sxs-lookup"><span data-stu-id="590eb-453">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="590eb-454">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="590eb-454">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="590eb-455">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="590eb-455">Suppress cache headers</span></span>

<span data-ttu-id="590eb-456"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> によって、応答キャッシュを禁止する目的で、正常性チェック ミドルウェアによって HTTP ヘッダーがプローブ応答に追加されるかどうかが制御されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-456"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="590eb-457">値が `false` (既定) の場合、応答キャッシュを禁止する目的で、ミドルウェアによってヘッダー `Cache-Control`、`Expires`、`Pragma` が設定またはオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="590eb-457">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="590eb-458">値が `true` の場合、応答のキャッシュ ヘッダーがミドルウェアによって変更されることはありません。</span><span class="sxs-lookup"><span data-stu-id="590eb-458">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="590eb-459">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="590eb-459">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="590eb-460">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="590eb-460">Customize output</span></span>

<span data-ttu-id="590eb-461"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> オプションによって、応答の書き込みに使用される委任が取得または設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-461">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="590eb-462">既定の委任では、文字列値 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) を含む、最小のプレーンテキスト応答が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-462">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="590eb-463">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="590eb-463">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="590eb-464">既定の委任では、文字列値 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) を含む、最小のプレーンテキスト応答が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-464">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="590eb-465">次のカスタム デリゲート `WriteResponse` では、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-465">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="590eb-466">正常性チェック システムには、複雑な JSON の戻り値の形式に関する組み込みのサポートが提供されていません。これは、形式が監視システムの選択に固有であるためです。</span><span class="sxs-lookup"><span data-stu-id="590eb-466">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="590eb-467">必要に応じて、前の例の `JObject` を自由にカスタマイズしてください。</span><span class="sxs-lookup"><span data-stu-id="590eb-467">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="590eb-468">データベース プローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-468">Database probe</span></span>

<span data-ttu-id="590eb-469">正常性チェックでは、データベースが通常どおり応答しているかどうかを示すブール値テストとして実行されるよう、データベース クエリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-469">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="590eb-470">サンプル アプリでは、SQL Server データベース上で正常性のチェックを実行するために、ASP.NET Core アプリの正常性チェック ライブラリの [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 使用します。</span><span class="sxs-lookup"><span data-stu-id="590eb-470">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="590eb-471">`AspNetCore.Diagnostics.HealthChecks` によってデータベースに対して `SELECT 1` クエリが実行され、データベースへの接続が正常であることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-471">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="590eb-472">クエリでデータベース接続を確認するとき、すぐに返されるクエリを選択します。</span><span class="sxs-lookup"><span data-stu-id="590eb-472">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="590eb-473">このクエリ手法には、データベースをオーバーロードし、そのパフォーマンスを低下させるというリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="590eb-473">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="590eb-474">ほとんどの場合、テスト クエリは実行する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="590eb-474">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="590eb-475">データベースに正常に接続できれば十分です。</span><span class="sxs-lookup"><span data-stu-id="590eb-475">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="590eb-476">クエリを実行する必要があれば、`SELECT 1` など、単純な SELECT クエリを選択してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-476">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="590eb-477">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) へのパッケージ参照を含めます。</span><span class="sxs-lookup"><span data-stu-id="590eb-477">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="590eb-478">サンプル アプリの *appsettings.json* ファイルに有効なデータベース接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-478">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="590eb-479">このアプリでは `HealthCheckSample` という名前の SQL Server データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-479">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="590eb-480">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="590eb-480">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="590eb-481">サンプル アプリでは、データベースの接続文字列 (*DbHealthStartup.cs*) を利用して `AddSqlServer` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-481">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="590eb-482">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-482">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="590eb-483">サンプル アプリを使用してデータベース プローブ シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-483">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="590eb-484">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="590eb-484">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="590eb-485">Entity Framework Core DbContext プローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-485">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="590eb-486">`DbContext` チェックでは、EF Core `DbContext` に対して構成されているデータベースとアプリとが通信できることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-486">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="590eb-487">`DbContext` チェックは、次のようなアプリでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="590eb-487">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="590eb-488">[Entity Framework (EF) Core を使用する](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="590eb-488">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="590eb-489">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) へのパッケージ参照を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="590eb-489">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="590eb-490">`AddDbContextCheck<TContext>` によって `DbContext` の正常性チェックが登録されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-490">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="590eb-491">`DbContext` は `TContext` としてメソッドに指定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-491">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="590eb-492">オーバーロードはエラー状態、タグ、カスタム テスト クエリの設定に利用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-492">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="590eb-493">既定では:</span><span class="sxs-lookup"><span data-stu-id="590eb-493">By default:</span></span>

* <span data-ttu-id="590eb-494">`DbContextHealthCheck` によって EF Core の `CanConnectAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-494">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="590eb-495">`AddDbContextCheck` メソッドのオーバーロードを使用して正常性を確認するときに実行される操作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="590eb-495">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="590eb-496">正常性チェックの名前は `TContext` 型の名前になります。</span><span class="sxs-lookup"><span data-stu-id="590eb-496">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="590eb-497">サンプル アプリでは、`AppDbContext` が `AddDbContextCheck` に提供され、`Startup.ConfigureServices` でサービスとして登録されます (*DbContextHealthStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-497">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="590eb-498">サンプル アプリで、`UseHealthChecks` によって `Startup.Configure` に正常性チェック ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-498">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="590eb-499">サンプル アプリを利用して `DbContext` プローブ シナリオを実行するには、接続文字列によって指定されるデータベースが SQL Server インスタンスに存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="590eb-499">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="590eb-500">データベースが存在する場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="590eb-500">If the database exists, delete it.</span></span>

<span data-ttu-id="590eb-501">コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-501">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="590eb-502">アプリの実行後、ブラウザーで `/health` エンドポイントに要求することで正常性状態を確認します。</span><span class="sxs-lookup"><span data-stu-id="590eb-502">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="590eb-503">データベースと `AppDbContext` は存在しません。そこで、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-503">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="590eb-504">サンプル アプリにデータベースを作成させます。</span><span class="sxs-lookup"><span data-stu-id="590eb-504">Trigger the sample app to create the database.</span></span> <span data-ttu-id="590eb-505">`/createdatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="590eb-505">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="590eb-506">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590eb-506">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="590eb-507">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="590eb-507">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="590eb-508">データベースとコンテキストが存在します。そのため、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-508">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="590eb-509">サンプル アプリにデータベースを削除させます。</span><span class="sxs-lookup"><span data-stu-id="590eb-509">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="590eb-510">`/deletedatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="590eb-510">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="590eb-511">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590eb-511">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="590eb-512">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="590eb-512">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="590eb-513">アプリから異常という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-513">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="590eb-514">対応性と活動性に区分されるプローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-514">Separate readiness and liveness probes</span></span>

<span data-ttu-id="590eb-515">一部のホスティング シナリオでは、2 つのアプリ状態を区別する一組の正常性チェックが使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-515">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="590eb-516">アプリは機能しているが、要求を受信する準備がまだできていない。</span><span class="sxs-lookup"><span data-stu-id="590eb-516">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="590eb-517">この状態がアプリの*対応性*です。</span><span class="sxs-lookup"><span data-stu-id="590eb-517">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="590eb-518">アプリは機能しており、要求に応答する。</span><span class="sxs-lookup"><span data-stu-id="590eb-518">The app is functioning and responding to requests.</span></span> <span data-ttu-id="590eb-519">この状態がアプリの*活動性*です。</span><span class="sxs-lookup"><span data-stu-id="590eb-519">This state is the app's *liveness*.</span></span>

<span data-ttu-id="590eb-520">対応性チェックは通常、より広範囲で時間のかかる一連のチェックが実行され、アプリのすべてのサブシステムとリソースが利用できるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-520">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="590eb-521">活動性チェックでは、アプリが要求を処理できるかどうかを判断する簡易チェックのみが実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-521">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="590eb-522">アプリが対応性チェックに合格したら、活動性の確認のみを求める不経済な一連の活動性チェックをアプリに負わせる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="590eb-522">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="590eb-523">サンプル アプリには、[ホステッド サービス](xref:fundamentals/host/hosted-services)の長時間実行スタートアップ タスクの完了を報告する正常性チェックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="590eb-523">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="590eb-524">`StartupHostedServiceHealthCheck` によって `StartupTaskCompleted` というプロパティが公開されます。ホステッド サービスでは長時間実行タスクの完了時にこのプロパティを `true` に設定できます (*StartupHostedServiceHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-524">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="590eb-525">長時間実行バックグラウンド タスクは[ホステッド サービス](xref:fundamentals/host/hosted-services)によって開始されます (*Services/StartupHostedService*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-525">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="590eb-526">タスクが終わると、`StartupHostedServiceHealthCheck.StartupTaskCompleted` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-526">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="590eb-527">ホステッド サービスと共に `Startup.ConfigureServices` で正常性チェックが <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に登録されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-527">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="590eb-528">ホステッド サービスでは正常性チェックにプロパティを設定する必要があるため、正常性チェックはサービス コンテナーにも登録されます (*LivenessProbeStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-528">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="590eb-529">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-529">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="590eb-530">サンプル アプリで、対応性チェックのための正常性チェック エンドポイントが `/health/ready` で作成され、活動性チェックのための正常性チェック エンドポイントが `/health/live` で作成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-530">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="590eb-531">対応性チェックでは、`ready` タグが与えられているものに正常性チェックが絞り込まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-531">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="590eb-532">活動性チェックでは、[HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) で `false` を返すことで `StartupHostedServiceHealthCheck` が除外されます (詳細については、「[正常性チェックをフィルター処理する](#filter-health-checks)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="590eb-532">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="590eb-533">サンプル アプリを使用して対応性/活動性構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-533">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="590eb-534">ブラウザーで、15 秒が経過するまで `/health/ready` に数回アクセスします。</span><span class="sxs-lookup"><span data-stu-id="590eb-534">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="590eb-535">正常性チェックにより最初の 15 秒に対して "*異常*" が報告されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-535">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="590eb-536">15 秒後、エンドポイントから "*正常*" が報告されます。これは、ホステッド サービスによる長時間実行タスクが完了したことを反映しています。</span><span class="sxs-lookup"><span data-stu-id="590eb-536">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="590eb-537">この例では、2 秒間の遅延で最初の対応性チェックを実行する正常性チェック パブリッシャー (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装) も作成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-537">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="590eb-538">詳細については、「[正常性チェック パブリッシャー](#health-check-publisher)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="590eb-538">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="590eb-539">Kubernetes の例</span><span class="sxs-lookup"><span data-stu-id="590eb-539">Kubernetes example</span></span>

<span data-ttu-id="590eb-540">対応性チェックと活動性チェックを使い分けることは、[Kubernetes](https://kubernetes.io/) などの環境で便利です。</span><span class="sxs-lookup"><span data-stu-id="590eb-540">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="590eb-541">Kubernetes では、要求を受け付ける前に、基礎をなすデータベースの可用性テストなど、時間のかかるスタートアップ作業の実行がアプリに要求されることがあります。</span><span class="sxs-lookup"><span data-stu-id="590eb-541">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="590eb-542">別個のチェックを利用することで、アプリは機能しているがまだ準備ができていないか、あるいはアプリが起動に失敗したかをオーケストレーターは区別できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-542">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="590eb-543">Kubernetes の対応性プローブと活動性プローブに関する詳細については、Kubernetes ドキュメントの「[Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)」 (活動性プローブと対応性プローブを設定する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-543">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="590eb-544">次の例では、Kubernetes 対応性プローブの構成を確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-544">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="590eb-545">カスタム応答ライターを含むメトリック ベースのプローブ</span><span class="sxs-lookup"><span data-stu-id="590eb-545">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="590eb-546">このサンプル アプリでは、カスタム応答ライターを含む、メモリ正常性チェックを確認できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-546">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="590eb-547">与えられたしきい値を超えるメモリがアプリで使用された場合 (このサンプル アプリでは 1 GB)、`MemoryHealthCheck` から異常状態が報告されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-547">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="590eb-548"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> にはアプリのガベージ コレクター (GC) 情報が含まれています (*MemoryHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-548">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="590eb-549">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="590eb-549">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="590eb-550"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に渡して正常性チェックを有効にする代わりに、`MemoryHealthCheck` がサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-550">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="590eb-551"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> が登録されたサービスはすべて、正常性チェック サービスとミドルウェアで利用できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-551">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="590eb-552">正常性チェック サービスはシングルトン サービスとして登録することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="590eb-552">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="590eb-553">サンプル アプリ (*CustomWriterStartup.cs*) の内容:</span><span class="sxs-lookup"><span data-stu-id="590eb-553">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="590eb-554">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="590eb-554">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="590eb-555">`WriteResponse` 委任が `ResponseWriter` プロパティに与えられることで、正常性チェックの実行時に、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-555">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="590eb-556">`WriteResponse` メソッドによって `CompositeHealthCheckResult` が書式設定されて JSON オブジェクトが生成され、正常性チェック応答のために JSON 出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-556">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="590eb-557">サンプル アプリを使用してカスタム応答ライターを含むメトリックベースのプローブを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-557">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="590eb-558">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、ディスク ストレージや最大値活動性のチェックなど、メトリックベースの正常性チェック シナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-558">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="590eb-559">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="590eb-559">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="590eb-560">ポート別のフィルター処理</span><span class="sxs-lookup"><span data-stu-id="590eb-560">Filter by port</span></span>

<span data-ttu-id="590eb-561">ポートを指定して <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を呼び出すと、正常性チェック要求は指定されたポートに制限されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-561">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="590eb-562">これは通常、サービスを監視するためのポートを公開する目的で、コンテナー環境で使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-562">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="590eb-563">サンプル アプリによって、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を使用してポートが設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-563">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="590eb-564">ポートは *launchSettings.json* ファイルに設定され、環境変数を介して構成プロバイダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-564">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="590eb-565">管理ポートで要求を待つようにサーバーを設定する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="590eb-565">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="590eb-566">サンプル アプリを使用し、管理ポート設定を実演するには、*Properties* フォルダーで *launchSettings.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="590eb-566">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="590eb-567">サンプル アプリの次の *Properties/launchSettings.json* ファイルは、サンプル アプリのプロジェクト ファイルに含まれていないため、手動で作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590eb-567">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="590eb-568">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="590eb-568">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="590eb-569"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> の呼び出しによって管理ポートが指定されます (*ManagementPortStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="590eb-569">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="590eb-570">URL と管理ポートをコードに明示的に設定することで、サンプル アプリで *launchSettings.json* ファイルを作成することを回避できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-570">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="590eb-571"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が作成される *Program.cs* で、<xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> の呼び出しを追加し、アプリの通常の応答エンドポイントと管理ポート エンドポイントを指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-571">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="590eb-572"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> が呼び出される *ManagementPortStartup.cs* で、管理ポートを明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-572">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="590eb-573">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="590eb-573">*Program.cs*:</span></span>
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
> <span data-ttu-id="590eb-574">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="590eb-574">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="590eb-575">サンプル アプリを使用して管理ポート構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="590eb-575">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="590eb-576">正常性チェック ライブラリを配布する</span><span class="sxs-lookup"><span data-stu-id="590eb-576">Distribute a health check library</span></span>

<span data-ttu-id="590eb-577">正常性チェックをライブラリとして配布するには:</span><span class="sxs-lookup"><span data-stu-id="590eb-577">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="590eb-578">スタンドアロン クラスとして <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装する正常性チェックを記述します。</span><span class="sxs-lookup"><span data-stu-id="590eb-578">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="590eb-579">このクラスは、設定データにアクセスする目的で[依存関係挿入 (DI)](xref:fundamentals/dependency-injection)、型のアクティブ化、[名前付きオプション](xref:fundamentals/configuration/options)に依存できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-579">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="590eb-580">`CheckHealthAsync` の正常性チェックのロジックでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590eb-580">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="590eb-581">`data1` と `data2` は、プローブの正常性チェック ロジックを実行するためにメソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-581">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="590eb-582">`AccessViolationException` が処理されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-582">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="590eb-583"><xref:System.AccessViolationException> が発生すると、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> とともに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> が返され、ユーザーは正常性チェックの失敗状態を構成できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-583">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="590eb-584">拡張メソッドを記述します。拡張メソッドを使用するアプリがその `Startup.Configure` メソッドで呼び出すパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-584">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="590eb-585">次の例では、次の正常性チェック メソッド シグネチャを想定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-585">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="590eb-586">先のシグネチャは、正常性チェック プローブ ロジックを処理する目的で `ExampleHealthCheck` に追加データが要求されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="590eb-586">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="590eb-587">正常性チェックが拡張メソッドに登録されたときに正常性チェック インスタンスを作成するための委任にデータが提供されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-587">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="590eb-588">次の例では、呼び出し元によって次が任意で指定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-588">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="590eb-589">正常性チェック名 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="590eb-589">health check name (`name`).</span></span> <span data-ttu-id="590eb-590">`null` の場合、`example_health_check` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-590">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="590eb-591">正常性チェックの文字列データ ポイント (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="590eb-591">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="590eb-592">正常性チェックの整数データ ポイント (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="590eb-592">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="590eb-593">`null` の場合、`1` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-593">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="590eb-594">エラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="590eb-594">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="590eb-595">既定値は、`null` です。</span><span class="sxs-lookup"><span data-stu-id="590eb-595">The default is `null`.</span></span> <span data-ttu-id="590eb-596">`null` の場合、エラー状態に対して [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-596">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="590eb-597">タグ (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="590eb-597">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="590eb-598">正常性チェック パブリッシャー</span><span class="sxs-lookup"><span data-stu-id="590eb-598">Health Check Publisher</span></span>

<span data-ttu-id="590eb-599">サービス コンテナーに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> が追加されると、正常性チェック システムにより定期的に正常性チェックが実行され、結果と共に `PublishAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-599">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="590eb-600">これは、正常性を判断する目的で監視システムを定期的に呼び出すことを各プロセスに求めるプッシュベースの監視システム シナリオで便利です。</span><span class="sxs-lookup"><span data-stu-id="590eb-600">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="590eb-601"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インターフェイスには単一メソッドが与えられます。</span><span class="sxs-lookup"><span data-stu-id="590eb-601">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="590eb-602"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> を使うと次を設定できます。</span><span class="sxs-lookup"><span data-stu-id="590eb-602"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="590eb-603"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; アプリが起動した後、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスを実行する前に適用される初期遅延です。</span><span class="sxs-lookup"><span data-stu-id="590eb-603"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="590eb-604">遅延はスタートアップ時に一度だけ適用され、以降の繰り返しには適用されません。</span><span class="sxs-lookup"><span data-stu-id="590eb-604">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="590eb-605">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="590eb-605">The default value is five seconds.</span></span>
* <span data-ttu-id="590eb-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> を実行する期間です。</span><span class="sxs-lookup"><span data-stu-id="590eb-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="590eb-607">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="590eb-607">The default value is 30 seconds.</span></span>
* <span data-ttu-id="590eb-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> が `null` (既定値) の場合、正常性チェック パブリッシャー サービスにより登録済みのすべての正常性チェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="590eb-609">正常性チェックのサブセットを実行するには、チェックのセットをフィルター処理する関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="590eb-609">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="590eb-610">述語は期間ごとに評価されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-610">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="590eb-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; すべての <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスに対する正常性チェックの実行のタイムアウトです。</span><span class="sxs-lookup"><span data-stu-id="590eb-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="590eb-612">タイムアウトなしで実行するには <xref:System.Threading.Timeout.InfiniteTimeSpan> を使います。</span><span class="sxs-lookup"><span data-stu-id="590eb-612">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="590eb-613">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="590eb-613">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="590eb-614">ASP.NET Core 2.2 のリリースでは、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> を設定しても <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装により無視されます。<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> の値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-614">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="590eb-615">この問題は ASP.NET Core 3.0 で解決しています。</span><span class="sxs-lookup"><span data-stu-id="590eb-615">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="590eb-616">サンプル アプリでは、`ReadinessPublisher` が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装です。</span><span class="sxs-lookup"><span data-stu-id="590eb-616">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="590eb-617">正常性チェックの状態は、チェックごとに以下のいずれかでログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-617">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="590eb-618">情報 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>): 正常性チェックの状態が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> の場合。</span><span class="sxs-lookup"><span data-stu-id="590eb-618">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="590eb-619">エラー (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) 状態が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> または <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> の場合。</span><span class="sxs-lookup"><span data-stu-id="590eb-619">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="590eb-620">サンプル アプリの `LivenessProbeStartup` の例では、対応性チェック `StartupHostedService` にはスタートアップの遅延が 2 秒間あり、30 秒ごとにチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="590eb-620">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="590eb-621"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装をアクティブ化するために、サンプルでは `ReadinessPublisher` をシングルトン サービスとして[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーに登録しています。</span><span class="sxs-lookup"><span data-stu-id="590eb-621">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="590eb-622">1 つ以上の他のホステッド サービスが既にアプリに追加されている場合、次の回避策により <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスをサービス コンテナーに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="590eb-622">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="590eb-623">ASP.NET Core 3.0 では、この回避策は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="590eb-623">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="590eb-624">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、[Application Insights](/azure/application-insights/app-insights-overview) など、いくつかのシステムのパブリッシャーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="590eb-624">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="590eb-625">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は、マイクロソフトによって保守またはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="590eb-625">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="590eb-626">MapWhen で正常性チェックを制限する</span><span class="sxs-lookup"><span data-stu-id="590eb-626">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="590eb-627"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> を使用して、正常性チェック エンドポイントの要求パイプラインを条件付きで分岐します。</span><span class="sxs-lookup"><span data-stu-id="590eb-627">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="590eb-628">次の例では、`api/HealthCheck` エンドポイントに対して GET 要求が受信された場合に、`MapWhen` が要求パイプラインを分岐して正常性チェック ミドルウェアをアクティブ化します。</span><span class="sxs-lookup"><span data-stu-id="590eb-628">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="590eb-629">詳細については、<xref:fundamentals/middleware/index#use-run-and-map> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590eb-629">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
