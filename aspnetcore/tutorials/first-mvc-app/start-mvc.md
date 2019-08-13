---
title: ASP.NET Core MVC の概要
author: rick-anderson
description: ASP.NET Core MVC の概要について説明します。
ms.author: riande
ms.date: 08/05/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: f6a92423546ebd9d4c8e1a92fb81b6b72f847f61
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820090"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="236dc-103">ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="236dc-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="236dc-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="236dc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="236dc-105">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="236dc-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="236dc-106">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="236dc-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="236dc-107">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="236dc-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="236dc-108">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="236dc-108">Create a web app.</span></span>
> * <span data-ttu-id="236dc-109">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="236dc-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="236dc-110">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="236dc-110">Work with a database.</span></span>
> * <span data-ttu-id="236dc-111">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="236dc-111">Add search and validation.</span></span>

<span data-ttu-id="236dc-112">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="236dc-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="236dc-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="236dc-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="236dc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="236dc-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="236dc-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="236dc-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="236dc-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="236dc-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="236dc-117">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="236dc-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="236dc-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="236dc-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="236dc-119">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="236dc-120">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="236dc-122">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="236dc-123">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="236dc-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)

* <span data-ttu-id="236dc-125">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="236dc-126">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="236dc-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="236dc-127">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="236dc-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="236dc-128">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="236dc-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="236dc-129">これは基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="236dc-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="236dc-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="236dc-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="236dc-131">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="236dc-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="236dc-132">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="236dc-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="236dc-133">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="236dc-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="236dc-134">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="236dc-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="236dc-135">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="236dc-135">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="236dc-136">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="236dc-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="236dc-137">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="236dc-137">Select **Yes**</span></span>

  * <span data-ttu-id="236dc-138">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="236dc-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="236dc-139">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="236dc-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="236dc-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="236dc-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="236dc-141">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-141">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="236dc-143">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション (Model-View-Controller)]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="236dc-145">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* **[.NET Core 3.0]** の **[ターゲット フレームワーク]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="236dc-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.0**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="236dc-146">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="236dc-147">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="236dc-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="236dc-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="236dc-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="236dc-149">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="236dc-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="236dc-150">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="236dc-151">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="236dc-152">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="236dc-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="236dc-153">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="236dc-154">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="236dc-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="236dc-155">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="236dc-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="236dc-156">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="236dc-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="236dc-158">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="236dc-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)


  <span data-ttu-id="236dc-160">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="236dc-160">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="236dc-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="236dc-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="236dc-163">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="236dc-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="236dc-164">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="236dc-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="236dc-165">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="236dc-166">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="236dc-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="236dc-167">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="236dc-168">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="236dc-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="236dc-169">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="236dc-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="236dc-170">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="236dc-170">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="236dc-171">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="236dc-171">This app doesn't track personal information.</span></span> <span data-ttu-id="236dc-172">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="236dc-172">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="236dc-174">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="236dc-174">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="236dc-176">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="236dc-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="236dc-177">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="236dc-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="236dc-178">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="236dc-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="236dc-179">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="236dc-180">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="236dc-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="236dc-181">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="236dc-182">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="236dc-183">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="236dc-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="236dc-184">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="236dc-184">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="236dc-186">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="236dc-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="236dc-187">次へ</span><span class="sxs-lookup"><span data-stu-id="236dc-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="236dc-188">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="236dc-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="236dc-189">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="236dc-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="236dc-190">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="236dc-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="236dc-191">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="236dc-191">Create a web app.</span></span>
> * <span data-ttu-id="236dc-192">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="236dc-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="236dc-193">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="236dc-193">Work with a database.</span></span>
> * <span data-ttu-id="236dc-194">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="236dc-194">Add search and validation.</span></span>

<span data-ttu-id="236dc-195">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="236dc-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="236dc-196">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="236dc-196">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="236dc-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="236dc-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="236dc-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="236dc-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="236dc-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="236dc-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="236dc-200">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="236dc-200">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="236dc-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="236dc-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="236dc-202">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="236dc-203">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-203">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="236dc-205">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="236dc-206">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="236dc-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)


* <span data-ttu-id="236dc-208">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="236dc-209">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="236dc-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="236dc-210">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="236dc-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="236dc-211">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="236dc-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="236dc-212">これは基本的なスターター プロジェクトなので、ここから始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="236dc-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="236dc-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="236dc-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="236dc-214">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="236dc-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="236dc-215">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="236dc-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="236dc-216">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="236dc-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="236dc-217">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="236dc-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="236dc-218">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="236dc-218">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="236dc-219">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="236dc-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="236dc-220">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="236dc-220">Select **Yes**</span></span>

  * <span data-ttu-id="236dc-221">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="236dc-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="236dc-222">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="236dc-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="236dc-223">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="236dc-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="236dc-224">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-224">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="236dc-226">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション (Model-View-Controller)]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-226">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="236dc-228">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、 **[.NET Core 2.2]** という既定の **[ターゲット フレームワーク]** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="236dc-228">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 の選択](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="236dc-230">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="236dc-230">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="236dc-231">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="236dc-231">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="236dc-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="236dc-232">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="236dc-233">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="236dc-233">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="236dc-234">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-234">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="236dc-235">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-235">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="236dc-236">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="236dc-236">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="236dc-237">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-237">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="236dc-238">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="236dc-238">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="236dc-239">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="236dc-239">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="236dc-240">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="236dc-240">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="236dc-242">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="236dc-242">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="236dc-244">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="236dc-244">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="236dc-245">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="236dc-245">This app doesn't track personal information.</span></span> <span data-ttu-id="236dc-246">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="236dc-246">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="236dc-248">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="236dc-248">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="236dc-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="236dc-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="236dc-251">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="236dc-251">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="236dc-252">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="236dc-252">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="236dc-253">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-253">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="236dc-254">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="236dc-254">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="236dc-255">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-255">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="236dc-256">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="236dc-256">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="236dc-257">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="236dc-257">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="236dc-258">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="236dc-258">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="236dc-259">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="236dc-259">This app doesn't track personal information.</span></span> <span data-ttu-id="236dc-260">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="236dc-260">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="236dc-262">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="236dc-262">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="236dc-264">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="236dc-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="236dc-265">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="236dc-265">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="236dc-266">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="236dc-266">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="236dc-267">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-267">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="236dc-268">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="236dc-268">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="236dc-269">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-269">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="236dc-270">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="236dc-270">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="236dc-271">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="236dc-271">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="236dc-272">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="236dc-272">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="236dc-273">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="236dc-273">This app doesn't track personal information.</span></span> <span data-ttu-id="236dc-274">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="236dc-274">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="236dc-276">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="236dc-276">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="236dc-278">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="236dc-278">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="236dc-279">次へ</span><span class="sxs-lookup"><span data-stu-id="236dc-279">Next</span></span>](adding-controller.md)

::: moniker-end
