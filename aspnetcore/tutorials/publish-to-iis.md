---
title: IIS に ASP.NET Core アプリを発行する
author: guardrex
description: IIS サーバーで ASP.NET Core アプリをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: c31beae16f46153daac188ab1638e5530584ac88
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927092"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="dbed4-103">IIS に ASP.NET Core アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="dbed4-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="dbed4-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dbed4-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dbed4-105">このチュートリアルでは、IIS サーバーで ASP.NET Core アプリをホストする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-105">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="dbed4-106">このチュートリアルでは、次の件について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="dbed4-106">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dbed4-107">.NET Core ホスティング バンドルを Windows Server にインストールします。</span><span class="sxs-lookup"><span data-stu-id="dbed4-107">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="dbed4-108">IIS サイトを IIS マネージャーで作成します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-108">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="dbed4-109">ASP.NET Core アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-109">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dbed4-110">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="dbed4-110">Prerequisites</span></span>

* <span data-ttu-id="dbed4-111">開発用のコンピューターにインストールされている [.NET Core SDK](/dotnet/core/sdk)。</span><span class="sxs-lookup"><span data-stu-id="dbed4-111">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="dbed4-112">**Web Server (IIS)** サーバー ロールで構成された Windows Server。</span><span class="sxs-lookup"><span data-stu-id="dbed4-112">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="dbed4-113">IIS で Web サイトをホストするようにサーバーが構成されていない場合、<xref:host-and-deploy/iis/index#iis-configuration> 記事の「*IIS 構成*」セクションの指示に従い、その後、このチュートリアルに戻ってください。</span><span class="sxs-lookup"><span data-stu-id="dbed4-113">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="dbed4-114">**IIS 構成と Web サイトのセキュリティには、このチュートリアルでは説明されていない概念が含まれています。**</span><span class="sxs-lookup"><span data-stu-id="dbed4-114">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="dbed4-115">IIS で本稼働アプリをホストする前に、[Microsoft IIS ドキュメント](https://www.iis.net/)と [IIS でホストする方法に関する ASP.NET Core 記事](xref:host-and-deploy/iis/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dbed4-115">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="dbed4-116">このチュートリアルで取り上げられていない IIS ホスティングの重要なシナリオ:</span><span class="sxs-lookup"><span data-stu-id="dbed4-116">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="dbed4-117">ASP.NET Core データ保護のためのレジストリ ハイブの作成</span><span class="sxs-lookup"><span data-stu-id="dbed4-117">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="dbed4-118">アプリ プールのアクセス制御リスト (ACL) の構成</span><span class="sxs-lookup"><span data-stu-id="dbed4-118">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="dbed4-119">IIS 配置の概念を集中的に取り上げるために、このチュートリアルでは、IIS で HTTPS セキュリティを構成せずにアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-119">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="dbed4-120">HTTPS プロトコルに対して有効にするアプリをホストする方法については、この記事の「[その他の技術情報](#additional-resources)」セクションのセキュリティ トピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dbed4-120">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="dbed4-121">ASP.NET Core アプリのホスティングに関するその他の情報は <xref:host-and-deploy/iis/index> 記事にあります。</span><span class="sxs-lookup"><span data-stu-id="dbed4-121">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="dbed4-122">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="dbed4-122">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="dbed4-123">*.NET Core ホスティング バンドル*を IIS サーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="dbed4-123">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="dbed4-124">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="dbed4-124">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="dbed4-125">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="dbed4-125">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="dbed4-126">システムにインターネット接続が設定されていない場合は、.NET Core ホスティング バンドルをインストールする前に、[Microsoft Visual C++ 2015 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=53840)を入手してインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="dbed4-126">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

<span data-ttu-id="dbed4-127">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="dbed4-127">Download the installer using the following link:</span></span>

[<span data-ttu-id="dbed4-128">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="dbed4-128">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="dbed4-129">IIS サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-129">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="dbed4-130">サーバーを再起動するか、コマンド シェルで **net stop was /y** に続けて **net start w3svc** を実行します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-130">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="dbed4-131">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="dbed4-131">Create the IIS site</span></span>

1. <span data-ttu-id="dbed4-132">IIS サーバーで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-132">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="dbed4-133">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="dbed4-133">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="dbed4-134">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="dbed4-134">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="dbed4-135">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="dbed4-135">Right-click the **Sites** folder.</span></span> <span data-ttu-id="dbed4-136">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-136">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="dbed4-137">**[サイト名]** を指定し、 **[物理パス]** には作成したアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-137">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="dbed4-138">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-138">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="dbed4-139">ASP.NET Core Razor ページ アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="dbed4-139">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="dbed4-140"><xref:getting-started> チュートリアルに従い、Razor Pages アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-140">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="dbed4-141">アプリを発行および配置する</span><span class="sxs-lookup"><span data-stu-id="dbed4-141">Publish and deploy the app</span></span>

<span data-ttu-id="dbed4-142">"*アプリを発行する*" とは、サーバーでホストできるコンパイル済みのアプリを生成するということです。</span><span class="sxs-lookup"><span data-stu-id="dbed4-142">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="dbed4-143">"*アプリを展開する*" とは、発行済みのアプリをホスティング システムに移動するということです。</span><span class="sxs-lookup"><span data-stu-id="dbed4-143">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="dbed4-144">発行手順は [.NET Core SDK](/dotnet/core/sdk) で処理されます。一方で、展開手順はさまざまな手法で処理できます。</span><span class="sxs-lookup"><span data-stu-id="dbed4-144">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="dbed4-145">このチュートリアルでは、"*フォルダー*" 展開手法を採用しています。</span><span class="sxs-lookup"><span data-stu-id="dbed4-145">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="dbed4-146">アプリはフォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="dbed4-146">The app is published to a folder.</span></span>
* <span data-ttu-id="dbed4-147">フォルダーの内容が IIS サイトのフォルダーに移動されます (IIS マネージャーのサイトの**物理パス**)。</span><span class="sxs-lookup"><span data-stu-id="dbed4-147">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbed4-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbed4-148">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dbed4-149">**[ソリューション エクスプローラー]** でプロジェクトを右クリックし、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-149">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="dbed4-150">**[発行先を選択]** ダイアログで、 **[フォルダー]** 発行オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-150">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="dbed4-151">**フォルダーまたはファイル共有**パスを選択します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-151">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="dbed4-152">ネットワーク共有として開発用のコンピューターで利用できる IIS サイトのフォルダーを作成した場合、共有へのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-152">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="dbed4-153">現在のユーザーに、共有に発行するための書き込みアクセスを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="dbed4-153">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="dbed4-154">IIS サーバー上の IIS サイト フォルダーに直接展開できない場合、リムーバブル メディア上のフォルダーに発行し、IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに発行済みのアプリを物理的に移動します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-154">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="dbed4-155">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに *bin/Release/{TARGET FRAMEWORK}/publish* フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-155">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="dbed4-156">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="dbed4-156">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="dbed4-157">コマンド シェルで、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使用してリリース構成でアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-157">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="dbed4-158">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに *bin/Release/{TARGET FRAMEWORK}/publish* フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-158">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbed4-159">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dbed4-159">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="dbed4-160">**[ソリューション]** でプロジェクトを右クリックし、 **[発行]** 、 **[フォルダーに発行]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-160">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="dbed4-161">**[フォルダーを選択してください]** パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-161">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="dbed4-162">ネットワーク共有として開発用のコンピューターで利用できる IIS サイトのフォルダーを作成した場合、共有へのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-162">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="dbed4-163">現在のユーザーに、共有に発行するための書き込みアクセスを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="dbed4-163">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="dbed4-164">IIS サーバー上の IIS サイト フォルダーに直接展開できない場合、リムーバブル メディア上のフォルダーに発行し、IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに発行済みのアプリを物理的に移動します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-164">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="dbed4-165">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに *bin/Release/{TARGET FRAMEWORK}/publish* フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-165">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="dbed4-166">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="dbed4-166">Browse the website</span></span>

<span data-ttu-id="dbed4-167">アプリには、最初の要求の受信後、ブラウザーでアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="dbed4-167">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="dbed4-168">サイトの IIS マネージャーで設定したエンドポイント バインドでアプリへの要求を行います。</span><span class="sxs-lookup"><span data-stu-id="dbed4-168">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dbed4-169">次の手順</span><span class="sxs-lookup"><span data-stu-id="dbed4-169">Next steps</span></span>

<span data-ttu-id="dbed4-170">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="dbed4-170">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dbed4-171">.NET Core ホスティング バンドルを Windows Server にインストールします。</span><span class="sxs-lookup"><span data-stu-id="dbed4-171">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="dbed4-172">IIS サイトを IIS マネージャーで作成します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-172">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="dbed4-173">ASP.NET Core アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="dbed4-173">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="dbed4-174">IIS で ASP.NET Core アプリをホストする方法の詳細については、IIS の概要に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dbed4-174">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="dbed4-175">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dbed4-175">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="dbed4-176">ASP.NET Core ドキュメント セットの記事</span><span class="sxs-lookup"><span data-stu-id="dbed4-176">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="dbed4-177">ASP.NET Core アプリの展開に関する記事</span><span class="sxs-lookup"><span data-stu-id="dbed4-177">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="dbed4-178">Visual Studio for Mac を使用してフォルダーに Web アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="dbed4-178">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="dbed4-179">IIS HTTPS 構成に関する記事</span><span class="sxs-lookup"><span data-stu-id="dbed4-179">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="dbed4-180">IIS マネージャーで SSL を構成する</span><span class="sxs-lookup"><span data-stu-id="dbed4-180">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="dbed4-181">IIS で SSL を設定する方法</span><span class="sxs-lookup"><span data-stu-id="dbed4-181">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="dbed4-182">IIS と Windows Server に関する記事</span><span class="sxs-lookup"><span data-stu-id="dbed4-182">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="dbed4-183">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="dbed4-183">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="dbed4-184">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="dbed4-184">Windows Server technical content library</span></span>](/windows-server/windows-server)
