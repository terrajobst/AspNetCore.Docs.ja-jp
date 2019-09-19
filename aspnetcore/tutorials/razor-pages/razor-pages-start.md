---
title: 'チュートリアル: ASP.NET Core の Razor ページの概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、および移行を使用したモデルの更新の方法について説明します。
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 0cc00cb85b6054752417b82c783cfd4c306aeda5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082578"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="5cc8b-104">チュートリアル: ASP.NET Core の Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="5cc8b-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="5cc8b-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5cc8b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="5cc8b-106">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="5cc8b-107">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="5cc8b-108">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5cc8b-109">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="5cc8b-110">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-110">Run the app.</span></span>
> * <span data-ttu-id="5cc8b-111">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="5cc8b-111">Examine the project files.</span></span>

<span data-ttu-id="5cc8b-112">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="5cc8b-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5cc8b-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cc8b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cc8b-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cc8b-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cc8b-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5cc8b-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5cc8b-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="5cc8b-118">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="5cc8b-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cc8b-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cc8b-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5cc8b-120">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5cc8b-121">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="5cc8b-122">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="5cc8b-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="5cc8b-123">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="5cc8b-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="5cc8b-125">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="5cc8b-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="5cc8b-126">ドロップダウンの **[ASP.NET Core 3.0]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="5cc8b-128">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-128">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cc8b-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cc8b-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5cc8b-131">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="5cc8b-132">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="5cc8b-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="5cc8b-134">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="5cc8b-135">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="5cc8b-136">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="5cc8b-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="5cc8b-137">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-137">Select **Yes**.</span></span>

  <span data-ttu-id="5cc8b-138">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5cc8b-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5cc8b-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5cc8b-140">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-140">Select **File** > **New Solution**.</span></span>

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="5cc8b-142">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="5cc8b-144">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、 **[ターゲット フレームワーク]** を **[.NET Core 3.0]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![macOS .NET Core 3.0 の選択](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="5cc8b-146">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="5cc8b-148">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="5cc8b-148">Open the project</span></span>

<span data-ttu-id="5cc8b-149">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*RazorPagesMovie.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="5cc8b-150">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="5cc8b-150">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cc8b-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cc8b-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5cc8b-152">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-152">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="5cc8b-153">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-153">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5cc8b-154">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-154">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5cc8b-155">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-155">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="5cc8b-156">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-156">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="5cc8b-157">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-157">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cc8b-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cc8b-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="5cc8b-159">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="5cc8b-160">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="5cc8b-161">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5cc8b-162">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5cc8b-163">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-163">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5cc8b-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5cc8b-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5cc8b-165">**Alt-Cmd-Enter** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-165">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="5cc8b-166">または、メニュー バーに移動して [実行] > [デバッグなしで開始] を選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-166">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="5cc8b-167">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="5cc8b-168">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="5cc8b-168">Examine the project files</span></span>

<span data-ttu-id="5cc8b-169">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-169">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="5cc8b-170">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="5cc8b-170">Pages folder</span></span>

<span data-ttu-id="5cc8b-171">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-171">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="5cc8b-172">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-172">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="5cc8b-173">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-173">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="5cc8b-174">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-174">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="5cc8b-175">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-175">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="5cc8b-176">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-176">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="5cc8b-177">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-177">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="5cc8b-178">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-178">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="5cc8b-179">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="5cc8b-179">wwwroot folder</span></span>

<span data-ttu-id="5cc8b-180">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-180">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="5cc8b-181">詳細については、<xref:fundamentals/static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-181">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="5cc8b-182">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="5cc8b-182">appSettings.json</span></span>

<span data-ttu-id="5cc8b-183">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-183">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="5cc8b-184">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="5cc8b-185">Program.cs</span><span class="sxs-lookup"><span data-stu-id="5cc8b-185">Program.cs</span></span>

<span data-ttu-id="5cc8b-186">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-186">Contains the entry point for the program.</span></span> <span data-ttu-id="5cc8b-187">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-187">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="5cc8b-188">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="5cc8b-188">Startup.cs</span></span>

<span data-ttu-id="5cc8b-189">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-189">Contains code that configures app behavior.</span></span> <span data-ttu-id="5cc8b-190">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-190">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5cc8b-191">次の手順</span><span class="sxs-lookup"><span data-stu-id="5cc8b-191">Next steps</span></span>

<span data-ttu-id="5cc8b-192">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-192">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5cc8b-193">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="5cc8b-193">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5cc8b-194">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-194">This is the first tutorial of a series.</span></span> <span data-ttu-id="5cc8b-195">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-195">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="5cc8b-196">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-196">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="5cc8b-197">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5cc8b-198">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-198">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="5cc8b-199">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-199">Run the app.</span></span>
> * <span data-ttu-id="5cc8b-200">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="5cc8b-200">Examine the project files.</span></span>

<span data-ttu-id="5cc8b-201">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-201">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="5cc8b-203">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5cc8b-203">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cc8b-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cc8b-204">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cc8b-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cc8b-205">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5cc8b-206">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5cc8b-206">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="5cc8b-207">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="5cc8b-207">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cc8b-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cc8b-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5cc8b-209">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-209">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="5cc8b-210">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-210">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="5cc8b-212">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-212">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="5cc8b-213">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-213">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="5cc8b-215">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-215">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="5cc8b-217">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-217">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cc8b-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cc8b-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5cc8b-220">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="5cc8b-221">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-221">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="5cc8b-222">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-222">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="5cc8b-223">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-223">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="5cc8b-224">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-224">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="5cc8b-225">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="5cc8b-225">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="5cc8b-226">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-226">Select **Yes**.</span></span>

  <span data-ttu-id="5cc8b-227">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-227">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5cc8b-228">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5cc8b-228">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5cc8b-229">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-229">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="5cc8b-230">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、Razor ページ プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-230">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="5cc8b-231">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="5cc8b-231">Open the project</span></span>

<span data-ttu-id="5cc8b-232">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*RazorPagesMovie.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-232">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="5cc8b-233">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="5cc8b-233">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cc8b-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cc8b-234">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5cc8b-235">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-235">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="5cc8b-236">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5cc8b-237">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-237">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5cc8b-238">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-238">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="5cc8b-239">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-239">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="5cc8b-240">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="5cc8b-241">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="5cc8b-242">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="5cc8b-244">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-244">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cc8b-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cc8b-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="5cc8b-247">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-247">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="5cc8b-248">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="5cc8b-249">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-249">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5cc8b-250">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5cc8b-251">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-251">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="5cc8b-252">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-252">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="5cc8b-253">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-253">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="5cc8b-255">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-255">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5cc8b-257">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5cc8b-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="5cc8b-258">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-258">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="5cc8b-259">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-259">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="5cc8b-260">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-260">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="5cc8b-261">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-261">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="5cc8b-263">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-263">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="5cc8b-265">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="5cc8b-265">Examine the project files</span></span>

<span data-ttu-id="5cc8b-266">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-266">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="5cc8b-267">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="5cc8b-267">Pages folder</span></span>

<span data-ttu-id="5cc8b-268">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-268">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="5cc8b-269">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-269">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="5cc8b-270">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-270">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="5cc8b-271">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-271">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="5cc8b-272">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-272">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="5cc8b-273">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-273">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="5cc8b-274">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-274">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="5cc8b-275">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-275">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="5cc8b-276">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="5cc8b-276">wwwroot folder</span></span>

<span data-ttu-id="5cc8b-277">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-277">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="5cc8b-278">詳細については、<xref:fundamentals/static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-278">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="5cc8b-279">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="5cc8b-279">appSettings.json</span></span>

<span data-ttu-id="5cc8b-280">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-280">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="5cc8b-281">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-281">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="5cc8b-282">Program.cs</span><span class="sxs-lookup"><span data-stu-id="5cc8b-282">Program.cs</span></span>

<span data-ttu-id="5cc8b-283">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-283">Contains the entry point for the program.</span></span> <span data-ttu-id="5cc8b-284">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-284">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="5cc8b-285">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="5cc8b-285">Startup.cs</span></span>

<span data-ttu-id="5cc8b-286">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-286">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="5cc8b-287">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-287">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5cc8b-288">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5cc8b-288">Additional resources</span></span>

* [<span data-ttu-id="5cc8b-289">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="5cc8b-289">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="5cc8b-290">次の手順</span><span class="sxs-lookup"><span data-stu-id="5cc8b-290">Next steps</span></span>

<span data-ttu-id="5cc8b-291">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="5cc8b-291">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5cc8b-292">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="5cc8b-292">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
