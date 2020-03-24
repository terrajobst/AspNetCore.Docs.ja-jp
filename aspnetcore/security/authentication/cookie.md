---
title: ASP.NET Core Id を指定せずに cookie 認証を使用する
author: rick-anderson
description: ASP.NET Core Id を使用せずに cookie 認証を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: b7c8b2cccb27dd6818330b17439675e41bfef013
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219208"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="524d5-103">ASP.NET Core Id を指定せずに cookie 認証を使用する</span><span class="sxs-lookup"><span data-stu-id="524d5-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="524d5-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="524d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="524d5-105">ASP.NET Core Id は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="524d5-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="524d5-106">ただし、ASP.NET Core Id のない cookie ベースの認証プロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="524d5-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="524d5-107">詳細については、<xref:security/authentication/identity> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="524d5-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="524d5-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="524d5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="524d5-109">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="524d5-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="524d5-110">**電子メール**アドレス `maria.rodriguez@contoso.com` とパスワードを使用して、ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="524d5-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="524d5-111">ユーザーは、 *Pages/Account/Login. cshtml. .cs*ファイルの `AuthenticateUser` メソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="524d5-112">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="524d5-113">構成</span><span class="sxs-lookup"><span data-stu-id="524d5-113">Configuration</span></span>

<span data-ttu-id="524d5-114">アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="524d5-115">`Startup.ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> と <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> のメソッドを使用して認証ミドルウェアサービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="524d5-116">`AddAuthentication` に渡された <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> は、アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="524d5-117">`AuthenticationScheme` は、cookie 認証の複数のインスタンスがあり、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="524d5-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="524d5-118">`AuthenticationScheme` を[Cookieauthenticationdefaults に設定します。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="524d5-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="524d5-119">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="524d5-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="524d5-120">アプリの認証方式は、アプリの cookie 認証スキームとは異なります。</span><span class="sxs-lookup"><span data-stu-id="524d5-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="524d5-121"><xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>に cookie 認証スキームが指定されていない場合、`CookieAuthenticationDefaults.AuthenticationScheme` ("Cookie") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="524d5-122">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定では `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="524d5-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="524d5-123">サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="524d5-124">詳細については、<xref:security/gdpr#essential-cookies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="524d5-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="524d5-125">`Startup.Configure`で `UseAuthentication` と `UseAuthorization` を呼び出して `HttpContext.User` プロパティを設定し、要求の承認ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="524d5-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="524d5-126">`UseEndpoints`を呼び出す前に、`UseAuthentication` および `UseAuthorization` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="524d5-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="524d5-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> クラスは、認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="524d5-128">`Startup.ConfigureServices` 方法で認証を行うために、サービス構成の `CookieAuthenticationOptions` を設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="524d5-129">クッキーポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="524d5-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="524d5-130">Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="524d5-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="524d5-131">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、パイプラインに登録されている下流コンポーネントにのみ影響&mdash;ます。</span><span class="sxs-lookup"><span data-stu-id="524d5-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="524d5-132">Cookie を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするには、クッキーポリシーミドルウェアに提供されている <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> を使用します。</span><span class="sxs-lookup"><span data-stu-id="524d5-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="524d5-133">既定の <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、OAuth2 認証を許可する `SameSiteMode.Lax` です。</span><span class="sxs-lookup"><span data-stu-id="524d5-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="524d5-134">`SameSiteMode.Strict`の同じサイトポリシーを厳密に適用するには、`MinimumSameSitePolicy`を設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="524d5-135">この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="524d5-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="524d5-136">`MinimumSameSitePolicy` の Cookie ポリシーのミドルウェア設定は、次の表に従って `CookieAuthenticationOptions` 設定の `Cookie.SameSite` の設定に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="524d5-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="524d5-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="524d5-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="524d5-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="524d5-138">Cookie.SameSite</span></span> | <span data-ttu-id="524d5-139">結果の SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="524d5-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="524d5-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-140">SameSiteMode.None</span></span>     | <span data-ttu-id="524d5-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-141">SameSiteMode.None</span></span><br><span data-ttu-id="524d5-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="524d5-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-144">SameSiteMode.None</span></span><br><span data-ttu-id="524d5-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="524d5-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="524d5-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-148">SameSiteMode.None</span></span><br><span data-ttu-id="524d5-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="524d5-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="524d5-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="524d5-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-155">SameSiteMode.None</span></span><br><span data-ttu-id="524d5-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="524d5-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="524d5-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="524d5-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="524d5-161">認証 cookie を作成する</span><span class="sxs-lookup"><span data-stu-id="524d5-161">Create an authentication cookie</span></span>

<span data-ttu-id="524d5-162">ユーザー情報を保持する cookie を作成するには、<xref:System.Security.Claims.ClaimsPrincipal>を構築します。</span><span class="sxs-lookup"><span data-stu-id="524d5-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="524d5-163">ユーザー情報がシリアル化され、cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="524d5-164">必要な <xref:System.Security.Claims.Claim>s を持つ <xref:System.Security.Claims.ClaimsIdentity> を作成し、<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="524d5-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="524d5-165">`SignInAsync` は、暗号化されたクッキーを作成し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="524d5-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="524d5-166">`AuthenticationScheme` が指定されていない場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="524d5-167">暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="524d5-168">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。</span><span class="sxs-lookup"><span data-stu-id="524d5-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="524d5-169">サインアウトする</span><span class="sxs-lookup"><span data-stu-id="524d5-169">Sign out</span></span>

<span data-ttu-id="524d5-170">現在のユーザーをサインアウトして cookie を削除するには、<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="524d5-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="524d5-171">`CookieAuthenticationDefaults.AuthenticationScheme` (または "Cookie") がスキームとして使用されていない場合 (例: "ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="524d5-172">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="524d5-173">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="524d5-173">React to back-end changes</span></span>

<span data-ttu-id="524d5-174">Cookie が作成されると、cookie は id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="524d5-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="524d5-175">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="524d5-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="524d5-176">アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。</span><span class="sxs-lookup"><span data-stu-id="524d5-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="524d5-177">認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。</span><span class="sxs-lookup"><span data-stu-id="524d5-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="524d5-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="524d5-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="524d5-179">すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="524d5-180">Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="524d5-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="524d5-181">ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="524d5-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="524d5-182">サンプルアプリでは、データベースは `IUserRepository` に実装され、`LastChanged` 値を格納します。</span><span class="sxs-lookup"><span data-stu-id="524d5-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="524d5-183">データベースでユーザーが更新されると、`LastChanged` の値が現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="524d5-184">`LastChanged` 値に基づいてデータベースが変更されたときに cookie を無効にするには、データベースの現在の `LastChanged` 値を含む `LastChanged` 要求を使用して cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="524d5-185">`ValidatePrincipal` イベントのオーバーライドを実装するには、<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>から派生したクラスに次のシグネチャを持つメソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="524d5-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="524d5-186">`CookieAuthenticationEvents`の実装例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="524d5-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="524d5-187">Cookie サービスの登録時に、`Startup.ConfigureServices` メソッドでイベントインスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="524d5-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="524d5-188">`CustomCookieAuthenticationEvents` クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="524d5-189">ユーザーの名前が&mdash;更新された場合に、セキュリティに影響を与えないようにするという状況を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="524d5-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="524d5-190">ユーザープリンシパルを非破壊更新する場合は、`context.ReplacePrincipal` を呼び出し、`context.ShouldRenew` プロパティを `true`に設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="524d5-191">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="524d5-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="524d5-192">すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="524d5-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="524d5-193">永続的な cookie</span><span class="sxs-lookup"><span data-stu-id="524d5-193">Persistent cookies</span></span>

<span data-ttu-id="524d5-194">Cookie がブラウザーセッション間で保持されるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="524d5-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="524d5-195">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="524d5-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="524d5-196">次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="524d5-197">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="524d5-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="524d5-198">ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。</span><span class="sxs-lookup"><span data-stu-id="524d5-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="524d5-199"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>で `true` に <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> を設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="524d5-200">クッキーの絶対有効期限</span><span class="sxs-lookup"><span data-stu-id="524d5-200">Absolute cookie expiration</span></span>

<span data-ttu-id="524d5-201">絶対有効期限は <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>で設定できます。</span><span class="sxs-lookup"><span data-stu-id="524d5-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="524d5-202">永続的なクッキーを作成するには、`IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="524d5-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="524d5-203">それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="524d5-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="524d5-204">`ExpiresUtc` が設定されている場合は <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>の <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> オプションの値が上書きされます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="524d5-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="524d5-205">次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="524d5-206">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="524d5-207">ASP.NET Core Id は、ログインを作成および管理するための完全な機能を備えた完全な認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="524d5-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="524d5-208">ただし、ASP.NET Core Id のない cookie ベースの認証認証プロバイダーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="524d5-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="524d5-209">詳細については、<xref:security/authentication/identity> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="524d5-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="524d5-210">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="524d5-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="524d5-211">サンプルアプリでのデモンストレーションのために、架空のユーザーであるマリア Rodriguez のユーザーアカウントは、アプリにハードコードされています。</span><span class="sxs-lookup"><span data-stu-id="524d5-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="524d5-212">**電子メール**アドレス `maria.rodriguez@contoso.com` とパスワードを使用して、ユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="524d5-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="524d5-213">ユーザーは、 *Pages/Account/Login. cshtml. .cs*ファイルの `AuthenticateUser` メソッドで認証されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="524d5-214">実際の例では、ユーザーはデータベースに対して認証されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="524d5-215">構成</span><span class="sxs-lookup"><span data-stu-id="524d5-215">Configuration</span></span>

<span data-ttu-id="524d5-216">アプリで[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)が使用されていない場合は、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)パッケージのプロジェクトファイルにパッケージ参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="524d5-217">`Startup.ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> および <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> メソッドを使用して認証ミドルウェアサービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="524d5-218">`AddAuthentication` に渡された <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> は、アプリの既定の認証スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="524d5-219">`AuthenticationScheme` は、cookie 認証の複数のインスタンスがあり、[特定のスキームで承認](xref:security/authorization/limitingidentitybyscheme)する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="524d5-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="524d5-220">`AuthenticationScheme` を[Cookieauthenticationdefaults に設定します。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme)は、スキームに対して "cookie" の値を提供します。</span><span class="sxs-lookup"><span data-stu-id="524d5-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="524d5-221">スキームを区別する任意の文字列値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="524d5-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="524d5-222">アプリの認証方式は、アプリの cookie 認証スキームとは異なります。</span><span class="sxs-lookup"><span data-stu-id="524d5-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="524d5-223"><xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>に cookie 認証スキームが指定されていない場合、`CookieAuthenticationDefaults.AuthenticationScheme` ("Cookie") が使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="524d5-224">認証 cookie の <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> プロパティは、既定では `true` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="524d5-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="524d5-225">サイトビジターがデータコレクションに同意していない場合は、認証 cookie が許可されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="524d5-226">詳細については、<xref:security/gdpr#essential-cookies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="524d5-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="524d5-227">`Startup.Configure` メソッドで、`UseAuthentication` メソッドを呼び出して、`HttpContext.User` プロパティを設定する認証ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="524d5-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="524d5-228">`UseMvcWithDefaultRoute` または `UseMvc`を呼び出す前に、`UseAuthentication` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="524d5-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="524d5-229"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> クラスは、認証プロバイダーオプションを構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="524d5-230">`Startup.ConfigureServices` 方法で認証を行うために、サービス構成の `CookieAuthenticationOptions` を設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="524d5-231">クッキーポリシーミドルウェア</span><span class="sxs-lookup"><span data-stu-id="524d5-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="524d5-232">Cookie[ポリシーミドルウェア](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware)を使用すると、cookie ポリシー機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="524d5-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="524d5-233">アプリ処理パイプラインへのミドルウェアの追加は順序に依存しており、パイプラインに登録されている下流コンポーネントにのみ影響&mdash;ます。</span><span class="sxs-lookup"><span data-stu-id="524d5-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="524d5-234">Cookie を追加または削除したときに cookie 処理のグローバル特性を制御し、クッキー処理ハンドラーにフックするには、クッキーポリシーミドルウェアに提供されている <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> を使用します。</span><span class="sxs-lookup"><span data-stu-id="524d5-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="524d5-235">既定の <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> 値は、OAuth2 認証を許可する `SameSiteMode.Lax` です。</span><span class="sxs-lookup"><span data-stu-id="524d5-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="524d5-236">`SameSiteMode.Strict`の同じサイトポリシーを厳密に適用するには、`MinimumSameSitePolicy`を設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="524d5-237">この設定は、OAuth2 およびその他のクロスオリジン認証スキームを分割しますが、クロスオリジン要求処理に依存しない他の種類のアプリの cookie セキュリティのレベルを昇格させます。</span><span class="sxs-lookup"><span data-stu-id="524d5-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="524d5-238">`MinimumSameSitePolicy` の Cookie ポリシーのミドルウェア設定は、次の表に従って `CookieAuthenticationOptions` 設定の `Cookie.SameSite` の設定に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="524d5-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="524d5-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="524d5-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="524d5-240">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="524d5-240">Cookie.SameSite</span></span> | <span data-ttu-id="524d5-241">結果の SameSite 設定</span><span class="sxs-lookup"><span data-stu-id="524d5-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="524d5-242">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-242">SameSiteMode.None</span></span>     | <span data-ttu-id="524d5-243">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-243">SameSiteMode.None</span></span><br><span data-ttu-id="524d5-244">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-245">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="524d5-246">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-246">SameSiteMode.None</span></span><br><span data-ttu-id="524d5-247">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-248">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="524d5-249">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="524d5-250">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-250">SameSiteMode.None</span></span><br><span data-ttu-id="524d5-251">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-252">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="524d5-253">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-254">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-255">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="524d5-256">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="524d5-257">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="524d5-257">SameSiteMode.None</span></span><br><span data-ttu-id="524d5-258">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="524d5-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="524d5-259">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="524d5-260">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="524d5-261">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="524d5-262">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="524d5-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="524d5-263">認証 cookie を作成する</span><span class="sxs-lookup"><span data-stu-id="524d5-263">Create an authentication cookie</span></span>

<span data-ttu-id="524d5-264">ユーザー情報を保持する cookie を作成するには、<xref:System.Security.Claims.ClaimsPrincipal>を構築します。</span><span class="sxs-lookup"><span data-stu-id="524d5-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="524d5-265">ユーザー情報がシリアル化され、cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="524d5-266">必要な <xref:System.Security.Claims.Claim>s を持つ <xref:System.Security.Claims.ClaimsIdentity> を作成し、<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> を呼び出してユーザーにサインインします。</span><span class="sxs-lookup"><span data-stu-id="524d5-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="524d5-267">`SignInAsync` は、暗号化されたクッキーを作成し、現在の応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="524d5-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="524d5-268">`AuthenticationScheme` が指定されていない場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="524d5-269">暗号化には ASP.NET Core の[データ保護](xref:security/data-protection/using-data-protection)システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="524d5-270">複数のコンピューターでホストされているアプリの場合、アプリ間で負荷を分散する場合、または web ファームを使用する場合は、同じキーリングとアプリ識別子を使用するように[データ保護を構成](xref:security/data-protection/configuration/overview)します。</span><span class="sxs-lookup"><span data-stu-id="524d5-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="524d5-271">サインアウトする</span><span class="sxs-lookup"><span data-stu-id="524d5-271">Sign out</span></span>

<span data-ttu-id="524d5-272">現在のユーザーをサインアウトして cookie を削除するには、<xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="524d5-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="524d5-273">`CookieAuthenticationDefaults.AuthenticationScheme` (または "Cookie") がスキームとして使用されていない場合 (例: "ContosoCookie")、認証プロバイダーを構成するときに使用するスキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="524d5-274">それ以外の場合は、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="524d5-275">バックエンドの変更に対処する</span><span class="sxs-lookup"><span data-stu-id="524d5-275">React to back-end changes</span></span>

<span data-ttu-id="524d5-276">Cookie が作成されると、cookie は id の単一のソースになります。</span><span class="sxs-lookup"><span data-stu-id="524d5-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="524d5-277">バックエンドシステムでユーザーアカウントが無効になっている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="524d5-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="524d5-278">アプリの cookie 認証システムは、認証 cookie に基づいて要求を処理し続けます。</span><span class="sxs-lookup"><span data-stu-id="524d5-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="524d5-279">認証 cookie が有効である限り、ユーザーはアプリにサインインしたままになります。</span><span class="sxs-lookup"><span data-stu-id="524d5-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="524d5-280"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> イベントを使用して、cookie id の検証をインターセプトおよびオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="524d5-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="524d5-281">すべての要求で cookie を検証すると、アプリにアクセスするユーザーが失効するリスクが軽減されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="524d5-282">Cookie を検証する方法の1つは、ユーザーデータベースがいつ変更されたかを追跡することです。</span><span class="sxs-lookup"><span data-stu-id="524d5-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="524d5-283">ユーザーの cookie が発行されてからデータベースが変更されていない場合、cookie が有効である場合、ユーザーを再認証する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="524d5-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="524d5-284">サンプルアプリでは、データベースは `IUserRepository` に実装され、`LastChanged` 値を格納します。</span><span class="sxs-lookup"><span data-stu-id="524d5-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="524d5-285">データベースでユーザーが更新されると、`LastChanged` の値が現在の時刻に設定されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="524d5-286">`LastChanged` 値に基づいてデータベースが変更されたときに cookie を無効にするには、データベースの現在の `LastChanged` 値を含む `LastChanged` 要求を使用して cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="524d5-287">`ValidatePrincipal` イベントのオーバーライドを実装するには、<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>から派生したクラスに次のシグネチャを持つメソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="524d5-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="524d5-288">`CookieAuthenticationEvents`の実装例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="524d5-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="524d5-289">Cookie サービスの登録時に、`Startup.ConfigureServices` メソッドでイベントインスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="524d5-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="524d5-290">`CustomCookieAuthenticationEvents` クラスの[スコープ付きサービス登録](xref:fundamentals/dependency-injection#service-lifetimes)を指定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="524d5-291">ユーザーの名前が&mdash;更新された場合に、セキュリティに影響を与えないようにするという状況を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="524d5-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="524d5-292">ユーザープリンシパルを非破壊更新する場合は、`context.ReplacePrincipal` を呼び出し、`context.ShouldRenew` プロパティを `true`に設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="524d5-293">ここで説明する方法は、すべての要求でトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="524d5-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="524d5-294">すべての要求に対して認証 cookie を検証すると、アプリのパフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="524d5-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="524d5-295">永続的な cookie</span><span class="sxs-lookup"><span data-stu-id="524d5-295">Persistent cookies</span></span>

<span data-ttu-id="524d5-296">Cookie がブラウザーセッション間で保持されるようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="524d5-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="524d5-297">この永続化を有効にする必要があるのは、サインイン時に [忘れずに保存] チェックボックスをオンにして、明示的なユーザーの同意を有効にすることだけです。</span><span class="sxs-lookup"><span data-stu-id="524d5-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="524d5-298">次のコードスニペットは、ブラウザーのクロージャを介して存続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="524d5-299">以前に構成したスライド式有効期限の設定はすべて受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="524d5-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="524d5-300">ブラウザーを閉じている間に cookie の有効期限が切れると、ブラウザーは再起動後に cookie をクリアします。</span><span class="sxs-lookup"><span data-stu-id="524d5-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="524d5-301"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>で `true` に <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> を設定します。</span><span class="sxs-lookup"><span data-stu-id="524d5-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="524d5-302">クッキーの絶対有効期限</span><span class="sxs-lookup"><span data-stu-id="524d5-302">Absolute cookie expiration</span></span>

<span data-ttu-id="524d5-303">絶対有効期限は <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>で設定できます。</span><span class="sxs-lookup"><span data-stu-id="524d5-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="524d5-304">永続的なクッキーを作成するには、`IsPersistent` も設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="524d5-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="524d5-305">それ以外の場合、cookie はセッションベースの有効期間で作成され、保持する認証チケットの前または後に有効期限が切れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="524d5-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="524d5-306">`ExpiresUtc` が設定されている場合は <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>の <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> オプションの値が上書きされます (設定されている場合)。</span><span class="sxs-lookup"><span data-stu-id="524d5-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="524d5-307">次のコードスニペットでは、20分間継続する id とそれに対応する cookie を作成します。</span><span class="sxs-lookup"><span data-stu-id="524d5-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="524d5-308">これにより、以前に構成したスライド式有効期限の設定は無視されます。</span><span class="sxs-lookup"><span data-stu-id="524d5-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="524d5-309">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="524d5-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="524d5-310">ポリシーベースのロールチェック</span><span class="sxs-lookup"><span data-stu-id="524d5-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
