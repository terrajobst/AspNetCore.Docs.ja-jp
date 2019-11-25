---
title: ASP.NET Core Id を使用しない Facebook、Google、および外部プロバイダーの認証
author: rick-anderson
description: ASP.NET Core Id を使用しない Facebook、Google、Twitter などのアカウントユーザー認証の使用について説明します。
ms.author: riande
ms.date: 11/19/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 680ea091dcc5ed7f94879b5d277e8be7e5abeb7b
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289114"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="51efb-103">ASP.NET Core Id なしでソーシャルサインインプロバイダー認証を使用する</span><span class="sxs-lookup"><span data-stu-id="51efb-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51efb-104"><xref:security/authentication/social/index> では、ユーザーが外部認証プロバイダーからの資格情報で OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="51efb-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="51efb-105">このトピックで説明するアプローチには、認証プロバイダーとしての ASP.NET Core Id が含まれています。</span><span class="sxs-lookup"><span data-stu-id="51efb-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="51efb-106">このサンプルでは、ASP.NET Core Id**なしで**外部認証プロバイダーを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="51efb-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="51efb-107">これは ASP.NET Core Id のすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="51efb-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="51efb-108">このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="51efb-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="51efb-109">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="51efb-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="51efb-110">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="51efb-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="51efb-111">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="51efb-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="51efb-112">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="51efb-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="51efb-113">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="51efb-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="51efb-114">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="51efb-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="51efb-115">構成</span><span class="sxs-lookup"><span data-stu-id="51efb-115">Configuration</span></span>

<span data-ttu-id="51efb-116">`ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>、<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>、および <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> の各メソッドを使用して、アプリの認証スキームを構成します。</span><span class="sxs-lookup"><span data-stu-id="51efb-116">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="51efb-117"><xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出すと、アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>が設定されます。</span><span class="sxs-lookup"><span data-stu-id="51efb-117">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="51efb-118">`DefaultScheme` は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="51efb-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="51efb-119">アプリの `DefaultScheme` を[Cookieauthenticationdefaults に設定します。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="51efb-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="51efb-120">アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、`ChallengeAsync`の呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="51efb-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="51efb-121">`DefaultChallengeScheme` は `DefaultScheme`を上書きします。</span><span class="sxs-lookup"><span data-stu-id="51efb-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="51efb-122">設定時に `DefaultScheme` をオーバーライドするその他のプロパティについては、「<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="51efb-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="51efb-123">`Startup.Configure`で、`UseRouting` と `UseEndpoints`の呼び出しの間で `UseAuthentication` と `UseAuthorization` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="51efb-123">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="51efb-124">これにより、`HttpContext.User` プロパティが設定され、要求の承認ミドルウェアが実行されます。</span><span class="sxs-lookup"><span data-stu-id="51efb-124">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="51efb-125">認証スキームと cookie 認証の詳細については、「<xref:security/authentication/cookie>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="51efb-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="51efb-126">承認の適用</span><span class="sxs-lookup"><span data-stu-id="51efb-126">Apply authorization</span></span>

<span data-ttu-id="51efb-127">コントローラー、アクション、またはページに `AuthorizeAttribute` 属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="51efb-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="51efb-128">次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="51efb-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="51efb-129">サインアウト</span><span class="sxs-lookup"><span data-stu-id="51efb-129">Sign out</span></span>

<span data-ttu-id="51efb-130">現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="51efb-130">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="51efb-131">次のコードでは、*インデックス*ページに `Logout` ページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="51efb-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="51efb-132">`SignOutAsync` の呼び出しで認証スキームが指定されていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="51efb-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="51efb-133">アプリの `CookieAuthenticationDefaults.AuthenticationScheme` の `DefaultScheme` はフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="51efb-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51efb-134">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="51efb-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51efb-135"><xref:security/authentication/social/index> では、ユーザーが外部認証プロバイダーからの資格情報で OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="51efb-135"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="51efb-136">このトピックで説明するアプローチには、認証プロバイダーとしての ASP.NET Core Id が含まれています。</span><span class="sxs-lookup"><span data-stu-id="51efb-136">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="51efb-137">このサンプルでは、ASP.NET Core Id**なしで**外部認証プロバイダーを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="51efb-137">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="51efb-138">これは ASP.NET Core Id のすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="51efb-138">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="51efb-139">このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="51efb-139">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="51efb-140">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="51efb-140">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="51efb-141">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="51efb-141">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="51efb-142">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="51efb-142">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="51efb-143">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="51efb-143">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="51efb-144">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="51efb-144">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="51efb-145">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="51efb-145">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="51efb-146">構成</span><span class="sxs-lookup"><span data-stu-id="51efb-146">Configuration</span></span>

<span data-ttu-id="51efb-147">`ConfigureServices` メソッドで、`AddAuthentication`、`AddCookie`、および `AddGoogle` の各メソッドを使用して、アプリの認証スキームを構成します。</span><span class="sxs-lookup"><span data-stu-id="51efb-147">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="51efb-148">[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)を呼び出すと、アプリの[defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="51efb-148">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="51efb-149">`DefaultScheme` は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="51efb-149">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="51efb-150">アプリの `DefaultScheme` を[Cookieauthenticationdefaults に設定します。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="51efb-150">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="51efb-151">アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、`ChallengeAsync`の呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="51efb-151">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="51efb-152">`DefaultChallengeScheme` は `DefaultScheme`を上書きします。</span><span class="sxs-lookup"><span data-stu-id="51efb-152">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="51efb-153">設定時に `DefaultScheme` をオーバーライドするその他のプロパティについては、「<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="51efb-153">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="51efb-154">`Configure` メソッドで、`UseAuthentication` メソッドを呼び出して、`HttpContext.User` プロパティを設定する認証ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="51efb-154">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="51efb-155">`UseMvcWithDefaultRoute` または `UseMvc`を呼び出す前に、`UseAuthentication` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="51efb-155">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="51efb-156">認証スキームと cookie 認証の詳細については、「<xref:security/authentication/cookie>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="51efb-156">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="51efb-157">承認の適用</span><span class="sxs-lookup"><span data-stu-id="51efb-157">Apply authorization</span></span>

<span data-ttu-id="51efb-158">コントローラー、アクション、またはページに `AuthorizeAttribute` 属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="51efb-158">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="51efb-159">次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="51efb-159">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="51efb-160">サインアウト</span><span class="sxs-lookup"><span data-stu-id="51efb-160">Sign out</span></span>

<span data-ttu-id="51efb-161">現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="51efb-161">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="51efb-162">次のコードでは、*インデックス*ページに `Logout` ページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="51efb-162">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="51efb-163">`SignOutAsync` の呼び出しで認証スキームが指定されていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="51efb-163">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="51efb-164">アプリの `CookieAuthenticationDefaults.AuthenticationScheme` の `DefaultScheme` はフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="51efb-164">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51efb-165">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="51efb-165">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
