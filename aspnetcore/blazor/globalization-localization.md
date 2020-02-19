---
title: ASP.NET Core Blazor のグローバリゼーションとローカリゼーション
author: guardrex
description: 複数のカルチャおよび言語のユーザーが Razor コンポーネントにアクセスできるようにする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453188"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="40022-103">ASP.NET Core Blazor のグローバリゼーションとローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="40022-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="40022-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="40022-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="40022-105">Razor コンポーネントは、複数のカルチャおよび言語のユーザーがアクセスできるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="40022-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="40022-106">次の .NET グローバリゼーションおよびローカリゼーションシナリオを利用できます。</span><span class="sxs-lookup"><span data-stu-id="40022-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="40022-107">.NET のリソースシステム</span><span class="sxs-lookup"><span data-stu-id="40022-107">.NET's resources system</span></span>
* <span data-ttu-id="40022-108">カルチャ固有の数値と日付の書式設定</span><span class="sxs-lookup"><span data-stu-id="40022-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="40022-109">現在、次のような ASP.NET Core のローカライズシナリオがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="40022-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="40022-110">`IStringLocalizer<>` は Blazor アプリで*サポートされて*います。</span><span class="sxs-lookup"><span data-stu-id="40022-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="40022-111">`IHtmlLocalizer<>`、`IViewLocalizer<>`、データ注釈のローカライズは MVC シナリオ ASP.NET Core、Blazor アプリではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="40022-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="40022-112">詳細については、<xref:fundamentals/localization> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40022-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="40022-113">グローバリゼーション</span><span class="sxs-lookup"><span data-stu-id="40022-113">Globalization</span></span>

Blazor<span data-ttu-id="40022-114">の `@bind` 機能は、ユーザーの現在のカルチャに基づいて、形式を実行し、値を解析して表示します。</span><span class="sxs-lookup"><span data-stu-id="40022-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="40022-115">現在のカルチャには、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="40022-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="40022-116">[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)は、次のフィールドの種類 (`<input type="{TYPE}" />`) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="40022-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="40022-117">上記のフィールド型は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="40022-117">The preceding field types:</span></span>

* <span data-ttu-id="40022-118">は、適切なブラウザーベースの書式規則を使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="40022-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="40022-119">自由形式のテキストを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="40022-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="40022-120">ブラウザーの実装に基づいてユーザーの操作特性を指定します。</span><span class="sxs-lookup"><span data-stu-id="40022-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="40022-121">次のフィールド型には特定の書式設定要件がありますが、Blazor で現在サポートされていないのは、すべての主要なブラウザーでサポートされていないためです。</span><span class="sxs-lookup"><span data-stu-id="40022-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="40022-122">`@bind` では、`@bind:culture` パラメーターを使用して、値の解析および書式設定のための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供します。</span><span class="sxs-lookup"><span data-stu-id="40022-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="40022-123">`date` および `number` のフィールドの種類を使用する場合は、カルチャを指定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="40022-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="40022-124">`date` と `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="40022-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="40022-125">ローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="40022-125">Localization</span></span>

Blazor<span data-ttu-id="40022-126"> サーバーアプリはローカライズ[ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="40022-126"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="40022-127">ミドルウェアは、アプリからリソースを要求するユーザーに適切なカルチャを選択します。</span><span class="sxs-lookup"><span data-stu-id="40022-127">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="40022-128">カルチャは、次のいずれかの方法を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="40022-128">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="40022-129">Cookie</span><span class="sxs-lookup"><span data-stu-id="40022-129">Cookies</span></span>](#cookies)
* [<span data-ttu-id="40022-130">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="40022-130">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="40022-131">詳細と例については、<xref:fundamentals/localization> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="40022-131">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="40022-132">国際化のためのリンカーの構成 (Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="40022-132">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="40022-133">既定では、Blazor WebAssembly に対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。</span><span class="sxs-lookup"><span data-stu-id="40022-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="40022-134">リンカーの動作を制御する方法の詳細とガイダンスについては、「<xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40022-134">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="40022-135">Cookie</span><span class="sxs-lookup"><span data-stu-id="40022-135">Cookies</span></span>

<span data-ttu-id="40022-136">ローカリゼーションカルチャクッキーは、ユーザーのカルチャを保持できます。</span><span class="sxs-lookup"><span data-stu-id="40022-136">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="40022-137">Cookie は、アプリのホストページ (*Pages/host. cshtml. .cs*) の `OnGet` メソッドによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="40022-137">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="40022-138">ローカリゼーションミドルウェアは、後続の要求で cookie を読み取り、ユーザーのカルチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="40022-138">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="40022-139">Cookie を使用すると、WebSocket 接続がカルチャを正しく伝達できるようになります。</span><span class="sxs-lookup"><span data-stu-id="40022-139">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="40022-140">ローカライズスキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが Websocket を使用できない可能性があるため、カルチャを永続化できません。</span><span class="sxs-lookup"><span data-stu-id="40022-140">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="40022-141">したがって、ローカライズカルチャ cookie を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="40022-141">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="40022-142">カルチャがローカライズ cookie に保存されている場合は、任意の手法を使用してカルチャを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="40022-142">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="40022-143">アプリにサーバー側 ASP.NET Core 用に確立されたローカライズスキームが既にある場合は、引き続きアプリの既存のローカライズインフラストラクチャを使用し、アプリのスキーム内でローカライズカルチャ cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="40022-143">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="40022-144">次の例では、ローカリゼーションミドルウェアが読み取ることができる cookie で現在のカルチャを設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="40022-144">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="40022-145">Blazor Server アプリで次の内容を含む*ページ/ホストの cshtml*ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="40022-145">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="40022-146">ローカライズは、次の一連のイベントでアプリによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="40022-146">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="40022-147">ブラウザーは、アプリに最初の HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="40022-147">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="40022-148">カルチャは、ローカリゼーションミドルウェアによって割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="40022-148">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="40022-149">*_Host*の `OnGet` メソッドは、応答の一部として cookie 内のカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="40022-149">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="40022-150">ブラウザーは、WebSocket 接続を開き、対話型の Blazor サーバーセッションを作成します。</span><span class="sxs-lookup"><span data-stu-id="40022-150">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="40022-151">ローカリゼーションミドルウェアは cookie を読み取り、カルチャを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="40022-151">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="40022-152">Blazor サーバーセッションは、正しいカルチャで開始されます。</span><span class="sxs-lookup"><span data-stu-id="40022-152">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="40022-153">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="40022-153">Provide UI to choose the culture</span></span>

<span data-ttu-id="40022-154">ユーザーがカルチャを選択できるように UI を提供するには、*リダイレクトベースのアプローチ*を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="40022-154">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="40022-155">このプロセスは、ユーザーがセキュリティで保護されたリソースにアクセスしようとしたときに、ユーザーがサインインページにリダイレクトされ、元のリソースにリダイレクトされる&mdash;、web アプリで発生する処理に似ています。</span><span class="sxs-lookup"><span data-stu-id="40022-155">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="40022-156">アプリは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャを永続化します。</span><span class="sxs-lookup"><span data-stu-id="40022-156">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="40022-157">コントローラーは、ユーザーが選択したカルチャを cookie に設定し、ユーザーを元の URI にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="40022-157">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="40022-158">Cookie でユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するために、サーバー上に HTTP エンドポイントを確立します。</span><span class="sxs-lookup"><span data-stu-id="40022-158">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="40022-159">`LocalRedirect` アクションの結果を使用して、開いているリダイレクト攻撃を防止します。</span><span class="sxs-lookup"><span data-stu-id="40022-159">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="40022-160">詳細については、<xref:security/preventing-open-redirects> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="40022-160">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="40022-161">次のコンポーネントは、ユーザーがカルチャを選択したときに最初のリダイレクトを実行する方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="40022-161">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="40022-162">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="40022-162">Additional resources</span></span>

* <xref:fundamentals/localization>
