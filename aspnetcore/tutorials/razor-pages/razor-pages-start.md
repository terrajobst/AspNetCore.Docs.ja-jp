---
title: 'チュートリアル: ASP.NET Core の Razor ページの概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、および移行を使用したモデルの更新の方法について説明します。
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 57a10895c718c539ece280afcb27cb4033c7fb45
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682790"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="1f485-104">チュートリアル: ASP.NET Core の Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="1f485-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="1f485-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f485-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="1f485-106">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="1f485-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1f485-107">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="1f485-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1f485-108">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="1f485-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1f485-109">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="1f485-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1f485-110">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-110">Run the app.</span></span>
> * <span data-ttu-id="1f485-111">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="1f485-111">Examine the project files.</span></span>

<span data-ttu-id="1f485-112">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="1f485-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1f485-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1f485-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f485-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f485-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f485-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f485-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f485-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f485-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1f485-118">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="1f485-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f485-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f485-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f485-120">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1f485-121">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="1f485-122">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="1f485-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="1f485-123">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="1f485-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1f485-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="1f485-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="1f485-125">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="1f485-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="1f485-126">ドロップダウンの **[ASP.NET Core 3.0]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="1f485-128">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-128">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f485-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f485-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f485-131">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="1f485-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1f485-132">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="1f485-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1f485-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1f485-134">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1f485-135">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="1f485-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1f485-136">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="1f485-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1f485-137">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-137">Select **Yes**.</span></span>

  <span data-ttu-id="1f485-138">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f485-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f485-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f485-140">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="1f485-141">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、Razor ページ プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="1f485-142">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="1f485-142">Open the project</span></span>

<span data-ttu-id="1f485-143">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*RazorPagesMovie.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1f485-144">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="1f485-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f485-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f485-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f485-146">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1f485-147">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1f485-148">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f485-149">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="1f485-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1f485-150">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1f485-151">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f485-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f485-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1f485-153">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1f485-154">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="1f485-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1f485-155">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f485-156">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="1f485-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1f485-157">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f485-158">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f485-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1f485-159">**Alt-Cmd-Enter** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-159">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="1f485-160">または、メニュー バーに移動して [実行] > [デバッグなしで開始] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-160">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="1f485-161">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="1f485-161">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1f485-162">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="1f485-162">Examine the project files</span></span>

<span data-ttu-id="1f485-163">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1f485-163">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1f485-164">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="1f485-164">Pages folder</span></span>

<span data-ttu-id="1f485-165">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-165">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1f485-166">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="1f485-166">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1f485-167">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-167">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1f485-168">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-168">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1f485-169">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="1f485-169">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1f485-170">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-170">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1f485-171">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-171">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1f485-172">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-172">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1f485-173">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="1f485-173">wwwroot folder</span></span>

<span data-ttu-id="1f485-174">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-174">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1f485-175">詳細については、<xref:fundamentals/static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-175">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1f485-176">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1f485-176">appSettings.json</span></span>

<span data-ttu-id="1f485-177">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-177">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1f485-178">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-178">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1f485-179">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1f485-179">Program.cs</span></span>

<span data-ttu-id="1f485-180">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-180">Contains the entry point for the program.</span></span> <span data-ttu-id="1f485-181">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-181">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1f485-182">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1f485-182">Startup.cs</span></span>

<span data-ttu-id="1f485-183">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f485-183">Contains code that configures app behavior.</span></span> <span data-ttu-id="1f485-184">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-184">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1f485-185">次の手順</span><span class="sxs-lookup"><span data-stu-id="1f485-185">Next steps</span></span>

<span data-ttu-id="1f485-186">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="1f485-186">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1f485-187">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="1f485-187">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f485-188">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="1f485-188">This is the first tutorial of a series.</span></span> <span data-ttu-id="1f485-189">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="1f485-189">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1f485-190">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="1f485-190">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1f485-191">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="1f485-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1f485-192">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="1f485-192">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1f485-193">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-193">Run the app.</span></span>
> * <span data-ttu-id="1f485-194">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="1f485-194">Examine the project files.</span></span>

<span data-ttu-id="1f485-195">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="1f485-195">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1f485-197">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1f485-197">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f485-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f485-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f485-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f485-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f485-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f485-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1f485-201">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="1f485-201">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f485-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f485-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f485-203">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-203">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="1f485-204">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-204">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="1f485-206">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="1f485-206">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1f485-207">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="1f485-207">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="1f485-209">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-209">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="1f485-211">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-211">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f485-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f485-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f485-214">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="1f485-214">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1f485-215">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="1f485-215">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1f485-216">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-216">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1f485-217">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-217">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1f485-218">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="1f485-218">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1f485-219">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="1f485-219">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1f485-220">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-220">Select **Yes**.</span></span>

  <span data-ttu-id="1f485-221">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-221">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f485-222">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f485-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f485-223">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-223">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="1f485-224">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、Razor ページ プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-224">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="1f485-225">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="1f485-225">Open the project</span></span>

<span data-ttu-id="1f485-226">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*RazorPagesMovie.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="1f485-226">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1f485-227">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="1f485-227">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f485-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f485-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f485-229">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-229">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1f485-230">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1f485-231">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-231">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f485-232">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="1f485-232">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1f485-233">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-233">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1f485-234">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-234">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="1f485-235">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="1f485-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1f485-236">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="1f485-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1f485-238">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f485-238">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f485-240">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f485-240">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1f485-241">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-241">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1f485-242">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="1f485-242">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1f485-243">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-243">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f485-244">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="1f485-244">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1f485-245">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-245">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="1f485-246">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="1f485-246">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1f485-247">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="1f485-247">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1f485-249">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f485-249">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f485-251">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f485-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1f485-252">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="1f485-252">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1f485-253">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="1f485-253">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="1f485-254">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="1f485-254">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1f485-255">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="1f485-255">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="1f485-257">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f485-257">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1f485-259">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="1f485-259">Examine the project files</span></span>

<span data-ttu-id="1f485-260">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1f485-260">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1f485-261">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="1f485-261">Pages folder</span></span>

<span data-ttu-id="1f485-262">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-262">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1f485-263">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="1f485-263">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1f485-264">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-264">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1f485-265">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-265">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1f485-266">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="1f485-266">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1f485-267">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-267">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1f485-268">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-268">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1f485-269">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-269">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1f485-270">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="1f485-270">wwwroot folder</span></span>

<span data-ttu-id="1f485-271">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-271">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1f485-272">詳細については、<xref:fundamentals/static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-272">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1f485-273">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1f485-273">appSettings.json</span></span>

<span data-ttu-id="1f485-274">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-274">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1f485-275">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-275">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1f485-276">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1f485-276">Program.cs</span></span>

<span data-ttu-id="1f485-277">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-277">Contains the entry point for the program.</span></span> <span data-ttu-id="1f485-278">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-278">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1f485-279">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1f485-279">Startup.cs</span></span>

<span data-ttu-id="1f485-280">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="1f485-280">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="1f485-281">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f485-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f485-282">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1f485-282">Additional resources</span></span>

* [<span data-ttu-id="1f485-283">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="1f485-283">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="1f485-284">次の手順</span><span class="sxs-lookup"><span data-stu-id="1f485-284">Next steps</span></span>

<span data-ttu-id="1f485-285">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="1f485-285">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1f485-286">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="1f485-286">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
