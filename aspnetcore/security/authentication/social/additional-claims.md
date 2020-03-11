---
title: ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する
author: rick-anderson
description: 外部プロバイダーから追加の要求とトークンを確立する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 9dfe5745125e34ed813d078529471a0ba2a53ab0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654662"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="38d65-103">ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する</span><span class="sxs-lookup"><span data-stu-id="38d65-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38d65-104">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="38d65-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="38d65-105">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="38d65-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="38d65-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="38d65-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="38d65-107">前提条件</span><span class="sxs-lookup"><span data-stu-id="38d65-107">Prerequisites</span></span>

<span data-ttu-id="38d65-108">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="38d65-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="38d65-109">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="38d65-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="38d65-110">詳細については、<xref:security/authentication/social/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="38d65-111">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="38d65-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="38d65-112">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="38d65-112">Set the client ID and client secret</span></span>

<span data-ttu-id="38d65-113">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="38d65-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="38d65-114">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="38d65-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="38d65-115">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38d65-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="38d65-116">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="38d65-117">Facebook 認証</span><span class="sxs-lookup"><span data-stu-id="38d65-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="38d65-118">Google 認証</span><span class="sxs-lookup"><span data-stu-id="38d65-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="38d65-119">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="38d65-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="38d65-120">Twitter 認証</span><span class="sxs-lookup"><span data-stu-id="38d65-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="38d65-121">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="38d65-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="38d65-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="38d65-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="38d65-123">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="38d65-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="38d65-124">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="38d65-124">Establish the authentication scope</span></span>

<span data-ttu-id="38d65-125"><xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>を指定して、プロバイダーから取得するアクセス許可の一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="38d65-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="38d65-126">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="38d65-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="38d65-127">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="38d65-127">Provider</span></span>  | <span data-ttu-id="38d65-128">スコープ</span><span class="sxs-lookup"><span data-stu-id="38d65-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="38d65-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="38d65-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="38d65-130">Google</span><span class="sxs-lookup"><span data-stu-id="38d65-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="38d65-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="38d65-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="38d65-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="38d65-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="38d65-133">サンプルアプリでは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>で <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> が呼び出されると、Google の `userinfo.profile` スコープがフレームワークによって自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="38d65-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="38d65-134">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="38d65-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="38d65-135">次の例では、ユーザーの誕生日を取得するために Google `https://www.googleapis.com/auth/user.birthday.read` スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="38d65-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="38d65-136">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="38d65-136">Map user data keys and create claims</span></span>

<span data-ttu-id="38d65-137">プロバイダーのオプションで、サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータの各キー/サブキーに対して、<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> または <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> を指定します。</span><span class="sxs-lookup"><span data-stu-id="38d65-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="38d65-138">要求の種類の詳細については、「<xref:System.Security.Claims.ClaimTypes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="38d65-139">このサンプルアプリでは、Google ユーザーデータの `locale` および `picture` キーから、ロケール (`urn:google:locale`) と画像 (`urn:google:picture`) の要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="38d65-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="38d65-140">`Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`では、<xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) が <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>を使用してアプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="38d65-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="38d65-141">サインインプロセス中に、<xref:Microsoft.AspNetCore.Identity.UserManager%601> は <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>から利用可能なユーザーデータの `ApplicationUser` 要求を格納できます。</span><span class="sxs-lookup"><span data-stu-id="38d65-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="38d65-142">サンプルアプリでは、`OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml*) を使用して、署名された `ApplicationUser`のロケール (`urn:google:locale`) 要求と画像 (`urn:google:picture`) 要求を確立します。これには <xref:System.Security.Claims.ClaimTypes.GivenName>の要求も含まれます。</span><span class="sxs-lookup"><span data-stu-id="38d65-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="38d65-143">既定では、ユーザーの要求は認証 cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="38d65-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="38d65-144">認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="38d65-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="38d65-145">ブラウザーは cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="38d65-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="38d65-146">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="38d65-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="38d65-147">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="38d65-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="38d65-148">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="38d65-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="38d65-149">Cookie 認証ミドルウェアの <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> のカスタム <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> を使用して、要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="38d65-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="38d65-150">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="38d65-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="38d65-151">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="38d65-151">Save the access token</span></span>

<span data-ttu-id="38d65-152">認証が正常に完了した後に、アクセストークンと更新トークンを <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> に格納するかどうかを <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> が定義します。</span><span class="sxs-lookup"><span data-stu-id="38d65-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="38d65-153">`SaveTokens` は、最終的な認証クッキーのサイズを小さくするために、既定で `false` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="38d65-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="38d65-154">このサンプルアプリでは、`SaveTokens` の値を <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>の `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="38d65-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="38d65-155">`OnPostConfirmationAsync` 実行するときに、外部プロバイダーのアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) を `ApplicationUser`の `AuthenticationProperties`に格納します。</span><span class="sxs-lookup"><span data-stu-id="38d65-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="38d65-156">このサンプルアプリでは、アクセストークンを `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録したユーザー) の*アカウント/ExternalLogin. cshtml*に保存します。</span><span class="sxs-lookup"><span data-stu-id="38d65-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="38d65-157">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="38d65-157">How to add additional custom tokens</span></span>

<span data-ttu-id="38d65-158">`SaveTokens`の一部として格納されているカスタムトークンを追加する方法を示すために、サンプルアプリでは、`TicketCreated`の[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)の現在の <xref:System.DateTime> に <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> を追加します。</span><span class="sxs-lookup"><span data-stu-id="38d65-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="38d65-159">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="38d65-159">Creating and adding claims</span></span>

<span data-ttu-id="38d65-160">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="38d65-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="38d65-161">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="38d65-162">ユーザーは、<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> から派生させ、抽象 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> メソッドを実装することによって、カスタムアクションを定義できます。</span><span class="sxs-lookup"><span data-stu-id="38d65-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="38d65-163">詳細については、<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="38d65-164">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="38d65-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="38d65-165">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> のすべての要求アクションをコレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="38d65-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="38d65-166">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> の要求を id から削除します。</span><span class="sxs-lookup"><span data-stu-id="38d65-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="38d65-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は、主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="38d65-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="38d65-168">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="38d65-168">Sample app output</span></span>

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

<span data-ttu-id="38d65-169">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="38d65-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="38d65-170">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="38d65-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="38d65-171">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="38d65-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="38d65-172">前提条件</span><span class="sxs-lookup"><span data-stu-id="38d65-172">Prerequisites</span></span>

<span data-ttu-id="38d65-173">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="38d65-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="38d65-174">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="38d65-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="38d65-175">詳細については、<xref:security/authentication/social/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="38d65-176">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="38d65-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="38d65-177">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="38d65-177">Set the client ID and client secret</span></span>

<span data-ttu-id="38d65-178">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="38d65-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="38d65-179">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="38d65-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="38d65-180">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38d65-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="38d65-181">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="38d65-182">Facebook 認証</span><span class="sxs-lookup"><span data-stu-id="38d65-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="38d65-183">Google 認証</span><span class="sxs-lookup"><span data-stu-id="38d65-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="38d65-184">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="38d65-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="38d65-185">Twitter 認証</span><span class="sxs-lookup"><span data-stu-id="38d65-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="38d65-186">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="38d65-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="38d65-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="38d65-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="38d65-188">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="38d65-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="38d65-189">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="38d65-189">Establish the authentication scope</span></span>

<span data-ttu-id="38d65-190"><xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>を指定して、プロバイダーから取得するアクセス許可の一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="38d65-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="38d65-191">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="38d65-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="38d65-192">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="38d65-192">Provider</span></span>  | <span data-ttu-id="38d65-193">スコープ</span><span class="sxs-lookup"><span data-stu-id="38d65-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="38d65-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="38d65-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="38d65-195">Google</span><span class="sxs-lookup"><span data-stu-id="38d65-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="38d65-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="38d65-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="38d65-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="38d65-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="38d65-198">サンプルアプリでは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>で <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> が呼び出されると、Google の `userinfo.profile` スコープがフレームワークによって自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="38d65-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="38d65-199">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="38d65-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="38d65-200">次の例では、ユーザーの誕生日を取得するために Google `https://www.googleapis.com/auth/user.birthday.read` スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="38d65-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="38d65-201">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="38d65-201">Map user data keys and create claims</span></span>

<span data-ttu-id="38d65-202">プロバイダーのオプションで、サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータの各キー/サブキーに対して、<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> または <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> を指定します。</span><span class="sxs-lookup"><span data-stu-id="38d65-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="38d65-203">要求の種類の詳細については、「<xref:System.Security.Claims.ClaimTypes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="38d65-204">このサンプルアプリでは、Google ユーザーデータの `locale` および `picture` キーから、ロケール (`urn:google:locale`) と画像 (`urn:google:picture`) の要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="38d65-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="38d65-205">`Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`では、<xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) が <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>を使用してアプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="38d65-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="38d65-206">サインインプロセス中に、<xref:Microsoft.AspNetCore.Identity.UserManager%601> は <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>から利用可能なユーザーデータの `ApplicationUser` 要求を格納できます。</span><span class="sxs-lookup"><span data-stu-id="38d65-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="38d65-207">サンプルアプリでは、`OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml*) を使用して、署名された `ApplicationUser`のロケール (`urn:google:locale`) 要求と画像 (`urn:google:picture`) 要求を確立します。これには <xref:System.Security.Claims.ClaimTypes.GivenName>の要求も含まれます。</span><span class="sxs-lookup"><span data-stu-id="38d65-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="38d65-208">既定では、ユーザーの要求は認証 cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="38d65-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="38d65-209">認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="38d65-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="38d65-210">ブラウザーは cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="38d65-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="38d65-211">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="38d65-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="38d65-212">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="38d65-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="38d65-213">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="38d65-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="38d65-214">Cookie 認証ミドルウェアの <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> のカスタム <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> を使用して、要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="38d65-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="38d65-215">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="38d65-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="38d65-216">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="38d65-216">Save the access token</span></span>

<span data-ttu-id="38d65-217">認証が正常に完了した後に、アクセストークンと更新トークンを <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> に格納するかどうかを <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> が定義します。</span><span class="sxs-lookup"><span data-stu-id="38d65-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="38d65-218">`SaveTokens` は、最終的な認証クッキーのサイズを小さくするために、既定で `false` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="38d65-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="38d65-219">このサンプルアプリでは、`SaveTokens` の値を <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>の `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="38d65-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="38d65-220">`OnPostConfirmationAsync` 実行するときに、外部プロバイダーのアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) を `ApplicationUser`の `AuthenticationProperties`に格納します。</span><span class="sxs-lookup"><span data-stu-id="38d65-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="38d65-221">このサンプルアプリでは、アクセストークンを `OnPostConfirmationAsync` (新しいユーザー登録) と `OnGetCallbackAsync` (以前に登録したユーザー) の*アカウント/ExternalLogin. cshtml*に保存します。</span><span class="sxs-lookup"><span data-stu-id="38d65-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="38d65-222">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="38d65-222">How to add additional custom tokens</span></span>

<span data-ttu-id="38d65-223">`SaveTokens`の一部として格納されているカスタムトークンを追加する方法を示すために、サンプルアプリでは、`TicketCreated`の[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)の現在の <xref:System.DateTime> に <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> を追加します。</span><span class="sxs-lookup"><span data-stu-id="38d65-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="38d65-224">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="38d65-224">Creating and adding claims</span></span>

<span data-ttu-id="38d65-225">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="38d65-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="38d65-226">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="38d65-227">ユーザーは、<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> から派生させ、抽象 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> メソッドを実装することによって、カスタムアクションを定義できます。</span><span class="sxs-lookup"><span data-stu-id="38d65-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="38d65-228">詳細については、<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38d65-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="38d65-229">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="38d65-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="38d65-230">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> のすべての要求アクションをコレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="38d65-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="38d65-231">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> の要求を id から削除します。</span><span class="sxs-lookup"><span data-stu-id="38d65-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="38d65-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は、主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="38d65-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="38d65-233">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="38d65-233">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="38d65-234">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="38d65-234">Additional resources</span></span>

* <span data-ttu-id="38d65-235">[dotnet/AspNetCore engineering サンプルアプリ](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)は、リンクされたサンプルアプリ &ndash;、 [Dotnet/AspNetCore GitHub リポジトリの](https://github.com/dotnet/AspNetCore)`master` エンジニアリング分岐にあります。</span><span class="sxs-lookup"><span data-stu-id="38d65-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="38d65-236">`master` 分岐には、ASP.NET Core の次のリリースでアクティブな開発のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="38d65-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="38d65-237">リリースされたバージョンの ASP.NET Core 用のサンプルアプリのバージョンを表示するには、 **[ブランチ]** ドロップダウンリストを使用してリリースブランチを選択します (`release/{X.Y}`など)。</span><span class="sxs-lookup"><span data-stu-id="38d65-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
