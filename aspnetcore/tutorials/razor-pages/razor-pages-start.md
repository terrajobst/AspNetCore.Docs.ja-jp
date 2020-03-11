---
title: 'チュートリアル: ASP.NET Core の Razor ページの概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、および移行を使用したモデルの更新の方法について説明します。
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646904"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="3570e-104">チュートリアル: ASP.NET Core の Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="3570e-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="3570e-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3570e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="3570e-106">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="3570e-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="3570e-107">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="3570e-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="3570e-108">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="3570e-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3570e-109">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="3570e-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="3570e-110">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3570e-110">Run the app.</span></span>
> * <span data-ttu-id="3570e-111">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="3570e-111">Examine the project files.</span></span>

<span data-ttu-id="3570e-112">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="3570e-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="3570e-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3570e-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3570e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3570e-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3570e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3570e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3570e-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3570e-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="3570e-118">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="3570e-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3570e-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3570e-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3570e-120">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3570e-121">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="3570e-122">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="3570e-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="3570e-123">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3570e-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="3570e-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="3570e-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="3570e-125">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="3570e-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="3570e-126">ドロップダウンの **[ASP.NET Core 3.1]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="3570e-128">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-128">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3570e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3570e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3570e-131">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="3570e-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="3570e-132">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="3570e-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="3570e-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3570e-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="3570e-134">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="3570e-135">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3570e-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="3570e-136">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="3570e-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="3570e-137">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-137">Select **Yes**.</span></span>

  <span data-ttu-id="3570e-138">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3570e-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3570e-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3570e-140">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-140">Select **File** > **New Solution**.</span></span>

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3570e-142">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="3570e-144">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、 **[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3570e-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.1 の選択](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="3570e-146">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="3570e-148">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="3570e-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="3570e-149">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="3570e-149">Examine the project files</span></span>

<span data-ttu-id="3570e-150">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="3570e-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="3570e-151">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="3570e-151">Pages folder</span></span>

<span data-ttu-id="3570e-152">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="3570e-153">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="3570e-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="3570e-154">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="3570e-155">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="3570e-156">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="3570e-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="3570e-157">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="3570e-158">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="3570e-159">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="3570e-160">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="3570e-160">wwwroot folder</span></span>

<span data-ttu-id="3570e-161">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="3570e-162">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3570e-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="3570e-163">appSettings.json</span></span>

<span data-ttu-id="3570e-164">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="3570e-165">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="3570e-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="3570e-166">Program.cs</span></span>

<span data-ttu-id="3570e-167">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-167">Contains the entry point for the program.</span></span> <span data-ttu-id="3570e-168">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="3570e-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3570e-169">Startup.cs</span></span>

<span data-ttu-id="3570e-170">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3570e-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="3570e-171">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3570e-172">次の手順</span><span class="sxs-lookup"><span data-stu-id="3570e-172">Next steps</span></span>

<span data-ttu-id="3570e-173">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="3570e-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3570e-174">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="3570e-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3570e-175">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="3570e-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="3570e-176">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="3570e-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="3570e-177">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="3570e-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="3570e-178">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="3570e-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3570e-179">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="3570e-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="3570e-180">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3570e-180">Run the app.</span></span>
> * <span data-ttu-id="3570e-181">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="3570e-181">Examine the project files.</span></span>

<span data-ttu-id="3570e-182">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="3570e-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="3570e-184">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3570e-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3570e-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3570e-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3570e-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3570e-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3570e-187">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3570e-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="3570e-188">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="3570e-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3570e-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3570e-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3570e-190">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="3570e-191">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="3570e-193">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3570e-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="3570e-194">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="3570e-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="3570e-196">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="3570e-198">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-198">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3570e-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3570e-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3570e-201">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="3570e-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="3570e-202">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="3570e-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="3570e-203">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3570e-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="3570e-204">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="3570e-205">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3570e-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="3570e-206">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="3570e-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="3570e-207">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-207">Select **Yes**.</span></span>

  <span data-ttu-id="3570e-208">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3570e-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3570e-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3570e-210">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-210">Select **File** > **New Solution**.</span></span>

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3570e-212">**[.NET Core]** > **[アプリ]** > **[Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="3570e-214">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、 **[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="3570e-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.0 の選択](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="3570e-216">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3570e-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="3570e-218">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="3570e-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3570e-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3570e-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3570e-220">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="3570e-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="3570e-221">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3570e-222">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3570e-223">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="3570e-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="3570e-224">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="3570e-225">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="3570e-226">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="3570e-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3570e-227">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="3570e-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="3570e-229">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="3570e-229">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="3570e-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3570e-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="3570e-232">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="3570e-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="3570e-233">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="3570e-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="3570e-234">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3570e-235">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="3570e-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3570e-236">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="3570e-237">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="3570e-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3570e-238">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="3570e-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="3570e-240">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="3570e-240">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3570e-242">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3570e-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3570e-243">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="3570e-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="3570e-244">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="3570e-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="3570e-245">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="3570e-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3570e-246">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="3570e-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="3570e-248">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="3570e-248">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="3570e-250">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="3570e-250">Examine the project files</span></span>

<span data-ttu-id="3570e-251">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="3570e-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="3570e-252">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="3570e-252">Pages folder</span></span>

<span data-ttu-id="3570e-253">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="3570e-254">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="3570e-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="3570e-255">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="3570e-256">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="3570e-257">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="3570e-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="3570e-258">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="3570e-259">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="3570e-260">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="3570e-261">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="3570e-261">wwwroot folder</span></span>

<span data-ttu-id="3570e-262">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="3570e-263">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3570e-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="3570e-264">appSettings.json</span></span>

<span data-ttu-id="3570e-265">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="3570e-266">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="3570e-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="3570e-267">Program.cs</span></span>

<span data-ttu-id="3570e-268">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-268">Contains the entry point for the program.</span></span> <span data-ttu-id="3570e-269">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="3570e-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3570e-270">Startup.cs</span></span>

<span data-ttu-id="3570e-271">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="3570e-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="3570e-272">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3570e-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3570e-273">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3570e-273">Additional resources</span></span>

* [<span data-ttu-id="3570e-274">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="3570e-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="3570e-275">次の手順</span><span class="sxs-lookup"><span data-stu-id="3570e-275">Next steps</span></span>

<span data-ttu-id="3570e-276">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="3570e-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3570e-277">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="3570e-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
