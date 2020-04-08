---
title: ASP.NET Core Blazor のルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、NavLink コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218896"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="66aa9-103">ASP.NET Core Blazor のルーティング</span><span class="sxs-lookup"><span data-stu-id="66aa9-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="66aa9-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="66aa9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="66aa9-105">Blazor アプリで、要求をルーティングする方法と、`NavLink` コンポーネントを使用して、ナビゲーション リンクを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="66aa9-106">ASP.NET Core エンドポイントのルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="66aa9-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="66aa9-107">Blazor サーバーは [ASP.NET Core エンドポイントのルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="66aa9-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="66aa9-108">ASP.NET Core アプリは、`MapBlazorHub` で `Startup.Configure` を使用して、対話型コンポーネントの着信接続を受け入れるように構成します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="66aa9-109">最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor サーバー アプリのサーバー側部分のホストとして機能します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="66aa9-110">通常、*ホスト* ページは、 *_Host.cshtml* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="66aa9-111">ホスト ファイルに指定されるルートは、ルート照合で低い優先順位で動作するため、*フォールバック ルート*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="66aa9-112">フォールバック ルートは、他のルートが一致しない場合に考慮されます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="66aa9-113">これにより、Blazor サーバー アプリと干渉することなく、他のコントローラーやページをアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="66aa9-114">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="66aa9-114">Route templates</span></span>

<span data-ttu-id="66aa9-115">`Router` コンポーネントでは、指定されたルートによる各コンポーネントへのルーティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="66aa9-116">`Router` コンポーネントは *App.razor* ファイルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-116">The `Router` component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="66aa9-117">*ディレクティブを含む*razor`@page` ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Components.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="66aa9-118">実行時に、`RouteView` コンポーネントは、</span><span class="sxs-lookup"><span data-stu-id="66aa9-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="66aa9-119">`RouteData` から、必要なパラメーターと共に `Router` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="66aa9-120">指定されたパラメーターを使用して、指定されたコンポーネントを、そのレイアウト (または任意の既定のレイアウト) でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="66aa9-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="66aa9-121">必要に応じて、レイアウト クラスで `DefaultLayout` パラメーターを指定して、レイアウトを指定しないコンポーネントに使用できます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="66aa9-122">既定の Blazor テンプレートでは、`MainLayout` コンポーネントを指定しています。</span><span class="sxs-lookup"><span data-stu-id="66aa9-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="66aa9-123">*MainLayout.razor* は、テンプレート プロジェクトの *Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="66aa9-124">レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66aa9-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="66aa9-125">コンポーネントには、複数のルート テンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="66aa9-126">次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute` に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="66aa9-127">URL が正しく解決されるように、アプリでは、`<base>` 属性に指定されているアプリのベース パス ( *) を使用して、その* wwwroot/index.html*ファイル (Blazor WebAssembly) または*Pages/_Host.cshtml`href` ファイル (Blazor Server) に `<base href="/">` タグを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="66aa9-128">詳細については、「<xref:host-and-deploy/blazor/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66aa9-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="66aa9-129">コンテンツが見つからないときにカスタム コンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="66aa9-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="66aa9-130">`Router` コンポーネントを使用すると、要求されたルートでコンテンツが見つからない場合に、アプリでカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="66aa9-131">*App.razor* ファイルで、`NotFound` コンポーネントの `Router` テンプレート パラメーターにカスタム コンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="66aa9-132">`<NotFound>` タグのコンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="66aa9-133">`NotFound` コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66aa9-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="66aa9-134">複数のアセンブリからコンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="66aa9-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="66aa9-135">`AdditionalAssemblies` パラメーターを使用して、`Router` コンポーネントで、ルーティング可能なコンポーネントを検索するときに考慮する追加のアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="66aa9-136">指定されたアセンブリは、`AppAssembly` に指定されたアセンブリに加えて考慮されます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="66aa9-137">次の例では、`Component1` は、参照されているクラス ライブラリに定義されているルーティング可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="66aa9-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="66aa9-138">次の `AdditionalAssemblies` の例では、`Component1` のルーティング サポートの結果を示しています。</span><span class="sxs-lookup"><span data-stu-id="66aa9-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="66aa9-139">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="66aa9-139">Route parameters</span></span>

<span data-ttu-id="66aa9-140">ルーターは、ルート パラメーターを使用して、同じ名前の対応するコンポーネント パラメーターを設定します (大文字と小文字は区別されません)。</span><span class="sxs-lookup"><span data-stu-id="66aa9-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="66aa9-141">省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="66aa9-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="66aa9-142">前の例では、2 つの `@page` ディレクティブが適用されています。</span><span class="sxs-lookup"><span data-stu-id="66aa9-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="66aa9-143">1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="66aa9-144">2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="66aa9-145">ルート制約</span><span class="sxs-lookup"><span data-stu-id="66aa9-145">Route constraints</span></span>

<span data-ttu-id="66aa9-146">ルート制約は、コンポーネントへのルート セグメントに型の一致を適用します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="66aa9-147">次の例で、`Users` コンポーネントへのルートは、次の場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="66aa9-148">要求 URL に `Id` ルート セグメントが存在する。</span><span class="sxs-lookup"><span data-stu-id="66aa9-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="66aa9-149">`Id` セグメントは整数 (`int`) である。</span><span class="sxs-lookup"><span data-stu-id="66aa9-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="66aa9-150">次の表に示すルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="66aa9-151">インバリアント カルチャと一致するルート制約については、表の下の警告で詳細を確認してください。</span><span class="sxs-lookup"><span data-stu-id="66aa9-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="66aa9-152">制約</span><span class="sxs-lookup"><span data-stu-id="66aa9-152">Constraint</span></span> | <span data-ttu-id="66aa9-153">例</span><span class="sxs-lookup"><span data-stu-id="66aa9-153">Example</span></span>           | <span data-ttu-id="66aa9-154">一致の例</span><span class="sxs-lookup"><span data-stu-id="66aa9-154">Example Matches</span></span>                                                                  | <span data-ttu-id="66aa9-155">インバリアント</span><span class="sxs-lookup"><span data-stu-id="66aa9-155">Invariant</span></span><br><span data-ttu-id="66aa9-156">カルチャ</span><span class="sxs-lookup"><span data-stu-id="66aa9-156">culture</span></span><br><span data-ttu-id="66aa9-157">一致</span><span class="sxs-lookup"><span data-stu-id="66aa9-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="66aa9-158">`true`、 `FALSE`</span><span class="sxs-lookup"><span data-stu-id="66aa9-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="66aa9-159">いいえ</span><span class="sxs-lookup"><span data-stu-id="66aa9-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="66aa9-160">`2016-12-31`、 `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="66aa9-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="66aa9-161">[はい]</span><span class="sxs-lookup"><span data-stu-id="66aa9-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="66aa9-162">`49.99`、 `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="66aa9-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="66aa9-163">[はい]</span><span class="sxs-lookup"><span data-stu-id="66aa9-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="66aa9-164">`1.234`、 `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="66aa9-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="66aa9-165">[はい]</span><span class="sxs-lookup"><span data-stu-id="66aa9-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="66aa9-166">`1.234`、 `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="66aa9-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="66aa9-167">[はい]</span><span class="sxs-lookup"><span data-stu-id="66aa9-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="66aa9-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`、 `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="66aa9-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="66aa9-169">いいえ</span><span class="sxs-lookup"><span data-stu-id="66aa9-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="66aa9-170">`123456789`、 `-123456789`</span><span class="sxs-lookup"><span data-stu-id="66aa9-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="66aa9-171">[はい]</span><span class="sxs-lookup"><span data-stu-id="66aa9-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="66aa9-172">`123456789`、 `-123456789`</span><span class="sxs-lookup"><span data-stu-id="66aa9-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="66aa9-173">[はい]</span><span class="sxs-lookup"><span data-stu-id="66aa9-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="66aa9-174">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="66aa9-175">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="66aa9-176">ドットを含む URL によるルーティング</span><span class="sxs-lookup"><span data-stu-id="66aa9-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="66aa9-177">Blazor サーバー アプリでは、 *_Host.cshtml* の既定のルートは `/` (`@page "/"`) です。</span><span class="sxs-lookup"><span data-stu-id="66aa9-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="66aa9-178">ドット (`.`) を含む要求 URL は、既定のルートによって照合されません。URL がファイルを要求しているように見えるためです。</span><span class="sxs-lookup"><span data-stu-id="66aa9-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="66aa9-179">Blazor アプリは、存在しない静的ファイルに対して "*404 見つかりません*" 応答 を返します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="66aa9-180">ドットを含むルートを使用するには、次のルート テンプレートを使用して *_Host.cshtml* を構成します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="66aa9-181">`"/{**path}"` テンプレートには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="66aa9-182">二重アスタリスクの*キャッチオール*構文 (`**`)。スラッシュ (`/`) をエンコードせずに複数のフォルダー境界をまたがるパスをキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="66aa9-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="66aa9-183">`path` ルート パラメーター名。</span><span class="sxs-lookup"><span data-stu-id="66aa9-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="66aa9-184">*キャッチオール* パラメーター構文 (`*`/`**`) は、Razor コンポーネント ( **.razor**) ではサポートされて*いません*。</span><span class="sxs-lookup"><span data-stu-id="66aa9-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="66aa9-185">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66aa9-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="66aa9-186">NavLink コンポーネント</span><span class="sxs-lookup"><span data-stu-id="66aa9-186">NavLink component</span></span>

<span data-ttu-id="66aa9-187">ナビゲーション リンクを作成するときは、HTML ハイパーリンク要素 (`NavLink`) の代わりに `<a>` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="66aa9-188">`NavLink` コンポーネントは `<a>` 要素のように動作しますが、`active` が現在の URL と一致するかどうかに基づいて `href` CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="66aa9-189">`active` クラスは、表示されているナビゲーション リンクの中でどのページがアクティブ ページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="66aa9-190">次の `NavMenu` コンポーネントでは、[ コンポーネントの使用方法を示す](https://getbootstrap.com/docs/)ブートストラップ`NavLink` ナビゲーション バーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="66aa9-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="66aa9-191">`NavLinkMatch` 要素の `Match` 属性に割り当てられる 2 つの `<NavLink>` オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="66aa9-192">`NavLinkMatch.All` &ndash; `NavLink` は、現在の URL 全体に一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="66aa9-193">`NavLinkMatch.Prefix` (*既定*) &ndash; `NavLink` は、現在の URL の任意のプレフィックスに一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="66aa9-194">前の例では、ホーム `NavLink` `href=""` はホーム URL と一致し、アプリの既定のベース パス URL (`active` など) でのみ `https://localhost:5001/` CSS クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="66aa9-195">2 番目の `NavLink` は、ユーザーが `active` プレフィックスを含む任意の URL (`MyComponent` や `https://localhost:5001/MyComponent` など) にアクセスしたときに、`https://localhost:5001/MyComponent/AnotherSegment` クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="66aa9-196">追加の `NavLink` コンポーネント属性は、レンダリングされるアンカー タグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="66aa9-197">次の例では、`NavLink` コンポーネントに `target` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="66aa9-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="66aa9-198">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="66aa9-199">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="66aa9-199">URI and navigation state helpers</span></span>

<span data-ttu-id="66aa9-200">C# コード内の URI とナビゲーションを操作するには、<xref:Microsoft.AspNetCore.Components.NavigationManager> を使用します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="66aa9-201">`NavigationManager` には、次の表に示すイベントとメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="66aa9-202">メンバー</span><span class="sxs-lookup"><span data-stu-id="66aa9-202">Member</span></span> | <span data-ttu-id="66aa9-203">説明</span><span class="sxs-lookup"><span data-stu-id="66aa9-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="66aa9-204">URI</span><span class="sxs-lookup"><span data-stu-id="66aa9-204">Uri</span></span> | <span data-ttu-id="66aa9-205">現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="66aa9-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="66aa9-206">BaseUri</span></span> | <span data-ttu-id="66aa9-207">絶対 URI を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="66aa9-208">通常、`BaseUri` は `href`wwwroot/index.html`<base>` (*WebAssembly)、または*Pages/_Host.cshtmlBlazor (*サーバー) 内のドキュメントの* 要素の Blazor 属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="66aa9-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="66aa9-209">NavigateTo</span></span> | <span data-ttu-id="66aa9-210">指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-210">Navigates to the specified URI.</span></span> <span data-ttu-id="66aa9-211">`forceLoad` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="66aa9-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="66aa9-212">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="66aa9-213">URI が通常クライアント側ルーターによって処理されるかどうかにかかわらず、ブラウザーでは、強制的にサーバーから新しいページが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="66aa9-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="66aa9-214">LocationChanged</span></span> | <span data-ttu-id="66aa9-215">ナビゲーションの場所が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="66aa9-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="66aa9-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="66aa9-216">ToAbsoluteUri</span></span> | <span data-ttu-id="66aa9-217">相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="66aa9-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="66aa9-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="66aa9-219">ベース URI (たとえば、`GetBaseUri` によって以前に返された URI) が与えられると、絶対 URI を、ベース URI プレフィックスに相対的な URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="66aa9-220">次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="66aa9-221">次のコンポーネントは、場所の変更イベントを処理します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-221">The following component handles a location changed event.</span></span> <span data-ttu-id="66aa9-222">`HandleLocationChanged` メソッドは、`Dispose` がフレームワークによって呼び出されると、アンフックになります。</span><span class="sxs-lookup"><span data-stu-id="66aa9-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="66aa9-223">このメソッドをアンフックすることで、コンポーネントのガベージ コレクションが許可されます。</span><span class="sxs-lookup"><span data-stu-id="66aa9-223">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implement IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="66aa9-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> は、イベントに関する次の情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="66aa9-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="66aa9-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; 新しい場所の URL。</span><span class="sxs-lookup"><span data-stu-id="66aa9-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="66aa9-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; `true` の場合、Blazor によってブラウザーからナビゲーションがインターセプトされました。</span><span class="sxs-lookup"><span data-stu-id="66aa9-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="66aa9-227">`false` の場合、[NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) によってナビゲーションが発生しました。</span><span class="sxs-lookup"><span data-stu-id="66aa9-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="66aa9-228">コンポーネントの破棄の詳細については、「<xref:blazor/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="66aa9-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
