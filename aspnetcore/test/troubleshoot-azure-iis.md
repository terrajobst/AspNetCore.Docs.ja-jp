---
title: Azure App Service および IIS での ASP.NET Core のトラブルシューティング
author: rick-anderson
description: ASP.NET Core アプリの Azure App Service およびインターネット インフォメーション サービス (IIS) のデプロイに関する問題を診断する方法について学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 671f68da2ea261cb8ae32a9d5ef875217859054d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78644882"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="2875e-103">Azure App Service および IIS での ASP.NET Core のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="2875e-104">作成者: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="2875e-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2875e-105">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="2875e-106">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="2875e-107">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2875e-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="2875e-108">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="2875e-109">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="2875e-110">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="2875e-111">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="2875e-112">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="2875e-113">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="2875e-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="2875e-114">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2875e-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="2875e-115">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2875e-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="2875e-116">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="2875e-117">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-117">App startup errors</span></span>

<span data-ttu-id="2875e-118">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="2875e-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="2875e-119">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 - 処理エラー*" または "*500.30 - 開始エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="2875e-120">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="2875e-120">403.14 Forbidden</span></span>

<span data-ttu-id="2875e-121">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2875e-121">The app fails to start.</span></span> <span data-ttu-id="2875e-122">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="2875e-123">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="2875e-124">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="2875e-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="2875e-125">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="2875e-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="2875e-126">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="2875e-127">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="2875e-127">Perform the following steps:</span></span>

1. <span data-ttu-id="2875e-128">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="2875e-129">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="2875e-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="2875e-130">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="2875e-131">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="2875e-132">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="2875e-133">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="2875e-134">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="2875e-135">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="2875e-136">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="2875e-136">500 Internal Server Error</span></span>

<span data-ttu-id="2875e-137">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="2875e-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="2875e-138">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="2875e-139">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="2875e-140">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="2875e-141">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="2875e-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="2875e-142">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="2875e-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="2875e-143">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="2875e-144">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="2875e-145">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-145">The worker process fails.</span></span> <span data-ttu-id="2875e-146">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-146">The app doesn't start.</span></span>

<span data-ttu-id="2875e-147">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module) コンポーネントの読み込み中に不明なエラーが発生しました。</span><span class="sxs-lookup"><span data-stu-id="2875e-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="2875e-148">次のいずれかのアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-148">Take one of the following actions:</span></span>

* <span data-ttu-id="2875e-149">[Microsoft サポート](https://support.microsoft.com/oas/default.aspx?prid=15832)に問い合わせます ( **[開発者ツール]** 、 **[ASP.NET Core]** の順に選択します)。</span><span class="sxs-lookup"><span data-stu-id="2875e-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="2875e-150">Stack Overflow について質問します。</span><span class="sxs-lookup"><span data-stu-id="2875e-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="2875e-151">[GitHub リポジトリ](https://github.com/dotnet/AspNetCore)で問題を報告します。</span><span class="sxs-lookup"><span data-stu-id="2875e-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="2875e-152">500.30 インプロセス起動エラー</span><span class="sxs-lookup"><span data-stu-id="2875e-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="2875e-153">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-153">The worker process fails.</span></span> <span data-ttu-id="2875e-154">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-154">The app doesn't start.</span></span>

<span data-ttu-id="2875e-155">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .NET Core CLR の開始をインプロセスで試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="2875e-156">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="2875e-157">一般的なエラー条件:</span><span class="sxs-lookup"><span data-stu-id="2875e-157">Common failure conditions:</span></span>

* <span data-ttu-id="2875e-158">存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="2875e-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="2875e-159">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="2875e-160">Azure Key Vault を使用している場合、Key Vault へのアクセス許可がありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="2875e-161">対象の Key Vault のアクセス ポリシーを確認して、正しいアクセス許可が付与されていることを確実にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="2875e-162">500.31 ANCM Failed to Find Native Dependencies (500.31 ANCM ネイティブの依存関係を見つけられませんでした)</span><span class="sxs-lookup"><span data-stu-id="2875e-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="2875e-163">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-163">The worker process fails.</span></span> <span data-ttu-id="2875e-164">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-164">The app doesn't start.</span></span>

<span data-ttu-id="2875e-165">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)で .NET Core ランタイムの開始がインプロセスで試行されますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="2875e-166">このスタートアップ エラーの最も一般的な原因は、`Microsoft.NETCore.App` または `Microsoft.AspNetCore.App` ランタイムがインストールされていない場合です。</span><span class="sxs-lookup"><span data-stu-id="2875e-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="2875e-167">アプリが ASP.NET Core 3.0 をターゲットとして展開されていて、そのバージョンがコンピューターに存在しない場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="2875e-168">エラー メッセージの例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2875e-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="2875e-169">エラー メッセージには、インストールされているすべての .NET Core のバージョンとアプリに必要なバージョンが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="2875e-170">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-170">To fix this error, either:</span></span>

* <span data-ttu-id="2875e-171">適切なバージョンの .NET Core をマシンにインストールします。</span><span class="sxs-lookup"><span data-stu-id="2875e-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="2875e-172">マシンに存在する .NET Core のバージョンをターゲットにするようにアプリを変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="2875e-173">アプリを[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="2875e-174">開発環境で実行している場合 (`ASPNETCORE_ENVIRONMENT` 環境変数が `Development` に設定されている場合)、特定のエラーが HTTP 応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="2875e-175">プロセスのスタートアップ エラーの原因は、アプリケーション イベント ログでも見つかります。</span><span class="sxs-lookup"><span data-stu-id="2875e-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="2875e-176">500.32 ANCM Failed to Load dll (500.32 ANCM DLL を読み込めませんでした)</span><span class="sxs-lookup"><span data-stu-id="2875e-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="2875e-177">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-177">The worker process fails.</span></span> <span data-ttu-id="2875e-178">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-178">The app doesn't start.</span></span>

<span data-ttu-id="2875e-179">このエラーの最も一般的な原因は、アプリが互換性のないプロセッサ アーキテクチャ用に発行されていることです。</span><span class="sxs-lookup"><span data-stu-id="2875e-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="2875e-180">ワーカー プロセスが 32 ビットアプリとして実行されていて、そのアプリが 64 ビットをターゲットとして発行されている場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="2875e-181">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-181">To fix this error, either:</span></span>

* <span data-ttu-id="2875e-182">ワーカー プロセスと同じプロセッサ アーキテクチャ用にアプリを再発行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="2875e-183">アプリを[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-executables-fde)として発行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="2875e-184">500.33 ANCM Request Handler Load Failure (500.33 ANCM 要求ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="2875e-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="2875e-185">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-185">The worker process fails.</span></span> <span data-ttu-id="2875e-186">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-186">The app doesn't start.</span></span>

<span data-ttu-id="2875e-187">アプリは `Microsoft.AspNetCore.App` フレームワークを参照していませんでした。</span><span class="sxs-lookup"><span data-stu-id="2875e-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="2875e-188">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)でホストできるのは、`Microsoft.AspNetCore.App` フレームワークをターゲットとしているアプリのみです。</span><span class="sxs-lookup"><span data-stu-id="2875e-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="2875e-189">このエラーを修正するには、アプリが `Microsoft.AspNetCore.App` フレームワークをターゲットにしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="2875e-190">アプリがターゲットとしているフレームワークを確認するには、`.runtimeconfig.json` を確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="2875e-191">500.34 ANCM Mixed Hosting Models Not Supported (500.34 ANCM 混在ホスティング モデルはサポートされません)</span><span class="sxs-lookup"><span data-stu-id="2875e-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="2875e-192">ワーカー プロセスでは、インプロセス アプリとアウト プロセス アプリの両方を同じプロセスで実行できません。</span><span class="sxs-lookup"><span data-stu-id="2875e-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="2875e-193">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="2875e-194">500.35 ANCM Multiple In-Process Applications in same Process (500.35 ANCM 同一プロセス内の複数のインプロセス アプリケーション)</span><span class="sxs-lookup"><span data-stu-id="2875e-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="2875e-195">ワーカー プロセスによって、同じプロセスで複数のインプロセス アプリを実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="2875e-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="2875e-196">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="2875e-197">500.36 ANCM Out-Of-Process Handler Load Failure (500.36 ANCM アウト プロセス ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="2875e-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="2875e-198">アウト プロセス要求ハンドラーの *aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* ファイルの次にありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="2875e-199">これは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)のインストールが破損していることを示しています。</span><span class="sxs-lookup"><span data-stu-id="2875e-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="2875e-200">このエラーを修正するには、[.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS 用) または Visual Studio (IIS Express 用) のインストールを修復します。</span><span class="sxs-lookup"><span data-stu-id="2875e-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="2875e-201">500.37 ANCM Failed to Start Within Startup Time Limit (500.37 ANCM スタートアップ時間の制限内に起動できませんでした)</span><span class="sxs-lookup"><span data-stu-id="2875e-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="2875e-202">指定されたスタートアップ時間の制限内に ANCM が起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2875e-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="2875e-203">既定では、タイムアウトは 120 秒です。</span><span class="sxs-lookup"><span data-stu-id="2875e-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="2875e-204">このエラーは、同じマシン上で多数のアプリを起動したときに発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="2875e-205">スタートアップ中のサーバー上の CPU/メモリ使用量の急上昇を確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="2875e-206">必要に応じて、複数のアプリのスタートアップ プロセスをずらします。</span><span class="sxs-lookup"><span data-stu-id="2875e-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="2875e-207">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="2875e-207">502.5 Process Failure</span></span>

<span data-ttu-id="2875e-208">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-208">The worker process fails.</span></span> <span data-ttu-id="2875e-209">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-209">The app doesn't start.</span></span>

<span data-ttu-id="2875e-210">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-210">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="2875e-211">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-211">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="2875e-212">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="2875e-212">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="2875e-213">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-213">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="2875e-214">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-214">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="2875e-215">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-215">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="2875e-216">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-216">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="2875e-217">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-217">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="2875e-218">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="2875e-218">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="2875e-219">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2875e-219">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="2875e-220">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-220">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="2875e-221">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-221">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="2875e-222">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-222">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="2875e-223">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-223">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="2875e-224">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-224">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="2875e-225">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-225">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="2875e-226">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-226">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="2875e-227">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-227">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="2875e-228">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="2875e-228">Connection reset</span></span>

<span data-ttu-id="2875e-229">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="2875e-229">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="2875e-230">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-230">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="2875e-231">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-231">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="2875e-232">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-232">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="2875e-233">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="2875e-233">Default startup limits</span></span>

<span data-ttu-id="2875e-234">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-234">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="2875e-235">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="2875e-235">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="2875e-236">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-236">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="2875e-237">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-237">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="2875e-238">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-238">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="2875e-239">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-239">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="2875e-240">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-240">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="2875e-241">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-241">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="2875e-242">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-242">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="2875e-243">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-243">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="2875e-244">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-244">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="2875e-245">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="2875e-245">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="2875e-246">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-246">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-247">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-247">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-248">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-248">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-249">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-249">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-250">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-250">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="2875e-251">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-251">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="2875e-252">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-252">Examine the log.</span></span> <span data-ttu-id="2875e-253">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-253">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="2875e-254">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-254">Run the app in the Kudu console</span></span>

<span data-ttu-id="2875e-255">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="2875e-255">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="2875e-256">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="2875e-256">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="2875e-257">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-257">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-258">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-258">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-259">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-259">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-260">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-260">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="2875e-261">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="2875e-261">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="2875e-262">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="2875e-262">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="2875e-263">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-263">Run the app:</span></span>
   * <span data-ttu-id="2875e-264">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-264">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="2875e-265">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-265">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="2875e-266">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-266">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="2875e-267">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="2875e-267">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="2875e-268">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="2875e-268">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="2875e-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="2875e-269">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="2875e-270">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-270">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="2875e-271">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-271">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="2875e-272">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="2875e-272">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="2875e-273">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="2875e-273">**Current release**</span></span>

* <span data-ttu-id="2875e-274">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-274">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="2875e-275">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-275">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="2875e-276">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-276">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="2875e-277">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="2875e-277">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="2875e-278">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-278">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="2875e-279">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="2875e-279">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="2875e-280">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="2875e-280">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="2875e-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="2875e-281">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="2875e-282">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-282">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="2875e-283">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-283">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="2875e-284">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-284">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="2875e-285">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-285">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="2875e-286">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="2875e-286">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2875e-287">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-287">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="2875e-288">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-288">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="2875e-289">**[推奨されている解決方法]** > **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、 **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-289">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="2875e-290">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-290">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2875e-291">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-291">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="2875e-292">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2875e-292">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-293">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-293">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="2875e-294">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-294">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="2875e-295">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-295">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-296">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="2875e-296">Return to the Azure portal.</span></span> <span data-ttu-id="2875e-297">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-297">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="2875e-298">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-298">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-299">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-299">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-300">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-300">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-301">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-301">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="2875e-302">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-302">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="2875e-303">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-303">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="2875e-304">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-304">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="2875e-305">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-305">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="2875e-306">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-306">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="2875e-307">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-307">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="2875e-308">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-308">Select **Save** to save the file.</span></span>

<span data-ttu-id="2875e-309">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-309">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-310">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-310">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-311">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-311">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="2875e-312">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="2875e-312">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="2875e-313">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-313">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-314">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-314">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="2875e-315">ASP.NET Core モジュールのデバッグ ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-315">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="2875e-316">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-316">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="2875e-317">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="2875e-317">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2875e-318">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-318">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="2875e-319">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-319">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="2875e-320">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="2875e-320">Redeploy the app.</span></span>
   * <span data-ttu-id="2875e-321">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2875e-321">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="2875e-322">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-322">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-323">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-323">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-324">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-324">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="2875e-325">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-325">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="2875e-326">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-326">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2875e-327">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-327">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="2875e-328">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="2875e-328">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="2875e-329">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-329">Select the **Save** button.</span></span>
1. <span data-ttu-id="2875e-330">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-331">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-332">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-332">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-333">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-334">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2875e-335">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-335">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="2875e-336">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-336">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="2875e-337">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-337">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="2875e-338">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-338">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="2875e-339">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-339">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="2875e-340">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="2875e-340">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="2875e-341">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-341">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="2875e-342">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-342">Save the file.</span></span>

<span data-ttu-id="2875e-343">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-343">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-344">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-344">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-345">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-345">There's no limit on log file size.</span></span> <span data-ttu-id="2875e-346">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="2875e-346">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="2875e-347">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-347">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-348">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-348">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="2875e-349">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-349">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="2875e-350">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-350">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="2875e-351">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-351">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="2875e-352">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="2875e-352">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="2875e-353">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="2875e-353">Monitoring blades</span></span>

<span data-ttu-id="2875e-354">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-354">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="2875e-355">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-355">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="2875e-356">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-356">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="2875e-357">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="2875e-357">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="2875e-358">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-358">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="2875e-359">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-359">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="2875e-360">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-360">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="2875e-361">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-361">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="2875e-362">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="2875e-362">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="2875e-363">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-363">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="2875e-364">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-364">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="2875e-365">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="2875e-365">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="2875e-366">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-366">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="2875e-367">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-367">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-368">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-368">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-369">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-369">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-370">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-370">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="2875e-371">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2875e-371">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-372">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-372">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="2875e-373">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-373">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="2875e-374">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="2875e-374">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="2875e-375">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-375">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="2875e-376">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-376">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="2875e-377">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-377">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="2875e-378">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-378">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="2875e-379">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-379">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="2875e-380">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-380">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-381">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-381">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="2875e-382">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="2875e-382">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="2875e-383">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="2875e-383">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="2875e-384">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-384">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="2875e-385">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-385">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="2875e-386">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-386">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="2875e-387">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-387">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-388">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-388">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-389">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-389">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="2875e-390">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-390">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-391">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-391">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="2875e-392">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-392">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="2875e-393">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-393">Application Event Log (IIS)</span></span>

<span data-ttu-id="2875e-394">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2875e-394">Access the Application Event Log:</span></span>

1. <span data-ttu-id="2875e-395">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-395">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="2875e-396">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-396">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="2875e-397">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-397">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="2875e-398">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="2875e-398">Search for errors associated with the failing app.</span></span> <span data-ttu-id="2875e-399">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-399">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="2875e-400">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="2875e-400">Run the app at a command prompt</span></span>

<span data-ttu-id="2875e-401">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="2875e-401">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="2875e-402">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-402">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="2875e-403">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="2875e-403">Framework-dependent deployment</span></span>

<span data-ttu-id="2875e-404">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-404">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="2875e-405">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-405">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="2875e-406">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-406">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="2875e-407">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-407">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="2875e-408">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-408">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="2875e-409">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="2875e-409">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="2875e-410">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="2875e-410">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="2875e-411">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="2875e-411">Self-contained deployment</span></span>

<span data-ttu-id="2875e-412">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-412">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="2875e-413">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-413">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="2875e-414">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="2875e-415">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="2875e-416">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="2875e-417">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="2875e-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="2875e-418">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="2875e-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="2875e-419">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-419">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="2875e-420">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="2875e-420">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2875e-421">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-421">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="2875e-422">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-422">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="2875e-423">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-423">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="2875e-424">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-424">Edit the *web.config* file.</span></span> <span data-ttu-id="2875e-425">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-425">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="2875e-426">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="2875e-426">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="2875e-427">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-427">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="2875e-428">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="2875e-428">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="2875e-429">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-429">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="2875e-430">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-430">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="2875e-431">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-431">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-432">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-432">Navigate to the *logs* folder.</span></span> <span data-ttu-id="2875e-433">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-433">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="2875e-434">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-434">Study the log for errors.</span></span>

<span data-ttu-id="2875e-435">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-435">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="2875e-436">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-436">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-437">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-437">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="2875e-438">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-438">Save the file.</span></span>

<span data-ttu-id="2875e-439">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-439">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-440">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-440">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-441">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-441">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="2875e-442">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-442">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-443">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-443">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="2875e-444">ASP.NET Core モジュールのデバッグ ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-444">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="2875e-445">ASP.NET Core モジュールのデバッグ ログを有効にするには、次のハンドラー設定をアプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2875e-445">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="2875e-446">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-446">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="2875e-447">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-447">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="2875e-448">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="2875e-448">Enable the Developer Exception Page</span></span>

<span data-ttu-id="2875e-449">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="2875e-449">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="2875e-450">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-450">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="2875e-451">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-451">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="2875e-452">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-452">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="2875e-453">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-453">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="2875e-454">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="2875e-454">Obtain data from an app</span></span>

<span data-ttu-id="2875e-455">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="2875e-455">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="2875e-456">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-456">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="2875e-457">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-457">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="2875e-458">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2875e-458">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="2875e-459">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="2875e-459">App crashes or encounters an exception</span></span>

<span data-ttu-id="2875e-460">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="2875e-460">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="2875e-461">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-461">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="2875e-462">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="2875e-462">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="2875e-463">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-463">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="2875e-464">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-464">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="2875e-465">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-465">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="2875e-466">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-466">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="2875e-467">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-467">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="2875e-468">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-468">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="2875e-469">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-469">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="2875e-470">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="2875e-470">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="2875e-471">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-471">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-472">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-472">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="2875e-473">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="2875e-473">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="2875e-474">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-474">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="2875e-475">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="2875e-475">Analyze the dump</span></span>

<span data-ttu-id="2875e-476">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-476">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="2875e-477">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-477">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="2875e-478">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="2875e-478">Clear package caches</span></span>

<span data-ttu-id="2875e-479">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-479">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="2875e-480">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-480">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="2875e-481">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-481">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="2875e-482">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-482">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="2875e-483">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="2875e-483">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="2875e-484">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-484">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="2875e-485">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-485">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="2875e-486">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="2875e-486">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="2875e-487">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-487">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2875e-488">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2875e-488">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="2875e-489">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-489">Azure documentation</span></span>

* [<span data-ttu-id="2875e-490">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="2875e-490">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="2875e-491">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="2875e-491">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="2875e-492">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="2875e-492">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="2875e-493">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="2875e-493">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="2875e-494">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-494">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="2875e-495">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-495">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="2875e-496">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-496">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="2875e-497">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="2875e-497">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="2875e-498">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="2875e-498">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="2875e-499">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="2875e-499">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="2875e-500">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-500">Visual Studio documentation</span></span>

* [<span data-ttu-id="2875e-501">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-501">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="2875e-502">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-502">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="2875e-503">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="2875e-503">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="2875e-504">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-504">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="2875e-505">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-505">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2875e-506">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-506">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="2875e-507">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-507">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="2875e-508">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2875e-508">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="2875e-509">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-509">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="2875e-510">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-510">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="2875e-511">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-511">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="2875e-512">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-512">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="2875e-513">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-513">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="2875e-514">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="2875e-514">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="2875e-515">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2875e-515">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="2875e-516">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2875e-516">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="2875e-517">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-517">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="2875e-518">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-518">App startup errors</span></span>

<span data-ttu-id="2875e-519">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="2875e-519">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="2875e-520">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 - 処理エラー*" または "*500.30 - 開始エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-520">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="2875e-521">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="2875e-521">403.14 Forbidden</span></span>

<span data-ttu-id="2875e-522">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2875e-522">The app fails to start.</span></span> <span data-ttu-id="2875e-523">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-523">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="2875e-524">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-524">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="2875e-525">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="2875e-525">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="2875e-526">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="2875e-526">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="2875e-527">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-527">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="2875e-528">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="2875e-528">Perform the following steps:</span></span>

1. <span data-ttu-id="2875e-529">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-529">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="2875e-530">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="2875e-530">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="2875e-531">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-531">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="2875e-532">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-532">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="2875e-533">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-533">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="2875e-534">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-534">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="2875e-535">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-535">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="2875e-536">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-536">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="2875e-537">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="2875e-537">500 Internal Server Error</span></span>

<span data-ttu-id="2875e-538">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="2875e-538">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="2875e-539">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-539">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="2875e-540">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-540">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="2875e-541">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-541">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="2875e-542">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="2875e-542">From the server's perspective, that's correct.</span></span> <span data-ttu-id="2875e-543">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="2875e-543">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="2875e-544">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-544">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="2875e-545">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-545">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="2875e-546">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-546">The worker process fails.</span></span> <span data-ttu-id="2875e-547">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-547">The app doesn't start.</span></span>

<span data-ttu-id="2875e-548">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .NET Core CLR の検出に失敗し、インプロセス要求ハンドラー (*aspnetcorev2_inprocess.dll*) を検出します。</span><span class="sxs-lookup"><span data-stu-id="2875e-548">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="2875e-549">次の点をご確認ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-549">Check that:</span></span>

* <span data-ttu-id="2875e-550">アプリが [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet パッケージまたは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を対象としている。</span><span class="sxs-lookup"><span data-stu-id="2875e-550">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="2875e-551">アプリが対象としているバージョンの ASP.NET Core 共有フレームワークが対象のコンピューターにインストールされている。</span><span class="sxs-lookup"><span data-stu-id="2875e-551">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="2875e-552">500.0 アウト プロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-552">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="2875e-553">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-553">The worker process fails.</span></span> <span data-ttu-id="2875e-554">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-554">The app doesn't start.</span></span>

<span data-ttu-id="2875e-555">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は、アウト プロセスのホスティング要求ハンドラーの検索に失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-555">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="2875e-556">*aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* の隣のサブフォルダーにあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-556">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="2875e-557">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="2875e-557">502.5 Process Failure</span></span>

<span data-ttu-id="2875e-558">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-558">The worker process fails.</span></span> <span data-ttu-id="2875e-559">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-559">The app doesn't start.</span></span>

<span data-ttu-id="2875e-560">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-560">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="2875e-561">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-561">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="2875e-562">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="2875e-562">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="2875e-563">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-563">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="2875e-564">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-564">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="2875e-565">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-565">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="2875e-566">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-566">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="2875e-567">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-567">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="2875e-568">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="2875e-568">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="2875e-569">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2875e-569">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="2875e-570">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-570">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="2875e-571">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-571">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="2875e-572">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-572">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="2875e-573">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-573">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="2875e-574">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-574">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="2875e-575">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-575">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="2875e-576">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-576">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="2875e-577">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-577">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="2875e-578">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="2875e-578">Connection reset</span></span>

<span data-ttu-id="2875e-579">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="2875e-579">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="2875e-580">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-580">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="2875e-581">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-581">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="2875e-582">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-582">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="2875e-583">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="2875e-583">Default startup limits</span></span>

<span data-ttu-id="2875e-584">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-584">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="2875e-585">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="2875e-585">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="2875e-586">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-586">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="2875e-587">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-587">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="2875e-588">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-588">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="2875e-589">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-589">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="2875e-590">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-590">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="2875e-591">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-591">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="2875e-592">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-592">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="2875e-593">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-593">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="2875e-594">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-594">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="2875e-595">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="2875e-595">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="2875e-596">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-596">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-597">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-597">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-598">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-598">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-599">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-599">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-600">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-600">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="2875e-601">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-601">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="2875e-602">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-602">Examine the log.</span></span> <span data-ttu-id="2875e-603">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-603">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="2875e-604">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-604">Run the app in the Kudu console</span></span>

<span data-ttu-id="2875e-605">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="2875e-605">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="2875e-606">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="2875e-606">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="2875e-607">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-607">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-608">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-608">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-609">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-609">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-610">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-610">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="2875e-611">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="2875e-611">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="2875e-612">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="2875e-612">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="2875e-613">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-613">Run the app:</span></span>
   * <span data-ttu-id="2875e-614">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-614">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="2875e-615">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-615">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="2875e-616">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-616">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="2875e-617">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="2875e-617">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="2875e-618">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="2875e-618">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="2875e-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="2875e-619">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="2875e-620">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-620">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="2875e-621">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-621">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="2875e-622">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="2875e-622">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="2875e-623">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="2875e-623">**Current release**</span></span>

* <span data-ttu-id="2875e-624">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-624">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="2875e-625">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-625">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="2875e-626">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-626">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="2875e-627">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="2875e-627">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="2875e-628">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-628">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="2875e-629">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="2875e-629">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="2875e-630">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="2875e-630">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="2875e-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="2875e-631">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="2875e-632">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-632">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="2875e-633">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-633">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="2875e-634">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-634">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="2875e-635">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-635">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="2875e-636">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="2875e-636">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2875e-637">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-637">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="2875e-638">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-638">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="2875e-639">**[推奨されている解決方法]** > **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、 **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-639">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="2875e-640">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-640">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2875e-641">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-641">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="2875e-642">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2875e-642">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-643">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-643">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="2875e-644">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-644">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="2875e-645">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-645">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-646">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="2875e-646">Return to the Azure portal.</span></span> <span data-ttu-id="2875e-647">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-647">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="2875e-648">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-648">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-649">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-649">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-650">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-650">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-651">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-651">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="2875e-652">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-652">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="2875e-653">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-653">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="2875e-654">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-654">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="2875e-655">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-655">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="2875e-656">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-656">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="2875e-657">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-657">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="2875e-658">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-658">Select **Save** to save the file.</span></span>

<span data-ttu-id="2875e-659">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-659">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-660">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-660">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-661">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-661">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="2875e-662">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="2875e-662">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="2875e-663">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-663">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-664">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-664">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="2875e-665">ASP.NET Core モジュールのデバッグ ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-665">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="2875e-666">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-666">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="2875e-667">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="2875e-667">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2875e-668">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-668">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="2875e-669">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-669">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="2875e-670">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="2875e-670">Redeploy the app.</span></span>
   * <span data-ttu-id="2875e-671">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2875e-671">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="2875e-672">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-672">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-673">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-673">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-674">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-674">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="2875e-675">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-675">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="2875e-676">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-676">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2875e-677">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-677">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="2875e-678">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="2875e-678">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="2875e-679">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-679">Select the **Save** button.</span></span>
1. <span data-ttu-id="2875e-680">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-681">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-682">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-682">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-683">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-684">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2875e-685">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-685">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="2875e-686">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-686">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="2875e-687">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-687">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="2875e-688">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-688">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="2875e-689">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-689">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="2875e-690">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="2875e-690">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="2875e-691">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-691">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="2875e-692">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-692">Save the file.</span></span>

<span data-ttu-id="2875e-693">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-693">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-694">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-694">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-695">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-695">There's no limit on log file size.</span></span> <span data-ttu-id="2875e-696">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="2875e-696">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="2875e-697">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-697">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-698">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-698">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="2875e-699">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-699">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="2875e-700">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-700">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="2875e-701">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-701">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="2875e-702">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="2875e-702">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="2875e-703">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="2875e-703">Monitoring blades</span></span>

<span data-ttu-id="2875e-704">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-704">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="2875e-705">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-705">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="2875e-706">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-706">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="2875e-707">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="2875e-707">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="2875e-708">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-708">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="2875e-709">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-709">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="2875e-710">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-710">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="2875e-711">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-711">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="2875e-712">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="2875e-712">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="2875e-713">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-713">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="2875e-714">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-714">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="2875e-715">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="2875e-715">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="2875e-716">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-716">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="2875e-717">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-717">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-718">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-718">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-719">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-719">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-720">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-720">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="2875e-721">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2875e-721">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-722">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-722">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="2875e-723">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-723">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="2875e-724">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="2875e-724">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="2875e-725">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-725">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="2875e-726">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-726">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="2875e-727">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-727">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="2875e-728">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-728">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="2875e-729">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-729">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="2875e-730">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-730">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-731">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-731">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="2875e-732">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="2875e-732">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="2875e-733">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="2875e-733">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="2875e-734">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-734">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="2875e-735">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-735">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="2875e-736">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-736">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="2875e-737">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-737">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-738">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-738">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-739">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-739">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="2875e-740">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-740">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-741">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-741">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="2875e-742">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-742">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="2875e-743">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-743">Application Event Log (IIS)</span></span>

<span data-ttu-id="2875e-744">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2875e-744">Access the Application Event Log:</span></span>

1. <span data-ttu-id="2875e-745">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-745">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="2875e-746">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-746">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="2875e-747">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-747">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="2875e-748">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="2875e-748">Search for errors associated with the failing app.</span></span> <span data-ttu-id="2875e-749">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-749">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="2875e-750">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="2875e-750">Run the app at a command prompt</span></span>

<span data-ttu-id="2875e-751">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="2875e-751">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="2875e-752">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-752">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="2875e-753">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="2875e-753">Framework-dependent deployment</span></span>

<span data-ttu-id="2875e-754">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-754">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="2875e-755">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-755">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="2875e-756">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-756">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="2875e-757">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-757">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="2875e-758">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-758">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="2875e-759">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="2875e-759">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="2875e-760">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="2875e-760">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="2875e-761">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="2875e-761">Self-contained deployment</span></span>

<span data-ttu-id="2875e-762">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-762">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="2875e-763">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-763">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="2875e-764">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="2875e-765">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="2875e-766">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="2875e-767">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="2875e-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="2875e-768">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="2875e-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="2875e-769">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-769">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="2875e-770">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="2875e-770">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2875e-771">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-771">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="2875e-772">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-772">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="2875e-773">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-773">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="2875e-774">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-774">Edit the *web.config* file.</span></span> <span data-ttu-id="2875e-775">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-775">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="2875e-776">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="2875e-776">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="2875e-777">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-777">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="2875e-778">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="2875e-778">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="2875e-779">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-779">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="2875e-780">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-780">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="2875e-781">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-781">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-782">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-782">Navigate to the *logs* folder.</span></span> <span data-ttu-id="2875e-783">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-783">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="2875e-784">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-784">Study the log for errors.</span></span>

<span data-ttu-id="2875e-785">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-785">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="2875e-786">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-786">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-787">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-787">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="2875e-788">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-788">Save the file.</span></span>

<span data-ttu-id="2875e-789">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-789">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-790">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-790">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-791">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-791">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="2875e-792">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-792">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-793">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-793">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="2875e-794">ASP.NET Core モジュールのデバッグ ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-794">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="2875e-795">ASP.NET Core モジュールのデバッグ ログを有効にするには、次のハンドラー設定をアプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2875e-795">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="2875e-796">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-796">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="2875e-797">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-797">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="2875e-798">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="2875e-798">Enable the Developer Exception Page</span></span>

<span data-ttu-id="2875e-799">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="2875e-799">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="2875e-800">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-800">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="2875e-801">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-801">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="2875e-802">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-802">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="2875e-803">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-803">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="2875e-804">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="2875e-804">Obtain data from an app</span></span>

<span data-ttu-id="2875e-805">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="2875e-805">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="2875e-806">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-806">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="2875e-807">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-807">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="2875e-808">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2875e-808">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="2875e-809">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="2875e-809">App crashes or encounters an exception</span></span>

<span data-ttu-id="2875e-810">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="2875e-810">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="2875e-811">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-811">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="2875e-812">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="2875e-812">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="2875e-813">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-813">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="2875e-814">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-814">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="2875e-815">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-815">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="2875e-816">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-816">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="2875e-817">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-817">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="2875e-818">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-818">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="2875e-819">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-819">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="2875e-820">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="2875e-820">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="2875e-821">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-821">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-822">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-822">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="2875e-823">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="2875e-823">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="2875e-824">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-824">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="2875e-825">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="2875e-825">Analyze the dump</span></span>

<span data-ttu-id="2875e-826">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-826">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="2875e-827">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-827">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="2875e-828">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="2875e-828">Clear package caches</span></span>

<span data-ttu-id="2875e-829">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-829">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="2875e-830">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-830">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="2875e-831">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-831">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="2875e-832">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-832">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="2875e-833">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="2875e-833">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="2875e-834">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-834">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="2875e-835">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-835">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="2875e-836">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="2875e-836">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="2875e-837">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-837">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2875e-838">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2875e-838">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="2875e-839">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-839">Azure documentation</span></span>

* [<span data-ttu-id="2875e-840">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="2875e-840">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="2875e-841">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="2875e-841">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="2875e-842">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="2875e-842">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="2875e-843">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="2875e-843">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="2875e-844">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-844">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="2875e-845">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-845">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="2875e-846">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-846">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="2875e-847">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="2875e-847">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="2875e-848">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="2875e-848">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="2875e-849">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="2875e-849">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="2875e-850">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-850">Visual Studio documentation</span></span>

* [<span data-ttu-id="2875e-851">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-851">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="2875e-852">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-852">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="2875e-853">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="2875e-853">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="2875e-854">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-854">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="2875e-855">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-855">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2875e-856">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-856">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="2875e-857">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-857">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="2875e-858">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2875e-858">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="2875e-859">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-859">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="2875e-860">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-860">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="2875e-861">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-861">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="2875e-862">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2875e-862">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="2875e-863">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-863">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="2875e-864">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="2875e-864">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="2875e-865">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2875e-865">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="2875e-866">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2875e-866">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="2875e-867">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-867">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="2875e-868">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="2875e-868">App startup errors</span></span>

<span data-ttu-id="2875e-869">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="2875e-869">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="2875e-870">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 処理エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-870">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="2875e-871">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="2875e-871">403.14 Forbidden</span></span>

<span data-ttu-id="2875e-872">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2875e-872">The app fails to start.</span></span> <span data-ttu-id="2875e-873">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-873">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="2875e-874">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-874">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="2875e-875">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="2875e-875">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="2875e-876">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="2875e-876">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="2875e-877">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-877">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="2875e-878">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="2875e-878">Perform the following steps:</span></span>

1. <span data-ttu-id="2875e-879">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-879">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="2875e-880">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="2875e-880">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="2875e-881">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-881">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="2875e-882">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-882">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="2875e-883">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-883">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="2875e-884">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-884">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="2875e-885">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-885">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="2875e-886">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-886">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="2875e-887">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="2875e-887">500 Internal Server Error</span></span>

<span data-ttu-id="2875e-888">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="2875e-888">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="2875e-889">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-889">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="2875e-890">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-890">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="2875e-891">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-891">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="2875e-892">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="2875e-892">From the server's perspective, that's correct.</span></span> <span data-ttu-id="2875e-893">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="2875e-893">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="2875e-894">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-894">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="2875e-895">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="2875e-895">502.5 Process Failure</span></span>

<span data-ttu-id="2875e-896">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-896">The worker process fails.</span></span> <span data-ttu-id="2875e-897">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="2875e-897">The app doesn't start.</span></span>

<span data-ttu-id="2875e-898">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="2875e-898">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="2875e-899">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-899">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="2875e-900">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="2875e-900">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="2875e-901">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-901">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="2875e-902">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-902">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="2875e-903">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-903">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="2875e-904">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-904">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="2875e-905">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-905">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="2875e-906">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="2875e-906">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="2875e-907">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2875e-907">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="2875e-908">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-908">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="2875e-909">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-909">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="2875e-910">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-910">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="2875e-911">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-911">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="2875e-912">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-912">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="2875e-913">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-913">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="2875e-914">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-914">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="2875e-915">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-915">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="2875e-916">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="2875e-916">Connection reset</span></span>

<span data-ttu-id="2875e-917">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="2875e-917">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="2875e-918">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="2875e-918">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="2875e-919">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-919">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="2875e-920">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-920">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="2875e-921">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="2875e-921">Default startup limits</span></span>

<span data-ttu-id="2875e-922">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-922">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="2875e-923">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="2875e-923">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="2875e-924">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-924">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="2875e-925">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-925">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="2875e-926">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-926">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="2875e-927">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-927">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="2875e-928">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-928">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="2875e-929">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-929">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="2875e-930">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-930">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="2875e-931">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-931">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="2875e-932">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-932">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="2875e-933">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="2875e-933">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="2875e-934">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-934">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-935">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-935">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-936">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-936">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-937">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-937">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-938">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-938">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="2875e-939">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-939">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="2875e-940">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-940">Examine the log.</span></span> <span data-ttu-id="2875e-941">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-941">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="2875e-942">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-942">Run the app in the Kudu console</span></span>

<span data-ttu-id="2875e-943">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="2875e-943">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="2875e-944">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="2875e-944">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="2875e-945">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-945">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2875e-946">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-946">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-947">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-947">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-948">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-948">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="2875e-949">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="2875e-949">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="2875e-950">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="2875e-950">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="2875e-951">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-951">Run the app:</span></span>
   * <span data-ttu-id="2875e-952">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-952">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="2875e-953">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-953">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="2875e-954">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-954">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="2875e-955">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="2875e-955">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="2875e-956">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="2875e-956">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="2875e-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="2875e-957">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="2875e-958">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-958">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="2875e-959">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-959">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="2875e-960">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="2875e-960">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="2875e-961">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="2875e-961">**Current release**</span></span>

* <span data-ttu-id="2875e-962">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-962">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="2875e-963">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-963">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="2875e-964">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-964">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="2875e-965">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="2875e-965">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="2875e-966">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-966">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="2875e-967">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="2875e-967">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="2875e-968">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="2875e-968">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="2875e-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="2875e-969">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="2875e-970">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2875e-970">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="2875e-971">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-971">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="2875e-972">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-972">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="2875e-973">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-973">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="2875e-974">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="2875e-974">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2875e-975">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-975">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="2875e-976">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-976">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="2875e-977">**[推奨されている解決方法]** > **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、 **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-977">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="2875e-978">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-978">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2875e-979">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-979">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="2875e-980">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2875e-980">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-981">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-981">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="2875e-982">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-982">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="2875e-983">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-983">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-984">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="2875e-984">Return to the Azure portal.</span></span> <span data-ttu-id="2875e-985">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-985">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="2875e-986">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-986">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-987">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-987">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-988">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-988">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-989">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-989">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="2875e-990">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-990">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="2875e-991">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-991">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="2875e-992">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-992">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="2875e-993">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-993">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="2875e-994">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-994">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="2875e-995">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-995">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="2875e-996">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-996">Select **Save** to save the file.</span></span>

<span data-ttu-id="2875e-997">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-997">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-998">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-998">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-999">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-999">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="2875e-1000">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1000">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="2875e-1001">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-1001">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-1002">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1002">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="2875e-1003">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="2875e-1003">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="2875e-1004">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1004">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="2875e-1005">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-1005">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="2875e-1006">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="2875e-1006">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="2875e-1007">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="2875e-1007">Monitoring blades</span></span>

<span data-ttu-id="2875e-1008">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1008">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="2875e-1009">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1009">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="2875e-1010">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1010">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="2875e-1011">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="2875e-1011">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="2875e-1012">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1012">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="2875e-1013">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1013">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="2875e-1014">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1014">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="2875e-1015">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1015">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="2875e-1016">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1016">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="2875e-1017">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1017">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="2875e-1018">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1018">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="2875e-1019">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="2875e-1019">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="2875e-1020">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1020">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="2875e-1021">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1021">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2875e-1022">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1022">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2875e-1023">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1023">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2875e-1024">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1024">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="2875e-1025">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2875e-1025">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-1026">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1026">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="2875e-1027">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1027">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="2875e-1028">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="2875e-1028">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="2875e-1029">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1029">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="2875e-1030">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1030">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="2875e-1031">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1031">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="2875e-1032">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1032">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="2875e-1033">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1033">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="2875e-1034">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1034">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-1035">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1035">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="2875e-1036">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1036">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="2875e-1037">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="2875e-1037">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="2875e-1038">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1038">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="2875e-1039">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1039">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="2875e-1040">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1040">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="2875e-1041">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1041">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-1042">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1042">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-1043">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-1043">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="2875e-1044">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-1044">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-1045">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1045">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="2875e-1046">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-1046">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="2875e-1047">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-1047">Application Event Log (IIS)</span></span>

<span data-ttu-id="2875e-1048">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2875e-1048">Access the Application Event Log:</span></span>

1. <span data-ttu-id="2875e-1049">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1049">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="2875e-1050">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1050">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="2875e-1051">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1051">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="2875e-1052">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1052">Search for errors associated with the failing app.</span></span> <span data-ttu-id="2875e-1053">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1053">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="2875e-1054">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="2875e-1054">Run the app at a command prompt</span></span>

<span data-ttu-id="2875e-1055">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="2875e-1055">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="2875e-1056">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1056">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="2875e-1057">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="2875e-1057">Framework-dependent deployment</span></span>

<span data-ttu-id="2875e-1058">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-1058">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="2875e-1059">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1059">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="2875e-1060">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1060">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="2875e-1061">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1061">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="2875e-1062">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1062">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="2875e-1063">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="2875e-1063">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="2875e-1064">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1064">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="2875e-1065">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="2875e-1065">Self-contained deployment</span></span>

<span data-ttu-id="2875e-1066">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="2875e-1066">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="2875e-1067">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1067">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="2875e-1068">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="2875e-1069">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="2875e-1070">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="2875e-1071">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="2875e-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="2875e-1072">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="2875e-1073">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-1073">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="2875e-1074">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="2875e-1074">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2875e-1075">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1075">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="2875e-1076">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1076">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="2875e-1077">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1077">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="2875e-1078">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1078">Edit the *web.config* file.</span></span> <span data-ttu-id="2875e-1079">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1079">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="2875e-1080">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="2875e-1080">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="2875e-1081">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1081">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="2875e-1082">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1082">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="2875e-1083">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1083">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="2875e-1084">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1084">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="2875e-1085">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1085">Make a request to the app.</span></span>
1. <span data-ttu-id="2875e-1086">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1086">Navigate to the *logs* folder.</span></span> <span data-ttu-id="2875e-1087">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1087">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="2875e-1088">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1088">Study the log for errors.</span></span>

<span data-ttu-id="2875e-1089">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="2875e-1089">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="2875e-1090">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1090">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="2875e-1091">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1091">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="2875e-1092">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1092">Save the file.</span></span>

<span data-ttu-id="2875e-1093">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1093">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-1094">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1094">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2875e-1095">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2875e-1095">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="2875e-1096">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="2875e-1096">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2875e-1097">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1097">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="2875e-1098">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="2875e-1098">Enable the Developer Exception Page</span></span>

<span data-ttu-id="2875e-1099">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="2875e-1099">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="2875e-1100">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1100">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="2875e-1101">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1101">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="2875e-1102">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1102">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="2875e-1103">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1103">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="2875e-1104">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="2875e-1104">Obtain data from an app</span></span>

<span data-ttu-id="2875e-1105">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1105">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="2875e-1106">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1106">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="2875e-1107">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="2875e-1107">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="2875e-1108">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1108">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="2875e-1109">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="2875e-1109">App crashes or encounters an exception</span></span>

<span data-ttu-id="2875e-1110">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1110">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="2875e-1111">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1111">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="2875e-1112">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="2875e-1112">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="2875e-1113">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1113">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="2875e-1114">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1114">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="2875e-1115">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1115">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="2875e-1116">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1116">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="2875e-1117">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1117">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="2875e-1118">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1118">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="2875e-1119">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1119">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="2875e-1120">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="2875e-1120">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="2875e-1121">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1121">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="2875e-1122">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1122">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="2875e-1123">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="2875e-1123">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="2875e-1124">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1124">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="2875e-1125">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="2875e-1125">Analyze the dump</span></span>

<span data-ttu-id="2875e-1126">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1126">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="2875e-1127">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2875e-1127">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="2875e-1128">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="2875e-1128">Clear package caches</span></span>

<span data-ttu-id="2875e-1129">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1129">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="2875e-1130">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1130">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="2875e-1131">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="2875e-1131">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="2875e-1132">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1132">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="2875e-1133">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="2875e-1133">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="2875e-1134">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1134">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="2875e-1135">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2875e-1135">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="2875e-1136">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="2875e-1136">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="2875e-1137">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="2875e-1137">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2875e-1138">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2875e-1138">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="2875e-1139">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-1139">Azure documentation</span></span>

* [<span data-ttu-id="2875e-1140">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="2875e-1140">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="2875e-1141">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="2875e-1141">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="2875e-1142">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="2875e-1142">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="2875e-1143">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="2875e-1143">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="2875e-1144">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-1144">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="2875e-1145">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-1145">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="2875e-1146">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2875e-1146">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="2875e-1147">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="2875e-1147">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="2875e-1148">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="2875e-1148">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="2875e-1149">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="2875e-1149">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="2875e-1150">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-1150">Visual Studio documentation</span></span>

* [<span data-ttu-id="2875e-1151">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-1151">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="2875e-1152">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-1152">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="2875e-1153">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="2875e-1153">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="2875e-1154">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="2875e-1154">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="2875e-1155">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="2875e-1155">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
