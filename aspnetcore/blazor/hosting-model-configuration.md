---
title: ASP.NET Core Blazor ホスティングモデルの構成
author: guardrex
description: Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティングモデルの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 91dd1aa32bb5165845eb4794377a50ccbd01adc3
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77214941"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="1d4b1-103">ASP.NET Core Blazor ホスティングモデルの構成</span><span class="sxs-lookup"><span data-stu-id="1d4b1-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="1d4b1-104">[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1d4b1-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="1d4b1-105">この記事では、ホスティングモデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-105">This article covers hosting model configuration.</span></span>

<!-- For future use:

## Blazor WebAssembly

-->

## <a name="blazor-server"></a><span data-ttu-id="1d4b1-106">Blazor サーバー</span><span class="sxs-lookup"><span data-stu-id="1d4b1-106">Blazor Server</span></span>

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="1d4b1-107">Razor コンポーネントを Razor Pages および MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="1d4b1-107">Integrate Razor components into Razor Pages and MVC apps</span></span>

#### <a name="use-components-in-pages-and-views"></a><span data-ttu-id="1d4b1-108">ページおよびビューでのコンポーネントの使用</span><span class="sxs-lookup"><span data-stu-id="1d4b1-108">Use components in pages and views</span></span>

<span data-ttu-id="1d4b1-109">既存の Razor Pages または MVC アプリでは、Razor コンポーネントをページとビューに統合できます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-109">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="1d4b1-110">アプリのレイアウトファイル ( *_Layout*) で、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-110">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="1d4b1-111">次の `<base>` タグを `<head>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-111">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="1d4b1-112">前の例の `href` 値 (*アプリのベースパス*) は、アプリがルート URL パス (`/`) に存在することを前提としています。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-112">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="1d4b1-113">アプリがサブアプリケーションである場合は、<xref:host-and-deploy/blazor/index#app-base-path> に関する記事の「*アプリの基本パス*」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-113">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="1d4b1-114">*_Layout*のファイルは、MVC アプリの Razor Pages アプリまたは*Views/shared*フォルダー内の*Pages/shared*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-114">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="1d4b1-115">*Blazor*スクリプトの `<script>` タグを、終了 `</body>` タグの直前に追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-115">Add a `<script>` tag for the *blazor.server.js* script right before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="1d4b1-116">フレームワークによって、 *blazor*スクリプトがアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-116">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="1d4b1-117">アプリにスクリプトを手動で追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-117">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="1d4b1-118">次の内容を使用して、プロジェクトのルートフォルダーに *_Imports razor*ファイルを追加します (最後の名前空間、`MyAppNamespace`をアプリの名前空間に変更します)。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-118">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="1d4b1-119">`Startup.ConfigureServices`で、Blazor Server サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-119">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="1d4b1-120">`Startup.Configure`で、`app.UseEndpoints`に Blazor Hub エンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-120">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="1d4b1-121">コンポーネントを任意のページまたはビューに統合します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-121">Integrate components into any page or view.</span></span> <span data-ttu-id="1d4b1-122">詳細については、<xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> の記事の「*コンポーネントを Razor Pages と MVC アプリに統合*する」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-122">For more information, see the *Integrate components into Razor Pages and MVC apps* section of the <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> article.</span></span>

#### <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="1d4b1-123">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="1d4b1-123">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="1d4b1-124">Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには:</span><span class="sxs-lookup"><span data-stu-id="1d4b1-124">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="1d4b1-125">「[ページおよびビューでコンポーネントを使用する](#use-components-in-pages-and-views)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-125">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="1d4b1-126">次の内容を含むプロジェクトのルートに、*アプリケーションの razor*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-126">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="1d4b1-127">次の内容を含む *_Host*ファイルを*Pages*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-127">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="1d4b1-128">コンポーネントは、そのレイアウトのために共有 *_Layout*ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-128">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="1d4b1-129">`Startup.Configure`のエンドポイント構成に *_Host*の、次のように低優先度のルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-129">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="1d4b1-130">ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-130">Add routable components to the app.</span></span> <span data-ttu-id="1d4b1-131">例 :</span><span class="sxs-lookup"><span data-stu-id="1d4b1-131">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="1d4b1-132">カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を*Pages/_ViewImports cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-132">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Pages/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="1d4b1-133">詳細については、<xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-133">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

#### <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="1d4b1-134">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="1d4b1-134">Use routable components in an MVC app</span></span>

<span data-ttu-id="1d4b1-135">MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-135">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="1d4b1-136">「[ページおよびビューでコンポーネントを使用する](#use-components-in-pages-and-views)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-136">Follow the guidance in the [Use components in pages and views](#use-components-in-pages-and-views) section.</span></span>

1. <span data-ttu-id="1d4b1-137">次の内容を含むプロジェクトのルートに、*アプリケーションの razor*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-137">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="1d4b1-138">次の内容を含む *_Host*ファイルを*Views/Home*フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-138">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="1d4b1-139">コンポーネントは、そのレイアウトのために共有 *_Layout*ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-139">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

1. <span data-ttu-id="1d4b1-140">Home コントローラーにアクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-140">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="1d4b1-141">`Startup.Configure`のエンドポイント構成に *_Host*のビューを返すコントローラーアクションの優先度の低いルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-141">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="1d4b1-142">*ページ*フォルダーを作成し、ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-142">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="1d4b1-143">例 :</span><span class="sxs-lookup"><span data-stu-id="1d4b1-143">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   <span data-ttu-id="1d4b1-144">カスタムフォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を*Views/_ViewImports cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-144">When using a custom folder to hold the app's components, add the namespace representing the folder to the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="1d4b1-145">詳細については、<xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-145">For more information, see <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="1d4b1-146">UI の接続状態を反映します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-146">Reflect the connection state in the UI</span></span>

<span data-ttu-id="1d4b1-147">クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-147">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="1d4b1-148">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-148">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="1d4b1-149">UI をカスタマイズするには、 *_Host*の `<body>` の `components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-149">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="1d4b1-150">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-150">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="1d4b1-151">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="1d4b1-151">CSS class</span></span>                       | <span data-ttu-id="1d4b1-152">&hellip; を示します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-152">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="1d4b1-153">失われた接続。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-153">A lost connection.</span></span> <span data-ttu-id="1d4b1-154">クライアントが再接続しようとしています。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-154">The client is attempting to reconnect.</span></span> <span data-ttu-id="1d4b1-155">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-155">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="1d4b1-156">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-156">An active connection is re-established to the server.</span></span> <span data-ttu-id="1d4b1-157">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-157">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="1d4b1-158">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-158">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="1d4b1-159">再接続を試みるには、`window.Blazor.reconnect()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-159">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="1d4b1-160">再接続は拒否されました。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-160">Reconnection rejected.</span></span> <span data-ttu-id="1d4b1-161">サーバーに到達したが接続を拒否したため、サーバー上のユーザーの状態が失われています。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-161">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="1d4b1-162">アプリを再度読み込むには、`location.reload()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-162">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="1d4b1-163">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-163">This connection state may result when:</span></span><ul><li><span data-ttu-id="1d4b1-164">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-164">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="1d4b1-165">サーバーがユーザーの状態を削除するのに十分な時間、クライアントが接続されていません。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-165">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="1d4b1-166">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-166">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="1d4b1-167">サーバーが再起動されたか、アプリのワーカープロセスがリサイクルされています。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-167">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="1d4b1-168">表示モード</span><span class="sxs-lookup"><span data-stu-id="1d4b1-168">Render mode</span></span>

<span data-ttu-id="1d4b1-169">Blazor サーバーアプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に作成するために既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-169">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="1d4b1-170">これは *_Host. cshtml* Razor ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-170">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="1d4b1-171">コンポーネントの `RenderMode` を構成します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-171">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="1d4b1-172">ページに prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-172">Is prerendered into the page.</span></span>
* <span data-ttu-id="1d4b1-173">は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-173">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="1d4b1-174">説明</span><span class="sxs-lookup"><span data-stu-id="1d4b1-174">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="1d4b1-175">コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-175">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1d4b1-176">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-176">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="1d4b1-177">Blazor サーバーアプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-177">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1d4b1-178">コンポーネントからの出力は含まれていません。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-178">Output from the component isn't included.</span></span> <span data-ttu-id="1d4b1-179">ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-179">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="1d4b1-180">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-180">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1d4b1-181">静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-181">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="1d4b1-182">Razor ページとビューからのステートフル対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="1d4b1-182">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="1d4b1-183">ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-183">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="1d4b1-184">ページまたはビューが表示される場合:</span><span class="sxs-lookup"><span data-stu-id="1d4b1-184">When the page or view renders:</span></span>

* <span data-ttu-id="1d4b1-185">コンポーネントは、ページまたはビューと prerendered ます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-185">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="1d4b1-186">プリレンダリングに使用される初期コンポーネントの状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-186">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="1d4b1-187">SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-187">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="1d4b1-188">次の Razor ページでは、`Counter` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-188">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="1d4b1-189">Razor ページとビューからの非対話型コンポーネントのレンダリング</span><span class="sxs-lookup"><span data-stu-id="1d4b1-189">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="1d4b1-190">次の Razor ページでは、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-190">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="1d4b1-191">`MyComponent` は静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-191">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="1d4b1-192">Blazor Server アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="1d4b1-192">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="1d4b1-193">場合によっては、Blazor サーバーアプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-193">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="1d4b1-194">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-194">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="1d4b1-195">*Pages/_Host cshtml*ファイルで SignalR クライアントを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-195">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="1d4b1-196">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-196">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="1d4b1-197">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="1d4b1-197">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
