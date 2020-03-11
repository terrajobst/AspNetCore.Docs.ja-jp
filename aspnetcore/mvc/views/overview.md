---
title: ASP.NET Core MVC のビュー
author: ardalis
description: ビューがアプリのデータ表示と、ASP.NET Core MVC でのユーザー操作を処理する方法について説明します。
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: de78624bafeee16a3ace322643cf89337531eef8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654122"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="2011a-103">ASP.NET Core MVC のビュー</span><span class="sxs-lookup"><span data-stu-id="2011a-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="2011a-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2011a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2011a-105">このドキュメントでは、ASP.NET Core MVC アプリケーションで使用されるビューについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2011a-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="2011a-106">Razor ページについては、「[ASP.NET Core での Razor ページの概要](xref:razor-pages/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2011a-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="2011a-107">Model-View-Controller (MVC) パターンでは、*ビュー*がアプリのデータ表示とユーザー操作を処理します。</span><span class="sxs-lookup"><span data-stu-id="2011a-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="2011a-108">ビューは、[Razor マークアップ](xref:mvc/views/razor)が埋め込まれた HTML テンプレートてです。</span><span class="sxs-lookup"><span data-stu-id="2011a-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="2011a-109">Razor マークアップは、クライアントに送信する Web ページを生成するために HTML マークアップと対話するコードです。</span><span class="sxs-lookup"><span data-stu-id="2011a-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="2011a-110">ASP.NET Core MVC では、ビューは、Razor マークアップで *C# プログラミング言語*を使用する [.cshtml](/dotnet/csharp/) ファイルです。</span><span class="sxs-lookup"><span data-stu-id="2011a-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="2011a-111">通常、ビュー ファイルは、各アプリの[コントローラー](xref:mvc/controllers/actions)の名前が付いたフォルダーにグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="2011a-112">これらのフォルダーは、アプリのルートにある *Views* フォルダーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Visual Studio のソリューション エクスプローラーで Views フォルダー、Home フォルダーが開かれ、About.cshtml、Contact.cshtml、および Index.cshtml ファイルが示されています。](overview/_static/views_solution_explorer.png)

<span data-ttu-id="2011a-114">*ホーム* コントローラーは、*Views* フォルダー内の *Home* フォルダーによって表されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="2011a-115">*Home* フォルダーには、*About*、*Contact*、*Index* (ホームページ) の Web ページのビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2011a-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="2011a-116">ユーザーがこれら 3 つの Web ページのうちの 1 つを要求すると、*ホーム* コントローラー内のコントローラー アクションが 3 つのビューからビルドに使用するものを決定して、ユーザーに Web ページを返します。</span><span class="sxs-lookup"><span data-stu-id="2011a-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="2011a-117">[レイアウト](xref:mvc/views/layout)を使用して、一貫性のある Web ページ セクションを提供し、コードの繰り返しを削減します。</span><span class="sxs-lookup"><span data-stu-id="2011a-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="2011a-118">多くの場合、レイアウトには、ヘッダー、ナビゲーションとメニュー要素、フッターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2011a-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="2011a-119">ヘッダーとフッターには通常、多くのメタデータ要素とスクリプトおよびスタイル アセットへのリンクの定型マークアップが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2011a-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="2011a-120">レイアウトは、ビューでこの定型マークアップを回避するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2011a-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="2011a-121">[部分ビュー](xref:mvc/views/partial)は、再利用可能なビューの部分を管理することで、コードの重複を削減します。</span><span class="sxs-lookup"><span data-stu-id="2011a-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="2011a-122">たとえば、ブログ Web サイトで複数のビューに表示される作成者の略歴に、部分ビューが役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2011a-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="2011a-123">作成者の略歴は、通常のビュー コンテンツで、Web ページのコンテンツを生成するためにコードを実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2011a-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="2011a-124">作成者の略歴コンテンツは、モデル バインディングだけでビューで使用することができるため、この種類のコンテンツには部分ビューを使用するのが最適です。</span><span class="sxs-lookup"><span data-stu-id="2011a-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="2011a-125">[ビュー コンポーネント](xref:mvc/views/view-components)は、コードの繰り返しを削減できる点は部分ビューと似ていますが、Web ページをレンダリングするために、コードをサーバーで実行する必要があるビュー コンテンツに適しています。</span><span class="sxs-lookup"><span data-stu-id="2011a-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="2011a-126">ビュー コンポーネントは、レンダリングされたコンテンツで、Web サイトのショッピング カートのように、データベースとの対話を必要とする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="2011a-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="2011a-127">ビュー コンポーネントは、Web ページの出力を生成するためのモデル バインディングに限定されるものではありません。</span><span class="sxs-lookup"><span data-stu-id="2011a-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="2011a-128">ビューを使用するメリット</span><span class="sxs-lookup"><span data-stu-id="2011a-128">Benefits of using views</span></span>

<span data-ttu-id="2011a-129">ビューは、ユーザー インターフェイス マークアップをアプリの他の部分から分離して、MVC アプリ内で[懸念事項の分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)を確立するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2011a-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="2011a-130">SoC 設計に従うことで、アプリをモジュール化することができます。これにより次のような複数のメリットがもたらされます。</span><span class="sxs-lookup"><span data-stu-id="2011a-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="2011a-131">より効率的に整理されるため、アプリの維持が容易になります。</span><span class="sxs-lookup"><span data-stu-id="2011a-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="2011a-132">ビューは通常、アプリの機能によってグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="2011a-133">これにより、機能を使用する際に、関連するビューが見つけやすくなります。</span><span class="sxs-lookup"><span data-stu-id="2011a-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="2011a-134">アプリの部分は弱く結合されています。</span><span class="sxs-lookup"><span data-stu-id="2011a-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="2011a-135">ビジネス ロジックとデータ アクセス コンポーネントと切り離して、アプリのビューをビルドおよび更新できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="2011a-136">アプリの他の部分を更新しなくても、アプリのビューを変更できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="2011a-137">ビューは個別の単位であるため、アプリのユーザー インターフェイス部分をより簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="2011a-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="2011a-138">より効率的に整理されるため、ユーザー インターフェイスのセクションを誤って繰り返す可能性が低くなります。</span><span class="sxs-lookup"><span data-stu-id="2011a-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="2011a-139">ビューの作成</span><span class="sxs-lookup"><span data-stu-id="2011a-139">Creating a view</span></span>

<span data-ttu-id="2011a-140">コントローラーに固有のビューは、*Views/[ControllerName]* フォルダー内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="2011a-141">コントローラー間で共有されるビューは、*Views/Shared* フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="2011a-142">ビューを作成するには、新しいファイルを追加し、このファイルに、関連付けられたコントローラー アクションと同じ名前にファイル拡張子 *.cshtml* を付けた名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2011a-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="2011a-143">*ホーム* コントローラーで、*About* アクションに対応するビューを作成するには、*Views/Home* フォルダー内に *About.cshtml* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2011a-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="2011a-144">*Razor* マークアップは、`@` 記号で始まります。</span><span class="sxs-lookup"><span data-stu-id="2011a-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="2011a-145">中かっこ ([) で始まる ](xref:mvc/views/razor#razor-code-blocks)Razor コード ブロック`{ ... }`内に C# コードを配置して、C# ステートメントを実行します。</span><span class="sxs-lookup"><span data-stu-id="2011a-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="2011a-146">例として、上に示されている `ViewData["Title"]` への "About" の割り当てを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2011a-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="2011a-147">`@` 記号を使用して値を参照するだけで、HTML 内に値を表示することができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="2011a-148">上記の `<h2>` 要素と `<h3>` 要素のコンテンツを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2011a-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="2011a-149">上記のビュー コンテンツは、ユーザーにレンダリングされる Web ページ全体の一部のみを示しています。</span><span class="sxs-lookup"><span data-stu-id="2011a-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="2011a-150">残りのページのレイアウトとビューのその他の共通する側面は、他のビュー ファイルで指定されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="2011a-151">詳細については、「[Layout](xref:mvc/views/layout)」 (レイアウト) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2011a-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="2011a-152">コントローラーがビューを指定する方法</span><span class="sxs-lookup"><span data-stu-id="2011a-152">How controllers specify views</span></span>

<span data-ttu-id="2011a-153">ビューは通常、[ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult) の型である [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) としてアクションから返されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="2011a-154">アクション メソッドで `ViewResult` を作成して直接返すことはできますが、一般的には行われていません。</span><span class="sxs-lookup"><span data-stu-id="2011a-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="2011a-155">ほとんどのコントローラーは [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) から継承されるため、`View` ヘルパー メソッドを使って `ViewResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="2011a-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="2011a-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="2011a-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="2011a-157">このアクションが返されると、最後のセクションに表示されている *About.cshtml* ビューが、次の Web ページとしてレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="2011a-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Microsoft Edge ブラウザーでレンダリングされた About ページ](overview/_static/about-page.png)

<span data-ttu-id="2011a-159">`View` ヘルパー メソッドには複数のオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="2011a-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="2011a-160">必要に応じて以下を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-160">You can optionally specify:</span></span>

* <span data-ttu-id="2011a-161">返す明示的なビュー:</span><span class="sxs-lookup"><span data-stu-id="2011a-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="2011a-162">ビューに渡す[モデル](xref:mvc/models/model-binding):</span><span class="sxs-lookup"><span data-stu-id="2011a-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="2011a-163">ビューとモデルの両方:</span><span class="sxs-lookup"><span data-stu-id="2011a-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="2011a-164">ビューの検出</span><span class="sxs-lookup"><span data-stu-id="2011a-164">View discovery</span></span>

<span data-ttu-id="2011a-165">アクションがビューを返すときに、*ビューの検出*と呼ばれるプロセスが行われます。</span><span class="sxs-lookup"><span data-stu-id="2011a-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="2011a-166">このプロセスでは、ビュー名に基づいて、使用するビュー ファイルを決定します。</span><span class="sxs-lookup"><span data-stu-id="2011a-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="2011a-167">`View` メソッド (`return View();`) の既定の動作は、呼び出し元のアクション メソッドと同じ名前を持つビューを返すことです。</span><span class="sxs-lookup"><span data-stu-id="2011a-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="2011a-168">たとえば、コントローラーの*about* `ActionResult` メソッド名を使用して、 *about. cshtml*という名前のビューファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="2011a-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="2011a-169">ランタイムは最初に、ビューの *Views/[ControllerName]* フォルダーを調べます。</span><span class="sxs-lookup"><span data-stu-id="2011a-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="2011a-170">そこで一致するビューが見つからない場合は、ビューの *Shared* フォルダーを検索します。</span><span class="sxs-lookup"><span data-stu-id="2011a-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="2011a-171">`ViewResult` を使用して `return View();` を暗黙的に返すか、`View` を使用してビュー名を `return View("<ViewName>");` メソッドに明示的に渡すかは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="2011a-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="2011a-172">どちらの場合も、ビューの検出は、次の順序で一致するビュー ファイルを検索します。</span><span class="sxs-lookup"><span data-stu-id="2011a-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="2011a-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="2011a-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="2011a-174">*Views/Shared/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="2011a-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="2011a-175">ビュー名の代わりに、ビュー ファイル パスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="2011a-176">アプリのルートから始まる (必要に応じて"/" または "~/" で始まる) 絶対パスを使用する場合は、 *.cshtml* 拡張子を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2011a-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="2011a-177">*.cshtml* 拡張子なしで、相対パスを使用して異なるディレクトリ内にあるビューを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="2011a-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="2011a-178">`HomeController` の内部で、相対パスを使用して、*Manage* ビューの *Index* ビューを返すことができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="2011a-179">同様に、プレフィックス "./" を使用して、現在のコントローラーに固有のディレクトリを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="2011a-180">[部分ビュー](xref:mvc/views/partial)と[ビュー コンポーネント](xref:mvc/views/view-components)は、まったく同じではありませんが、同様の検出メカニズムを使用します。</span><span class="sxs-lookup"><span data-stu-id="2011a-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="2011a-181">カスタムの [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) を使用して、ビューをアプリ内に配置する方法の既定の規則をカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="2011a-182">ビューの検出は、ファイル名によるビュー ファイルの検出に依存しています。</span><span class="sxs-lookup"><span data-stu-id="2011a-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="2011a-183">基になるファイル システムが大文字と小文字を区別する場合は、ビューの名前も大文字と小文字を区別する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2011a-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="2011a-184">オペレーティング システム間の互換性のため、コントローラー、アクション名、関連するビュー フォルダー、ファイル名の間で、大文字と小文字の区別を一致させます。</span><span class="sxs-lookup"><span data-stu-id="2011a-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="2011a-185">大文字と小文字を区別するファイル システムを使用していて、ビュー ファイルが見つからないというエラーが発生する場合は、要求されたビュー ファイルと実際のビュー ファイルの名前の大文字と小文字が一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2011a-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="2011a-186">保守性を高め、わかりやすくするため、ビューのファイル構造を整理するためのベスト プラクティスに従って、コントローラー、アクション、およびビューのリレーションシップを反映します。</span><span class="sxs-lookup"><span data-stu-id="2011a-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="2011a-187">ビューにデータを渡す</span><span class="sxs-lookup"><span data-stu-id="2011a-187">Passing data to views</span></span>

<span data-ttu-id="2011a-188">ビューにデータを渡すには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="2011a-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="2011a-189">厳密に型指定されたデータ: viewmodel</span><span class="sxs-lookup"><span data-stu-id="2011a-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="2011a-190">弱く型指定されたデータ</span><span class="sxs-lookup"><span data-stu-id="2011a-190">Weakly typed data</span></span>
  * <span data-ttu-id="2011a-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="2011a-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="2011a-192">厳密に型指定されたデータ (viewmodel)</span><span class="sxs-lookup"><span data-stu-id="2011a-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="2011a-193">最も確実な方法は、ビューで[モデル](xref:mvc/models/model-binding)の型を指定することです。</span><span class="sxs-lookup"><span data-stu-id="2011a-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="2011a-194">このモデルは、一般的に *viewmodel* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2011a-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="2011a-195">viewmodel 型のインスタンスをアクションからビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="2011a-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="2011a-196">viewmodel を使用してデータをビューに渡すことで、ビューで*厳密な*型チェックを利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2011a-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="2011a-197">*厳密な型指定* (または*厳密に型指定された*) は、すべての変数および定数に明示的に定義された型 (`string`、`int`、`DateTime` など) があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="2011a-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="2011a-198">ビューで使用される型の妥当性は、コンパイル時にチェックされます。</span><span class="sxs-lookup"><span data-stu-id="2011a-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="2011a-199">[Visual Studio](https://visualstudio.microsoft.com) と [Visual Studio Code](https://code.visualstudio.com/) では、[IntelliSense](/visualstudio/ide/using-intellisense) と呼ばれる機能を使用して、厳密に型指定されたクラス メンバーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="2011a-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="2011a-200">viewmodel のプロパティを表示する場合は、viewmodel の変数名に続けてピリオド (`.`) を入力します。</span><span class="sxs-lookup"><span data-stu-id="2011a-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="2011a-201">これにより、エラーの少ないコードをより早く記述できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="2011a-202">`@model` ディレクティブを使用してモデルを指定します。</span><span class="sxs-lookup"><span data-stu-id="2011a-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="2011a-203">`@Model` を使用してモデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="2011a-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="2011a-204">モデルをビューに提供するため、コントローラーはモデルをパラメーターとして渡します。</span><span class="sxs-lookup"><span data-stu-id="2011a-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="2011a-205">ビューに提供できるモデルの型に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="2011a-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="2011a-206">Plain Old CLR Object (POCO) viewmodel を、動作 (メソッド) をほとんど定義せずに使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2011a-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="2011a-207">通常、viewmodel クラスは、*Models* フォルダーまたはアプリのルートにある個別の *ViewModels* フォルダーのいずれかに格納されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="2011a-208">上記の例で使用されている *Address* viewmodel は、*Address.cs* という名前のファイルに格納されている POCO viewmodel です。</span><span class="sxs-lookup"><span data-stu-id="2011a-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

<span data-ttu-id="2011a-209">viewmodel 型とビジネス モデル型の両方に同じクラスを使用することを妨げるものはありません。</span><span class="sxs-lookup"><span data-stu-id="2011a-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="2011a-210">しかし、別のモデルを使用することで、ビジネス ロジックとアプリのデータ アクセス部分からは独立して、ビューを変えることができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="2011a-211">モデルと viewmodel を分離することで、モデルが[モデル バインディング](xref:mvc/models/model-binding)と[検証](xref:mvc/models/validation)を使用する際に、ユーザーによってアプリに送信されるデータに対してセキュリティ上の利点ももたらされます。</span><span class="sxs-lookup"><span data-stu-id="2011a-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="2011a-212">弱く型指定されたデータ (ViewData、ViewData 属性、ViewBag)</span><span class="sxs-lookup"><span data-stu-id="2011a-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="2011a-213">`ViewBag`*は Razor Pages では使用できません。*</span><span class="sxs-lookup"><span data-stu-id="2011a-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="2011a-214">厳密に型指定されたビューに加え、ビューはデータの*弱く型指定された* (*緩く型指定された*ともいう) コレクションにもアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="2011a-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="2011a-215">厳密な型とは異なり、*弱い型* (または*緩い型*) は、使用するデータの型を明示的に宣言しないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="2011a-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="2011a-216">弱く型指定されたデータのコレクションを使用して、少量のデータをコントローラーとビュー間でやり取りすることができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="2011a-217">データをやり取りする相手</span><span class="sxs-lookup"><span data-stu-id="2011a-217">Passing data between a ...</span></span>                        | <span data-ttu-id="2011a-218">例</span><span class="sxs-lookup"><span data-stu-id="2011a-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="2011a-219">コントローラーとビュー</span><span class="sxs-lookup"><span data-stu-id="2011a-219">Controller and a view</span></span>                             | <span data-ttu-id="2011a-220">ドロップダウン リストにデータを読み込む。</span><span class="sxs-lookup"><span data-stu-id="2011a-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="2011a-221">ビューと[レイアウト ビュー](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="2011a-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="2011a-222">ビュー ファイルからレイアウト ビューの **\<title>** 要素の内容を設定する。</span><span class="sxs-lookup"><span data-stu-id="2011a-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="2011a-223">[部分ビュー](xref:mvc/views/partial)とビュー</span><span class="sxs-lookup"><span data-stu-id="2011a-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="2011a-224">ユーザーが要求した Web ページに基づいてデータを表示するウィジェット。</span><span class="sxs-lookup"><span data-stu-id="2011a-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="2011a-225">このコレクションは、コントローラーおよびビューで `ViewData` または `ViewBag` のいずれかのプロパティを通じて参照できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="2011a-226">`ViewData` プロパティは、弱く型指定されたオブジェクトのディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="2011a-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="2011a-227">`ViewBag` プロパティは、基になる `ViewData` コレクションに動的プロパティを提供する `ViewData` をラップするラッパーです。</span><span class="sxs-lookup"><span data-stu-id="2011a-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="2011a-228">注: キーの参照では、`ViewData` と `ViewBag`の両方で大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="2011a-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="2011a-229">`ViewData` および `ViewBag` は実行時に動的に解決されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="2011a-230">これらはコンパイル時の型チェックを提供していないため、どちらも viewmodel を使用する場合よりも一般的にエラーが発生しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="2011a-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="2011a-231">そのため、開発者の中には、`ViewData` および `ViewBag` の使用を最小限に抑えるか、まったく使用しない人もいます。</span><span class="sxs-lookup"><span data-stu-id="2011a-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="2011a-232">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="2011a-232">**ViewData**</span></span>

<span data-ttu-id="2011a-233">`ViewData` は [ キーを介してアクセスされる ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)ViewDataDictionary`string` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2011a-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="2011a-234">文字列データは、格納してキャストなしで直接使用できますが、特定の型を抽出するときには、他の `ViewData` オブジェクトの値をこれらの型にキャストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2011a-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="2011a-235">`ViewData` を使用して、データをコントローラーからビューとビュー内部 ([部分ビュー](xref:mvc/views/partial)および[レイアウト](xref:mvc/views/layout)を含む) に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="2011a-236">次の例では、1 つのアクションで `ViewData` を使用して、あいさつ文とアドレスに値を設定します。</span><span class="sxs-lookup"><span data-stu-id="2011a-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="2011a-237">ビューでデータを使用します。</span><span class="sxs-lookup"><span data-stu-id="2011a-237">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2011a-238">**ViewData 属性**</span><span class="sxs-lookup"><span data-stu-id="2011a-238">**ViewData attribute**</span></span>

<span data-ttu-id="2011a-239">[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) を使用するもう 1 つの方法は [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) です。</span><span class="sxs-lookup"><span data-stu-id="2011a-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="2011a-240">コントローラーや Razor ページのモデルのプロパティが `[ViewData]` 属性でマークされている場合、その値をディクショナリに格納してそこから読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="2011a-241">次の例では、Home コントローラーには `Title` でマークされた `[ViewData]` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2011a-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="2011a-242">`About` メソッドは、About ビューのタイトルを設定します。</span><span class="sxs-lookup"><span data-stu-id="2011a-242">The `About` method sets the title for the About view:</span></span>

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

<span data-ttu-id="2011a-243">About ビューでは、モデル プロパティとして `Title` プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2011a-243">In the About view, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="2011a-244">レイアウトでは、タイトルは ViewData ディクショナリから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2011a-244">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="2011a-245">**ViewBag**</span><span class="sxs-lookup"><span data-stu-id="2011a-245">**ViewBag**</span></span>

<span data-ttu-id="2011a-246">`ViewBag`*は Razor Pages では使用できません。*</span><span class="sxs-lookup"><span data-stu-id="2011a-246">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="2011a-247">`ViewBag` は、[ に格納されているオブジェクトへの動的アクセスを提供する ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)DynamicViewData`ViewData` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2011a-247">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="2011a-248">`ViewBag` はキャストを必要としないため、より簡単に使用できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-248">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="2011a-249">次の例は、上記の `ViewBag` を使用した時と同じ結果になるように、`ViewData` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="2011a-249">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="2011a-250">**ViewBag、ViewData を同時に使用する**</span><span class="sxs-lookup"><span data-stu-id="2011a-250">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="2011a-251">`ViewBag`*は Razor Pages では使用できません。*</span><span class="sxs-lookup"><span data-stu-id="2011a-251">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="2011a-252">`ViewData` と `ViewBag` は基になる同じ `ViewData` コレクションを参照しているため、値を読み書きするときに、`ViewData` と `ViewBag` の両方を使用して、それらを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="2011a-252">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="2011a-253">`ViewBag`About.cshtml`ViewData` ビューの一番上で、 *を使用してタイトルを設定し、* を使用して説明を設定します。</span><span class="sxs-lookup"><span data-stu-id="2011a-253">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="2011a-254">プロパティを読み取りますが、`ViewData` と `ViewBag` の使用を反転します。</span><span class="sxs-lookup"><span data-stu-id="2011a-254">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="2011a-255">*_Layout.cshtml*ファイルで、`ViewData` を使用してタイトルを取得し、`ViewBag` を使用して説明を取得します。</span><span class="sxs-lookup"><span data-stu-id="2011a-255">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="2011a-256">文字列には `ViewData` のキャストを必要としないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="2011a-256">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="2011a-257">キャストせずに `@ViewData["Title"]` を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-257">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="2011a-258">`ViewData` と `ViewBag` の両方を同時に使用することは、プロパティの読み取りと書き込みを組み合わせることと同様に可能です。</span><span class="sxs-lookup"><span data-stu-id="2011a-258">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="2011a-259">次のマークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="2011a-259">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="2011a-260">**ViewData と ViewBag の相違点の概要**</span><span class="sxs-lookup"><span data-stu-id="2011a-260">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="2011a-261">`ViewBag` は Razor ページでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="2011a-261">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="2011a-262">[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) から派生するため、`ContainsKey`、`Add`、`Remove`、`Clear` などの役に立つディクショナリ プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="2011a-262">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="2011a-263">ディクショナリ内のキーは文字列なので、空白が許可されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-263">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="2011a-264">例: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="2011a-264">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="2011a-265">`string` を使用するには、ビューで `ViewData` 以外のすべての型をキャストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2011a-265">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="2011a-266">[DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) から派生するため、ドット表記 (`@ViewBag.SomeKey = <value or object>`) を使用して動的プロパティを作成できます。キャストは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2011a-266">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="2011a-267">`ViewBag` の構文は、コントローラーとビューへの追加を高速化します。</span><span class="sxs-lookup"><span data-stu-id="2011a-267">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="2011a-268">null 値のチェックを簡素化します。</span><span class="sxs-lookup"><span data-stu-id="2011a-268">Simpler to check for null values.</span></span> <span data-ttu-id="2011a-269">例: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="2011a-269">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="2011a-270">**ViewData または ViewBag を使用する場合**</span><span class="sxs-lookup"><span data-stu-id="2011a-270">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="2011a-271">コントローラーとビュー間で少量のデータを渡すには、`ViewData` と `ViewBag` はどちらも等しく有効な方法です。</span><span class="sxs-lookup"><span data-stu-id="2011a-271">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="2011a-272">どちらを使用するかは、任意で選択できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-272">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="2011a-273">`ViewData` オブジェクトと `ViewBag` オブジェクトを組み合わせることはできますが、1 つの方法を一貫して使用した方が、コードが読みやすくなり、維持も容易になります。</span><span class="sxs-lookup"><span data-stu-id="2011a-273">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="2011a-274">どちらの方法も実行時に動的に解決されるため、実行時エラーが発生しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="2011a-274">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="2011a-275">開発チームの中には、これらを避けているところもあります。</span><span class="sxs-lookup"><span data-stu-id="2011a-275">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="2011a-276">動的ビュー</span><span class="sxs-lookup"><span data-stu-id="2011a-276">Dynamic views</span></span>

<span data-ttu-id="2011a-277">`@model` を使用してモデル型は宣言しないが、渡されるモデル インスタンス (`return View(Address);` など) があるビューは、インスタンスのプロパティを動的に参照できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-277">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="2011a-278">この機能は柔軟性を提供しますが、コンパイルの保護や IntelliSense は提供していません。</span><span class="sxs-lookup"><span data-stu-id="2011a-278">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="2011a-279">プロパティが存在しない場合は、実行時に Web ページの生成が失敗します。</span><span class="sxs-lookup"><span data-stu-id="2011a-279">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="2011a-280">ビューのその他の機能</span><span class="sxs-lookup"><span data-stu-id="2011a-280">More view features</span></span>

<span data-ttu-id="2011a-281">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)は、既存の HTML タグへのサーバー側の動作の追加を容易にします。</span><span class="sxs-lookup"><span data-stu-id="2011a-281">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="2011a-282">タグ ヘルパーを使用すると、ビュー内でカスタム コードやヘルパーを記述する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="2011a-282">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="2011a-283">タグ ヘルパーは、HTML 要素に属性として適用され、タグ ヘルパーを処理できないエディターでは無視されます。</span><span class="sxs-lookup"><span data-stu-id="2011a-283">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="2011a-284">これにより、さまざまなツールでビューのマークアップの編集およびレンダリングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="2011a-284">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="2011a-285">カスタムの HTML マークアップの生成は、さまざまな組み込み HTML ヘルパーを使って行えます。</span><span class="sxs-lookup"><span data-stu-id="2011a-285">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="2011a-286">より複雑なユーザー インターフェイス ロジックは、[ビュー コンポーネント](xref:mvc/views/view-components)で処理できます。</span><span class="sxs-lookup"><span data-stu-id="2011a-286">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="2011a-287">ビュー コンポーネントは、コントローラーとビューが提供しているのと同じ SoC を提供します。</span><span class="sxs-lookup"><span data-stu-id="2011a-287">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="2011a-288">これにより、共通のユーザー インターフェイス要素で使用されるデータを扱うアクションおよびビューの必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="2011a-288">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="2011a-289">ASP.NET Core の他の多くの側面と同様に、ビューは、サービスを[ビューに挿入する](xref:fundamentals/dependency-injection)ことを許可する、[依存性の注入](xref:mvc/views/dependency-injection)をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2011a-289">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
