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
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Core Blazor のグローバリゼーションおよびローカライズ

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)

複数のカルチャと言語でユーザーが Razor コンポーネントにアクセスできるようにすることができます。 利用できる .NET のグローバリゼーションおよびローカライズのシナリオは、次のとおりです。

* .NET のリソース システム
* カルチャ固有の数値と日付の書式

現在、サポートされている ASP.NET Core のローカライズ シナリオは限られています。

* Blazor アプリでは `IStringLocalizer<>` は "*サポートされていません*"。
* `IHtmlLocalizer<>`、`IViewLocalizer<>`、データ注釈のローカライズは ASP.NET Core の MVC シナリオであり、Blazor アプリでは "**サポートされていません**"。

詳細については、「<xref:fundamentals/localization>」を参照してください。

## <a name="globalization"></a>グローバリゼーション

Blazor の `@bind` 機能では、書式設定が実行され、ユーザーの現在のカルチャに基づいて、表示する値が解析されます。

現在のカルチャは、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) は、次のフィールドの型 (`<input type="{TYPE}" />`) に使用されます。

* `date`
* `number`

前のフィールドの型は次のようになります。

* 適切なブラウザー ベースの書式ルールを使用して表示されます。
* 自由形式のテキストを含めることはできません。
* ブラウザーの実装に基づいてユーザー操作の特性を指定します。

次のフィールドの型には、特定の書式設定の要件がありますが、すべての主要なブラウザーでサポートされていないため、Blazor では現在サポートされていません。

* `datetime-local`
* `month`
* `week`

`@bind` では、値の解析および書式設定のための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供する `@bind:culture` パラメーターをサポートしています。 `date` および `number` のフィールドの型を使用する場合は、カルチャを指定しないことをお勧めします。 `date` および `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。

## <a name="localization"></a>ローカリゼーション

Blazor サーバー アプリは、[ローカライズ ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。 ミドルウェアによって、アプリからリソースを要求するユーザーに対して適切なカルチャが選択されます。

カルチャは、次のいずれかの方法を使用して設定できます。

* [Cookie](#cookies)
* [カルチャを選択するための UI を提供する](#provide-ui-to-choose-the-culture)

使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>国際化用にリンカーを構成する (Blazor WebAssembly)

既定では、Blazor WebAssembly に対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。 リンカーの動作を制御する方法の詳細とガイダンスについては、「<xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>」を参照してください。

### <a name="cookies"></a>クッキー

ローカライズ カルチャの Cookie では、ユーザーのカルチャを保持できます。 Cookie は、アプリのホスト ページ (*Pages/Host. cshtml*) の `OnGet` メソッドによって作成されます。 ローカライズ ミドルウェアでは、後続の要求で Cookie を読み取り、ユーザーのカルチャを設定します。 

Cookie を使用すると、WebSocket 接続によってカルチャを正しく伝達できることを確実にします。 ローカライズ スキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが WebSocket を使用できない可能性があるため、カルチャを保持できません。 したがって、ローカライズ カルチャの Cookie を使用することをお勧めします。

カルチャがローカライズ Cookie で保持されている場合は、任意の手法を使用してカルチャを割り当てることができます。 アプリにサーバー側 ASP.NET Core 用に確立されたローカライズ スキームが既にある場合は、アプリの既存のローカライズ インフラストラクチャを引き続き使用し、アプリのスキーム内にローカライズ カルチャ Cookie を設定します。

次の例では、ローカライズ ミドルウェアによって読み取ることができる Cookie で現在のカルチャを設定する方法を示します。 Blazor サーバーアプリで次の内容を使用して、*Pages/Host.cshtml.cs* ファイルを作成します。

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

1. ブラウザーによって、アプリに最初の HTTP 要求が送信されます。
1. カルチャは、ローカライズ ミドルウェアによって割り当てられます。
1. *_Host.cshtml.cs* の `OnGet` メソッドによって、応答の一部として Cookie にカルチャが保持されます。
1. ブラウザーによって、WebSocket 接続が開かれ、対話型の Blazor サーバー セッションが作成されます。
1. ローカライズ ミドルウェアによって、Cookie が読み取られ、カルチャが割り当てられます。
1. Blazor サーバー セッションは、正しいカルチャで開始されます。

### <a name="provide-ui-to-choose-the-culture"></a>カルチャを選択するための UI を提供する

ユーザーがカルチャを選択できるように UI を提供するには、"*リダイレクト ベースのアプローチ*" をお勧めします。 このプロセスは、ユーザーがセキュリティで保護されたリソースにアクセスしようとしたときに、Web アプリで発生する処理に似ています&mdash;ユーザーがサインイン ページにリダイレクトされ、元のリソースに戻されます。 

アプリでは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャが保持されます。 コントローラーによって、ユーザーが選択したカルチャが Cookie に設定され、ユーザーは元の URI にリダイレクトされます。

Cookie にユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するように、サーバー上に HTTP エンドポイントを確立します。

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
> `LocalRedirect` アクションの結果を使用して、オープン リダイレクト攻撃を防ぎます。 詳細については、「<xref:security/preventing-open-redirects>」を参照してください。

次のコンポーネントでは、ユーザーがカルチャを選択したときに、最初のリダイレクトを実行する方法の例を示します。

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

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/localization>
