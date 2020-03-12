---
title: ASP.NET Core Id を使用しない Facebook、Google、および外部プロバイダーの認証
author: rick-anderson
description: ASP.NET Core Id を使用しない Facebook、Google、Twitter などのアカウントユーザー認証の使用について説明します。
ms.author: riande
ms.date: 12/10/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: b30ce7055b35b721c7fb83b61a328200d6a136b1
ms.sourcegitcommit: 3ca4a2235a8129def9e480d0a6ad54cc856920ec
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025395"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="fbc7f-103">ASP.NET Core Id なしでソーシャルサインインプロバイダー認証を使用する</span><span class="sxs-lookup"><span data-stu-id="fbc7f-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="fbc7f-104">[Kirk Larkin](https://twitter.com/serpent5)と[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fbc7f-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fbc7f-105"><xref:security/authentication/social/index> では、ユーザーが外部認証プロバイダーからの資格情報で OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="fbc7f-106">このトピックで説明するアプローチには、認証プロバイダーとしての ASP.NET Core Id が含まれています。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="fbc7f-107">このサンプルでは、ASP.NET Core Id**なしで**外部認証プロバイダーを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="fbc7f-108">これは ASP.NET Core Id のすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="fbc7f-109">このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="fbc7f-110">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="fbc7f-111">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="fbc7f-112">Facebook 認証</span><span class="sxs-lookup"><span data-stu-id="fbc7f-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="fbc7f-113">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="fbc7f-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="fbc7f-114">Twitter 認証</span><span class="sxs-lookup"><span data-stu-id="fbc7f-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="fbc7f-115">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="fbc7f-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="fbc7f-116">構成</span><span class="sxs-lookup"><span data-stu-id="fbc7f-116">Configuration</span></span>

<span data-ttu-id="fbc7f-117">`ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>、<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>、および <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> の各メソッドを使用して、アプリの認証スキームを構成します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="fbc7f-118"><xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出すと、アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>が設定されます。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="fbc7f-119">`DefaultScheme` は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="fbc7f-120">アプリの `DefaultScheme` を[Cookieauthenticationdefaults に設定します。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="fbc7f-121">アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、`ChallengeAsync`の呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="fbc7f-122">`DefaultChallengeScheme` は `DefaultScheme`を上書きします。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="fbc7f-123">設定時に `DefaultScheme` をオーバーライドするその他のプロパティについては、「<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="fbc7f-124">`Startup.Configure`で、`UseRouting` と `UseEndpoints`の呼び出しの間で `UseAuthentication` と `UseAuthorization` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="fbc7f-125">これにより、`HttpContext.User` プロパティが設定され、要求の承認ミドルウェアが実行されます。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="fbc7f-126">認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="fbc7f-127">Cookie 認証の詳細については、「<xref:security/authentication/cookie>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="fbc7f-128">承認の適用</span><span class="sxs-lookup"><span data-stu-id="fbc7f-128">Apply authorization</span></span>

<span data-ttu-id="fbc7f-129">コントローラー、アクション、またはページに `AuthorizeAttribute` 属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="fbc7f-130">次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="fbc7f-131">サインアウトする</span><span class="sxs-lookup"><span data-stu-id="fbc7f-131">Sign out</span></span>

<span data-ttu-id="fbc7f-132">現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="fbc7f-133">次のコードでは、*インデックス*ページに `Logout` ページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="fbc7f-134">`SignOutAsync` の呼び出しで認証スキームが指定されていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="fbc7f-135">アプリの `CookieAuthenticationDefaults.AuthenticationScheme` の `DefaultScheme` はフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbc7f-136">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="fbc7f-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fbc7f-137"><xref:security/authentication/social/index> では、ユーザーが外部認証プロバイダーからの資格情報で OAuth 2.0 を使用してサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="fbc7f-138">このトピックで説明するアプローチには、認証プロバイダーとしての ASP.NET Core Id が含まれています。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="fbc7f-139">このサンプルでは、ASP.NET Core Id**なしで**外部認証プロバイダーを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="fbc7f-140">これは ASP.NET Core Id のすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="fbc7f-141">このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="fbc7f-142">Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="fbc7f-143">別の外部認証プロバイダーと統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="fbc7f-144">Facebook 認証</span><span class="sxs-lookup"><span data-stu-id="fbc7f-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="fbc7f-145">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="fbc7f-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="fbc7f-146">Twitter 認証</span><span class="sxs-lookup"><span data-stu-id="fbc7f-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="fbc7f-147">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="fbc7f-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="fbc7f-148">構成</span><span class="sxs-lookup"><span data-stu-id="fbc7f-148">Configuration</span></span>

<span data-ttu-id="fbc7f-149">`ConfigureServices` メソッドで、`AddAuthentication`、`AddCookie`、および `AddGoogle` の各メソッドを使用して、アプリの認証スキームを構成します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="fbc7f-150">[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)を呼び出すと、アプリの[defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="fbc7f-151">`DefaultScheme` は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="fbc7f-152">アプリの `DefaultScheme` を[Cookieauthenticationdefaults に設定します。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="fbc7f-153">アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、`ChallengeAsync`の呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="fbc7f-154">`DefaultChallengeScheme` は `DefaultScheme`を上書きします。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="fbc7f-155">設定時に `DefaultScheme` をオーバーライドするその他のプロパティについては、「<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="fbc7f-156">`Configure` メソッドで、`UseAuthentication` メソッドを呼び出して、`HttpContext.User` プロパティを設定する認証ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="fbc7f-157">`UseMvcWithDefaultRoute` または `UseMvc`を呼び出す前に、`UseAuthentication` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="fbc7f-158">認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="fbc7f-159">Cookie 認証の詳細については、「<xref:security/authentication/cookie>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="fbc7f-160">承認の適用</span><span class="sxs-lookup"><span data-stu-id="fbc7f-160">Apply authorization</span></span>

<span data-ttu-id="fbc7f-161">コントローラー、アクション、またはページに `AuthorizeAttribute` 属性を適用して、アプリの認証構成をテストします。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="fbc7f-162">次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="fbc7f-163">サインアウトする</span><span class="sxs-lookup"><span data-stu-id="fbc7f-163">Sign out</span></span>

<span data-ttu-id="fbc7f-164">現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="fbc7f-165">次のコードでは、*インデックス*ページに `Logout` ページハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="fbc7f-166">`SignOutAsync` の呼び出しで認証スキームが指定されていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="fbc7f-167">アプリの `CookieAuthenticationDefaults.AuthenticationScheme` の `DefaultScheme` はフォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="fbc7f-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbc7f-168">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="fbc7f-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
