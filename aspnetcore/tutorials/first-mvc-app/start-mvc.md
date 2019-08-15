---
title: ASP.NET Core MVC の概要
author: rick-anderson
description: ASP.NET Core MVC の概要について説明します。
ms.author: riande
ms.date: 08/05/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 36f4811f876a6e35440445103a1f86ae06b31b6a
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022520"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="f498a-103">ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="f498a-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="f498a-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f498a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f498a-105">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="f498a-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f498a-106">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="f498a-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="f498a-107">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f498a-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f498a-108">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="f498a-108">Create a web app.</span></span>
> * <span data-ttu-id="f498a-109">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="f498a-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f498a-110">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="f498a-110">Work with a database.</span></span>
> * <span data-ttu-id="f498a-111">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="f498a-111">Add search and validation.</span></span>

<span data-ttu-id="f498a-112">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="f498a-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f498a-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f498a-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f498a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f498a-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f498a-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f498a-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f498a-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f498a-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f498a-117">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="f498a-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f498a-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f498a-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f498a-119">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="f498a-120">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f498a-122">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f498a-123">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="f498a-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)

* <span data-ttu-id="f498a-125">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="f498a-126">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="f498a-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="f498a-127">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="f498a-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f498a-128">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="f498a-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f498a-129">これは基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="f498a-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f498a-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f498a-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f498a-131">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="f498a-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f498a-132">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f498a-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f498a-133">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="f498a-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f498a-134">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="f498a-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f498a-135">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f498a-135">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f498a-136">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="f498a-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f498a-137">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="f498a-137">Select **Yes**</span></span>

  * <span data-ttu-id="f498a-138">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f498a-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f498a-139">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="f498a-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f498a-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f498a-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f498a-141">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-141">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f498a-143">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション (Model-View-Controller)]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="f498a-145">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* **[.NET Core 3.0]** の **[ターゲット フレームワーク]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="f498a-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.0**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="f498a-146">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="f498a-147">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="f498a-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f498a-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f498a-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f498a-149">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f498a-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f498a-150">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f498a-151">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f498a-152">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="f498a-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f498a-153">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f498a-154">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="f498a-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f498a-155">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="f498a-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f498a-156">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="f498a-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f498a-158">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="f498a-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="f498a-160">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="f498a-160">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f498a-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f498a-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f498a-163">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="f498a-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f498a-164">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="f498a-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f498a-165">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f498a-166">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="f498a-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f498a-167">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f498a-168">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="f498a-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f498a-169">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="f498a-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f498a-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f498a-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f498a-172">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="f498a-172">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f498a-173">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="f498a-173">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f498a-174">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-174">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f498a-175">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="f498a-175">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f498a-176">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-176">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f498a-177">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-177">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f498a-178">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="f498a-178">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="f498a-179">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="f498a-179">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f498a-181">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="f498a-181">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f498a-182">次へ</span><span class="sxs-lookup"><span data-stu-id="f498a-182">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f498a-183">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="f498a-183">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f498a-184">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="f498a-184">The app manages a database of movie titles.</span></span> <span data-ttu-id="f498a-185">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f498a-185">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f498a-186">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="f498a-186">Create a web app.</span></span>
> * <span data-ttu-id="f498a-187">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="f498a-187">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f498a-188">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="f498a-188">Work with a database.</span></span>
> * <span data-ttu-id="f498a-189">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="f498a-189">Add search and validation.</span></span>

<span data-ttu-id="f498a-190">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="f498a-190">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f498a-191">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f498a-191">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f498a-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f498a-192">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f498a-193">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f498a-193">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f498a-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f498a-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="f498a-195">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="f498a-195">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f498a-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f498a-196">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f498a-197">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-197">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="f498a-198">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-198">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f498a-200">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-200">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f498a-201">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="f498a-201">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)


* <span data-ttu-id="f498a-203">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-203">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="f498a-204">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="f498a-204">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="f498a-205">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="f498a-205">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f498a-206">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="f498a-206">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f498a-207">これは基本的なスターター プロジェクトなので、ここから始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f498a-207">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f498a-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f498a-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f498a-209">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="f498a-209">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f498a-210">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f498a-210">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f498a-211">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="f498a-211">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f498a-212">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="f498a-212">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f498a-213">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f498a-213">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f498a-214">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="f498a-214">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f498a-215">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="f498a-215">Select **Yes**</span></span>

  * <span data-ttu-id="f498a-216">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f498a-216">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f498a-217">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="f498a-217">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f498a-218">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f498a-218">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f498a-219">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-219">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f498a-221">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション (Model-View-Controller)]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-221">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="f498a-223">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、 **[.NET Core 2.2]** という既定の **[ターゲット フレームワーク]** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="f498a-223">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 の選択](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="f498a-225">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f498a-225">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="f498a-226">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="f498a-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f498a-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f498a-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f498a-228">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f498a-228">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f498a-229">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f498a-230">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-230">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f498a-231">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="f498a-231">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f498a-232">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-232">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f498a-233">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="f498a-233">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f498a-234">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="f498a-234">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f498a-235">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="f498a-235">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f498a-237">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="f498a-237">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="f498a-239">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="f498a-239">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f498a-240">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="f498a-240">This app doesn't track personal information.</span></span> <span data-ttu-id="f498a-241">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f498a-241">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="f498a-243">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="f498a-243">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f498a-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f498a-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f498a-246">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="f498a-246">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f498a-247">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="f498a-247">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f498a-248">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-248">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f498a-249">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="f498a-249">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f498a-250">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-250">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f498a-251">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="f498a-251">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f498a-252">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="f498a-252">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="f498a-253">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="f498a-253">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f498a-254">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="f498a-254">This app doesn't track personal information.</span></span> <span data-ttu-id="f498a-255">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f498a-255">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="f498a-257">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="f498a-257">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f498a-259">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f498a-259">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f498a-260">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="f498a-260">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f498a-261">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="f498a-261">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f498a-262">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-262">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f498a-263">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="f498a-263">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f498a-264">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-264">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f498a-265">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f498a-265">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f498a-266">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="f498a-266">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="f498a-267">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="f498a-267">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f498a-268">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="f498a-268">This app doesn't track personal information.</span></span> <span data-ttu-id="f498a-269">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f498a-269">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="f498a-271">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="f498a-271">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f498a-273">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="f498a-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f498a-274">次へ</span><span class="sxs-lookup"><span data-stu-id="f498a-274">Next</span></span>](adding-controller.md)

::: moniker-end
