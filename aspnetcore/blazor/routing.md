---
title: ASP.NET Core Blazor ルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、[ナビゲーションリンク] コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/routing
ms.openlocfilehash: 1c61eedf7dbf0bbc8796eaa11360783b9d7aba6c
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963866"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="5f1ba-103">ASP.NET Core Blazor ルーティング</span><span class="sxs-lookup"><span data-stu-id="5f1ba-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="5f1ba-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5f1ba-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5f1ba-105">要求をルーティングする方法と、 `NavLink`コンポーネントを使用して Blazor アプリでナビゲーションリンクを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="5f1ba-106">ASP.NET Core エンドポイントルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="5f1ba-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="5f1ba-107">Blazor サーバーは[ASP.NET Core エンドポイントルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="5f1ba-108">次の`MapBlazorHub` `Startup.Configure`のを使用して、対話型コンポーネントの着信接続を受け入れるように ASP.NET Core アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="5f1ba-109">ルートテンプレート</span><span class="sxs-lookup"><span data-stu-id="5f1ba-109">Route templates</span></span>

<span data-ttu-id="5f1ba-110">コンポーネント`Router`は、指定されたルートを使用して各コンポーネントにルーティングできるようにします。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-110">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="5f1ba-111">この`Router`コンポーネントは、*アプリケーションの razor*ファイルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-111">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="5f1ba-112">ディレクティブを含む<xref:Microsoft.AspNetCore.Mvc.RouteAttribute> `@page` razor ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定するが提供されます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-112">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="5f1ba-113">実行時には`RouteView` 、次のコンポーネントが実行されます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-113">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="5f1ba-114">必要なパラメーターと`Router`共にからを受け取ります。`RouteData`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-114">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="5f1ba-115">指定されたパラメーターを使用して、指定されたコンポーネントをそのレイアウト (または任意の既定のレイアウト) でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-115">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="5f1ba-116">必要に応じて、 `DefaultLayout`レイアウトクラスを含むパラメーターを指定して、レイアウトを指定しないコンポーネントに使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-116">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="5f1ba-117">既定の Blazor テンプレートでは`MainLayout` 、コンポーネントが指定されています。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-117">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="5f1ba-118">*Mainlayout。 razor*は、テンプレートプロジェクトの*共有*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-118">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="5f1ba-119">レイアウトの詳細については<xref:blazor/layouts>、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-119">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="5f1ba-120">コンポーネントには、複数のルートテンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-120">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="5f1ba-121">次のコンポーネントは、と`/BlazorRoute` `/DifferentBlazorRoute`に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-121">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="5f1ba-122">Url が正しく解決されるようにするには`<base>` 、アプリで、 `href`属性 で指定されたアプリのベースパスを使用して、wwwroot/index.htmlファイル(Blazor)またはPages/_Hostファイル(BlazorServer)にタグを含める必要があります。`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="5f1ba-122">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="5f1ba-123">詳細については、「 <xref:host-and-deploy/blazor/index#app-base-path> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-123">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="5f1ba-124">コンテンツが見つからないときにカスタムコンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="5f1ba-124">Provide custom content when content isn't found</span></span>

<span data-ttu-id="5f1ba-125">`Router`コンポーネントを使用すると、要求されたルートのコンテンツが見つからない場合に、アプリでカスタムコンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-125">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="5f1ba-126">アプリケーションの*razor*ファイルで、 `NotFound` `Router`コンポーネントのテンプレートパラメーターにカスタムコンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-126">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="5f1ba-127">タグの`<NotFound>`内容には、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-127">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="5f1ba-128">コンテンツに`NotFound`既定のレイアウトを適用するに<xref:blazor/layouts>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-128">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="5f1ba-129">複数のアセンブリからコンポーネントへのルーティング</span><span class="sxs-lookup"><span data-stu-id="5f1ba-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="5f1ba-130">パラメーターを使用して、 `Router`ルーティング可能なコンポーネントを検索するときに考慮するコンポーネントの追加のアセンブリを指定します。 `AdditionalAssemblies`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-130">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="5f1ba-131">指定されたアセンブリは、指定`AppAssembly`されたアセンブリに加えて考慮されます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-131">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="5f1ba-132">次の例では`Component1` 、は、参照先クラスライブラリで定義されているルーティング可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-132">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="5f1ba-133">次`AdditionalAssemblies`の例では、のルーティング`Component1`がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-133">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

<span data-ttu-id="5f1ba-134">< Router AppAssembly = "typeof (Program).アセンブリ "AdditionalAssemblies =" new [] {typeof (Component1)。アセンブリ} >...</Router></span><span class="sxs-lookup"><span data-stu-id="5f1ba-134"><Router AppAssembly="typeof(Program).Assembly" AdditionalAssemblies="new[] { typeof(Component1).Assembly }> ... </Router></span></span>

## <a name="route-parameters"></a><span data-ttu-id="5f1ba-135">ルートパラメーター</span><span class="sxs-lookup"><span data-stu-id="5f1ba-135">Route parameters</span></span>

<span data-ttu-id="5f1ba-136">ルーターはルートパラメーターを使用して、同じ名前の対応するコンポーネントパラメーターを設定します (大文字と小文字は区別されません)。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-136">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="5f1ba-137">ASP.NET Core 3.0 Preview の Blazor アプリでは、省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-137">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="5f1ba-138">前`@page`の例では、2つのディレクティブが適用されています。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-138">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="5f1ba-139">最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-139">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="5f1ba-140">2番`@page`目のディレクティブ`{text}`は route パラメーターを受け取り、その値`Text`をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-140">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="5f1ba-141">ルート制約</span><span class="sxs-lookup"><span data-stu-id="5f1ba-141">Route constraints</span></span>

<span data-ttu-id="5f1ba-142">ルート制約は、ルートセグメントの型の一致をコンポーネントに適用します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-142">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="5f1ba-143">次の例では、 `Users`コンポーネントへのルートのみが一致します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-143">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="5f1ba-144">`Id`ルートセグメントは、要求 URL に存在します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-144">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="5f1ba-145">セグメントは整数 (`int`) です。 `Id`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-145">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="5f1ba-146">次の表に示されているルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-146">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="5f1ba-147">インバリアントカルチャと一致するルート制約の詳細については、表の下の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-147">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="5f1ba-148">制約</span><span class="sxs-lookup"><span data-stu-id="5f1ba-148">Constraint</span></span> | <span data-ttu-id="5f1ba-149">例</span><span class="sxs-lookup"><span data-stu-id="5f1ba-149">Example</span></span>           | <span data-ttu-id="5f1ba-150">一致の例</span><span class="sxs-lookup"><span data-stu-id="5f1ba-150">Example Matches</span></span>                                                                  | <span data-ttu-id="5f1ba-151">インバリアント</span><span class="sxs-lookup"><span data-stu-id="5f1ba-151">Invariant</span></span><br><span data-ttu-id="5f1ba-152">カルチャ</span><span class="sxs-lookup"><span data-stu-id="5f1ba-152">culture</span></span><br><span data-ttu-id="5f1ba-153">一致</span><span class="sxs-lookup"><span data-stu-id="5f1ba-153">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="5f1ba-154">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-154">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="5f1ba-155">いいえ</span><span class="sxs-lookup"><span data-stu-id="5f1ba-155">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="5f1ba-156">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-156">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="5f1ba-157">[はい]</span><span class="sxs-lookup"><span data-stu-id="5f1ba-157">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="5f1ba-158">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-158">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="5f1ba-159">[はい]</span><span class="sxs-lookup"><span data-stu-id="5f1ba-159">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="5f1ba-160">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-160">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="5f1ba-161">[はい]</span><span class="sxs-lookup"><span data-stu-id="5f1ba-161">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="5f1ba-162">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-162">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="5f1ba-163">[はい]</span><span class="sxs-lookup"><span data-stu-id="5f1ba-163">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="5f1ba-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="5f1ba-165">いいえ</span><span class="sxs-lookup"><span data-stu-id="5f1ba-165">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="5f1ba-166">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-166">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="5f1ba-167">[はい]</span><span class="sxs-lookup"><span data-stu-id="5f1ba-167">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="5f1ba-168">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="5f1ba-168">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="5f1ba-169">[はい]</span><span class="sxs-lookup"><span data-stu-id="5f1ba-169">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="5f1ba-170">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-170">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="5f1ba-171">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-171">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="5f1ba-172">ドットを含む Url を使用したルーティング</span><span class="sxs-lookup"><span data-stu-id="5f1ba-172">Routing with URLs that contain dots</span></span>

<span data-ttu-id="5f1ba-173">Blazor Server apps では、 *_Host*の既定のルートは`/` (`@page "/"`) です。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-173">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="5f1ba-174">ドット (`.`) を含む要求 url が、既定のルートと一致しません。これは、url がファイルの要求によって表示されるためです。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-174">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="5f1ba-175">Blazor アプリは、存在しない静的ファイルに対して*404-Not Found*応答を返します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-175">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="5f1ba-176">ドットを含むルートを使用するには、次のルートテンプレートを使用して *_Host*を構成します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-176">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="5f1ba-177">テンプレート`"/{**path}"`には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-177">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="5f1ba-178">2つの*アスタリスク*`**`() を使用すると、スラッシュ (`/`) をエンコードせずに複数のフォルダー境界を越えてパスをキャプチャできます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-178">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="5f1ba-179">`path`ルートパラメーター名。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-179">A `path` route parameter name.</span></span>

<span data-ttu-id="5f1ba-180">詳細については、「 <xref:fundamentals/routing> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-180">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="5f1ba-181">ナビゲーションリンクコンポーネント</span><span class="sxs-lookup"><span data-stu-id="5f1ba-181">NavLink component</span></span>

<span data-ttu-id="5f1ba-182">ナビゲーションリンク`NavLink`を作成するときは、HTML ハイパーリンク`<a>`要素 () の代わりにコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-182">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="5f1ba-183">コンポーネント`NavLink` `<a>`は要素のように動作しますが、 `active`が現在の URL `href`と一致するかどうかに基づいて CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-183">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="5f1ba-184">クラス`active`は、表示されているナビゲーションリンク内のどのページがアクティブページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-184">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="5f1ba-185">次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/)ナビゲーションバーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-185">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="5f1ba-186">要素の`Match` `NavLinkMatch` 属性には、次の2つのオプションを割り当てることができます`<NavLink>` 。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-186">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="5f1ba-187">`NavLinkMatch.All`は、現在の`NavLink` URL 全体に一致するとアクティブになります。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="5f1ba-187">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="5f1ba-188">`NavLinkMatch.Prefix`(*既定値*)は、`NavLink`現在の URL の任意のプレフィックスに一致するとアクティブになります。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="5f1ba-188">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="5f1ba-189">前の例では、ホーム`NavLink` `href=""`はホーム`active` URL と一致し、アプリの既定の基本パス URL (たとえば、 `https://localhost:5001/`) でのみ CSS クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-189">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="5f1ba-190">2番`NavLink`目の`active`は、ユーザーが`MyComponent`プレフィックス (や`https://localhost:5001/MyComponent/AnotherSegment`など`https://localhost:5001/MyComponent` ) を持つ任意の URL にアクセスしたときにクラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-190">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="5f1ba-191">追加`NavLink`のコンポーネント属性は、表示されるアンカータグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-191">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="5f1ba-192">次の例では、 `NavLink`コンポーネントに`target`属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-192">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="5f1ba-193">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-193">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="5f1ba-194">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="5f1ba-194">URI and navigation state helpers</span></span>

<span data-ttu-id="5f1ba-195">コード`Microsoft.AspNetCore.Components.NavigationManager`内でC# uri とナビゲーションを操作するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-195">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="5f1ba-196">`NavigationManager`次の表に示すイベントとメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-196">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="5f1ba-197">メンバー</span><span class="sxs-lookup"><span data-stu-id="5f1ba-197">Member</span></span> | <span data-ttu-id="5f1ba-198">説明</span><span class="sxs-lookup"><span data-stu-id="5f1ba-198">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="5f1ba-199">現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-199">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="5f1ba-200">絶対 uri を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-200">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="5f1ba-201">通常、 `BaseUri`は、wwwroot/index.html `href` (Blazor webassembly)または*Pages/* (Blazor Server) のドキュメントの`<base>`要素の属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-201">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="5f1ba-202">指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-202">Navigates to the specified URI.</span></span> <span data-ttu-id="5f1ba-203">が`forceLoad` の`true`場合:</span><span class="sxs-lookup"><span data-stu-id="5f1ba-203">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="5f1ba-204">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-204">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="5f1ba-205">ブラウザーは、通常、URI がクライアント側ルーターによって処理されるかどうかにかかわらず、サーバーから新しいページを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-205">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="5f1ba-206">ナビゲーション位置が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-206">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="5f1ba-207">相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-207">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="5f1ba-208">ベース uri (たとえば、によって以前に`GetBaseUri`返された uri) を指定すると、は、絶対 uri をベース uri プレフィックスに対する相対 uri に変換します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-208">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="5f1ba-209">次のコンポーネントは、ボタンが選択`Counter`されたときにアプリのコンポーネントに移動します。</span><span class="sxs-lookup"><span data-stu-id="5f1ba-209">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
