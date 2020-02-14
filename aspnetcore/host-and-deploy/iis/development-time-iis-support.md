---
title: Visual Studio for ASP.NET Core の開発時 IIS サポート
author: guardrex
description: ASP.NET Core アプリが Windows Server 上の IIS で実行されている場合に、そのデバッグのサポートを検出します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: f03ee94e5890c58addef4ba0d3ba7a4af6b659e7
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172105"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="89723-103">Visual Studio for ASP.NET Core の開発時 IIS サポート</span><span class="sxs-lookup"><span data-stu-id="89723-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="89723-104">著者: [Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="89723-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89723-105">この記事では、Windows Server 上の IIS で実行されている ASP.NET Core アプリをデバッグするための、[Visual Studio](https://visualstudio.microsoft.com) のサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="89723-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="89723-106">このトピックでは、このシナリオを有効にしてプロジェクトを設定する手順を示します。</span><span class="sxs-lookup"><span data-stu-id="89723-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="89723-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="89723-107">Prerequisites</span></span>

* <span data-ttu-id="89723-108">[Visual Studio (Windows 版)](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="89723-108">[Visual Studio for Windows](https://visualstudio.microsoft.com/downloads/)</span></span>
* <span data-ttu-id="89723-109">**ASP.NET および Web の開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="89723-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="89723-110">**.NET Core クロスプラットフォームの開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="89723-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="89723-111">X.509 セキュリティ証明書 (HTTPS のサポート用)</span><span class="sxs-lookup"><span data-stu-id="89723-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="89723-112">IIS を有効にする</span><span class="sxs-lookup"><span data-stu-id="89723-112">Enable IIS</span></span>

1. <span data-ttu-id="89723-113">Windows で、 **[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** > **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="89723-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="89723-114">**[インターネット インフォメーション サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="89723-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="89723-115">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-115">Select **OK**.</span></span>

<span data-ttu-id="89723-116">IIS のインストールには、システムの再起動が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="89723-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="89723-117">IIS の構成</span><span class="sxs-lookup"><span data-stu-id="89723-117">Configure IIS</span></span>

<span data-ttu-id="89723-118">IIS には、次のように構成された Web サイトが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="89723-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="89723-119">**ホスト名** &ndash; 通常、`localhost` の**ホスト名**では**既定の Web サイト**が使用されます。</span><span class="sxs-lookup"><span data-stu-id="89723-119">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="89723-120">ただし、一意のホスト名を持つ任意の有効な IIS Web サイトが動作します。</span><span class="sxs-lookup"><span data-stu-id="89723-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="89723-121">**サイト バインド**</span><span class="sxs-lookup"><span data-stu-id="89723-121">**Site Binding**</span></span>
  * <span data-ttu-id="89723-122">HTTPS を必要とするアプリの場合は、証明書でポート 443 へのバインドを作成します。</span><span class="sxs-lookup"><span data-stu-id="89723-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="89723-123">通常は、**IIS Express 開発証明書**が使用されますが、任意の有効な証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="89723-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="89723-124">HTTP を使用するアプリの場合は、ポート 80 へのバインドが存在することを確認するか、または新しいサイト用にポート 80 へのバインドを作成します。</span><span class="sxs-lookup"><span data-stu-id="89723-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="89723-125">HTTP または HTTPS のいずれかに対する 1 つのバインドを使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="89723-126">**HTTP と HTTPS の両方のポートに同時にバインドすることはサポートされていません。**</span><span class="sxs-lookup"><span data-stu-id="89723-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="89723-127">Visual Studio の開発時 IIS サポートを有効にする</span><span class="sxs-lookup"><span data-stu-id="89723-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="89723-128">Visual Studio インストーラーを起動します。</span><span class="sxs-lookup"><span data-stu-id="89723-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="89723-129">IIS 開発時のサポート用に使用する Visual Studio のインストールの **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="89723-130">**ASP.NET と Web 開発**ワークロードで、**開発時 IIS サポート** コンポーネントを探してインストールします。</span><span class="sxs-lookup"><span data-stu-id="89723-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="89723-131">ワークロードの右側にある **[インストールの詳細]** パネルの**開発時 IIS サポート**の下の **[省略可能]** セクションの一覧に、コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89723-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="89723-132">このコンポーネントにより、IIS を使用した ASP.NET Core アプリの実行に必要なネイティブの IIS モジュールである [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="89723-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="89723-133">プロジェクトを構成する</span><span class="sxs-lookup"><span data-stu-id="89723-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="89723-134">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="89723-134">HTTPS redirection</span></span>

<span data-ttu-id="89723-135">HTTPS が必要な新しいプロジェクトの場合は、 **[新しい ASP.NET Core Web アプリケーションを作成する]** ウィンドウで、 **[HTTPS 用の構成]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="89723-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="89723-136">チェック ボックスをオンにすると、アプリの作成時に [HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)がアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="89723-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="89723-137">HTTPS が必要な既存のプロジェクトでは、`Startup.Configure` 内で HTTPS リダイレクトと HSTS ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="89723-138">詳細については、「<xref:security/enforcing-ssl>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89723-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="89723-139">HTTP を使用するプロジェクトの場合は、[HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)はアプリに追加されません。</span><span class="sxs-lookup"><span data-stu-id="89723-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="89723-140">アプリの構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="89723-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="89723-141">IIS 起動プロファイル</span><span class="sxs-lookup"><span data-stu-id="89723-141">IIS launch profile</span></span>

<span data-ttu-id="89723-142">新しい起動プロファイルを作成して、開発時 IIS サポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="89723-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="89723-143">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="89723-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="89723-144">**[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="89723-144">Select **Properties**.</span></span> <span data-ttu-id="89723-145">**[デバッグ]** タブを開きます。</span><span class="sxs-lookup"><span data-stu-id="89723-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="89723-146">**[プロファイル]** で、 **[新規]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="89723-147">ポップアップ ウィンドウで、プロファイルに "IIS" という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="89723-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="89723-148">**[OK]** をクリックして、プロファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="89723-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="89723-149">**[起動]** の設定で、一覧から **[IIS]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="89723-150">**[ブラウザーの起動]** チェック ボックスをオンにして、エンドポイント URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="89723-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="89723-151">アプリで HTTPS が必要な場合は、HTTPS エンドポイント (`https://`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="89723-152">HTTP の場合は、HTTP エンドポイント (`http://`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="89723-153">[前に指定した IIS 構成で使用されている](#configure-iis)ものと同じホスト名とポートを指定します。通常は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="89723-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="89723-154">URL の最後でアプリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="89723-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="89723-155">たとえば、`https://localhost/WebApplication1` (HTTPS) や `http://localhost/WebApplication1`(HTTP) は有効なエンドポイント URL です。</span><span class="sxs-lookup"><span data-stu-id="89723-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="89723-156">**[環境変数]** セクションで、 **[追加]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="89723-157">`ASPNETCORE_ENVIRONMENT` の **[名前]** および `Development` の **[値]** で環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="89723-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="89723-158">**[Web サーバーの設定]** 領域で、 **[アプリの URL]** を **[ブラウザーの起動]** エンドポイント URL に使用したものと同じ値に設定します。</span><span class="sxs-lookup"><span data-stu-id="89723-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="89723-159">Visual Studio 2019 以降の **[ホスティング モデル]** の設定では、 **[既定]** を選択して、プロジェクトによって使用されるホスティング モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="89723-160">プロジェクトのプロジェクト ファイルで `<AspNetCoreHostingModel>` プロパティが設定されている場合は、プロパティ (`InProcess` または `OutOfProcess`) の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="89723-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="89723-161">プロパティが存在しない場合は、アプリの既定のホスティング モデルが使用され、それはインプロセスです。</span><span class="sxs-lookup"><span data-stu-id="89723-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="89723-162">アプリの通常のホスティング モデルとは異なるホスティング モデルを明示的に設定する必要があるアプリの場合は、必要に応じて、 **[ホスティング モデル]** を `In Process` または `Out Of Process` に設定します。</span><span class="sxs-lookup"><span data-stu-id="89723-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="89723-163">プロファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="89723-163">Save the profile.</span></span>

<span data-ttu-id="89723-164">Visual Studio を使用していない場合は、起動プロファイルを *Properties* フォルダーの [launchSettings.json](https://json.schemastore.org/launchsettings) ファイルに手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="89723-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="89723-165">次の例では、HTTPS プロトコルを使用するようにプロファイルを構成しています。</span><span class="sxs-lookup"><span data-stu-id="89723-165">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="89723-166">`applicationUrl` エンドポイントと `launchUrl` エンドポイントが一致し、IIS バインド構成と同じプロトコル (HTTP または HTTPS) が使用されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="89723-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="89723-167">プロジェクトを実行する</span><span class="sxs-lookup"><span data-stu-id="89723-167">Run the project</span></span>

<span data-ttu-id="89723-168">Visual Studio を管理者として実行します。</span><span class="sxs-lookup"><span data-stu-id="89723-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="89723-169">ビルド構成のドロップダウン リストが **[デバッグ]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="89723-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="89723-170">[[デバッグの開始] ボタン](/visualstudio/debugger/debugger-feature-tour)を **[IIS]** プロファイルに設定し、ボタンを選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="89723-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="89723-171">管理者として実行していない場合、Visual Studio によって再起動を求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="89723-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="89723-172">その場合は、Visual Studio を再起動します。</span><span class="sxs-lookup"><span data-stu-id="89723-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="89723-173">信頼されていない開発証明書を使用すると、信頼されていない証明書に対する例外を作成するように、ブラウザーによって求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="89723-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="89723-174">[マイ コードのみ](/visualstudio/debugger/just-my-code)とコンパイラの最適化を使用してリリースのビルド構成をデバッグすると、エクスペリエンスの品質が低下します。</span><span class="sxs-lookup"><span data-stu-id="89723-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="89723-175">たとえば、ブレーク ポイントがヒットしません。</span><span class="sxs-lookup"><span data-stu-id="89723-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89723-176">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="89723-176">Additional resources</span></span>

* [<span data-ttu-id="89723-177">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="89723-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89723-178">この記事では、Windows Server 上の IIS で実行されている ASP.NET Core アプリをデバッグするための、[Visual Studio](https://visualstudio.microsoft.com) のサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="89723-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="89723-179">このトピックでは、このシナリオを有効にしてプロジェクトを設定する手順を示します。</span><span class="sxs-lookup"><span data-stu-id="89723-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="89723-180">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="89723-180">Prerequisites</span></span>

* <span data-ttu-id="89723-181">[Visual Studio (Windows 版)](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="89723-181">[Visual Studio for Windows](https://visualstudio.microsoft.com/downloads/)</span></span>
* <span data-ttu-id="89723-182">**ASP.NET および Web の開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="89723-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="89723-183">**.NET Core クロスプラットフォームの開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="89723-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="89723-184">X.509 セキュリティ証明書 (HTTPS のサポート用)</span><span class="sxs-lookup"><span data-stu-id="89723-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="89723-185">IIS を有効にする</span><span class="sxs-lookup"><span data-stu-id="89723-185">Enable IIS</span></span>

1. <span data-ttu-id="89723-186">Windows で、 **[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** > **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="89723-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="89723-187">**[インターネット インフォメーション サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="89723-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="89723-188">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-188">Select **OK**.</span></span>

<span data-ttu-id="89723-189">IIS のインストールには、システムの再起動が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="89723-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="89723-190">IIS の構成</span><span class="sxs-lookup"><span data-stu-id="89723-190">Configure IIS</span></span>

<span data-ttu-id="89723-191">IIS には、次のように構成された Web サイトが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="89723-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="89723-192">**ホスト名** &ndash; 通常、`localhost` の**ホスト名**では**既定の Web サイト**が使用されます。</span><span class="sxs-lookup"><span data-stu-id="89723-192">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="89723-193">ただし、一意のホスト名を持つ任意の有効な IIS Web サイトが動作します。</span><span class="sxs-lookup"><span data-stu-id="89723-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="89723-194">**サイト バインド**</span><span class="sxs-lookup"><span data-stu-id="89723-194">**Site Binding**</span></span>
  * <span data-ttu-id="89723-195">HTTPS を必要とするアプリの場合は、証明書でポート 443 へのバインドを作成します。</span><span class="sxs-lookup"><span data-stu-id="89723-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="89723-196">通常は、**IIS Express 開発証明書**が使用されますが、任意の有効な証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="89723-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="89723-197">HTTP を使用するアプリの場合は、ポート 80 へのバインドが存在することを確認するか、または新しいサイト用にポート 80 へのバインドを作成します。</span><span class="sxs-lookup"><span data-stu-id="89723-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="89723-198">HTTP または HTTPS のいずれかに対する 1 つのバインドを使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="89723-199">**HTTP と HTTPS の両方のポートに同時にバインドすることはサポートされていません。**</span><span class="sxs-lookup"><span data-stu-id="89723-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="89723-200">Visual Studio の開発時 IIS サポートを有効にする</span><span class="sxs-lookup"><span data-stu-id="89723-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="89723-201">Visual Studio インストーラーを起動します。</span><span class="sxs-lookup"><span data-stu-id="89723-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="89723-202">IIS 開発時のサポート用に使用する Visual Studio のインストールの **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="89723-203">**ASP.NET と Web 開発**ワークロードで、**開発時 IIS サポート** コンポーネントを探してインストールします。</span><span class="sxs-lookup"><span data-stu-id="89723-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="89723-204">ワークロードの右側にある **[インストールの詳細]** パネルの**開発時 IIS サポート**の下の **[省略可能]** セクションの一覧に、コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89723-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="89723-205">このコンポーネントにより、IIS を使用した ASP.NET Core アプリの実行に必要なネイティブの IIS モジュールである [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="89723-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="89723-206">プロジェクトを構成する</span><span class="sxs-lookup"><span data-stu-id="89723-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="89723-207">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="89723-207">HTTPS redirection</span></span>

<span data-ttu-id="89723-208">HTTPS が必要な新しいプロジェクトの場合は、 **[新しい ASP.NET Core Web アプリケーションを作成する]** ウィンドウで、 **[HTTPS 用の構成]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="89723-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="89723-209">チェック ボックスをオンにすると、アプリの作成時に [HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)がアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="89723-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="89723-210">HTTPS が必要な既存のプロジェクトでは、`Startup.Configure` 内で HTTPS リダイレクトと HSTS ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="89723-211">詳細については、「<xref:security/enforcing-ssl>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="89723-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="89723-212">HTTP を使用するプロジェクトの場合は、[HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)はアプリに追加されません。</span><span class="sxs-lookup"><span data-stu-id="89723-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="89723-213">アプリの構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="89723-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="89723-214">IIS 起動プロファイル</span><span class="sxs-lookup"><span data-stu-id="89723-214">IIS launch profile</span></span>

<span data-ttu-id="89723-215">新しい起動プロファイルを作成して、開発時 IIS サポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="89723-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="89723-216">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="89723-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="89723-217">**[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="89723-217">Select **Properties**.</span></span> <span data-ttu-id="89723-218">**[デバッグ]** タブを開きます。</span><span class="sxs-lookup"><span data-stu-id="89723-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="89723-219">**[プロファイル]** で、 **[新規]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="89723-220">ポップアップ ウィンドウで、プロファイルに "IIS" という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="89723-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="89723-221">**[OK]** をクリックして、プロファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="89723-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="89723-222">**[起動]** の設定で、一覧から **[IIS]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="89723-223">**[ブラウザーの起動]** チェック ボックスをオンにして、エンドポイント URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="89723-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="89723-224">アプリで HTTPS が必要な場合は、HTTPS エンドポイント (`https://`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="89723-225">HTTP の場合は、HTTP エンドポイント (`http://`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="89723-226">[前に指定した IIS 構成で使用されている](#configure-iis)ものと同じホスト名とポートを指定します。通常は `localhost` です。</span><span class="sxs-lookup"><span data-stu-id="89723-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="89723-227">URL の最後でアプリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="89723-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="89723-228">たとえば、`https://localhost/WebApplication1` (HTTPS) や `http://localhost/WebApplication1`(HTTP) は有効なエンドポイント URL です。</span><span class="sxs-lookup"><span data-stu-id="89723-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="89723-229">**[環境変数]** セクションで、 **[追加]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="89723-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="89723-230">`ASPNETCORE_ENVIRONMENT` の **[名前]** および `Development` の **[値]** で環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="89723-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="89723-231">**[Web サーバーの設定]** 領域で、 **[アプリの URL]** を **[ブラウザーの起動]** エンドポイント URL に使用したものと同じ値に設定します。</span><span class="sxs-lookup"><span data-stu-id="89723-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="89723-232">Visual Studio 2019 以降の **[ホスティング モデル]** の設定では、 **[既定]** を選択して、プロジェクトによって使用されるホスティング モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="89723-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="89723-233">プロジェクトのプロジェクト ファイルで `<AspNetCoreHostingModel>` プロパティが設定されている場合は、プロパティ (`InProcess` または `OutOfProcess`) の値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="89723-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="89723-234">プロパティが存在しない場合は、アプリの既定のホスティング モデルが使用され、それはアウトプロセスです。</span><span class="sxs-lookup"><span data-stu-id="89723-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="89723-235">アプリの通常のホスティング モデルとは異なるホスティング モデルを明示的に設定する必要があるアプリの場合は、必要に応じて、 **[ホスティング モデル]** を `In Process` または `Out Of Process` に設定します。</span><span class="sxs-lookup"><span data-stu-id="89723-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="89723-236">プロファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="89723-236">Save the profile.</span></span>

<span data-ttu-id="89723-237">Visual Studio を使用していない場合は、起動プロファイルを *Properties* フォルダーの [launchSettings.json](https://json.schemastore.org/launchsettings) ファイルに手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="89723-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="89723-238">次の例では、HTTPS プロトコルを使用するようにプロファイルを構成しています。</span><span class="sxs-lookup"><span data-stu-id="89723-238">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="89723-239">`applicationUrl` エンドポイントと `launchUrl` エンドポイントが一致し、IIS バインド構成と同じプロトコル (HTTP または HTTPS) が使用されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="89723-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="89723-240">プロジェクトを実行する</span><span class="sxs-lookup"><span data-stu-id="89723-240">Run the project</span></span>

<span data-ttu-id="89723-241">Visual Studio を管理者として実行します。</span><span class="sxs-lookup"><span data-stu-id="89723-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="89723-242">ビルド構成のドロップダウン リストが **[デバッグ]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="89723-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="89723-243">[[デバッグの開始] ボタン](/visualstudio/debugger/debugger-feature-tour)を **[IIS]** プロファイルに設定し、ボタンを選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="89723-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="89723-244">管理者として実行していない場合、Visual Studio によって再起動を求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="89723-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="89723-245">その場合は、Visual Studio を再起動します。</span><span class="sxs-lookup"><span data-stu-id="89723-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="89723-246">信頼されていない開発証明書を使用すると、信頼されていない証明書に対する例外を作成するように、ブラウザーによって求められる場合があります。</span><span class="sxs-lookup"><span data-stu-id="89723-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="89723-247">[マイ コードのみ](/visualstudio/debugger/just-my-code)とコンパイラの最適化を使用してリリースのビルド構成をデバッグすると、エクスペリエンスの品質が低下します。</span><span class="sxs-lookup"><span data-stu-id="89723-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="89723-248">たとえば、ブレーク ポイントがヒットしません。</span><span class="sxs-lookup"><span data-stu-id="89723-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89723-249">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="89723-249">Additional resources</span></span>

* [<span data-ttu-id="89723-250">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="89723-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
