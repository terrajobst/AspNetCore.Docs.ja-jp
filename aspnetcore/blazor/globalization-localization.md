---
title: ASP.NET Core Blazor のグローバリゼーションおよびローカライズ
author: guardrex
description: 複数のカルチャと言語でユーザーが Razor コンポーネントにアクセスできるようにする方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: aba62fa7b6285c8ba884652694f1ea3e3a66ed18
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644894"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="3cd68-103">ASP.NET Core Blazor のグローバリゼーションおよびローカライズ</span><span class="sxs-lookup"><span data-stu-id="3cd68-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="3cd68-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3cd68-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3cd68-105">複数のカルチャと言語でユーザーが Razor コンポーネントにアクセスできるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="3cd68-106">利用できる .NET のグローバリゼーションおよびローカライズのシナリオは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3cd68-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="3cd68-107">.NET のリソース システム</span><span class="sxs-lookup"><span data-stu-id="3cd68-107">.NET's resources system</span></span>
* <span data-ttu-id="3cd68-108">カルチャ固有の数値と日付の書式</span><span class="sxs-lookup"><span data-stu-id="3cd68-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="3cd68-109">現在、サポートされている ASP.NET Core のローカライズ シナリオは限られています。</span><span class="sxs-lookup"><span data-stu-id="3cd68-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="3cd68-110">Blazor アプリでは `IStringLocalizer<>` は "*サポートされていません*"。</span><span class="sxs-lookup"><span data-stu-id="3cd68-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="3cd68-111">`IHtmlLocalizer<>`、`IViewLocalizer<>`、データ注釈のローカライズは ASP.NET Core の MVC シナリオであり、Blazor アプリでは "**サポートされていません**"。</span><span class="sxs-lookup"><span data-stu-id="3cd68-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="3cd68-112">詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3cd68-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="3cd68-113">グローバリゼーション</span><span class="sxs-lookup"><span data-stu-id="3cd68-113">Globalization</span></span>

Blazor<span data-ttu-id="3cd68-114"> の `@bind` 機能では、書式設定が実行され、ユーザーの現在のカルチャに基づいて、表示する値が解析されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="3cd68-115">現在のカルチャは、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="3cd68-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) は、次のフィールドの型 (`<input type="{TYPE}" />`) に使用されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="3cd68-117">前のフィールドの型は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3cd68-117">The preceding field types:</span></span>

* <span data-ttu-id="3cd68-118">適切なブラウザー ベースの書式ルールを使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="3cd68-119">自由形式のテキストを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3cd68-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="3cd68-120">ブラウザーの実装に基づいてユーザー操作の特性を指定します。</span><span class="sxs-lookup"><span data-stu-id="3cd68-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="3cd68-121">次のフィールドの型には、特定の書式設定の要件がありますが、すべての主要なブラウザーでサポートされていないため、Blazor では現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3cd68-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="3cd68-122">`@bind` では、値の解析および書式設定のための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供する `@bind:culture` パラメーターをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="3cd68-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="3cd68-123">`date` および `number` のフィールドの型を使用する場合は、カルチャを指定しないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3cd68-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="3cd68-124">`date` および `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="3cd68-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="3cd68-125">ローカリゼーション</span><span class="sxs-lookup"><span data-stu-id="3cd68-125">Localization</span></span>

Blazor<span data-ttu-id="3cd68-126"> サーバー アプリは、[ローカライズ ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-126"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="3cd68-127">ミドルウェアによって、アプリからリソースを要求するユーザーに対して適切なカルチャが選択されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-127">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="3cd68-128">カルチャは、次のいずれかの方法を使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-128">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="3cd68-129">Cookie</span><span class="sxs-lookup"><span data-stu-id="3cd68-129">Cookies</span></span>](#cookies)
* [<span data-ttu-id="3cd68-130">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="3cd68-130">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="3cd68-131">使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3cd68-131">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="3cd68-132">国際化用にリンカーを構成する (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="3cd68-132">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="3cd68-133">既定では、Blazor WebAssembly に対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="3cd68-134">リンカーの動作を制御する方法の詳細とガイダンスについては、「<xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3cd68-134">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="3cd68-135">クッキー</span><span class="sxs-lookup"><span data-stu-id="3cd68-135">Cookies</span></span>

<span data-ttu-id="3cd68-136">ローカライズ カルチャの Cookie では、ユーザーのカルチャを保持できます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-136">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="3cd68-137">Cookie は、アプリのホスト ページ (*Pages/Host. cshtml*) の `OnGet` メソッドによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-137">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="3cd68-138">ローカライズ ミドルウェアでは、後続の要求で Cookie を読み取り、ユーザーのカルチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="3cd68-138">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="3cd68-139">Cookie を使用すると、WebSocket 接続によってカルチャを正しく伝達できることを確実にします。</span><span class="sxs-lookup"><span data-stu-id="3cd68-139">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="3cd68-140">ローカライズ スキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが WebSocket を使用できない可能性があるため、カルチャを保持できません。</span><span class="sxs-lookup"><span data-stu-id="3cd68-140">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="3cd68-141">したがって、ローカライズ カルチャの Cookie を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3cd68-141">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="3cd68-142">カルチャがローカライズ Cookie で保持されている場合は、任意の手法を使用してカルチャを割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-142">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="3cd68-143">アプリにサーバー側 ASP.NET Core 用に確立されたローカライズ スキームが既にある場合は、アプリの既存のローカライズ インフラストラクチャを引き続き使用し、アプリのスキーム内にローカライズ カルチャ Cookie を設定します。</span><span class="sxs-lookup"><span data-stu-id="3cd68-143">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="3cd68-144">次の例では、ローカライズ ミドルウェアによって読み取ることができる Cookie で現在のカルチャを設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="3cd68-144">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="3cd68-145">Blazor サーバーアプリで次の内容を使用して、*Pages/Host.cshtml.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3cd68-145">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="3cd68-146">ローカライズは、次の一連のイベントでアプリによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-146">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="3cd68-147">ブラウザーによって、アプリに最初の HTTP 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-147">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="3cd68-148">カルチャは、ローカライズ ミドルウェアによって割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-148">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="3cd68-149">*_Host.cshtml.cs* の `OnGet` メソッドによって、応答の一部として Cookie にカルチャが保持されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-149">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="3cd68-150">ブラウザーによって、WebSocket 接続が開かれ、対話型の Blazor サーバー セッションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-150">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="3cd68-151">ローカライズ ミドルウェアによって、Cookie が読み取られ、カルチャが割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-151">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="3cd68-152">Blazor サーバー セッションは、正しいカルチャで開始されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-152">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="3cd68-153">カルチャを選択するための UI を提供する</span><span class="sxs-lookup"><span data-stu-id="3cd68-153">Provide UI to choose the culture</span></span>

<span data-ttu-id="3cd68-154">ユーザーがカルチャを選択できるように UI を提供するには、"*リダイレクト ベースのアプローチ*" をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3cd68-154">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="3cd68-155">このプロセスは、ユーザーがセキュリティで保護されたリソースにアクセスしようとしたときに、Web アプリで発生する処理に似ています&mdash;ユーザーがサインイン ページにリダイレクトされ、元のリソースに戻されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-155">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="3cd68-156">アプリでは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャが保持されます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-156">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="3cd68-157">コントローラーによって、ユーザーが選択したカルチャが Cookie に設定され、ユーザーは元の URI にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-157">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="3cd68-158">Cookie にユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するように、サーバー上に HTTP エンドポイントを確立します。</span><span class="sxs-lookup"><span data-stu-id="3cd68-158">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="3cd68-159">`LocalRedirect` アクションの結果を使用して、オープン リダイレクト攻撃を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="3cd68-159">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="3cd68-160">詳細については、「<xref:security/preventing-open-redirects>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3cd68-160">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="3cd68-161">次のコンポーネントでは、ユーザーがカルチャを選択したときに、最初のリダイレクトを実行する方法の例を示します。</span><span class="sxs-lookup"><span data-stu-id="3cd68-161">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3cd68-162">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3cd68-162">Additional resources</span></span>

* <xref:fundamentals/localization>
