---
title: ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータバインディングのシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: de1a37ffd9456c956e3d84fcc69431ecb794513c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453212"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="2564f-103">ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="2564f-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="2564f-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2564f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2564f-105">Razor コンポーネントは Razor Pages と MVC アプリに統合できます。</span><span class="sxs-lookup"><span data-stu-id="2564f-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="2564f-106">ページまたはビューが表示されると、コンポーネントを同時に prerendered することができます。</span><span class="sxs-lookup"><span data-stu-id="2564f-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a><span data-ttu-id="2564f-107">ページおよびビューでコンポーネントを使用するようにアプリを準備する</span><span class="sxs-lookup"><span data-stu-id="2564f-107">Prepare the app to use components in pages and views</span></span>

<span data-ttu-id="2564f-108">既存の Razor Pages または MVC アプリでは、Razor コンポーネントをページとビューに統合できます。</span><span class="sxs-lookup"><span data-stu-id="2564f-108">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="2564f-109">アプリのレイアウトファイル ( *_Layout*) で、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2564f-109">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="2564f-110">次の `<base>` タグを `<head>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-110">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="2564f-111">前の例の `href` 値 (*アプリのベースパス*) は、アプリがルート URL パス (`/`) に存在することを前提としています。</span><span class="sxs-lookup"><span data-stu-id="2564f-111">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="2564f-112">アプリがサブアプリケーションである場合は、<xref:host-and-deploy/blazor/index#app-base-path> に関する記事の「*アプリの基本パス*」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="2564f-112">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="2564f-113">*_Layout*のファイルは、MVC アプリの Razor Pages アプリまたは*Views/shared*フォルダー内の*Pages/shared*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="2564f-113">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="2564f-114">Blazor スクリプトの `<script>` タグを、終了 `</body>` タグの直前に追加*し*ます。</span><span class="sxs-lookup"><span data-stu-id="2564f-114">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="2564f-115">フレームワークによって、 *blazor*スクリプトがアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="2564f-115">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="2564f-116">アプリにスクリプトを手動で追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2564f-116">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="2564f-117">次の内容を使用して、プロジェクトのルートフォルダーに *_Imports razor*ファイルを追加します (最後の名前空間、`MyAppNamespace`をアプリの名前空間に変更します)。</span><span class="sxs-lookup"><span data-stu-id="2564f-117">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="2564f-118">`Startup.ConfigureServices`で、Blazor Server サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="2564f-118">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="2564f-119">`Startup.Configure`で、`app.UseEndpoints`に Blazor Hub エンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-119">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="2564f-120">コンポーネントを任意のページまたはビューに統合します。</span><span class="sxs-lookup"><span data-stu-id="2564f-120">Integrate components into any page or view.</span></span> <span data-ttu-id="2564f-121">詳細については、「[ページまたはビューからのコンポーネントのレンダリング](#render-components-from-a-page-or-view)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2564f-121">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="2564f-122">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="2564f-122">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="2564f-123">*このセクションでは、ユーザー要求から直接ルーティング可能なコンポーネントを追加する方法について説明します。*</span><span class="sxs-lookup"><span data-stu-id="2564f-123">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="2564f-124">Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには:</span><span class="sxs-lookup"><span data-stu-id="2564f-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="2564f-125">「[ページとビューでコンポーネントを使用するようにアプリを準備する](#prepare-the-app-to-use-components-in-pages-and-views)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="2564f-125">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="2564f-126">次のコンテンツを使用して、プロジェクトルートに*アプリケーションの razor*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-126">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="2564f-127">次の内容を含む *_Host*ファイルを*Pages*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="2564f-128">コンポーネントは、そのレイアウトのために共有 *_Layout*ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="2564f-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="2564f-129">`Startup.Configure`のエンドポイント構成に *_Host*の、次のように低優先度のルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="2564f-130">ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-130">Add routable components to the app.</span></span> <span data-ttu-id="2564f-131">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2564f-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="2564f-132">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2564f-132">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="2564f-133">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="2564f-133">Use routable components in an MVC app</span></span>

<span data-ttu-id="2564f-134">*このセクションでは、ユーザー要求から直接ルーティング可能なコンポーネントを追加する方法について説明します。*</span><span class="sxs-lookup"><span data-stu-id="2564f-134">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="2564f-135">MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2564f-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="2564f-136">「[ページとビューでコンポーネントを使用するようにアプリを準備する](#prepare-the-app-to-use-components-in-pages-and-views)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="2564f-136">Follow the guidance in the [Prepare the app to use components in pages and views](#prepare-the-app-to-use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="2564f-137">次の内容を含むプロジェクトのルートに、*アプリケーションの razor*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="2564f-138">次の内容を含む *_Host*ファイルを*Views/Home*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="2564f-139">コンポーネントは、そのレイアウトのために共有 *_Layout*ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="2564f-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="2564f-140">Home コントローラーにアクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="2564f-141">`Startup.Configure`のエンドポイント構成に *_Host*のビューを返すコントローラーアクションの優先度の低いルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="2564f-142">*ページ*フォルダーを作成し、ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="2564f-143">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2564f-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="2564f-144">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2564f-144">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="2564f-145">コンポーネントの名前空間</span><span class="sxs-lookup"><span data-stu-id="2564f-145">Component namespaces</span></span>

<span data-ttu-id="2564f-146">カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間をページ/ビューまたは *_ViewImports*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2564f-146">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2564f-147">次の例では</span><span class="sxs-lookup"><span data-stu-id="2564f-147">In the following example:</span></span>

* <span data-ttu-id="2564f-148">`MyAppNamespace` をアプリの名前空間に変更します。</span><span class="sxs-lookup"><span data-stu-id="2564f-148">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="2564f-149">*コンポーネントという名前*のフォルダーを使用してコンポーネントを保持していない場合は、コンポーネントが存在するフォルダーに `Components` を変更します。</span><span class="sxs-lookup"><span data-stu-id="2564f-149">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="2564f-150">*_ViewImports*のファイルは、Razor Pages アプリの*Pages*フォルダーまたは MVC アプリの*Views*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="2564f-150">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="2564f-151">詳細については、<xref:blazor/components#import-components> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2564f-151">For more information, see <xref:blazor/components#import-components>.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="2564f-152">ページまたはビューからのコンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="2564f-152">Render components from a page or view</span></span>

<span data-ttu-id="2564f-153">*このセクションでは、コンポーネントをユーザー要求から直接ルーティングできないページまたはビューに追加する方法について説明します。*</span><span class="sxs-lookup"><span data-stu-id="2564f-153">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="2564f-154">ページまたはビューからコンポーネントを表示するには、`Component` タグヘルパーを使用します。</span><span class="sxs-lookup"><span data-stu-id="2564f-154">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="2564f-155">パラメーターの型は、JSON シリアル化可能である必要があります。これは通常、型が既定のコンストラクターと設定可能なプロパティを持つ必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="2564f-155">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="2564f-156">たとえば、`IncrementAmount` の型は `int`であるため、`IncrementAmount` の値を指定できます。これは、JSON シリアライザーによってサポートされるプリミティブ型です。</span><span class="sxs-lookup"><span data-stu-id="2564f-156">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="2564f-157">コンポーネントの `RenderMode` を構成します。</span><span class="sxs-lookup"><span data-stu-id="2564f-157">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="2564f-158">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="2564f-158">Is prerendered into the page.</span></span>
* <span data-ttu-id="2564f-159">は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2564f-159">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="2564f-160">Description</span><span class="sxs-lookup"><span data-stu-id="2564f-160">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="2564f-161">コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="2564f-161">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2564f-162">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2564f-162">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="2564f-163">Blazor サーバーアプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="2564f-163">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2564f-164">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="2564f-164">Output from the component isn't included.</span></span> <span data-ttu-id="2564f-165">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2564f-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="2564f-166">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="2564f-166">Renders the component into static HTML.</span></span> |

<span data-ttu-id="2564f-167">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="2564f-167">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="2564f-168">コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="2564f-168">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="2564f-169">コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="2564f-169">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="2564f-170">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2564f-170">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="2564f-171">コンポーネントのレンダリング方法、コンポーネントの状態、および `Component` タグヘルパーの詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2564f-171">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
