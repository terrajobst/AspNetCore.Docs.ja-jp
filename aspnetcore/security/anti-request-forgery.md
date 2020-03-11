---
title: ASP.NET Core でのクロスサイト要求偽造 (XSRF/CSRF) 攻撃を防ぐ
author: steve-smith
description: 悪意のある web サイトがクライアントブラウザーとアプリの間の対話に影響を与える可能性がある web アプリに対する攻撃を防ぐ方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: security/anti-request-forgery
ms.openlocfilehash: 3da73b8fe3e3d73d5d7754e0642e55feeb785de3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651776"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>ASP.NET Core でのクロスサイト要求偽造 (XSRF/CSRF) 攻撃を防ぐ

[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)、[上田 Smith](https://ardalis.com/)

クロスサイトリクエストフォージェリ（XSRF または CSRF とも呼ばれます）は、Web でホストされているアプリへの攻撃であり、それによって悪意のある Web アプリが、クライアントのブラウザーとそのブラウザーを信頼している Web アプリとのやり取りに影響を及ぼします。 Web ブラウザーは、Web サイトへのリクエストの度にある種の認証トークンを送信しているため、攻撃が可能です。 この形式の悪用は、*ワンクリック攻撃*または*セッション*によっても知られています。これは、攻撃がユーザーの以前に認証されたセッションを利用しているためです。

CSRF 攻撃の例を次に示します。

1. ユーザーは、フォーム認証を使用して `www.good-banking-site.com` にサインインします。 サーバーはユーザーを認証し、認証 Cookie を含む応答を返します。 このサイトは有効な認証 Cookie を持った要求を全て信頼しているため、攻撃に対して脆弱です。
1. ユーザーが悪意のあるサイトにアクセスして `www.bad-crook-site.com`。

   悪意のあるサイトの `www.bad-crook-site.com`には、次のような HTML フォームが含まれています。

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   フォームの `action` が、悪意のあるサイトではなく、脆弱なサイトに投稿されていることに注意してください。 これは、CSRF の "cross-site" の一部です。

1. ユーザーは [送信] ボタンを選択します。 ブラウザーによって要求が作成され、要求されたドメインの認証 cookie `www.good-banking-site.com`が自動的に含まれます。
1. 要求は、ユーザーの認証コンテキストを使用して `www.good-banking-site.com` サーバーで実行され、認証されたユーザーが実行を許可されている任意のアクションを実行できます。

ユーザーがフォームの送信ボタンを選択するシナリオに加えて、悪意のあるサイトは以下のことを行う可能性があります。

* 自動的にフォームの送信をするスクリプトの実行
* フォームの送信を AJAX 要求として送信
* CSS を使用してフォームを非表示

これらのシナリオでは、最初に悪意のあるサイトにアクセスする以外に、ユーザーの操作や入力は必要ありません。

HTTPS を使用しても CSRF 攻撃を防ぐことはできません。 悪意のあるサイトは、セキュリティで保護されていない要求を送信するのと同じくらい簡単に `https://www.good-banking-site.com/` 要求を送信できます。

GET 要求の応答のエンドポイントを標的とした攻撃もあります。その場合、イメージタグがアクションの実行によく使われます。 この形式の攻撃は、画像は許可するが JavaScript はブロックするフォーラムのサイトでよくあります。 GETメソッドの変数やリソースの変更によって状態を変更するアプリは、悪意のある攻撃に対して脆弱です。 **状態を変更する GET 要求は安全ではありません。GET 要求の状態を変更しないことをお勧めします。**

認証に Cookie を使用する Web アプリに対して、CSRF 攻撃が可能です。理由は以下です。

* ブラウザーは Web アプリが発行した Cookie を保存します。
* 保存された Cookie には、認証されたユーザーのセッション Cookie が含まれています。
* ブラウザーは、ブラウザー内でアプリへのリクエストがどのように生成されたかにかかわらず、Web アプリの全ての要求に対しドメインに関連する全ての Cookie を送信します。

しかし、CSRF 攻撃は Cookie の悪用だけではありません。 例えば、ベーシック認証やダイジェスト認証も脆弱です。 ユーザーが基本認証またはダイジェスト認証を使用してサインインすると、セッション&dagger; が終了するまで、ブラウザーは資格情報を自動的に送信します。

このコンテキストでは、*セッション*とは、ユーザーが認証されるクライアント側のセッションを指します。 &dagger; サーバー側セッションや[ASP.NET Core セッションミドルウェア](xref:fundamentals/app-state)とは無関係です。

ユーザーは、次の対策をとることで CSRF の脆弱性を防ぐことができます。

* 使用が終わったら、Web アプリからサインオフする
* 定期的にブラウザーの Cookie をクリアする

ただし、CSRF の脆弱性は基本的に Web アプリの問題であり、エンドユーザーの問題ではありません。

## <a name="authentication-fundamentals"></a>認証の基礎

Cookie ベースの認証は一般的な認証方式です。 トークンベースの認証システムは、特にシングルページアプリケーション（SPA）で人気が高まっています。

### <a name="cookie-based-authentication"></a>Cookie ベースの認証

ユーザーがユーザー名とパスワードを使って認証すると、ユーザーが認証と承認ができる認証チケットを含むトークンが発行されます。 トークンは、クライアントが生成する全てのリクエストに含まれる Cookie として保存されます。 この Cookie の生成と検証は Cookie 認証ミドルウェアによって処理されます。 [ミドルウェア](xref:fundamentals/middleware/index)は、ユーザープリンシパルを暗号化された cookie にシリアル化します。 後続の要求では、ミドルウェアが cookie を検証し、プリンシパルを再作成し、そのプリンシパルを[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)の[User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)プロパティに割り当てます。

### <a name="token-based-authentication"></a>トークンベースの認証

ユーザーが認証されると、（偽造防止トークンとは別の）トークンが発行されます。 このトークンには、[要求](/dotnet/framework/security/claims-based-identity-model)の形式でユーザー情報が含まれているか、アプリがアプリで保持されているユーザー状態を示す参照トークンが含まれています。 ユーザーが認証の必要なリソースにアクセスしようとすると、ベアラートークンの形式で追加の認証ヘッダーを付与して、アプリにトークンが送信されます。 これによってアプリはステートレスになります。 以降の要求では、サーバーサイドでの検証のためにトークンが渡されます。 このトークンは*暗号化さ*れていません。これは*エンコード*されます。 サーバーでは、この情報にアクセスするためにトークンがデコードされます。 以降のリクエストでトークンを送信する際は、ブラウザーのローカルストレージに保存します。 トークンがブラウザーのローカルストレージに保存された場合、CSRF の脆弱性の心配はありません。 トークンが Cookie に保存されている場合は、CSRF の懸念があります。 詳細については、GitHub の issue [SPA のコードサンプル](https://github.com/dotnet/AspNetCore.Docs/issues/13369)を参照してください。

### <a name="multiple-apps-hosted-at-one-domain"></a>1 つのドメインでホストされている複数のアプリ

共有のホスティング環境では、セッションハイジャック、ログインの CSRFやその他の攻撃に対して脆弱です。

`example1.contoso.net` と `example2.contoso.net` は異なるホストですが、`*.contoso.net` ドメインにあるホスト間には暗黙の信頼関係があります。 この暗黙の信頼関係により、潜在的に信頼できないホストに対して互いの Cookie が影響を及ぼす可能性があります（AJAX 要求を管理する同一オリジンポリシーは、HTTP の Cookie には必ずしも適用されるわけではありません）。

同じドメインでホストされているアプリ感の信頼された Cookie を悪用した攻撃は、ドメインを共有しないことで防ぐことができます。 アプリがそれぞれ各自のドメインでホストされていれば、悪用できる信頼関係をもつ暗黙的な Cookie はありません。

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core の偽造防止の構成

> [!WARNING]
> ASP.NET Core は[ASP.NET Core データ保護](xref:security/data-protection/introduction)を使用してアンチ偽造を実装します。 データ保護のスタックは、サーバー ファームで動作するように構成する必要があります。 詳細については、「[データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。

::: moniker range=">= aspnetcore-3.0"

次のいずれかの Api が `Startup.ConfigureServices`で呼び出されると、アンチ偽造ミドルウェアが[依存関係の注入](xref:fundamentals/dependency-injection)コンテナーに追加されます。

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> が呼び出されると、アンチ偽造ミドルウェアが[依存関係の注入](xref:fundamentals/dependency-injection)コンテナーに追加され `Startup.ConfigureServices`

::: moniker-end

ASP.NET Core 2.0 以降では、 [Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper)は、アンチ偽造トークンを HTML フォーム要素に挿入します。 Razor ファイルの次のマークアップには、偽造防止トークンが自動的に生成されます:

```cshtml
<form method="post">
    ...
</form>
```

同様に、フォームのメソッドが GET でない場合、 [Ihtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform)は既定で偽造防止トークンを生成します。

HTML フォーム要素のアンチ偽造トークンの自動生成は、`<form>` タグに `method="post"` 属性が含まれていて、次のいずれかに該当する場合に発生します。

* Action 属性が空です (`action=""`)。
* Action 属性が指定されていません (`<form method="post">`)。

HTML フォーム要素で偽造防止トークンの自動生成を無効にすることができます:

* `asp-antiforgery` 属性を使用してアンチ偽造トークンを明示的に無効にします。

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Form 要素は、タグヘルパー [! オプトアウトシンボル](xref:mvc/views/tag-helpers/intro#opt-out)を使用してタグヘルパーをオプトアウトします。

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* ビューから `FormTagHelper` を削除します。 `FormTagHelper` は、次のディレクティブを Razor ビューに追加することで、ビューから削除できます。

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor Pages](xref:razor-pages/index)は XSRF/csrf から自動的に保護されます。 詳細については、「 [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf)」を参照してください。

CSRF 攻撃を防ぐための最も一般的な方法は、*シンクロナイザートークンパターン*(STP) を使用することです。 STPは、ユーザーがフォームデータを含むページを要求したときに使用されます:

1. サーバーは、現在のユーザーの ID に関連したトークンをクライアントに送信します。
1. クライアントは、検証のためにトークンをサーバーに送り返します。
1. サーバーが認証されたユーザーの ID と一致しないトークンを受け取った場合、要求は拒否されます。

トークンは一意で予測不可能です。 このトークンは、一連の要求を適切にシーケンス処理するためにも使用できます (たとえば、ページ 1 &ndash; ページ 2 &ndash; ページ 3)。 ASP.NET Core MVC と Razor Pages のテンプレートの全てのフォームで偽造防止トークンは生成できます。 次の2つのビューの例で、偽造防止トークンを生成します:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

タグヘルパーを HTML ヘルパー [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken)と共に使用せずに、`<form>` 要素にアンチ偽造トークンを明示的に追加します。

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

上の 2 つのケースでは、ASP.NET Core は次の隠しフォームフィールドを追加します:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core には、偽造防止トークンを操作するための3つの[フィルター](xref:mvc/controllers/filters)が含まれています。

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [Autovalidateアンチ Forgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [Ignoreアンチ Forgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>偽造防止のオプション

`Startup.ConfigureServices`で[偽造防止オプション](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions)をカスタマイズします。

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

[Cookiebuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder)クラスのプロパティを使用して、アンチ偽造 `Cookie` プロパティを設定 &dagger;ます。

| オプション | 説明 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 偽造防止の Cookie の作成に使用する設定を決定します。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 偽造防止トークンをビューに表示するために偽造防止システムで使われる、隠しフォームフィールドの名前。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 偽造防止システムで使用されるヘッダーの名前。 `null`場合、システムはフォームデータのみを考慮します。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | `X-Frame-Options` ヘッダーの生成を抑制するかどうかを指定します。 デフォルトでは、ヘッダーは "SAMEORIGIN"の値で生成されます。 既定値は `false` です。 |

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
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | 偽造防止の Cookie の作成に使用する設定を決定します。 |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Cookie のドメイン。 既定値は `null` です。 このプロパティは互換性のために残されていますが、今後のバージョンでは削除される予定です。 別の方法として、"Cookie. ドメイン" をお勧めします。 |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | クッキーの名前。 設定されていない場合、システムは、 [Defaultcookieprefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("で始まる一意の名前を生成します。AspNetCore。 ")。 このプロパティは互換性のために残されていますが、今後のバージョンでは削除される予定です。 別の方法として、Cookie.Name をお勧めします。 |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | クッキーに設定されたパス。 このプロパティは互換性のために残されていますが、今後のバージョンでは削除される予定です。 別の方法として、Cookie. Path をお勧めします。 |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | 偽造防止トークンをビューに表示するために偽造防止システムで使われる、隠しフォームフィールドの名前。 |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | 偽造防止システムで使用されるヘッダーの名前。 `null`場合、システムはフォームデータのみを考慮します。 |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | 偽造防止システムによって HTTPS が要求されるかどうかを指定します。 `true`場合、HTTPS 以外の要求は失敗します。 既定値は `false` です。 このプロパティは互換性のために残されていますが、今後のバージョンでは削除される予定です。 別の方法として、Cookie の SecurePolicy を設定することをお勧めします。 |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | `X-Frame-Options` ヘッダーの生成を抑制するかどうかを指定します。 デフォルトでは、ヘッダーは "SAMEORIGIN"の値で生成されます。 既定値は `false` です。 |

::: moniker-end

詳細については、「 [Cookieauthenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions)」を参照してください。

## <a name="configure-antiforgery-features-with-iantiforgery"></a>IAntiforgery 偽造防止の機能を構成する

[Iアンチ偽造](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)は、偽造防止機能を構成するための API を提供します。 `IAntiforgery` は、`Startup` クラスの `Configure` メソッドで要求できます。 次の例では、ミドルウェアを使用して、アプリのホームページから偽造防止トークンを生成し、それを Cookie として応答を送信しています(このトピックで後述する Angular のデフォルトの命名規則を使っています):

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

[Validateアンチ Forgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)は、個々のアクション、コントローラー、またはグローバルに適用できるアクションフィルターです。 このフィルターが適用したアクションへの要求は、有効な偽造防止トークンが含まていなければブロックされます。

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

`ValidateAntiForgeryToken` 属性には、HTTP GET 要求を含む、マークするアクションメソッドへの要求にトークンが必要です。 `ValidateAntiForgeryToken` 属性がアプリのコントローラー全体に適用される場合は、`IgnoreAntiforgeryToken` 属性でオーバーライドできます。

> [!NOTE]
> ASP.NET Core では、GET 要求に対して偽造防止トークンの自動的な追加はサポートしていません。

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>安全でない HTTP メソッドのみ偽造防止トークンを自動的に検証

ASP.NET Core アプリケーションでは、安全な HTTP メソッド（GET、HEAD、OPTIONS、および TRACE）に対して偽造防止トークンを生成しません。 `ValidateAntiForgeryToken` 属性を広範に適用し、それを `IgnoreAntiforgeryToken` 属性でオーバーライドするのではなく、 [Autovalidateアンチ Forgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)属性を使用できます。 この属性は、`ValidateAntiForgeryToken` 属性と同じように動作しますが、次の HTTP メソッドを使用して行われた要求に対してトークンを必要としない点が異なります。

* GET
* HEAD
* OPTIONS
* TRACE

非 API のシナリオでは、`AutoValidateAntiforgeryToken` を幅広く使用することをお勧めします。 これで POST のアクションはデフォルトで保護されます。 代替手段は、`ValidateAntiForgeryToken` が個々のアクションメソッドに適用されない限り、既定で偽造防止トークンを無視することです。 このシナリオでは、POST のアクションメソッドが誤って保護されずにアプリが CSRF 攻撃に対して脆弱のままになる可能性が高くなります。 すべての POST メソッドが偽造防止トークンを送信すべきです。

API は、トークンの Cookie 以外の部分を送信する自動的なメカニズムはありません。 その実装はクライアントのコードの実装に依存するでしょう。 以下でいくつかの例を挙げます。

クラスレベルの例:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

グローバルな例:

::: moniker range="< aspnetcore-3.0"

サーヴィス.AddMvc (オプション = > オプション。Filters。 Add (新しい Autovalidateアンチ Forgerytokenattribute ());

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>グローバルまたはコントローラーの偽造防止属性の上書き

[Ignoreアンチ Forgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)フィルターは、特定のアクション (またはコントローラー) に対して偽造防止トークンが不要になるようにするために使用されます。 このフィルターを適用すると、より高いレベルで指定された `ValidateAntiForgeryToken` および `AutoValidateAntiforgeryToken` フィルター (グローバルまたはコントローラー上) が上書きされます。

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

## <a name="javascript-ajax-and-spas"></a>JavaScript、AJAX、および SPA

従来の HTML ベースのアプリでは、偽造防止トークンは隠しフォーム フィールドを使ってサーバーに渡されていました。 最新の JavaScript ベースや SPA では多くの要求がプログラムによって行われています。 これらの AJAX 要求は、トークンを送信するための他の技術（要求ヘッダーや Cookie）を使うことがあります。

Cookie に認証トークンを保存しサーバーの API の要求で認証するために使われるなら、CSRF は潜在的な問題です。 トークンの保存にローカルストレージを使用すると CSRF の脆弱性が軽減される可能性があります。なぜならローカルストレージの値は、全ての要求は自動でサーバーに送信されないからです。 したがって、クライアントの偽造防止トークンの保存にローカルストレージを使い、要求ヘッダーとしてトークンを送信することがお薦めのアプローチです。

### <a name="javascript"></a>JavaScript

ビューで JavaScript を使うとビュー内でサービスを使ってトークンを作ることができます。 ビューに[AspNetCore の偽造](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery)サービスを挿入し、 [Getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)を呼び出します。

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

このアプローチで、サーバーから Cookie を設定したりクライアントから Cookie を読み取る必要が無くなります。

上の例では、JavaScript を使用して AJAX の POST のヘッダーの非表示フィールドの値を読み取ります。

JavaScriptは Cookie 内のトークンにアクセスし、その Cookie の内容を使ってトークンの値を持つヘッダーを作成することもできます。

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

スクリプトが `X-CSRF-TOKEN`というヘッダーにトークンを送信するように要求した場合、`X-CSRF-TOKEN` ヘッダーを検索するようにアンチ偽造サービスを構成します。

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

AngularJS では CSRF に対処する規約があります。 サーバーが `XSRF-TOKEN`という名前のクッキーを送信した場合、サーバーに要求を送信するときに、AngularJS `$http` サービスによって cookie の値がヘッダーに追加されます。 このプロセスは自動的に実行されます。 クライアントで明示的にヘッダーを設定する必要はありません。 ヘッダー名が `X-XSRF-TOKEN`。 サーバーはこのヘッダー検出して検証する必要があります。

ASP.NET Core の API では、アプリケーションの起動時にこの規約が動作するようにします:

* `XSRF-TOKEN`という cookie にトークンを提供するようにアプリを構成します。
* アンチ偽造サービスを構成して、`X-XSRF-TOKEN`という名前のヘッダーを探します。

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

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="extend-antiforgery"></a>偽造防止の拡張

[Iアンチ Forgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider)型を使用すると、開発者は各トークンの追加データをラウンドトリップさせることで、csrf システムの動作を拡張できます。 [Getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata)メソッドは、フィールドトークンが生成されるたびに呼び出され、戻り値は生成されたトークン内に埋め込まれます。 実装者は、タイムスタンプ、nonce、またはその他の値を返し、 [Validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata)を呼び出して、トークンが検証されたときにこのデータを検証することができます。 クライアントのユーザー名は既に生成されたトークンに埋め込まれているので、この情報を含める必要はありません。 トークンに補足データが含まれていても `IAntiForgeryAdditionalDataProvider` が構成されていない場合、補足データは検証されません。

## <a name="additional-resources"></a>その他のリソース

* [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (owasp) での[csrf](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 。
* <xref:host-and-deploy/web-farm>
