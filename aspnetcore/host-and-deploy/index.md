---
title: ASP.NET Core のホストと展開
author: guardrex
description: ホスティング環境を設定し、ASP.NET Core アプリを展開する方法を学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/index
ms.openlocfilehash: 173fcfe6d9368a1892d82b32d7b8f5e3cc71fc65
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172361"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="78c5d-103">ASP.NET Core のホストと展開</span><span class="sxs-lookup"><span data-stu-id="78c5d-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="78c5d-104">通常、ASP.NET Core アプリをホスティング環境に展開するには、以下を実行します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="78c5d-105">ホスティング サーバー上のフォルダーに発行されたアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="78c5d-106">要求の着信時にアプリを開始し、クラッシュ後、またはサーバーの再起動後にアプリを再起動するプロセス マネージャーを設定します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="78c5d-107">リバース プロキシを構成する場合は、アプリに要求を転送するリバース プロキシを設定します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="78c5d-108">フォルダーに発行する</span><span class="sxs-lookup"><span data-stu-id="78c5d-108">Publish to a folder</span></span>

<span data-ttu-id="78c5d-109">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドはアプリ コードをコンパイルし、アプリを実行するために必要なファイルを *publish* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="78c5d-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="78c5d-110">Visual Studio から展開する場合は、ファイルが展開先にコピーされる前に `dotnet publish` ステップが自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="78c5d-111">フォルダーの内容</span><span class="sxs-lookup"><span data-stu-id="78c5d-111">Folder contents</span></span>

<span data-ttu-id="78c5d-112">*publish* フォルダーには、1 つ以上のアプリのアセンブリ ファイル、依存関係、さらに必要に応じて .NET ランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-112">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="78c5d-113">.NET Core アプリは、*自己完結型展開*または*フレームワーク依存展開*として発行できます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-113">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="78c5d-114">アプリが自己完結型の場合、.NET ランタイムを含むアセンブリ ファイルが *publish* フォルダーに含まれています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-114">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="78c5d-115">アプリがフレームワークに依存する場合、.NET ランタイムのファイルは含まれていません。これは、サーバーにインストールされている .NET のバージョンへの参照がアプリに含まれていないためです。</span><span class="sxs-lookup"><span data-stu-id="78c5d-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="78c5d-116">既定の展開モデルはフレームワークに依存します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="78c5d-117">詳細については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-117">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="78c5d-118">*.exe* ファイルと *.dll* ファイルに加え、ASP.NET Core アプリの *publish* フォルダーには、通常、構成ファイル、静的資産、および MVC ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="78c5d-119">詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-119">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="78c5d-120">プロセス マネージャーを設定する</span><span class="sxs-lookup"><span data-stu-id="78c5d-120">Set up a process manager</span></span>

<span data-ttu-id="78c5d-121">ASP.NET Core アプリは、サーバーの起動時に起動し、クラッシュした場合には再起動する必要があるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="78c5d-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="78c5d-122">起動と再起動を自動化するには、プロセス マネージャーが必要です。</span><span class="sxs-lookup"><span data-stu-id="78c5d-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="78c5d-123">ASP.NET Core の最も一般的なプロセス マネージャーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="78c5d-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="78c5d-124">Linux</span><span class="sxs-lookup"><span data-stu-id="78c5d-124">Linux</span></span>
  * [<span data-ttu-id="78c5d-125">Nginx</span><span class="sxs-lookup"><span data-stu-id="78c5d-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="78c5d-126">Apache</span><span class="sxs-lookup"><span data-stu-id="78c5d-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="78c5d-127">Windows</span><span class="sxs-lookup"><span data-stu-id="78c5d-127">Windows</span></span>
  * [<span data-ttu-id="78c5d-128">IIS</span><span class="sxs-lookup"><span data-stu-id="78c5d-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="78c5d-129">Windows サービス</span><span class="sxs-lookup"><span data-stu-id="78c5d-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="78c5d-130">リバース プロキシを設定する</span><span class="sxs-lookup"><span data-stu-id="78c5d-130">Set up a reverse proxy</span></span>

<span data-ttu-id="78c5d-131">アプリで [Kestrel](xref:fundamentals/servers/kestrel) サーバーを使用する場合は、リバース プロキシ サーバーとして、[Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache)、または [IIS](xref:host-and-deploy/iis/index) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-131">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="78c5d-132">リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="78c5d-133">いずれの構成でも、&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-133">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="78c5d-134">詳細については、「[When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)」 (Kestrel とリバース プロキシを使用するタイミング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="78c5d-135">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="78c5d-135">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="78c5d-136">プロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="78c5d-136">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="78c5d-137">追加の構成をしないと、要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスにアクセスできない場合があります。</span><span class="sxs-lookup"><span data-stu-id="78c5d-137">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="78c5d-138">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-138">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="78c5d-139">Visual Studio と MSBuild を使用してデプロイを自動化する</span><span class="sxs-lookup"><span data-stu-id="78c5d-139">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="78c5d-140">多くの場合、展開には、[dotnet publish](/dotnet/core/tools/dotnet-publish) からサーバーへの出力のコピーのほか、追加の作業が必要になります。</span><span class="sxs-lookup"><span data-stu-id="78c5d-140">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="78c5d-141">たとえば、追加のファイルが必要になる場合や、*publish* フォルダーから除外される場合があります。</span><span class="sxs-lookup"><span data-stu-id="78c5d-141">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="78c5d-142">Visual Studio では Web 展開で MSBuild を使用します。この MSBuild は、展開時に他の多くの作業を行うためにカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-142">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="78c5d-143">詳細については、<xref:host-and-deploy/visual-studio-publish-profiles> と書籍『[Using MSBuild and Team Foundation Build](http://msbuildbook.com/)』(MSBuild と Team Foundation Build の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-143">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="78c5d-144">[Web の発行機能](xref:tutorials/publish-to-azure-webapp-using-vs)または[組み込みの Git サポート](xref:host-and-deploy/azure-apps/azure-continuous-deployment)を使用して、Visual Studio から Azure App Service にアプリを直接展開することができます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-144">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="78c5d-145">Azure DevOps Services では、[Azure App Service への継続的な展開](/azure/devops/pipelines/targets/webapp)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-145">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="78c5d-146">詳細については、[ASP.NET Core および Azure を使用した DevOps](xref:azure/devops/index) に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-146">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="78c5d-147">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="78c5d-147">Publish to Azure</span></span>

<span data-ttu-id="78c5d-148">Visual Studio を使って Azure にアプリを発行するための手順については、<xref:tutorials/publish-to-azure-webapp-using-vs> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-148">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="78c5d-149">その他の例については、「[Azure に ASP.NET Core Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-149">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="78c5d-150">Windows での MSDeploy を使用した発行</span><span class="sxs-lookup"><span data-stu-id="78c5d-150">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="78c5d-151">Visual Studio 発行プロファイルを使って (Windows コマンド プロンプトからの [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドの使用を含む) アプリを発行する方法については、「<xref:host-and-deploy/visual-studio-publish-profiles>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-151">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="78c5d-152">インターネット インフォメーション サービス (IIS)</span><span class="sxs-lookup"><span data-stu-id="78c5d-152">Internet Information Services (IIS)</span></span>

<span data-ttu-id="78c5d-153">*web.config* ファイルによって提供される構成を使用したインターネット インフォメーション サービス (IIS) への展開については、「<xref:host-and-deploy/iis/index>」の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-153">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="78c5d-154">Web ファームでのホスト</span><span class="sxs-lookup"><span data-stu-id="78c5d-154">Host in a web farm</span></span>

<span data-ttu-id="78c5d-155">Web ファーム環境 (たとえば、スケーラビリティのためのアプリの複数のインスタンスの展開) で ASP.NET Core アプリをホストするための構成については、<xref:host-and-deploy/web-farm> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-155">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="78c5d-156">Docker でのホスト</span><span class="sxs-lookup"><span data-stu-id="78c5d-156">Host on Docker</span></span>

<span data-ttu-id="78c5d-157">詳細については、「<xref:host-and-deploy/docker/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-157">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="78c5d-158">正常性チェックを実行する</span><span class="sxs-lookup"><span data-stu-id="78c5d-158">Perform health checks</span></span>

<span data-ttu-id="78c5d-159">アプリとその依存関係に対して正常性チェックを実行するには、正常性チェックのミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-159">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="78c5d-160">詳細については、「<xref:host-and-deploy/health-checks>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-160">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78c5d-161">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="78c5d-161">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="78c5d-162">ASP.NET ホスティング</span><span class="sxs-lookup"><span data-stu-id="78c5d-162">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="78c5d-163">通常、ASP.NET Core アプリをホスティング環境に展開するには、以下を実行します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-163">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="78c5d-164">ホスティング サーバー上のフォルダーに発行されたアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-164">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="78c5d-165">要求の着信時にアプリを開始し、クラッシュ後、またはサーバーの再起動後にアプリを再起動するプロセス マネージャーを設定します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-165">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="78c5d-166">リバース プロキシを構成する場合は、アプリに要求を転送するリバース プロキシを設定します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-166">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="78c5d-167">フォルダーに発行する</span><span class="sxs-lookup"><span data-stu-id="78c5d-167">Publish to a folder</span></span>

<span data-ttu-id="78c5d-168">[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドはアプリ コードをコンパイルし、アプリを実行するために必要なファイルを *publish* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="78c5d-168">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="78c5d-169">Visual Studio から展開する場合は、ファイルが展開先にコピーされる前に `dotnet publish` ステップが自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-169">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="78c5d-170">フォルダーの内容</span><span class="sxs-lookup"><span data-stu-id="78c5d-170">Folder contents</span></span>

<span data-ttu-id="78c5d-171">*publish* フォルダーには、1 つ以上のアプリのアセンブリ ファイル、依存関係、さらに必要に応じて .NET ランタイムが含まれています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-171">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="78c5d-172">.NET Core アプリは、*自己完結型展開*または*フレームワーク依存展開*として発行できます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-172">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="78c5d-173">アプリが自己完結型の場合、.NET ランタイムを含むアセンブリ ファイルが *publish* フォルダーに含まれています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-173">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="78c5d-174">アプリがフレームワークに依存する場合、.NET ランタイムのファイルは含まれていません。これは、サーバーにインストールされている .NET のバージョンへの参照がアプリに含まれていないためです。</span><span class="sxs-lookup"><span data-stu-id="78c5d-174">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="78c5d-175">既定の展開モデルはフレームワークに依存します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-175">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="78c5d-176">詳細については、「[.NET Core アプリケーションの展開](/dotnet/core/deploying/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-176">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="78c5d-177">*.exe* ファイルと *.dll* ファイルに加え、ASP.NET Core アプリの *publish* フォルダーには、通常、構成ファイル、静的資産、および MVC ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-177">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="78c5d-178">詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-178">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="78c5d-179">プロセス マネージャーを設定する</span><span class="sxs-lookup"><span data-stu-id="78c5d-179">Set up a process manager</span></span>

<span data-ttu-id="78c5d-180">ASP.NET Core アプリは、サーバーの起動時に起動し、クラッシュした場合には再起動する必要があるコンソール アプリです。</span><span class="sxs-lookup"><span data-stu-id="78c5d-180">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="78c5d-181">起動と再起動を自動化するには、プロセス マネージャーが必要です。</span><span class="sxs-lookup"><span data-stu-id="78c5d-181">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="78c5d-182">ASP.NET Core の最も一般的なプロセス マネージャーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="78c5d-182">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="78c5d-183">Linux</span><span class="sxs-lookup"><span data-stu-id="78c5d-183">Linux</span></span>
  * [<span data-ttu-id="78c5d-184">Nginx</span><span class="sxs-lookup"><span data-stu-id="78c5d-184">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="78c5d-185">Apache</span><span class="sxs-lookup"><span data-stu-id="78c5d-185">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="78c5d-186">Windows</span><span class="sxs-lookup"><span data-stu-id="78c5d-186">Windows</span></span>
  * [<span data-ttu-id="78c5d-187">IIS</span><span class="sxs-lookup"><span data-stu-id="78c5d-187">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="78c5d-188">Windows サービス</span><span class="sxs-lookup"><span data-stu-id="78c5d-188">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="78c5d-189">リバース プロキシを設定する</span><span class="sxs-lookup"><span data-stu-id="78c5d-189">Set up a reverse proxy</span></span>

<span data-ttu-id="78c5d-190">アプリで [Kestrel](xref:fundamentals/servers/kestrel) サーバーを使用する場合は、リバース プロキシ サーバーとして、[Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache)、または [IIS](xref:host-and-deploy/iis/index) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-190">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="78c5d-191">リバース プロキシ サーバーはインターネットから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="78c5d-191">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="78c5d-192">いずれの構成でも、&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-192">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="78c5d-193">詳細については、「[When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)」 (Kestrel とリバース プロキシを使用するタイミング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-193">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="78c5d-194">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="78c5d-194">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="78c5d-195">プロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="78c5d-195">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="78c5d-196">追加の構成をしないと、要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスにアクセスできない場合があります。</span><span class="sxs-lookup"><span data-stu-id="78c5d-196">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="78c5d-197">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-197">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="78c5d-198">Visual Studio と MSBuild を使用してデプロイを自動化する</span><span class="sxs-lookup"><span data-stu-id="78c5d-198">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="78c5d-199">多くの場合、展開には、[dotnet publish](/dotnet/core/tools/dotnet-publish) からサーバーへの出力のコピーのほか、追加の作業が必要になります。</span><span class="sxs-lookup"><span data-stu-id="78c5d-199">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="78c5d-200">たとえば、追加のファイルが必要になる場合や、*publish* フォルダーから除外される場合があります。</span><span class="sxs-lookup"><span data-stu-id="78c5d-200">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="78c5d-201">Visual Studio では Web 展開で MSBuild を使用します。この MSBuild は、展開時に他の多くの作業を行うためにカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-201">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="78c5d-202">詳細については、<xref:host-and-deploy/visual-studio-publish-profiles> と書籍『[Using MSBuild and Team Foundation Build](http://msbuildbook.com/)』(MSBuild と Team Foundation Build の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-202">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="78c5d-203">[Web の発行機能](xref:tutorials/publish-to-azure-webapp-using-vs)または[組み込みの Git サポート](xref:host-and-deploy/azure-apps/azure-continuous-deployment)を使用して、Visual Studio から Azure App Service にアプリを直接展開することができます。</span><span class="sxs-lookup"><span data-stu-id="78c5d-203">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="78c5d-204">Azure DevOps Services では、[Azure App Service への継続的な展開](/azure/devops/pipelines/targets/webapp)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="78c5d-204">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="78c5d-205">詳細については、[ASP.NET Core および Azure を使用した DevOps](xref:azure/devops/index) に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-205">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="78c5d-206">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="78c5d-206">Publish to Azure</span></span>

<span data-ttu-id="78c5d-207">Visual Studio を使って Azure にアプリを発行するための手順については、<xref:tutorials/publish-to-azure-webapp-using-vs> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-207">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="78c5d-208">その他の例については、「[Azure に ASP.NET Core Web アプリを作成する](/azure/app-service/app-service-web-get-started-dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-208">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="78c5d-209">Windows での MSDeploy を使用した発行</span><span class="sxs-lookup"><span data-stu-id="78c5d-209">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="78c5d-210">Visual Studio 発行プロファイルを使って (Windows コマンド プロンプトからの [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) コマンドの使用を含む) アプリを発行する方法については、「<xref:host-and-deploy/visual-studio-publish-profiles>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-210">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="78c5d-211">インターネット インフォメーション サービス (IIS)</span><span class="sxs-lookup"><span data-stu-id="78c5d-211">Internet Information Services (IIS)</span></span>

<span data-ttu-id="78c5d-212">*web.config* ファイルによって提供される構成を使用したインターネット インフォメーション サービス (IIS) への展開については、「<xref:host-and-deploy/iis/index>」の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-212">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="78c5d-213">Web ファームでのホスト</span><span class="sxs-lookup"><span data-stu-id="78c5d-213">Host in a web farm</span></span>

<span data-ttu-id="78c5d-214">Web ファーム環境 (たとえば、スケーラビリティのためのアプリの複数のインスタンスの展開) で ASP.NET Core アプリをホストするための構成については、<xref:host-and-deploy/web-farm> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-214">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="78c5d-215">Docker でのホスト</span><span class="sxs-lookup"><span data-stu-id="78c5d-215">Host on Docker</span></span>

<span data-ttu-id="78c5d-216">詳細については、「<xref:host-and-deploy/docker/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="78c5d-216">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78c5d-217">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="78c5d-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="78c5d-218">ASP.NET ホスティング</span><span class="sxs-lookup"><span data-stu-id="78c5d-218">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
