---
title: ASP.NET Core でホステッド サービスを使用するバックグラウンド タスク
author: guardrex
description: ASP.NET Core でホステッド サービスを使用するバックグラウンド タスクの実装方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 6a88e56afc4fb1b4f673c362f83d948eda84b930
ms.sourcegitcommit: bd896935e91236e03241f75e6534ad6debcecbbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77044881"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="b148a-103">ASP.NET Core でホステッド サービスを使用するバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="b148a-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="b148a-104">著者: [Luke Latham](https://github.com/guardrex)、[Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="b148a-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b148a-105">ASP.NET Core では、バックグラウンド タスクを*ホステッド サービス*として実装することができます。</span><span class="sxs-lookup"><span data-stu-id="b148a-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="b148a-106">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="b148a-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="b148a-107">このトピックでは、3 つのホステッド サービスの例について説明します。</span><span class="sxs-lookup"><span data-stu-id="b148a-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="b148a-108">タイマーで実行されるバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="b148a-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="b148a-109">[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)をアクティブ化するホステッド サービス。</span><span class="sxs-lookup"><span data-stu-id="b148a-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b148a-110">スコープ サービスは[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b148a-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="b148a-111">連続して実行される、キューに格納されたバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="b148a-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="b148a-112">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b148a-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="b148a-113">ワーカー サービス テンプレート</span><span class="sxs-lookup"><span data-stu-id="b148a-113">Worker Service template</span></span>

<span data-ttu-id="b148a-114">ASP.NET Core ワーカー サービス テンプレートは、実行時間が長いサービス アプリを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="b148a-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="b148a-115">ワーカー サービス テンプレートから作成されたアプリで、そのプロジェクト ファイル内のワーカー SDK が指定されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="b148a-116">ホステッド サービス アプリの基礎としてテンプレートを使用するには:</span><span class="sxs-lookup"><span data-stu-id="b148a-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="b148a-117">Package</span><span class="sxs-lookup"><span data-stu-id="b148a-117">Package</span></span>

<span data-ttu-id="b148a-118">ワーカー サービス テンプレートに基づくアプリは `Microsoft.NET.Sdk.Worker` SDK を使用し、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージへの明示的なパッケージ参照を含んでいます。</span><span class="sxs-lookup"><span data-stu-id="b148a-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="b148a-119">たとえば、サンプル アプリのプロジェクト ファイル (*BackgroundTasksSample.csproj*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b148a-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="b148a-120">`Microsoft.NET.Sdk.Web` SDK を使用する Web アプリの場合、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージは共有フレームワークから暗黙的に参照されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="b148a-121">アプリのプロジェクト ファイル内の明示的なパッケージ参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b148a-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="b148a-122">IHostedService インターフェイス</span><span class="sxs-lookup"><span data-stu-id="b148a-122">IHostedService interface</span></span>

<span data-ttu-id="b148a-123"><xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスは、ホストによって管理されるオブジェクトの 2 つのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="b148a-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="b148a-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` には、バックグラウンド タスクを開始するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b148a-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="b148a-125">`StartAsync` は、以下よりも "*前に*" 呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="b148a-126">アプリの要求処理パイプラインが構成される (`Startup.Configure`)。</span><span class="sxs-lookup"><span data-stu-id="b148a-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="b148a-127">サーバーが起動して、[IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) がトリガーされる。</span><span class="sxs-lookup"><span data-stu-id="b148a-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="b148a-128">既定の動作を変更して、アプリのパイプラインが構成されて `ApplicationStarted` が呼び出された後で、ホステッド サービスの `StartAsync` が実行するようにできます。</span><span class="sxs-lookup"><span data-stu-id="b148a-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="b148a-129">既定の動作を変更するには、`ConfigureWebHostDefaults` を呼び出した後でホステッド サービス (次の例では `VideosWatcher`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="b148a-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="b148a-130">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; ホストが正常なシャットダウンを実行しているときにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="b148a-131">`StopAsync` には、バックグラウンド タスクを終了するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b148a-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="b148a-132">アンマネージ リソースを破棄するには、<xref:System.IDisposable> と[ファイナライザー (デストラクター)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) を実装します。</span><span class="sxs-lookup"><span data-stu-id="b148a-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="b148a-133">キャンセル トークンには、シャットダウン プロセスが正常に行われないことを示す、既定の 5 秒間のタイムアウトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b148a-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="b148a-134">キャンセルがトークンに要求された場合:</span><span class="sxs-lookup"><span data-stu-id="b148a-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="b148a-135">アプリで実行されている残りのバックグラウンド操作が中止します。</span><span class="sxs-lookup"><span data-stu-id="b148a-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="b148a-136">`StopAsync` で呼び出されたすべてのメソッドが速やかに戻ります。</span><span class="sxs-lookup"><span data-stu-id="b148a-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="b148a-137">ただし、キャンセルが要求された後もタスクは破棄されません&mdash;呼び出し元がすべてのタスクの完了を待機します。</span><span class="sxs-lookup"><span data-stu-id="b148a-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="b148a-138">アプリが予期せずシャットダウンした場合 (たとえば、アプリのプロセスが失敗した場合)、`StopAsync` は呼び出されないことがあります。</span><span class="sxs-lookup"><span data-stu-id="b148a-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="b148a-139">そのため、`StopAsync` で呼び出されたメソッドや行われた操作が実行されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b148a-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="b148a-140">既定の 5 秒のシャットダウン タイムアウトを延長するには、次を設定します。</span><span class="sxs-lookup"><span data-stu-id="b148a-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="b148a-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (汎用ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="b148a-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="b148a-142">詳細については、「<xref:fundamentals/host/generic-host#shutdown-timeout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b148a-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="b148a-143">シャットダウン タイムアウトのホスト構成設定 (Web ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="b148a-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="b148a-144">詳細については、「<xref:fundamentals/host/web-host#shutdown-timeout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b148a-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="b148a-145">ホステッド サービスは、アプリの起動時に一度アクティブ化され、アプリのシャットダウン時に正常にシャットダウンされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="b148a-146">バックグラウンド タスクの実行中にエラーがスローされた場合、`StopAsync` が呼び出されていなくても `Dispose` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="b148a-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="b148a-147">BackgroundService 基底クラス</span><span class="sxs-lookup"><span data-stu-id="b148a-147">BackgroundService base class</span></span>

<span data-ttu-id="b148a-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> は、長期 <xref:Microsoft.Extensions.Hosting.IHostedService> を実装するための基底クラスです。</span><span class="sxs-lookup"><span data-stu-id="b148a-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="b148a-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) は、バックグラウンド サービスを実行するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="b148a-150">この実装では、バックグラウンド サービスの有効期間全体を表す <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="b148a-151">`await` を呼び出すなどして [ExecuteAsync が非同期になる](https://github.com/dotnet/extensions/issues/2149)まで、以降のサービスは開始されません。</span><span class="sxs-lookup"><span data-stu-id="b148a-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="b148a-152">`ExecuteAsync` では、長時間の初期化作業を実行しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="b148a-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="b148a-153">ホストは [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) でブロックされ、`ExecuteAsync` の完了を待機します。</span><span class="sxs-lookup"><span data-stu-id="b148a-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="b148a-154">[IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) が呼び出されると、キャンセル トークンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="b148a-155">サービスを正常にシャットダウンするためのキャンセル トークンが起動すると、`ExecuteAsync` の実装はすぐに終了します。</span><span class="sxs-lookup"><span data-stu-id="b148a-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="b148a-156">それ以外の場合は、シャットダウンのタイムアウト時にサービスが強制的にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="b148a-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="b148a-157">詳細については、「[IHostedService インターフェイス](#ihostedservice-interface)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b148a-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="b148a-158">時間が指定されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="b148a-158">Timed background tasks</span></span>

<span data-ttu-id="b148a-159">時間が指定されたバックグラウンド タスクは、[System.Threading.Timer](xref:System.Threading.Timer) クラスを利用します。</span><span class="sxs-lookup"><span data-stu-id="b148a-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="b148a-160">このタイマーはタスクの `DoWork` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="b148a-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="b148a-161">タイマーは `StopAsync` で無効になり、`Dispose` でサービス コンテナーが破棄されたときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="b148a-162">前の `DoWork` の実行が完了するまで <xref:System.Threading.Timer> は待機されないため、ここで示したアプローチはすべてのシナリオに適しているとは限りません。</span><span class="sxs-lookup"><span data-stu-id="b148a-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="b148a-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) は、アトミック操作として実行カウンターをインクリメントするために使用されされます。これにより、複数のスレッドによって `executionCount` が同時に更新されなくなります。</span><span class="sxs-lookup"><span data-stu-id="b148a-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="b148a-164">サービスは、`AddHostedService` 拡張メソッドを使用して `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="b148a-165">バックグラウンド タスクでスコープ サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="b148a-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="b148a-166">[BackgroundService](#backgroundservice-base-class) 内で[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)を使用するには、スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="b148a-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="b148a-167">既定では、ホステッド サービスのスコープは作成されません。</span><span class="sxs-lookup"><span data-stu-id="b148a-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="b148a-168">バックグラウンド タスクのスコープ サービスには、バックグラウンド タスクのロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b148a-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="b148a-169">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b148a-169">In the following example:</span></span>

* <span data-ttu-id="b148a-170">サービスは非同期です。</span><span class="sxs-lookup"><span data-stu-id="b148a-170">The service is asynchronous.</span></span> <span data-ttu-id="b148a-171">`DoWork` メソッドは `Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="b148a-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="b148a-172">デモンストレーションのために、10 秒の遅延が `DoWork` メソッドで待機されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="b148a-173"><xref:Microsoft.Extensions.Logging.ILogger> がサービスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="b148a-174">ホステッド サービスはスコープを作成してバックグラウンド タスクのスコープ サービスを解決し、`DoWork` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b148a-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="b148a-175">`ExecuteAsync` で待機していた `DoWork` が `Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="b148a-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="b148a-176">サービスは `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されています。</span><span class="sxs-lookup"><span data-stu-id="b148a-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="b148a-177">ホステッド サービスは、`AddHostedService` 拡張メソッドを使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="b148a-178">キューに格納されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="b148a-178">Queued background tasks</span></span>

<span data-ttu-id="b148a-179">バックグラウンド タスク キューは、.NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([ASP.NET Core 用に暫定的に組み込まれる予定](https://github.com/aspnet/Hosting/issues/1280)) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="b148a-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="b148a-180">次の `QueueHostedService` の例では以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="b148a-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="b148a-181">`ExecuteAsync` で待機していた `BackgroundProcessing` メソッドが `Task`を返します。</span><span class="sxs-lookup"><span data-stu-id="b148a-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="b148a-182">`BackgroundProcessing` で、キュー内のバックグラウンド タスクがデキューされ、実行されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="b148a-183">作業項目が待機されてから、`StopAsync` でサービスが停止します。</span><span class="sxs-lookup"><span data-stu-id="b148a-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="b148a-184">`MonitorLoop` サービスは、`w` キーが入力デバイスで選択されると常に、ホステッド サービスのためにタスクのエンキューを処理します。</span><span class="sxs-lookup"><span data-stu-id="b148a-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="b148a-185">`IBackgroundTaskQueue` が `MonitorLoop` サービスに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="b148a-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` が呼び出され、作業項目がエンキューされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="b148a-187">作業項目により、実行時間の長いバックグラウンド タスクがシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="b148a-188">5 秒間の遅延が 3 回実行されます (`Task.Delay`)。</span><span class="sxs-lookup"><span data-stu-id="b148a-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="b148a-189">タスクが取り消された場合、`try-catch` ステートメントによって <xref:System.OperationCanceledException> がトラップされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="b148a-190">サービスは `IHostBuilder.ConfigureServices` (*Program.cs*) に登録されています。</span><span class="sxs-lookup"><span data-stu-id="b148a-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="b148a-191">ホステッド サービスは、`AddHostedService` 拡張メソッドを使用して登録されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b148a-192">ASP.NET Core では、バックグラウンド タスクを*ホステッド サービス*として実装することができます。</span><span class="sxs-lookup"><span data-stu-id="b148a-192">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="b148a-193">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="b148a-193">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="b148a-194">このトピックでは、3 つのホステッド サービスの例について説明します。</span><span class="sxs-lookup"><span data-stu-id="b148a-194">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="b148a-195">タイマーで実行されるバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="b148a-195">Background task that runs on a timer.</span></span>
* <span data-ttu-id="b148a-196">[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)をアクティブ化するホステッド サービス。</span><span class="sxs-lookup"><span data-stu-id="b148a-196">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b148a-197">スコープ サービスは[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を使用できます</span><span class="sxs-lookup"><span data-stu-id="b148a-197">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="b148a-198">連続して実行される、キューに格納されたバックグラウンド タスク。</span><span class="sxs-lookup"><span data-stu-id="b148a-198">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="b148a-199">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b148a-199">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="b148a-200">Package</span><span class="sxs-lookup"><span data-stu-id="b148a-200">Package</span></span>

<span data-ttu-id="b148a-201">[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="b148a-201">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="b148a-202">IHostedService インターフェイス</span><span class="sxs-lookup"><span data-stu-id="b148a-202">IHostedService interface</span></span>

<span data-ttu-id="b148a-203">ホステッド サービスでは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="b148a-203">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="b148a-204">このインターフェイスは、ホストによって管理されるオブジェクトの 2 つのメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="b148a-204">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="b148a-205">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` には、バックグラウンド タスクを開始するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b148a-205">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="b148a-206">[Web ホスト](xref:fundamentals/host/web-host) を使用する場合は、サーバーが起動し、[IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) がトリガーされた後で、`StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-206">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="b148a-207">[汎用ホスト](xref:fundamentals/host/generic-host) を使用する場合は、`ApplicationStarted` がトリガーされる前に `StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-207">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="b148a-208">[StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; ホストが正常なシャットダウンを実行しているときにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-208">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="b148a-209">`StopAsync` には、バックグラウンド タスクを終了するロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b148a-209">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="b148a-210">アンマネージ リソースを破棄するには、<xref:System.IDisposable> と[ファイナライザー (デストラクター)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) を実装します。</span><span class="sxs-lookup"><span data-stu-id="b148a-210">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="b148a-211">キャンセル トークンには、シャットダウン プロセスが正常に行われないことを示す、既定の 5 秒間のタイムアウトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b148a-211">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="b148a-212">キャンセルがトークンに要求された場合:</span><span class="sxs-lookup"><span data-stu-id="b148a-212">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="b148a-213">アプリで実行されている残りのバックグラウンド操作が中止します。</span><span class="sxs-lookup"><span data-stu-id="b148a-213">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="b148a-214">`StopAsync` で呼び出されたすべてのメソッドが速やかに戻ります。</span><span class="sxs-lookup"><span data-stu-id="b148a-214">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="b148a-215">ただし、キャンセルが要求された後もタスクは破棄されません&mdash;呼び出し元がすべてのタスクの完了を待機します。</span><span class="sxs-lookup"><span data-stu-id="b148a-215">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="b148a-216">アプリが予期せずシャットダウンした場合 (たとえば、アプリのプロセスが失敗した場合)、`StopAsync` は呼び出されないことがあります。</span><span class="sxs-lookup"><span data-stu-id="b148a-216">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="b148a-217">そのため、`StopAsync` で呼び出されたメソッドや行われた操作が実行されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b148a-217">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="b148a-218">既定の 5 秒のシャットダウン タイムアウトを延長するには、次を設定します。</span><span class="sxs-lookup"><span data-stu-id="b148a-218">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="b148a-219"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> (汎用ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="b148a-219"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="b148a-220">詳細については、「<xref:fundamentals/host/generic-host#shutdown-timeout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b148a-220">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="b148a-221">シャットダウン タイムアウトのホスト構成設定 (Web ホストを使用するとき)</span><span class="sxs-lookup"><span data-stu-id="b148a-221">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="b148a-222">詳細については、「<xref:fundamentals/host/web-host#shutdown-timeout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b148a-222">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="b148a-223">ホステッド サービスは、アプリの起動時に一度アクティブ化され、アプリのシャットダウン時に正常にシャットダウンされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-223">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="b148a-224">バックグラウンド タスクの実行中にエラーがスローされた場合、`StopAsync` が呼び出されていなくても `Dispose` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="b148a-224">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="b148a-225">時間が指定されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="b148a-225">Timed background tasks</span></span>

<span data-ttu-id="b148a-226">時間が指定されたバックグラウンド タスクは、[System.Threading.Timer](xref:System.Threading.Timer) クラスを利用します。</span><span class="sxs-lookup"><span data-stu-id="b148a-226">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="b148a-227">このタイマーはタスクの `DoWork` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="b148a-227">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="b148a-228">タイマーは `StopAsync` で無効になり、`Dispose` でサービス コンテナーが破棄されたときに破棄されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-228">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="b148a-229">前の `DoWork` の実行が完了するまで <xref:System.Threading.Timer> は待機されないため、ここで示したアプローチはすべてのシナリオに適しているとは限りません。</span><span class="sxs-lookup"><span data-stu-id="b148a-229">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="b148a-230">サービスは、`AddHostedService` 拡張メソッドを使用して `Startup.ConfigureServices` に登録されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-230">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="b148a-231">バックグラウンド タスクでスコープ サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="b148a-231">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="b148a-232">`IHostedService` 内で[スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)を使用するには、スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="b148a-232">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="b148a-233">既定では、ホステッド サービスのスコープは作成されません。</span><span class="sxs-lookup"><span data-stu-id="b148a-233">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="b148a-234">バックグラウンド タスクのスコープ サービスには、バックグラウンド タスクのロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b148a-234">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="b148a-235">次の例では、<xref:Microsoft.Extensions.Logging.ILogger> がサービスに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="b148a-235">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="b148a-236">ホステッド サービスはスコープを作成してバックグラウンド タスクのスコープ サービスを解決し、`DoWork` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b148a-236">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="b148a-237">サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="b148a-237">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b148a-238">`IHostedService` の実装は、`AddHostedService` 拡張メソッドで登録されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-238">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="b148a-239">キューに格納されたバックグラウンド タスク</span><span class="sxs-lookup"><span data-stu-id="b148a-239">Queued background tasks</span></span>

<span data-ttu-id="b148a-240">バックグラウンド タスク キューは、.NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([暫定的に ASP.NET Core に組み込まれる予定](https://github.com/aspnet/Hosting/issues/1280)) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="b148a-240">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="b148a-241">`QueueHostedService` では、キュー内のバックグラウンド タスクは [BackgroundService](#backgroundservice-base-class) としてデキューされ、実行されます。これは、長時間実行する `IHostedService` を構成するための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="b148a-241">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="b148a-242">サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="b148a-242">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b148a-243">`IHostedService` の実装は、`AddHostedService` 拡張メソッドで登録されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-243">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="b148a-244">インデックス ページ モデル クラスで:</span><span class="sxs-lookup"><span data-stu-id="b148a-244">In the Index page model class:</span></span>

* <span data-ttu-id="b148a-245">`IBackgroundTaskQueue` がコンストラクターに挿入され、`Queue` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="b148a-245">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="b148a-246"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> が挿入され、`_serviceScopeFactory` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="b148a-246">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="b148a-247">ファクトリは、スコープ内でサービス作成するための <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> のインスタンス作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-247">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="b148a-248">スコープは、アプリの `AppDbContext` ([スコープ サービス](xref:fundamentals/dependency-injection#service-lifetimes)) を使用し、データベース レコードを `IBackgroundTaskQueue` (シングルトン サービス) に書き込むために作成されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-248">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="b148a-249">[インデックス] ページで **[タスクの追加]** ボタンを選択すると、`OnPostAddTask` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="b148a-249">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="b148a-250">`QueueBackgroundWorkItem` が呼び出され、作業項目がエンキューされます。</span><span class="sxs-lookup"><span data-stu-id="b148a-250">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b148a-251">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b148a-251">Additional resources</span></span>

* [<span data-ttu-id="b148a-252">IHostedService と BackgroundService クラスを使ってマイクロサービスのバックグラウンド タスクを実装する</span><span class="sxs-lookup"><span data-stu-id="b148a-252">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="b148a-253">Azure App Service で WebJobs を使用してバックグラウンド タスクを実行する</span><span class="sxs-lookup"><span data-stu-id="b148a-253">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
