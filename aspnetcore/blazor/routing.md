---
title: ASP.NET Core Blazor ルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、[ナビゲーションリンク] コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/routing
ms.openlocfilehash: 76266aedd4655161f1f50a8beb0936660d452912
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999817"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="d4357-103">ASP.NET Core Blazor ルーティング</span><span class="sxs-lookup"><span data-stu-id="d4357-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="d4357-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d4357-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d4357-105">要求をルーティングする方法と、`NavLink` コンポーネントを使用して Blazor アプリでナビゲーションリンクを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d4357-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d4357-106">ASP.NET Core エンドポイントルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="d4357-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="d4357-107">Blazor サーバーは[ASP.NET Core エンドポイントルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="d4357-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d4357-108">ASP.NET Core アプリは、`Startup.Configure` で @no__t 0 を使用して対話型コンポーネントの着信接続を受け入れるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="d4357-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="d4357-109">最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor Server アプリのサーバー側の部分のホストとして機能します。</span><span class="sxs-lookup"><span data-stu-id="d4357-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="d4357-110">規則により、通常、*ホスト*ページは *_Host*という名前になります。</span><span class="sxs-lookup"><span data-stu-id="d4357-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="d4357-111">ホストファイルに指定されているルートは、ルート照合の優先順位が低いため、*フォールバックルート*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d4357-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="d4357-112">フォールバックルートは、他のルートが一致しない場合に考慮されます。</span><span class="sxs-lookup"><span data-stu-id="d4357-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="d4357-113">これにより、Blazor Server アプリに干渉することなく、他のコントローラーやページをアプリで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="d4357-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="d4357-114">ルートテンプレート</span><span class="sxs-lookup"><span data-stu-id="d4357-114">Route templates</span></span>

<span data-ttu-id="d4357-115">@No__t-0 コンポーネントを使用すると、指定されたルートを使用して各コンポーネントにルーティングできます。</span><span class="sxs-lookup"><span data-stu-id="d4357-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="d4357-116">@No__t-0 コンポーネントが、アプリケーションの*razor*ファイルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="d4357-116">The `Router` component appears in the *App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="d4357-117">@No__t-1 ディレクティブの付いた*razor*ファイルがコンパイルされると、生成されたクラスに、ルートテンプレートを指定する @no__t 2 が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4357-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="d4357-118">実行時には、@no__t 0 のコンポーネントが次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d4357-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="d4357-119">必要なパラメーターと共に @no__t から `RouteData` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d4357-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="d4357-120">指定されたパラメーターを使用して、指定されたコンポーネントをそのレイアウト (または任意の既定のレイアウト) でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="d4357-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="d4357-121">必要に応じて、レイアウトクラスを含む @no__t 0 パラメーターを指定して、レイアウトを指定しないコンポーネントに使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d4357-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="d4357-122">既定の Blazor テンプレートでは、`MainLayout` コンポーネントが指定されています。</span><span class="sxs-lookup"><span data-stu-id="d4357-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="d4357-123">*Mainlayout。 razor*は、テンプレートプロジェクトの*共有*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="d4357-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="d4357-124">レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4357-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="d4357-125">コンポーネントには、複数のルートテンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="d4357-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="d4357-126">次のコンポーネントは `/BlazorRoute` および `/DifferentBlazorRoute` の要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="d4357-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="d4357-127">Url が正しく解決されるようにするには、アプリで*wwwroot/index.html*ファイル (Blazor) または*Pages/_Host*ファイル (Blazor Server) に @no__t 0 タグを含める必要があります。このとき、アプリの基本パスは、`href` 属性 (`<base href="/">`) で指定します。</span><span class="sxs-lookup"><span data-stu-id="d4357-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="d4357-128">詳細については、「 <xref:host-and-deploy/blazor/index#app-base-path> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4357-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="d4357-129">コンテンツが見つからないときにカスタムコンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="d4357-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="d4357-130">@No__t-0 コンポーネントを使用すると、要求されたルートのコンテンツが見つからない場合に、アプリでカスタムコンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d4357-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="d4357-131">*App.xaml*ファイルで、`Router` コンポーネントの `NotFound` テンプレートパラメーターにカスタムコンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="d4357-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```cshtml
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

<span data-ttu-id="d4357-132">@No__t-0 タグの内容には、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d4357-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="d4357-133">@No__t-0 のコンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4357-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="d4357-134">複数のアセンブリからコンポーネントへのルーティング</span><span class="sxs-lookup"><span data-stu-id="d4357-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="d4357-135">@No__t-0 パラメーターを使用して、ルーティング可能なコンポーネントを検索するときに考慮する `Router` コンポーネントの追加のアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4357-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="d4357-136">指定されたアセンブリは、@no__t 横-0 で指定されたアセンブリに加えて考慮されます。</span><span class="sxs-lookup"><span data-stu-id="d4357-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="d4357-137">次の例では、`Component1` は、参照先クラスライブラリで定義されているルーティング可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="d4357-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="d4357-138">次の @no__t 例では、`Component1` のルーティングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d4357-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

<span data-ttu-id="d4357-139">< Router AppAssembly = "typeof (Program).アセンブリ "AdditionalAssemblies =" new [] {typeof (Component1)。Assembly} >... </Router></span><span class="sxs-lookup"><span data-stu-id="d4357-139"><Router AppAssembly="typeof(Program).Assembly" AdditionalAssemblies="new[] { typeof(Component1).Assembly }> ... </Router></span></span>

## <a name="route-parameters"></a><span data-ttu-id="d4357-140">ルートパラメーター</span><span class="sxs-lookup"><span data-stu-id="d4357-140">Route parameters</span></span>

<span data-ttu-id="d4357-141">ルーターはルートパラメーターを使用して、同じ名前の対応するコンポーネントパラメーターを設定します (大文字と小文字は区別されません)。</span><span class="sxs-lookup"><span data-stu-id="d4357-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="d4357-142">ASP.NET Core 3.0 の Blazor アプリでは、省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d4357-142">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0.</span></span> <span data-ttu-id="d4357-143">前の例では、2つの `@page` ディレクティブが適用されています。</span><span class="sxs-lookup"><span data-stu-id="d4357-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="d4357-144">最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="d4357-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d4357-145">2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、`Text` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d4357-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="d4357-146">ルート制約</span><span class="sxs-lookup"><span data-stu-id="d4357-146">Route constraints</span></span>

<span data-ttu-id="d4357-147">ルート制約は、ルートセグメントの型の一致をコンポーネントに適用します。</span><span class="sxs-lookup"><span data-stu-id="d4357-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d4357-148">次の例では、`Users` コンポーネントへのルートは、次の場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="d4357-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="d4357-149">要求 URL には、@no__t 0 のルートセグメントが存在します。</span><span class="sxs-lookup"><span data-stu-id="d4357-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="d4357-150">@No__t-0 セグメントは整数 (`int`) です。</span><span class="sxs-lookup"><span data-stu-id="d4357-150">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="d4357-151">次の表に示されているルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4357-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d4357-152">インバリアントカルチャと一致するルート制約の詳細については、表の下の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4357-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d4357-153">制約</span><span class="sxs-lookup"><span data-stu-id="d4357-153">Constraint</span></span> | <span data-ttu-id="d4357-154">例</span><span class="sxs-lookup"><span data-stu-id="d4357-154">Example</span></span>           | <span data-ttu-id="d4357-155">一致の例</span><span class="sxs-lookup"><span data-stu-id="d4357-155">Example Matches</span></span>                                                                  | <span data-ttu-id="d4357-156">インバリアント</span><span class="sxs-lookup"><span data-stu-id="d4357-156">Invariant</span></span><br><span data-ttu-id="d4357-157">カルチャ</span><span class="sxs-lookup"><span data-stu-id="d4357-157">culture</span></span><br><span data-ttu-id="d4357-158">一致</span><span class="sxs-lookup"><span data-stu-id="d4357-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="d4357-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="d4357-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="d4357-160">いいえ</span><span class="sxs-lookup"><span data-stu-id="d4357-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="d4357-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="d4357-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="d4357-162">[はい]</span><span class="sxs-lookup"><span data-stu-id="d4357-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="d4357-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="d4357-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="d4357-164">[はい]</span><span class="sxs-lookup"><span data-stu-id="d4357-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="d4357-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d4357-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d4357-166">[はい]</span><span class="sxs-lookup"><span data-stu-id="d4357-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="d4357-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d4357-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d4357-168">[はい]</span><span class="sxs-lookup"><span data-stu-id="d4357-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="d4357-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="d4357-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="d4357-170">いいえ</span><span class="sxs-lookup"><span data-stu-id="d4357-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="d4357-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d4357-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d4357-172">[はい]</span><span class="sxs-lookup"><span data-stu-id="d4357-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="d4357-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d4357-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d4357-174">[はい]</span><span class="sxs-lookup"><span data-stu-id="d4357-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="d4357-175">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4357-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="d4357-176">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="d4357-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="d4357-177">ドットを含む Url を使用したルーティング</span><span class="sxs-lookup"><span data-stu-id="d4357-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="d4357-178">Blazor Server apps では、 *_Host*の既定のルートは `/` (`@page "/"`) です。</span><span class="sxs-lookup"><span data-stu-id="d4357-178">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="d4357-179">ドット (`.`) を含む要求 URL が、既定のルートと一致しません。これは、URL がファイルを要求しているためです。</span><span class="sxs-lookup"><span data-stu-id="d4357-179">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="d4357-180">Blazor アプリは、存在しない静的ファイルに対して*404-Not Found*応答を返します。</span><span class="sxs-lookup"><span data-stu-id="d4357-180">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="d4357-181">ドットを含むルートを使用するには、次のルートテンプレートを使用して *_Host*を構成します。</span><span class="sxs-lookup"><span data-stu-id="d4357-181">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="d4357-182">@No__t 0 テンプレートには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4357-182">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="d4357-183">2つの*アスタリスク (* `**`) を使用すると、スラッシュ (@no__t) をエンコードせずに複数のフォルダー境界を越えてパスをキャプチャできます。</span><span class="sxs-lookup"><span data-stu-id="d4357-183">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="d4357-184">@No__t-0 ルートパラメーター名。</span><span class="sxs-lookup"><span data-stu-id="d4357-184">A `path` route parameter name.</span></span>

<span data-ttu-id="d4357-185">詳細については、「 <xref:fundamentals/routing> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4357-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="d4357-186">ナビゲーションリンクコンポーネント</span><span class="sxs-lookup"><span data-stu-id="d4357-186">NavLink component</span></span>

<span data-ttu-id="d4357-187">ナビゲーションリンクを作成するときは、HTML ハイパーリンク要素の代わりに `NavLink` コンポーネントを使用します (`<a>`)。</span><span class="sxs-lookup"><span data-stu-id="d4357-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="d4357-188">@No__t 0 のコンポーネントは、`<a>` の要素のように動作しますが、@no__t 2 の CSS クラスが現在の URL と一致するかどう @no__t かに基づいて2の CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="d4357-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d4357-189">@No__t-0 クラスは、表示されているナビゲーションリンク内のどのページがアクティブページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d4357-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="d4357-190">次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/)ナビゲーションバーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4357-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="d4357-191">@No__t-2 要素の `Match` 属性に割り当てることができる @no__t 0 オプションが2つあります。</span><span class="sxs-lookup"><span data-stu-id="d4357-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="d4357-192">`NavLinkMatch.All` &ndash; `NavLink` は、現在の URL 全体に一致するとアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="d4357-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="d4357-193">`NavLinkMatch.Prefix` (*既定値*) &ndash; `NavLink` は、現在の URL の任意のプレフィックスに一致するとアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="d4357-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d4357-194">前の例では、ホーム `NavLink` `href=""` はホーム URL と一致し、アプリの既定の基本パス URL (たとえば、`https://localhost:5001/`) では @no__t 2 CSS クラスのみを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d4357-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="d4357-195">2番目の `NavLink` は、ユーザーが `MyComponent` プレフィックス (たとえば、`https://localhost:5001/MyComponent` と `https://localhost:5001/MyComponent/AnotherSegment`) を持つ任意の URL にアクセスしたときに、`active` クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d4357-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="d4357-196">追加の `NavLink` コンポーネント属性は、表示されるアンカータグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d4357-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="d4357-197">次の例では、`NavLink` コンポーネントに `target` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d4357-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="d4357-198">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d4357-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="d4357-199">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="d4357-199">URI and navigation state helpers</span></span>

<span data-ttu-id="d4357-200">コード内でC# uri とナビゲーションを操作するには `Microsoft.AspNetCore.Components.NavigationManager` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d4357-200">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="d4357-201">`NavigationManager` は、次の表に示すイベントとメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="d4357-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="d4357-202">メンバー</span><span class="sxs-lookup"><span data-stu-id="d4357-202">Member</span></span> | <span data-ttu-id="d4357-203">説明</span><span class="sxs-lookup"><span data-stu-id="d4357-203">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="d4357-204">現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="d4357-204">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="d4357-205">絶対 uri を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="d4357-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="d4357-206">通常、`BaseUri` は、 *wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) のドキュメントの `<base>` 要素の `href` 属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="d4357-206">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="d4357-207">指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="d4357-207">Navigates to the specified URI.</span></span> <span data-ttu-id="d4357-208">@No__t-0 が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="d4357-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="d4357-209">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="d4357-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="d4357-210">ブラウザーは、通常、URI がクライアント側ルーターによって処理されるかどうかにかかわらず、サーバーから新しいページを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d4357-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="d4357-211">ナビゲーション位置が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="d4357-211">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="d4357-212">相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="d4357-212">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="d4357-213">ベース URI (たとえば、前に `GetBaseUri` によって返された URI) を指定すると、は、絶対 URI をベース URI プレフィックスに対して相対的な URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="d4357-213">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="d4357-214">次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。</span><span class="sxs-lookup"><span data-stu-id="d4357-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
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
