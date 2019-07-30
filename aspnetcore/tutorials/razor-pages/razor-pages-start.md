---
title: 'チュートリアル: ASP.NET Core の Razor ページの概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、および移行を使用したモデルの更新の方法について説明します。
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1605197188d97f27a884739a72400da2d5818b1a
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372005"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="4a8fb-104">チュートリアル: ASP.NET Core の Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="4a8fb-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="4a8fb-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4a8fb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="4a8fb-106">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="4a8fb-107">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="4a8fb-108">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4a8fb-109">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4a8fb-110">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-110">Run the app.</span></span>
> * <span data-ttu-id="4a8fb-111">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="4a8fb-111">Examine the project files.</span></span>

<span data-ttu-id="4a8fb-112">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="4a8fb-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4a8fb-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a8fb-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a8fb-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4a8fb-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a8fb-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4a8fb-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4a8fb-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="4a8fb-118">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="4a8fb-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a8fb-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a8fb-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a8fb-120">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4a8fb-121">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="4a8fb-122">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="4a8fb-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="4a8fb-123">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="4a8fb-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="4a8fb-125">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="4a8fb-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="4a8fb-126">ドロップダウンの **[ASP.NET Core 3.0]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="4a8fb-128">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-128">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4a8fb-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a8fb-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4a8fb-131">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4a8fb-132">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4a8fb-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="4a8fb-134">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="4a8fb-135">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4a8fb-136">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="4a8fb-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="4a8fb-137">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-137">Select **Yes**.</span></span>

  <span data-ttu-id="4a8fb-138">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4a8fb-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4a8fb-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4a8fb-140">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="4a8fb-141">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、Razor ページ プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="4a8fb-142">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="4a8fb-142">Open the project</span></span>

<span data-ttu-id="4a8fb-143">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*RazorPagesMovie.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4a8fb-144">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="4a8fb-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a8fb-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a8fb-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a8fb-146">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="4a8fb-147">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4a8fb-148">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4a8fb-149">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="4a8fb-150">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="4a8fb-151">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4a8fb-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a8fb-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="4a8fb-153">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4a8fb-154">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="4a8fb-155">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4a8fb-156">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4a8fb-157">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4a8fb-158">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4a8fb-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4a8fb-159">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-159">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4a8fb-160">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-160">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="4a8fb-161">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="4a8fb-161">Examine the project files</span></span>

<span data-ttu-id="4a8fb-162">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-162">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4a8fb-163">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="4a8fb-163">Pages folder</span></span>

<span data-ttu-id="4a8fb-164">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-164">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4a8fb-165">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-165">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4a8fb-166">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-166">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4a8fb-167">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-167">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="4a8fb-168">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-168">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4a8fb-169">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-169">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4a8fb-170">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-170">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4a8fb-171">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-171">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4a8fb-172">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="4a8fb-172">wwwroot folder</span></span>

<span data-ttu-id="4a8fb-173">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-173">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4a8fb-174">詳細については、<xref:fundamentals/static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-174">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4a8fb-175">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4a8fb-175">appSettings.json</span></span>

<span data-ttu-id="4a8fb-176">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-176">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="4a8fb-177">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-177">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4a8fb-178">Program.cs</span><span class="sxs-lookup"><span data-stu-id="4a8fb-178">Program.cs</span></span>

<span data-ttu-id="4a8fb-179">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-179">Contains the entry point for the program.</span></span> <span data-ttu-id="4a8fb-180">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-180">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4a8fb-181">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4a8fb-181">Startup.cs</span></span>

<span data-ttu-id="4a8fb-182">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-182">Contains code that configures app behavior.</span></span> <span data-ttu-id="4a8fb-183">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-183">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4a8fb-184">次の手順</span><span class="sxs-lookup"><span data-stu-id="4a8fb-184">Next steps</span></span>

<span data-ttu-id="4a8fb-185">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-185">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4a8fb-186">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="4a8fb-186">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4a8fb-187">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-187">This is the first tutorial of a series.</span></span> <span data-ttu-id="4a8fb-188">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-188">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="4a8fb-189">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-189">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="4a8fb-190">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-190">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4a8fb-191">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-191">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4a8fb-192">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-192">Run the app.</span></span>
> * <span data-ttu-id="4a8fb-193">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="4a8fb-193">Examine the project files.</span></span>

<span data-ttu-id="4a8fb-194">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-194">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="4a8fb-196">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4a8fb-196">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a8fb-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a8fb-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4a8fb-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a8fb-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4a8fb-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4a8fb-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="4a8fb-200">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="4a8fb-200">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a8fb-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a8fb-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a8fb-202">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** 、> **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-202">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="4a8fb-203">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-203">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="4a8fb-205">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-205">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="4a8fb-206">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-206">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="4a8fb-208">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-208">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="4a8fb-210">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-210">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4a8fb-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a8fb-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4a8fb-213">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-213">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4a8fb-214">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-214">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4a8fb-215">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-215">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="4a8fb-216">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-216">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="4a8fb-217">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-217">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4a8fb-218">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="4a8fb-218">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="4a8fb-219">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-219">Select **Yes**.</span></span>

  <span data-ttu-id="4a8fb-220">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-220">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4a8fb-221">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4a8fb-221">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4a8fb-222">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-222">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="4a8fb-223">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、Razor ページ プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-223">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="4a8fb-224">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="4a8fb-224">Open the project</span></span>

<span data-ttu-id="4a8fb-225">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*RazorPagesMovie.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-225">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4a8fb-226">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="4a8fb-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a8fb-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a8fb-227">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4a8fb-228">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-228">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="4a8fb-229">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4a8fb-230">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-230">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4a8fb-231">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-231">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="4a8fb-232">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-232">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="4a8fb-233">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="4a8fb-234">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-234">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4a8fb-235">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-235">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4a8fb-237">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-237">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4a8fb-239">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4a8fb-239">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="4a8fb-240">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-240">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4a8fb-241">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-241">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="4a8fb-242">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-242">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4a8fb-243">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-243">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4a8fb-244">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-244">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="4a8fb-245">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4a8fb-246">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4a8fb-248">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-248">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4a8fb-250">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4a8fb-250">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4a8fb-251">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-251">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4a8fb-252">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-252">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="4a8fb-253">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-253">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4a8fb-254">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-254">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="4a8fb-256">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-256">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="4a8fb-258">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="4a8fb-258">Examine the project files</span></span>

<span data-ttu-id="4a8fb-259">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-259">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4a8fb-260">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="4a8fb-260">Pages folder</span></span>

<span data-ttu-id="4a8fb-261">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-261">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4a8fb-262">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-262">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4a8fb-263">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-263">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4a8fb-264">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-264">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="4a8fb-265">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-265">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4a8fb-266">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-266">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4a8fb-267">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-267">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4a8fb-268">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-268">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4a8fb-269">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="4a8fb-269">wwwroot folder</span></span>

<span data-ttu-id="4a8fb-270">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-270">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4a8fb-271">詳細については、<xref:fundamentals/static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-271">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4a8fb-272">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4a8fb-272">appSettings.json</span></span>

<span data-ttu-id="4a8fb-273">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-273">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="4a8fb-274">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-274">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4a8fb-275">Program.cs</span><span class="sxs-lookup"><span data-stu-id="4a8fb-275">Program.cs</span></span>

<span data-ttu-id="4a8fb-276">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-276">Contains the entry point for the program.</span></span> <span data-ttu-id="4a8fb-277">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-277">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4a8fb-278">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4a8fb-278">Startup.cs</span></span>

<span data-ttu-id="4a8fb-279">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-279">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="4a8fb-280">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-280">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4a8fb-281">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4a8fb-281">Additional resources</span></span>

* [<span data-ttu-id="4a8fb-282">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="4a8fb-282">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="4a8fb-283">次の手順</span><span class="sxs-lookup"><span data-stu-id="4a8fb-283">Next steps</span></span>

<span data-ttu-id="4a8fb-284">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="4a8fb-284">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4a8fb-285">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="4a8fb-285">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end