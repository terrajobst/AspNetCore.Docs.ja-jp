---
title: ASP.NET Core のクラス ライブラリの再利用可能 Razor UI
author: Rick-Anderson
description: Razor を ASP.NET core クラス ライブラリの部分ビューを使用して再利用可能な UI を作成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/22/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 5b83cb44302a5900ec7b2ccc049790b4c1ca57e5
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985383"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="eb379-103">ASP.NET Core の Razor クラスライブラリプロジェクトを使用して、再利用可能な UI を作成する</span><span class="sxs-lookup"><span data-stu-id="eb379-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="eb379-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eb379-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="eb379-105">Razor ビュー、ページ、コントローラー、ページモデル、 [razor コンポーネント](xref:blazor/class-libraries)、[ビューコンポーネント](xref:mvc/views/view-components)、およびデータモデルは、razor クラスライブラリ (rcl) に組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="eb379-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="eb379-106">RCL はパッケージ化し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="eb379-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="eb379-107">アプリケーションには RCL を含めることができます。また、それに含まれるビューやページをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="eb379-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="eb379-108">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="eb379-109">この機能が必要です。 [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="eb379-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="eb379-110">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="eb379-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="eb379-111">Razor UI を含むクラス ライブラリを作成する</span><span class="sxs-lookup"><span data-stu-id="eb379-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eb379-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb379-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eb379-113">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="eb379-114">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="eb379-115">ライブラリに名前を付け ("RazorClassLib" など)、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="eb379-116">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="eb379-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="eb379-117">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="eb379-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="eb379-118">**[Razor クラス ライブラリ]** 、 **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="eb379-119">RCL には、次のプロジェクトファイルがあります。</span><span class="sxs-lookup"><span data-stu-id="eb379-119">An RCL has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eb379-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="eb379-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eb379-121">コマンド ラインから `dotnet new razorclasslib` を実行します。</span><span class="sxs-lookup"><span data-stu-id="eb379-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="eb379-122">例:</span><span class="sxs-lookup"><span data-stu-id="eb379-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="eb379-123">詳細については、「[dotnet new](/dotnet/core/tools/dotnet-new)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb379-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="eb379-124">生成されたビュー ライブラリとファイル名の競合を避けるため、ライブラリ名の末尾が `.Views` ではないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="eb379-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="eb379-125">Razor ファイルを RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="eb379-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="eb379-126">ASP.NET Core テンプレートは、RCL コンテンツが前提としています。、*領域*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="eb379-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="eb379-127">「 [Rcl ページのレイアウト](#afs)」を参照して、では`~/Pages`なく`~/Areas/Pages`、でコンテンツを公開する rcl を作成します。</span><span class="sxs-lookup"><span data-stu-id="eb379-127">See [RCL Pages layout](#afs) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-rcl-content"></a><span data-ttu-id="eb379-128">RCL コンテンツの参照</span><span class="sxs-lookup"><span data-stu-id="eb379-128">Referencing RCL content</span></span>

<span data-ttu-id="eb379-129">RCL は次によって参照できます。</span><span class="sxs-lookup"><span data-stu-id="eb379-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="eb379-130">NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="eb379-130">NuGet package.</span></span> <span data-ttu-id="eb379-131">「[Creating NuGet packages](/nuget/create-packages/creating-a-package)」 (NuGet パッケージの作成)、「[dotnet add package](/dotnet/core/tools/dotnet-add-package)」、「[Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)」 (NuGet パッケージの作成と公開) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb379-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="eb379-132">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="eb379-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="eb379-133">「[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eb379-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="eb379-134">チュートリアル: RCL プロジェクトを作成し、Razor Pages プロジェクトから使用する</span><span class="sxs-lookup"><span data-stu-id="eb379-134">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="eb379-135">作成しなくても、[完全なプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)をダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="eb379-135">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="eb379-136">サンプル ダウンロードには、プロジェクトのテストを簡単にする追加のコードやリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="eb379-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="eb379-137">GitHub の問題に関するフィードバックは[こちら](https://github.com/aspnet/AspNetCore.Docs/issues/6098)で扱っています。ダウンロード サンプルと段階的指示の違いについてコメントを投稿できます。</span><span class="sxs-lookup"><span data-stu-id="eb379-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="eb379-138">ダウンロード アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="eb379-138">Test the download app</span></span>

<span data-ttu-id="eb379-139">完全なアプリをダウンロードしておらず、チュートリアル プロジェクトを作成する場合、[次のセクション](#create-an-rcl)に進んでください。</span><span class="sxs-lookup"><span data-stu-id="eb379-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eb379-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb379-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb379-141">Visual Studio で *.sln* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="eb379-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="eb379-142">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="eb379-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eb379-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="eb379-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eb379-144">*cli* ディレクトリのコマンド プロンプトから、RCL と Web アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="eb379-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="eb379-145">*WebApp1* ディレクトリに移動し、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="eb379-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="eb379-146">[テスト WebApp1](#test) の指示に従ってください。</span><span class="sxs-lookup"><span data-stu-id="eb379-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="eb379-147">RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="eb379-147">Create an RCL</span></span>

<span data-ttu-id="eb379-148">このセクションでは、RCL を作成します。</span><span class="sxs-lookup"><span data-stu-id="eb379-148">In this section, an RCL is created.</span></span> <span data-ttu-id="eb379-149">Razor ファイルが RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eb379-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb379-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb379-151">RCL プロジェクトの作成:</span><span class="sxs-lookup"><span data-stu-id="eb379-151">Create the RCL project:</span></span>

* <span data-ttu-id="eb379-152">Visual Studio の **[ファイル]** メニューから、 **[新規作成]**  >  **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="eb379-153">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="eb379-154">アプリの名前を付けます**RazorUIClassLib** > **OK**します。</span><span class="sxs-lookup"><span data-stu-id="eb379-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="eb379-155">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="eb379-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="eb379-156">**[Razor クラス ライブラリ]** 、 **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="eb379-157">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* という名前が付いた Razor 部分ビュー ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="eb379-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eb379-158">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="eb379-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eb379-159">コマンド ラインから次を実行します。</span><span class="sxs-lookup"><span data-stu-id="eb379-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="eb379-160">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="eb379-160">The preceding commands:</span></span>

* <span data-ttu-id="eb379-161">Rcl `RazorUIClassLib`を作成します。</span><span class="sxs-lookup"><span data-stu-id="eb379-161">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="eb379-162">Razor _Message ページが作成され、RCL に追加されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="eb379-163">`-np` パラメーターによって、`PageModel` なしでページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="eb379-164">作成、 [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)ファイルを開き、RCL に追加します。</span><span class="sxs-lookup"><span data-stu-id="eb379-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="eb379-165">*_ViewStart.cshtml* (これは、次のセクションに追加されます) Razor ページ プロジェクトのレイアウトを使用するファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="eb379-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="eb379-166">Razor ファイルおよびフォルダーをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="eb379-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="eb379-167">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="eb379-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="eb379-168">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* のマークアップを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="eb379-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="eb379-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` は部分ビュー (`<partial name="_Message" />`) を使用するために必要です。</span><span class="sxs-lookup"><span data-stu-id="eb379-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="eb379-170">`@addTagHelper` ディレクティブを含める代わりに、 *_ViewImports.cshtml* ファイルを追加できます。</span><span class="sxs-lookup"><span data-stu-id="eb379-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="eb379-171">例:</span><span class="sxs-lookup"><span data-stu-id="eb379-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="eb379-172">詳細については *_ViewImports.cshtml*を参照してください[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="eb379-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="eb379-173">クラス ライブラリをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="eb379-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="eb379-174">ビルド出力には、*RazorUIClassLib.dll* と *RazorUIClassLib.Views.dll* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="eb379-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="eb379-175">*RazorUIClassLib.Views.dll* には、コンパイル済みの Razor コンテンツが含まれています。</span><span class="sxs-lookup"><span data-stu-id="eb379-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="eb379-176">Razor ページ プロジェクトから Razor UI ライブラリを使用します。</span><span class="sxs-lookup"><span data-stu-id="eb379-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eb379-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb379-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb379-178">Razor ページ Web アプリの作成:</span><span class="sxs-lookup"><span data-stu-id="eb379-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="eb379-179">**ソリューション エクスプローラー**で、ソリューションを右クリックし、 **[追加]** 、 **[新しいプロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="eb379-180">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="eb379-181">アプリに **WebApp1** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="eb379-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="eb379-182">**ASP.NET Core 2.1** 以降が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="eb379-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="eb379-183">**[Web アプリケーション]** 、 **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="eb379-184">**ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="eb379-185">**ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[ビルド依存関係]** 、 **[プロジェクトの依存関係]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="eb379-186">**WebApp1** の依存関係として **RazorUIClassLib** を選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="eb379-187">**ソリューション エクスプローラー**で、**WebApp1** を右クリックし、 **[追加]** 、 **[参照]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="eb379-188">**[参照マネージャー]** ダイアログで、 **[RazorUIClassLib]** 、 **[OK]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="eb379-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="eb379-189">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="eb379-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eb379-190">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="eb379-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eb379-191">Razor Pages web アプリと、Razor Pages アプリと RCL を含むソリューションファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="eb379-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="eb379-192">Web アプリをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="eb379-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="eb379-193">テスト WebApp1</span><span class="sxs-lookup"><span data-stu-id="eb379-193">Test WebApp1</span></span>

<span data-ttu-id="eb379-194">Razor UI クラスライブラリが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="eb379-194">Verify the Razor UI class library is in use:</span></span>

* <span data-ttu-id="eb379-195">`/MyFeature/Page1` を参照します。</span><span class="sxs-lookup"><span data-stu-id="eb379-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="eb379-196">ビュー、部分ビュー、ページのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="eb379-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="eb379-197">Web アプリと RCL の両方にビュー、部分ビュー、Razor ページがあるとき、Web アプリの Razor マークアップ ( *.cshtml* ファイル) が優先されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-197">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="eb379-198">たとえば、 *WebApp1/Areas/MyFeature/Pages/Page1. cshtml*を WebApp1 に追加すると、WebApp1 の PAGE1 が Rcl の page1 よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="eb379-199">サンプル ダウンロードの *WebApp1/Areas/MyFeature2* の名前を *WebApp1/Areas/MyFeature* に変更し、優先設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="eb379-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="eb379-200">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 部分ビューを *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* ビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="eb379-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="eb379-201">新しい場所を示すようにマークアップを更新します。</span><span class="sxs-lookup"><span data-stu-id="eb379-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="eb379-202">アプリをビルドして実行し、アプリの部分ビューが使用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="eb379-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="eb379-203">RCL ページ レイアウト</span><span class="sxs-lookup"><span data-stu-id="eb379-203">RCL Pages layout</span></span>

<span data-ttu-id="eb379-204">参照 RCL コンテンツの web アプリの一部である場合と同様に*ページ*フォルダー、RCL プロジェクト ファイルの構造を作成します。</span><span class="sxs-lookup"><span data-stu-id="eb379-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="eb379-205">*RazorUIClassLib/ページ*</span><span class="sxs-lookup"><span data-stu-id="eb379-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="eb379-206">*RazorUIClassLib/ページ/共有*</span><span class="sxs-lookup"><span data-stu-id="eb379-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="eb379-207">たとえば*RazorUIClassLib/ページ/共有*2 つの部分的なファイルが含まれています: *_Header.cshtml*と *_Footer.cshtml*します。</span><span class="sxs-lookup"><span data-stu-id="eb379-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="eb379-208">`<partial>`タグに追加できる *_Layout.cshtml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="eb379-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="eb379-209">静的なアセットを含む RCL を作成する</span><span class="sxs-lookup"><span data-stu-id="eb379-209">Create an RCL with static assets</span></span>

<span data-ttu-id="eb379-210">RCL では、RCL の消費アプリから参照できる、関連する静的アセットが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="eb379-210">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="eb379-211">ASP.NET Core では、使用中のアプリで利用可能な静的アセットを含む RCLs を作成できます。</span><span class="sxs-lookup"><span data-stu-id="eb379-211">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="eb379-212">RCL の一部としてコンパニオン資産を含めるには、クラスライブラリに*wwwroot*フォルダーを作成し、そのフォルダーに必要なファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="eb379-212">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="eb379-213">RCL をパッキングすると、 *wwwroot*フォルダー内のすべてのコンパニオン資産がパッケージに自動的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="eb379-213">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="eb379-214">静的アセットを除外する</span><span class="sxs-lookup"><span data-stu-id="eb379-214">Exclude static assets</span></span>

<span data-ttu-id="eb379-215">静的アセットを除外するには、目的の除外パス`$(DefaultItemExcludes)`をプロジェクトファイルのプロパティグループに追加します。</span><span class="sxs-lookup"><span data-stu-id="eb379-215">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="eb379-216">エントリをセミコロン (`;`) で区切ります。</span><span class="sxs-lookup"><span data-stu-id="eb379-216">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="eb379-217">次の例では、 *wwwroot*フォルダーの *.lib*スタイルシートは静的アセットとは見なされず、公開された rcl には含まれていません。</span><span class="sxs-lookup"><span data-stu-id="eb379-217">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="eb379-218">Typescript の統合</span><span class="sxs-lookup"><span data-stu-id="eb379-218">Typescript integration</span></span>

<span data-ttu-id="eb379-219">TypeScript ファイルを RCL に含めるには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="eb379-219">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="eb379-220">TypeScript ファイル (*ts*) を*wwwroot*フォルダーの外側に配置します。</span><span class="sxs-lookup"><span data-stu-id="eb379-220">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="eb379-221">たとえば、ファイルを*クライアント*フォルダーに配置します。</span><span class="sxs-lookup"><span data-stu-id="eb379-221">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="eb379-222">*Wwwroot*フォルダーの TypeScript ビルド出力を構成します。</span><span class="sxs-lookup"><span data-stu-id="eb379-222">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="eb379-223">プロジェクトファイル`TypescriptOutDir`の`PropertyGroup`内でプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="eb379-223">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="eb379-224">プロジェクトファイルの`PropertyGroup`内に次のターゲットを`ResolveCurrentProjectStaticWebAssets`追加することにより、TypeScript ターゲットをターゲットの依存関係として含めます。</span><span class="sxs-lookup"><span data-stu-id="eb379-224">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="eb379-225">参照されている RCL からのコンテンツの使用</span><span class="sxs-lookup"><span data-stu-id="eb379-225">Consume content from a referenced RCL</span></span>

<span data-ttu-id="eb379-226">RCL の*wwwroot*フォルダーに含まれるファイルは、プレフィックス`_content/{LIBRARY NAME}/`の下にあるアプリに公開されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-226">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="eb379-227">たとえば、という名前のライブラリを使用すると、の静的コンテンツ`_content/Razor.Class.Lib/`へのパスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-227">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="eb379-228">使用中のアプリは`<script>` `<img>`、、 `<style>`、、およびその他の HTML タグを使用して、ライブラリによって提供される静的アセットを参照します。</span><span class="sxs-lookup"><span data-stu-id="eb379-228">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="eb379-229">使用中のアプリでは、次の方法`Startup.Configure`で[静的ファイルのサポート](xref:fundamentals/static-files)を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eb379-229">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="eb379-230">ビルド出力 (`dotnet run`) から使用中のアプリを実行すると、開発環境では、静的な web アセットが既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="eb379-230">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="eb379-231">ビルド出力から実行するときに他の環境のアセットを`UseStaticWebAssets`サポートするには、 *Program.cs*のホストビルダーでを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eb379-231">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="eb379-232">発行`UseStaticWebAssets`された出力 (`dotnet publish`) からアプリを実行する場合、を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="eb379-232">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="eb379-233">複数プロジェクトの開発フロー</span><span class="sxs-lookup"><span data-stu-id="eb379-233">Multi-project development flow</span></span>

<span data-ttu-id="eb379-234">コンシューマーアプリの実行時:</span><span class="sxs-lookup"><span data-stu-id="eb379-234">When the consuming app runs:</span></span>

* <span data-ttu-id="eb379-235">RCL のアセットは元のフォルダーにとどまります。</span><span class="sxs-lookup"><span data-stu-id="eb379-235">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="eb379-236">アセットは、消費側のアプリに移動されません。</span><span class="sxs-lookup"><span data-stu-id="eb379-236">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="eb379-237">Rcl の*wwwroot*フォルダー内の変更は、rcl がリビルドされた後、使用中のアプリを再構築せずに、消費側アプリに反映されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-237">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="eb379-238">RCL がビルドされると、静的な web 資産の場所を記述するマニフェストが生成されます。</span><span class="sxs-lookup"><span data-stu-id="eb379-238">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="eb379-239">コンシューマーアプリは、実行時にマニフェストを読み取り、参照されたプロジェクトとパッケージのアセットを使用します。</span><span class="sxs-lookup"><span data-stu-id="eb379-239">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="eb379-240">RCL に新しいアセットが追加されたときに、使用中のアプリが新しい資産にアクセスできるようにするには、そのマニフェストを更新するために RCL を再構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eb379-240">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="eb379-241">パブリッシュ</span><span class="sxs-lookup"><span data-stu-id="eb379-241">Publish</span></span>

<span data-ttu-id="eb379-242">アプリが発行されると、参照されているすべてのプロジェクトおよびパッケージの関連する資産が、で`_content/{LIBRARY NAME}/`発行されたアプリの wwwroot フォルダーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="eb379-242">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end
