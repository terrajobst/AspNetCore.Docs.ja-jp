---
title: Windows サービスでの ASP.NET Core のホスト
author: guardrex
description: Windows サービスで ASP.NET Core アプリケーションをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 014585cd1e170fc94f7f577e11ec19824e54572f
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659842"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="1bfd6-103">Windows サービスでの ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="1bfd6-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="1bfd6-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1bfd6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1bfd6-105">ASP.NET Core アプリは、IIS を 使用せずに、[Windows サービス](/dotnet/framework/windows-services/introduction-to-windows-service-applications)として Windows にホストできます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1bfd6-106">Windows サービスとしてホストされている場合、サーバーの再起動後にアプリが自動的に起動します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1bfd6-107">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1bfd6-108">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1bfd6-108">Prerequisites</span></span>

* [<span data-ttu-id="1bfd6-109">ASP.NET Core SDK 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="1bfd6-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1bfd6-110">PowerShell 6.2 以降</span><span class="sxs-lookup"><span data-stu-id="1bfd6-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="1bfd6-111">ワーカー サービス テンプレート</span><span class="sxs-lookup"><span data-stu-id="1bfd6-111">Worker Service template</span></span>

<span data-ttu-id="1bfd6-112">ASP.NET Core ワーカー サービス テンプレートは、実行時間が長いサービス アプリを作成する場合の出発点として利用できます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="1bfd6-113">テンプレートを Windows サービス アプリの基礎として使用するには:</span><span class="sxs-lookup"><span data-stu-id="1bfd6-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="1bfd6-114">.NET Core テンプレートからワーカー サービス アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="1bfd6-115">「[アプリの構成](#app-configuration)」セクションのガイダンスに従って、Windows サービスとして実行できるようにワーカー サービス アプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="1bfd6-116">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="1bfd6-116">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bfd6-117">アプリには、[Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)のパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="1bfd6-118">ホストのビルド時に `IHostBuilder.UseWindowsService` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="1bfd6-119">アプリが Windows サービスとして実行している場合、メソッドは</span><span class="sxs-lookup"><span data-stu-id="1bfd6-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="1bfd6-120">ホストの有効期間を `WindowsServiceLifetime` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="1bfd6-121">[コンテンツ ルート](xref:fundamentals/index#content-root)を設定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-121">Sets the [content root](xref:fundamentals/index#content-root).</span></span>
* <span data-ttu-id="1bfd6-122">既定のソース名として、アプリケーション名によるイベント ログへの記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-122">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="1bfd6-123">*appsettings.Production.json* ファイルで `Logging:LogLevel:Default` キーを使用してログ レベルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-123">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="1bfd6-124">管理者のみが新しいイベント ソースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-124">Only administrators can create new event sources.</span></span> <span data-ttu-id="1bfd6-125">アプリケーション名を使用して、イベント ソースを作成できない場合、警告が*アプリケーション* ソースに記録され、イベント ログが無効になります。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-125">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="1bfd6-126">*Program.cs* の `CreateHostBuilder` の場合:</span><span class="sxs-lookup"><span data-stu-id="1bfd6-126">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="1bfd6-127">このトピックには、次のサンプル アプリが付属しています。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-127">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="1bfd6-128">バックグラウンド ワーカー サービスのサンプル &ndash; バックグラウンド タスク用に[ホステッド サービス](xref:fundamentals/host/hosted-services)を使用する[ワーカー サービステンプレート](#worker-service-template)に基づく、非 Web アプリのサンプルです。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-128">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="1bfd6-129">Web アプリ サービスのサンプル &ndash; バックグラウンド タスク用の[ホステッド サービス](xref:fundamentals/host/hosted-services)を使用する Windows サービスとして実行される、Razor Pages Web アプリのサンプルです。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-129">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="1bfd6-130">MVC のガイダンスについては、「<xref:mvc/overview>」と「<xref:migration/22-to-30>」にある記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-130">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bfd6-131">アプリでは、[Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) と [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) へのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-131">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="1bfd6-132">サービスの外部で実行しているときにテストとデバッグを行うには、アプリがサービスとして実行しているかコンソール アプリとして実行しているかを判別するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-132">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="1bfd6-133">デバッガーがアタッチされているか、`--console` スイッチが存在するかを検査します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-133">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="1bfd6-134">いずれかの条件が満たされる場合 (アプリがサービスとして実行していない場合)、<xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-134">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="1bfd6-135">条件が満たされない場合 (アプリがサービスとして実行している場合):</span><span class="sxs-lookup"><span data-stu-id="1bfd6-135">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="1bfd6-136"><xref:System.IO.Directory.SetCurrentDirectory*> を呼び出し、アプリの発行場所のパスを使用します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-136">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="1bfd6-137">パスを取得するために <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出さないでください。<xref:System.IO.Directory.GetCurrentDirectory*> が呼び出されると、Windows サービス アプリは *C:\\WINDOWS\\system32* フォルダーを戻すためです。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-137">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="1bfd6-138">詳しくは、「[現在のディレクトリとコンテンツのルート](#current-directory-and-content-root)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-138">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="1bfd6-139">`CreateWebHostBuilder` でアプリを構成する前に、この手順に従います。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-139">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="1bfd6-140"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> を呼び出して、アプリをサービスとして実行します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-140">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="1bfd6-141">[コマンドライン構成プロバイダー](xref:fundamentals/configuration/index#command-line-configuration-provider)では、コマンドライン引数に名前と値の組が必要であるため、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> が引数を受け取る前に `--console` スイッチは引数から削除されます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-141">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="1bfd6-142">Windows イベント ログに書き込むには、EventLog プロバイダーを <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> に追加します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-142">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="1bfd6-143">*appsettings.Production.json* ファイルで `Logging:LogLevel:Default` キーを使用してログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-143">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="1bfd6-144">サンプル アプリの次の例では、アプリ内で有効期間イベントを処理するために、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の代わりに `RunAsCustomService` を呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-144">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="1bfd6-145">詳しくは、「[イベントの開始と停止を扱う](#handle-starting-and-stopping-events)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-145">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="1bfd6-146">配置の種類</span><span class="sxs-lookup"><span data-stu-id="1bfd6-146">Deployment type</span></span>

<span data-ttu-id="1bfd6-147">展開のシナリオに関する情報および注意事項については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-147">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1bfd6-148">SDK</span><span class="sxs-lookup"><span data-stu-id="1bfd6-148">SDK</span></span>

<span data-ttu-id="1bfd6-149">Razor Pages または MVC フレームワークを使用する Web アプリベースのサービスでは、プロジェクト ファイルに Web SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-149">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1bfd6-150">サービスでバックグラウンド タスク ([ホステッド サービス](xref:fundamentals/host/hosted-services)など) のみを実行する場合は、プロジェクト ファイルにワーカー SDK を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-150">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1bfd6-151">フレームワーク依存型展開 (FDD)</span><span class="sxs-lookup"><span data-stu-id="1bfd6-151">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1bfd6-152">フレームワーク依存型展開 (FDD) は、ターゲット システムに .NET Core のシステム全体の共有バージョンが存在することに依存します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-152">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1bfd6-153">この記事のガイダンスに従って、FDD シナリオを採用すると、*フレームワーク依存型実行可能ファイル* と呼ばれる実行可能ファイル ( *.exe*) が SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-153">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bfd6-154">[Web SDK](#sdk) を使用している場合、*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-154">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1bfd6-155">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-155">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1bfd6-156">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) にはターゲット フレームワークが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-156">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="1bfd6-157">次の例では、RID が `win7-x64` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-157">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1bfd6-158">`<SelfContained>` プロパティが `false` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-158">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="1bfd6-159">これらのプロパティによって、Windows 用の実行可能ファイル ( *.exe*) と共有 .NET Core フレームワークに依存するアプリを生成するよう SDK に指示します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-159">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="1bfd6-160">*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-160">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1bfd6-161">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-161">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="1bfd6-162">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) にはターゲット フレームワークが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-162">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="1bfd6-163">次の例では、RID が `win7-x64` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-163">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1bfd6-164">`<SelfContained>` プロパティが `false` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-164">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="1bfd6-165">これらのプロパティによって、Windows 用の実行可能ファイル ( *.exe*) と共有 .NET Core フレームワークに依存するアプリを生成するよう SDK に指示します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-165">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="1bfd6-166">`<UseAppHost>` プロパティが `true` に設定されている。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-166">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="1bfd6-167">このプロパティによって、サービスに FDD のアクティブ化パス (実行可能ファイル、 *.exe*) を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-167">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="1bfd6-168">*web.config* ファイル (通常 ASP.NET Core アプリを発行する際に生成されます) は、Windows サービス アプリに対しては必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-168">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1bfd6-169">*web.config* ファイルの作成を無効にするには、`true` に設定した `<IsTransformWebConfigDisabled>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-169">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1bfd6-170">自己完結型の展開 (SCD)</span><span class="sxs-lookup"><span data-stu-id="1bfd6-170">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1bfd6-171">自己完結型の展開 (SCD) は、ホスト システムに共有フレームワークが存在することに依存しません。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-171">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1bfd6-172">ランタイムとアプリの依存関係が、アプリと共に展開されます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-172">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1bfd6-173">Windows [ランタイム識別子 (RID)](/dotnet/core/rid-catalog) は、ターゲット フレームワークを格納する `<PropertyGroup>` に含まれます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-173">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1bfd6-174">複数の RID を発行するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-174">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1bfd6-175">セミコロンで区切られたリストの形式で RID を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-175">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1bfd6-176">プロパティ名 [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (複数) を使用します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-176">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1bfd6-177">詳細については、「[.NET Core の RID カタログ](/dotnet/core/rid-catalog)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-177">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bfd6-178">`<SelfContained>` プロパティが `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-178">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="1bfd6-179">サービス ユーザー アカウント</span><span class="sxs-lookup"><span data-stu-id="1bfd6-179">Service user account</span></span>

<span data-ttu-id="1bfd6-180">サービス用のユーザー アカウントを作成するには、PowerShell 6 の管理コマンド シェルから [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-180">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1bfd6-181">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以降:</span><span class="sxs-lookup"><span data-stu-id="1bfd6-181">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="1bfd6-182">Windows 10 October 2018 Update (バージョン 1809/ビルド 10.0.17763) 以前の Windows OS:</span><span class="sxs-lookup"><span data-stu-id="1bfd6-182">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="1bfd6-183">入力を求められたら、[強力なパスワード](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-183">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1bfd6-184">[New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) コマンドレットに <xref:System.DateTime> という有効期限で `-AccountExpires` パラメーターを指定しない場合、アカウントは期限切れになりません。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-184">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1bfd6-185">詳しくは、「[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/)」および「[Service User Accounts (サービス ユーザー アカウント)](/windows/desktop/services/service-user-accounts)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-185">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1bfd6-186">Active Directory を使う場合、ユーザーを管理するための別の方法は、マネージド サービス アカウントを使うことです。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-186">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1bfd6-187">詳細については、「[Group Managed Service Accounts Overview (グループ マネージド サービス アカウントの概要)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-187">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1bfd6-188">サービスとしてログオン権利</span><span class="sxs-lookup"><span data-stu-id="1bfd6-188">Log on as a service rights</span></span>

<span data-ttu-id="1bfd6-189">サービス ユーザー アカウントに*サービスとしてログオン*権利を確立するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-189">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1bfd6-190">*secpol.msc* を実行して、ローカル セキュリティ ポリシー エディターを開きます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-190">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="1bfd6-191">**[ローカル ポリシー]** ノードを展開し、 **[ユーザー権利の割り当て]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-191">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="1bfd6-192">**[サービスとしてログオン]** ポリシーを開きます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-192">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1bfd6-193">**[ユーザーまたはグループの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-193">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="1bfd6-194">次のいずれかの方法を使用して、オブジェクト名 (ユーザー アカウント) を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-194">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1bfd6-195">オブジェクト名フィールドにユーザー アカウント (`{DOMAIN OR COMPUTER NAME\USER}`) を入力し、 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-195">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1bfd6-196">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-196">Select **Advanced**.</span></span> <span data-ttu-id="1bfd6-197">**[検索開始]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-197">Select **Find Now**.</span></span> <span data-ttu-id="1bfd6-198">一覧からユーザー アカウントを選択します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-198">Select the user account from the list.</span></span> <span data-ttu-id="1bfd6-199">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-199">Select **OK**.</span></span> <span data-ttu-id="1bfd6-200">再度 **[OK]** を選択して、ポリシーにユーザーを追加します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-200">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1bfd6-201">**[OK]** または **[適用]** を選択して、変更を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-201">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1bfd6-202">Windows サービスを作成して管理する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-202">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1bfd6-203">サービスを作成する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-203">Create a service</span></span>

<span data-ttu-id="1bfd6-204">PowerShell コマンドを使用して、サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-204">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1bfd6-205">PowerShell 6 の管理コマンド シェルから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-205">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1bfd6-206">`{EXE PATH}` &ndash; ホスト上のアプリのフォルダーへのパス (`d:\myservice` など)。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-206">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1bfd6-207">このパスに、アプリの実行可能ファイルは含めないでください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-207">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1bfd6-208">末尾のスラッシュは、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-208">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1bfd6-209">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; サービスのユーザー アカウント (`Contoso\ServiceUser` など)。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-209">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1bfd6-210">`{NAME}` &ndash; サービス名 (`MyService` など)。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-210">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1bfd6-211">`{EXE FILE PATH}` &ndash; アプリの実行可能ファイルのパス (`d:\myservice\myservice.exe` など)。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-211">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1bfd6-212">拡張子付きの実行可能ファイルのファイル名を含めます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-212">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1bfd6-213">`{DESCRIPTION}` &ndash; サービスの説明 (`My sample service` など)。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-213">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1bfd6-214">`{DISPLAY NAME}` &ndash; サービスの表示名 (`My Service` など)。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-214">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1bfd6-215">サービスを開始する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-215">Start a service</span></span>

<span data-ttu-id="1bfd6-216">次の PowerShell 6 コマンドでサービスを開始します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-216">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="1bfd6-217">このコマンドでサービスを開始するには数秒かかります。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-217">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1bfd6-218">サービスの状態を確認する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-218">Determine a service's status</span></span>

<span data-ttu-id="1bfd6-219">サービスの状態を確認するには、次の PowerShell 6 コマンドを使います。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-219">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="1bfd6-220">この状態は、次のいずれかの値として報告されます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-220">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1bfd6-221">サービスを停止する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-221">Stop a service</span></span>

<span data-ttu-id="1bfd6-222">次の PowerShell 6 コマンドでサービスを停止します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-222">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1bfd6-223">サービスを削除する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-223">Remove a service</span></span>

<span data-ttu-id="1bfd6-224">サービスを停止した後、少ししてから、次の Powershell 6 コマンドを使ってサービスを削除します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-224">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="1bfd6-225">イベントの開始と停止を扱う</span><span class="sxs-lookup"><span data-stu-id="1bfd6-225">Handle starting and stopping events</span></span>

<span data-ttu-id="1bfd6-226"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>、および <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> イベントを処理するには、次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-226">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="1bfd6-227">`OnStarting`、`OnStarted`、および `OnStopping` メソッドを使用して、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> から派生するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-227">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="1bfd6-228">`CustomWebHostService` を <xref:System.ServiceProcess.ServiceBase.Run*> に渡す <xref:Microsoft.AspNetCore.Hosting.IWebHost> の拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-228">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="1bfd6-229">`Program.Main` で、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> ではなく、拡張メソッド `RunAsCustomService` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-229">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="1bfd6-230">`Program.Main` 内の <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> の場所を確認するには、「[展開の種類](#deployment-type)」セクションに示されているコード サンプルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-230">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1bfd6-231">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="1bfd6-231">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1bfd6-232">インターネットまたは企業ネットワークからの要求とやり取りするサービスやプロキシまたはロード バランサーの背後にあるサービスでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-232">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1bfd6-233">詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-233">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1bfd6-234">エンドポイントを構成する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-234">Configure endpoints</span></span>

<span data-ttu-id="1bfd6-235">既定では、ASP.NET Core は `http://localhost:5000` にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-235">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1bfd6-236">`ASPNETCORE_URLS` 環境変数を設定して、URL とポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-236">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1bfd6-237">追加の URL とポートの構成方法については、関連するサーバーの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-237">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1bfd6-238">上記のガイダンスでは、HTTPS エンドポイントのサポートについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-238">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1bfd6-239">たとえば、Windows サービスで認証が使用されている場合は、アプリを HTTPS 用に構成します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-239">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1bfd6-240">サービス エンドポイントをセキュリティで保護するために ASP.NET Core の HTTPS 開発証明書を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-240">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1bfd6-241">現在のディレクトリとコンテンツのルート</span><span class="sxs-lookup"><span data-stu-id="1bfd6-241">Current directory and content root</span></span>

<span data-ttu-id="1bfd6-242">Windows サービスに対して <xref:System.IO.Directory.GetCurrentDirectory*> を呼び出して返される現在の作業ディレクトリは *C:\\WINDOWS\\system32* フォルダーです。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-242">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1bfd6-243">*system32* フォルダーは、サービスのファイル (設定ファイルなど) を保存するために適した場所ではありません。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-243">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1bfd6-244">次のいずれかの方法を使用して、サービスのアセットと設定ファイルを管理し、アクセスします。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-244">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="1bfd6-245">ContentRootPath または ContentRootFileProvider を使用する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-245">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="1bfd6-246">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) または <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> を使用して、アプリのリソースを見つけます。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-246">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="1bfd6-247">アプリのフォルダーにコンテンツ ルート パスを設定する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-247">Set the content root path to the app's folder</span></span>

<span data-ttu-id="1bfd6-248"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> は、サービスの作成時に `binPath` 引数に指定されたものと同じパスです。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-248">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="1bfd6-249">`GetCurrentDirectory` を呼び出して設定ファイルへのパスを作成する代わりに、アプリの[コンテンツ ルート](xref:fundamentals/index#content-root)へのパスを指定して <xref:System.IO.Directory.SetCurrentDirectory*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-249">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="1bfd6-250">`Program.Main` で、サービスの実行可能ファイルがあるフォルダーへのパスを判別し、そのパスを使用してアプリのコンテンツ ルートを確立します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-250">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1bfd6-251">ディスク上の適切な場所にサービスのファイルを格納する</span><span class="sxs-lookup"><span data-stu-id="1bfd6-251">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1bfd6-252">ファイルを含むフォルダーに対して <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用するときは、<xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> を使用して絶対パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="1bfd6-252">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1bfd6-253">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1bfd6-253">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="1bfd6-254">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="1bfd6-254">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="1bfd6-255">[Kestrel エンドポイント構成](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS 構成と SNI サポートを含む)</span><span class="sxs-lookup"><span data-stu-id="1bfd6-255">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
