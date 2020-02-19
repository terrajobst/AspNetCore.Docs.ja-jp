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
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor のグローバリゼーションとローカリゼーション

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

Razor コンポーネントは、複数のカルチャおよび言語のユーザーがアクセスできるようにすることができます。 次の .NET グローバリゼーションおよびローカリゼーションシナリオを利用できます。

* .NET のリソースシステム
* カルチャ固有の数値と日付の書式設定

現在、次のような ASP.NET Core のローカライズシナリオがサポートされています。

* `IStringLocalizer<>` は Blazor アプリで*サポートされて*います。
* `IHtmlLocalizer<>`、`IViewLocalizer<>`、データ注釈のローカライズは MVC シナリオ ASP.NET Core、Blazor アプリではサポートされて**いません**。

詳細については、<xref:fundamentals/localization> を参照してください。

## <a name="globalization"></a>グローバリゼーション

Blazorの `@bind` 機能は、ユーザーの現在のカルチャに基づいて、形式を実行し、値を解析して表示します。

現在のカルチャには、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。

[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)は、次のフィールドの種類 (`<input type="{TYPE}" />`) に使用されます。

* `date`
* `number`

上記のフィールド型は次のとおりです。

* は、適切なブラウザーベースの書式規則を使用して表示されます。
* 自由形式のテキストを含めることはできません。
* ブラウザーの実装に基づいてユーザーの操作特性を指定します。

次のフィールド型には特定の書式設定要件がありますが、Blazor で現在サポートされていないのは、すべての主要なブラウザーでサポートされていないためです。

* `datetime-local`
* `month`
* `week`

`@bind` では、`@bind:culture` パラメーターを使用して、値の解析および書式設定のための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供します。 `date` および `number` のフィールドの種類を使用する場合は、カルチャを指定しないことをお勧めします。 `date` と `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。

## <a name="localization"></a>ローカリゼーション

Blazor サーバーアプリはローカライズ[ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。 ミドルウェアは、アプリからリソースを要求するユーザーに適切なカルチャを選択します。

カルチャは、次のいずれかの方法を使用して設定できます。

* [Cookie](#cookies)
* [カルチャを選択するための UI を提供する](#provide-ui-to-choose-the-culture)

詳細と例については、<xref:fundamentals/localization> をご覧ください。

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>国際化のためのリンカーの構成 (Blazor WebAssembly

既定では、Blazor WebAssembly に対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。 リンカーの動作を制御する方法の詳細とガイダンスについては、「<xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>」を参照してください。

### <a name="cookies"></a>Cookie

ローカリゼーションカルチャクッキーは、ユーザーのカルチャを保持できます。 Cookie は、アプリのホストページ (*Pages/host. cshtml. .cs*) の `OnGet` メソッドによって作成されます。 ローカリゼーションミドルウェアは、後続の要求で cookie を読み取り、ユーザーのカルチャを設定します。 

Cookie を使用すると、WebSocket 接続がカルチャを正しく伝達できるようになります。 ローカライズスキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが Websocket を使用できない可能性があるため、カルチャを永続化できません。 したがって、ローカライズカルチャ cookie を使用することをお勧めします。

カルチャがローカライズ cookie に保存されている場合は、任意の手法を使用してカルチャを割り当てることができます。 アプリにサーバー側 ASP.NET Core 用に確立されたローカライズスキームが既にある場合は、引き続きアプリの既存のローカライズインフラストラクチャを使用し、アプリのスキーム内でローカライズカルチャ cookie を設定します。

次の例では、ローカリゼーションミドルウェアが読み取ることができる cookie で現在のカルチャを設定する方法を示します。 Blazor Server アプリで次の内容を含む*ページ/ホストの cshtml*ファイルを作成します。

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

ローカライズは、次の一連のイベントでアプリによって処理されます。

1. ブラウザーは、アプリに最初の HTTP 要求を送信します。
1. カルチャは、ローカリゼーションミドルウェアによって割り当てられます。
1. *_Host*の `OnGet` メソッドは、応答の一部として cookie 内のカルチャを永続化します。
1. ブラウザーは、WebSocket 接続を開き、対話型の Blazor サーバーセッションを作成します。
1. ローカリゼーションミドルウェアは cookie を読み取り、カルチャを割り当てます。
1. Blazor サーバーセッションは、正しいカルチャで開始されます。

### <a name="provide-ui-to-choose-the-culture"></a>カルチャを選択するための UI を提供する

ユーザーがカルチャを選択できるように UI を提供するには、*リダイレクトベースのアプローチ*を使用することをお勧めします。 このプロセスは、ユーザーがセキュリティで保護されたリソースにアクセスしようとしたときに、ユーザーがサインインページにリダイレクトされ、元のリソースにリダイレクトされる&mdash;、web アプリで発生する処理に似ています。 

アプリは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャを永続化します。 コントローラーは、ユーザーが選択したカルチャを cookie に設定し、ユーザーを元の URI にリダイレクトします。

Cookie でユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するために、サーバー上に HTTP エンドポイントを確立します。

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
> `LocalRedirect` アクションの結果を使用して、開いているリダイレクト攻撃を防止します。 詳細については、<xref:security/preventing-open-redirects> を参照してください。

次のコンポーネントは、ユーザーがカルチャを選択したときに最初のリダイレクトを実行する方法の例を示しています。

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

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/localization>
