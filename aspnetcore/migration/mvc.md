---
title: ASP.NET MVC から ASP.NET Core MVC への移行
author: ardalis
description: ASP.NET MVC プロジェクトの ASP.NET Core MVC への移行を開始する方法について説明します。
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: 6c9449fb43960d05db8aa6dcba64d3d830834cdb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652550"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="49187-103">ASP.NET MVC から ASP.NET Core MVC への移行</span><span class="sxs-lookup"><span data-stu-id="49187-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="49187-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、[上田 Smith](https://ardalis.com/)、 [Scott addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="49187-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="49187-105">この記事では、ASP.NET MVC プロジェクトの[ASP.NET CORE mvc](../mvc/overview.md)への移行を開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="49187-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="49187-106">このプロセスでは、ASP.NET MVC から変更されたものの多くを強調表示します。</span><span class="sxs-lookup"><span data-stu-id="49187-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="49187-107">ASP.NET MVC からの移行は、複数の手順から構成されるプロセスです。この記事では、初期セットアップ、基本的なコントローラーとビュー、静的コンテンツ、およびクライアント側の依存関係について説明します。</span><span class="sxs-lookup"><span data-stu-id="49187-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="49187-108">その他の記事では、多くの ASP.NET MVC プロジェクトで検出された構成と id コードの移行について説明します。</span><span class="sxs-lookup"><span data-stu-id="49187-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="49187-109">サンプルのバージョン番号が最新ではない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="49187-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="49187-110">必要に応じて、プロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49187-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="49187-111">Starter ASP.NET MVC プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="49187-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="49187-112">アップグレードを示すために、まず ASP.NET MVC アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="49187-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="49187-113">名前空間が次の手順で作成する ASP.NET Core プロジェクトと一致するように、 *WebApp1*という名前を付けて作成します。</span><span class="sxs-lookup"><span data-stu-id="49187-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio の [新しいプロジェクト] ダイアログ](mvc/_static/new-project.png)

![新しい Web アプリケーションダイアログ: ASP.NET templates パネルで選択された MVC プロジェクトテンプレート](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="49187-116">*省略可能:* ソリューションの名前を*WebApp1*から*Mvc5*に変更します。</span><span class="sxs-lookup"><span data-stu-id="49187-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="49187-117">Visual Studio に新しいソリューション名 (*Mvc5*) が表示されます。これにより、次のプロジェクトからこのプロジェクトを簡単に指定できます。</span><span class="sxs-lookup"><span data-stu-id="49187-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="49187-118">ASP.NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="49187-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="49187-119">2つのプロジェクトの名前空間が一致するように、前のプロジェクト (*WebApp1*) と同じ名前の新しい*空*の ASP.NET Core web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="49187-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="49187-120">同じ名前空間を使用すると、2つのプロジェクト間でコードを簡単にコピーできます。</span><span class="sxs-lookup"><span data-stu-id="49187-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="49187-121">このプロジェクトは、同じ名前を使用する前のプロジェクトとは別のディレクトリに作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49187-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![[新しいプロジェクト] ダイアログ](mvc/_static/new_core.png)

![New ASP.NET Web Application dialog: ASP.NET Core テンプレートパネルで空のプロジェクトテンプレートが選択されました](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="49187-124">*省略可能:* *Web アプリケーション*プロジェクトテンプレートを使用して、新しい ASP.NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="49187-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="49187-125">プロジェクトに*WebApp1*という名前を設定し、**個々のユーザーアカウント**の認証オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="49187-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="49187-126">このアプリの名前を*FullAspNetCore*に変更します。</span><span class="sxs-lookup"><span data-stu-id="49187-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="49187-127">このプロジェクトを作成すると、変換にかかる時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="49187-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="49187-128">テンプレートで生成されたコードを参照して、最終的な結果を確認したり、変換プロジェクトにコードをコピーしたりできます。</span><span class="sxs-lookup"><span data-stu-id="49187-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="49187-129">また、テンプレートで生成されたプロジェクトと比較するために変換手順でスタックしている場合にも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="49187-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="49187-130">MVC を使用するようにサイトを構成する</span><span class="sxs-lookup"><span data-stu-id="49187-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="49187-131">.NET Core を対象とする場合、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)は既定で参照されます。</span><span class="sxs-lookup"><span data-stu-id="49187-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="49187-132">このパッケージには、MVC アプリで一般的に使用されるパッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="49187-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="49187-133">.NET Framework を対象とする場合は、パッケージ参照をプロジェクトファイルに個別に一覧表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49187-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="49187-134">.NET Core を対象とする場合、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage)は既定で参照されます。</span><span class="sxs-lookup"><span data-stu-id="49187-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="49187-135">このパッケージには、MVC アプリによって一般的に使用されるパッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="49187-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="49187-136">.NET Framework を対象とする場合は、パッケージ参照をプロジェクトファイルに個別に一覧表示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49187-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="49187-137">.NET Core または .NET Framework を対象とする場合、MVC アプリによって頻繁に使用されるパッケージは、プロジェクトファイルに個別に一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="49187-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="49187-138">`Microsoft.AspNetCore.Mvc` は ASP.NET Core MVC フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="49187-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="49187-139">`Microsoft.AspNetCore.StaticFiles` は静的ファイルハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="49187-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="49187-140">ASP.NET Core ランタイムはモジュール形式であり、静的ファイルの提供を明示的に選択する必要があります (「[静的ファイル](xref:fundamentals/static-files)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="49187-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="49187-141">*Startup.cs*ファイルを開き、次のコードに一致するようにコードを変更します。</span><span class="sxs-lookup"><span data-stu-id="49187-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="49187-142">`UseStaticFiles` 拡張メソッドは、静的ファイルハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="49187-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="49187-143">前述のように、ASP.NET ランタイムはモジュール形式であるため、静的ファイルの提供を明示的に選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49187-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="49187-144">`UseMvc` 拡張メソッドは、ルーティングを追加します。</span><span class="sxs-lookup"><span data-stu-id="49187-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="49187-145">詳細については、「[アプリケーションの起動](xref:fundamentals/startup)と[ルーティング](xref:fundamentals/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49187-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="49187-146">コントローラーとビューを追加する</span><span class="sxs-lookup"><span data-stu-id="49187-146">Add a controller and view</span></span>

<span data-ttu-id="49187-147">このセクションでは、最小コントローラーとビューを追加して、次のセクションで移行する ASP.NET MVC コントローラーとビューのプレースホルダーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="49187-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="49187-148">*Controllers*フォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="49187-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="49187-149">*HomeController.cs*という名前の**コントローラークラス**を*Controllers*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="49187-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![[新しい項目の追加] ダイアログ](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="49187-151">*Views*フォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="49187-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="49187-152">*ビュー/ホーム*フォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="49187-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="49187-153">*Views/Home*フォルダーに、 *Index. cshtml*という名前の**Razor ビュー**を追加します。</span><span class="sxs-lookup"><span data-stu-id="49187-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![[新しい項目の追加] ダイアログ](mvc/_static/view.png)

<span data-ttu-id="49187-155">プロジェクトの構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="49187-155">The project structure is shown below:</span></span>

![WebApp1 のファイルとフォルダーを示すソリューションエクスプローラー](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="49187-157">*Views/Home/Index. cshtml*ファイルの内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="49187-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="49187-158">アプリケーションを実行します。</span><span class="sxs-lookup"><span data-stu-id="49187-158">Run the app.</span></span>

![Microsoft Edge で開いている Web アプリ](mvc/_static/hello-world.png)

<span data-ttu-id="49187-160">詳細については、「[コントローラー](xref:mvc/controllers/actions)と[ビュー](xref:mvc/views/overview) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49187-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="49187-161">最小限の作業 ASP.NET Core プロジェクトを作成したので、ASP.NET MVC プロジェクトから機能の移行を開始できます。</span><span class="sxs-lookup"><span data-stu-id="49187-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="49187-162">次のものを移動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49187-162">We need to move the following:</span></span>

* <span data-ttu-id="49187-163">クライアント側コンテンツ (CSS、フォント、およびスクリプト)</span><span class="sxs-lookup"><span data-stu-id="49187-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="49187-164">controllers</span><span class="sxs-lookup"><span data-stu-id="49187-164">controllers</span></span>

* <span data-ttu-id="49187-165">表示</span><span class="sxs-lookup"><span data-stu-id="49187-165">views</span></span>

* <span data-ttu-id="49187-166">モデル</span><span class="sxs-lookup"><span data-stu-id="49187-166">models</span></span>

* <span data-ttu-id="49187-167">まとめる</span><span class="sxs-lookup"><span data-stu-id="49187-167">bundling</span></span>

* <span data-ttu-id="49187-168">フィルタ</span><span class="sxs-lookup"><span data-stu-id="49187-168">filters</span></span>

* <span data-ttu-id="49187-169">ログイン/送信、Id (これは次のチュートリアルで行います)。</span><span class="sxs-lookup"><span data-stu-id="49187-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="49187-170">コントローラーとビュー</span><span class="sxs-lookup"><span data-stu-id="49187-170">Controllers and views</span></span>

* <span data-ttu-id="49187-171">ASP.NET MVC `HomeController` の各メソッドを新しい `HomeController`にコピーします。</span><span class="sxs-lookup"><span data-stu-id="49187-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="49187-172">ASP.NET MVC では、組み込みテンプレートのコントローラーアクションメソッドの戻り値の型は[Actionresult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); であることに注意してください。ASP.NET Core MVC では、アクションメソッドは代わりに `IActionResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="49187-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="49187-173">`ActionResult` は `IActionResult`を実装するので、アクションメソッドの戻り値の型を変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="49187-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="49187-174">ASP.NET MVC プロジェクトの*About. cshtml*、 *Contact.* cshtml、および*Index. cshtml* view ファイルを ASP.NET Core プロジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="49187-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="49187-175">ASP.NET Core アプリを実行し、各メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="49187-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="49187-176">レイアウトファイルをまだ移行していないため、表示されているビューにはビューファイルのコンテンツのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="49187-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="49187-177">`About` ビューと `Contact` ビューに対してレイアウトファイルが生成されたリンクがないため、ブラウザーからそれらを呼び出す必要があります ( **4492**はプロジェクトで使用されているポート番号に置き換えてください)。</span><span class="sxs-lookup"><span data-stu-id="49187-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![連絡先ページ](mvc/_static/contact-page.png)

<span data-ttu-id="49187-179">スタイル設定とメニュー項目がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="49187-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="49187-180">次のセクションでこれを修正します。</span><span class="sxs-lookup"><span data-stu-id="49187-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="49187-181">静的コンテンツ</span><span class="sxs-lookup"><span data-stu-id="49187-181">Static content</span></span>

<span data-ttu-id="49187-182">以前のバージョンの ASP.NET MVC では、静的コンテンツは web プロジェクトのルートからホストされており、サーバー側ファイルと混在していました。</span><span class="sxs-lookup"><span data-stu-id="49187-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="49187-183">ASP.NET Core では、静的なコンテンツは*wwwroot*フォルダーでホストされます。</span><span class="sxs-lookup"><span data-stu-id="49187-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="49187-184">古い ASP.NET MVC アプリの静的コンテンツを ASP.NET Core プロジェクトの*wwwroot*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="49187-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="49187-185">このサンプルの変換の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="49187-185">In this sample conversion:</span></span>

* <span data-ttu-id="49187-186">古い MVC プロジェクトの*favicon*ファイルを ASP.NET Core プロジェクトの*wwwroot*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="49187-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="49187-187">Old ASP.NET MVC プロジェクトでは、そのスタイルに[ブートストラップ](https://getbootstrap.com/)を使用して、ブートストラップファイルを*Content*フォルダーおよび*Scripts*フォルダーに格納します。</span><span class="sxs-lookup"><span data-stu-id="49187-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="49187-188">古い ASP.NET MVC プロジェクトを生成したテンプレートは、レイアウトファイル (*Views/Shared/_Layout*) のブートストラップを参照します。</span><span class="sxs-lookup"><span data-stu-id="49187-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="49187-189">ASP.NET MVC プロジェクトの*ブートストラップ*ファイルと*ブートストラップ*ファイルを、新しいプロジェクトの*wwwroot*フォルダーにコピーできます。</span><span class="sxs-lookup"><span data-stu-id="49187-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="49187-190">代わりに、次のセクションでは、CDNs を使用してブートストラップ (およびその他のクライアント側ライブラリ) のサポートを追加します。</span><span class="sxs-lookup"><span data-stu-id="49187-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="49187-191">レイアウトファイルを移行する</span><span class="sxs-lookup"><span data-stu-id="49187-191">Migrate the layout file</span></span>

* <span data-ttu-id="49187-192">古い ASP.NET MVC プロジェクトの*views*フォルダーの *_ViewStart*ファイルを ASP.NET Core プロジェクトの*views*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="49187-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="49187-193">*_ViewStart* ASP.NET Core MVC では、このファイルは変更されていません。</span><span class="sxs-lookup"><span data-stu-id="49187-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="49187-194">*ビュー/共有*フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="49187-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="49187-195">*省略可能:* *FullAspNetCore* MVC プロジェクトの*views*フォルダーから ASP.NET Core プロジェクトの*views*フォルダーに *_ViewImports*をコピーします。</span><span class="sxs-lookup"><span data-stu-id="49187-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="49187-196">*_ViewImports*ファイル内の名前空間宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="49187-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="49187-197">*_ViewImports*のファイルは、すべてのビューファイルの名前空間を提供し、[タグヘルパー](xref:mvc/views/tag-helpers/intro)を取り込みます。</span><span class="sxs-lookup"><span data-stu-id="49187-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="49187-198">タグヘルパーは、新しいレイアウトファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="49187-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="49187-199">*_ViewImports* 、ASP.NET Core の新しいファイルです。</span><span class="sxs-lookup"><span data-stu-id="49187-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="49187-200">古い ASP.NET MVC プロジェクトの*views/shared*フォルダーの *_Layout*ファイルを ASP.NET Core プロジェクトの*views/shared*フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="49187-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="49187-201">*_Layout*ファイルを開き、次のように変更します (完成したコードは次のようになります)。</span><span class="sxs-lookup"><span data-stu-id="49187-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="49187-202">`@Styles.Render("~/Content/css")` を `<link>` 要素に置き換えて、*ブートストラップ*を読み込みます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="49187-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="49187-203">`@Scripts.Render("~/bundles/modernizr")`を削除します。</span><span class="sxs-lookup"><span data-stu-id="49187-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="49187-204">`@Html.Partial("_LoginPartial")` 行をコメントアウトします (行を `@*...*@`で囲みます)。</span><span class="sxs-lookup"><span data-stu-id="49187-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="49187-205">詳細については[、「ASP.NET Core への認証と id の移行](xref:migration/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49187-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="49187-206">`@Scripts.Render("~/bundles/jquery")` を `<script>` の要素に置き換えます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="49187-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="49187-207">`@Scripts.Render("~/bundles/bootstrap")` を `<script>` の要素に置き換えます (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="49187-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="49187-208">ブートストラップ CSS インクルードの置換マークアップ:</span><span class="sxs-lookup"><span data-stu-id="49187-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="49187-209">JQuery および Bootstrap JavaScript インクルードの置換マークアップ:</span><span class="sxs-lookup"><span data-stu-id="49187-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="49187-210">更新された *_Layout*ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="49187-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="49187-211">ブラウザーでサイトを表示します。</span><span class="sxs-lookup"><span data-stu-id="49187-211">View the site in the browser.</span></span> <span data-ttu-id="49187-212">これで、必要なスタイルが適切に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="49187-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="49187-213">*省略可能:* 新しいレイアウトファイルを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="49187-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="49187-214">このプロジェクトでは、 *FullAspNetCore*プロジェクトからレイアウトファイルをコピーできます。</span><span class="sxs-lookup"><span data-stu-id="49187-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="49187-215">新しいレイアウトファイルは[タグヘルパー](xref:mvc/views/tag-helpers/intro)を使用し、その他の機能強化が行われています。</span><span class="sxs-lookup"><span data-stu-id="49187-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="49187-216">バンドルと縮小の構成</span><span class="sxs-lookup"><span data-stu-id="49187-216">Configure bundling and minification</span></span>

<span data-ttu-id="49187-217">バンドルと縮小を構成する方法の詳細については、「[バンドルと縮小](../client-side/bundling-and-minification.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49187-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="49187-218">HTTP 500 エラーを解決する</span><span class="sxs-lookup"><span data-stu-id="49187-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="49187-219">問題の原因としては、問題の原因に関する情報が含まれていない HTTP 500 のエラーメッセージが表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="49187-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="49187-220">たとえば、 *Views/_ViewImports cshtml*ファイルにプロジェクト内に存在しない名前空間が含まれている場合、HTTP 500 エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="49187-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="49187-221">ASP.NET Core アプリの既定では、`UseDeveloperExceptionPage` 拡張機能が `IApplicationBuilder` に追加され、構成が*開発*されるときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="49187-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="49187-222">詳細については、次のコードを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="49187-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="49187-223">ASP.NET Core は、web アプリ内の未処理の例外を HTTP 500 エラー応答に変換します。</span><span class="sxs-lookup"><span data-stu-id="49187-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="49187-224">通常、サーバーに関する機密情報が漏えいするのを防ぐために、エラーの詳細はこれらの応答に含まれていません。</span><span class="sxs-lookup"><span data-stu-id="49187-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="49187-225">詳細については、「[エラーの処理](../fundamentals/error-handling.md)」の「**開発者の例外ページの使用**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="49187-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49187-226">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="49187-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
