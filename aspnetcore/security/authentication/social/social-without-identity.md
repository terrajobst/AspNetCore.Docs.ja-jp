---
title: ASP.NET Core Id を使用しない Facebook、Google、および外部プロバイダーの認証
author: rick-anderson
description: ASP.NET Core Id を使用しない Facebook、Google、Twitter などのアカウントユーザー認証の使用について説明します。
ms.author: riande
ms.date: 12/10/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 612964ec9ed4975cdc81780dda3bac6cce96037f
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359059"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>ASP.NET Core Id なしでソーシャルサインインプロバイダー認証を使用する

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> では、ユーザーが外部認証プロバイダーからの資格情報で OAuth 2.0 を使用してサインインできるようにする方法について説明します。 このトピックで説明するアプローチには、認証プロバイダーとしての ASP.NET Core Id が含まれています。

このサンプルでは、ASP.NET Core Id**なしで**外部認証プロバイダーを使用する方法を示します。 これは ASP.NET Core Id のすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。

このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。 Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。 別の外部認証プロバイダーと統合するには、次のトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [その他のプロバイダー](xref:security/authentication/otherlogins)

## <a name="configuration"></a>の構成

`ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>、<xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>、および <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> の各メソッドを使用して、アプリの認証スキームを構成します。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出すと、アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>が設定されます。 `DefaultScheme` は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

アプリの `DefaultScheme` を[Cookieauthenticationdefaults に設定します。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。 アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、`ChallengeAsync`の呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。 `DefaultChallengeScheme` は `DefaultScheme`を上書きします。 設定時に `DefaultScheme` をオーバーライドするその他のプロパティについては、「<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>」を参照してください。

`Startup.Configure`で、`UseRouting` と `UseEndpoints`の呼び出しの間で `UseAuthentication` と `UseAuthorization` を呼び出します。 これにより、`HttpContext.User` プロパティが設定され、要求の承認ミドルウェアが実行されます。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。 Cookie 認証の詳細については、「<xref:security/authentication/cookie>」を参照してください。

## <a name="apply-authorization"></a>承認の適用

コントローラー、アクション、またはページに `AuthorizeAttribute` 属性を適用して、アプリの認証構成をテストします。 次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。 次のコードでは、*インデックス*ページに `Logout` ページハンドラーを追加します。

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

`SignOutAsync` の呼び出しで認証スキームが指定されていないことに注意してください。 アプリの `CookieAuthenticationDefaults.AuthenticationScheme` の `DefaultScheme` はフォールバックとして使用されます。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> では、ユーザーが外部認証プロバイダーからの資格情報で OAuth 2.0 を使用してサインインできるようにする方法について説明します。 このトピックで説明するアプローチには、認証プロバイダーとしての ASP.NET Core Id が含まれています。

このサンプルでは、ASP.NET Core Id**なしで**外部認証プロバイダーを使用する方法を示します。 これは ASP.NET Core Id のすべての機能を必要としないが、信頼された外部認証プロバイダーとの統合を必要とするアプリに便利です。

このサンプルでは、ユーザーの認証に[Google 認証](xref:security/authentication/google-logins)を使用します。 Google 認証を使用すると、サインインプロセスを管理するための多くの複雑な作業が Google に移ります。 別の外部認証プロバイダーと統合するには、次のトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [その他のプロバイダー](xref:security/authentication/otherlogins)

## <a name="configuration"></a>の構成

`ConfigureServices` メソッドで、`AddAuthentication`、`AddCookie`、および `AddGoogle` の各メソッドを使用して、アプリの認証スキームを構成します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

[Addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)を呼び出すと、アプリの[defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)が設定されます。 `DefaultScheme` は、次の `HttpContext` 認証拡張メソッドによって使用される既定のスキームです。

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

アプリの `DefaultScheme` を[Cookieauthenticationdefaults に設定します。 AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") は、これらの拡張メソッドの既定のスキームとして cookie を使用するようにアプリを構成します。 アプリの <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> を[GoogleDefaults](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") に設定すると、`ChallengeAsync`の呼び出しの既定のスキームとして Google を使用するようにアプリが構成されます。 `DefaultChallengeScheme` は `DefaultScheme`を上書きします。 設定時に `DefaultScheme` をオーバーライドするその他のプロパティについては、「<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>」を参照してください。

`Configure` メソッドで、`UseAuthentication` メソッドを呼び出して、`HttpContext.User` プロパティを設定する認証ミドルウェアを呼び出します。 `UseMvcWithDefaultRoute` または `UseMvc`を呼び出す前に、`UseAuthentication` メソッドを呼び出します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

認証方式の詳細については、「[認証の概念](xref:security/authentication/index#authentication-concepts)」を参照してください。 Cookie 認証の詳細については、「<xref:security/authentication/cookie>」を参照してください。

## <a name="apply-authorization"></a>承認の適用

コントローラー、アクション、またはページに `AuthorizeAttribute` 属性を適用して、アプリの認証構成をテストします。 次のコードは、認証されているユーザーへの*プライバシー*ページへのアクセスを制限します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトして cookie を削除するには、 [Signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)呼び出します。 次のコードでは、*インデックス*ページに `Logout` ページハンドラーを追加します。

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

`SignOutAsync` の呼び出しで認証スキームが指定されていないことに注意してください。 アプリの `CookieAuthenticationDefaults.AuthenticationScheme` の `DefaultScheme` はフォールバックとして使用されます。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
