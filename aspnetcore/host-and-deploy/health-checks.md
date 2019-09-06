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
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="8a2c9-103">ASP.NET Core の正常性チェック</span><span class="sxs-lookup"><span data-stu-id="8a2c9-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="8a2c9-104">作成者: [Luke Latham](https://github.com/guardrex) と [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="8a2c9-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

<span data-ttu-id="8a2c9-105">ASP.NET Core からは、アプリ インフラストラクチャ コンポーネントの正常性を報告するための正常性チェック ミドルウェアとライブラリが提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-105">ASP.NET Core offers Health Check Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="8a2c9-106">正常性チェックは HTTP エンドポイントとしてアプリによって公開されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="8a2c9-107">正常性チェック エンドポイントは、さまざまなリアルタイム監視シナリオに合わせて設定できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="8a2c9-108">正常性プローブは、アプリの状態を確認する目的でコンテナー オーケストレーターとロード バランサーによって使用できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="8a2c9-109">たとえば、正常性チェックで問題が確認された場合、コンテナー オーケストレーターは実行中の展開を停止したり、コンテナーを再起動したりします。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="8a2c9-110">ロード バランサーはアプリの異常に対処するため、問題のあるインスタンスから正常なインスタンスにトラフィック経路を変更することがあります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="8a2c9-111">メモリ、ディスク、その他の物理サーバー リソースの使用を監視し、正常性の状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="8a2c9-112">正常性チェックでは、データベースや外部サービス エンドポイントなど、アプリの依存関係をテストし、それらが利用できることと正常に機能していることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="8a2c9-113">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8a2c9-114">サンプル アプリには、このトピックで説明されているシナリオの例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="8a2c9-115">所与のシナリオに対してサンプル アプリを実行するには、コマンド シェルでプロジェクトのフォルダーから [dotnet run](/dotnet/core/tools/dotnet-run) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="8a2c9-116">サンプル アプリの詳しい使用方法については、サンプル アプリの *README.md* ファイルと本トピックのシナリオ説明をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8a2c9-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8a2c9-117">Prerequisites</span></span>

<span data-ttu-id="8a2c9-118">正常性チェックは通常、アプリの状態を確認する目的で、外部の監視サービスまたはコンテナー オーケストレーターと共に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="8a2c9-119">正常性チェックをアプリに追加する前に、使用する監視システムを決定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="8a2c9-120">監視システムからは、作成する正常性チェックの種類とそのエンドポイントの設定方法が指示されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="8a2c9-121">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-121">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="8a2c9-122">サンプル アプリからは、いくつかのシナリオで正常性チェックを実演するスタートアップ コードが提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-122">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="8a2c9-123">[データベース プローブ](#database-probe) シナリオでは、[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) を使用して、データベース接続の正常性がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-123">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="8a2c9-124">[DbContext プローブ](#entity-framework-core-dbcontext-probe) シナリオでは、EF Core `DbContext` を使用して、データベースがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-124">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="8a2c9-125">データベース シナリオを探索するために、サンプル アプリでは次のことが行われます:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-125">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="8a2c9-126">データベースを作成して、*appsettings.json* ファイルにその接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-126">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="8a2c9-127">そのプロジェクト ファイルに次のパッケージ参照が含まれています:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-127">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="8a2c9-128">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="8a2c9-128">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="8a2c9-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="8a2c9-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="8a2c9-130">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は [BeatPulse](https://github.com/xabaril/beatpulse) の 1 つのポートであり、マイクロソフトによる保守やサポートは行われません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-130">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="8a2c9-131">もう 1 つの正常性チェックのシナリオでは、1 つの管理ポートに正常性チェックを絞り込む方法が実演されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-131">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="8a2c9-132">このサンプル アプリでは、管理 URL と管理ポートを含む *Properties/launchSettings.json* ファイルを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-132">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="8a2c9-133">詳細については、「[ポート別のフィルター処理](#filter-by-port)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-133">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="8a2c9-134">基本的な正常性プローブ</span><span class="sxs-lookup"><span data-stu-id="8a2c9-134">Basic health probe</span></span>

<span data-ttu-id="8a2c9-135">多くのアプリでは、アプリが要求を処理できること (*活動性*) を報告する基本的な正常性チェック構成で十分にアプリの状態を検出できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-135">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="8a2c9-136">基本の構成では、正常性チェック サービスを登録し、正常性チェック ミドルウェアを呼び出します。このミドルウェアが特定の URL エンドポイントにおける正常性を返します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-136">The basic configuration registers health check services and calls the Health Check Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="8a2c9-137">既定では、特定の依存関係またはサブシステムをテストする正常性チェックは登録されていません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-137">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="8a2c9-138">正常性エンドポイント URL で応答できる場合、そのアプリは正常であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-138">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="8a2c9-139">既定の応答ライターによって、プレーンテキストの応答として状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) がクライアントに書き込まれます。このとき、状態として [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、または [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が示されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-139">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="8a2c9-140">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-140">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a2c9-141">`Startup.Configure` の要求処理パイプラインに正常性チェック ミドルウェアと <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を追加します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-141">Add Health Check Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="8a2c9-142">サンプル アプリでは、正常性チェック エンドポイントは `/health` で作成されます (*BasicStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-142">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

<span data-ttu-id="8a2c9-143">サンプル アプリを使用して基本的な構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-143">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="8a2c9-144">Docker の例</span><span class="sxs-lookup"><span data-stu-id="8a2c9-144">Docker example</span></span>

<span data-ttu-id="8a2c9-145">[Docker](xref:host-and-deploy/docker/index) からは、組み込み `HEALTHCHECK` ディレクティブが提供されます。これを使用し、基本的な正常性チェック構成を使用するアプリの状態を確認できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-145">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="8a2c9-146">正常性チェックを作成する</span><span class="sxs-lookup"><span data-stu-id="8a2c9-146">Create health checks</span></span>

<span data-ttu-id="8a2c9-147">正常性チェックは <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-147">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="8a2c9-148"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> メソッドによって、`Healthy`、`Degraded`、`Unhealthy` のいずれかの正常性を示す `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>` が返されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-148">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>` that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="8a2c9-149">この結果が構成可能な状態コードと共にプレーンテキストの応答として書き込まれます (構成の説明は「[正常性チェック オプション](#health-check-options)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-149">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="8a2c9-150"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> からは、任意のキーと値のペアを返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-150"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="8a2c9-151">正常性チェックの例</span><span class="sxs-lookup"><span data-stu-id="8a2c9-151">Example health check</span></span>

<span data-ttu-id="8a2c9-152">次の `ExampleHealthCheck` クラスからは、正常性チェックのレイアウトがどのようなものかがわかります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check:</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="8a2c9-153">正常性チェック サービスを登録する</span><span class="sxs-lookup"><span data-stu-id="8a2c9-153">Register health check services</span></span>

<span data-ttu-id="8a2c9-154"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> によって `ExampleHealthCheck` 型が正常性チェック サービスに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-154">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

<span data-ttu-id="8a2c9-155">次の例にある <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> オーバーロードでは、正常性チェックでエラーが報告されると、レポートにエラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-155">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="8a2c9-156">エラー状態が `null` (既定) に設定された場合、[HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-156">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="8a2c9-157">このオーバーロードはライブラリ作成者にとって便利です。この設定に正常性チェック実装が従う場合、正常性チェック エラーが発生したとき、ライブラリによって指示されるエラー状態がアプリによって適用されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-157">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="8a2c9-158">*タグ*を使用し、正常性チェックをフィルター処理できます (詳細は「[正常性チェックをフィルター処理する](#filter-health-checks)」セクションにあります)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-158">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="8a2c9-159"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> では、lambda 関数も実行できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-159"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="8a2c9-160">次の例では、正常性チェック名に「`Example`」が指定されいます。このチェックでは状態として常に正常が返されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-160">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () =>
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="8a2c9-161">正常性チェック ミドルウェアを使用する</span><span class="sxs-lookup"><span data-stu-id="8a2c9-161">Use Health Checks Middleware</span></span>

<span data-ttu-id="8a2c9-162">`Startup.Configure` で、エンドポイント URL または相対パスを利用し、処理パイプラインで <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-162">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

<span data-ttu-id="8a2c9-163">正常性チェックに特定のポートで待機させる場合、<xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> のオーバーロードを使用してポートを設定します ([詳細は「ポート別のフィルター処理」セクションにあります](#filter-by-port))。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-163">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

<span data-ttu-id="8a2c9-164">正常性チェック ミドルウェアは、アプリの要求処理パイプラインの*ターミナル ミドルウェア*です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-164">Health Checks Middleware is a *terminal middleware* in the app's request processing pipeline.</span></span> <span data-ttu-id="8a2c9-165">最初に現れた、要求 URL にぴったり一致する正常性チェック エンドポイントでチェックが実行され、ミドルウェア パイプラインの残りは回避されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-165">The first health check endpoint encountered that's an exact match to the request URL executes and short-circuits the rest of the middleware pipeline.</span></span> <span data-ttu-id="8a2c9-166">回避になると、一致した正常性チェックの後、ミドルウェアは実行されません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-166">When short-circuiting occurs, no middleware following the matched health check executes.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="8a2c9-167">正常性チェック オプション</span><span class="sxs-lookup"><span data-stu-id="8a2c9-167">Health check options</span></span>

<span data-ttu-id="8a2c9-168"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> では、正常性チェックの動作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-168"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="8a2c9-169">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="8a2c9-169">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="8a2c9-170">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8a2c9-170">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="8a2c9-171">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="8a2c9-171">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="8a2c9-172">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8a2c9-172">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="8a2c9-173">正常性チェックをフィルター処理する</span><span class="sxs-lookup"><span data-stu-id="8a2c9-173">Filter health checks</span></span>

<span data-ttu-id="8a2c9-174">既定では、正常性チェック ミドルウェアでは、登録されている正常性チェックがすべて実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-174">By default, Health Check Middleware runs all registered health checks.</span></span> <span data-ttu-id="8a2c9-175">正常性チェックのサブセットを実行するには、<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> オプションにブール値を返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-175">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="8a2c9-176">次の例では、関数の条件文にあるそのタグ (`bar_tag`) により `Bar` 正常性チェックが除外されます。`true` は、正常性チェックの <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> プロパティが `foo_tag` か `baz_tag` に一致した場合にのみ返されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-176">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="8a2c9-177">HTTP 状態コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8a2c9-177">Customize the HTTP status code</span></span>

<span data-ttu-id="8a2c9-178"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> を使用し、HTTP 状態コードに対する正常性状態のマッピングをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-178">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="8a2c9-179">次の <xref:Microsoft.AspNetCore.Http.StatusCodes> 代入はミドルウェアによって使用される既定値です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-179">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="8a2c9-180">要件に合わせて状態コード値を変更します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-180">Change the status code values to meet your requirements.</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="8a2c9-181">キャッシュ ヘッダーを非表示にする</span><span class="sxs-lookup"><span data-stu-id="8a2c9-181">Suppress cache headers</span></span>

<span data-ttu-id="8a2c9-182"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> によって、応答キャッシュを禁止する目的で、正常性チェック ミドルウェアによって HTTP ヘッダーがプローブ応答に追加されるかどうかが制御されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-182"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Check Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="8a2c9-183">値が `false` (既定) の場合、応答キャッシュを禁止する目的で、ミドルウェアによってヘッダー `Cache-Control`、`Expires`、`Pragma` が設定またはオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-183">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="8a2c9-184">値が `true` の場合、応答のキャッシュ ヘッダーがミドルウェアによって変更されることはありません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-184">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

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

### <a name="customize-output"></a><span data-ttu-id="8a2c9-185">出力をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8a2c9-185">Customize output</span></span>

<span data-ttu-id="8a2c9-186"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> オプションによって、応答の書き込みに使用される委任が取得または設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-186">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="8a2c9-187">既定の委任では、文字列値 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) を含む、最小のプレーンテキスト応答が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-187">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

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

## <a name="database-probe"></a><span data-ttu-id="8a2c9-188">データベース プローブ</span><span class="sxs-lookup"><span data-stu-id="8a2c9-188">Database probe</span></span>

<span data-ttu-id="8a2c9-189">正常性チェックでは、データベースが通常どおり応答しているかどうかを示すブール値テストとして実行されるよう、データベース クエリを指定できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-189">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="8a2c9-190">サンプル アプリでは、SQL Server データベース上で正常性のチェックを実行するために、ASP.NET Core アプリの正常性チェック ライブラリの [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 使用します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-190">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="8a2c9-191">`AspNetCore.Diagnostics.HealthChecks` によってデータベースに対して `SELECT 1` クエリが実行され、データベースへの接続が正常であることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-191">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="8a2c9-192">クエリでデータベース接続を確認するとき、すぐに返されるクエリを選択します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-192">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="8a2c9-193">このクエリ手法には、データベースをオーバーロードし、そのパフォーマンスを低下させるというリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-193">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="8a2c9-194">ほとんどの場合、テスト クエリは実行する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-194">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="8a2c9-195">データベースに正常に接続できれば十分です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-195">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="8a2c9-196">クエリを実行する必要があれば、`SELECT 1` など、単純な SELECT クエリを選択してください。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-196">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="8a2c9-197">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) へのパッケージ参照を含めます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-197">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="8a2c9-198">サンプル アプリの *appsettings.json* ファイルに有効なデータベース接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-198">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="8a2c9-199">このアプリでは `HealthCheckSample` という名前の SQL Server データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-199">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="8a2c9-200">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-200">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a2c9-201">サンプル アプリでは、データベースの接続文字列 (*DbHealthStartup.cs*) を利用して `AddSqlServer` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-201">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="8a2c9-202">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-202">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="8a2c9-203">サンプル アプリを使用してデータベース プローブ シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-203">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="8a2c9-204">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は [BeatPulse](https://github.com/xabaril/beatpulse) の 1 つのポートであり、マイクロソフトによる保守やサポートは行われません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-204">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="8a2c9-205">Entity Framework Core DbContext プローブ</span><span class="sxs-lookup"><span data-stu-id="8a2c9-205">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="8a2c9-206">`DbContext` チェックでは、EF Core `DbContext` に対して構成されているデータベースとアプリとが通信できることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-206">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="8a2c9-207">`DbContext` チェックは、次のようなアプリでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-207">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="8a2c9-208">[Entity Framework (EF) Core を使用する](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-208">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="8a2c9-209">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) へのパッケージ参照を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-209">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="8a2c9-210">`AddDbContextCheck<TContext>` によって `DbContext` の正常性チェックが登録されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-210">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="8a2c9-211">`DbContext` は `TContext` としてメソッドに指定されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-211">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="8a2c9-212">オーバーロードはエラー状態、タグ、カスタム テスト クエリの設定に利用できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-212">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="8a2c9-213">既定では:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-213">By default:</span></span>

* <span data-ttu-id="8a2c9-214">`DbContextHealthCheck` によって EF Core の `CanConnectAsync` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-214">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="8a2c9-215">`AddDbContextCheck` メソッドのオーバーロードを使用して正常性を確認するときに実行される操作をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-215">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="8a2c9-216">正常性チェックの名前は `TContext` 型の名前になります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-216">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="8a2c9-217">サンプル アプリでは、`AppDbContext` が `AddDbContextCheck` に提供され、`Startup.ConfigureServices` でサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-217">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="8a2c9-218">*DbContextHealthStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-218">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="8a2c9-219">サンプル アプリで、`UseHealthChecks` によって `Startup.Configure` に正常性チェック ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-219">In the sample app, `UseHealthChecks` adds the Health Check Middleware in `Startup.Configure`.</span></span>

<span data-ttu-id="8a2c9-220">*DbContextHealthStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-220">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="8a2c9-221">サンプル アプリを利用して `DbContext` プローブ シナリオを実行するには、接続文字列によって指定されるデータベースが SQL Server インスタンスに存在しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-221">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="8a2c9-222">データベースが存在する場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-222">If the database exists, delete it.</span></span>

<span data-ttu-id="8a2c9-223">コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-223">Execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario dbcontext
```

<span data-ttu-id="8a2c9-224">アプリの実行後、ブラウザーで `/health` エンドポイントに要求することで正常性状態を確認します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-224">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="8a2c9-225">データベースと `AppDbContext` は存在しません。そこで、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-225">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="8a2c9-226">サンプル アプリにデータベースを作成させます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-226">Trigger the sample app to create the database.</span></span> <span data-ttu-id="8a2c9-227">`/createdatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-227">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="8a2c9-228">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-228">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="8a2c9-229">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-229">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="8a2c9-230">データベースとコンテキストが存在します。そのため、アプリによって次の応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-230">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="8a2c9-231">サンプル アプリにデータベースを削除させます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-231">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="8a2c9-232">`/deletedatabase` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-232">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="8a2c9-233">アプリからの応答は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-233">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="8a2c9-234">`/health` エンドポイントに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-234">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="8a2c9-235">アプリから異常という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-235">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="8a2c9-236">対応性と活動性に区分されるプローブ</span><span class="sxs-lookup"><span data-stu-id="8a2c9-236">Separate readiness and liveness probes</span></span>

<span data-ttu-id="8a2c9-237">一部のホスティング シナリオでは、2 つのアプリ状態を区別する一組の正常性チェックが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-237">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="8a2c9-238">アプリは機能しているが、要求を受信する準備がまだできていない。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-238">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="8a2c9-239">この状態がアプリの*対応性*です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-239">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="8a2c9-240">アプリは機能しており、要求に応答する。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-240">The app is functioning and responding to requests.</span></span> <span data-ttu-id="8a2c9-241">この状態がアプリの*活動性*です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-241">This state is the app's *liveness*.</span></span>

<span data-ttu-id="8a2c9-242">対応性チェックは通常、より広範囲で時間のかかる一連のチェックが実行され、アプリのすべてのサブシステムとリソースが利用できるかどうかが判断されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-242">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="8a2c9-243">活動性チェックでは、アプリが要求を処理できるかどうかを判断する簡易チェックのみが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-243">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="8a2c9-244">アプリが対応性チェックに合格したら、活動性の確認のみを求める不経済な一連の活動性チェックをアプリに負わせる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-244">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="8a2c9-245">サンプル アプリには、[ホステッド サービス](xref:fundamentals/host/hosted-services)の長時間実行スタートアップ タスクの完了を報告する正常性チェックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-245">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="8a2c9-246">`StartupHostedServiceHealthCheck` によって `StartupTaskCompleted` というプロパティが公開されます。ホステッド サービスでは長時間実行タスクの完了時にこのプロパティを `true` に設定できます (*StartupHostedServiceHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-246">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="8a2c9-247">長時間実行バックグラウンド タスクは[ホステッド サービス](xref:fundamentals/host/hosted-services)によって開始されます (*Services/StartupHostedService*)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-247">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="8a2c9-248">タスクが終わると、`StartupHostedServiceHealthCheck.StartupTaskCompleted` が `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-248">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="8a2c9-249">ホステッド サービスと共に `Startup.ConfigureServices` で正常性チェックが <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に登録されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-249">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="8a2c9-250">ホステッド サービスでは正常性チェックにプロパティを設定する必要があるため、正常性チェックはサービス コンテナーにも登録されます (*LivenessProbeStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-250">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="8a2c9-251">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-251">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="8a2c9-252">サンプル アプリで、対応性チェックのための正常性チェック エンドポイントが `/health/ready` で作成され、活動性チェックのための正常性チェック エンドポイントが `/health/live` で作成されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-252">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="8a2c9-253">対応性チェックでは、`ready` タグが与えられているものに正常性チェックが絞り込まれます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-253">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="8a2c9-254">活動性チェックでは、[HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) で `false` を返すことで `StartupHostedServiceHealthCheck` が除外されます (詳細については、「[正常性チェックをフィルター処理する](#filter-health-checks)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-254">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

<span data-ttu-id="8a2c9-255">サンプル アプリを使用して対応性/活動性構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-255">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario liveness
```

<span data-ttu-id="8a2c9-256">ブラウザーで、15 秒が経過するまで `/health/ready` に数回アクセスします。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-256">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="8a2c9-257">正常性チェックにより最初の 15 秒に対して "*異常*" が報告されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-257">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="8a2c9-258">15 秒後、エンドポイントから "*正常*" が報告されます。これは、ホステッド サービスによる長時間実行タスクが完了したことを反映しています。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-258">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="8a2c9-259">この例では、2 秒間の遅延で最初の対応性チェックを実行する正常性チェック パブリッシャー (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装) も作成されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-259">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="8a2c9-260">詳細については、「[正常性チェック パブリッシャー](#health-check-publisher)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-260">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="8a2c9-261">Kubernetes の例</span><span class="sxs-lookup"><span data-stu-id="8a2c9-261">Kubernetes example</span></span>

<span data-ttu-id="8a2c9-262">対応性チェックと活動性チェックを使い分けることは、[Kubernetes](https://kubernetes.io/) などの環境で便利です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-262">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="8a2c9-263">Kubernetes では、要求を受け付ける前に、基礎をなすデータベースの可用性テストなど、時間のかかるスタートアップ作業の実行がアプリに要求されることがあります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-263">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="8a2c9-264">別個のチェックを利用することで、アプリは機能しているがまだ準備ができていないか、あるいはアプリが起動に失敗したかをオーケストレーターは区別できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-264">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="8a2c9-265">Kubernetes の対応性プローブと活動性プローブに関する詳細については、Kubernetes ドキュメントの「[Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)」 (活動性プローブと対応性プローブを設定する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-265">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="8a2c9-266">次の例では、Kubernetes 対応性プローブの構成を確認できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-266">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="8a2c9-267">カスタム応答ライターを含むメトリック ベースのプローブ</span><span class="sxs-lookup"><span data-stu-id="8a2c9-267">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="8a2c9-268">このサンプル アプリでは、カスタム応答ライターを含む、メモリ正常性チェックを確認できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-268">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="8a2c9-269">与えられたしきい値を超えるメモリがアプリで使用された場合 (このサンプル アプリでは 1 GB)、`MemoryHealthCheck` から異常状態が報告されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-269">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="8a2c9-270"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> にはアプリのガベージ コレクター (GC) 情報が含まれています (*MemoryHealthCheck.cs*)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-270">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="8a2c9-271">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-271">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a2c9-272"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> に渡して正常性チェックを有効にする代わりに、`MemoryHealthCheck` がサービスとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-272">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="8a2c9-273"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> が登録されたサービスはすべて、正常性チェック サービスとミドルウェアで利用できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-273">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="8a2c9-274">正常性チェック サービスはシングルトン サービスとして登録することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-274">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="8a2c9-275">*CustomWriterStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-275">*CustomWriterStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="8a2c9-276">`Startup.Configure` のアプリ処理パイプラインで正常性チェック ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-276">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="8a2c9-277">`WriteResponse` 委任が `ResponseWriter` プロパティに与えられることで、正常性チェックの実行時に、カスタム JSON 応答が出力されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-277">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

<span data-ttu-id="8a2c9-278">`WriteResponse` メソッドによって `CompositeHealthCheckResult` が書式設定されて JSON オブジェクトが生成され、正常性チェック応答のために JSON 出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-278">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="8a2c9-279">サンプル アプリを使用してカスタム応答ライターを含むメトリックベースのプローブを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-279">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="8a2c9-280">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、ディスク ストレージや最大値活動性のチェックなど、メトリックベースの正常性チェック シナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-280">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="8a2c9-281">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は [BeatPulse](https://github.com/xabaril/beatpulse) の 1 つのポートであり、マイクロソフトによる保守やサポートは行われません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-281">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="8a2c9-282">ポート別のフィルター処理</span><span class="sxs-lookup"><span data-stu-id="8a2c9-282">Filter by port</span></span>

<span data-ttu-id="8a2c9-283">ポートを指定して <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> を呼び出すと、正常性チェック要求は指定されたポートに制限されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-283">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="8a2c9-284">これは通常、サービスを監視するためのポートを公開する目的で、コンテナー環境で使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-284">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="8a2c9-285">サンプル アプリによって、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を使用してポートが設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-285">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8a2c9-286">ポートは *launchSettings.json* ファイルに設定され、環境変数を介して構成プロバイダーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-286">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="8a2c9-287">管理ポートで要求を待つようにサーバーを設定する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-287">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="8a2c9-288">サンプル アプリを使用し、管理ポート設定を実演するには、*Properties* フォルダーで *launchSettings.json* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-288">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="8a2c9-289">次の *launchSettings.json* ファイルはサンプル アプリのプロジェクト ファイルに含まれません。手動で作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-289">The following *launchSettings.json* file isn't included in the sample app's project files and must be created manually.</span></span>

<span data-ttu-id="8a2c9-290">*Properties/launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-290">*Properties/launchSettings.json*:</span></span>

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

<span data-ttu-id="8a2c9-291">正常性チェック サービスを `Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> に登録します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-291">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a2c9-292"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> の呼び出しによって管理ポートが指定されます (*ManagementPortStartup.cs*)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-292">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> <span data-ttu-id="8a2c9-293">URL と管理ポートをコードに明示的に設定することで、サンプル アプリで *launchSettings.json* ファイルを作成することを回避できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-293">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="8a2c9-294"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が作成される *Program.cs* で、<xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> の呼び出しを追加し、アプリの通常の応答エンドポイントと管理ポート エンドポイントを指定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-294">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="8a2c9-295"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> が呼び出される *ManagementPortStartup.cs* で、管理ポートを明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-295">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="8a2c9-296">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-296">*Program.cs*:</span></span>
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
> <span data-ttu-id="8a2c9-297">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-297">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="8a2c9-298">サンプル アプリを使用して管理ポート構成シナリオを実行するには、コマンド シェルでプロジェクトのフォルダーから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-298">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="8a2c9-299">正常性チェック ライブラリを配布する</span><span class="sxs-lookup"><span data-stu-id="8a2c9-299">Distribute a health check library</span></span>

<span data-ttu-id="8a2c9-300">正常性チェックをライブラリとして配布するには:</span><span class="sxs-lookup"><span data-stu-id="8a2c9-300">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="8a2c9-301">スタンドアロン クラスとして <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> インターフェイスを実装する正常性チェックを記述します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-301">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="8a2c9-302">このクラスは、設定データにアクセスする目的で[依存関係挿入 (DI)](xref:fundamentals/dependency-injection)、型のアクティブ化、[名前付きオプション](xref:fundamentals/configuration/options)に依存できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-302">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

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

1. <span data-ttu-id="8a2c9-303">拡張メソッドを記述します。拡張メソッドを使用するアプリがその `Startup.Configure` メソッドで呼び出すパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-303">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="8a2c9-304">次の例では、次の正常性チェック メソッド シグネチャを想定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-304">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="8a2c9-305">先のシグネチャは、正常性チェック プローブ ロジックを処理する目的で `ExampleHealthCheck` に追加データが要求されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-305">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="8a2c9-306">正常性チェックが拡張メソッドに登録されたときに正常性チェック インスタンスを作成するための委任にデータが提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-306">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="8a2c9-307">次の例では、呼び出し元によって次が任意で指定されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-307">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="8a2c9-308">正常性チェック名 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-308">health check name (`name`).</span></span> <span data-ttu-id="8a2c9-309">`null` の場合、`example_health_check` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-309">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="8a2c9-310">正常性チェックの文字列データ ポイント (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-310">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="8a2c9-311">正常性チェックの整数データ ポイント (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-311">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="8a2c9-312">`null` の場合、`1` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-312">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="8a2c9-313">エラー状態 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-313">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="8a2c9-314">既定値は、`null` です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-314">The default is `null`.</span></span> <span data-ttu-id="8a2c9-315">`null` の場合、エラー状態に対して [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-315">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="8a2c9-316">タグ (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-316">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="8a2c9-317">正常性チェック パブリッシャー</span><span class="sxs-lookup"><span data-stu-id="8a2c9-317">Health Check Publisher</span></span>

<span data-ttu-id="8a2c9-318">サービス コンテナーに <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> が追加されると、正常性チェック システムにより定期的に正常性チェックが実行され、結果と共に `PublishAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-318">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="8a2c9-319">これは、正常性を判断する目的で監視システムを定期的に呼び出すことを各プロセスに求めるプッシュベースの監視システム シナリオで便利です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-319">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="8a2c9-320"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インターフェイスには単一メソッドが与えられます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-320">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="8a2c9-321"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> を使うと次を設定できます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-321"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="8a2c9-322"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; アプリが起動した後、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスを実行する前に適用される初期遅延です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-322"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="8a2c9-323">遅延はスタートアップ時に一度だけ適用され、以降の繰り返しには適用されません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-323">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="8a2c9-324">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-324">The default value is five seconds.</span></span>
* <span data-ttu-id="8a2c9-325"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> を実行する期間です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-325"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="8a2c9-326">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-326">The default value is 30 seconds.</span></span>
* <span data-ttu-id="8a2c9-327"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> が `null` (既定値) の場合、正常性チェック パブリッシャー サービスにより登録済みのすべての正常性チェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-327"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="8a2c9-328">正常性チェックのサブセットを実行するには、チェックのセットをフィルター処理する関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-328">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="8a2c9-329">述語は期間ごとに評価されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-329">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="8a2c9-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; すべての <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスに対する正常性チェックの実行のタイムアウトです。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="8a2c9-331">タイムアウトなしで実行するには <xref:System.Threading.Timeout.InfiniteTimeSpan> を使います。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-331">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="8a2c9-332">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-332">The default value is 30 seconds.</span></span>

::: moniker range="= aspnetcore-2.2"

> [!WARNING]
> <span data-ttu-id="8a2c9-333">ASP.NET Core 2.2 のリリースでは、<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> を設定しても <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装により無視されます。<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> の値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-333">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="8a2c9-334">この問題は、ASP.NET Core 3.0 で修正される予定です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-334">This issue will be fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a2c9-335">詳細については、「[HealthCheckPublisherOptions.Period sets the value of .Delay (HealthCheckPublisherOptions.Period により .Delay の値が設定される)](https://github.com/aspnet/Extensions/issues/1041)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-335">For more information, see [HealthCheckPublisherOptions.Period sets the value of .Delay](https://github.com/aspnet/Extensions/issues/1041).</span></span>

::: moniker-end

<span data-ttu-id="8a2c9-336">サンプル アプリでは、`ReadinessPublisher` が <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装です。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-336">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="8a2c9-337">正常性チェックの状態は `Entries` に記録され、チェックごとにログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-337">The health check status is recorded in `Entries` and logged for each check:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=20,22-23)]

<span data-ttu-id="8a2c9-338">サンプル アプリの `LivenessProbeStartup` の例では、対応性チェック `StartupHostedService` にはスタートアップの遅延が 2 秒間あり、30 秒ごとにチェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-338">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="8a2c9-339"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> の実装をアクティブ化するために、サンプルでは `ReadinessPublisher` をシングルトン サービスとして[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーに登録しています。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-339">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

::: moniker range="= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="8a2c9-340">1 つ以上の他のホステッド サービスが既にアプリに追加されている場合、次の回避策により <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> インスタンスをサービス コンテナーに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-340">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="8a2c9-341">この回避策は、ASP.NET Core 3.0 のリリースと共に必要なくなります。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-341">This workaround won't be required with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="8a2c9-342">詳細については、「 https://github.com/aspnet/Extensions/issues/639 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-342">For more information, see: https://github.com/aspnet/Extensions/issues/639.</span></span>
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
> <span data-ttu-id="8a2c9-343">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) には、[Application Insights](/azure/application-insights/app-insights-overview) など、いくつかのシステムのパブリッシャーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-343">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="8a2c9-344">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) は [BeatPulse](https://github.com/xabaril/beatpulse) の 1 つのポートであり、マイクロソフトによる保守やサポートは行われません。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-344">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="8a2c9-345">MapWhen で正常性チェックを制限する</span><span class="sxs-lookup"><span data-stu-id="8a2c9-345">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="8a2c9-346"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> を使用して、正常性チェック エンドポイントの要求パイプラインを条件付きで分岐します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-346">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="8a2c9-347">次の例では、`api/HealthCheck` エンドポイントに対して GET 要求が受信された場合に、`MapWhen` が要求パイプラインを分岐して正常性チェック ミドルウェアをアクティブ化します。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-347">In the following example, `MapWhen` branches the request pipeline to activate Health Check Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="8a2c9-348">詳細については、<xref:fundamentals/middleware/index#use-run-and-map> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a2c9-348">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>
