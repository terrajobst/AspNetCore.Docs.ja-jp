---
title: ASP.NET Core を使用した Azure App Service および IIS の一般的なエラーのリファレンス
author: guardrex
description: Azure Apps Service と IIS で ASP.NET Core アプリをホストするときに発生する一般的なエラーを解決する方法について助言を得ます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 3030bc57be113d9034123c96403742442b9240bb
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308099"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="b78e5-103">ASP.NET Core を使用した Azure App Service および IIS の一般的なエラーのリファレンス</span><span class="sxs-lookup"><span data-stu-id="b78e5-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="b78e5-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b78e5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b78e5-105">このトピックでは、Azure Apps Service と IIS で ASP.NET Core アプリをホストするときに発生する一般的なエラーを解決する方法を紹介します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-105">This topic offers troubleshooting advice for common errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="b78e5-106">次の情報を収集します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-106">Collect the following information:</span></span>

* <span data-ttu-id="b78e5-107">ブラウザーの動作 (ステータス コードとエラー メッセージ)</span><span class="sxs-lookup"><span data-stu-id="b78e5-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="b78e5-108">アプリケーション イベント ログのエントリ</span><span class="sxs-lookup"><span data-stu-id="b78e5-108">Application Event Log entries</span></span>
  * <span data-ttu-id="b78e5-109">Azure App Service &ndash; (<xref:test/troubleshoot-azure-iis> 参照)。</span><span class="sxs-lookup"><span data-stu-id="b78e5-109">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="b78e5-110">IIS</span><span class="sxs-lookup"><span data-stu-id="b78e5-110">IIS</span></span>
    1. <span data-ttu-id="b78e5-111">**[Windows]** メニューで **[スタート]** を選択し、「*Event Viewer*」と入力し、**Enter** を押します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="b78e5-112">**イベント ビューアー**が開いたら、サイドバーで **[Windows ログ]** 、 **[アプリケーション]** の順に展開します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="b78e5-113">ASP.NET Core モジュールの stdout ログ エントリと debug ログ エントリ</span><span class="sxs-lookup"><span data-stu-id="b78e5-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="b78e5-114">Azure App Service &ndash; (<xref:test/troubleshoot-azure-iis> 参照)。</span><span class="sxs-lookup"><span data-stu-id="b78e5-114">Azure App Service &ndash; See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="b78e5-115">IIS &ndash; ASP.NET Core モジュール トピックの「[ログの作成とリダイレクト](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)」セクションと「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」セクションの指示に従います。</span><span class="sxs-lookup"><span data-stu-id="b78e5-115">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="b78e5-116">エラー情報を次の一般的なエラーと比較します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="b78e5-117">一致が見つかった場合は、トラブルシューティングのアドバイスに従います。</span><span class="sxs-lookup"><span data-stu-id="b78e5-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="b78e5-118">このトピックではすべてのエラーを網羅しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="b78e5-119">ここに記載されていないエラーに遭遇した場合、このトピックの一番下にある **[コンテンツ フィードバック]** ボタンで新しい問題を登録してください。その際、エラーを再現する方法を詳しく教えてください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="b78e5-120">インストーラーが VC++ 再頒布可能パッケージを取得できない</span><span class="sxs-lookup"><span data-stu-id="b78e5-120">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="b78e5-121">**インストーラー例外:** 0x80072efd **--または--** 0x80072f76 - 特定できないエラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-121">**Installer Exception:** 0x80072efd **--OR--** 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="b78e5-122">**インストーラー ログ例外&#8224;:** エラー 0x80072efd **--または--** 0x80072f76:EXE パッケージを実行できませんでした</span><span class="sxs-lookup"><span data-stu-id="b78e5-122">**Installer Log Exception&#8224;:** Error 0x80072efd **--OR--** 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="b78e5-123">&#8224;ログの場所は *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log* です。</span><span class="sxs-lookup"><span data-stu-id="b78e5-123">&#8224;The log is located at *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span></span>

<span data-ttu-id="b78e5-124">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-124">Troubleshooting:</span></span>

<span data-ttu-id="b78e5-125">[NET Core ホスティング バンドル](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)のインストール時にインストーラーがインターネットにアクセスできない場合、インストーラーは *Microsoft Visual C++ 2015 再頒布可能パッケージ*を取得できず、この例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-125">If the system doesn't have Internet access while [installing the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="b78e5-126">[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53840)からインストーラーを入手します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-126">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="b78e5-127">インストーラーが失敗する場合、[フレームワークに依存する展開 (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd) をホストするために必要な .NET Core ランタイムをサーバーが受け取っていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-127">If the installer fails, the server may not receive the .NET Core runtime required to host a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span> <span data-ttu-id="b78e5-128">FDD をホストしている場合は、 **[プログラムと機能]** または **[アプリと機能]** でランタイムがインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-128">If hosting an FDD, confirm that the runtime is installed in **Programs & Features** or **Apps & features**.</span></span> <span data-ttu-id="b78e5-129">特定のランタイムが必要な場合、[.NET ダウンロード アーカイブ](https://dotnet.microsoft.com/download/archives)からダウンロードし、システムにインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-129">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="b78e5-130">ランタイムのインストール後、システムを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-130">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="b78e5-131">OS のアップグレードによって 32 ビット ASP.NET Core モジュールが削除された</span><span class="sxs-lookup"><span data-stu-id="b78e5-131">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="b78e5-132">**アプリケーション ログ:** モジュール DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** を読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-132">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="b78e5-133">このデータはエラーです。</span><span class="sxs-lookup"><span data-stu-id="b78e5-133">The data is the error.</span></span>

<span data-ttu-id="b78e5-134">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-134">Troubleshooting:</span></span>

<span data-ttu-id="b78e5-135">**C:\Windows\SysWOW64\inetsrv** ディレクトリにある OS ファイルでないファイルは、OS アップグレード時に保持されません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-135">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="b78e5-136">OS アップグレードより前に ASP.NET Core モジュールをインストールしていた場合、OS アップグレード後に 32 ビット モードでアプリ プールを実行しようとすると、この問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-136">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="b78e5-137">OS アップグレード後に ASP.NET Core モジュールを修復してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-137">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="b78e5-138">「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-138">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="b78e5-139">インストーラーを実行するときに **[修復]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-139">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="b78e5-140">サイト拡張機能の不足、32 ビット (x86) および 64 ビット (x64) サイト拡張機能がインストールされている、または間違ったプロセス ビットが設定されている</span><span class="sxs-lookup"><span data-stu-id="b78e5-140">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="b78e5-141">"*Azure App Services でホストしているアプリに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="b78e5-141">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="b78e5-142">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-142">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="b78e5-143">**アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-143">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="b78e5-144">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-144">Could not find inprocess request handler.</span></span> <span data-ttu-id="b78e5-145">hostfxr の呼び出し時にキャプチャされた出力:互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-145">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="b78e5-146">指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-146">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="b78e5-147">アプリケーション '/LM/W3SVC/1416782824/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="b78e5-147">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="b78e5-148">**ASP.NET Core モジュールの stdout ログ:** 互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-148">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="b78e5-149">指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-149">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-150">**ASP.NET Core モジュール デバッグ ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-150">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="b78e5-151">これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-151">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="b78e5-152">HRESULT が失敗し、次が返されました:0x8000ffff。</span><span class="sxs-lookup"><span data-stu-id="b78e5-152">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="b78e5-153">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-153">Could not find inprocess request handler.</span></span> <span data-ttu-id="b78e5-154">互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-154">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="b78e5-155">指定したフレームワーク 'Microsoft.AspNetCore.App'、バージョン '{VERSION}-preview-\*' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-155">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-156">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-156">Troubleshooting:</span></span>

* <span data-ttu-id="b78e5-157">プレビュー ランタイムでアプリを実行している場合、アプリのビットとアプリのランタイム バージョンに一致する、32 ビット (x86) **または** 64 ビット (x64) のいずれかのサイト拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b78e5-157">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="b78e5-158">**両方の拡張機能や、拡張機能の複数のランタイム バージョンをインストールしないでください。**</span><span class="sxs-lookup"><span data-stu-id="b78e5-158">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="b78e5-159">ASP.NET Core {ランタイム バージョン} (x86) ランタイム</span><span class="sxs-lookup"><span data-stu-id="b78e5-159">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="b78e5-160">ASP.NET Core {ランタイム バージョン} (x64) ランタイム</span><span class="sxs-lookup"><span data-stu-id="b78e5-160">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="b78e5-161">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-161">Restart the app.</span></span> <span data-ttu-id="b78e5-162">アプリが再起動するまで数秒待ちます。</span><span class="sxs-lookup"><span data-stu-id="b78e5-162">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="b78e5-163">プレビュー ランタイムでアプリを実行していて、32 ビット (x86)、64 ビット (x64) 両方の[サイト拡張機能](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)がインストールされている場合、アプリのビットと一致しないサイト拡張機能をアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="b78e5-163">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="b78e5-164">サイト拡張機能を削除した後、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-164">After removing the site extension, restart the app.</span></span> <span data-ttu-id="b78e5-165">アプリが再起動するまで数秒待ちます。</span><span class="sxs-lookup"><span data-stu-id="b78e5-165">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="b78e5-166">プレビュー ランタイムでアプリを実行していて、サイト拡張機能とアプリのビットが一致している場合、プレビュー サイト拡張機能の "*ランタイム バージョン*" がアプリのランタイム バージョンと一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-166">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="b78e5-167">**[アプリケーション設定]** のアプリの **[プラットフォーム]** がアプリのビットと一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-167">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="b78e5-168">詳細については、<xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-168">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="b78e5-169">x86 アプリが展開されますが、32 ビット アプリに対してアプリ プールは有効になりません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-169">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="b78e5-170">**ブラウザー:** HTTP エラー 500.30 - ANCM インプロセス起動失敗</span><span class="sxs-lookup"><span data-stu-id="b78e5-170">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="b78e5-171">**アプリケーション ログ:** 物理ルートが '{PATH}' のアプリケーション '/LM/W3SVC/5/ROOT' に予想外のマネージド例外が発生しました、例外コード = '0xe0434352'。</span><span class="sxs-lookup"><span data-stu-id="b78e5-171">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="b78e5-172">詳細については、stderr ログを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-172">Please check the stderr logs for more information.</span></span> <span data-ttu-id="b78e5-173">物理ルートが '{PATH}' のアプリケーション '/LM/W3SVC/5/ROOT' で clr とマネージド アプリケーションを読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-173">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="b78e5-174">CLR ワーカー スレッドが途中で終了しました</span><span class="sxs-lookup"><span data-stu-id="b78e5-174">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="b78e5-175">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。</span><span class="sxs-lookup"><span data-stu-id="b78e5-175">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-176">**ASP.NET Core モジュール デバッグ ログ:** HRESULT が失敗し、次が返されました:0x8007023e</span><span class="sxs-lookup"><span data-stu-id="b78e5-176">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="b78e5-177">このシナリオは、自己完結型アプリの公開時、SDK によってトラップされます。</span><span class="sxs-lookup"><span data-stu-id="b78e5-177">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="b78e5-178">RID がプラットフォーム ターゲットに一致しない場合 (`win10-x64` RID とプロジェクト ファイルの `<PlatformTarget>x86</PlatformTarget>` など)、SDK からエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b78e5-178">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="b78e5-179">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-179">Troubleshooting:</span></span>

<span data-ttu-id="b78e5-180">x86 フレームワークに依存する展開の場合 (`<PlatformTarget>x86</PlatformTarget>`)、32 ビット アプリに対して IIS アプリ プールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="b78e5-180">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="b78e5-181">IIS Manager でアプリ プールの **[詳細設定]** を開き、 **[32 ビット アプリケーションの有効化]** を **[True]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-181">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="b78e5-182">プラットフォームが RID と競合している</span><span class="sxs-lookup"><span data-stu-id="b78e5-182">Platform conflicts with RID</span></span>

* <span data-ttu-id="b78e5-183">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-183">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="b78e5-184">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' でプロセスを開始できませんでした、エラー コード = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="b78e5-184">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="b78e5-185">**ASP.NET Core モジュールの stdout ログ:** 未処理の例外:System.BadImageFormatException:ファイルまたはアセンブリ '{ASSEMBLY}.dll' を読み込めませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-185">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="b78e5-186">正しくない形式のプログラムを読み込もうとしました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-186">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="b78e5-187">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-187">Troubleshooting:</span></span>

* <span data-ttu-id="b78e5-188">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-188">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="b78e5-189">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-189">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="b78e5-190">詳細については、<xref:test/troubleshoot-azure-iis> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-190">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="b78e5-191">Azure Apps 展開で、アプリケーションをアップグレードして新しいアセンブリを展開しようとしたときにこの例外が発生した場合は、以前の展開からすべてのファイルを手動で削除してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-191">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="b78e5-192">アップグレードしたアプリを展開するとき、互換性のないアセンブリが残っていると、`System.BadImageFormatException` 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-192">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="b78e5-193">URI のエンドポイントが間違っているか、Web サイトが停止している</span><span class="sxs-lookup"><span data-stu-id="b78e5-193">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="b78e5-194">**ブラウザー:** ERR_CONNECTION_REFUSED **--または--** 接続できません</span><span class="sxs-lookup"><span data-stu-id="b78e5-194">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="b78e5-195">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="b78e5-195">**Application Log:** No entry</span></span>

* <span data-ttu-id="b78e5-196">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-196">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-197">**ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-197">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-198">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-198">Troubleshooting:</span></span>

* <span data-ttu-id="b78e5-199">アプリに対して正しい URI エンドポイントが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-199">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="b78e5-200">バインドを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-200">Check the bindings.</span></span>

* <span data-ttu-id="b78e5-201">IIS Web サイトが *[停止]* 状態でないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-201">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="b78e5-202">CoreWebEngine または W3SVC サーバー機能が無効</span><span class="sxs-lookup"><span data-stu-id="b78e5-202">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="b78e5-203">**OS の例外:** ASP.NET Core モジュールを使用するには、IIS 7.0 CoreWebEngine および W3SVC の機能をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-203">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="b78e5-204">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-204">Troubleshooting:</span></span>

<span data-ttu-id="b78e5-205">適切な役割と機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-205">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="b78e5-206">「[IIS 構成](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-206">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="b78e5-207">Web サイト物理パスが間違っているか、アプリが見つからない</span><span class="sxs-lookup"><span data-stu-id="b78e5-207">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="b78e5-208">**ブラウザー:** 403 許可されていません - アクセスが拒否されました **--または--** 403.14 許可されていません - Web サーバーは、このディレクトリの内容の一覧を表示しないように構成されています。</span><span class="sxs-lookup"><span data-stu-id="b78e5-208">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="b78e5-209">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="b78e5-209">**Application Log:** No entry</span></span>

* <span data-ttu-id="b78e5-210">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-210">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-211">**ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-211">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-212">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-212">Troubleshooting:</span></span>

<span data-ttu-id="b78e5-213">IIS Web サイトの**基本設定**と物理アプリのフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-213">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="b78e5-214">アプリが IIS Web サイトの**物理パス**にあるフォルダー内に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-214">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="b78e5-215">役割が正しくない、ASP.NET Core モジュールがインストールされていない、または不適切なアクセス許可</span><span class="sxs-lookup"><span data-stu-id="b78e5-215">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="b78e5-216">**ブラウザー:** 500.19 内部サーバー エラー - ページに関連する構成データが無効であるため、要求されたページにアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-216">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="b78e5-217">**--または--** このページを表示できません</span><span class="sxs-lookup"><span data-stu-id="b78e5-217">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="b78e5-218">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="b78e5-218">**Application Log:** No entry</span></span>

* <span data-ttu-id="b78e5-219">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-219">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-220">**ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-220">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-221">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-221">Troubleshooting:</span></span>

* <span data-ttu-id="b78e5-222">適切な役割が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-222">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="b78e5-223">「[IIS 構成](xref:host-and-deploy/iis/index#iis-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-223">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="b78e5-224">**[プログラムと機能]** または **[アプリと機能]** を開き、 **[Windows Server Hosting]** がインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-224">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="b78e5-225">インストールされているプログラムの一覧に **[Windows Server Hosting]** がない場合、.NET Core ホスティング バンドルをダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="b78e5-225">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="b78e5-226">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="b78e5-226">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="b78e5-227">詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-227">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="b78e5-228">**[アプリケーション プール]**  >  **[プロセス モデル]**  >  **[ID]** が **ApplicationPoolIdentity** に設定されていることを確認します。または、アプリの展開フォルダーにアクセスするための正しいアクセス許可がカスタム ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-228">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="b78e5-229">ASP.NET Core ホスティング バンドルをアンインストールし、以前のバージョンのホスティング バンドルをインストールした場合、*applicationHost.config* ファイルには ASP.NET Core モジュールのセクションが含まれません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-229">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="b78e5-230">*applicationHost.config* で *%windir%/System32/inetsrv/config* を開き、`<configuration><configSections><sectionGroup name="system.webServer">` セクション グループを見つけます。</span><span class="sxs-lookup"><span data-stu-id="b78e5-230">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="b78e5-231">セクション グループに ASP.NET Core モジュールのセクションがない場合は、セクション要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-231">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="b78e5-232">または、ASP.NET Core ホスティング バンドルの最新バージョンをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b78e5-232">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="b78e5-233">最新バージョンは、ポートされている ASP.NET Core アプリと下位互換性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-233">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="b78e5-234">processPath の誤り、PATH 変数の欠如、ホスティング バンドルが未インストール、システムまたは IIS が再起動されていない、VC++ 再頒布可能パッケージが未インストール、dotnet.exe アクセス違反</span><span class="sxs-lookup"><span data-stu-id="b78e5-234">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-235">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-235">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="b78e5-236">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"{...}"</span><span class="sxs-lookup"><span data-stu-id="b78e5-236">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="b78e5-237">' でプロセスを開始できませんでした、エラー コード = '0x80070002 :0.</span><span class="sxs-lookup"><span data-stu-id="b78e5-237">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="b78e5-238">アプリケーション '{PATH}' は開始できませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-238">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="b78e5-239">実行可能ファイルが '{PATH}' で見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-239">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="b78e5-240">アプリケーション '/LM/W3SVC/2/ROOT' を起動できませんでした、エラー コード '0x8007023e'。</span><span class="sxs-lookup"><span data-stu-id="b78e5-240">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="b78e5-241">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-241">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="b78e5-242">**ASP.NET Core モジュール デバッグ ログ:** イベント ログ:'アプリケーション '{PATH}' を起動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-242">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="b78e5-243">実行可能ファイルが '{PATH}' で見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-243">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="b78e5-244">HRESULT が失敗し、次が返されました:0x8007023e</span><span class="sxs-lookup"><span data-stu-id="b78e5-244">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="b78e5-245">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-245">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="b78e5-246">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"{...}"</span><span class="sxs-lookup"><span data-stu-id="b78e5-246">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="b78e5-247">' でプロセスを開始できませんでした、エラー コード = '0x80070002 :0.</span><span class="sxs-lookup"><span data-stu-id="b78e5-247">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="b78e5-248">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。</span><span class="sxs-lookup"><span data-stu-id="b78e5-248">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-249">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-249">Troubleshooting:</span></span>

* <span data-ttu-id="b78e5-250">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-250">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="b78e5-251">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-251">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="b78e5-252">詳細については、<xref:test/troubleshoot-azure-iis> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-252">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="b78e5-253">*web.config* で `<aspNetCore>` 要素の *processPath* 属性を調べ、フレームワークに依存する展開 (FDD) の場合はそれが `dotnet` であること、[自己完結型展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) の場合はそれが `.\{ASSEMBLY}.exe` であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-253">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="b78e5-254">FDD の場合、PATH 設定で *dotnet.exe* にアクセスできていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-254">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="b78e5-255">*C:\Program Files\dotnet\\* がシステムの PATH 設定に含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-255">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="b78e5-256">FDD では、アプリ プールのユーザー ID で *dotnet.exe* にアクセスできていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-256">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="b78e5-257">アプリ プール ユーザー ID に、*C:\Program Files\dotnet* ディレクトリへのアクセス許可が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-257">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="b78e5-258">*C:\Program Files\dotnet* とアプリのディレクトリに、アプリ プール ユーザー ID に対する拒否ルールが構成されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-258">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="b78e5-259">FDD を配置し、IIS を再起動せずに .NET Core をインストールした可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-259">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="b78e5-260">サーバーを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-260">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="b78e5-261">ホスト システムで、 .NET Core ランタイムをインストールせずに、FDD を配置した可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-261">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="b78e5-262">.NET Core ランタイムがインストールされていない場合は、システムで **.NET Core ホスティング バンドルのインストーラー**を実行します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-262">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="b78e5-263">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="b78e5-263">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="b78e5-264">詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-264">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="b78e5-265">特定のランタイムが必要な場合、[.NET ダウンロード アーカイブ](https://dotnet.microsoft.com/download/archives)からダウンロードし、システムにインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-265">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="b78e5-266">インストールを完了するために、システムを再起動するか、コマンド プロンプトから **net stop was /y** に続けて **net start w3svc** を実行して IIS を再起動します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-266">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="b78e5-267">FDD を配置し、*Microsoft Visual C++ 2015 再頒布可能パッケージ (x64)* がシステムにインストールされていない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-267">An FDD may have been deployed and the *Microsoft Visual C++ 2015 Redistributable (x64)* isn't installed on the system.</span></span> <span data-ttu-id="b78e5-268">[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53840)からインストーラーを入手します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-268">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="b78e5-269">\<aspNetCore> 要素の引数の誤り</span><span class="sxs-lookup"><span data-stu-id="b78e5-269">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-270">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-270">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="b78e5-271">**アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-271">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="b78e5-272">これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-272">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="b78e5-273">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-273">Could not find inprocess request handler.</span></span> <span data-ttu-id="b78e5-274">hostfxr の呼び出し時にキャプチャされた出力:dotnet SDK コマンドを実行しますか?</span><span class="sxs-lookup"><span data-stu-id="b78e5-274">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="b78e5-275">dotnet SDK を次の場所からインストールしてください。 https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 アプリケーション '/LM/W3SVC/3/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="b78e5-275">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="b78e5-276">**ASP.NET Core モジュールの stdout ログ:** dotnet SDK コマンドを実行しますか?</span><span class="sxs-lookup"><span data-stu-id="b78e5-276">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="b78e5-277">dotnet SDK を https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 からインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-277">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="b78e5-278">**ASP.NET Core モジュール デバッグ ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-278">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="b78e5-279">これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-279">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="b78e5-280">HRESULT が失敗し、次が返されました:0x8000ffff インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-280">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="b78e5-281">hostfxr の呼び出し時にキャプチャされた出力:dotnet SDK コマンドを実行しますか?</span><span class="sxs-lookup"><span data-stu-id="b78e5-281">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="b78e5-282">dotnet SDK を次の場所からインストールしてください。 https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT が失敗し、次が返されました:0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="b78e5-282">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="b78e5-283">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-283">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="b78e5-284">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"dotnet" .\{ASSEMBLY}.dll' でプロセスを開始できませんでした、エラー コード = '0x80004005 : 80008081。</span><span class="sxs-lookup"><span data-stu-id="b78e5-284">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="b78e5-285">**ASP.NET Core モジュールの stdout ログ:** 実行するアプリケーションが存在しません。'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="b78e5-285">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="b78e5-286">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-286">Troubleshooting:</span></span>

* <span data-ttu-id="b78e5-287">Kestrel でアプリをローカルに実行できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-287">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="b78e5-288">プロセスのエラーは、アプリ内の問題の結果である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b78e5-288">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="b78e5-289">詳細については、<xref:test/troubleshoot-azure-iis> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-289">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="b78e5-290">*web.config* で `<aspNetCore>` 要素の *arguments* 属性を調べ、次のいずれかになっていることを確認します。(a) フレームワークに依存する展開 (FDD) の場合は `.\{ASSEMBLY}.dll`、または (b) 自己完結型の展開 (SCD) の場合は、未指定の空の文字列 (`arguments=""`) か、アプリの引数のリスト (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`)。</span><span class="sxs-lookup"><span data-stu-id="b78e5-290">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="b78e5-291">.NET Core 共有フレームワークがありません</span><span class="sxs-lookup"><span data-stu-id="b78e5-291">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="b78e5-292">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-292">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="b78e5-293">**アプリケーション ログ:** hostfxr を呼び出し、インプロセス要求ハンドラーを見つけようとすると、ネイティブの依存関係が見つからず、失敗しました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-293">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="b78e5-294">これが意味するところは、ほとんどの場合、アプリが正しく設定されていないということです。アプリケーションの対象であり、コンピューターにインストールされている Microsoft.NetCore.App と Microsoft.AspNetCore.App のバージョンを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-294">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="b78e5-295">インプロセス要求ハンドラーが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-295">Could not find inprocess request handler.</span></span> <span data-ttu-id="b78e5-296">hostfxr の呼び出し時にキャプチャされた出力:互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-296">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="b78e5-297">指定のフレームワーク 'Microsoft.AspNetCore.App', version '{VERSION}' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-297">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="b78e5-298">アプリケーション '/LM/W3SVC/5/ROOT' を起動できませんでした、エラー コード '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="b78e5-298">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="b78e5-299">**ASP.NET Core モジュールの stdout ログ:** 互換性のあるフレームワーク バージョンが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-299">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="b78e5-300">指定のフレームワーク 'Microsoft.AspNetCore.App', version '{VERSION}' が見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-300">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="b78e5-301">**ASP.NET Core モジュール デバッグ ログ:** HRESULT が失敗し、次が返されました:0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="b78e5-301">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="b78e5-302">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-302">Troubleshooting:</span></span>

<span data-ttu-id="b78e5-303">フレームワークに依存する展開 (FDD) では、正しいランタイムがシステムにインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-303">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="b78e5-304">アプリケーション プールの停止</span><span class="sxs-lookup"><span data-stu-id="b78e5-304">Stopped Application Pool</span></span>

* <span data-ttu-id="b78e5-305">**ブラウザー:** 503 サービスをご利用いただけません</span><span class="sxs-lookup"><span data-stu-id="b78e5-305">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="b78e5-306">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="b78e5-306">**Application Log:** No entry</span></span>

* <span data-ttu-id="b78e5-307">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-307">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-308">**ASP.NET Core モジュール デバッグ ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-308">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-309">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-309">Troubleshooting:</span></span>

<span data-ttu-id="b78e5-310">アプリケーション プールが *[停止]* 状態でないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-310">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="b78e5-311">サブアプリケーションに \<handlers> セクションが含まれている</span><span class="sxs-lookup"><span data-stu-id="b78e5-311">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="b78e5-312">**ブラウザー:** HTTP エラー 500.19 - 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-312">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="b78e5-313">**アプリケーション ログ:** エントリなし</span><span class="sxs-lookup"><span data-stu-id="b78e5-313">**Application Log:** No entry</span></span>

* <span data-ttu-id="b78e5-314">**ASP.NET Core モジュールの stdout ログ:** ルート アプリのログ ファイルが作成され、通常の操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="b78e5-314">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="b78e5-315">サブアプリのログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-315">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-316">**ASP.NET Core モジュール デバッグ ログ:** ルート アプリのログ ファイルが作成され、通常の動作を示します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-316">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="b78e5-317">サブアプリのログ ファイルが作成されません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-317">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-318">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-318">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b78e5-319">サブアプリの *web.config* ファイルに `<handlers>` セクションがないか、サブアプリが親アプリのハンドラーを継承していないことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-319">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="b78e5-320">*web.config* の親アプリの `<system.webServer>` セクションが `<location>` 要素の中に置かれています。</span><span class="sxs-lookup"><span data-stu-id="b78e5-320">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="b78e5-321"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、親アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-321">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="b78e5-322">詳細については、<xref:host-and-deploy/aspnet-core-module> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-322">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b78e5-323">サブアプリの *web.config* ファイルに `<handlers>` セクションが含まれていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-323">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="b78e5-324">stdout ログのパスが正しくありません</span><span class="sxs-lookup"><span data-stu-id="b78e5-324">stdout log path incorrect</span></span>

* <span data-ttu-id="b78e5-325">**ブラウザー:** アプリは通常どおりに応答します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-325">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-326">**アプリケーション ログ:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを開始できません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-326">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="b78e5-327">例外メッセージ:HRESULT 0x80070005 が {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 で返されました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-327">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="b78e5-328">C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを停止できません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-328">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="b78e5-329">例外メッセージ:HRESULT 0x80070002 が {PATH} で返されました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-329">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="b78e5-330">{PATH}\aspnetcorev2_inprocess.dll で stdout リダイレクトを開始できません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-330">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="b78e5-331">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-331">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="b78e5-332">**ASP.NET Core モジュール デバッグ ログ:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを開始できません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-332">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="b78e5-333">例外メッセージ:HRESULT 0x80070005 が {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 で返されました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-333">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="b78e5-334">C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll で stdout リダイレクトを停止できません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-334">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="b78e5-335">例外メッセージ:HRESULT 0x80070002 が {PATH} で返されました。</span><span class="sxs-lookup"><span data-stu-id="b78e5-335">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="b78e5-336">{PATH}\aspnetcorev2_inprocess.dll で stdout リダイレクトを開始できません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-336">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="b78e5-337">**アプリケーション ログ:** 警告 :stdout ログ ファイル \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log を作成できません、エラー コード = -2147024893。</span><span class="sxs-lookup"><span data-stu-id="b78e5-337">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="b78e5-338">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されていません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-338">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-339">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-339">Troubleshooting:</span></span>

* <span data-ttu-id="b78e5-340">*web.config* の `<aspNetCore>` 要素で指定された `stdoutLogFile` パスが存在しません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-340">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="b78e5-341">詳細については、「[ASP.NET Core モジュール:ログの作成とリダイレクト](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-341">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="b78e5-342">アプリ プール ユーザーに stdout ログ パスに対する書き込みアクセス権がありません。</span><span class="sxs-lookup"><span data-stu-id="b78e5-342">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="b78e5-343">アプリケーション構成の一般的な問題</span><span class="sxs-lookup"><span data-stu-id="b78e5-343">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b78e5-344">**ブラウザー:** HTTP エラー 500.0 - ANCM インプロセス要求ハンドラー失敗 **--または--** HTTP エラー 500.30 - ANCM インプロセス起動失敗</span><span class="sxs-lookup"><span data-stu-id="b78e5-344">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="b78e5-345">**アプリケーション ログ:** 変数</span><span class="sxs-lookup"><span data-stu-id="b78e5-345">**Application Log:** Variable</span></span>

* <span data-ttu-id="b78e5-346">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されますが空です。あるいは作成され、通常のエントリが含まれますが、アプリのところでエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="b78e5-346">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="b78e5-347">**ASP.NET Core モジュール デバッグ ログ:** 変数</span><span class="sxs-lookup"><span data-stu-id="b78e5-347">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="b78e5-348">**ブラウザー:** HTTP エラー 502.5 - 処理エラー</span><span class="sxs-lookup"><span data-stu-id="b78e5-348">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="b78e5-349">**アプリケーション ログ:** 物理ルートが 'C:\{PATH}\' のアプリケーション 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' はコマンドライン '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' でプロセスを作成しましたが、クラッシュしたか、応答しなかったか、所与のポート '{PORT}' で待機しませんでした、エラー コード = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="b78e5-349">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="b78e5-350">**ASP.NET Core モジュールの stdout ログ:** ログ ファイルが作成されましたが、空です。</span><span class="sxs-lookup"><span data-stu-id="b78e5-350">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="b78e5-351">トラブルシューティング:</span><span class="sxs-lookup"><span data-stu-id="b78e5-351">Troubleshooting:</span></span>

<span data-ttu-id="b78e5-352">このプロセスはおそらく、アプリの設定またはプログラミングに問題があり、開始できませんでした。</span><span class="sxs-lookup"><span data-stu-id="b78e5-352">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="b78e5-353">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b78e5-353">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>
