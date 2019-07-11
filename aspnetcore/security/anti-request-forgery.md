---
title: ASP.NET Core で防ぐクロスサイト リクエスト フォージェリ (XSRF または CSRF) 攻撃
author: steve-smith
description: 悪意のある web サイトがクライアント ブラウザーとアプリ間のやり取りに影響する web アプリに対する攻撃を防ぐ方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: a32e0e2dbd7fab95562a562cb88767d4c1e8049d
ms.sourcegitcommit: c5339594101d30b189f61761275b7d310e80d18a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2019
ms.locfileid: "66458486"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>ASP.NET Core で防ぐクロスサイト リクエスト フォージェリ (XSRF または CSRF) 攻撃

著者: [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), [Rick Anderson](https://twitter.com/RickAndMSFT)

クロスサイトリクエストフォージェリ（XSRFまたはCSRFとも呼ばれます）は、Web でホストされているアプリへの攻撃であり、それによって悪意のある Web アプリが、クライアントのブラウザーとそのブラウザーを信頼している Web アプリとのやり取りに影響を及ぼします。Web ブラウザーは、Web サイトへのリクエストの度にある種の認証トークンを送信しているため、攻撃が可能です。この形式の攻撃は、ワンクリック攻撃やセッションライディングとしても知られています。これは、 攻撃者がユーザーの認証済みのセッションを利用するためです。

CSRF 攻撃の例:

1. ユーザーがフォーム認証を使用して `www.good-banking-site.com` にサインインします。 サーバーはユーザーを認証し、認証 Cookie を含む応答を返します。このサイトは有効な認証 Cookie を持った要求を全て信頼しているため、攻撃に対して脆弱です。
1. ユーザーは、悪意のあるサイトである `www.bad-crook-site.com` にアクセスします。

   悪意のあるサイトである `www.bad-crook-site.com` では、次のような HTML フォームが含まれているとします:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   注意として、フォームの `action` は脆弱性のあるサイトにポストしており、悪意のあるサイトへではありません。これは、CSRF の "cross-site" の一部です。

1. ブラウザーが要求を行うと、自動的に、要求先のドメインである `www.good-banking-site.com` の認証 Cookie が含まれます。
1. 要求は、ユーザーの認証コンテキストを持った状態で `www.good-banking-site.com` サーバー上で実行されるので、認証されたユーザーに許可されている任意のアクションを実行できます。

ユーザーがフォームの送信ボタンを選択するシナリオに加えて、悪意のあるサイトは以下のことを行う可能性があります。

* 自動的にフォームの送信をするスクリプトの実行
* フォームの送信を AJAX 要求として送信
* CSS を使用してフォームを非表示

これらのシナリオでは、最初に悪意のあるサイトにアクセスする以外に、ユーザーの操作や入力は必要ありません。

HTTPS を使用しても CSRF 攻撃を防ぐことはできません。悪意のあるサイトは、セキュアではないリクエストを送信するの同じくらい容易に `https://www.good-banking-site.com/` へリクエストを送信することができます。

GET 要求の応答のエンドポイントを標的とした攻撃もあります。その場合、イメージタグがアクションの実行によく使われます。この形式の攻撃は、画像は許可するが JavaScript はブロックするフォーラムのサイトでよくあります。GETメソッドの変数やリソースの変更によって状態を変更するアプリは、悪意のある攻撃に対して脆弱です。**状態を変更する GET 要求はセキュアではありません。ベストプラクティスは、GET 要求で状態を変更しないことです。**


認証に Cookie を使用する Web アプリに対して、CSRF 攻撃が可能です。理由は以下です。

* ブラウザーは Web アプリが発行した Cookie を保存します。
* 保存された Cookie には、認証されたユーザーのセッション Cookie が含まれています。
* ブラウザーは、ブラウザー内でアプリへのリクエストがどのように生成されたかにかかわらず、Web アプリの全ての要求に対しドメインに関連する全ての Cookie を送信します。

しかし、CSRF 攻撃は Cookie の悪用だけではありません。例えば、ベーシック認証やダイジェスト認証も脆弱です。ベーシック認証やダイジェスト認証でサインインした後、ブラウザーは動的にセッション&dagger;が終了するまで自動的に資格情報を送信します。

&dagger;ここでの *セッション* は、ユーザーが認証されている間のクライアントサイドのセッションを指します。サーバーサイドのセッションや[ASP.NET Core のセッションミドルウェア](xref:fundamentals/app-state)とは無関係です。

ユーザーは、次の対策をとることで CSRF の脆弱性を防ぐことができます。

* 使用が終わったら、Web アプリからサインオフする
* 定期的にブラウザーの Cookie をクリアする

ただし、CSRF の脆弱性は基本的に Web アプリの問題であり、エンドユーザーの問題ではありません。

## <a name="authentication-fundamentals"></a>認証の基礎

Cookie ベースの認証は一般的な認証方式です。トークンベースの認証システムは、特にシングルページアプリケーション（SPA）で人気が高まっています。

### <a name="cookie-based-authentication"></a>Cookie ベースの認証

ユーザーがユーザー名とパスワードを使って認証すると、ユーザーが認証と承認ができる認証チケットを含むトークンが発行されます。トークンは、クライアントが生成する全てのリクエストに含まれる Cookie として保存されます。この Cookie の生成と検証は Cookie 認証ミドルウェアによって処理されます。[ミドルウェア](xref:fundamentals/middleware/index)は、ユーザープリンシパルを暗号化した Cookie にシリアル化します。以降の要求では、ミドルウェアが Cookie を検証し、プリンシパルを再作成したりそのプリンシパルを [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext) の [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) プロパティに割り当てます。

### <a name="token-based-authentication"></a>トークンベースの認証

ユーザーが認証されると、（偽造防止トークンとは別の）トークンが発行されます。トークンには、[Claim](/dotnet/framework/security/claims-based-identity-model) 形式のユーザー情報や、アプリ内で保持されているユーザーの状態をアプリに紐づける参照トークンが含まれています。ユーザーが認証の必要なリソースにアクセスしようとすると、ベアラートークンの形式で追加の認証ヘッダーを付与して、アプリにトークンが送信されます。これによってアプリはステートレスになります。以降の要求では、サーバーサイドでの検証のためにトークンが渡されます。このトークンは、*暗号化* されていません。 *エンコード* されています。サーバーでは、この情報にアクセスするためにトークンがデコードされます。以降のリクエストでトークンを送信する際は、ブラウザーのローカルストレージに保存します。トークンがブラウザーのローカルストレージに保存された場合、CSRF の脆弱性の心配はありません。トークンが Cookie に保存されている場合は、CSRF の懸念があります。

### <a name="multiple-apps-hosted-at-one-domain"></a>1つのドメインでホストされている複数のアプリ

共有のホスティング環境では、セッションハイジャック、ログインの CSRFやその他の攻撃に対して脆弱です。

`example1.contoso.net` と `example2.contoso.net` は別のホストですが、 `*.contoso.net` ドメイン下のホストにあるため、暗黙の信頼関係があります。この暗黙の信頼関係により、潜在的に信頼できないホストに対して互いの Cookie が影響を及ぼす可能性があります（AJAX 要求を管理する同一オリジンポリシーは、HTTP の Cookie には必ずしも適用されるわけではありません）。

同じドメインでホストされているアプリ感の信頼された Cookie を悪用した攻撃は、ドメインを共有しないことで防ぐことができます。アプリがそれぞれ各自のドメインでホストされていれば、悪用できる信頼関係をもつ暗黙的な Cookie はありません。

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core の偽造防止の構成

> [!WARNING]
> ASP.NET Core では、[ASP.NET Core データ保護](xref:security/data-protection/introduction)を使用して偽造防止の実装をします。データ保護のスタックは、サーバー ファームで動作するように構成する必要があります。詳しくは、[データ保護の構成](xref:security/data-protection/configuration/overview)を参照してください。

ASP.NET Core 2.0 以降では、 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)が HTML フォームの要素に偽造防止トークンを挿入します。Razor ファイルの次のマークアップには、偽造防止トークンが自動的に生成されます:

```cshtml
<form method="post">
    ...
</form>
```

同様に、 [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) も GET 以外のメソッドの場合、デフォルトで偽造防止トークンを生成します。

HTML フォーム要素の偽造防止トークンの自動生成は、`<form>` タグに `method = "post"` 属性が含まれていて、次のいずれかが当てはまる場合に生成されます:

* Action 属性が空（`action=""`）
* Action 属性が指定差r邸内（`<form method="post">`）

HTML フォーム要素で偽造防止トークンの自動生成を無効にすることができます:

* `asp-antiforgery`属性によって偽造防止トークンを明示的に無効化する:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* フォーム要素で、タグヘルパーの [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out) によって、タグヘルパーがオプトアウトされます:

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* ビューから `FormTagHelper` を削除します。`FormTagHelper` は、Razor ビューに次のディレクティブを追加することで、ビューから削除できます:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor ページ](xref:razor-pages/index)は、自動的に XSRF/CSRF から保護されます。詳細については、[XSRF/CSRF と Razor ページ](xref:razor-pages/index#xsrf) 参照してください。

CSRF 攻撃に対する防御の最も一般的なアプローチは、Synchronizer Token Pattern（STP）を使用することです。 STPは、ユーザーがフォームデータを含むページを要求したときに使用されます:

1. サーバーは、現在のユーザーのの ID に関連したトークンをクライアントに送信します。
1. クライアントは、検証のためにトークンをサーバーに送り返します。
1. サーバーが認証されたユーザーの ID と一致しないトークンを受け取った場合、要求は拒否されます。

トークンは一意で予測不可能です。トークンは、一連の要求の適切な順序付け（例えば、ページ1 - ページ2 - ページ3といった要求順の保証）を保証するためにも使用できます。 ASP.NET Core MVC と Razor Pages のテンプレートの全てのフォームで偽造防止トークンは生成できます。次の2つのビューの例で、偽造防止トークンを生成します:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

タグヘルパー使わず、HTML ヘルパーの[ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken)を使って明示的に偽造防止トークンを追加します:

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

上の2つのケースでは、ASP.NET Core は次の隠しフォームフィールドを追加します:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core では、偽造防止トークンを動作するために3つの[フィルター](xref:mvc/controllers/filters)が含まれています:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>偽造防止のオプション

`Startup.ConfigureServices` で、[偽造防止のオプション](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions)をカスタマイズできます:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger; [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) クラスのプロパティを使って、偽造防止の `Cookie` プロパティを設定できます。

| オプション | 説明 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 偽造防止の Cookie の作成に使用する設定を決定します。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 偽造防止トークンをビューに表示するために偽造防止システムで使われる、隠しフォームフィールドの名前。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 偽造防止システムで使用されるヘッダーの名前。 `null` の場合、システムはフォームデータのみを考慮します。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | `X-Frame-Options` ヘッダーの生成を抑制するかを指定します。デフォルトでは、ヘッダーは "SAMEORIGIN"の値で生成されます。デフォルトは `false` です。 |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| オプション | 説明 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 偽造防止 cookie を作成するために使用する設定を決定します。 |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Cookie のドメイン。 既定値は `null` です。 このプロパティは廃止され、今後のバージョンで削除される予定です。 Cookie.Domain お勧めします。 |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | クッキーの名前。 設定されていないシステム生成一意の名前の先頭に、 [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("。AspNetCore.Antiforgery。")。 このプロパティは廃止され、今後のバージョンで削除される予定です。 Cookie.Name お勧めします。 |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Cookie の設定のパス。 このプロパティは廃止され、今後のバージョンで削除される予定です。 Cookie.Path お勧めします。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | ビューで偽造防止トークンを表示するために、偽造防止システムによって使用される隠しフォーム フィールドの名前。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 偽造防止システムによって使用されるヘッダーの名前。 場合`null`フォームのデータのみが考慮されます。 |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | 偽造防止システムで HTTPS が必要かどうかを指定します。 場合`true`、HTTPS 以外の要求は失敗します。 既定値は `false` です。 このプロパティは廃止され、今後のバージョンで削除される予定です。 Cookie.SecurePolicy を設定することをお勧めします。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | 生成を抑制するかどうかを指定します、`X-Frame-Options`ヘッダー。 既定では、"SAMEORIGIN"の値を持つヘッダーが生成されます。 既定値は `false` です。 |

::: moniker-end

詳細については、[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions) を参照してください。

## <a name="configure-antiforgery-features-with-iantiforgery"></a>IAntiforgery 偽造防止の機能を構成する

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) は偽造防止の機能を構成する API を提供します。 `IAntiforgery` は `Startup` クラスの `Configure` メソッドで要求されます。次の例では、ミドルウェアを使用して、アプリのホームページから偽造防止トークンを生成し、それを Cookie として応答を送信しています(このトピックで後述する Angular のデフォルトの命名規則を使っています):

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>偽造防止の検証の要求

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) は、個々のアクション、コントローラー、またはグローバルに適用できるアクションフィルターです。このフィルターが適用したアクションへの要求は、有効な偽造防止トークンが含まていなければブロックされます。

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

`ValidateAntiForgeryToken` 属性は、HTTP GET の要求も含め、属性の付いたアクションメソッドへの要求にはトークンが必要になります。`ValidateAntiForgeryToken` 属性がアプリのコントローラーで適用されている場合は、`IgnoreAntiforgegeryToken` 属性で上書きできます。

> [!NOTE]
> ASP.NET Core では、GET 要求に対して偽造防止トークンの自動的な追加はサポートしていません。

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>安全でない HTTP メソッドのみ偽造防止トークンを自動的に検証

ASP.NET Core アプリケーションでは、安全な HTTP メソッド（GET、HEAD、OPTIONS、および TRACE）に対して偽造防止トークンを生成しません。明示的に `ValidateAntiForgeryToken` 属性を適用してから `IgnoreAntiforgeryToken` で上書きする代わりとして、[AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 属性を使うことができます。この属性は、`ValidateAntiForgeryToke` 属性と同様に機能しますが、次の HTTP メソッドを使った要求にトークンを必要としません:

* GET
* HEAD
* OPTION
* TRACE

API ではないシナリオでは、`AutoValidateAntiforgeryToken` の仕様をお薦めします。これで POST のアクションはデフォルトで保護されます。別の方法として、`ValidateAntiForgeryToken` が個別にアクションメソッドに適用されていない限り、デフォルトで偽造防止トークンを無効にすることもできます。このシナリオでは、POST のアクションメソッドが誤って保護されずにアプリが CSRF 攻撃に対して脆弱のままになる可能性が高くなります。すべての POST メソッドが偽造防止トークンを送信すべきです。

API は、トークンの Cookie 以外の部分を送信する自動的なメカニズムはありません。その実装はクライアントのコードの実装に依存するでしょう。以下でいくつかの例を挙げます。

クラスレベルの例:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

グローバルの例:

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a>グローバルまたはコントローラーの偽造防止属性の上書き

[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) フィルターを使用して、特定のアクション（またはコントローラー）の偽造防止トークンの必要性を無くすことができます。適用すると、このフィルターはハイレベル（グローバルまたはコントローラー上）で指定された `ValidateAntiForgeryToken` と `AutoValidateAntiforgeryToken` フィルターを上書きします。

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>認証後のリフレッシュトークン

ユーザーの認証後、ビューまたは Razor Pages のページにリダイレクトするときにトークンを更新する必要があります。

## <a name="javascript-ajax-and-spas"></a>JavaScript、AJAX、および SPAs

従来の HTML ベースのアプリでは、偽造防止トークンは隠しフォームフィールドを使ってサーバーに渡されていました。モダンな JavaScript ベースや SPAs では多くの要求がプログラムによって行われています。これらの AJAX 要求は、トークンを送信するための他の技術（要求ヘッダーや Cookie）を使うことがあります。

Cookie に認証トークンを保存しサーバーの API の要求で認証するために使われるなら、CSRF は潜在的な問題です。トークンの保存にローカルストレージを使用すると CSRF の脆弱性が軽減される可能性があります。なぜならローカルストレージの値は、全ての要求に自動でサーバーに送信されないからです。したがって、クライアントの偽造防止トークンの保存にローカルストレージを使い、要求ヘッダーとしてトークンを送信することがお薦めのアプローチです。

### <a name="javascript"></a>JavaScript

ビューで JavaScript を使うとビュー内でサービスを使ってトークンを作ることができます。[Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) サービスをビューに挿入して [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens) を呼び出します:

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

このアプローチで、サーバーから Cookie を設定したりクライアントから Cookie を読み取る必要が無くなります。

上の例では、JavaScript を使用して AJAX の POST のヘッダーの非表示フィールドの値を読み取ります。

JavaScriptは Cookie 内のトークンにアクセスし、その Cookie の内容を使ってトークンの値を持つヘッダーを作成することもできます。

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

スクリプトが `X-CSRF-TOKEN` というヘッダーでトークンの送信を要求すると前提であれば、`X-CSRF-TOKEN` ヘッダーを探すように偽造防止サービスを構成します:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

次の例は、JavaScript を使用して適切なヘッダーで AJAX の要求をします:

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS では CSRF に対処する規約があります。サーバーが `XSRF-TOKEN` という名前の Cookie を送信すると、AngularJS の `$http` サービスはサーバーに要求を送信するとき、ヘッダーに Cookie の値を追加します。このプロセスは自動です。クライアントで明示的にヘッダーを設定する必要はありません。ヘッダーの名前は `X-XSRF-TOKEN` です。サーバーはこのヘッダー検出して検証する必要があります。

ASP.NET Core の API では、アプリケーションの起動時にこの規約が動作するようにします: 

* `XSRF-TOKEN` という Cookie にトークンを提供するようにアプリを構成する
* `X-XSRF-TOKEN` という名前のヘッダーに探すように偽造防止サービスを構成する

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="extend-antiforgery"></a>偽造防止の拡張

開発者は、[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) 型を使用して各トークンの追加のデータをラウンドトリップすることで、CSRF 対策の動作を拡張できます。[GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) メソッドは、フィールドトークンが生成される度に呼ばれ、戻り値は生成されたトークン内に埋め込まれます。実装する人は、タイムスタンプ、nonce、その他の値を返しトークンが検証されたときにデータを検証するために [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) を呼ぶことができます。クライアントのユーザー名は既に生成されたトークンに埋め込まれているので、この情報を含める必要はありません。トークンに補足のデータが含まれていても `IAntiForgeryAdditionalDataProvider` が構成されていなければ、補足のデータは検証されません。

## <a name="additional-resources"></a>その他の技術情報

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page)(OWASP)。
* <xref:host-and-deploy/web-farm>
