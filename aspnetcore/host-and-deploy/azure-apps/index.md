---
title: Azure App Service に ASP.NET Core アプリを展開する
author: guardrex
description: この記事には、Azure のホストと展開リソースへのリンクが含まれます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 4dc150ff4534e42e1995a185f650cea9df70ccc4
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187051"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="b45fa-103">Azure App Service に ASP.NET Core アプリを展開する</span><span class="sxs-lookup"><span data-stu-id="b45fa-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="b45fa-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) は ASP.NET Core を含む Web アプリをホストするための [Microsoft クラウド コンピューティング プラットフォーム サービス](https://azure.microsoft.com/)です。</span><span class="sxs-lookup"><span data-stu-id="b45fa-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="b45fa-105">役に立つリソース</span><span class="sxs-lookup"><span data-stu-id="b45fa-105">Useful resources</span></span>

<span data-ttu-id="b45fa-106">「[App Service のドキュメント](/azure/app-service/)」は、Azure アプリのドキュメント、チュートリアル、サンプル、ハウツー ガイド、その他のリソースのホームです。</span><span class="sxs-lookup"><span data-stu-id="b45fa-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="b45fa-107">ASP.NET Core アプリのホスティングに関連する次の 2 つのチュートリアルは特に重要です。</span><span class="sxs-lookup"><span data-stu-id="b45fa-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="b45fa-108">Azure に ASP.NET Core Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b45fa-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="b45fa-109">Visual Studio を使用して ASP.NET Core Web アプリを作成し、Windows の Azure App Service に配置します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="b45fa-110">App Service on Linux で ASP.NET Core アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b45fa-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="b45fa-111">コマンド ラインを使用して ASP.NET Core Web アプリを作成し、Linux の Azure App Service に配置します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="b45fa-112">ASP.NET Core のドキュメントでは、次の記事を参照できます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-112">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="b45fa-113">Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-113">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="b45fa-114">Visual Studio で ASP.NET Core Web アプリを作成し、それを Azure App Service に配置する方法について説明します。Git を利用し、継続的に配置します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-114">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="b45fa-115">最初のパイプラインを作成する</span><span class="sxs-lookup"><span data-stu-id="b45fa-115">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="b45fa-116">ASP.NET Core アプリ用に CI ビルドを設定し、Azure App Service に継続的配置リリースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-116">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="b45fa-117">Azure Web アプリのサンドボックス</span><span class="sxs-lookup"><span data-stu-id="b45fa-117">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="b45fa-118">Azure アプリのプラットフォームで適用される Azure App Service ランタイム実行の制限事項について説明します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-118">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="b45fa-119">ASP.NET Core プロジェクトでの警告とエラーについて説明し、トラブルシューティングを行います。</span><span class="sxs-lookup"><span data-stu-id="b45fa-119">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="b45fa-120">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="b45fa-120">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="b45fa-121">プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="b45fa-121">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b45fa-122">64 ビット (x64) と 32 ビット (x86) アプリ用のランタイムは、Azure App Service 上に存在します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-122">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="b45fa-123">App Service で使用できる [.NET Core SDK](/dotnet/core/sdk) は 32 ビットですが、[Kudu](https://github.com/projectkudu/kudu/wiki) コンソールまたは Visual Studio の発行プロセスを使用すれば、ローカルでビルドされた 64 ビット アプリを展開できます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-123">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="b45fa-124">詳細については、「[アプリを発行および配置する](#publish-and-deploy-the-app)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-124">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b45fa-125">ネイティブの依存関係を含むアプリのため、32 ビット (x86) アプリ用のランタイムが Azure App Service 上に存在します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-125">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="b45fa-126">App Service で使用できる [.NET Core SDK](/dotnet/core/sdk) は 32 ビットです。</span><span class="sxs-lookup"><span data-stu-id="b45fa-126">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="b45fa-127">.Net Core ランタイムおよび .NET Core SDK に関する情報など、.NET Core フレームワーク コンポーネントおよび配布メソッドの詳細については、[.NET Core: コンポジション](/dotnet/core/about#composition)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-127">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="b45fa-128">パッケージ</span><span class="sxs-lookup"><span data-stu-id="b45fa-128">Packages</span></span>

<span data-ttu-id="b45fa-129">次の NuGet パッケージを、Azure App Service にデプロイされたアプリ用の自動ログ記録機能を提供するために含めます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-129">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="b45fa-130">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) は [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) を使用して Azure App Service と ASP.NET Core の Light-Up 統合を提供します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-130">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="b45fa-131">追加されるログ記録機能は `Microsoft.AspNetCore.AzureAppServicesIntegration` パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-131">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="b45fa-132">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) は [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) を実行して、`Microsoft.Extensions.Logging.AzureAppServices` パッケージに Azure App Service 診断ログ記録プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-132">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="b45fa-133">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) はロガー実装を提供することで、Azure App Service 診断ログとログ ストリーミング機能をサポートします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-133">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="b45fa-134">上記のパッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)からは使用できません。</span><span class="sxs-lookup"><span data-stu-id="b45fa-134">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b45fa-135">.NET Framework を対象とする、または `Microsoft.AspNetCore.App` メタパッケージを参照するアプリは、アプリのプロジェクト ファイル内の個々 のパッケージを明示的に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-135">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="b45fa-136">Azure Portal を使用してアプリの構成をオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="b45fa-136">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="b45fa-137">Azure Portal のアプリの設定により、アプリの環境変数の設定が許可されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-137">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="b45fa-138">環境変数は、[環境変数構成プロバイダー](xref:fundamentals/configuration/index#environment-variables-configuration-provider)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-138">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="b45fa-139">Azure Portal でアプリの設定が作成または変更され、 **[保存]** ボタンが選択された場合、Azure アプリは再起動されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-139">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="b45fa-140">環境変数は、サービスが再起動された後にアプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-140">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b45fa-141">アプリが[汎用ホスト](xref:fundamentals/host/generic-host)を使用する場合、環境変数は既定ではアプリの構成に読み込まれません。開発者が構成プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-141">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="b45fa-142">開発者は、構成プロバーダーを追加する際に環境変数のプレフィックスを決定します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-142">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="b45fa-143">詳細については、<xref:fundamentals/host/generic-host> および「[Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider)」(環境変数構成プロバイダー) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-143">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b45fa-144">アプリが [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) を使用してホストをビルドする場合、ホストを構成する環境変数では `ASPNETCORE_` プレフィックスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-144">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="b45fa-145">詳細については、<xref:fundamentals/host/web-host> および「[Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider)」(環境変数構成プロバイダー) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-145">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b45fa-146">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="b45fa-146">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b45fa-147">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、[アウトプロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)をホストするときに Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-147">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="b45fa-148">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-148">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="b45fa-149">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-149">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="b45fa-150">監視およびログ記録</span><span class="sxs-lookup"><span data-stu-id="b45fa-150">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b45fa-151">App Service にデプロイされる ASP.NET Core アプリは、App Service の拡張機能、**ASP.NET Core ログ記録の統合**を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-151">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="b45fa-152">拡張機能は、Azure App Service での ASP.NET Core アプリの統合のログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-152">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b45fa-153">App Service にデプロイされる ASP.NET Core アプリは、App Service の拡張機能、**ASP.NET Core ログ記録の拡張機能**を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-153">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="b45fa-154">拡張機能は、Azure App Service での ASP.NET Core アプリの統合のログ記録を有効にします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-154">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="b45fa-155">監視、ログ記録、トラブルシューティングに関する情報は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-155">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="b45fa-156">Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="b45fa-156">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="b45fa-157">アプリと App Service プランに関するクォータとメトリックを確認する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-157">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="b45fa-158">Azure App Service のアプリの診断ログの有効化</span><span class="sxs-lookup"><span data-stu-id="b45fa-158">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="b45fa-159">HTTP 状態コード、失敗した要求、Web サーバー アクティビティの診断ログを有効にしてアクセスする方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-159">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="b45fa-160">ASP.NET Core アプリでエラーを処理するための一般的な手法について理解します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-160">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="b45fa-161">ASP.NET Core アプリを使用した Azure App Service の配置に関する問題を診断する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-161">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="b45fa-162">Azure App Service/IIS によってホストされるアプリの一般的な配置の構成エラーのトラブルシューティングに関するアドバイスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-162">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="b45fa-163">データ保護キー リングとデプロイ スロット</span><span class="sxs-lookup"><span data-stu-id="b45fa-163">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="b45fa-164">[データ保護キー](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)は *%HOME%\ASP.NET\DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-164">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="b45fa-165">このフォルダーはネットワーク ストレージにバックアップされ、アプリをホストしているすべてのマシンで同期されています。</span><span class="sxs-lookup"><span data-stu-id="b45fa-165">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="b45fa-166">保存中のキーは保護されていません。</span><span class="sxs-lookup"><span data-stu-id="b45fa-166">Keys aren't protected at rest.</span></span> <span data-ttu-id="b45fa-167">このフォルダーから、単一のデプロイ スロットのアプリのすべてのインスタンスにキー リングが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-167">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="b45fa-168">ステージングや運用などの別のデプロイ スロットでは、キー リングが共有されません。</span><span class="sxs-lookup"><span data-stu-id="b45fa-168">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="b45fa-169">デプロイ スロットを切り替えると、データ保護を使用しているすべてのシステムが前のスロット内のキー リングを使用して格納されたデータを復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-169">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="b45fa-170">ASP.NET Cookie ミドルウェアは、その Cookie の保護にデータ保護を使用します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-170">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="b45fa-171">これにより、ユーザーが標準の ASP.NET Cookie ミドルウェアを使用するアプリからサインアウトします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-171">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="b45fa-172">スロットに依存しないキー リング ソリューションの場合、次のような外部キー リング プロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-172">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="b45fa-173">Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="b45fa-173">Azure Blob Storage</span></span>
* <span data-ttu-id="b45fa-174">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b45fa-174">Azure Key Vault</span></span>
* <span data-ttu-id="b45fa-175">SQL ストア</span><span class="sxs-lookup"><span data-stu-id="b45fa-175">SQL store</span></span>
* <span data-ttu-id="b45fa-176">Redis Cache</span><span class="sxs-lookup"><span data-stu-id="b45fa-176">Redis cache</span></span>

<span data-ttu-id="b45fa-177">詳細については、<xref:security/data-protection/implementation/key-storage-providers> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-177">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>
<!-- revert this after 3.0 supported
## Deploy ASP.NET Core preview release to Azure App Service

Use one of the following approaches if the app relies on a preview release of .NET Core:

* [Install the preview site extension](#install-the-preview-site-extension).
* [Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).
* [Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).
-->
## <a name="deploy-aspnet-core-30-to-azure-app-service"></a><span data-ttu-id="b45fa-178">Azure App Service に ASP.NET Core 3.0 を展開する</span><span class="sxs-lookup"><span data-stu-id="b45fa-178">Deploy ASP.NET Core 3.0 to Azure App Service</span></span>

<span data-ttu-id="b45fa-179">間もなく Azure App Service 上で ASP.NET Core 3.0 をご利用いただけるようになる予定です。</span><span class="sxs-lookup"><span data-stu-id="b45fa-179">We hope to have ASP.NET Core 3.0 available on Azure App Service soon.</span></span>

<span data-ttu-id="b45fa-180">アプリが .NET Core 3.0 に依存している場合は、次のいずれかの方法をお使いください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-180">Use one of the following approaches if the app relies on .NET Core 3.0:</span></span>

* <span data-ttu-id="b45fa-181">[プレビュー サイト拡張機能をインストールする](#install-the-preview-site-extension)</span><span class="sxs-lookup"><span data-stu-id="b45fa-181">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="b45fa-182">[自己完結型のプレビュー アプリを展開する](#deploy-a-self-contained-preview-app)。</span><span class="sxs-lookup"><span data-stu-id="b45fa-182">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span></span>
* <span data-ttu-id="b45fa-183">[コンテナー用の Web アプリで Docker を使用する](#use-docker-with-web-apps-for-containers)</span><span class="sxs-lookup"><span data-stu-id="b45fa-183">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="b45fa-184">プレビュー サイト拡張機能をインストールする</span><span class="sxs-lookup"><span data-stu-id="b45fa-184">Install the preview site extension</span></span>

<span data-ttu-id="b45fa-185">プレビュー サイト拡張機能の使用に関する問題が発生した場合は、[aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues) を作成してください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-185">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="b45fa-186">Azure Portal から App Service に移動します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-186">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="b45fa-187">Web アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-187">Select the web app.</span></span>
1. <span data-ttu-id="b45fa-188">検索ボックスに「拡張」と入力して "拡張機能" にフィルターするか、管理ツールの一覧をスクロール ダウンします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-188">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="b45fa-189">**[拡張機能]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-189">Select **Extensions**.</span></span>
1. <span data-ttu-id="b45fa-190">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-190">Select **Add**.</span></span>
1. <span data-ttu-id="b45fa-191">**[ASP.NET Core {X.Y} ({x64|x86}) ランタイム]** の拡張機能を一覧から選択します。`{X.Y}` は ASP.NET Core のプレビュー バージョンで、`{x64|x86}` はプラットフォームを指定します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-191">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="b45fa-192">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-192">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="b45fa-193">**[OK]** を選択し、拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-193">Select **OK** to install the extension.</span></span>

<span data-ttu-id="b45fa-194">操作が完了すると、最新の .NET Core プレビューがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-194">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="b45fa-195">次のようにしてインストールを検証します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-195">Verify the installation:</span></span>

1. <span data-ttu-id="b45fa-196">**[高度なツール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-196">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="b45fa-197">**[高度なツール]** で **[移動]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-197">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="b45fa-198">**[デバッグ コンソール]**  >  **[PowerShell]** のメニュー項目を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-198">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="b45fa-199">PowerShell のプロンプトで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-199">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="b45fa-200">コマンドの `{X.Y}` を ASP.NET Core ランタイム バージョンに、`{PLATFORM}` をプラットフォームに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-200">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="b45fa-201">x64 プレビューのランタイムがインストールされていると、コマンドで `True` が返されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-201">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="b45fa-202">App Services アプリのプラットフォーム アーキテクチャ (x86/x64) が、Azure Portal で A シリーズ計算、またはより優れたホスティング階層でホストされているアプリの設定に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-202">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="b45fa-203">アプリがインプロセス モードで実行されていて、プラットフォーム アーキテクチャが 64 ビット (x64) 向けに構成されている場合は、ASP.NET Core モジュールで 64 ビット プレビュー ランタイム (ある場合) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-203">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="b45fa-204">**ASP.NET Core {X.Y} (x64) ランタイム**の拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-204">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="b45fa-205">x64 プレビュー ランタイムがインストールされたら、Kudu PowerShell コマンド ウィンドウで次のコマンドを実行して、インストールを確認します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-205">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="b45fa-206">コマンドの `{X.Y}` を ASP.NET Core ランタイム バージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-206">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="b45fa-207">x64 プレビューのランタイムがインストールされていると、コマンドで `True` が返されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-207">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="b45fa-208">**ASP.NET Core 拡張機能**によって、たとえば Azure のログ記録など、Azure App Services での ASP.NET Core の追加機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-208">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="b45fa-209">この拡張機能は、Visual Studio からデプロイするときに自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-209">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="b45fa-210">拡張機能がインストールされていない場合は、アプリにインストールします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-210">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="b45fa-211">**ARM テンプレートでプレビュー サイト拡張機能を使用する**</span><span class="sxs-lookup"><span data-stu-id="b45fa-211">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="b45fa-212">ARM テンプレートを使用してアプリを作成し、展開する場合は、リソースの種類として `siteextensions` を使用してサイト拡張機能を Web アプリに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-212">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="b45fa-213">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-213">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="b45fa-214">自己完結型のプレビュー アプリを展開する</span><span class="sxs-lookup"><span data-stu-id="b45fa-214">Deploy a self-contained preview app</span></span>

<span data-ttu-id="b45fa-215">プレビュー ランタイムを対象とする[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) では、展開でプレビュー ランタイムを保持します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-215">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="b45fa-216">自己完結型アプリを展開する場合:</span><span class="sxs-lookup"><span data-stu-id="b45fa-216">When deploying a self-contained app:</span></span>

* <span data-ttu-id="b45fa-217">Azure App Service のサイトには、[プレビュー サイト拡張機能](#install-the-preview-site-extension)は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b45fa-217">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="b45fa-218">アプリは、[フレームワークに依存する展開 (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd) に発行するときとは異なる方法に従って、発行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-218">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="b45fa-219">「[自己完結型アプリを展開する](#deploy-the-app-self-contained)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-219">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="b45fa-220">コンテナー用の Web アプリで Docker を使用する</span><span class="sxs-lookup"><span data-stu-id="b45fa-220">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="b45fa-221">[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) には最新のプレビュー Docker イメージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b45fa-221">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="b45fa-222">イメージを基本イメージとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-222">The images can be used as a base image.</span></span> <span data-ttu-id="b45fa-223">通常は、イメージを使用して、Web App for Containers に展開します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-223">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="b45fa-224">アプリを発行および配置する</span><span class="sxs-lookup"><span data-stu-id="b45fa-224">Publish and deploy the app</span></span>

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="b45fa-225">フレームワークに依存するアプリを展開する</span><span class="sxs-lookup"><span data-stu-id="b45fa-225">Deploy the app framework-dependent</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b45fa-226">64 ビットの[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="b45fa-226">For a 64-bit [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

* <span data-ttu-id="b45fa-227">64 ビットのアプリをビルドするには、64 ビットの .NET Core SDK を使用します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-227">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="b45fa-228">App Service の **[構成]**  >  **[全般設定]** で、 **[プラットフォーム]** を **[64 ビット]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-228">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="b45fa-229">アプリでは、プラットフォームのビット数の選択を有効にするために、Basic 以上のサービス プランを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-229">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b45fa-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b45fa-230">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b45fa-231">Visual Studio ツールバーから **[ビルド]**  >  **[発行 {アプリケーション名}]** を選択するか、**ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-231">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="b45fa-232">**[発行先を選択]** ダイアログで、 **[App Service]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-232">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="b45fa-233">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-233">Select **Advanced**.</span></span> <span data-ttu-id="b45fa-234">**[発行]** ダイアログが開きます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-234">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="b45fa-235">**[発行]** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="b45fa-235">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="b45fa-236">**[リリース]** の構成が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-236">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="b45fa-237">**[展開モード]** ドロップダウン リストを開き、 **[フレームワーク依存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-237">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="b45fa-238">**[ターゲット ランタイム]** として **[ポータブル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-238">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="b45fa-239">展開時に追加のファイルを削除する場合、 **[ファイル発行オプション]** を開いて、転送先で追加のファイルを削除するチェック ボックスを選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-239">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="b45fa-240">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-240">Select **Save**.</span></span>
1. <span data-ttu-id="b45fa-241">発行ウィザードの残りのメッセージに従って、新しいサイトを作成するか、既存のサイトを更新します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-241">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b45fa-242">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b45fa-242">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b45fa-243">プロジェクト ファイルで、[ランタイム識別子 (RID)](/dotnet/core/rid-catalog) を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-243">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="b45fa-244">コマンド シェルから [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使って、リリースの構成でアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-244">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b45fa-245">次の例では、アプリがフレームワークに依存するアプリとして公開されています。</span><span class="sxs-lookup"><span data-stu-id="b45fa-245">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="b45fa-246">*bin/Release/{TARGET FRAMEWORK}/publish* ディレクトリのコンテンツを App Service のサイトに移動します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-246">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="b45fa-247">*publush* フォルダーの内容をご利用のローカル ハード ドライブまたはネットワーク共有から直接 [Kudu](https://github.com/projectkudu/kudu/wiki) コンソールの App Service にドラッグする場合は、Kudu コンソールの `D:\home\site\wwwroot` フォルダーにファイルをドラッグします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-247">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="b45fa-248">自己完結型アプリを展開する</span><span class="sxs-lookup"><span data-stu-id="b45fa-248">Deploy the app self-contained</span></span>

<span data-ttu-id="b45fa-249">[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) には、Visual Studio またはコマンドライン インターフェイス (CLI) ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-249">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b45fa-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b45fa-250">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b45fa-251">Visual Studio ツールバーから **[ビルド]**  >  **[発行 {アプリケーション名}]** を選択するか、**ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-251">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="b45fa-252">**[発行先を選択]** ダイアログで、 **[App Service]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-252">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="b45fa-253">**[詳細]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-253">Select **Advanced**.</span></span> <span data-ttu-id="b45fa-254">**[発行]** ダイアログが開きます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-254">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="b45fa-255">**[発行]** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="b45fa-255">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="b45fa-256">**[リリース]** の構成が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-256">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="b45fa-257">**[展開モード]** ドロップダウン リストを開いて、 **[自己完結]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-257">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="b45fa-258">**[ターゲット ランタイム]** ドロップダウン リストからターゲット ランタイムを選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-258">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="b45fa-259">既定値は、`win-x86` です。</span><span class="sxs-lookup"><span data-stu-id="b45fa-259">The default is `win-x86`.</span></span>
   * <span data-ttu-id="b45fa-260">展開時に追加のファイルを削除する場合、 **[ファイル発行オプション]** を開いて、転送先で追加のファイルを削除するチェック ボックスを選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-260">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="b45fa-261">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-261">Select **Save**.</span></span>
1. <span data-ttu-id="b45fa-262">発行ウィザードの残りのメッセージに従って、新しいサイトを作成するか、既存のサイトを更新します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-262">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b45fa-263">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b45fa-263">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b45fa-264">プロジェクト ファイルで、1 つまたは複数の[ランタイムの識別子 (RID)](/dotnet/core/rid-catalog) を指定します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-264">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="b45fa-265">単一の RID に `<RuntimeIdentifier>` (単数形) を使用するか、`<RuntimeIdentifiers>` (複数形) を使用して RID のセミコロン区切りのリストを提供します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-265">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="b45fa-266">次に例では、`win-x86` RID が指定されています。</span><span class="sxs-lookup"><span data-stu-id="b45fa-266">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b45fa-267">コマンド シェルから [dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使って、ホストのランタイムに対するリリースの構成でアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-267">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b45fa-268">次の例では、アプリは `win-x86` RID に発行されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-268">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="b45fa-269">`--runtime` オプションに指定された RID は、プロジェクト ファイルの `<RuntimeIdentifier>` (`<RuntimeIdentifiers>`) プロパティで提供される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b45fa-269">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86
   ```

1. <span data-ttu-id="b45fa-270">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* ディレクトリのコンテンツを App Service のサイトに移動します。</span><span class="sxs-lookup"><span data-stu-id="b45fa-270">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="b45fa-271">*publush* フォルダーの内容をご利用のローカル ハード ドライブまたはネットワーク共有から直接 Kudu コンソールの App Service にドラッグする場合は、Kudu コンソールの `D:\home\site\wwwroot` フォルダーにファイルをドラッグします。</span><span class="sxs-lookup"><span data-stu-id="b45fa-271">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="b45fa-272">プロトコル設定 (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="b45fa-272">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="b45fa-273">セキュリティで保護されたプロトコル バインディングを使うと、HTTPS 経由で要求に応答するときに使用する証明書を指定できます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-273">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="b45fa-274">バインディングには、特定のホスト名に向けて発行された有効なプライベート証明書 ( *.pfx*) が必要です。</span><span class="sxs-lookup"><span data-stu-id="b45fa-274">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="b45fa-275">詳しくは、「[チュートリアル: 既存のカスタム SSL 証明書を Azure App Service にバインドする](/azure/app-service/app-service-web-tutorial-custom-ssl)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-275">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="b45fa-276">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="b45fa-276">Transform web.config</span></span>

<span data-ttu-id="b45fa-277">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b45fa-277">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b45fa-278">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b45fa-278">Additional resources</span></span>

* [<span data-ttu-id="b45fa-279">App Service の概要</span><span class="sxs-lookup"><span data-stu-id="b45fa-279">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="b45fa-280">Azure App Service: .NET アプリのホスティングに最適な場所 (55 分間の概要ビデオ)</span><span class="sxs-lookup"><span data-stu-id="b45fa-280">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="b45fa-281">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="b45fa-281">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="b45fa-282">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="b45fa-282">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="b45fa-283">Windows Server の Azure App Service では[インターネット インフォメーション サービス (IIS)](https://www.iis.net/) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b45fa-283">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="b45fa-284">次のトピックは、基になる IIS テクノロジと関連しています。</span><span class="sxs-lookup"><span data-stu-id="b45fa-284">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="b45fa-285">Windows Server - IT 管理者のコンテンツの現在と以前のリリース</span><span class="sxs-lookup"><span data-stu-id="b45fa-285">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
