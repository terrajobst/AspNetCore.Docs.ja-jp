---
title: ASP.NET Core Blazor ルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、[ナビゲーションリンク] コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: blazor/routing
ms.openlocfilehash: 067dad657c1e89a31fac45fdfa095cce4b10798d
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238065"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="a5d20-103">ASP.NET Core Blazor ルーティング</span><span class="sxs-lookup"><span data-stu-id="a5d20-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="a5d20-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a5d20-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a5d20-105">要求をルーティングする方法と、 `NavLink`コンポーネントを使用して Blazor アプリでナビゲーションリンクを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="a5d20-106">ASP.NET Core エンドポイントルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="a5d20-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="a5d20-107">Blazor サーバー側は[ASP.NET Core エンドポイントルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="a5d20-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="a5d20-108">次の`MapBlazorHub` `Startup.Configure`のを使用して、対話型コンポーネントの着信接続を受け入れるように ASP.NET Core アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="a5d20-109">ルートテンプレート</span><span class="sxs-lookup"><span data-stu-id="a5d20-109">Route templates</span></span>

<span data-ttu-id="a5d20-110">`Router`コンポーネントによってルーティングが有効になり、各ユーザー補助コンポーネントにルートテンプレートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="a5d20-111">この`Router`コンポーネントは、*アプリケーションの razor*ファイルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="a5d20-112">Blazor サーバー側アプリの場合:</span><span class="sxs-lookup"><span data-stu-id="a5d20-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="a5d20-113">Blazor クライアント側アプリの場合:</span><span class="sxs-lookup"><span data-stu-id="a5d20-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="a5d20-114">ディレクティブを含む<xref:Microsoft.AspNetCore.Mvc.RouteAttribute> `@page` razor ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定するが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="a5d20-115">実行時に、ルーターはを`RouteAttribute`使用してコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを使用してコンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="a5d20-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="a5d20-116">コンポーネントには、複数のルートテンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="a5d20-117">次のコンポーネントは、と`/BlazorRoute` `/DifferentBlazorRoute`に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="a5d20-118">ルートを正しく生成するには、アプリで`<base>` 、 `href`属性に指定されたアプリのベースパスを使用して、 *wwwroot/index.html*ファイル (Blazor client 側) または*Pages/_Host*ファイル (Blazor サーバー側) にタグを含める必要があります。`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="a5d20-118">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="a5d20-119">詳細については、「 <xref:host-and-deploy/blazor/client-side#app-base-path> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5d20-119">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="a5d20-120">コンテンツが見つからないときにカスタムコンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="a5d20-120">Provide custom content when content isn't found</span></span>

<span data-ttu-id="a5d20-121">`Router`コンポーネントを使用すると、要求されたルートのコンテンツが見つからない場合に、アプリでカスタムコンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-121">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="a5d20-122">アプリケーションの*razor*ファイルで、 `<NotFoundContent>` `Router`コンポーネントの要素にカスタムコンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-122">In the *App.razor* file, set custom content in the `<NotFoundContent>` element of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

<span data-ttu-id="a5d20-123">の`<NotFoundContent>`コンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-123">The content of `<NotFoundContent>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="a5d20-124">ルートパラメーター</span><span class="sxs-lookup"><span data-stu-id="a5d20-124">Route parameters</span></span>

<span data-ttu-id="a5d20-125">ルーターはルートパラメーターを使用して、同じ名前の対応するコンポーネントパラメーターを設定します (大文字と小文字は区別されません)。</span><span class="sxs-lookup"><span data-stu-id="a5d20-125">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="a5d20-126">ASP.NET Core 3.0 Preview の Blazor アプリでは、省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a5d20-126">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="a5d20-127">前`@page`の例では、2つのディレクティブが適用されています。</span><span class="sxs-lookup"><span data-stu-id="a5d20-127">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="a5d20-128">最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-128">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="a5d20-129">2番`@page`目のディレクティブ`{text}`は route パラメーターを受け取り、その値`Text`をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-129">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="a5d20-130">ルート制約</span><span class="sxs-lookup"><span data-stu-id="a5d20-130">Route constraints</span></span>

<span data-ttu-id="a5d20-131">ルート制約は、ルートセグメントの型の一致をコンポーネントに適用します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-131">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="a5d20-132">次の例では、 `Users`コンポーネントへのルートのみが一致します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-132">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="a5d20-133">`Id`ルートセグメントは、要求 URL に存在します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-133">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="a5d20-134">セグメントは整数 (`int`) です。 `Id`</span><span class="sxs-lookup"><span data-stu-id="a5d20-134">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="a5d20-135">次の表に示されているルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-135">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="a5d20-136">インバリアントカルチャと一致するルート制約の詳細については、表の下の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5d20-136">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="a5d20-137">制約</span><span class="sxs-lookup"><span data-stu-id="a5d20-137">Constraint</span></span> | <span data-ttu-id="a5d20-138">例</span><span class="sxs-lookup"><span data-stu-id="a5d20-138">Example</span></span>           | <span data-ttu-id="a5d20-139">一致の例</span><span class="sxs-lookup"><span data-stu-id="a5d20-139">Example Matches</span></span>                                                                  | <span data-ttu-id="a5d20-140">インバリアント</span><span class="sxs-lookup"><span data-stu-id="a5d20-140">Invariant</span></span><br><span data-ttu-id="a5d20-141">カルチャ</span><span class="sxs-lookup"><span data-stu-id="a5d20-141">culture</span></span><br><span data-ttu-id="a5d20-142">一致</span><span class="sxs-lookup"><span data-stu-id="a5d20-142">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="a5d20-143">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="a5d20-143">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="a5d20-144">いいえ</span><span class="sxs-lookup"><span data-stu-id="a5d20-144">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="a5d20-145">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="a5d20-145">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="a5d20-146">[はい]</span><span class="sxs-lookup"><span data-stu-id="a5d20-146">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="a5d20-147">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="a5d20-147">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="a5d20-148">[はい]</span><span class="sxs-lookup"><span data-stu-id="a5d20-148">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="a5d20-149">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="a5d20-149">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="a5d20-150">[はい]</span><span class="sxs-lookup"><span data-stu-id="a5d20-150">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="a5d20-151">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="a5d20-151">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="a5d20-152">[はい]</span><span class="sxs-lookup"><span data-stu-id="a5d20-152">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="a5d20-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="a5d20-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="a5d20-154">いいえ</span><span class="sxs-lookup"><span data-stu-id="a5d20-154">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="a5d20-155">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="a5d20-155">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="a5d20-156">[はい]</span><span class="sxs-lookup"><span data-stu-id="a5d20-156">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="a5d20-157">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="a5d20-157">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="a5d20-158">[はい]</span><span class="sxs-lookup"><span data-stu-id="a5d20-158">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="a5d20-159">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-159">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="a5d20-160">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="a5d20-160">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="a5d20-161">ドットを含む Url を使用したルーティング</span><span class="sxs-lookup"><span data-stu-id="a5d20-161">Routing with URLs that contain dots</span></span>

<span data-ttu-id="a5d20-162">Blazor サーバー側アプリでは、 *_Host*の既定のルートは`/` (`@page "/"`) です。</span><span class="sxs-lookup"><span data-stu-id="a5d20-162">In Blazor server-side apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="a5d20-163">ドット (`.`) を含む要求 url が、既定のルートと一致しません。これは、url がファイルの要求によって表示されるためです。</span><span class="sxs-lookup"><span data-stu-id="a5d20-163">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="a5d20-164">Blazor アプリは、存在しない静的ファイルに対して*404-Not Found*応答を返します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-164">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="a5d20-165">ドットを含むルートを使用するには、次のルートテンプレートを使用して *_Host*を構成します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-165">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="a5d20-166">テンプレート`"/{**path}"`には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-166">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="a5d20-167">2つの*アスタリスク*`**`() を使用すると、スラッシュ (`/`) をエンコードせずに複数のフォルダー境界を越えてパスをキャプチャできます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-167">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="a5d20-168">`path`ルートパラメーター名。</span><span class="sxs-lookup"><span data-stu-id="a5d20-168">A `path` route parameter name.</span></span>

<span data-ttu-id="a5d20-169">詳細については、「 <xref:fundamentals/routing> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a5d20-169">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="a5d20-170">ナビゲーションリンクコンポーネント</span><span class="sxs-lookup"><span data-stu-id="a5d20-170">NavLink component</span></span>

<span data-ttu-id="a5d20-171">ナビゲーションリンク`NavLink`を作成するときは、HTML ハイパーリンク`<a>`要素 () の代わりにコンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-171">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="a5d20-172">コンポーネント`NavLink` `<a>`は要素のように動作しますが、 `active`が現在の URL `href`と一致するかどうかに基づいて CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="a5d20-172">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="a5d20-173">クラス`active`は、表示されているナビゲーションリンク内のどのページがアクティブページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-173">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="a5d20-174">次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/)ナビゲーションバーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-174">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="a5d20-175">要素の`Match` `NavLinkMatch` 属性には、次の2つのオプションを割り当てることができます`<NavLink>` 。</span><span class="sxs-lookup"><span data-stu-id="a5d20-175">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="a5d20-176">`NavLinkMatch.All`は、現在の`NavLink` URL 全体に一致するとアクティブになります。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="a5d20-176">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="a5d20-177">`NavLinkMatch.Prefix`(*既定値*)は、`NavLink`現在の URL の任意のプレフィックスに一致するとアクティブになります。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="a5d20-177">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="a5d20-178">前の例では、ホーム`NavLink` `href=""`はホーム`active` URL と一致し、アプリの既定の基本パス URL (たとえば、 `https://localhost:5001/`) でのみ CSS クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a5d20-178">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="a5d20-179">2番`NavLink`目の`active`は、ユーザーが`MyComponent`プレフィックス (や`https://localhost:5001/MyComponent/AnotherSegment`など`https://localhost:5001/MyComponent` ) を持つ任意の URL にアクセスしたときにクラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a5d20-179">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="a5d20-180">追加`NavLink`のコンポーネント属性は、表示されるアンカータグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-180">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="a5d20-181">次の例では、 `NavLink`コンポーネントに`target`属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a5d20-181">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="a5d20-182">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-182">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="a5d20-183">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="a5d20-183">URI and navigation state helpers</span></span>

<span data-ttu-id="a5d20-184">コード`Microsoft.AspNetCore.Components.IUriHelper`内でC# uri とナビゲーションを操作するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-184">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="a5d20-185">`IUriHelper`次の表に示すイベントとメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-185">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="a5d20-186">メンバー</span><span class="sxs-lookup"><span data-stu-id="a5d20-186">Member</span></span> | <span data-ttu-id="a5d20-187">説明</span><span class="sxs-lookup"><span data-stu-id="a5d20-187">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="a5d20-188">現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-188">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="a5d20-189">絶対 uri を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-189">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="a5d20-190">通常、 `GetBaseUri`は、 *wwwroot/index.html* (Blazor client 側) `<base>`または*Pages/_Host* (Blazor サーバー側) のドキュメントの要素の`href`属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-190">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="a5d20-191">指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-191">Navigates to the specified URI.</span></span> <span data-ttu-id="a5d20-192">が`forceLoad` の`true`場合:</span><span class="sxs-lookup"><span data-stu-id="a5d20-192">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="a5d20-193">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-193">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="a5d20-194">ブラウザーは、通常、URI がクライアント側ルーターによって処理されるかどうかにかかわらず、サーバーから新しいページを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="a5d20-194">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="a5d20-195">ナビゲーション位置が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="a5d20-195">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="a5d20-196">相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-196">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="a5d20-197">ベース uri (たとえば、によって以前に`GetBaseUri`返された uri) を指定すると、は、絶対 uri をベース uri プレフィックスに対する相対 uri に変換します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-197">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="a5d20-198">次のコンポーネントは、ボタンが選択`Counter`されたときにアプリのコンポーネントに移動します。</span><span class="sxs-lookup"><span data-stu-id="a5d20-198">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```

