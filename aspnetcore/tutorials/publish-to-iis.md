---
title: IIS に ASP.NET Core アプリを発行する
author: rick-anderson
description: IIS サーバーで ASP.NET Core アプリをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 47f78ba78741a8e0175ce801c0c0e51f091273a8
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511393"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="3af86-103">IIS に ASP.NET Core アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="3af86-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="3af86-104">このチュートリアルでは、IIS サーバーで ASP.NET Core アプリをホストする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="3af86-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="3af86-105">このチュートリアルでは、次の件について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="3af86-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3af86-106">.NET Core ホスティング バンドルを Windows Server にインストールします。</span><span class="sxs-lookup"><span data-stu-id="3af86-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="3af86-107">IIS サイトを IIS マネージャーで作成します。</span><span class="sxs-lookup"><span data-stu-id="3af86-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="3af86-108">ASP.NET Core アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="3af86-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3af86-109">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3af86-109">Prerequisites</span></span>

* <span data-ttu-id="3af86-110">開発用のコンピューターにインストールされている [.NET Core SDK](/dotnet/core/sdk)。</span><span class="sxs-lookup"><span data-stu-id="3af86-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="3af86-111">**Web Server (IIS)** サーバー ロールで構成された Windows Server。</span><span class="sxs-lookup"><span data-stu-id="3af86-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="3af86-112">IIS で Web サイトをホストするようにサーバーが構成されていない場合、<xref:host-and-deploy/iis/index#iis-configuration> 記事の「*IIS 構成*」セクションの指示に従い、その後、このチュートリアルに戻ってください。</span><span class="sxs-lookup"><span data-stu-id="3af86-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="3af86-113">**IIS 構成と Web サイトのセキュリティには、このチュートリアルでは説明されていない概念が含まれています。**</span><span class="sxs-lookup"><span data-stu-id="3af86-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="3af86-114">IIS で本稼働アプリをホストする前に、[Microsoft IIS ドキュメント](https://www.iis.net/)と [IIS でホストする方法に関する ASP.NET Core 記事](xref:host-and-deploy/iis/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3af86-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="3af86-115">このチュートリアルで取り上げられていない IIS ホスティングの重要なシナリオ:</span><span class="sxs-lookup"><span data-stu-id="3af86-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="3af86-116">ASP.NET Core データ保護のためのレジストリ ハイブの作成</span><span class="sxs-lookup"><span data-stu-id="3af86-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="3af86-117">アプリ プールのアクセス制御リスト (ACL) の構成</span><span class="sxs-lookup"><span data-stu-id="3af86-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="3af86-118">IIS 配置の概念を集中的に取り上げるために、このチュートリアルでは、IIS で HTTPS セキュリティを構成せずにアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="3af86-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="3af86-119">HTTPS プロトコルに対して有効にするアプリをホストする方法については、この記事の「[その他の技術情報](#additional-resources)」セクションのセキュリティ トピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3af86-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="3af86-120">ASP.NET Core アプリのホスティングに関するその他の情報は <xref:host-and-deploy/iis/index> 記事にあります。</span><span class="sxs-lookup"><span data-stu-id="3af86-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="3af86-121">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="3af86-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="3af86-122">*.NET Core ホスティング バンドル*を IIS サーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="3af86-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="3af86-123">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="3af86-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="3af86-124">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="3af86-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="3af86-125">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="3af86-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="3af86-126">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="3af86-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="3af86-127">IIS サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="3af86-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="3af86-128">サーバーを再起動するか、コマンド シェルで **net stop was /y** に続けて **net start w3svc** を実行します。</span><span class="sxs-lookup"><span data-stu-id="3af86-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="3af86-129">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="3af86-129">Create the IIS site</span></span>

1. <span data-ttu-id="3af86-130">IIS サーバーで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3af86-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="3af86-131">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="3af86-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="3af86-132">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="3af86-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="3af86-133">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="3af86-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="3af86-134">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3af86-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="3af86-135">**[サイト名]** を指定し、 **[物理パス]** には作成したアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="3af86-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="3af86-136">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="3af86-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="3af86-137">ASP.NET Core Razor ページ アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="3af86-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="3af86-138"><xref:getting-started> チュートリアルに従い、Razor Pages アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="3af86-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="3af86-139">アプリを発行および配置する</span><span class="sxs-lookup"><span data-stu-id="3af86-139">Publish and deploy the app</span></span>

<span data-ttu-id="3af86-140">"*アプリを発行する*" とは、サーバーでホストできるコンパイル済みのアプリを生成するということです。</span><span class="sxs-lookup"><span data-stu-id="3af86-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="3af86-141">"*アプリを展開する*" とは、発行済みのアプリをホスティング システムに移動するということです。</span><span class="sxs-lookup"><span data-stu-id="3af86-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="3af86-142">発行手順は [.NET Core SDK](/dotnet/core/sdk) で処理されます。一方で、展開手順はさまざまな手法で処理できます。</span><span class="sxs-lookup"><span data-stu-id="3af86-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="3af86-143">このチュートリアルでは、"*フォルダー*" 展開手法を採用しています。</span><span class="sxs-lookup"><span data-stu-id="3af86-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="3af86-144">アプリはフォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="3af86-144">The app is published to a folder.</span></span>
* <span data-ttu-id="3af86-145">フォルダーの内容が IIS サイトのフォルダーに移動されます (IIS マネージャーのサイトの**物理パス**)。</span><span class="sxs-lookup"><span data-stu-id="3af86-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3af86-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3af86-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3af86-147">**[ソリューション エクスプローラー]** でプロジェクトを右クリックし、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3af86-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="3af86-148">**[発行先を選択]** ダイアログで、 **[フォルダー]** 発行オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="3af86-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="3af86-149">**フォルダーまたはファイル共有**パスを選択します。</span><span class="sxs-lookup"><span data-stu-id="3af86-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="3af86-150">ネットワーク共有として開発用のコンピューターで利用できる IIS サイトのフォルダーを作成した場合、共有へのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="3af86-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="3af86-151">現在のユーザーに、共有に発行するための書き込みアクセスを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="3af86-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="3af86-152">IIS サーバー上の IIS サイト フォルダーに直接展開できない場合、リムーバブル メディア上のフォルダーに発行し、IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに発行済みのアプリを物理的に移動します。</span><span class="sxs-lookup"><span data-stu-id="3af86-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="3af86-153">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに *bin/Release/{TARGET FRAMEWORK}/publish* フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="3af86-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3af86-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3af86-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="3af86-155">コマンド シェルで、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使用してリリース構成でアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="3af86-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="3af86-156">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに *bin/Release/{TARGET FRAMEWORK}/publish* フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="3af86-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3af86-157">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3af86-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3af86-158">**[ソリューション]** でプロジェクトを右クリックし、 **[発行]**  >  **[フォルダーに発行]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3af86-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="3af86-159">**[フォルダーを選択してください]** パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="3af86-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="3af86-160">ネットワーク共有として開発用のコンピューターで利用できる IIS サイトのフォルダーを作成した場合、共有へのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="3af86-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="3af86-161">現在のユーザーに、共有に発行するための書き込みアクセスを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="3af86-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="3af86-162">IIS サーバー上の IIS サイト フォルダーに直接展開できない場合、リムーバブル メディア上のフォルダーに発行し、IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに発行済みのアプリを物理的に移動します。</span><span class="sxs-lookup"><span data-stu-id="3af86-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="3af86-163">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに *bin/Release/{TARGET FRAMEWORK}/publish* フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="3af86-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="3af86-164">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="3af86-164">Browse the website</span></span>

<span data-ttu-id="3af86-165">アプリには、最初の要求の受信後、ブラウザーでアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3af86-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="3af86-166">サイトの IIS マネージャーで設定したエンドポイント バインドでアプリへの要求を行います。</span><span class="sxs-lookup"><span data-stu-id="3af86-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3af86-167">次の手順</span><span class="sxs-lookup"><span data-stu-id="3af86-167">Next steps</span></span>

<span data-ttu-id="3af86-168">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="3af86-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3af86-169">.NET Core ホスティング バンドルを Windows Server にインストールします。</span><span class="sxs-lookup"><span data-stu-id="3af86-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="3af86-170">IIS サイトを IIS マネージャーで作成します。</span><span class="sxs-lookup"><span data-stu-id="3af86-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="3af86-171">ASP.NET Core アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="3af86-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="3af86-172">IIS で ASP.NET Core アプリをホストする方法の詳細については、IIS の概要に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3af86-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="3af86-173">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3af86-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="3af86-174">ASP.NET Core ドキュメント セットの記事</span><span class="sxs-lookup"><span data-stu-id="3af86-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="3af86-175">ASP.NET Core アプリの展開に関する記事</span><span class="sxs-lookup"><span data-stu-id="3af86-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="3af86-176">Visual Studio for Mac を使用してフォルダーに Web アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="3af86-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="3af86-177">IIS HTTPS 構成に関する記事</span><span class="sxs-lookup"><span data-stu-id="3af86-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="3af86-178">IIS マネージャーで SSL を構成する</span><span class="sxs-lookup"><span data-stu-id="3af86-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="3af86-179">IIS で SSL を設定する方法</span><span class="sxs-lookup"><span data-stu-id="3af86-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="3af86-180">IIS と Windows Server に関する記事</span><span class="sxs-lookup"><span data-stu-id="3af86-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="3af86-181">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="3af86-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="3af86-182">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="3af86-182">Windows Server technical content library</span></span>](/windows-server/windows-server)
