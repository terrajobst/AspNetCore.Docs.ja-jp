---
title: ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する
author: guardrex
description: 外部プロバイダーから追加の要求とトークンを確立する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: cdf263df8d1aa17ea3820a16ecbd10abce9d683d
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925152"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する

作成者: [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。 各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>前提条件

アプリでサポートする外部認証プロバイダーを決定します。 各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。 詳細については、「 <xref:security/authentication/social/index> 」を参照してください。 このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。

## <a name="set-the-client-id-and-client-secret"></a>クライアント ID とクライアントシークレットを設定する

OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。 アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。 アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。 詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Google での認証](xref:security/authentication/google-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [他の認証プロバイダー](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>認証スコープを確立する

@No__t-0 を指定して、プロバイダーから取得するアクセス許可の一覧を指定します。 共通外部プロバイダーの認証スコープは、次の表に表示されます。

| プロバイダー  | スコープ                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| マイクロソフト | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

サンプルアプリでは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> で <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> が呼び出されると、Google の `userinfo.profile` スコープがフレームワークによって自動的に追加されます。 アプリに追加のスコープが必要な場合は、それらをオプションに追加します。 次の例では、ユーザーの誕生日を取得するために、Google `https://www.googleapis.com/auth/user.birthday.read` スコープが追加されています。

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>ユーザーデータキーをマップして要求を作成する

プロバイダーのオプションで、サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータ内のキー/サブキーごとに、<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> または <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> を指定します。 要求の種類の詳細については、「<xref:System.Security.Claims.ClaimTypes>」を参照してください。

サンプルアプリでは、Google ユーザーデータの `locale` および @no__t キーから、locale (`urn:google:locale`) 要求と画像 (@no__t) 要求を作成します。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

@No__t-0 の場合、<xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) は、<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> でアプリにサインインします。 サインインプロセスの間、<xref:Microsoft.AspNetCore.Identity.UserManager%601> は @no__t から利用可能なユーザーデータの @no__t 1 要求を格納できます。

サンプルアプリでは、`OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml*) によって、<xref:System.Security.Claims.ClaimTypes.GivenName> の要求を含む、署名された `ApplicationUser` のロケール (@no__t 2) と画像 (@no__t 3) の要求が確立されます。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

既定では、ユーザーの要求は認証 cookie に格納されます。 認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。

* ブラウザーは cookie ヘッダーが長すぎることを検出します。
* 要求の全体的なサイズが大きすぎます。

ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。

* 要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。
* Cookie 認証ミドルウェアの <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> のカスタム <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> を使用して、要求間で id を格納します。 小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。

## <a name="save-the-access-token"></a>アクセストークンを保存する

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> は、承認が正常に完了した後、アクセストークンと更新トークンを <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> に格納するかどうかを定義します。 `SaveTokens` は、最終的な認証クッキーのサイズを小さくするために、既定で `false` に設定されています。

サンプルアプリでは、`SaveTokens` の値が <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> の `true` に設定されます。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

@No__t-0 を実行する場合は、外部プロバイダーのアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) を @no__t の `AuthenticationProperties` に格納します。

このサンプルアプリでは、アクセストークンを `OnPostConfirmationAsync` (新しいユーザー登録) に保存し、*アカウント/ExternalLogin. cshtml. cs*で `OnGetCallbackAsync` (以前に登録されたユーザー) に保存します。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>カスタムトークンを追加する方法

@No__t-0 の一部として格納されているカスタムトークンを追加する方法を示すために、サンプルアプリでは、`TicketCreated` の[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)のために、現在の <xref:System.DateTime> を含む <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> を追加します。

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>要求の作成と追加

フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。 詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。

ユーザーは、<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> から派生させ、抽象 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> メソッドを実装することによって、カスタムアクションを定義できます。

詳細については、「 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims> 」を参照してください。

## <a name="removal-of-claim-actions-and-claims"></a>要求アクションと要求の削除

[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> のすべての要求アクションをコレクションから削除します。 [DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、id から指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> の要求を削除します。 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。

## <a name="sample-app-output"></a>サンプルアプリの出力

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。 各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>前提条件

アプリでサポートする外部認証プロバイダーを決定します。 各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。 詳細については、「 <xref:security/authentication/social/index> 」を参照してください。 このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。

## <a name="set-the-client-id-and-client-secret"></a>クライアント ID とクライアントシークレットを設定する

OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。 アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。 アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。 詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。

* [Facebook での認証](xref:security/authentication/facebook-logins)
* [Google での認証](xref:security/authentication/google-logins)
* [Microsoft での認証](xref:security/authentication/microsoft-logins)
* [Twitter での認証](xref:security/authentication/twitter-logins)
* [他の認証プロバイダー](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>認証スコープを確立する

@No__t-0 を指定して、プロバイダーから取得するアクセス許可の一覧を指定します。 共通外部プロバイダーの認証スコープは、次の表に表示されます。

| プロバイダー  | スコープ                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| マイクロソフト | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

サンプルアプリでは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> で <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> が呼び出されると、Google の `userinfo.profile` スコープがフレームワークによって自動的に追加されます。 アプリに追加のスコープが必要な場合は、それらをオプションに追加します。 次の例では、ユーザーの誕生日を取得するために、Google `https://www.googleapis.com/auth/user.birthday.read` スコープが追加されています。

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>ユーザーデータキーをマップして要求を作成する

プロバイダーのオプションで、サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータ内のキー/サブキーごとに、<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> または <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> を指定します。 要求の種類の詳細については、「<xref:System.Security.Claims.ClaimTypes>」を参照してください。

サンプルアプリでは、Google ユーザーデータの `locale` および @no__t キーから、locale (`urn:google:locale`) 要求と画像 (@no__t) 要求を作成します。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

@No__t-0 の場合、<xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) は、<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> でアプリにサインインします。 サインインプロセスの間、<xref:Microsoft.AspNetCore.Identity.UserManager%601> は @no__t から利用可能なユーザーデータの @no__t 1 要求を格納できます。

サンプルアプリでは、`OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml*) によって、<xref:System.Security.Claims.ClaimTypes.GivenName> の要求を含む、署名された `ApplicationUser` のロケール (@no__t 2) と画像 (@no__t 3) の要求が確立されます。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

既定では、ユーザーの要求は認証 cookie に格納されます。 認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。

* ブラウザーは cookie ヘッダーが長すぎることを検出します。
* 要求の全体的なサイズが大きすぎます。

ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。

* 要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。
* Cookie 認証ミドルウェアの <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> のカスタム <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> を使用して、要求間で id を格納します。 小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。

## <a name="save-the-access-token"></a>アクセストークンを保存する

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> は、承認が正常に完了した後、アクセストークンと更新トークンを <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> に格納するかどうかを定義します。 `SaveTokens` は、最終的な認証クッキーのサイズを小さくするために、既定で `false` に設定されています。

サンプルアプリでは、`SaveTokens` の値が <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> の `true` に設定されます。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

@No__t-0 を実行する場合は、外部プロバイダーのアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) を @no__t の `AuthenticationProperties` に格納します。

このサンプルアプリでは、アクセストークンを `OnPostConfirmationAsync` (新しいユーザー登録) に保存し、*アカウント/ExternalLogin. cshtml. cs*で `OnGetCallbackAsync` (以前に登録されたユーザー) に保存します。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>カスタムトークンを追加する方法

@No__t-0 の一部として格納されているカスタムトークンを追加する方法を示すために、サンプルアプリでは、`TicketCreated` の[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)のために、現在の <xref:System.DateTime> を含む <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> を追加します。

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>要求の作成と追加

フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。 詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。

ユーザーは、<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> から派生させ、抽象 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> メソッドを実装することによって、カスタムアクションを定義できます。

詳細については、「 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims> 」を参照してください。

## <a name="removal-of-claim-actions-and-claims"></a>要求アクションと要求の削除

[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> のすべての要求アクションをコレクションから削除します。 [DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、id から指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> の要求を削除します。 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。

## <a name="sample-app-output"></a>サンプルアプリの出力

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* [aspnet/AspNetCore engineering のサンプルアプリ](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)&ndash; リンクされたサンプルアプリは、 [Aspnet/AspNetCore GitHub リポジトリの](https://github.com/aspnet/AspNetCore)`master` エンジニアリングブランチにあります。 @No__t-0 分岐には、ASP.NET Core の次のリリースのアクティブな開発のコードが含まれています。 リリースされたバージョンの ASP.NET Core のサンプルアプリのバージョンを表示するには、 **[ブランチ]** ドロップダウンリストを使用してリリースブランチを選択します (例: `release/{X.Y}`)。
