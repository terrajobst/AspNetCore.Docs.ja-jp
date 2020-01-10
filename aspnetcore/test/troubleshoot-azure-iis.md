---
title: Azure App Service および IIS での ASP.NET Core のトラブルシューティング
author: guardrex
description: ASP.NET Core アプリの Azure App Service およびインターネットインフォメーションサービス (IIS) の展開に関する問題を診断する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/20/2019
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: b0f5d44f153a095a6108a12ee91f4cc46fe0a0de
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829011"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="808f7-103">Azure App Service および IIS での ASP.NET Core のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="808f7-104">[Luke Latham](https://github.com/guardrex)と[Justin Kotk k](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="808f7-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="808f7-105">この記事では、アプリの起動時に発生する一般的なエラーと、アプリが Azure App Service または IIS に展開されたときのエラーを診断する手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="808f7-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="808f7-106">アプリのスタートアップエラー</span><span class="sxs-lookup"><span data-stu-id="808f7-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="808f7-107">一般的なスタートアップ HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="808f7-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="808f7-108">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="808f7-109">Azure App Service に展開されたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="808f7-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="808f7-110">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="808f7-111">IIS に展開されているか IIS Express ローカルで実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="808f7-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="808f7-112">このガイダンスは、Windows Server と Windows デスクトップの両方の展開に適用されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="808f7-113">パッケージキャッシュのクリア</span><span class="sxs-lookup"><span data-stu-id="808f7-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="808f7-114">メジャーアップグレードの実行時またはパッケージバージョンの変更時に統一性パッケージがアプリを中断した場合の対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="808f7-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="808f7-115">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="808f7-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="808f7-116">トラブルシューティングに関するその他のトピックを示します。</span><span class="sxs-lookup"><span data-stu-id="808f7-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="808f7-117">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="808f7-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="808f7-118">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="808f7-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="808f7-119">このトピックのアドバイスを使用してローカルでデバッグすると、 *502.5 プロセスエラー*または*500.30 開始エラー*が発生します。</span><span class="sxs-lookup"><span data-stu-id="808f7-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="808f7-120">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="808f7-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="808f7-121">ローカルでデバッグするときに*502.5 プロセスエラー*が発生した場合は、このトピックのアドバイスを使用して診断できます。</span><span class="sxs-lookup"><span data-stu-id="808f7-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="40314-forbidden"></a><span data-ttu-id="808f7-122">403.14 許可されていません</span><span class="sxs-lookup"><span data-stu-id="808f7-122">403.14 Forbidden</span></span>

<span data-ttu-id="808f7-123">アプリを起動できません。</span><span class="sxs-lookup"><span data-stu-id="808f7-123">The app fails to start.</span></span> <span data-ttu-id="808f7-124">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-124">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="808f7-125">このエラーが発生するのは、通常、ホストシステム上の展開が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="808f7-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="808f7-126">アプリがホストシステムの間違ったフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="808f7-126">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="808f7-127">展開プロセスで、アプリのすべてのファイルとフォルダーを、ホスティングシステムの展開フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="808f7-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="808f7-128">配置に web.config ファイルがないか、web.config*ファイルの内容*の*形式*が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="808f7-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="808f7-129">次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-129">Perform the following steps:</span></span>

1. <span data-ttu-id="808f7-130">ホストシステム上の展開フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="808f7-130">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="808f7-131">Visual Studio、PowerShell、手動デプロイなどの通常のデプロイ方法を使用して、アプリの*publish*フォルダーの内容をホスティングシステムに再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="808f7-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="808f7-132">配置に web.config*ファイルが*存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="808f7-133">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーに展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="808f7-134">アプリが IIS によってホストされている場合は、iis**マネージャー**の **[基本設定]** に表示されている iis の**物理パス**にアプリが展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="808f7-135">ホスティングシステムの配置をプロジェクトの*publish*フォルダーの内容と比較することによって、アプリのすべてのファイルとフォルダーが展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="808f7-136">発行された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="808f7-137">*Web.config ファイルの*詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="808f7-138">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="808f7-138">500 Internal Server Error</span></span>

<span data-ttu-id="808f7-139">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="808f7-139">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="808f7-140">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="808f7-140">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="808f7-141">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="808f7-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="808f7-142">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-142">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="808f7-143">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="808f7-143">From the server's perspective, that's correct.</span></span> <span data-ttu-id="808f7-144">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="808f7-144">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="808f7-145">サーバーのコマンドプロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にして問題のトラブルシューティングを行います。</span><span class="sxs-lookup"><span data-stu-id="808f7-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="808f7-146">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="808f7-146">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="808f7-147">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-147">The worker process fails.</span></span> <span data-ttu-id="808f7-148">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="808f7-148">The app doesn't start.</span></span>

<span data-ttu-id="808f7-149">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)が .NET Core CLR を見つけられず、インプロセス要求ハンドラー (*aspnetcorev2_inprocess*) を検索できません。</span><span class="sxs-lookup"><span data-stu-id="808f7-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="808f7-150">次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-150">Check that:</span></span>

* <span data-ttu-id="808f7-151">アプリが [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet パッケージまたは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を対象としている。</span><span class="sxs-lookup"><span data-stu-id="808f7-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="808f7-152">アプリが対象としているバージョンの ASP.NET Core 共有フレームワークが対象のコンピューターにインストールされている。</span><span class="sxs-lookup"><span data-stu-id="808f7-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="808f7-153">500.0 アウト プロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="808f7-153">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="808f7-154">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-154">The worker process fails.</span></span> <span data-ttu-id="808f7-155">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="808f7-155">The app doesn't start.</span></span>

<span data-ttu-id="808f7-156">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は、アウトプロセスホスティング要求ハンドラーを見つけることができません。</span><span class="sxs-lookup"><span data-stu-id="808f7-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="808f7-157">*aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* の隣のサブフォルダーにあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="808f7-158">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="808f7-158">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="808f7-159">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-159">The worker process fails.</span></span> <span data-ttu-id="808f7-160">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="808f7-160">The app doesn't start.</span></span>

<span data-ttu-id="808f7-161">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)コンポーネントの読み込み中に不明なエラーが発生しました。</span><span class="sxs-lookup"><span data-stu-id="808f7-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="808f7-162">次のいずれかのアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-162">Take one of the following actions:</span></span>

* <span data-ttu-id="808f7-163">[Microsoft サポート](https://support.microsoft.com/oas/default.aspx?prid=15832)に問い合わせます ( **[開発者ツール]** 、 **[ASP.NET Core]** の順に選択します)。</span><span class="sxs-lookup"><span data-stu-id="808f7-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="808f7-164">Stack Overflow について質問します。</span><span class="sxs-lookup"><span data-stu-id="808f7-164">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="808f7-165">[GitHub リポジトリ](https://github.com/dotnet/AspNetCore)で問題を報告します。</span><span class="sxs-lookup"><span data-stu-id="808f7-165">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="808f7-166">500.30 インプロセス起動エラー</span><span class="sxs-lookup"><span data-stu-id="808f7-166">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="808f7-167">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-167">The worker process fails.</span></span> <span data-ttu-id="808f7-168">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="808f7-168">The app doesn't start.</span></span>

<span data-ttu-id="808f7-169">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .NET Core CLR をインプロセスで開始しようとしますが、起動に失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="808f7-170">プロセス起動エラーの原因は、通常、アプリケーションイベントログのエントリと、ASP.NET Core モジュールの stdout ログによって決まります。</span><span class="sxs-lookup"><span data-stu-id="808f7-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="808f7-171">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="808f7-171">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="808f7-172">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-172">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="808f7-173">500.31 ANCM Failed to Find Native Dependencies (500.31 ANCM ネイティブの依存関係を見つけられませんでした)</span><span class="sxs-lookup"><span data-stu-id="808f7-173">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="808f7-174">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-174">The worker process fails.</span></span> <span data-ttu-id="808f7-175">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="808f7-175">The app doesn't start.</span></span>

<span data-ttu-id="808f7-176">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .net Core ランタイムをインプロセスで開始しようとしますが、起動に失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-176">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="808f7-177">このスタートアップ エラーの最も一般的な原因は、`Microsoft.NETCore.App` または `Microsoft.AspNetCore.App` ランタイムがインストールされていない場合です。</span><span class="sxs-lookup"><span data-stu-id="808f7-177">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="808f7-178">アプリが ASP.NET Core 3.0 をターゲットとして展開されていて、そのバージョンがコンピューターに存在しない場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="808f7-178">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="808f7-179">エラー メッセージの例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="808f7-179">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="808f7-180">エラー メッセージには、インストールされているすべての .NET Core のバージョンとアプリに必要なバージョンが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-180">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="808f7-181">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-181">To fix this error, either:</span></span>

* <span data-ttu-id="808f7-182">適切なバージョンの .NET Core をマシンにインストールします。</span><span class="sxs-lookup"><span data-stu-id="808f7-182">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="808f7-183">マシンに存在する .NET Core のバージョンをターゲットにするようにアプリを変更します。</span><span class="sxs-lookup"><span data-stu-id="808f7-183">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="808f7-184">アプリを[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-184">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="808f7-185">開発環境で実行している場合 (`ASPNETCORE_ENVIRONMENT` 環境変数が `Development` に設定されている場合)、特定のエラーが HTTP 応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="808f7-185">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="808f7-186">プロセス起動エラーの原因は、アプリケーションイベントログにもあります。</span><span class="sxs-lookup"><span data-stu-id="808f7-186">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="808f7-187">500.32 ANCM Failed to Load dll (500.32 ANCM DLL を読み込めませんでした)</span><span class="sxs-lookup"><span data-stu-id="808f7-187">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="808f7-188">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-188">The worker process fails.</span></span> <span data-ttu-id="808f7-189">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="808f7-189">The app doesn't start.</span></span>

<span data-ttu-id="808f7-190">このエラーの最も一般的な原因は、アプリが互換性のないプロセッサ アーキテクチャ用に発行されていることです。</span><span class="sxs-lookup"><span data-stu-id="808f7-190">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="808f7-191">ワーカー プロセスが 32 ビットアプリとして実行されていて、そのアプリが 64 ビットをターゲットとして発行されている場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="808f7-191">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="808f7-192">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-192">To fix this error, either:</span></span>

* <span data-ttu-id="808f7-193">ワーカー プロセスと同じプロセッサ アーキテクチャ用にアプリを再発行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-193">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="808f7-194">アプリを[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-executables-fde)として発行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-194">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="808f7-195">500.33 ANCM Request Handler Load Failure (500.33 ANCM 要求ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="808f7-195">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="808f7-196">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-196">The worker process fails.</span></span> <span data-ttu-id="808f7-197">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="808f7-197">The app doesn't start.</span></span>

<span data-ttu-id="808f7-198">アプリは `Microsoft.AspNetCore.App` フレームワークを参照していませんでした。</span><span class="sxs-lookup"><span data-stu-id="808f7-198">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="808f7-199">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)でホストできるのは、`Microsoft.AspNetCore.App` framework を対象とするアプリだけです。</span><span class="sxs-lookup"><span data-stu-id="808f7-199">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="808f7-200">このエラーを修正するには、アプリが `Microsoft.AspNetCore.App` フレームワークをターゲットにしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-200">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="808f7-201">アプリがターゲットとしているフレームワークを確認するには、`.runtimeconfig.json` を確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-201">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="808f7-202">500.34 ANCM Mixed Hosting Models Not Supported (500.34 ANCM 混在ホスティング モデルはサポートされません)</span><span class="sxs-lookup"><span data-stu-id="808f7-202">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="808f7-203">ワーカー プロセスでは、インプロセス アプリとアウト プロセス アプリの両方を同じプロセスで実行できません。</span><span class="sxs-lookup"><span data-stu-id="808f7-203">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="808f7-204">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-204">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="808f7-205">500.35 ANCM Multiple In-Process Applications in same Process (500.35 ANCM 同一プロセス内の複数のインプロセス アプリケーション)</span><span class="sxs-lookup"><span data-stu-id="808f7-205">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="808f7-206">ワーカープロセスでは、同じプロセスで複数のインプロセスアプリを実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="808f7-206">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="808f7-207">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-207">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="808f7-208">500.36 ANCM Out-Of-Process Handler Load Failure (500.36 ANCM アウト プロセス ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="808f7-208">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="808f7-209">アウト プロセス要求ハンドラーの *aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* ファイルの次にありません。</span><span class="sxs-lookup"><span data-stu-id="808f7-209">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="808f7-210">これは、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)のインストールが破損していることを示します。</span><span class="sxs-lookup"><span data-stu-id="808f7-210">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="808f7-211">このエラーを修正するには、[.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS 用) または Visual Studio (IIS Express 用) のインストールを修復します。</span><span class="sxs-lookup"><span data-stu-id="808f7-211">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="808f7-212">500.37 ANCM Failed to Start Within Startup Time Limit (500.37 ANCM スタートアップ時間の制限内に起動できませんでした)</span><span class="sxs-lookup"><span data-stu-id="808f7-212">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="808f7-213">指定されたスタートアップ時間の制限内に ANCM が起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="808f7-213">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="808f7-214">既定では、タイムアウトは 120 秒です。</span><span class="sxs-lookup"><span data-stu-id="808f7-214">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="808f7-215">このエラーは、同じマシン上で多数のアプリを起動したときに発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="808f7-215">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="808f7-216">スタートアップ中のサーバー上の CPU/メモリ使用量の急上昇を確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-216">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="808f7-217">必要に応じて、複数のアプリのスタートアップ プロセスをずらします。</span><span class="sxs-lookup"><span data-stu-id="808f7-217">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="808f7-218">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="808f7-218">502.5 Process Failure</span></span>

<span data-ttu-id="808f7-219">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-219">The worker process fails.</span></span> <span data-ttu-id="808f7-220">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="808f7-220">The app doesn't start.</span></span>

<span data-ttu-id="808f7-221">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="808f7-221">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="808f7-222">プロセス起動エラーの原因は、通常、アプリケーションイベントログのエントリと、ASP.NET Core モジュールの stdout ログによって決まります。</span><span class="sxs-lookup"><span data-stu-id="808f7-222">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="808f7-223">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="808f7-223">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="808f7-224">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-224">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="808f7-225">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-225">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="808f7-226">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="808f7-226">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="808f7-227">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="808f7-227">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="808f7-228">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-228">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="808f7-229">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="808f7-229">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="808f7-230">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="808f7-230">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="808f7-231">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="808f7-231">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="808f7-232">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-232">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="808f7-233">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="808f7-233">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="808f7-234">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="808f7-234">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="808f7-235">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="808f7-235">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="808f7-236">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="808f7-236">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="808f7-237">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="808f7-237">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="808f7-238">プロジェクトファイルの `<Platform>` MSBuild プロパティとアプリの発行済みビットとの間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-238">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="808f7-239">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="808f7-239">Connection reset</span></span>

<span data-ttu-id="808f7-240">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="808f7-240">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="808f7-241">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="808f7-241">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="808f7-242">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-242">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="808f7-243">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="808f7-243">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="808f7-244">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="808f7-244">Default startup limits</span></span>

<span data-ttu-id="808f7-245">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は、120秒の既定の*startupTimeLimit*を使用して構成されています。</span><span class="sxs-lookup"><span data-stu-id="808f7-245">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="808f7-246">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="808f7-246">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="808f7-247">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-247">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="808f7-248">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-248">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="808f7-249">アプリケーションイベントログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="808f7-249">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="808f7-250">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="808f7-250">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="808f7-251">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-251">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="808f7-252">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="808f7-252">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="808f7-253">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="808f7-253">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="808f7-254">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="808f7-254">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="808f7-255">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="808f7-255">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="808f7-256">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="808f7-256">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="808f7-257">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="808f7-258">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="808f7-259">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="808f7-260">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="808f7-261">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-261">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="808f7-262">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-262">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="808f7-263">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="808f7-263">Examine the log.</span></span> <span data-ttu-id="808f7-264">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-264">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="808f7-265">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-265">Run the app in the Kudu console</span></span>

<span data-ttu-id="808f7-266">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="808f7-266">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="808f7-267">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="808f7-267">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="808f7-268">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-268">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="808f7-269">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-269">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="808f7-270">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-270">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="808f7-271">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-271">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="808f7-272">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="808f7-272">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="808f7-273">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="808f7-273">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="808f7-274">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-274">Run the app:</span></span>
   * <span data-ttu-id="808f7-275">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="808f7-275">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="808f7-276">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="808f7-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="808f7-277">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="808f7-277">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="808f7-278">**プレビューリリースで実行されているフレームワークに依存する配置**</span><span class="sxs-lookup"><span data-stu-id="808f7-278">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="808f7-279">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="808f7-279">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="808f7-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="808f7-280">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="808f7-281">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="808f7-281">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="808f7-282">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="808f7-282">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="808f7-283">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="808f7-283">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="808f7-284">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="808f7-284">**Current release**</span></span>

* <span data-ttu-id="808f7-285">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="808f7-285">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="808f7-286">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="808f7-286">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="808f7-287">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="808f7-287">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="808f7-288">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="808f7-288">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="808f7-289">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="808f7-289">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="808f7-290">**プレビューリリースで実行されているフレームワークに依存する配置**</span><span class="sxs-lookup"><span data-stu-id="808f7-290">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="808f7-291">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="808f7-291">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="808f7-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="808f7-292">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="808f7-293">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="808f7-293">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="808f7-294">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="808f7-294">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="808f7-295">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="808f7-295">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="808f7-296">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="808f7-296">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="808f7-297">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="808f7-297">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="808f7-298">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="808f7-298">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="808f7-299">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-299">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="808f7-300">**[Suggested Solutions]\(推奨される解決方法\)**  >  **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、ボタンをクリックして **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-300">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="808f7-301">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-301">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="808f7-302">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="808f7-302">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="808f7-303">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="808f7-303">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="808f7-304">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="808f7-304">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="808f7-305">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="808f7-305">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="808f7-306">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="808f7-306">Make a request to the app.</span></span>
1. <span data-ttu-id="808f7-307">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="808f7-307">Return to the Azure portal.</span></span> <span data-ttu-id="808f7-308">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-308">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="808f7-309">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-309">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="808f7-310">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-310">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="808f7-311">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-311">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="808f7-312">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-312">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="808f7-313">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="808f7-313">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="808f7-314">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-314">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="808f7-315">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="808f7-315">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="808f7-316">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="808f7-316">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="808f7-317">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-317">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="808f7-318">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="808f7-318">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="808f7-319">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="808f7-319">Select **Save** to save the file.</span></span>

<span data-ttu-id="808f7-320">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-320">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="808f7-321">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="808f7-321">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="808f7-322">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="808f7-322">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="808f7-323">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="808f7-323">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="808f7-324">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="808f7-324">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="808f7-325">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-325">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="808f7-326">ASP.NET Core モジュールデバッグログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="808f7-326">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="808f7-327">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-327">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="808f7-328">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="808f7-328">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="808f7-329">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-329">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="808f7-330">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="808f7-330">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="808f7-331">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="808f7-331">Redeploy the app.</span></span>
   * <span data-ttu-id="808f7-332">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="808f7-332">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="808f7-333">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-333">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="808f7-334">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-334">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="808f7-335">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-335">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="808f7-336">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-336">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="808f7-337">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-337">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="808f7-338">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="808f7-338">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="808f7-339">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="808f7-339">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="808f7-340">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="808f7-340">Select the **Save** button.</span></span>
1. <span data-ttu-id="808f7-341">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-341">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="808f7-342">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-342">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="808f7-343">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-343">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="808f7-344">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-344">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="808f7-345">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-345">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="808f7-346">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-346">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="808f7-347">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="808f7-347">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="808f7-348">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-348">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="808f7-349">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="808f7-349">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="808f7-350">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-350">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="808f7-351">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="808f7-351">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="808f7-352">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="808f7-352">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="808f7-353">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="808f7-353">Save the file.</span></span>

<span data-ttu-id="808f7-354">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-354">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="808f7-355">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="808f7-355">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="808f7-356">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="808f7-356">There's no limit on log file size.</span></span> <span data-ttu-id="808f7-357">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="808f7-357">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="808f7-358">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="808f7-358">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="808f7-359">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-359">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="808f7-360">低速またはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="808f7-360">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="808f7-361">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-361">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="808f7-362">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-362">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="808f7-363">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="808f7-363">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="808f7-364">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="808f7-364">Monitoring blades</span></span>

<span data-ttu-id="808f7-365">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-365">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="808f7-366">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="808f7-366">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="808f7-367">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-367">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="808f7-368">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="808f7-368">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="808f7-369">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-369">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="808f7-370">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-370">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="808f7-371">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-371">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="808f7-372">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-372">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="808f7-373">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="808f7-373">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="808f7-374">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-374">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="808f7-375">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-375">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="808f7-376">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="808f7-376">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="808f7-377">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-377">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="808f7-378">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-378">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="808f7-379">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-379">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="808f7-380">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-380">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="808f7-381">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="808f7-381">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="808f7-382">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="808f7-382">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="808f7-383">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="808f7-383">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="808f7-384">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="808f7-384">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="808f7-385">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="808f7-385">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="808f7-386">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-386">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="808f7-387">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-387">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="808f7-388">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-388">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="808f7-389">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-389">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="808f7-390">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-390">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="808f7-391">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="808f7-391">Make a request to the app.</span></span>
1. <span data-ttu-id="808f7-392">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-392">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="808f7-393">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="808f7-393">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="808f7-394">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="808f7-394">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="808f7-395">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="808f7-395">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="808f7-396">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="808f7-396">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="808f7-397">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="808f7-397">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="808f7-398">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="808f7-398">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="808f7-399">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="808f7-399">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="808f7-400">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="808f7-400">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="808f7-401">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="808f7-401">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="808f7-402">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-402">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="808f7-403">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-403">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="808f7-404">アプリケーションイベントログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="808f7-404">Application Event Log (IIS)</span></span>

<span data-ttu-id="808f7-405">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="808f7-405">Access the Application Event Log:</span></span>

1. <span data-ttu-id="808f7-406">[スタート] メニューを開き、**イベント ビューアー**を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="808f7-406">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="808f7-407">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-407">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="808f7-408">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-408">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="808f7-409">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="808f7-409">Search for errors associated with the failing app.</span></span> <span data-ttu-id="808f7-410">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-410">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="808f7-411">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="808f7-411">Run the app at a command prompt</span></span>

<span data-ttu-id="808f7-412">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="808f7-412">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="808f7-413">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="808f7-413">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="808f7-414">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="808f7-414">Framework-dependent deployment</span></span>

<span data-ttu-id="808f7-415">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="808f7-415">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="808f7-416">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-416">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="808f7-417">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-417">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="808f7-418">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="808f7-418">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="808f7-419">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-419">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="808f7-420">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="808f7-420">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="808f7-421">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="808f7-421">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="808f7-422">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="808f7-422">Self-contained deployment</span></span>

<span data-ttu-id="808f7-423">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="808f7-423">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="808f7-424">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-424">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="808f7-425">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-425">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="808f7-426">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="808f7-426">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="808f7-427">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-427">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="808f7-428">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="808f7-428">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="808f7-429">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="808f7-429">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="808f7-430">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="808f7-430">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="808f7-431">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="808f7-431">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="808f7-432">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="808f7-432">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="808f7-433">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="808f7-433">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="808f7-434">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-434">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="808f7-435">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="808f7-435">Edit the *web.config* file.</span></span> <span data-ttu-id="808f7-436">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="808f7-436">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="808f7-437">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="808f7-437">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="808f7-438">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-438">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="808f7-439">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="808f7-439">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="808f7-440">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-440">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="808f7-441">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="808f7-441">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="808f7-442">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="808f7-442">Make a request to the app.</span></span>
1. <span data-ttu-id="808f7-443">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="808f7-443">Navigate to the *logs* folder.</span></span> <span data-ttu-id="808f7-444">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="808f7-444">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="808f7-445">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="808f7-445">Study the log for errors.</span></span>

<span data-ttu-id="808f7-446">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="808f7-446">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="808f7-447">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="808f7-447">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="808f7-448">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="808f7-448">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="808f7-449">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="808f7-449">Save the file.</span></span>

<span data-ttu-id="808f7-450">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-450">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="808f7-451">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="808f7-451">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="808f7-452">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="808f7-452">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="808f7-453">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="808f7-453">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="808f7-454">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-454">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="808f7-455">ASP.NET Core モジュールデバッグログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="808f7-455">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="808f7-456">次のハンドラー設定をアプリの*web.config*ファイルに追加して、ASP.NET Core モジュールのデバッグログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="808f7-456">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="808f7-457">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="808f7-457">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="808f7-458">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-458">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="808f7-459">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="808f7-459">Enable the Developer Exception Page</span></span>

<span data-ttu-id="808f7-460">`ASPNETCORE_ENVIRONMENT`[環境変数を web.config に追加](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)して、開発環境でアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="808f7-460">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="808f7-461">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-461">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="808f7-462">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="808f7-462">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="808f7-463">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-463">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="808f7-464">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-464">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="808f7-465">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="808f7-465">Obtain data from an app</span></span>

<span data-ttu-id="808f7-466">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="808f7-466">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="808f7-467">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-467">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="808f7-468">低速またはハング中のアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="808f7-468">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="808f7-469">*クラッシュダンプ*は、システムのメモリのスナップショットであり、アプリのクラッシュ、スタートアップエラー、または低速アプリの原因を特定するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="808f7-469">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="808f7-470">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="808f7-470">App crashes or encounters an exception</span></span>

<span data-ttu-id="808f7-471">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="808f7-471">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="808f7-472">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="808f7-472">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="808f7-473">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="808f7-473">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="808f7-474">[EnableDumps PowerShell スクリプト](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-474">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="808f7-475">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-475">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="808f7-476">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-476">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="808f7-477">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-477">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="808f7-478">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-478">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="808f7-479">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-479">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="808f7-480">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="808f7-480">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="808f7-481">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="808f7-481">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="808f7-482">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="808f7-482">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="808f7-483">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="808f7-483">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="808f7-484">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="808f7-484">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="808f7-485">アプリが*ハング*した (応答が停止してもクラッシュしない) 場合、起動時に失敗した場合、または正常に実行された場合は、「[ユーザーモードのダンプファイル:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)ダンプを生成する適切なツールを選択する最適なツールを選択する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-485">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="808f7-486">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="808f7-486">Analyze the dump</span></span>

<span data-ttu-id="808f7-487">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="808f7-487">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="808f7-488">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="808f7-488">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="808f7-489">パッケージキャッシュのクリア</span><span class="sxs-lookup"><span data-stu-id="808f7-489">Clear package caches</span></span>

<span data-ttu-id="808f7-490">開発用コンピューターの .NET Core SDK をアップグレードした後、またはアプリ内のパッケージバージョンを変更した直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="808f7-490">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="808f7-491">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="808f7-491">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="808f7-492">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="808f7-492">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="808f7-493">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="808f7-493">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="808f7-494">コマンドシェルから `dotnet nuget locals all --clear` を実行して、パッケージキャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="808f7-494">Clear the package caches by executing `dotnet nuget locals all --clear` from a command shell.</span></span>

   <span data-ttu-id="808f7-495">パッケージキャッシュのクリアは、 [nuget.exe](https://www.nuget.org/downloads)ツールを使用して実行し、コマンド `nuget locals all -clear`を実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="808f7-495">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="808f7-496">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="808f7-496">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="808f7-497">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="808f7-497">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="808f7-498">アプリケーションを再展開する前に、サーバー上の展開フォルダーにあるすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="808f7-498">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="808f7-499">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="808f7-499">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="808f7-500">Azure ドキュメント</span><span class="sxs-lookup"><span data-stu-id="808f7-500">Azure documentation</span></span>

* [<span data-ttu-id="808f7-501">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="808f7-501">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="808f7-502">「Visual Studio を使用した Azure App Service での web アプリのトラブルシューティング」の「web アプリのリモートデバッグ」セクション</span><span class="sxs-lookup"><span data-stu-id="808f7-502">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="808f7-503">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="808f7-503">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="808f7-504">Azure App Service でアプリを監視する方法</span><span class="sxs-lookup"><span data-stu-id="808f7-504">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="808f7-505">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-505">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="808f7-506">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-506">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="808f7-507">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="808f7-507">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="808f7-508">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="808f7-508">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="808f7-509">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="808f7-509">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="808f7-510">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="808f7-510">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="808f7-511">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="808f7-511">Visual Studio documentation</span></span>

* [<span data-ttu-id="808f7-512">Visual Studio 2017 での Azure での IIS のリモートデバッグ ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="808f7-512">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="808f7-513">Visual Studio 2017 のリモート IIS コンピューター上のリモートデバッグ ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="808f7-513">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="808f7-514">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="808f7-514">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="808f7-515">Visual Studio Code のドキュメント</span><span class="sxs-lookup"><span data-stu-id="808f7-515">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="808f7-516">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="808f7-516">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)
