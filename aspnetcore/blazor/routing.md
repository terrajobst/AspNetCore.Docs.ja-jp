---
title: ASP.NET Core Blazor のルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、NavLink コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 32459f9f42220b01ce04e6444a9bb4a9592ee2da
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649238"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="9b09b-103">ASP.NET Core Blazor のルーティング</span><span class="sxs-lookup"><span data-stu-id="9b09b-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="9b09b-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9b09b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="9b09b-105">Blazor アプリで、要求をルーティングする方法と、`NavLink` コンポーネントを使用して、ナビゲーション リンクを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="9b09b-106">ASP.NET Core エンドポイントのルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="9b09b-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="9b09b-107">Blazor サーバーは [ASP.NET Core エンドポイントのルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="9b09b-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="9b09b-108">ASP.NET Core アプリは、`Startup.Configure` で `MapBlazorHub` を使用して、対話型コンポーネントの着信接続を受け入れるように構成します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="9b09b-109">最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor サーバー アプリのサーバー側部分のホストとして機能します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="9b09b-110">通常、*ホスト* ページは、 *_Host.cshtml* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="9b09b-111">ホスト ファイルに指定されるルートは、ルート照合で低い優先順位で動作するため、*フォールバック ルート*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="9b09b-112">フォールバック ルートは、他のルートが一致しない場合に考慮されます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="9b09b-113">これにより、Blazor サーバー アプリと干渉することなく、他のコントローラーやページをアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="9b09b-114">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="9b09b-114">Route templates</span></span>

<span data-ttu-id="9b09b-115">`Router` コンポーネントでは、指定されたルートによる各コンポーネントへのルーティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="9b09b-116">`Router` コンポーネントは *App.razor* ファイルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="9b09b-117">`@page` ディレクティブを含む *razor* ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Components.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="9b09b-118">実行時に、`RouteView` コンポーネントは、</span><span class="sxs-lookup"><span data-stu-id="9b09b-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="9b09b-119">`Router` から、必要なパラメーターと共に `RouteData` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="9b09b-120">指定されたパラメーターを使用して、指定されたコンポーネントを、そのレイアウト (または任意の既定のレイアウト) でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="9b09b-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="9b09b-121">必要に応じて、レイアウト クラスで `DefaultLayout` パラメーターを指定して、レイアウトを指定しないコンポーネントに使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="9b09b-122">既定の Blazor テンプレートでは、`MainLayout` コンポーネントを指定しています。</span><span class="sxs-lookup"><span data-stu-id="9b09b-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="9b09b-123">*MainLayout.razor* は、テンプレート プロジェクトの *Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="9b09b-124">レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b09b-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="9b09b-125">コンポーネントには、複数のルート テンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="9b09b-126">次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute` に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="9b09b-127">URL が正しく解決されるように、アプリでは、`href` 属性に指定されているアプリのベース パス (`<base href="/">`) を使用して、その *wwwroot/index.html* ファイル (Blazor WebAssembly) または *Pages/_Host.cshtml* ファイル (Blazor Server) に `<base>` タグを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="9b09b-128">詳細については、「<xref:host-and-deploy/blazor/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b09b-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="9b09b-129">コンテンツが見つからないときにカスタム コンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="9b09b-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="9b09b-130">`Router` コンポーネントを使用すると、要求されたルートでコンテンツが見つからない場合に、アプリでカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="9b09b-131">*App.razor* ファイルで、`Router` コンポーネントの `NotFound` テンプレート パラメーターにカスタム コンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="9b09b-132">`<NotFound>` タグのコンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="9b09b-133">`NotFound` コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b09b-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="9b09b-134">複数のアセンブリからコンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="9b09b-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="9b09b-135">`AdditionalAssemblies` パラメーターを使用して、`Router` コンポーネントで、ルーティング可能なコンポーネントを検索するときに考慮する追加のアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="9b09b-136">指定されたアセンブリは、`AppAssembly` に指定されたアセンブリに加えて考慮されます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="9b09b-137">次の例では、`Component1` は、参照されているクラス ライブラリに定義されているルーティング可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="9b09b-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="9b09b-138">次の `AdditionalAssemblies` の例では、`Component1` のルーティング サポートの結果を示しています。</span><span class="sxs-lookup"><span data-stu-id="9b09b-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="9b09b-139">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="9b09b-139">Route parameters</span></span>

<span data-ttu-id="9b09b-140">ルーターは、ルート パラメーターを使用して、同じ名前の対応するコンポーネント パラメーターを設定します (大文字と小文字は区別されません)。</span><span class="sxs-lookup"><span data-stu-id="9b09b-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="9b09b-141">省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9b09b-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="9b09b-142">前の例では、2 つの `@page` ディレクティブが適用されています。</span><span class="sxs-lookup"><span data-stu-id="9b09b-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="9b09b-143">1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="9b09b-144">2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="9b09b-145">ルート制約</span><span class="sxs-lookup"><span data-stu-id="9b09b-145">Route constraints</span></span>

<span data-ttu-id="9b09b-146">ルート制約は、コンポーネントへのルート セグメントに型の一致を適用します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="9b09b-147">次の例で、`Users` コンポーネントへのルートは、次の場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="9b09b-148">要求 URL に `Id` ルート セグメントが存在する。</span><span class="sxs-lookup"><span data-stu-id="9b09b-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="9b09b-149">`Id` セグメントは整数 (`int`) である。</span><span class="sxs-lookup"><span data-stu-id="9b09b-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="9b09b-150">次の表に示すルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="9b09b-151">インバリアント カルチャと一致するルート制約については、表の下の警告で詳細を確認してください。</span><span class="sxs-lookup"><span data-stu-id="9b09b-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="9b09b-152">制約</span><span class="sxs-lookup"><span data-stu-id="9b09b-152">Constraint</span></span> | <span data-ttu-id="9b09b-153">例</span><span class="sxs-lookup"><span data-stu-id="9b09b-153">Example</span></span>           | <span data-ttu-id="9b09b-154">一致の例</span><span class="sxs-lookup"><span data-stu-id="9b09b-154">Example Matches</span></span>                                                                  | <span data-ttu-id="9b09b-155">インバリアント</span><span class="sxs-lookup"><span data-stu-id="9b09b-155">Invariant</span></span><br><span data-ttu-id="9b09b-156">カルチャ</span><span class="sxs-lookup"><span data-stu-id="9b09b-156">culture</span></span><br><span data-ttu-id="9b09b-157">一致</span><span class="sxs-lookup"><span data-stu-id="9b09b-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="9b09b-158">`true`、`FALSE`</span><span class="sxs-lookup"><span data-stu-id="9b09b-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="9b09b-159">いいえ</span><span class="sxs-lookup"><span data-stu-id="9b09b-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="9b09b-160">`2016-12-31`、`2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="9b09b-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="9b09b-161">はい</span><span class="sxs-lookup"><span data-stu-id="9b09b-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="9b09b-162">`49.99`、`-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="9b09b-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="9b09b-163">はい</span><span class="sxs-lookup"><span data-stu-id="9b09b-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="9b09b-164">`1.234`、`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9b09b-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="9b09b-165">はい</span><span class="sxs-lookup"><span data-stu-id="9b09b-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="9b09b-166">`1.234`、`-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="9b09b-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="9b09b-167">はい</span><span class="sxs-lookup"><span data-stu-id="9b09b-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="9b09b-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`、`{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="9b09b-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="9b09b-169">いいえ</span><span class="sxs-lookup"><span data-stu-id="9b09b-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="9b09b-170">`123456789`、`-123456789`</span><span class="sxs-lookup"><span data-stu-id="9b09b-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="9b09b-171">はい</span><span class="sxs-lookup"><span data-stu-id="9b09b-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="9b09b-172">`123456789`、`-123456789`</span><span class="sxs-lookup"><span data-stu-id="9b09b-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="9b09b-173">はい</span><span class="sxs-lookup"><span data-stu-id="9b09b-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="9b09b-174">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="9b09b-175">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="9b09b-176">ドットを含む URL によるルーティング</span><span class="sxs-lookup"><span data-stu-id="9b09b-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="9b09b-177">Blazor サーバー アプリでは、 *_Host.cshtml* の既定のルートは `/` (`@page "/"`) です。</span><span class="sxs-lookup"><span data-stu-id="9b09b-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="9b09b-178">ドット (`.`) を含む要求 URL は、既定のルートによって照合されません。URL がファイルを要求しているように見えるためです。</span><span class="sxs-lookup"><span data-stu-id="9b09b-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="9b09b-179">Blazor アプリは、存在しない静的ファイルに対して "*404 見つかりません*" 応答 を返します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="9b09b-180">ドットを含むルートを使用するには、次のルート テンプレートを使用して *_Host.cshtml* を構成します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="9b09b-181">`"/{**path}"` テンプレートには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="9b09b-182">二重アスタリスクの*キャッチオール*構文 (`**`)。スラッシュ (`/`) をエンコードせずに複数のフォルダー境界をまたがるパスをキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="9b09b-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="9b09b-183">`path` ルート パラメーター名。</span><span class="sxs-lookup"><span data-stu-id="9b09b-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="9b09b-184">*キャッチオール* パラメーター構文 (`*`/`**`) は、Razor コンポーネント ( *.razor*) ではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="9b09b-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="9b09b-185">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b09b-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="9b09b-186">NavLink コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9b09b-186">NavLink component</span></span>

<span data-ttu-id="9b09b-187">ナビゲーション リンクを作成するときは、HTML ハイパーリンク要素 (`<a>`) の代わりに `NavLink` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="9b09b-188">`NavLink` コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="9b09b-189">`active` クラスは、表示されているナビゲーション リンクの中でどのページがアクティブ ページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="9b09b-190">次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/) ナビゲーション バーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="9b09b-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="9b09b-191">`<NavLink>` 要素の `Match` 属性に割り当てられる 2 つの `NavLinkMatch` オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="9b09b-192">`NavLinkMatch.All` &ndash; `NavLink` は、現在の URL 全体に一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="9b09b-193">`NavLinkMatch.Prefix` (*既定*) &ndash; `NavLink` は、現在の URL の任意のプレフィックスに一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="9b09b-194">前の例では、ホーム `NavLink` `href=""` はホーム URL と一致し、アプリの既定のベース パス URL (`https://localhost:5001/` など) でのみ `active` CSS クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="9b09b-195">2 番目の `NavLink` は、ユーザーが `MyComponent` プレフィックスを含む任意の URL (`https://localhost:5001/MyComponent` や `https://localhost:5001/MyComponent/AnotherSegment` など) にアクセスしたときに、`active` クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="9b09b-196">追加の `NavLink` コンポーネント属性は、レンダリングされるアンカー タグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="9b09b-197">次の例では、`NavLink` コンポーネントに `target` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9b09b-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="9b09b-198">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="9b09b-199">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="9b09b-199">URI and navigation state helpers</span></span>

<span data-ttu-id="9b09b-200">C# コード内の URI とナビゲーションを操作するには、`Microsoft.AspNetCore.Components.NavigationManager` を使用します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-200">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="9b09b-201">`NavigationManager` には、次の表に示すイベントとメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="9b09b-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="9b09b-202">メンバー</span><span class="sxs-lookup"><span data-stu-id="9b09b-202">Member</span></span> | <span data-ttu-id="9b09b-203">説明</span><span class="sxs-lookup"><span data-stu-id="9b09b-203">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="9b09b-204">現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-204">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="9b09b-205">絶対 URI を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="9b09b-206">通常、`BaseUri` は *wwwroot/index.html* (Blazor WebAssembly)、または *Pages/_Host.cshtml* (Blazor サーバー) 内のドキュメントの `<base>` 要素の `href` 属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-206">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="9b09b-207">指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-207">Navigates to the specified URI.</span></span> <span data-ttu-id="9b09b-208">`forceLoad` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="9b09b-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="9b09b-209">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="9b09b-210">URI が通常クライアント側ルーターによって処理されるかどうかにかかわらず、ブラウザーでは、強制的にサーバーから新しいページが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9b09b-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="9b09b-211">ナビゲーションの場所が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="9b09b-211">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="9b09b-212">相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-212">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="9b09b-213">ベース URI (たとえば、`GetBaseUri` によって以前に返された URI) が与えられると、絶対 URI を、ベース URI プレフィックスに相対的な URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-213">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="9b09b-214">次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。</span><span class="sxs-lookup"><span data-stu-id="9b09b-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
