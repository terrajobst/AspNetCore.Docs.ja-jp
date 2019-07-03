---
title: Facebook、Google、および ASP.NET Core Identity なしで認証に外部プロバイダー
author: rick-anderson
description: Facebook、Google、Twitter、ASP.NET Core Identity なしなどのアカウント ユーザー認証を使用しての説明。
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: e67da513fef1ce453110c465b08e9c7965e71df5
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557651"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="30ffc-103">ASP.NET Core Identity なしのソーシャル サインイン プロバイダー認証を使用します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="30ffc-104"><xref:security/authentication/social/index> OAuth 2.0 を使用して外部認証プロバイダーからの資格情報でサインインするユーザーを有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="30ffc-105">このトピックで説明されているアプローチには、認証プロバイダーとしての ASP.NET Core Identity が含まれています。</span><span class="sxs-lookup"><span data-stu-id="30ffc-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="30ffc-106">このサンプルは、外部認証プロバイダーを使用する方法を示します。**せず**ASP.NET Core Identity です。</span><span class="sxs-lookup"><span data-stu-id="30ffc-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="30ffc-107">これは、すべての ASP.NET Core Identity では、機能を必要としないが、まだ信頼されている外部認証プロバイダーとの統合を必要とするアプリの場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="30ffc-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="30ffc-108">このサンプルでは[Google 認証](xref:security/authentication/google-logins)ユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="30ffc-109">認証は Google を使用して、Google にサインイン プロセスの管理の複雑さの多くを移動します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="30ffc-110">別の外部認証プロバイダーを統合するには、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ffc-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="30ffc-111">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="30ffc-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="30ffc-112">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="30ffc-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="30ffc-113">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="30ffc-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="30ffc-114">その他のプロバイダー</span><span class="sxs-lookup"><span data-stu-id="30ffc-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="30ffc-115">構成</span><span class="sxs-lookup"><span data-stu-id="30ffc-115">Configuration</span></span>

<span data-ttu-id="30ffc-116">`ConfigureServices`メソッドで、アプリの認証スキームを構成、 `AddAuthentication`、`AddCookie`と`AddGoogle`メソッド。</span><span class="sxs-lookup"><span data-stu-id="30ffc-116">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie` and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="30ffc-117">呼び出し[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)設定アプリの[型](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-117">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="30ffc-118">`DefaultScheme`次で使用される既定のスキームは、`HttpContext`認証の拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="30ffc-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="30ffc-119">設定アプリの`DefaultScheme`に[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (「クッキー」) これらの拡張メソッドの既定のスキームとして Cookie を使用するアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="30ffc-120">設定アプリの<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>に[GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") への呼び出しの既定のスキームとして Google を使用するアプリを構成します`ChallengeAsync`します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="30ffc-121">`DefaultChallengeScheme` オーバーライド`DefaultScheme`します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="30ffc-122">参照してください<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>をオーバーライドする追加のプロパティの`DefaultScheme`設定するとします。</span><span class="sxs-lookup"><span data-stu-id="30ffc-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="30ffc-123">`Configure`メソッドを呼び出し、`UseAuthentication`を設定する、認証ミドルウェアを呼び出すメソッドを`HttpContext.User`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="30ffc-123">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="30ffc-124">呼び出す、`UseAuthentication`メソッドを呼び出す前に`UseMvcWithDefaultRoute`または`UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="30ffc-124">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="30ffc-125">認証スキームと cookie 認証の詳細については、次を参照してください。<xref:security/authentication/cookie>します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="applying-basic-authorization"></a><span data-ttu-id="30ffc-126">基本認証を適用します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-126">Applying basic authorization</span></span>

<span data-ttu-id="30ffc-127">適用することで、アプリの認証構成をテスト、`AuthorizeAttribute`属性をコント ローラー、アクション、またはページ。</span><span class="sxs-lookup"><span data-stu-id="30ffc-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="30ffc-128">次のコードへのアクセスを制限する、*プライバシー*に対して認証されたユーザー ページ。</span><span class="sxs-lookup"><span data-stu-id="30ffc-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="30ffc-129">サインアウト</span><span class="sxs-lookup"><span data-stu-id="30ffc-129">Sign out</span></span>

<span data-ttu-id="30ffc-130">現在のユーザーをサインアウトし、その cookie を削除、 [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0)します。</span><span class="sxs-lookup"><span data-stu-id="30ffc-130">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span></span> <span data-ttu-id="30ffc-131">次のコードを追加、`Logout`ページ ハンドラーに、*インデックス*ページ。</span><span class="sxs-lookup"><span data-stu-id="30ffc-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="30ffc-132">注意への呼び出し`SignOutAsync`認証方式が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="30ffc-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="30ffc-133">アプリの`DefaultScheme`の`CookieAuthenticationDefaults.AuthenticationScheme`フォールバックとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="30ffc-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30ffc-134">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="30ffc-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>
