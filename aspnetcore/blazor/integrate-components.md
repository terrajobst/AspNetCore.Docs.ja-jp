---
title: ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218935"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="9b3b8-103">ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="9b3b8-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="9b3b8-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9b3b8-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9b3b8-105">Razor コンポーネントは、Razor Pages と MVC アプリに統合できます。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="9b3b8-106">ページまたはビューがレンダリングされるときには、コンポーネントを同時に事前レンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="9b3b8-107">ページとビューでコンポーネントを使用するためにアプリを準備する</span><span class="sxs-lookup"><span data-stu-id="9b3b8-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="9b3b8-108">既存の Razor Pages や MVC アプリでは、Razor コンポーネントをページとビューに統合できます。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="9b3b8-109">アプリのレイアウト ファイル ( *_Layout.cshtml*) で次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="9b3b8-110">次の `<base>` タグを `<head>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="9b3b8-111">前の例の `href` 値 (*アプリ ベースのパス*) は、アプリがルート URL パス (`/`) に置かれていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="9b3b8-112">アプリがサブアプリケーションになっている場合は、記事  *の「* アプリのベース パス<xref:host-and-deploy/blazor/index#app-base-path>」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="9b3b8-113">*_Layout.cshtml* ファイルは、Razor Pages アプリの *Pages/Shared* フォルダーまたは MVC アプリの *Views/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="9b3b8-114">`<script>`blazor.server.js*スクリプトの* タグを、終了 `</body>` タグの直前に追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="9b3b8-115">フレームワークによって *blazor.server.js* スクリプトがアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="9b3b8-116">手動でアプリにスクリプトを追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="9b3b8-117">次の内容を含む *_Imports.razor* ファイルをプロジェクトのルート フォルダーに追加します (最後の名前空間 `MyAppNamespace` をアプリの名前空間に変更します)。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="9b3b8-118">`Startup.ConfigureServices` で、Blazor Server サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="9b3b8-119">`Startup.Configure` で、Blazor Hub エンドポイントを `app.UseEndpoints` に追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="9b3b8-120">コンポーネントを任意のページまたはビューに統合します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-120">Integrate components into any page or view.</span></span> <span data-ttu-id="9b3b8-121">詳細については、「[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="9b3b8-122">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="9b3b8-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="9b3b8-123">*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*</span><span class="sxs-lookup"><span data-stu-id="9b3b8-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="9b3b8-124">Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="9b3b8-125">「[ページとビューでコンポーネントを使用するためにアプリを準備する](#prepare-the-app-to-use-components-in-pages-and-views)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="9b3b8-126">次の内容の *App.razor* ファイルをプロジェクト ルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-126">Add an *App.razor* file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="9b3b8-127">次の内容の *_Host.cshtml* ファイルを *Pages* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="9b3b8-128">コンポーネントは、そのレイアウトで共有される *_Layout.cshtml* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="9b3b8-129">*_Host.cshtml* ページの優先度が低いルートを、`Startup.Configure` 内のエンドポイント構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="9b3b8-130">ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-130">Add routable components to the app.</span></span> <span data-ttu-id="9b3b8-131">(例:</span><span class="sxs-lookup"><span data-stu-id="9b3b8-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="9b3b8-132">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="9b3b8-133">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="9b3b8-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="9b3b8-134">*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*</span><span class="sxs-lookup"><span data-stu-id="9b3b8-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="9b3b8-135">MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="9b3b8-136">「[ページとビューでコンポーネントを使用するためにアプリを準備する](#prepare-the-app-to-use-components-in-pages-and-views)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="9b3b8-137">次の内容の *App.razor* ファイルを、プロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="9b3b8-138">次の内容の *_Host.cshtml* ファイルを *Views/Home* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="9b3b8-139">コンポーネントは、そのレイアウトで共有される *_Layout.cshtml* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="9b3b8-140">Home コントローラーにアクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="9b3b8-141">*内のエンドポイント構成に*_Host.cshtml`Startup.Configure` ビューを返すコントローラー アクションのために、優先度が低いルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="9b3b8-142">*Pages* フォルダーを作成し、アプリにルーティング可能なコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="9b3b8-143">(例:</span><span class="sxs-lookup"><span data-stu-id="9b3b8-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="9b3b8-144">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="9b3b8-145">コンポーネントの名前空間</span><span class="sxs-lookup"><span data-stu-id="9b3b8-145">Component namespaces</span></span>

<span data-ttu-id="9b3b8-146">カスタム フォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を、ページまたはビューのいずれかに追加するか、 *_ViewImports.cshtml* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="9b3b8-147">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-147">In the following example:</span></span>

* <span data-ttu-id="9b3b8-148">`MyAppNamespace` をアプリの名前空間に変更します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="9b3b8-149">コンポーネントを保持するために *Components* という名前のフォルダーを使用していない場合は、`Components` を、コンポーネントが置かれているフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="9b3b8-150">*_ViewImports.cshtml* ファイルは、Razor Pages アプリの *Pages* フォルダーまたは MVC アプリの *Views* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="9b3b8-151">詳細については、「<xref:blazor/components#import-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="9b3b8-152">ページまたはビューからコンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="9b3b8-152">Render components from a page or view</span></span>

<span data-ttu-id="9b3b8-153">*これは、コンポーネントをユーザー要求から直接ルーティングできないページまたはビューにコンポーネントを追加することに関係するセクションです。*</span><span class="sxs-lookup"><span data-stu-id="9b3b8-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="9b3b8-154">ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-154">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

<span data-ttu-id="9b3b8-155">コンポーネントがどのようにレンダリングされるか、コンポーネントの状態、および `Component` タグ ヘルパーの詳細については、以下の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3b8-155">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
