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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>ASP.NET Core Identity なしのソーシャル サインイン プロバイダー認証を使用します。

<xref:security/authentication/social/index> OAuth 2.0 を使用して外部認証プロバイダーからの資格情報でサインインするユーザーを有効にする方法について説明します。 このトピックで説明されているアプローチには、認証プロバイダーとしての ASP.NET Core Identity が含まれています。

このサンプルは、外部認証プロバイダーを使用する方法を示します。**せず**ASP.NET Core Identity です。 これは、すべての ASP.NET Core Identity では、機能を必要としないが、まだ信頼されている外部認証プロバイダーとの統合を必要とするアプリの場合に便利です。

このサンプルでは[Google 認証](xref:security/authentication/google-logins)ユーザーを認証します。 認証は Google を使用して、Google にサインイン プロセスの管理の複雑さの多くを移動します。 別の外部認証プロバイダーを統合するには、次のトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [その他のプロバイダー](xref:security/authentication/otherlogins)

## <a name="configuration"></a>構成

`ConfigureServices`メソッドで、アプリの認証スキームを構成、 `AddAuthentication`、`AddCookie`と`AddGoogle`メソッド。

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

呼び出し[AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__)設定アプリの[型](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)します。 `DefaultScheme`次で使用される既定のスキームは、`HttpContext`認証の拡張メソッド。

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

設定アプリの`DefaultScheme`に[CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (「クッキー」) これらの拡張メソッドの既定のスキームとして Cookie を使用するアプリを構成します。 設定アプリの<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme>に[GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") への呼び出しの既定のスキームとして Google を使用するアプリを構成します`ChallengeAsync`します。 `DefaultChallengeScheme` オーバーライド`DefaultScheme`します。 参照してください<xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>をオーバーライドする追加のプロパティの`DefaultScheme`設定するとします。

`Configure`メソッドを呼び出し、`UseAuthentication`を設定する、認証ミドルウェアを呼び出すメソッドを`HttpContext.User`プロパティ。 呼び出す、`UseAuthentication`メソッドを呼び出す前に`UseMvcWithDefaultRoute`または`UseMvc`:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

認証スキームと cookie 認証の詳細については、次を参照してください。<xref:security/authentication/cookie>します。

## <a name="applying-basic-authorization"></a>基本認証を適用します。

適用することで、アプリの認証構成をテスト、`AuthorizeAttribute`属性をコント ローラー、アクション、またはページ。 次のコードへのアクセスを制限する、*プライバシー*に対して認証されたユーザー ページ。

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>サインアウト

現在のユーザーをサインアウトし、その cookie を削除、 [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0)します。 次のコードを追加、`Logout`ページ ハンドラーに、*インデックス*ページ。

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

注意への呼び出し`SignOutAsync`認証方式が指定されていません。 アプリの`DefaultScheme`の`CookieAuthenticationDefaults.AuthenticationScheme`フォールバックとして使用されます。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>
