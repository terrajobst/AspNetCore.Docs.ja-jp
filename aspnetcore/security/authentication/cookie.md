---
title: ASP.NET Core Id を指定せずに cookie 認証を使用する
author: rick-anderson
description: ASP.NET Core Id を使用せずに cookie 認証を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/20/2019
uid: security/authentication/cookie
ms.openlocfilehash: 76c7fc20c8870668ca7c65d975e2ed59f40f7dc8
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384827"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>ASP.NET Core Id を指定せずに cookie 認証を使用する

By [Rick Anderson](https://twitter.com/RickAndMSFT)と[Luke latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Id は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。 ただし、ASP.NET Core Id のない cookie ベースの認証認証プロバイダーを使用できます。 詳細については、「 <xref:security/authentication/identity> 」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。 **電子メール**アドレス`maria.rodriguez@contoso.com`と任意のパスワードを使用して、ユーザーにサインインします。 ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs*ファイルのメソッドで認証されます。 実際の例では、ユーザーはデータベースに対して認証されます。

## <a name="configuration"></a>構成

アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。

メソッドで、メソッド<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>と<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>メソッドを使用して認証ミドルウェアサービスを作成します。 `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>に`AddAuthentication`渡されたは、アプリの既定の認証スキームを設定します。 `AuthenticationScheme`は、cookie 認証のインスタンスが複数存在し、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。 を cookieauthenticationdefaults に設定[します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。 `AuthenticationScheme` スキームを区別する任意の文字列値を指定できます。

アプリの認証方式は、アプリの cookie 認証スキームとは異なります。 クッキー認証スキームがに<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>指定されていない場合は、(cookie) を使用`CookieAuthenticationDefaults.AuthenticationScheme`します。

認証 cookie の<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>プロパティは、既定で`true`に設定されています。 サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。 詳細については、「 <xref:security/gdpr#essential-cookies> 」を参照してください。

で`Startup.Configure`、と`UseAuthentication`を呼び出して、 `HttpContext.User`プロパティを設定し、要求の承認ミドルウェアを実行します。 `UseAuthorization` `UseAuthentication`を`UseAuthorization`呼び出す前`UseEndpoints`に、メソッドとメソッドを呼び出します。

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>クラスは、認証プロバイダーオプションを構成するために使用されます。

`Startup.ConfigureServices`メソッド`CookieAuthenticationOptions`の認証用にサービス構成でを設定します。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>クッキーポリシーミドルウェア

Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。 アプリ処理パイプラインへのミドルウェアの追加は順序に&mdash;依存しており、パイプラインに登録されている下流コンポーネントにのみ影響します。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Cookie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするために、クッキーポリシーミドルウェアに提供されているを使用します。

既定<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy>値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。 と同じサイトポリシー `SameSiteMode.Strict`を厳密に適用するには`MinimumSameSitePolicy`、を設定します。 この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

の`MinimumSameSitePolicy` Cookie ポリシーミドルウェア設定は、次の表に`Cookie.SameSite`従っ`CookieAuthenticationOptions`て設定内のの設定に影響を与える可能性があります。

| MinimumSameSitePolicy | Cookie.SameSite | 結果の SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>認証 cookie を作成する

ユーザー情報を保持する cookie を作成するに<xref:System.Security.Claims.ClaimsPrincipal>は、を構築します。 ユーザー情報がシリアル化され、cookie に格納されます。 

任意の<xref:System.Security.Claims.ClaimsIdentity>が必要<xref:System.Security.Claims.Claim>なを使用し<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>てを作成し、を呼び出してユーザーにサインインします。

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`暗号化されたクッキーを作成し、現在の応答に追加します。 が`AuthenticationScheme`指定されていない場合は、既定のスキームが使用されます。

暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。 複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトして cookie を削除するに<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>は、次のように呼び出します。

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

( `CookieAuthenticationDefaults.AuthenticationScheme`または "cookie") がスキームとして使用されていない場合 (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。 それ以外の場合は、既定のスキームが使用されます。

## <a name="react-to-back-end-changes"></a>バックエンドの変更に対処する

Cookie が作成されると、cookie は id の単一のソースになります。 バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。

* アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。
* 認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。 すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。

Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。 ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。 サンプルアプリでは、データベースはに`IUserRepository`実装され、値を`LastChanged`格納します。 データベースでユーザーが更新されると、 `LastChanged`値は現在の時刻に設定されます。

データベースが`LastChanged`値に基づいて変更されたときに cookie を無効にするには、 `LastChanged`データベースから現在`LastChanged`の値を含むクレームを使用して cookie を作成します。

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

`ValidatePrincipal`イベントのオーバーライドを実装するには、から<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>派生するクラスに次のシグネチャを持つメソッドを記述します。

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

の`CookieAuthenticationEvents`実装例を次に示します。

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Cookie サービスの登録時に、 `Startup.ConfigureServices`メソッドでイベントインスタンスを登録します。 `CustomCookieAuthenticationEvents`クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定します。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

ユーザーの名前が、セキュリティに影響しない決定&mdash;をどのような方法でも更新している場合を考えてみます。 ユーザープリンシパルを非破壊更新する場合は、を呼び出し`context.ReplacePrincipal` 、 `context.ShouldRenew`プロパティをに`true`設定します。

> [!WARNING]
> ここで説明する方法は、すべての要求でトリガーされます。 すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。

## <a name="persistent-cookies"></a>永続的な cookie

Cookie がブラウザーセッション間で保持されるようにすることもできます。 この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。 

次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。 以前に構成したスライド式有効期限の設定はすべて受け入れられます。 ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。

をに`true`設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>します。<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>クッキーの絶対有効期限

絶対有効期限は、で<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>設定できます。 永続的なクッキーを作成する`IsPersistent`には、も設定する必要があります。 それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。 を`ExpiresUtc`設定すると、の<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>オプションの値がオーバーライドされます (設定されている場合)。

次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。 これにより、以前に構成したスライド式有効期限の設定は無視されます。

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core Id は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。 ただし、ASP.NET Core Id のない cookie ベースの認証認証プロバイダーを使用できます。 詳細については、「 <xref:security/authentication/identity> 」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。 **電子メール**アドレス`maria.rodriguez@contoso.com`と任意のパスワードを使用して、ユーザーにサインインします。 ユーザーは、 `AuthenticateUser` *Pages/Account/Login. cshtml. .cs*ファイルのメソッドで認証されます。 実際の例では、ユーザーはデータベースに対して認証されます。

## <a name="configuration"></a>構成

アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。

メソッドで、メソッド<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>と<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>メソッドを使用して認証ミドルウェアサービスを作成します。 `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>に`AddAuthentication`渡されたは、アプリの既定の認証スキームを設定します。 `AuthenticationScheme`は、cookie 認証のインスタンスが複数存在し、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。 を cookieauthenticationdefaults に設定[します。 authenticationscheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。 `AuthenticationScheme` スキームを区別する任意の文字列値を指定できます。

アプリの認証方式は、アプリの cookie 認証スキームとは異なります。 クッキー認証スキームがに<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>指定されていない場合は、(cookie) を使用`CookieAuthenticationDefaults.AuthenticationScheme`します。

認証 cookie の<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>プロパティは、既定で`true`に設定されています。 サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。 詳細については、「 <xref:security/gdpr#essential-cookies> 」を参照してください。

メソッドで、 `UseAuthentication`メソッドを呼び出して、 `HttpContext.User`プロパティを設定する認証ミドルウェアを呼び出します。 `Startup.Configure` または`UseAuthentication` `UseMvcWithDefaultRoute`を呼び出す前に、メソッドを呼び出します。 `UseMvc`

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>クラスは、認証プロバイダーオプションを構成するために使用されます。

`Startup.ConfigureServices`メソッド`CookieAuthenticationOptions`の認証用にサービス構成でを設定します。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>クッキーポリシーミドルウェア

Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。 アプリ処理パイプラインへのミドルウェアの追加は順序に&mdash;依存しており、パイプラインに登録されている下流コンポーネントにのみ影響します。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Cookie <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするために、クッキーポリシーミドルウェアに提供されているを使用します。

既定<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy>値は、 `SameSiteMode.Lax` OAuth2 認証を許可することです。 と同じサイトポリシー `SameSiteMode.Strict`を厳密に適用するには`MinimumSameSitePolicy`、を設定します。 この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

の`MinimumSameSitePolicy` Cookie ポリシーミドルウェア設定は、次の表に`Cookie.SameSite`従っ`CookieAuthenticationOptions`て設定内のの設定に影響を与える可能性があります。

| MinimumSameSitePolicy | Cookie.SameSite | 結果の SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>認証 cookie を作成する

ユーザー情報を保持する cookie を作成するに<xref:System.Security.Claims.ClaimsPrincipal>は、を構築します。 ユーザー情報がシリアル化され、cookie に格納されます。 

任意の<xref:System.Security.Claims.ClaimsIdentity>が必要<xref:System.Security.Claims.Claim>なを使用し<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>てを作成し、を呼び出してユーザーにサインインします。

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`暗号化されたクッキーを作成し、現在の応答に追加します。 が`AuthenticationScheme`指定されていない場合は、既定のスキームが使用されます。

暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。 複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトして cookie を削除するに<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>は、次のように呼び出します。

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

( `CookieAuthenticationDefaults.AuthenticationScheme`または "cookie") がスキームとして使用されていない場合 (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。 それ以外の場合は、既定のスキームが使用されます。

## <a name="react-to-back-end-changes"></a>バックエンドの変更に対処する

Cookie が作成されると、cookie は id の単一のソースになります。 バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。

* アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。
* 認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。 すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。

Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。 ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。 サンプルアプリでは、データベースはに`IUserRepository`実装され、値を`LastChanged`格納します。 データベースでユーザーが更新されると、 `LastChanged`値は現在の時刻に設定されます。

データベースが`LastChanged`値に基づいて変更されたときに cookie を無効にするには、 `LastChanged`データベースから現在`LastChanged`の値を含むクレームを使用して cookie を作成します。

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

`ValidatePrincipal`イベントのオーバーライドを実装するには、から<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>派生するクラスに次のシグネチャを持つメソッドを記述します。

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

の`CookieAuthenticationEvents`実装例を次に示します。

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Cookie サービスの登録時に、 `Startup.ConfigureServices`メソッドでイベントインスタンスを登録します。 `CustomCookieAuthenticationEvents`クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定します。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

ユーザーの名前が、セキュリティに影響しない決定&mdash;をどのような方法でも更新している場合を考えてみます。 ユーザープリンシパルを非破壊更新する場合は、を呼び出し`context.ReplacePrincipal` 、 `context.ShouldRenew`プロパティをに`true`設定します。

> [!WARNING]
> ここで説明する方法は、すべての要求でトリガーされます。 すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。

## <a name="persistent-cookies"></a>永続的な cookie

Cookie がブラウザーセッション間で保持されるようにすることもできます。 この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。 

次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。 以前に構成したスライド式有効期限の設定はすべて受け入れられます。 ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。

をに`true`設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>します。<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>クッキーの絶対有効期限

絶対有効期限は、で<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>設定できます。 永続的なクッキーを作成する`IsPersistent`には、も設定する必要があります。 それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。 を`ExpiresUtc`設定すると、の<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>オプションの値がオーバーライドされます (設定されている場合)。

次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。 これにより、以前に構成したスライド式有効期限の設定は無視されます。

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [ポリシーベースのロールチェック](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
