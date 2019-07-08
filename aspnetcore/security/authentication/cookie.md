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
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="c05bd-103">ASP.NET Core Identity なしでの cookie 認証を使用します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="c05bd-104">によって[Rick Anderson](https://twitter.com/RickAndMSFT)と[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c05bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c05bd-105">ASP.NET Core Identity は、の作成とログインの管理、フル機能の完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="c05bd-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="c05bd-106">ただし、ASP.NET Core Identity なし、cookie ベースの認証の認証プロバイダーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-106">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="c05bd-107">詳細については、「 <xref:security/authentication/identity> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c05bd-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="c05bd-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c05bd-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c05bd-109">サンプル アプリでは、デモンストレーションのための Maria Rodriguez、架空のユーザーのユーザー アカウント、アプリにハードコードしています。</span><span class="sxs-lookup"><span data-stu-id="c05bd-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="c05bd-110">使用して、**電子メール**ユーザー名`maria.rodriguez@contoso.com`と、ユーザーがサインインするすべてのパスワード。</span><span class="sxs-lookup"><span data-stu-id="c05bd-110">Use the **Email** user name `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="c05bd-111">における、ユーザーの認証、`AuthenticateUser`メソッドで、 *Pages/Account/Login.cshtml.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="c05bd-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="c05bd-112">実際の例では、ユーザーは、データベースに対して認証は。</span><span class="sxs-lookup"><span data-stu-id="c05bd-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="c05bd-113">構成</span><span class="sxs-lookup"><span data-stu-id="c05bd-113">Configuration</span></span>

<span data-ttu-id="c05bd-114">アプリを使用しない場合、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)、プロジェクト ファイルでパッケージの参照を作成、 [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="c05bd-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="c05bd-115">`Startup.ConfigureServices`メソッドを使用して、認証ミドルウェア サービスを作成、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>と<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>メソッド。</span><span class="sxs-lookup"><span data-stu-id="c05bd-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="c05bd-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> 渡される`AddAuthentication`アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="c05bd-117">`AuthenticationScheme` cookie 認証の複数のインスタンスがあるし、する場合に便利です[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="c05bd-118">設定、`AuthenticationScheme`に[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)スキームの「クッキー」の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="c05bd-119">スキームを識別する任意の文字列値を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="c05bd-120">アプリの認証スキームは、アプリの cookie 認証スキームと異なります。</span><span class="sxs-lookup"><span data-stu-id="c05bd-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="c05bd-121">Cookie の認証スキームがときに指定されていません<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>を使用して`CookieAuthenticationDefaults.AuthenticationScheme`(「クッキー」)。</span><span class="sxs-lookup"><span data-stu-id="c05bd-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="c05bd-122">認証 cookie の<xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>プロパティに設定されて`true`既定。</span><span class="sxs-lookup"><span data-stu-id="c05bd-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="c05bd-123">認証 cookie は、サイト訪問者がデータの収集に同意していない場合に許可されます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="c05bd-124">詳細については、「 <xref:security/gdpr#essential-cookies> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c05bd-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="c05bd-125">`Startup.Configure`メソッドを呼び出し、`UseAuthentication`を設定する、認証ミドルウェアを呼び出すメソッドを`HttpContext.User`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="c05bd-125">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="c05bd-126">呼び出す、`UseAuthentication`メソッドを呼び出す前に`UseMvcWithDefaultRoute`または`UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="c05bd-126">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="c05bd-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>認証プロバイダーのオプションを構成するクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="c05bd-128">設定`CookieAuthenticationOptions`での認証サービスの構成で、`Startup.ConfigureServices`メソッド。</span><span class="sxs-lookup"><span data-stu-id="c05bd-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="c05bd-129">Cookie のポリシーのミドルウェア</span><span class="sxs-lookup"><span data-stu-id="c05bd-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="c05bd-130">[Cookie のポリシーのミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)cookie のポリシーの機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="c05bd-131">機密性の高い順序は、アプリの処理パイプラインにミドルウェアを追加する&mdash;下流コンポーネントをパイプラインに登録されているのみに影響します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="c05bd-132">使用<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions>cookie を追加または削除されたとき、cookie 処理ハンドラーに cookie の処理とフックのグローバルの特性を制御する Cookie のポリシーのミドルウェアを提供します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="c05bd-133">既定の<xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy>値は`SameSiteMode.Lax`OAuth2 認証を許可するようにします。</span><span class="sxs-lookup"><span data-stu-id="c05bd-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="c05bd-134">厳密にポリシーを適用する同じサイトの`SameSiteMode.Strict`、設定、`MinimumSameSitePolicy`します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="c05bd-135">ただし、この設定は、OAuth2 やその他のクロス オリジンの認証スキームを区切り、クロス オリジン要求の処理に依存しないようにするアプリの他の種類の cookie のセキュリティのレベルを高めます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="c05bd-136">Cookie のポリシーのミドルウェアの設定`MinimumSameSitePolicy`の設定に影響を与える`Cookie.SameSite`で`CookieAuthenticationOptions`次の表に従って設定します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="c05bd-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="c05bd-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="c05bd-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="c05bd-138">Cookie.SameSite</span></span> | <span data-ttu-id="c05bd-139">最終的な Cookie.SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="c05bd-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="c05bd-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c05bd-140">SameSiteMode.None</span></span>     | <span data-ttu-id="c05bd-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c05bd-141">SameSiteMode.None</span></span><br><span data-ttu-id="c05bd-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c05bd-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="c05bd-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="c05bd-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c05bd-144">SameSiteMode.None</span></span><br><span data-ttu-id="c05bd-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c05bd-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="c05bd-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="c05bd-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c05bd-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="c05bd-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c05bd-148">SameSiteMode.None</span></span><br><span data-ttu-id="c05bd-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c05bd-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="c05bd-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="c05bd-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c05bd-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="c05bd-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c05bd-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="c05bd-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="c05bd-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="c05bd-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="c05bd-155">SameSiteMode.None</span></span><br><span data-ttu-id="c05bd-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="c05bd-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="c05bd-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="c05bd-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="c05bd-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="c05bd-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="c05bd-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="c05bd-161">認証 cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-161">Create an authentication cookie</span></span>

<span data-ttu-id="c05bd-162">ユーザー情報を保持するクッキーを作成するには、構築、<xref:System.Security.Claims.ClaimsPrincipal>します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="c05bd-163">ユーザー情報がシリアル化され、cookie に格納されています。</span><span class="sxs-lookup"><span data-stu-id="c05bd-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="c05bd-164">作成、<xref:System.Security.Claims.ClaimsIdentity>必須<xref:System.Security.Claims.Claim>s と呼び出し<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>ユーザーがサインインします。</span><span class="sxs-lookup"><span data-stu-id="c05bd-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="c05bd-165">`SignInAsync` 暗号化された cookie を作成し、それを現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="c05bd-166">場合`AuthenticationScheme`が指定されていない、既定のスキームを使用します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="c05bd-167">ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムは、暗号化に使用します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="c05bd-168">複数のマシンでホストされているアプリでは、負荷のアプリケーションで分散や、web ファームを使用して[データ保護を構成する](xref:security/data-protection/configuration/overview)同じキー リングとアプリ id を使用します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="c05bd-169">サインアウト</span><span class="sxs-lookup"><span data-stu-id="c05bd-169">Sign out</span></span>

<span data-ttu-id="c05bd-170">現在のユーザーをサインアウトし、その cookie を削除、 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="c05bd-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="c05bd-171">場合`CookieAuthenticationDefaults.AuthenticationScheme`(または「クッキー」) パターン (たとえば、"ContosoCookie")、認証プロバイダーを構成するときに使用されるスキームを指定するには使用されません。</span><span class="sxs-lookup"><span data-stu-id="c05bd-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="c05bd-172">それ以外の場合、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="c05bd-173">バックエンドの変更に対応します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-173">React to back-end changes</span></span>

<span data-ttu-id="c05bd-174">Cookie が作成されると、cookie は、id の 1 つのソースです。</span><span class="sxs-lookup"><span data-stu-id="c05bd-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="c05bd-175">バックエンド システムでは、ユーザー アカウントが無効です: 場合</span><span class="sxs-lookup"><span data-stu-id="c05bd-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="c05bd-176">アプリの cookie 認証システムは、認証クッキーに基づいて要求を処理が続行されます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="c05bd-177">ユーザーは、認証 cookie が有効な限り、アプリにサインインしたままです。</span><span class="sxs-lookup"><span data-stu-id="c05bd-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="c05bd-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>イベントをインターセプトし、cookie id の検証のオーバーライドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="c05bd-179">失効したユーザーがアプリにアクセスするリスクを軽減する要求ごとに cookie を検証しています。</span><span class="sxs-lookup"><span data-stu-id="c05bd-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="c05bd-180">Cookie を検証する方法の 1 つは、ユーザー データベースが変更されたときの追跡に基づいています。</span><span class="sxs-lookup"><span data-stu-id="c05bd-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="c05bd-181">ユーザーのクッキーが発行されたので、データベースが変更されていない場合、その cookie が有効である場合、ユーザーを再認証する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c05bd-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="c05bd-182">サンプル アプリで、データベースが実装されている`IUserRepository`し、格納、`LastChanged`値。</span><span class="sxs-lookup"><span data-stu-id="c05bd-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="c05bd-183">ユーザーが、データベースで更新されたときに、`LastChanged`値が現在の時刻に設定します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="c05bd-184">データベースの変更に基づいている場合は、cookie を無効にするには、`LastChanged`値には、cookie を作成、`LastChanged`現在を含む要求`LastChanged`データベースから値。</span><span class="sxs-lookup"><span data-stu-id="c05bd-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="c05bd-185">オーバーライドを実装するために、`ValidatePrincipal`イベントから派生したクラスでは、次のシグネチャを持つメソッドを書き込み<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="c05bd-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="c05bd-186">実装例を次に`CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="c05bd-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="c05bd-187">クッキーにサービスを登録中に、イベント インスタンスを登録、`Startup.ConfigureServices`メソッド。</span><span class="sxs-lookup"><span data-stu-id="c05bd-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c05bd-188">提供、[サービスの登録をスコープ](xref:fundamentals/dependency-injection#service-lifetimes)の`CustomCookieAuthenticationEvents`クラス。</span><span class="sxs-lookup"><span data-stu-id="c05bd-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="c05bd-189">ユーザーの名前が更新される場合を考えてみましょう&mdash;判断を任意の方法でセキュリティには影響しません。</span><span class="sxs-lookup"><span data-stu-id="c05bd-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="c05bd-190">非破壊的ユーザー プリンシパルを更新する場合は、呼び出す`context.ReplacePrincipal`設定と、`context.ShouldRenew`プロパティを`true`します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="c05bd-191">ここで説明したアプローチは、要求ごとにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="c05bd-192">要求ごとにすべてのユーザーの認証の cookie を検証すると、アプリの大規模なパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c05bd-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="c05bd-193">永続的な cookie</span><span class="sxs-lookup"><span data-stu-id="c05bd-193">Persistent cookies</span></span>

<span data-ttu-id="c05bd-194">Cookie がブラウザー セッション間で永続化することができます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="c05bd-195">この永続化は、明示的なユーザーによる同意を「記憶」のチェック ボックスで、サインインと同様のメカニズムでのみ有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c05bd-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="c05bd-196">次のコード スニペットでは、id およびブラウザー クロージャでは存続する対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="c05bd-197">以前に構成された、スライド式有効期限の設定が受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="c05bd-198">ブラウザーに cookie の期限切れのブラウザーが閉じられたときに、再起動の後、cookie をクリアします。</span><span class="sxs-lookup"><span data-stu-id="c05bd-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="c05bd-199">設定<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>に`true`で<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="c05bd-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="c05bd-200">絶対クッキーの有効期限</span><span class="sxs-lookup"><span data-stu-id="c05bd-200">Absolute cookie expiration</span></span>

<span data-ttu-id="c05bd-201">絶対有効期限を設定できる<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="c05bd-202">永続的な cookie を作成する`IsPersistent`も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c05bd-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="c05bd-203">それ以外の場合、cookie は、セッション ベースの有効期間を持つが作成され前に、または後、保持している認証チケット有効期限が切れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c05bd-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="c05bd-204">ときに`ExpiresUtc`が設定の値がオーバーライドされて、<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan>オプションの<xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>場合は、設定します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="c05bd-205">次のコード スニペットは、id と対応するクッキーを 20 分間継続を作成します。</span><span class="sxs-lookup"><span data-stu-id="c05bd-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="c05bd-206">これには、以前に構成された、スライド式有効期限の設定が無視されます。</span><span class="sxs-lookup"><span data-stu-id="c05bd-206">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c05bd-207">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c05bd-207">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="c05bd-208">ポリシー ベースのロールのチェック</span><span class="sxs-lookup"><span data-stu-id="c05bd-208">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
