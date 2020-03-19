---
title: ASP.NET Core での multi-factor authentication
author: damienbod
description: ASP.NET Core アプリで multi-factor authentication (MFA) を設定する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Identity
uid: security/authentication/mfa
ms.openlocfilehash: 6220688d53f0718ca5be5f63dd5d9539d37e2391
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520147"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>ASP.NET Core での multi-factor authentication

[Damien Bowden](https://github.com/damienbod)

Multi-factor authentication (MFA) は、追加の形式の識別のためにサインインイベント中にユーザーが要求されるプロセスです。 このプロンプトでは、cellphone からコードを入力したり、FIDO2 キーを使用したり、指紋スキャンを提供したりすることができます。 2番目の形式の認証が必要な場合は、セキュリティが強化されます。 追加の要因は、攻撃者が簡単に取得したり複製したりすることはできません。

この記事では、次の項目について説明します。

* MFA とは何か、推奨される MFA フロー
* ASP.NET Core Identity を使用して管理ページの MFA を構成する
* MFA のサインイン要件を OpenID Connect サーバーに送信する
* MFA を要求するように ASP.NET Core OpenID Connect クライアントを強制する

## <a name="mfa-2fa"></a>MFA、2FA

MFA には、既知の id、所有しているもの、認証するユーザーの生体認証の検証など、id に対して少なくとも2種類の証明が必要です。

2要素認証 (2FA) は MFA のサブセットに似ていますが、MFA の違いは、id を証明するために2つ以上の要因が必要になる場合があります。

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (時間ベースのワンタイムパスワードアルゴリズム)

TOTP を使用した MFA は、ASP.NET Core Identityを使用した実装でサポートされています。 これは、次のようなすべての準拠認証アプリと共に使用できます。

* Microsoft Authenticator アプリ
* Google Authenticator アプリ

実装の詳細については、次のリンクを参照してください。

[ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>MFA FIDO2 またはパスワードなし

現在、FIDO2 は次のとおりです。

* MFA を実現する最も安全な方法です。
* フィッシング攻撃から保護する唯一の MFA フロー。

現時点では、ASP.NET Core は FIDO2 を直接サポートしていません。 FIDO2 は、MFA またはパスワードの少ないフローに使用できます。

Azure Active Directory では、FIDO2 およびパスワードなしフローがサポートされています。 詳細については、「 [Azure Active Directory の Passwordless 認証オプション](/azure/active-directory/authentication/concept-authentication-passwordless)」を参照してください。

### <a name="mfa-sms"></a>MFA SMS

SMS による MFA は、パスワード認証 (単一要素) と比較して、セキュリティが大幅に向上します。 ただし、2番目の要素として SMS を使用することは推奨されなくなりました。 この種類の実装には、既知の攻撃ベクトルが多すぎます。

[NIST のガイドライン](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-opno-locidentity"></a>ASP.NET Core Identity を使用して管理ページの MFA を構成する

MFA は、ユーザーが ASP.NET Core Identity アプリ内の機密性の高いページにアクセスすることを強制する可能性があります。 これは、異なる id に対して異なるレベルのアクセスが存在するアプリに便利な場合があります。 たとえば、ユーザーはパスワードログインを使用してプロファイルデータを表示できますが、管理者は MFA を使用して管理ページにアクセスする必要があります。

### <a name="extend-the-login-with-an-mfa-claim"></a>MFA 要求を使用してログインを拡張する

デモコードは、Identity と Razor Pages で ASP.NET Core を使用して設定します。 `AddIdentity` メソッドは `AddDefaultIdentity` 1 ではなく使用されます。そのため、ログインが成功した後に、`IUserClaimsPrincipalFactory` の実装を使用して、クレームを id に追加できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

`AdditionalUserClaimsPrincipalFactory` クラスは、ログインが成功した後にのみ、ユーザー要求に `amr` 要求を追加します。 要求の値がデータベースから読み取られます。 Id が MFA でログインしている場合は、保護されたビューにユーザーがアクセスする必要があるため、この要求はここに追加されます。 データベースビューが要求を使用せずにデータベースから直接読み取られた場合は、MFA をアクティブ化した後に、MFA を使用せずにビューに直接アクセスすることができます。

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

`Startup` クラスで Identity サービスのセットアップが変更されたため、Identity のレイアウトを更新する必要があります。 Identity のページをアプリにスキャフォールディングします。 *Identity/Account/manage/_Layout*ファイルでレイアウトを定義します。

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

また、Identity のページからすべての管理ページのレイアウトを割り当てます。

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>[管理] ページで MFA の要件を検証する

管理 Razor ページは、ユーザーが MFA を使用してログインしたことを検証します。 `OnGet` メソッドでは、ユーザー要求にアクセスするために id が使用されます。 `amr` の要求は、`mfa`値に対してチェックされます。 Id にこの要求がない場合、または `false`場合、ページは [MFA を有効にする] ページにリダイレクトされます。 これは、ユーザーが既にログインしていて、MFA がないために発生する可能性があります。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>ユーザーのログイン情報を切り替える UI ロジック

スタートアップ時に承認ポリシーが追加されました。 このポリシーでは、`mfa`値の `amr` 要求が必要です。

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

このポリシーを `_Layout` ビューで使用すると、**管理者**メニューの表示と非表示を切り替えることができます。

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Id が MFA を使用してログインしている場合は、 **[管理者]** メニューが表示され、ツールヒントの警告は表示されません。 ユーザーが MFA なしでログインした場合、ユーザーに通知するツールヒントと共に **[管理者 (無効)]** メニューが表示されます (警告について説明しています)。

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

ユーザーが MFA を使用せずにログインすると、次の警告が表示されます。

![管理者の MFA 認証](mfa/_static/identitystandalonemfa_01.png)

ユーザーは、 **[管理者]** リンクをクリックすると、MFA の有効化ビューにリダイレクトされます。

![管理者が MFA 認証をアクティブ化する](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>MFA のサインイン要件を OpenID Connect サーバーに送信する 

`acr_values` パラメーターを使用すると、認証要求でクライアントから必要な `mfa` の値をサーバーに渡すことができます。

> [!NOTE]
> これを機能させるには、Open ID Connect サーバーで `acr_values` パラメーターを処理する必要があります。

### <a name="openid-connect-aspnet-core-client"></a>OpenID Connect ASP.NET Core クライアント

Open ID Connect クライアントアプリの ASP.NET Core Razor Pages は、`AddOpenIdConnect` メソッドを使用して Open ID Connect サーバーにログインします。 `acr_values` パラメーターは `mfa` 値で設定され、認証要求と共に送信されます。 このを追加するには、`OpenIdConnectEvents` を使用します。

推奨される `acr_values` パラメーター値については、「[認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)」を参照してください。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-identityserver-4-server-with-aspnet-core-opno-locidentity"></a>ASP.NET Core Identity を使用した OpenID Connect のサーバー4サーバーの例

MVC ビューで ASP.NET Core Identity を使用して実装された OpenID Connect サーバーでは、 *ErrorEnable2FA*という名前の新しいビューが作成されます。 ビュー:

* Identity が MFA を必要としているものの、ユーザーが Identityでアクティブ化していないアプリからのものであるかどうかが表示されます。
* ユーザーに通知し、このをアクティブにするためのリンクを追加します。

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

`Login` メソッドでは、Open ID Connect 要求パラメーターにアクセスするために、`IIdentityServerInteractionService` インターフェイス実装 `_interaction` が使用されます。 `acr_values` パラメーターには、`AcrValues` プロパティを使用してアクセスします。 クライアントが `mfa` セットを使用してこれを送信したときに、このチェックボックスをオンにすることができます。

MFA が必要で、ASP.NET Core Identity のユーザーが MFA を有効にしている場合、ログインは続行されます。 ユーザーが MFA を有効にしていない場合、ユーザーはカスタムビュー *ErrorEnable2FA*にリダイレクトされます。 次に、ASP.NET Core Identity ユーザーにサインインします。

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

`ExternalLoginCallback` メソッドは、ローカル Identity ログインと同様に機能します。 `mfa` 値の `AcrValues` プロパティがチェックされます。 `mfa` 値が存在する場合は、ログインが完了する前に MFA が強制されます (たとえば、`ErrorEnable2FA` ビューにリダイレクトされます)。

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

ユーザーが既にログインしている場合、クライアントアプリは次のようになります。

* は引き続き `amr` 要求を検証します。
* ASP.NET Core Identity ビューへのリンクを使用して MFA を設定できます。

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>MFA を要求するように ASP.NET Core OpenID Connect クライアントを強制する

この例は、OpenID Connect を使用してサインインする ASP.NET Core Razor ページアプリで、ユーザーが MFA を使用して認証されていることを要求できる方法を示しています。

MFA の要件を検証するために、`IAuthorizationRequirement` の要件が作成されます。 これは、MFA を必要とするポリシーを使用してページに追加されます。

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

`amr` 要求を使用して `mfa`値を確認する `AuthorizationHandler` が実装されています。 `amr` は、認証が成功したことを示す `id_token` に返され、[認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)の指定に定義されているように、さまざまな値を持つことができます。

返される値は、id の認証方法と Open ID Connect サーバーの実装によって異なります。

`AuthorizationHandler` は `RequireMfa` の要件を使用し、`amr` 要求を検証します。 OpenID Connect サーバーは、ASP.NET Core Identityと共に IdentityServer4 を使用して実装できます。 ユーザーが TOTP を使用してログインすると、`amr` 要求が MFA 値と共に返されます。 異なる OpenID Connect サーバーの実装または別の MFA の種類を使用している場合は、`amr` 要求の値が異なることがあります。 このコードも、このコードを受け入れるように拡張する必要があります。

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

`Startup.ConfigureServices` メソッドでは、`AddOpenIdConnect` メソッドが既定のチャレンジスキームとして使用されます。 `amr` 要求を確認するために使用される承認ハンドラーが、コントロールの反転コンテナーに追加されます。 その後、`RequireMfa` 要件を追加するポリシーが作成されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

このポリシーは、必要に応じて Razor ページで使用されます。 ポリシーは、アプリ全体に対してグローバルに追加することもできます。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

ユーザーが MFA を使用せずに認証した場合、`amr` の要求には、`pwd` 値が設定されている可能性があります。 要求は、ページへのアクセスを承認されません。 既定値を使用すると、ユーザーは [*アカウント/AccessDenied* ] ページにリダイレクトされます。 この動作は変更できます。または、独自のカスタムロジックを実装することもできます。 この例では、有効なユーザーが自分のアカウントに対して MFA を設定できるように、リンクが追加されています。

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

これで、MFA で認証されるユーザーのみがページまたは web サイトにアクセスできるようになりました。 異なる種類の MFA が使用されている場合、または2FA が正常な場合、`amr` 要求の値は異なり、正しく処理する必要があります。 Open ID Connect サーバーが異なると、この要求に対して異なる値が返され、[認証方法の参照値](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)の指定に従わないことがあります。

MFA を使用せずにログインする場合 (パスワードのみを使用する場合など):

* `amr` には `pwd` の値があります。

    ![require_mfa_oidc_02 .png](mfa/_static/require_mfa_oidc_02.png)

* アクセスが拒否されました:

    ![require_mfa_oidc_03 .png](mfa/_static/require_mfa_oidc_03.png)

または、Identityで OTP を使用してログインします。

![require_mfa_oidc_01 .png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core での TOTP authenticator アプリの QR コード生成を有効にする](xref:security/authentication/identity-enable-qrcodes)
* [Azure Active Directory 用の passwordless 認証オプション](/azure/active-directory/authentication/concept-authentication-passwordless)
* [.NET を使用した FIDO2/WebAuthn 構成証明およびアサーション用の FIDO2 .NET ライブラリ](https://github.com/abergs/fido2-net-lib)
* [WebAuthn すばらしい](https://github.com/herrjemand/awesome-webauthn)
