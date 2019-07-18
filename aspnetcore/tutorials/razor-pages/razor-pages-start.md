---
title: 'チュートリアル: ASP.NET Core の Razor ページの概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、および移行を使用したモデルの更新の方法について説明します。
ms.author: riande
ms.date: 06/03/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 7e228c99b4d55c14cea9c915cf06a7fbbbd5af44
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855737"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="2e159-104">チュートリアル: ASP.NET Core の Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="2e159-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="2e159-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e159-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2e159-106">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="2e159-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="2e159-107">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="2e159-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="2e159-108">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="2e159-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="2e159-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="2e159-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e159-110">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="2e159-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2e159-111">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e159-111">Run the app.</span></span>
> * <span data-ttu-id="2e159-112">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="2e159-112">Examine the project files.</span></span>

<span data-ttu-id="2e159-113">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="2e159-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2e159-115">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2e159-115">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e159-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e159-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e159-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e159-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2e159-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2e159-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="2e159-119">Razor ページ Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2e159-119">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e159-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e159-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e159-121">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2e159-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="2e159-122">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e159-122">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="2e159-124">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2e159-124">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2e159-125">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="2e159-125">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="2e159-127">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e159-127">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="2e159-129">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-129">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e159-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e159-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2e159-132">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="2e159-132">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2e159-133">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="2e159-133">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2e159-134">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e159-134">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2e159-135">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-135">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2e159-136">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="2e159-136">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2e159-137">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="2e159-137">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2e159-138">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2e159-138">Select **Yes**.</span></span>

  <span data-ttu-id="2e159-139">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-139">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2e159-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2e159-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2e159-141">端末から、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2e159-141">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="2e159-142">上記のコマンドでは、[.NET Core CLI](/dotnet/core/tools/dotnet) を使用して、Razor ページ プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-142">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="2e159-143">プロジェクトを開く</span><span class="sxs-lookup"><span data-stu-id="2e159-143">Open the project</span></span>

<span data-ttu-id="2e159-144">Visual Studio から、 **[ファイル]、[開く]** の順に選択し、*RazorPagesMovie.csproj* ファイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e159-144">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2e159-145">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="2e159-145">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2e159-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e159-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2e159-147">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="2e159-147">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2e159-148">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-148">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2e159-149">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-149">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2e159-150">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="2e159-150">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2e159-151">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-151">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2e159-152">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-152">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="2e159-153">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="2e159-153">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2e159-154">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="2e159-154">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2e159-156">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="2e159-156">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2e159-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e159-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="2e159-159">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="2e159-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2e159-160">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e159-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="2e159-161">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2e159-162">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="2e159-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2e159-163">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-163">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="2e159-164">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="2e159-164">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2e159-165">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="2e159-165">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2e159-167">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="2e159-167">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2e159-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2e159-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2e159-170">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="2e159-170">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2e159-171">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="2e159-171">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="2e159-172">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="2e159-172">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2e159-173">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="2e159-173">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="2e159-175">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="2e159-175">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="2e159-177">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="2e159-177">Examine the project files</span></span>

<span data-ttu-id="2e159-178">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="2e159-178">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2e159-179">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="2e159-179">Pages folder</span></span>

<span data-ttu-id="2e159-180">Razor ページとサポート ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-180">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2e159-181">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="2e159-181">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2e159-182">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-182">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2e159-183">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-183">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="2e159-184">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="2e159-184">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2e159-185">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-185">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2e159-186">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-186">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2e159-187">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e159-187">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2e159-188">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="2e159-188">wwwroot folder</span></span>

<span data-ttu-id="2e159-189">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-189">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2e159-190">詳細については、<xref:fundamentals/static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e159-190">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2e159-191">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="2e159-191">appSettings.json</span></span>

<span data-ttu-id="2e159-192">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-192">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="2e159-193">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e159-193">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2e159-194">Program.cs</span><span class="sxs-lookup"><span data-stu-id="2e159-194">Program.cs</span></span>

<span data-ttu-id="2e159-195">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-195">Contains the entry point for the program.</span></span> <span data-ttu-id="2e159-196">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e159-196">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2e159-197">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2e159-197">Startup.cs</span></span>

<span data-ttu-id="2e159-198">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="2e159-198">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="2e159-199">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e159-199">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e159-200">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2e159-200">Additional resources</span></span>

* [<span data-ttu-id="2e159-201">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="2e159-201">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="2e159-202">次の手順</span><span class="sxs-lookup"><span data-stu-id="2e159-202">Next steps</span></span>

<span data-ttu-id="2e159-203">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="2e159-203">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2e159-204">Razor ページ Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="2e159-204">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="2e159-205">アプリを実行する。</span><span class="sxs-lookup"><span data-stu-id="2e159-205">Ran the app.</span></span>
> * <span data-ttu-id="2e159-206">プロジェクト ファイルを確認する。</span><span class="sxs-lookup"><span data-stu-id="2e159-206">Examined the project files.</span></span>

<span data-ttu-id="2e159-207">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="2e159-207">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2e159-208">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="2e159-208">Add a model</span></span>](xref:tutorials/razor-pages/model)
