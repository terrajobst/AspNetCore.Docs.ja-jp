---
title: ASP.NET Core でホステッド サービスを使用するバックグラウンド タスク
author: guardrex
description: ASP.NET Core でホステッド サービスを使用するバックグラウンド タスクの実装方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c1fbb5ae8ffc4ee506f42df6a4cbbe845b2b903d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333655"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="1f8df-103">ASP.NET Core でホステッド サービスを使用するバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="1f8df-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="1f8df-104">著者: [Luke Latham](https://github.com/guardrex)、[Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="1f8df-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1f8df-105">ASP.NET Core では、バックグラウンド タスクを*ホステッド サービス*として実装することができます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="1f8df-106">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="1f8df-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="1f8df-107">このトピックでは、3 つのホステッド サービスの例について説明します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="1f8df-108">タイマーで実行されるバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="1f8df-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="1f8df-109">[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)をアクティブ化するホステッド サービス。</span><span class="sxs-lookup"><span data-stu-id="1f8df-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1f8df-110">スコープ サービスは[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="1f8df-111">連続して実行される、キューに格納されたバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="1f8df-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="1f8df-112">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1f8df-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1f8df-113">サンプル アプリには、2 つのバージョンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="1f8df-114">Web ホスト &ndash; Web ホストは、Web アプリをホストするのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="1f8df-115">このトピックで示すコード例は、サンプルの Web ホスト バージョンからのものです。</span><span class="sxs-lookup"><span data-stu-id="1f8df-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="1f8df-116">詳細については、[Web ホスト](xref:fundamentals/host/web-host)のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1f8df-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="1f8df-117">汎用ホスト &ndash; 汎用ホストは ASP.NET Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="1f8df-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="1f8df-118">詳細については、[汎用ホスト](xref:fundamentals/host/generic-host)のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1f8df-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="1f8df-119">ワーカー サービス テンプレート</span><span class="sxs-lookup"><span data-stu-id="1f8df-119">Worker Service template</span></span>

<span data-ttu-id="1f8df-120">ASP.NET Core ワーカー サービス テンプレートは、実行時間が長いサービス アプリを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="1f8df-121">ホステッド サービス アプリの基礎としてテンプレートを使用するには:</span><span class="sxs-lookup"><span data-stu-id="1f8df-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a><span data-ttu-id="1f8df-122">Package</span><span class="sxs-lookup"><span data-stu-id="1f8df-122">Package</span></span>

<span data-ttu-id="1f8df-123">[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージへのパッケージ参照が、ASP.NET Core アプリに対して暗黙に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-123">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="1f8df-124">IHostedService インターフェイス</span><span class="sxs-lookup"><span data-stu-id="1f8df-124">IHostedService interface</span></span>

<span data-ttu-id="1f8df-125"><xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスは、ホストによって管理されるオブジェクトの 2 つのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-125">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="1f8df-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` には、バックグラウンド タスクを開始するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="1f8df-127">`StartAsync` は、以下よりも "*前に*" 呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-127">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="1f8df-128">アプリの要求処理パイプラインが構成される (`Startup.Configure`)。</span><span class="sxs-lookup"><span data-stu-id="1f8df-128">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="1f8df-129">サーバーが起動して、[IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) がトリガーされる。</span><span class="sxs-lookup"><span data-stu-id="1f8df-129">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="1f8df-130">既定の動作を変更して、アプリのパイプラインが構成されて `ApplicationStarted` が呼び出された後で、ホステッド サービスの `StartAsync` が実行するようにできます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-130">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="1f8df-131">既定の動作を変更するには、`ConfigureWebHostDefaults` を呼び出した後でホステッド サービス (次の例では `VideosWatcher`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-131">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="1f8df-132">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; ホストが正常なシャットダウンを実行しているときにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="1f8df-133">`StopAsync` には、バックグラウンド タスクを終了するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-133">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="1f8df-134">アンマネージ リソースを破棄するには、<xref:System.IDisposable> と[ファイナライザー (デストラクター)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) を実装します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-134">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="1f8df-135">キャンセル トークンには、シャットダウン プロセスが正常に行われないことを示す、既定の 5 秒間のタイムアウトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-135">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="1f8df-136">キャンセルがトークンに要求された場合:</span><span class="sxs-lookup"><span data-stu-id="1f8df-136">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="1f8df-137">アプリで実行されている残りのバックグラウンド操作が中止します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-137">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="1f8df-138">`StopAsync` で呼び出されたすべてのメソッドが速やかに戻ります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-138">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="1f8df-139">ただし、キャンセルが要求された後もタスクは破棄されません&mdash;呼び出し元がすべてのタスクの完了を待機します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-139">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="1f8df-140">アプリが予期せずシャットダウンした場合 (たとえば、アプリのプロセスが失敗した場合)、`StopAsync` は呼び出されないことがあります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-140">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="1f8df-141">そのため、`StopAsync` で呼び出されたメソッドや行われた操作が実行されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-141">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="1f8df-142">既定の 5 秒のシャットダウン タイムアウトを延長するには、次を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-142">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="1f8df-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (汎用ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="1f8df-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="1f8df-144">詳細については、<xref:fundamentals/host/generic-host#shutdown-timeout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f8df-144">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="1f8df-145">シャットダウン タイムアウトのホスト構成設定 (Web ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="1f8df-145">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="1f8df-146">詳細については、<xref:fundamentals/host/web-host#shutdown-timeout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f8df-146">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="1f8df-147">ホステッド サービスは、アプリの起動時に一度アクティブ化され、アプリのシャットダウン時に正常にシャットダウンされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-147">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="1f8df-148">バックグラウンド タスクの実行中にエラーがスローされた場合、`StopAsync` が呼び出されていなくても `Dispose` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-148">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="1f8df-149">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="1f8df-149">BackgroundService</span></span>

<span data-ttu-id="1f8df-150">`BackgroundService` は、長期 <xref:Microsoft.Extensions.Hosting.IHostedService> を実装するための基底クラスです。</span><span class="sxs-lookup"><span data-stu-id="1f8df-150">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="1f8df-151">`BackgroundService` により、サービスのロジックを格納するための `ExecuteAsync(CancellationToken stoppingToken)` 抽象メソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-151">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="1f8df-152">[IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) が呼び出されると、`stoppingToken` がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-152">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="1f8df-153">このメソッドの実装では、バックグラウンド サービスの有効期間全体を表す `Task` が返されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-153">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="1f8df-154">また、*必要に応じて*、サービスのスタートアップ コードとシャットダウン コードを実行するために、`IHostedService` で定義されているメソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="1f8df-154">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="1f8df-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` は、アプリケーション ホストが正常なシャットダウンを実行しているときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="1f8df-156">`cancellationToken` は、ホストによって強制的にサービスを強制終了することが決定されたときに通知されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-156">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="1f8df-157">このメソッドがオーバーライドされた場合、サービスが正常にシャットダウンされるように、基本クラス メソッドを呼び出す (および `await` する) **必要があります**。</span><span class="sxs-lookup"><span data-stu-id="1f8df-157">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="1f8df-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` は、バックグラウンド サービスを開始するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="1f8df-159">`cancellationToken` は、スタートアップ プロセスが中断された場合に通知されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-159">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="1f8df-160">この実装では、サービスのスタートアップ プロセスを表す `Task` が返されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-160">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="1f8df-161">この `Task` が完了するまで、これ以上のサービスは開始されません。</span><span class="sxs-lookup"><span data-stu-id="1f8df-161">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="1f8df-162">このメソッドがオーバーライドされた場合、サービスが正常に開始されるように、基本クラス メソッドを呼び出す (および `await` する) **必要があります**。</span><span class="sxs-lookup"><span data-stu-id="1f8df-162">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="1f8df-163">時間が指定されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="1f8df-163">Timed background tasks</span></span>

<span data-ttu-id="1f8df-164">時間が指定されたバックグラウンド タスクは、[System.Threading.Timer](xref:System.Threading.Timer) クラスを利用します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="1f8df-165">このタイマーはタスクの `DoWork` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="1f8df-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="1f8df-166">タイマーは `StopAsync` で無効になり、`Dispose` でサービス コンテナーが破棄されたときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="1f8df-167">サービスは、`AddHostedService` 拡張メソッドを使用して `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-167">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="1f8df-168">バックグラウンド タスクでスコープ サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="1f8df-168">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="1f8df-169">`BackgroundService` 内で[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)を使用するには、スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-169">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="1f8df-170">既定では、ホステッド サービスのスコープは作成されません。</span><span class="sxs-lookup"><span data-stu-id="1f8df-170">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="1f8df-171">バックグラウンド タスクのスコープ サービスには、バックグラウンド タスクのロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-171">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="1f8df-172">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-172">In the following example:</span></span>

* <span data-ttu-id="1f8df-173">サービスは非同期です。</span><span class="sxs-lookup"><span data-stu-id="1f8df-173">The service is asynchronous.</span></span> <span data-ttu-id="1f8df-174">`DoWork` メソッドは `Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-174">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="1f8df-175">デモンストレーションのために、10 秒の遅延が `DoWork` メソッドで待機されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-175">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="1f8df-176"><xref:Microsoft.Extensions.Logging.ILogger> がサービスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-176">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="1f8df-177">ホステッド サービスはスコープを作成してバックグラウンド タスクのスコープ サービスを解決し、`DoWork` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-177">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="1f8df-178">`ExecuteAsync` で待機していた `DoWork` が `Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-178">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="1f8df-179">サービスは `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-179">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="1f8df-180">ホステッド サービスは、`AddHostedService` 拡張メソッドを使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-180">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="1f8df-181">キューに格納されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="1f8df-181">Queued background tasks</span></span>

<span data-ttu-id="1f8df-182">バックグラウンド タスク キューは、.NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([ASP.NET Core 用に暫定的に組み込まれる予定](https://github.com/aspnet/Hosting/issues/1280)) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-182">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="1f8df-183">次の `QueueHostedService` の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-183">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="1f8df-184">`ExecuteAsync` で待機していた `BackgroundProcessing` メソッドが `Task`を返します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-184">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="1f8df-185">`BackgroundProcessing` で、キュー内のバックグラウンド タスクがデキューされ、実行されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-185">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="1f8df-186">作業項目が待機されてから、`StopAsync` でサービスが停止します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-186">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="1f8df-187">`MonitorLoop` サービスは、`w` キーが入力デバイスで選択されると常に、ホステッド サービスのためにタスクのエンキューを処理します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-187">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="1f8df-188">`IBackgroundTaskQueue` が `MonitorLoop` サービスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-188">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="1f8df-189">`IBackgroundTaskQueue.QueueBackgroundWorkItem` が呼び出され、作業項目がエンキューされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-189">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="1f8df-190">作業項目により、実行時間の長いバックグラウンド タスクがシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-190">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="1f8df-191">5 秒間の遅延が 3 回実行されます (`Task.Delay`)。</span><span class="sxs-lookup"><span data-stu-id="1f8df-191">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="1f8df-192">タスクが取り消された場合、`try-catch` ステートメントによって <xref:System.OperationCanceledException> がトラップされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-192">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="1f8df-193">サービスは `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-193">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="1f8df-194">ホステッド サービスは、`AddHostedService` 拡張メソッドを使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-194">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f8df-195">ASP.NET Core では、バックグラウンド タスクを*ホステッド サービス*として実装することができます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-195">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="1f8df-196">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="1f8df-196">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="1f8df-197">このトピックでは、3 つのホステッド サービスの例について説明します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-197">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="1f8df-198">タイマーで実行されるバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="1f8df-198">Background task that runs on a timer.</span></span>
* <span data-ttu-id="1f8df-199">[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)をアクティブ化するホステッド サービス。</span><span class="sxs-lookup"><span data-stu-id="1f8df-199">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1f8df-200">スコープ サービスは[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用できます</span><span class="sxs-lookup"><span data-stu-id="1f8df-200">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="1f8df-201">連続して実行される、キューに格納されたバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="1f8df-201">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="1f8df-202">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1f8df-202">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1f8df-203">サンプル アプリには、2 つのバージョンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-203">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="1f8df-204">Web ホスト &ndash; Web ホストは、Web アプリをホストするのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-204">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="1f8df-205">このトピックで示すコード例は、サンプルの Web ホスト バージョンからのものです。</span><span class="sxs-lookup"><span data-stu-id="1f8df-205">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="1f8df-206">詳細については、[Web ホスト](xref:fundamentals/host/web-host)のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1f8df-206">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="1f8df-207">汎用ホスト &ndash; 汎用ホストは ASP.NET Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="1f8df-207">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="1f8df-208">詳細については、[汎用ホスト](xref:fundamentals/host/generic-host)のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1f8df-208">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="1f8df-209">Package</span><span class="sxs-lookup"><span data-stu-id="1f8df-209">Package</span></span>

<span data-ttu-id="1f8df-210">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-210">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="1f8df-211">IHostedService インターフェイス</span><span class="sxs-lookup"><span data-stu-id="1f8df-211">IHostedService interface</span></span>

<span data-ttu-id="1f8df-212">ホステッド サービスでは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-212">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="1f8df-213">このインターフェイスは、ホストによって管理されるオブジェクトの 2 つのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-213">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="1f8df-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` には、バックグラウンド タスクを開始するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="1f8df-215">[Web ホスト](xref:fundamentals/host/web-host) を使用する場合は、サーバーが起動し、[IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) がトリガーされた後で、`StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-215">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="1f8df-216">[汎用ホスト](xref:fundamentals/host/generic-host) を使用する場合は、`ApplicationStarted` がトリガーされる前に `StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-216">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="1f8df-217">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; ホストが正常なシャットダウンを実行しているときにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-217">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="1f8df-218">`StopAsync` には、バックグラウンド タスクを終了するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-218">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="1f8df-219">アンマネージ リソースを破棄するには、<xref:System.IDisposable> と[ファイナライザー (デストラクター)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) を実装します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-219">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="1f8df-220">キャンセル トークンには、シャットダウン プロセスが正常に行われないことを示す、既定の 5 秒間のタイムアウトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-220">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="1f8df-221">キャンセルがトークンに要求された場合:</span><span class="sxs-lookup"><span data-stu-id="1f8df-221">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="1f8df-222">アプリで実行されている残りのバックグラウンド操作が中止します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-222">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="1f8df-223">`StopAsync` で呼び出されたすべてのメソッドが速やかに戻ります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-223">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="1f8df-224">ただし、キャンセルが要求された後もタスクは破棄されません&mdash;呼び出し元がすべてのタスクの完了を待機します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-224">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="1f8df-225">アプリが予期せずシャットダウンした場合 (たとえば、アプリのプロセスが失敗した場合)、`StopAsync` は呼び出されないことがあります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-225">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="1f8df-226">そのため、`StopAsync` で呼び出されたメソッドや行われた操作が実行されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-226">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="1f8df-227">既定の 5 秒のシャットダウン タイムアウトを延長するには、次を設定します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-227">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="1f8df-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (汎用ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="1f8df-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="1f8df-229">詳細については、<xref:fundamentals/host/generic-host#shutdown-timeout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f8df-229">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="1f8df-230">シャットダウン タイムアウトのホスト構成設定 (Web ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="1f8df-230">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="1f8df-231">詳細については、<xref:fundamentals/host/web-host#shutdown-timeout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f8df-231">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="1f8df-232">ホステッド サービスは、アプリの起動時に一度アクティブ化され、アプリのシャットダウン時に正常にシャットダウンされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-232">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="1f8df-233">バックグラウンド タスクの実行中にエラーがスローされた場合、`StopAsync` が呼び出されていなくても `Dispose` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f8df-233">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="1f8df-234">時間が指定されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="1f8df-234">Timed background tasks</span></span>

<span data-ttu-id="1f8df-235">時間が指定されたバックグラウンド タスクは、[System.Threading.Timer](xref:System.Threading.Timer) クラスを利用します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-235">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="1f8df-236">このタイマーはタスクの `DoWork` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="1f8df-236">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="1f8df-237">タイマーは `StopAsync` で無効になり、`Dispose` でサービス コンテナーが破棄されたときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-237">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="1f8df-238">サービスは、`AddHostedService` 拡張メソッドを使用して `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-238">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="1f8df-239">バックグラウンド タスクでスコープ サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="1f8df-239">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="1f8df-240">`IHostedService` 内で[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)を使用するには、スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-240">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="1f8df-241">既定では、ホステッド サービスのスコープは作成されません。</span><span class="sxs-lookup"><span data-stu-id="1f8df-241">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="1f8df-242">バックグラウンド タスクのスコープ サービスには、バックグラウンド タスクのロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-242">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="1f8df-243">次の例では、<xref:Microsoft.Extensions.Logging.ILogger> がサービスに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-243">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="1f8df-244">ホステッド サービスはスコープを作成してバックグラウンド タスクのスコープ サービスを解決し、`DoWork` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1f8df-244">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="1f8df-245">サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-245">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1f8df-246">`IHostedService` の実装は、`AddHostedService` 拡張メソッドで登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-246">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="1f8df-247">キューに格納されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="1f8df-247">Queued background tasks</span></span>

<span data-ttu-id="1f8df-248">バックグラウンド タスク キューは、.NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([暫定的に ASP.NET Core に組み込まれる予定](https://github.com/aspnet/Hosting/issues/1280)) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-248">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="1f8df-249">`QueueHostedService` では、キュー内のバックグラウンド タスクは、<xref:Microsoft.Extensions.Hosting.BackgroundService> としてデキューされ、実行されます。これは、長時間実行する `IHostedService` を構成するための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="1f8df-249">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="1f8df-250">サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="1f8df-250">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1f8df-251">`IHostedService` の実装は、`AddHostedService` 拡張メソッドで登録されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-251">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="1f8df-252">インデックス ページ モデル クラスで:</span><span class="sxs-lookup"><span data-stu-id="1f8df-252">In the Index page model class:</span></span>

* <span data-ttu-id="1f8df-253">`IBackgroundTaskQueue` がコンストラクターに挿入され、`Queue` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-253">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="1f8df-254"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> が挿入され、`_serviceScopeFactory` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-254">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="1f8df-255">ファクトリは、スコープ内でサービス作成するための <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> のインスタンス作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-255">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="1f8df-256">スコープは、アプリの `AppDbContext` ([スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)) を使用し、データベース レコードを `IBackgroundTaskQueue` (シングルトン サービス) に書き込むために作成されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-256">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1f8df-257">[インデックス] ページで **[タスクの追加]** ボタンを選択すると、`OnPostAddTask` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-257">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="1f8df-258">`QueueBackgroundWorkItem` が呼び出され、作業項目がエンキューされます。</span><span class="sxs-lookup"><span data-stu-id="1f8df-258">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1f8df-259">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1f8df-259">Additional resources</span></span>

* [<span data-ttu-id="1f8df-260">IHostedService と BackgroundService クラスを使ってマイクロサービスのバックグラウンド タスクを実装する</span><span class="sxs-lookup"><span data-stu-id="1f8df-260">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
