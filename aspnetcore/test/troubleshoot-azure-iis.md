---
title: Azure App Service および IIS での ASP.NET Core のトラブルシューティング
author: guardrex
description: ASP.NET Core アプリの Azure App Service およびインターネットインフォメーションサービス (IIS) の展開に関する問題を診断する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/18/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 071dba9e936351e201b7582b3d0667cd6fac54bb
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294611"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="bf733-103">Azure App Service および IIS での ASP.NET Core のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="bf733-104">[Luke Latham](https://github.com/guardrex)と[Justin Kotk k](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="bf733-104">By [Luke Latham](https://github.com/guardrex) and [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="bf733-105">この記事では、アプリの起動時に発生する一般的なエラーと、アプリが Azure App Service または IIS に展開されたときのエラーを診断する手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="bf733-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="bf733-106">アプリのスタートアップエラー</span><span class="sxs-lookup"><span data-stu-id="bf733-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="bf733-107">一般的なスタートアップ HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="bf733-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="bf733-108">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="bf733-109">Azure App Service に展開されたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="bf733-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="bf733-110">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="bf733-111">IIS に展開されているか IIS Express ローカルで実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="bf733-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="bf733-112">このガイダンスは、Windows Server と Windows デスクトップの両方の展開に適用されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="bf733-113">パッケージキャッシュのクリア</span><span class="sxs-lookup"><span data-stu-id="bf733-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="bf733-114">メジャーアップグレードの実行時またはパッケージバージョンの変更時に統一性パッケージがアプリを中断した場合の対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="bf733-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="bf733-115">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="bf733-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="bf733-116">トラブルシューティングに関するその他のトピックを示します。</span><span class="sxs-lookup"><span data-stu-id="bf733-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="bf733-117">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="bf733-117">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="bf733-118">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="bf733-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="bf733-119">このトピックのアドバイスを使用してローカルでデバッグすると、 *502.5 プロセスエラー*または*500.30 開始エラー*が発生します。</span><span class="sxs-lookup"><span data-stu-id="bf733-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="bf733-120">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="bf733-120">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="bf733-121">ローカルでデバッグするときに*502.5 プロセスエラー*が発生した場合は、このトピックのアドバイスを使用して診断できます。</span><span class="sxs-lookup"><span data-stu-id="bf733-121">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

::: moniker-end

### <a name="40314-forbidden"></a><span data-ttu-id="bf733-122">403.14 許可されていません</span><span class="sxs-lookup"><span data-stu-id="bf733-122">403.14 Forbidden</span></span>

<span data-ttu-id="bf733-123">アプリを起動できません。</span><span class="sxs-lookup"><span data-stu-id="bf733-123">The app fails to start.</span></span> <span data-ttu-id="bf733-124">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-124">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="bf733-125">このエラーが発生するのは、通常、ホストシステム上の展開が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="bf733-125">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="bf733-126">アプリがホストシステムの間違ったフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="bf733-126">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="bf733-127">展開プロセスで、アプリのすべてのファイルとフォルダーを、ホスティングシステムの展開フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="bf733-127">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="bf733-128">配置に web.config ファイルがないか、web.config*ファイルの内容*の*形式*が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="bf733-128">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="bf733-129">次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-129">Perform the following steps:</span></span>

1. <span data-ttu-id="bf733-130">ホストシステム上の展開フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="bf733-130">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="bf733-131">Visual Studio、PowerShell、手動デプロイなどの通常のデプロイ方法を使用して、アプリの*publish*フォルダーの内容をホスティングシステムに再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="bf733-131">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="bf733-132">配置に web.config*ファイルが*存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-132">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="bf733-133">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーに展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-133">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="bf733-134">アプリが IIS によってホストされている場合は、iis**マネージャー**の **[基本設定]** に表示されている iis の**物理パス**にアプリが展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-134">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="bf733-135">ホスティングシステムの配置をプロジェクトの*publish*フォルダーの内容と比較することによって、アプリのすべてのファイルとフォルダーが展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-135">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="bf733-136">発行された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-136">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="bf733-137">*Web.config ファイルの*詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-137">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="bf733-138">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="bf733-138">500 Internal Server Error</span></span>

<span data-ttu-id="bf733-139">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="bf733-139">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="bf733-140">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="bf733-140">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="bf733-141">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-141">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="bf733-142">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-142">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="bf733-143">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="bf733-143">From the server's perspective, that's correct.</span></span> <span data-ttu-id="bf733-144">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="bf733-144">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="bf733-145">サーバーのコマンドプロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にして問題のトラブルシューティングを行います。</span><span class="sxs-lookup"><span data-stu-id="bf733-145">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="bf733-146">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="bf733-146">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="bf733-147">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-147">The worker process fails.</span></span> <span data-ttu-id="bf733-148">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="bf733-148">The app doesn't start.</span></span>

<span data-ttu-id="bf733-149">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)が .NET Core CLR を見つけられず、インプロセス要求ハンドラー (*aspnetcorev2_inprocess*) を検索できません。</span><span class="sxs-lookup"><span data-stu-id="bf733-149">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="bf733-150">次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-150">Check that:</span></span>

* <span data-ttu-id="bf733-151">アプリが [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet パッケージまたは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を対象としている。</span><span class="sxs-lookup"><span data-stu-id="bf733-151">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="bf733-152">アプリが対象としているバージョンの ASP.NET Core 共有フレームワークが対象のコンピューターにインストールされている。</span><span class="sxs-lookup"><span data-stu-id="bf733-152">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="bf733-153">500.0 アウト プロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="bf733-153">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="bf733-154">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-154">The worker process fails.</span></span> <span data-ttu-id="bf733-155">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="bf733-155">The app doesn't start.</span></span>

<span data-ttu-id="bf733-156">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は、アウトプロセスホスティング要求ハンドラーを見つけることができません。</span><span class="sxs-lookup"><span data-stu-id="bf733-156">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="bf733-157">*aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* の隣のサブフォルダーにあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-157">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="bf733-158">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="bf733-158">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="bf733-159">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-159">The worker process fails.</span></span> <span data-ttu-id="bf733-160">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="bf733-160">The app doesn't start.</span></span>

<span data-ttu-id="bf733-161">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)コンポーネントの読み込み中に不明なエラーが発生しました。</span><span class="sxs-lookup"><span data-stu-id="bf733-161">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="bf733-162">次のいずれかのアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-162">Take one of the following actions:</span></span>

* <span data-ttu-id="bf733-163">[Microsoft サポート](https://support.microsoft.com/oas/default.aspx?prid=15832)に問い合わせます ( **[開発者ツール]** 、 **[ASP.NET Core]** の順に選択します)。</span><span class="sxs-lookup"><span data-stu-id="bf733-163">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="bf733-164">Stack Overflow について質問します。</span><span class="sxs-lookup"><span data-stu-id="bf733-164">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="bf733-165">[GitHub リポジトリ](https://github.com/dotnet/AspNetCore)で問題を報告します。</span><span class="sxs-lookup"><span data-stu-id="bf733-165">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="bf733-166">500.30 インプロセス起動エラー</span><span class="sxs-lookup"><span data-stu-id="bf733-166">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="bf733-167">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-167">The worker process fails.</span></span> <span data-ttu-id="bf733-168">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="bf733-168">The app doesn't start.</span></span>

<span data-ttu-id="bf733-169">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .NET Core CLR をインプロセスで開始しようとしますが、起動に失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-169">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="bf733-170">プロセス起動エラーの原因は、通常、アプリケーションイベントログのエントリと、ASP.NET Core モジュールの stdout ログによって決まります。</span><span class="sxs-lookup"><span data-stu-id="bf733-170">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="bf733-171">一般的なエラー条件:</span><span class="sxs-lookup"><span data-stu-id="bf733-171">Common failure conditions:</span></span>

* <span data-ttu-id="bf733-172">アプリケーションの構成が正しくありません。これは、存在しない ASP.NET Core 共有フレームワークのバージョンをターゲットにしているためです。</span><span class="sxs-lookup"><span data-stu-id="bf733-172">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="bf733-173">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-173">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="bf733-174">Azure Key Vault を使用しています。 Key Vault に対する権限がありません。</span><span class="sxs-lookup"><span data-stu-id="bf733-174">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="bf733-175">対象の Key Vault のアクセスポリシーを確認して、正しいアクセス許可が付与されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-175">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="bf733-176">500.31 ANCM Failed to Find Native Dependencies (500.31 ANCM ネイティブの依存関係を見つけられませんでした)</span><span class="sxs-lookup"><span data-stu-id="bf733-176">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="bf733-177">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-177">The worker process fails.</span></span> <span data-ttu-id="bf733-178">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="bf733-178">The app doesn't start.</span></span>

<span data-ttu-id="bf733-179">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .net Core ランタイムをインプロセスで開始しようとしますが、起動に失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-179">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="bf733-180">このスタートアップ エラーの最も一般的な原因は、`Microsoft.NETCore.App` または `Microsoft.AspNetCore.App` ランタイムがインストールされていない場合です。</span><span class="sxs-lookup"><span data-stu-id="bf733-180">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="bf733-181">アプリが ASP.NET Core 3.0 をターゲットとして展開されていて、そのバージョンがコンピューターに存在しない場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="bf733-181">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="bf733-182">エラー メッセージの例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="bf733-182">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="bf733-183">エラー メッセージには、インストールされているすべての .NET Core のバージョンとアプリに必要なバージョンが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-183">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="bf733-184">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-184">To fix this error, either:</span></span>

* <span data-ttu-id="bf733-185">適切なバージョンの .NET Core をマシンにインストールします。</span><span class="sxs-lookup"><span data-stu-id="bf733-185">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="bf733-186">マシンに存在する .NET Core のバージョンをターゲットにするようにアプリを変更します。</span><span class="sxs-lookup"><span data-stu-id="bf733-186">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="bf733-187">アプリを[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-187">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="bf733-188">開発環境で実行している場合 (`ASPNETCORE_ENVIRONMENT` 環境変数が `Development` に設定されている場合)、特定のエラーが HTTP 応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="bf733-188">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="bf733-189">プロセス起動エラーの原因は、アプリケーションイベントログにもあります。</span><span class="sxs-lookup"><span data-stu-id="bf733-189">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="bf733-190">500.32 ANCM Failed to Load dll (500.32 ANCM DLL を読み込めませんでした)</span><span class="sxs-lookup"><span data-stu-id="bf733-190">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="bf733-191">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-191">The worker process fails.</span></span> <span data-ttu-id="bf733-192">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="bf733-192">The app doesn't start.</span></span>

<span data-ttu-id="bf733-193">このエラーの最も一般的な原因は、アプリが互換性のないプロセッサ アーキテクチャ用に発行されていることです。</span><span class="sxs-lookup"><span data-stu-id="bf733-193">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="bf733-194">ワーカー プロセスが 32 ビットアプリとして実行されていて、そのアプリが 64 ビットをターゲットとして発行されている場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="bf733-194">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="bf733-195">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-195">To fix this error, either:</span></span>

* <span data-ttu-id="bf733-196">ワーカー プロセスと同じプロセッサ アーキテクチャ用にアプリを再発行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-196">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="bf733-197">アプリを[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-executables-fde)として発行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-197">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="bf733-198">500.33 ANCM Request Handler Load Failure (500.33 ANCM 要求ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="bf733-198">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="bf733-199">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-199">The worker process fails.</span></span> <span data-ttu-id="bf733-200">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="bf733-200">The app doesn't start.</span></span>

<span data-ttu-id="bf733-201">アプリは `Microsoft.AspNetCore.App` フレームワークを参照していませんでした。</span><span class="sxs-lookup"><span data-stu-id="bf733-201">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="bf733-202">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)でホストできるのは、`Microsoft.AspNetCore.App` framework を対象とするアプリだけです。</span><span class="sxs-lookup"><span data-stu-id="bf733-202">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="bf733-203">このエラーを修正するには、アプリが `Microsoft.AspNetCore.App` フレームワークをターゲットにしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-203">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="bf733-204">アプリがターゲットとしているフレームワークを確認するには、`.runtimeconfig.json` を確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-204">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="bf733-205">500.34 ANCM Mixed Hosting Models Not Supported (500.34 ANCM 混在ホスティング モデルはサポートされません)</span><span class="sxs-lookup"><span data-stu-id="bf733-205">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="bf733-206">ワーカー プロセスでは、インプロセス アプリとアウト プロセス アプリの両方を同じプロセスで実行できません。</span><span class="sxs-lookup"><span data-stu-id="bf733-206">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="bf733-207">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-207">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="bf733-208">500.35 ANCM Multiple In-Process Applications in same Process (500.35 ANCM 同一プロセス内の複数のインプロセス アプリケーション)</span><span class="sxs-lookup"><span data-stu-id="bf733-208">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="bf733-209">ワーカープロセスでは、同じプロセスで複数のインプロセスアプリを実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="bf733-209">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="bf733-210">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-210">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="bf733-211">500.36 ANCM Out-Of-Process Handler Load Failure (500.36 ANCM アウト プロセス ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="bf733-211">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="bf733-212">アウト プロセス要求ハンドラーの *aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* ファイルの次にありません。</span><span class="sxs-lookup"><span data-stu-id="bf733-212">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="bf733-213">これは、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)のインストールが破損していることを示します。</span><span class="sxs-lookup"><span data-stu-id="bf733-213">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="bf733-214">このエラーを修正するには、[.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS 用) または Visual Studio (IIS Express 用) のインストールを修復します。</span><span class="sxs-lookup"><span data-stu-id="bf733-214">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="bf733-215">500.37 ANCM Failed to Start Within Startup Time Limit (500.37 ANCM スタートアップ時間の制限内に起動できませんでした)</span><span class="sxs-lookup"><span data-stu-id="bf733-215">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="bf733-216">指定されたスタートアップ時間の制限内に ANCM が起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="bf733-216">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="bf733-217">既定では、タイムアウトは 120 秒です。</span><span class="sxs-lookup"><span data-stu-id="bf733-217">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="bf733-218">このエラーは、同じマシン上で多数のアプリを起動したときに発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-218">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="bf733-219">スタートアップ中のサーバー上の CPU/メモリ使用量の急上昇を確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-219">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="bf733-220">必要に応じて、複数のアプリのスタートアップ プロセスをずらします。</span><span class="sxs-lookup"><span data-stu-id="bf733-220">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="bf733-221">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="bf733-221">502.5 Process Failure</span></span>

<span data-ttu-id="bf733-222">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-222">The worker process fails.</span></span> <span data-ttu-id="bf733-223">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="bf733-223">The app doesn't start.</span></span>

<span data-ttu-id="bf733-224">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="bf733-224">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="bf733-225">プロセス起動エラーの原因は、通常、アプリケーションイベントログのエントリと、ASP.NET Core モジュールの stdout ログによって決まります。</span><span class="sxs-lookup"><span data-stu-id="bf733-225">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="bf733-226">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="bf733-226">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="bf733-227">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-227">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="bf733-228">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-228">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="bf733-229">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="bf733-229">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="bf733-230">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bf733-230">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="bf733-231">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-231">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="bf733-232">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="bf733-232">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="bf733-233">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="bf733-233">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="bf733-234">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="bf733-234">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="bf733-235">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-235">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="bf733-236">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="bf733-236">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="bf733-237">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="bf733-237">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="bf733-238">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="bf733-238">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="bf733-239">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="bf733-239">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="bf733-240">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="bf733-240">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="bf733-241">プロジェクトファイルの `<Platform>` MSBuild プロパティとアプリの発行済みビットとの間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-241">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="bf733-242">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="bf733-242">Connection reset</span></span>

<span data-ttu-id="bf733-243">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="bf733-243">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="bf733-244">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="bf733-244">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="bf733-245">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-245">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="bf733-246">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="bf733-246">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="bf733-247">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="bf733-247">Default startup limits</span></span>

<span data-ttu-id="bf733-248">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は、120秒の既定の*startupTimeLimit*を使用して構成されています。</span><span class="sxs-lookup"><span data-stu-id="bf733-248">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="bf733-249">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="bf733-249">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="bf733-250">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-250">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="bf733-251">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-251">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="bf733-252">アプリケーションイベントログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="bf733-252">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="bf733-253">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="bf733-253">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="bf733-254">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-254">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="bf733-255">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="bf733-255">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="bf733-256">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="bf733-256">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="bf733-257">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="bf733-257">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="bf733-258">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="bf733-258">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="bf733-259">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="bf733-259">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="bf733-260">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-260">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="bf733-261">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-261">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="bf733-262">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-262">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="bf733-263">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-263">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="bf733-264">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-264">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="bf733-265">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-265">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="bf733-266">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="bf733-266">Examine the log.</span></span> <span data-ttu-id="bf733-267">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-267">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="bf733-268">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-268">Run the app in the Kudu console</span></span>

<span data-ttu-id="bf733-269">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="bf733-269">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="bf733-270">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="bf733-270">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="bf733-271">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-271">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="bf733-272">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-272">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="bf733-273">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-273">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="bf733-274">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-274">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="bf733-275">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="bf733-275">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="bf733-276">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="bf733-276">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="bf733-277">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-277">Run the app:</span></span>
   * <span data-ttu-id="bf733-278">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="bf733-278">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="bf733-279">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="bf733-279">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="bf733-280">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="bf733-280">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="bf733-281">**プレビューリリースで実行されているフレームワークに依存する配置**</span><span class="sxs-lookup"><span data-stu-id="bf733-281">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="bf733-282">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="bf733-282">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="bf733-283">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="bf733-283">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="bf733-284">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="bf733-284">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="bf733-285">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="bf733-285">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="bf733-286">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="bf733-286">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="bf733-287">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="bf733-287">**Current release**</span></span>

* <span data-ttu-id="bf733-288">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="bf733-288">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="bf733-289">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="bf733-289">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="bf733-290">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="bf733-290">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="bf733-291">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="bf733-291">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="bf733-292">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="bf733-292">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="bf733-293">**プレビューリリースで実行されているフレームワークに依存する配置**</span><span class="sxs-lookup"><span data-stu-id="bf733-293">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="bf733-294">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="bf733-294">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="bf733-295">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="bf733-295">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="bf733-296">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="bf733-296">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="bf733-297">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="bf733-297">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="bf733-298">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="bf733-298">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="bf733-299">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="bf733-299">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="bf733-300">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="bf733-300">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="bf733-301">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="bf733-301">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="bf733-302">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-302">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="bf733-303">**[Suggested Solutions]\(推奨される解決方法\)**  >  **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、ボタンをクリックして **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-303">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="bf733-304">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-304">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="bf733-305">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="bf733-305">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="bf733-306">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="bf733-306">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="bf733-307">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="bf733-307">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="bf733-308">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="bf733-308">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="bf733-309">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="bf733-309">Make a request to the app.</span></span>
1. <span data-ttu-id="bf733-310">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="bf733-310">Return to the Azure portal.</span></span> <span data-ttu-id="bf733-311">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-311">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="bf733-312">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-312">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="bf733-313">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-313">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="bf733-314">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-314">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="bf733-315">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-315">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="bf733-316">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="bf733-316">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="bf733-317">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-317">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="bf733-318">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="bf733-318">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="bf733-319">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="bf733-319">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="bf733-320">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-320">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="bf733-321">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="bf733-321">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="bf733-322">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="bf733-322">Select **Save** to save the file.</span></span>

<span data-ttu-id="bf733-323">詳細については、「 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-323">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="bf733-324">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-324">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="bf733-325">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="bf733-325">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="bf733-326">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="bf733-326">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="bf733-327">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="bf733-327">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="bf733-328">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-328">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="bf733-329">ASP.NET Core モジュールデバッグログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="bf733-329">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="bf733-330">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-330">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="bf733-331">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="bf733-331">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="bf733-332">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-332">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="bf733-333">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="bf733-333">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="bf733-334">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="bf733-334">Redeploy the app.</span></span>
   * <span data-ttu-id="bf733-335">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="bf733-335">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="bf733-336">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-336">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="bf733-337">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-337">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="bf733-338">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-338">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="bf733-339">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-339">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="bf733-340">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-340">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="bf733-341">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="bf733-341">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="bf733-342">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="bf733-342">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="bf733-343">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="bf733-343">Select the **Save** button.</span></span>
1. <span data-ttu-id="bf733-344">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-344">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="bf733-345">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-345">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="bf733-346">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-346">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="bf733-347">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-347">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="bf733-348">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-348">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="bf733-349">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-349">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="bf733-350">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="bf733-350">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="bf733-351">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-351">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="bf733-352">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="bf733-352">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="bf733-353">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-353">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="bf733-354">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="bf733-354">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="bf733-355">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="bf733-355">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="bf733-356">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="bf733-356">Save the file.</span></span>

<span data-ttu-id="bf733-357">詳細については、「 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-357">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="bf733-358">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-358">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="bf733-359">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="bf733-359">There's no limit on log file size.</span></span> <span data-ttu-id="bf733-360">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="bf733-360">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="bf733-361">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="bf733-361">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="bf733-362">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-362">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="bf733-363">低速またはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="bf733-363">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="bf733-364">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-364">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="bf733-365">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-365">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="bf733-366">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="bf733-366">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="bf733-367">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="bf733-367">Monitoring blades</span></span>

<span data-ttu-id="bf733-368">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-368">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="bf733-369">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="bf733-369">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="bf733-370">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-370">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="bf733-371">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="bf733-371">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="bf733-372">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-372">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="bf733-373">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-373">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="bf733-374">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-374">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="bf733-375">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-375">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="bf733-376">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="bf733-376">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="bf733-377">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-377">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="bf733-378">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-378">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="bf733-379">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="bf733-379">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="bf733-380">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-380">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="bf733-381">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-381">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="bf733-382">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-382">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="bf733-383">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-383">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="bf733-384">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="bf733-384">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="bf733-385">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="bf733-385">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="bf733-386">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="bf733-386">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="bf733-387">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="bf733-387">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="bf733-388">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="bf733-388">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="bf733-389">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-389">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="bf733-390">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-390">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="bf733-391">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-391">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="bf733-392">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-392">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="bf733-393">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-393">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="bf733-394">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="bf733-394">Make a request to the app.</span></span>
1. <span data-ttu-id="bf733-395">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-395">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="bf733-396">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="bf733-396">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="bf733-397">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="bf733-397">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="bf733-398">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="bf733-398">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="bf733-399">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="bf733-399">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="bf733-400">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bf733-400">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="bf733-401">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bf733-401">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="bf733-402">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-402">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="bf733-403">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="bf733-403">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="bf733-404">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="bf733-404">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="bf733-405">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-405">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="bf733-406">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-406">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="bf733-407">アプリケーションイベントログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="bf733-407">Application Event Log (IIS)</span></span>

<span data-ttu-id="bf733-408">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="bf733-408">Access the Application Event Log:</span></span>

1. <span data-ttu-id="bf733-409">[スタート] メニューを開き、*イベントビューアー*を検索して、**イベントビューアー**アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="bf733-409">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="bf733-410">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-410">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="bf733-411">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-411">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="bf733-412">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="bf733-412">Search for errors associated with the failing app.</span></span> <span data-ttu-id="bf733-413">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-413">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="bf733-414">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="bf733-414">Run the app at a command prompt</span></span>

<span data-ttu-id="bf733-415">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="bf733-415">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="bf733-416">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="bf733-416">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="bf733-417">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="bf733-417">Framework-dependent deployment</span></span>

<span data-ttu-id="bf733-418">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="bf733-418">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="bf733-419">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-419">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="bf733-420">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-420">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="bf733-421">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="bf733-421">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="bf733-422">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-422">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="bf733-423">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="bf733-423">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="bf733-424">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="bf733-424">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="bf733-425">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="bf733-425">Self-contained deployment</span></span>

<span data-ttu-id="bf733-426">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="bf733-426">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="bf733-427">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-427">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="bf733-428">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-428">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="bf733-429">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="bf733-429">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="bf733-430">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-430">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="bf733-431">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="bf733-431">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="bf733-432">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="bf733-432">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="bf733-433">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="bf733-433">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="bf733-434">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="bf733-434">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="bf733-435">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="bf733-435">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="bf733-436">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="bf733-436">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="bf733-437">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-437">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="bf733-438">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="bf733-438">Edit the *web.config* file.</span></span> <span data-ttu-id="bf733-439">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="bf733-439">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="bf733-440">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="bf733-440">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="bf733-441">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-441">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="bf733-442">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="bf733-442">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="bf733-443">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-443">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="bf733-444">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="bf733-444">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="bf733-445">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="bf733-445">Make a request to the app.</span></span>
1. <span data-ttu-id="bf733-446">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="bf733-446">Navigate to the *logs* folder.</span></span> <span data-ttu-id="bf733-447">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="bf733-447">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="bf733-448">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="bf733-448">Study the log for errors.</span></span>

<span data-ttu-id="bf733-449">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="bf733-449">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="bf733-450">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="bf733-450">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="bf733-451">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="bf733-451">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="bf733-452">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="bf733-452">Save the file.</span></span>

<span data-ttu-id="bf733-453">詳細については、「 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-453">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="bf733-454">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-454">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="bf733-455">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="bf733-455">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="bf733-456">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="bf733-456">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="bf733-457">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-457">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="bf733-458">ASP.NET Core モジュールデバッグログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="bf733-458">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="bf733-459">次のハンドラー設定をアプリの*web.config*ファイルに追加して、ASP.NET Core モジュールのデバッグログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="bf733-459">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="bf733-460">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf733-460">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="bf733-461">詳細については、「 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-461">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

::: moniker-end

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="bf733-462">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="bf733-462">Enable the Developer Exception Page</span></span>

<span data-ttu-id="bf733-463">`ASPNETCORE_ENVIRONMENT`[環境変数を web.config に追加](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)して、開発環境でアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="bf733-463">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="bf733-464">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-464">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="bf733-465">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="bf733-465">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="bf733-466">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-466">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="bf733-467">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-467">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="bf733-468">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="bf733-468">Obtain data from an app</span></span>

<span data-ttu-id="bf733-469">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="bf733-469">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="bf733-470">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-470">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="bf733-471">低速またはハング中のアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="bf733-471">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="bf733-472">*クラッシュダンプ*は、システムのメモリのスナップショットであり、アプリのクラッシュ、スタートアップエラー、または低速アプリの原因を特定するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="bf733-472">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="bf733-473">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="bf733-473">App crashes or encounters an exception</span></span>

<span data-ttu-id="bf733-474">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="bf733-474">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="bf733-475">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="bf733-475">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="bf733-476">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="bf733-476">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="bf733-477">[EnableDumps PowerShell スクリプト](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-477">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="bf733-478">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-478">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="bf733-479">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-479">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="bf733-480">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-480">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="bf733-481">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-481">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="bf733-482">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-482">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="bf733-483">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf733-483">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="bf733-484">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="bf733-484">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="bf733-485">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="bf733-485">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="bf733-486">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-486">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="bf733-487">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="bf733-487">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="bf733-488">アプリが*ハング*した (応答が停止してもクラッシュしない) 場合、起動時に失敗した場合、または正常に実行された場合は、「[ユーザーモードのダンプファイル:](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)ダンプを生成する適切なツールを選択する最適なツールを選択する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-488">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="bf733-489">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="bf733-489">Analyze the dump</span></span>

<span data-ttu-id="bf733-490">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="bf733-490">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="bf733-491">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf733-491">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="bf733-492">パッケージキャッシュのクリア</span><span class="sxs-lookup"><span data-stu-id="bf733-492">Clear package caches</span></span>

<span data-ttu-id="bf733-493">開発用コンピューターの .NET Core SDK をアップグレードした後、またはアプリ内のパッケージバージョンを変更した直後に、機能しているアプリが失敗する場合があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-493">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="bf733-494">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="bf733-494">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="bf733-495">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="bf733-495">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="bf733-496">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="bf733-496">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="bf733-497">コマンドシェルから[dotnet nuget ローカルのすべて](/dotnet/core/tools/dotnet-nuget-locals)を実行して、パッケージのキャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="bf733-497">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="bf733-498">パッケージキャッシュのクリアは、 [nuget.exe](https://www.nuget.org/downloads)ツールを使用して実行し、コマンド `nuget locals all -clear`を実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="bf733-498">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="bf733-499">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf733-499">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="bf733-500">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="bf733-500">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="bf733-501">アプリケーションを再展開する前に、サーバー上の展開フォルダーにあるすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="bf733-501">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf733-502">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bf733-502">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="bf733-503">Azure ドキュメント</span><span class="sxs-lookup"><span data-stu-id="bf733-503">Azure documentation</span></span>

* [<span data-ttu-id="bf733-504">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="bf733-504">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="bf733-505">「Visual Studio を使用した Azure App Service での web アプリのトラブルシューティング」の「web アプリのリモートデバッグ」セクション</span><span class="sxs-lookup"><span data-stu-id="bf733-505">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="bf733-506">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="bf733-506">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="bf733-507">Azure App Service でアプリを監視する方法</span><span class="sxs-lookup"><span data-stu-id="bf733-507">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="bf733-508">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-508">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="bf733-509">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-509">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="bf733-510">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bf733-510">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="bf733-511">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="bf733-511">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="bf733-512">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="bf733-512">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="bf733-513">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="bf733-513">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="bf733-514">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="bf733-514">Visual Studio documentation</span></span>

* [<span data-ttu-id="bf733-515">Visual Studio 2017 での Azure での IIS のリモートデバッグ ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf733-515">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="bf733-516">Visual Studio 2017 のリモート IIS コンピューター上のリモートデバッグ ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf733-516">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="bf733-517">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="bf733-517">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="bf733-518">Visual Studio Code のドキュメント</span><span class="sxs-lookup"><span data-stu-id="bf733-518">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="bf733-519">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="bf733-519">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)
