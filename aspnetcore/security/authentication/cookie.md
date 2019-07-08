---
title: ASP.NET Core Identity なしでの cookie 認証を使用します。
author: rick-anderson
description: ASP.NET Core Identity なしでの cookie 認証を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/07/2019
uid: security/authentication/cookie
ms.openlocfilehash: bbba2e77f806e1ed30bb734763cdbaedc1471d62
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622741"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>ASP.NET Core Identity なしでの cookie 認証を使用します。

によって[Rick Anderson](https://twitter.com/RickAndMSFT)と[Luke Latham](https://github.com/guardrex)

ASP.NET Core Identity は、の作成とログインの管理、フル機能の完全な認証プロバイダーです。 ただし、ASP.NET Core Identity なし、cookie ベースの認証の認証プロバイダーを使用できます。 詳細については、「 <xref:security/authentication/identity> 」を参照してください。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

サンプル アプリでは、デモンストレーションのための Maria Rodriguez、架空のユーザーのユーザー アカウント、アプリにハードコードしています。 使用して、**電子メール**ユーザー名`maria.rodriguez@contoso.com`と、ユーザーがサインインするすべてのパスワード。 における、ユーザーの認証、`AuthenticateUser`メソッドで、 *Pages/Account/Login.cshtml.cs*ファイル。 実際の例では、ユーザーは、データベースに対して認証は。

## <a name="configuration"></a>構成

アプリを使用しない場合、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)、プロジェクト ファイルでパッケージの参照を作成、 [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージ。

`Startup.ConfigureServices`メソッドを使用して、認証ミドルウェア サービスを作成、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>と<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>メソッド。

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 渡される`AddAuthentication`アプリの既定の認証スキームを設定します。 `AuthenticationScheme` cookie 認証の複数のインスタンスがあるし、する場合に便利です[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)します。 設定、`AuthenticationScheme`に[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)スキームの「クッキー」の値を指定します。 スキームを識別する任意の文字列値を指定することができます。

アプリの認証スキームは、アプリの cookie 認証スキームと異なります。 Cookie の認証スキームがときに指定されていません<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>を使用して`CookieAuthenticationDefaults.AuthenticationScheme`(「クッキー」)。

認証 cookie の<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>プロパティに設定されて`true`既定。 認証 cookie は、サイト訪問者がデータの収集に同意していない場合に許可されます。 詳細については、「 <xref:security/gdpr#essential-cookies> 」を参照してください。

`Startup.Configure`メソッドを呼び出し、`UseAuthentication`を設定する、認証ミドルウェアを呼び出すメソッドを`HttpContext.User`プロパティ。 呼び出す、`UseAuthentication`メソッドを呼び出す前に`UseMvcWithDefaultRoute`または`UseMvc`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>認証プロバイダーのオプションを構成するクラスを使用します。

設定`CookieAuthenticationOptions`での認証サービスの構成で、`Startup.ConfigureServices`メソッド。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie のポリシーのミドルウェア

[Cookie のポリシーのミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)cookie のポリシーの機能を使用します。 機密性の高い順序は、アプリの処理パイプラインにミドルウェアを追加する&mdash;下流コンポーネントをパイプラインに登録されているのみに影響します。

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

使用<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>cookie を追加または削除されたとき、cookie 処理ハンドラーに cookie の処理とフックのグローバルの特性を制御する Cookie のポリシーのミドルウェアを提供します。

既定の<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy>値は`SameSiteMode.Lax`OAuth2 認証を許可するようにします。 厳密にポリシーを適用する同じサイトの`SameSiteMode.Strict`、設定、`MinimumSameSitePolicy`します。 ただし、この設定は、OAuth2 やその他のクロス オリジンの認証スキームを区切り、クロス オリジン要求の処理に依存しないようにするアプリの他の種類の cookie のセキュリティのレベルを高めます。

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Cookie のポリシーのミドルウェアの設定`MinimumSameSitePolicy`の設定に影響を与える`Cookie.SameSite`で`CookieAuthenticationOptions`次の表に従って設定します。

| MinimumSameSitePolicy | Cookie.SameSite | 最終的な Cookie.SameSite 設定 |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>認証 cookie を作成します。

ユーザー情報を保持するクッキーを作成するには、構築、<xref:System.Security.Claims.ClaimsPrincipal>します。 ユーザー情報がシリアル化され、cookie に格納されています。 

作成、<xref:System.Security.Claims.ClaimsIdentity>必須<xref:System.Security.Claims.Claim>s と呼び出し<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>ユーザーがサインインします。

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` 暗号化された cookie を作成し、それを現在の応答に追加します。 場合`AuthenticationScheme`が指定されていない、既定のスキームを使用します。

ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムは、暗号化に使用します。 複数のマシンでホストされているアプリでは、負荷のアプリケーションで分散や、web ファームを使用して[データ保護を構成する](xref:security/data-protection/configuration/overview)同じキー リングとアプリ id を使用します。

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトし、その cookie を削除、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

場合`CookieAuthenticationDefaults.AuthenticationScheme`(または「クッキー」) パターン (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用されるスキームを指定するには使用されません。 それ以外の場合、既定のスキームが使用されます。

## <a name="react-to-back-end-changes"></a>バックエンドの変更に対応します。

Cookie が作成されると、cookie は、id の 1 つのソースです。 バックエンド システムでは、ユーザー アカウントが無効です: 場合

* アプリの cookie 認証システムは、認証クッキーに基づいて要求を処理が続行されます。
* ユーザーは、認証 cookie が有効な限り、アプリにサインインしたままです。

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントをインターセプトし、cookie id の検証のオーバーライドを使用できます。 失効したユーザーがアプリにアクセスするリスクを軽減する要求ごとに cookie を検証しています。

Cookie を検証する方法の 1 つは、ユーザー データベースが変更されたときの追跡に基づいています。 ユーザーのクッキーが発行されたので、データベースが変更されていない場合、その cookie が有効である場合、ユーザーを再認証する必要はありません。 サンプル アプリで、データベースが実装されている`IUserRepository`し、格納、`LastChanged`値。 ユーザーが、データベースで更新されたときに、`LastChanged`値が現在の時刻に設定します。

データベースの変更に基づいている場合は、cookie を無効にするには、`LastChanged`値には、cookie を作成、`LastChanged`現在を含む要求`LastChanged`データベースから値。

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

オーバーライドを実装するために、`ValidatePrincipal`イベントから派生したクラスでは、次のシグネチャを持つメソッドを書き込み<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

実装例を次に`CookieAuthenticationEvents`:

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

クッキーにサービスを登録中に、イベント インスタンスを登録、`Startup.ConfigureServices`メソッド。 提供、[サービスの登録をスコープ](xref:fundamentals/dependency-injection#service-lifetimes)の`CustomCookieAuthenticationEvents`クラス。

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

ユーザーの名前が更新される場合を考えてみましょう&mdash;判断を任意の方法でセキュリティには影響しません。 非破壊的ユーザー プリンシパルを更新する場合は、呼び出す`context.ReplacePrincipal`設定と、`context.ShouldRenew`プロパティを`true`します。

> [!WARNING]
> ここで説明したアプローチは、要求ごとにトリガーされます。 要求ごとにすべてのユーザーの認証の cookie を検証すると、アプリの大規模なパフォーマンスが低下する可能性があります。

## <a name="persistent-cookies"></a>永続的な cookie

Cookie がブラウザー セッション間で永続化することができます。 この永続化は、明示的なユーザーによる同意を「記憶」のチェック ボックスで、サインインと同様のメカニズムでのみ有効にする必要があります。 

次のコード スニペットでは、id およびブラウザー クロージャでは存続する対応する cookie を作成します。 以前に構成された、スライド式有効期限の設定が受け入れられます。 ブラウザーに cookie の期限切れのブラウザーが閉じられたときに、再起動の後、cookie をクリアします。

設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>に`true`で<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

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

## <a name="absolute-cookie-expiration"></a>絶対クッキーの有効期限

絶対有効期限を設定できる<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>します。 永続的な cookie を作成する`IsPersistent`も設定する必要があります。 それ以外の場合、cookie は、セッション ベースの有効期間を持つが作成され前に、または後、保持している認証チケット有効期限が切れる可能性があります。 ときに`ExpiresUtc`が設定の値がオーバーライドされて、<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>オプションの<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>場合は、設定します。

次のコード スニペットは、id と対応するクッキーを 20 分間継続を作成します。 これには、以前に構成された、スライド式有効期限の設定が無視されます。

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

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [ポリシー ベースのロールのチェック](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
