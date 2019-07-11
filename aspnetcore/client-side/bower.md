---
title: ASP.NET Core での Bower でのクライアント側パッケージを管理します。
author: rick-anderson
description: Bower でのクライアント側パッケージの管理。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 1292b75bdfe1cea56d9c8bc282b0dd90fc945a33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813528"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a><span data-ttu-id="a22a8-103">ASP.NET Core での Bower でのクライアント側パッケージを管理します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-103">Manage client-side packages with Bower in ASP.NET Core</span></span>

<span data-ttu-id="a22a8-104">によって[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Noel Rice](https://twitter.com/noelrice1)、および[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="a22a8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel Rice](https://twitter.com/noelrice1), and [Scott Addie](https://scottaddie.com)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a22a8-105">Bower を保持したまま、別のソリューションを使用して、管理者がお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a22a8-105">While Bower is maintained, its maintainers recommend using a different solution.</span></span> <span data-ttu-id="a22a8-106">[ライブラリ マネージャー](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (略して LibMan) は、Visual Studio の新しいクライアント側ライブラリ取得ツール (Visual Studio 15.8 またはそれ以降)。</span><span class="sxs-lookup"><span data-stu-id="a22a8-106">[Library Manager](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (LibMan for short) is Visual Studio's new client-side library acquisition tool (Visual Studio 15.8 or later).</span></span> <span data-ttu-id="a22a8-107">詳細については、「 <xref:client-side/libman/index> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a22a8-107">For more information, see <xref:client-side/libman/index>.</span></span> <span data-ttu-id="a22a8-108">Bower は、Visual studio バージョン 15.5 でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-108">Bower is supported in Visual Studio through version 15.5.</span></span>
>
> <span data-ttu-id="a22a8-109">Webpack と yarn を 1 つの一般的な代替手段は、[移行手順](https://bower.io/blog/2017/how-to-migrate-away-from-bower/)利用できます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-109">Yarn with Webpack is one popular alternative for which [migration instructions](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) are available.</span></span>

<span data-ttu-id="a22a8-110">[Bower](https://bower.io/) 「web 用のパッケージ マネージャー」自体を呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="a22a8-110">[Bower](https://bower.io/) calls itself "A package manager for the web".</span></span> <span data-ttu-id="a22a8-111">、.NET エコシステム内で NuGet の静的コンテンツ ファイルを提供できないまま void が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-111">Within the .NET ecosystem, it fills the void left by NuGet's inability to deliver static content files.</span></span> <span data-ttu-id="a22a8-112">ASP.NET Core プロジェクトでは、これらの静的ファイルはなどのクライアント側ライブラリを本質的な[jQuery](https://jquery.com/)と[ブートス トラップ](https://getbootstrap.com/)します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-112">For ASP.NET Core projects, these static files are inherent to client-side libraries like [jQuery](https://jquery.com/) and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="a22a8-113">.NET ライブラリを使用することも[NuGet](https://www.nuget.org/)パッケージ マネージャー。</span><span class="sxs-lookup"><span data-stu-id="a22a8-113">For .NET libraries, you still use [NuGet](https://www.nuget.org/) package manager.</span></span>

<span data-ttu-id="a22a8-114">設定するクライアント側の ASP.NET Core プロジェクト テンプレートで作成した新しいプロジェクトはビルド プロセスです。</span><span class="sxs-lookup"><span data-stu-id="a22a8-114">New projects created with the ASP.NET Core project templates set up the client-side build process.</span></span> <span data-ttu-id="a22a8-115">[jQuery](https://jquery.com/)と[ブートス トラップ](https://getbootstrap.com/)がインストールされている場合、Bower はサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a22a8-115">[jQuery](https://jquery.com/) and [Bootstrap](https://getbootstrap.com/) are installed, and Bower is supported.</span></span>

<span data-ttu-id="a22a8-116">クライアント側のパッケージに記載されて、 *bower.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="a22a8-116">Client-side packages are listed in the *bower.json* file.</span></span> <span data-ttu-id="a22a8-117">ASP.NET Core のプロジェクト テンプレートを構成します*bower.json* jQuery、jQuery の検証、およびブートス トラップを使用します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-117">The ASP.NET Core project templates configures *bower.json* with jQuery, jQuery validation, and Bootstrap.</span></span>

<span data-ttu-id="a22a8-118">このチュートリアルでは、サポートを追加します[Font Awesome](http://fontawesome.io)します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-118">In this tutorial, we'll add support for [Font Awesome](http://fontawesome.io).</span></span> <span data-ttu-id="a22a8-119">Bower パッケージと共にインストールできる、 **Bower パッケージの管理**UI または手動で、 *bower.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="a22a8-119">Bower packages can be installed with the **Manage Bower Packages** UI or manually in the *bower.json* file.</span></span>

### <a name="installation-via-manage-bower-packages-ui"></a><span data-ttu-id="a22a8-120">Bower パッケージの管理 UI を使用してインストール</span><span class="sxs-lookup"><span data-stu-id="a22a8-120">Installation via Manage Bower Packages UI</span></span>

* <span data-ttu-id="a22a8-121">新しい ASP.NET Core Web アプリを作成、 **ASP.NET Core Web アプリケーション (.NET Core)** テンプレート。</span><span class="sxs-lookup"><span data-stu-id="a22a8-121">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="a22a8-122">選択**Web アプリケーション**と**認証なし**します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-122">Select **Web Application** and **No Authentication**.</span></span>

* <span data-ttu-id="a22a8-123">ソリューション エクスプ ローラーでプロジェクトを右クリックして**Bower パッケージの管理**(またはメイン メニューで、**プロジェクト** > **Bower パッケージの管理**).</span><span class="sxs-lookup"><span data-stu-id="a22a8-123">Right-click the project in Solution Explorer and select **Manage Bower Packages** (alternatively from the main menu, **Project** > **Manage Bower Packages**).</span></span>

* <span data-ttu-id="a22a8-124">**Bower:\<プロジェクト名\>** ウィンドウで、[参照] タブをクリックし、入力して、パッケージ一覧をフィルター`font-awesome`検索ボックスで。</span><span class="sxs-lookup"><span data-stu-id="a22a8-124">In the **Bower: \<project name\>** window, click the "Browse" tab, and then filter the packages list by entering `font-awesome` in the search box:</span></span>

  ![bower パッケージを管理します。](bower/_static/manage-bower-packages.png)

* <span data-ttu-id="a22a8-126">いることを確認、"に変更を保存*bower.json*"チェック ボックスがオンにします。</span><span class="sxs-lookup"><span data-stu-id="a22a8-126">Confirm that the "Save changes to *bower.json*" check box is checked.</span></span> <span data-ttu-id="a22a8-127">ドロップダウン リストからバージョンを選択し、**インストール**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="a22a8-127">Select a version from the drop-down list and click the **Install** button.</span></span> <span data-ttu-id="a22a8-128">**出力**ウィンドウには、インストールの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-128">The **Output** window shows the installation details.</span></span>

### <a name="manual-installation-in-bowerjson"></a><span data-ttu-id="a22a8-129">Bower.json の手動インストール</span><span class="sxs-lookup"><span data-stu-id="a22a8-129">Manual installation in bower.json</span></span>

<span data-ttu-id="a22a8-130">開く、 *bower.json*ファイルを開き、依存関係に"フォント awesome"を追加します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-130">Open the *bower.json* file and add "font-awesome" to the dependencies.</span></span> <span data-ttu-id="a22a8-131">IntelliSense は、利用可能なパッケージを示しています。</span><span class="sxs-lookup"><span data-stu-id="a22a8-131">IntelliSense shows the available packages.</span></span> <span data-ttu-id="a22a8-132">パッケージを選択すると、使用可能なバージョンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-132">When a package is selected, the available versions are displayed.</span></span> <span data-ttu-id="a22a8-133">次のイメージは古いし、表示される内容と一致しません。</span><span class="sxs-lookup"><span data-stu-id="a22a8-133">The images below are older and won't match what you see.</span></span>

![Bower パッケージ エクスプ ローラーの IntelliSense](bower/_static/add-package.png)

![bower バージョン IntelliSense](bower/_static/version-intelliSense.png)

<span data-ttu-id="a22a8-136">Bower は[セマンティック バージョニング](https://semver.org/)依存関係を整理します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-136">Bower uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="a22a8-137">セマンティック バージョニング、SemVer とも呼ばれる、パッケージを識別する番号付けスキームで\<メジャー >.\<マイナー >。\<パッチ >。</span><span class="sxs-lookup"><span data-stu-id="a22a8-137">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="a22a8-138">IntelliSense は、いくつかの一般的な選択肢のみを表示することによって、セマンティック バージョン管理を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-138">IntelliSense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="a22a8-139">IntelliSense の一覧 (上記の例では 4.6.3) の一番上の項目は、パッケージの最新の安定バージョンと見なされます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-139">The top item in the IntelliSense list (4.6.3 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="a22a8-140">キャレット (^) 記号が最新のメジャー バージョンと一致して、一致する最新のマイナー バージョンをティルダ (~)。</span><span class="sxs-lookup"><span data-stu-id="a22a8-140">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span>

<span data-ttu-id="a22a8-141">保存、 *bower.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="a22a8-141">Save the *bower.json* file.</span></span> <span data-ttu-id="a22a8-142">Visual Studio のウォッチ、 *bower.json*ファイルの変更。</span><span class="sxs-lookup"><span data-stu-id="a22a8-142">Visual Studio watches the *bower.json* file for changes.</span></span> <span data-ttu-id="a22a8-143">保存時に、 *bower のインストール*コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-143">Upon saving, the *bower install* command is executed.</span></span> <span data-ttu-id="a22a8-144">出力ウィンドウの表示**bower/npm**正確に実行されるコマンドのビュー。</span><span class="sxs-lookup"><span data-stu-id="a22a8-144">See the Output window's **Bower/npm** view for the exact command executed.</span></span>

<span data-ttu-id="a22a8-145">開く、 *.bowerrc*ファイル*bower.json*します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-145">Open the *.bowerrc* file under *bower.json*.</span></span> <span data-ttu-id="a22a8-146">`directory`プロパティに設定されて*wwwroot/lib*パッケージ資産をインストール、Bower の場所を示します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-146">The `directory` property is set to *wwwroot/lib* which indicates the location Bower will install the package assets.</span></span>

```json
{
 "directory": "wwwroot/lib"
}
```

<span data-ttu-id="a22a8-147">検索し、フォント awesome パッケージを表示するのには、ソリューション エクスプ ローラーで検索ボックスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-147">You can use the search box in Solution Explorer to find and display the font-awesome package.</span></span>

<span data-ttu-id="a22a8-148">開く、 *views \shared\_Layout.cshtml*ファイルを開き、フォント awesome の CSS ファイルを環境に追加[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)の`Development`します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-148">Open the *Views\Shared\_Layout.cshtml* file and add the font-awesome CSS file to the environment [Tag Helper](xref:mvc/views/tag-helpers/intro) for `Development`.</span></span> <span data-ttu-id="a22a8-149">ソリューション エクスプ ローラーからドラッグ アンド ドロップ*フォント awesome.css*内で、`<environment names="Development">`要素。</span><span class="sxs-lookup"><span data-stu-id="a22a8-149">From Solution Explorer, drag and drop *font-awesome.css* inside the `<environment names="Development">` element.</span></span>

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

<span data-ttu-id="a22a8-150">運用アプリでは追加*フォント awesome.min.css*の環境タグ ヘルパーを`Staging,Production`します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-150">In a production app you would add *font-awesome.min.css* to the environment tag helper for `Staging,Production`.</span></span>

<span data-ttu-id="a22a8-151">内容を置き換える、 *Views\Home\About.cshtml*次のマークアップでの Razor ファイル。</span><span class="sxs-lookup"><span data-stu-id="a22a8-151">Replace the contents of the *Views\Home\About.cshtml* Razor file with the following markup:</span></span>

[!code-html[](bower/sample/About.cshtml)]

<span data-ttu-id="a22a8-152">アプリを実行し、フォント awesome パッケージの動作を検証する About ビューに移動します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-152">Run the app and navigate to the About view to verify the font-awesome package works.</span></span>

## <a name="exploring-the-client-side-build-process"></a><span data-ttu-id="a22a8-153">クライアント側のビルド プロセスを調べる</span><span class="sxs-lookup"><span data-stu-id="a22a8-153">Exploring the client-side build process</span></span>

<span data-ttu-id="a22a8-154">ほとんどの ASP.NET Core プロジェクト テンプレートは、Bower を使用して既に構成されます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-154">Most ASP.NET Core project templates are already configured to use Bower.</span></span> <span data-ttu-id="a22a8-155">この次のチュートリアルでは、空の ASP.NET Core プロジェクトから開始して、プロジェクトでの Bower の使用方法の感覚を取得できるように各部分を手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-155">This next walkthrough starts with an empty ASP.NET Core project and adds each piece manually, so you can get a feel for how Bower is used in a project.</span></span> <span data-ttu-id="a22a8-156">プロジェクトの構造を出力の各構成の変更が加えられると、ランタイムの動作を確認できます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-156">You can see what happens to the project structure and the runtime output as each configuration change is made.</span></span>

<span data-ttu-id="a22a8-157">Bower でクライアント側のビルド プロセスを使用する一般的な手順は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a22a8-157">The general steps to use the client-side build process with Bower are:</span></span>

* <span data-ttu-id="a22a8-158">プロジェクトで使用されるパッケージを定義します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-158">Define packages used in your project.</span></span> <!-- once defined, you don't need to download them, VS does -->
* <span data-ttu-id="a22a8-159">Web ページからパッケージを参照します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-159">Reference packages from your web pages.</span></span>

### <a name="define-packages"></a><span data-ttu-id="a22a8-160">パッケージを定義します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-160">Define packages</span></span>

<span data-ttu-id="a22a8-161">パッケージを一覧表示すると、 *bower.json*ファイル、Visual Studio がダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-161">Once you list packages in the *bower.json* file, Visual Studio will download them.</span></span> <span data-ttu-id="a22a8-162">次の例では、jQuery とブートス トラップを読み込むに Bower を使用して、 *wwwroot*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="a22a8-162">The following example uses Bower to load jQuery and Bootstrap to the *wwwroot* folder.</span></span>

* <span data-ttu-id="a22a8-163">新しい ASP.NET Core Web アプリを作成、 **ASP.NET Core Web アプリケーション (.NET Core)** テンプレート。</span><span class="sxs-lookup"><span data-stu-id="a22a8-163">Create a new ASP.NET Core Web app with the **ASP.NET Core Web Application (.NET Core)** template.</span></span> <span data-ttu-id="a22a8-164">選択、**空**プロジェクト テンプレートとクリック**OK**。</span><span class="sxs-lookup"><span data-stu-id="a22a8-164">Select the **Empty** project template and click **OK**.</span></span>

* <span data-ttu-id="a22a8-165">ソリューション エクスプ ローラーでプロジェクトを右クリックして >**新しい項目の追加**選択**Bower 構成ファイル**します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-165">In Solution Explorer, right-click the project > **Add New Item** and select **Bower Configuration File**.</span></span> <span data-ttu-id="a22a8-166">メモ:A *.bowerrc*ファイルも追加されます。</span><span class="sxs-lookup"><span data-stu-id="a22a8-166">Note: A *.bowerrc* file is also added.</span></span>

* <span data-ttu-id="a22a8-167">開いている*bower.json*、jquery を追加し、ブートス トラップ、`dependencies`セクション。</span><span class="sxs-lookup"><span data-stu-id="a22a8-167">Open *bower.json*, and add jquery and bootstrap to the `dependencies` section.</span></span> <span data-ttu-id="a22a8-168">その結果、 *bower.json*ファイルは次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="a22a8-168">The resulting *bower.json* file will look like the following example.</span></span> <span data-ttu-id="a22a8-169">バージョンは、時間の経過と共にが変更され、下の画像が一致しません。</span><span class="sxs-lookup"><span data-stu-id="a22a8-169">The versions will change over time and may not match the image below.</span></span>

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* <span data-ttu-id="a22a8-170">保存、 *bower.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="a22a8-170">Save the *bower.json* file.</span></span>

  <span data-ttu-id="a22a8-171">プロジェクトを含むことを確認、*ブートス トラップ*と*jQuery*ディレクトリ*wwwroot/lib*します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-171">Verify the project includes the *bootstrap* and *jQuery* directories in *wwwroot/lib*.</span></span> <span data-ttu-id="a22a8-172">Bower は、 *.bowerrc*で資産をインストールするファイル*wwwroot/lib*します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-172">Bower uses the *.bowerrc* file to install the assets in *wwwroot/lib*.</span></span>

  <span data-ttu-id="a22a8-173">メモ:"Bower パッケージの管理 の UI では、手動のファイルを編集する代わりに提供します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-173">Note: The "Manage Bower Packages" UI provides an alternative to manual file editing.</span></span>

### <a name="enable-static-files"></a><span data-ttu-id="a22a8-174">静的ファイルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a22a8-174">Enable static files</span></span>

* <span data-ttu-id="a22a8-175">追加、`Microsoft.AspNetCore.StaticFiles`をプロジェクトに NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="a22a8-175">Add the `Microsoft.AspNetCore.StaticFiles` NuGet package to the project.</span></span>
* <span data-ttu-id="a22a8-176">静的ファイルで処理を有効にする、[静的ファイル ミドルウェア](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-176">Enable static files to be served with the [Static file middleware](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions).</span></span> <span data-ttu-id="a22a8-177">呼び出しを追加して[UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions)を`Configure`メソッドの`Startup`します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-177">Add a call to [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) to the `Configure` method of `Startup`.</span></span>

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a><span data-ttu-id="a22a8-178">参照パッケージ</span><span class="sxs-lookup"><span data-stu-id="a22a8-178">Reference packages</span></span>

<span data-ttu-id="a22a8-179">このセクションでは、配置したパッケージにアクセスできることを確認する HTML ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-179">In this section, you will create an HTML page to verify it can access the deployed packages.</span></span>

* <span data-ttu-id="a22a8-180">という名前の新しい HTML ページを追加*Index.html*を*wwwroot*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="a22a8-180">Add a new HTML page named *Index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="a22a8-181">メモ:HTML ファイルを追加する必要があります、 *wwwroot*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="a22a8-181">Note: You must add the HTML file to the *wwwroot* folder.</span></span> <span data-ttu-id="a22a8-182">既定では、外部の静的コンテンツを提供できない*wwwroot*します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-182">By default, static content cannot be served outside *wwwroot*.</span></span> <span data-ttu-id="a22a8-183">参照してください[静的ファイル](xref:fundamentals/static-files)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="a22a8-183">See [Static files](xref:fundamentals/static-files) for more information.</span></span>

  <span data-ttu-id="a22a8-184">内容を置き換える*Index.html*を次のマークアップ。</span><span class="sxs-lookup"><span data-stu-id="a22a8-184">Replace the contents of *Index.html* with the following markup:</span></span>

[!code-html[](bower/sample/Index.html)]

* <span data-ttu-id="a22a8-185">アプリを実行しに移動します`http://localhost:<port>/Index.html`します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-185">Run the app and navigate to `http://localhost:<port>/Index.html`.</span></span> <span data-ttu-id="a22a8-186">代わりに、 *Index.html*開かれると、キーを押して`Ctrl+Shift+W`します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-186">Alternatively, with *Index.html* opened, press `Ctrl+Shift+W`.</span></span> <span data-ttu-id="a22a8-187">Jumbotron スタイルが適用される、jQuery コード、ボタンがクリックされたときの応答およびブートス トラップのボタンが状態を変更することを確認します。</span><span class="sxs-lookup"><span data-stu-id="a22a8-187">Verify that the jumbotron styling is applied, the jQuery code responds when the button is clicked, and that the Bootstrap button changes state.</span></span>

  ![jumbotron スタイルの適用](bower/_static/jumbotron.png)
