---
title: ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する
author: guardrex
description: 外部プロバイダーから追加の要求とトークンを確立する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 72710d249d3210208dd9b0356a700ba02a0b727a
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378885"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="9fca8-103">ASP.NET Core で外部プロバイダーからの追加の要求とトークンを保持する</span><span class="sxs-lookup"><span data-stu-id="9fca8-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

<span data-ttu-id="9fca8-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9fca8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9fca8-105">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-105">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="9fca8-106">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="9fca8-106">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="9fca8-107">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9fca8-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9fca8-108">必要条件</span><span class="sxs-lookup"><span data-stu-id="9fca8-108">Prerequisites</span></span>

<span data-ttu-id="9fca8-109">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-109">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="9fca8-110">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-110">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="9fca8-111">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-111">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="9fca8-112">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-112">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="9fca8-113">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="9fca8-113">Set the client ID and client secret</span></span>

<span data-ttu-id="9fca8-114">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-114">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="9fca8-115">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-115">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="9fca8-116">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fca8-116">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="9fca8-117">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-117">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="9fca8-118">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="9fca8-118">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="9fca8-119">Google での認証</span><span class="sxs-lookup"><span data-stu-id="9fca8-119">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="9fca8-120">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="9fca8-120">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="9fca8-121">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="9fca8-121">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="9fca8-122">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9fca8-122">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="9fca8-123">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="9fca8-123">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="9fca8-124">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-124">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="9fca8-125">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="9fca8-125">Establish the authentication scope</span></span>

<span data-ttu-id="9fca8-126">@No__t-0 を指定して、プロバイダーから取得するアクセス許可の一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-126">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="9fca8-127">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-127">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="9fca8-128">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9fca8-128">Provider</span></span>  | <span data-ttu-id="9fca8-129">[スコープ]</span><span class="sxs-lookup"><span data-stu-id="9fca8-129">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="9fca8-130">Facebook</span><span class="sxs-lookup"><span data-stu-id="9fca8-130">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="9fca8-131">Google</span><span class="sxs-lookup"><span data-stu-id="9fca8-131">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="9fca8-132">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9fca8-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="9fca8-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="9fca8-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="9fca8-134">サンプルアプリでは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> で <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> が呼び出されると、Google の `userinfo.profile` スコープがフレームワークによって自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-134">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="9fca8-135">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="9fca8-136">次の例では、ユーザーの誕生日を取得するために、Google `https://www.googleapis.com/auth/user.birthday.read` スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="9fca8-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="9fca8-137">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="9fca8-137">Map user data keys and create claims</span></span>

<span data-ttu-id="9fca8-138">プロバイダーのオプションで、サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータ内のキー/サブキーごとに、<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> または <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> を指定します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="9fca8-139">要求の種類の詳細については、「<xref:System.Security.Claims.ClaimTypes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="9fca8-140">サンプルアプリでは、Google ユーザーデータの `locale` および @no__t キーから、locale (`urn:google:locale`) 要求と画像 (@no__t) 要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="9fca8-141">@No__t-0 の場合、<xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) は、<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> でアプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="9fca8-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="9fca8-142">サインインプロセスの間、<xref:Microsoft.AspNetCore.Identity.UserManager%601> は @no__t から利用可能なユーザーデータの @no__t 1 要求を格納できます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="9fca8-143">サンプルアプリでは、`OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml*) によって、<xref:System.Security.Claims.ClaimTypes.GivenName> の要求を含む、署名された `ApplicationUser` のロケール (@no__t 2) と画像 (@no__t 3) の要求が確立されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="9fca8-144">既定では、ユーザーの要求は認証 cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="9fca8-145">認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9fca8-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="9fca8-146">ブラウザーは cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="9fca8-147">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="9fca8-148">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9fca8-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="9fca8-149">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="9fca8-150">Cookie 認証ミドルウェアの <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> のカスタム <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> を使用して、要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="9fca8-151">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="9fca8-152">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="9fca8-152">Save the access token</span></span>

<span data-ttu-id="9fca8-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> は、承認が正常に完了した後、アクセストークンと更新トークンを <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> に格納するかどうかを定義します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="9fca8-154">`SaveTokens` は、最終的な認証クッキーのサイズを小さくするために、既定で `false` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9fca8-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="9fca8-155">サンプルアプリでは、`SaveTokens` の値が <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> の `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="9fca8-156">@No__t-0 を実行する場合は、外部プロバイダーのアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) を @no__t の `AuthenticationProperties` に格納します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="9fca8-157">このサンプルアプリでは、アクセストークンを `OnPostConfirmationAsync` (新しいユーザー登録) に保存し、*アカウント/ExternalLogin. cshtml. cs*で `OnGetCallbackAsync` (以前に登録されたユーザー) に保存します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="9fca8-158">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="9fca8-158">How to add additional custom tokens</span></span>

<span data-ttu-id="9fca8-159">@No__t-0 の一部として格納されているカスタムトークンを追加する方法を示すために、サンプルアプリでは、`TicketCreated` の[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)のために、現在の <xref:System.DateTime> を含む <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> を追加します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-159">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="9fca8-160">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="9fca8-160">Creating and adding claims</span></span>

<span data-ttu-id="9fca8-161">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9fca8-161">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="9fca8-162">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-162">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="9fca8-163">ユーザーは、<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> から派生させ、抽象 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> メソッドを実装することによって、カスタムアクションを定義できます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-163">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="9fca8-164">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-164">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="9fca8-165">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="9fca8-165">Removal of claim actions and claims</span></span>

<span data-ttu-id="9fca8-166">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> のすべての要求アクションをコレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-166">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="9fca8-167">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、id から指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> の要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-167">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="9fca8-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-168"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="9fca8-169">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="9fca8-169">Sample app output</span></span>

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

<span data-ttu-id="9fca8-170">ASP.NET Core アプリは、Facebook、Google、Microsoft、Twitter などの外部認証プロバイダーからの追加の要求とトークンを確立できます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-170">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="9fca8-171">各プロバイダーは、プラットフォーム上のユーザーに関するさまざまな情報を明らかにしますが、ユーザーデータを受け取って追加の要求に変換するパターンは同じです。</span><span class="sxs-lookup"><span data-stu-id="9fca8-171">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="9fca8-172">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9fca8-172">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9fca8-173">必要条件</span><span class="sxs-lookup"><span data-stu-id="9fca8-173">Prerequisites</span></span>

<span data-ttu-id="9fca8-174">アプリでサポートする外部認証プロバイダーを決定します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-174">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="9fca8-175">各プロバイダーについて、アプリを登録し、クライアント ID とクライアントシークレットを取得します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-175">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="9fca8-176">詳細については、「<xref:security/authentication/social/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-176">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="9fca8-177">このサンプルアプリでは、 [Google 認証プロバイダー](xref:security/authentication/google-logins)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-177">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="9fca8-178">クライアント ID とクライアントシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="9fca8-178">Set the client ID and client secret</span></span>

<span data-ttu-id="9fca8-179">OAuth 認証プロバイダーは、クライアント ID とクライアントシークレットを使用して、アプリとの信頼関係を確立します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-179">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="9fca8-180">アプリがプロバイダーに登録されるときに、外部認証プロバイダーによってアプリのクライアント ID とクライアントシークレットの値が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-180">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="9fca8-181">アプリが使用する各外部プロバイダーは、プロバイダーのクライアント ID とクライアントシークレットとは別に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fca8-181">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="9fca8-182">詳細については、シナリオに当てはまる外部認証プロバイダーのトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-182">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="9fca8-183">Facebook での認証</span><span class="sxs-lookup"><span data-stu-id="9fca8-183">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="9fca8-184">Google での認証</span><span class="sxs-lookup"><span data-stu-id="9fca8-184">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="9fca8-185">Microsoft での認証</span><span class="sxs-lookup"><span data-stu-id="9fca8-185">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="9fca8-186">Twitter での認証</span><span class="sxs-lookup"><span data-stu-id="9fca8-186">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="9fca8-187">他の認証プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9fca8-187">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="9fca8-188">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="9fca8-188">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="9fca8-189">このサンプルアプリでは、google によって提供されるクライアント ID とクライアントシークレットを使用して Google 認証プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-189">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="9fca8-190">認証スコープを確立する</span><span class="sxs-lookup"><span data-stu-id="9fca8-190">Establish the authentication scope</span></span>

<span data-ttu-id="9fca8-191">@No__t-0 を指定して、プロバイダーから取得するアクセス許可の一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-191">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="9fca8-192">共通外部プロバイダーの認証スコープは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-192">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="9fca8-193">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9fca8-193">Provider</span></span>  | <span data-ttu-id="9fca8-194">[スコープ]</span><span class="sxs-lookup"><span data-stu-id="9fca8-194">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="9fca8-195">Facebook</span><span class="sxs-lookup"><span data-stu-id="9fca8-195">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="9fca8-196">Google</span><span class="sxs-lookup"><span data-stu-id="9fca8-196">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="9fca8-197">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9fca8-197">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="9fca8-198">Twitter</span><span class="sxs-lookup"><span data-stu-id="9fca8-198">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="9fca8-199">サンプルアプリでは、<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> で <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> が呼び出されると、Google の `userinfo.profile` スコープがフレームワークによって自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-199">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="9fca8-200">アプリに追加のスコープが必要な場合は、それらをオプションに追加します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-200">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="9fca8-201">次の例では、ユーザーの誕生日を取得するために、Google `https://www.googleapis.com/auth/user.birthday.read` スコープが追加されています。</span><span class="sxs-lookup"><span data-stu-id="9fca8-201">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="9fca8-202">ユーザーデータキーをマップして要求を作成する</span><span class="sxs-lookup"><span data-stu-id="9fca8-202">Map user data keys and create claims</span></span>

<span data-ttu-id="9fca8-203">プロバイダーのオプションで、サインイン時に読み取るアプリ id の外部プロバイダーの JSON ユーザーデータ内のキー/サブキーごとに、<xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> または <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> を指定します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-203">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="9fca8-204">要求の種類の詳細については、「<xref:System.Security.Claims.ClaimTypes>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-204">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="9fca8-205">サンプルアプリでは、Google ユーザーデータの `locale` および @no__t キーから、locale (`urn:google:locale`) 要求と画像 (@no__t) 要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-205">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="9fca8-206">@No__t-0 の場合、<xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) は、<xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> でアプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="9fca8-206">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="9fca8-207">サインインプロセスの間、<xref:Microsoft.AspNetCore.Identity.UserManager%601> は @no__t から利用可能なユーザーデータの @no__t 1 要求を格納できます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-207">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="9fca8-208">サンプルアプリでは、`OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml*) によって、<xref:System.Security.Claims.ClaimTypes.GivenName> の要求を含む、署名された `ApplicationUser` のロケール (@no__t 2) と画像 (@no__t 3) の要求が確立されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-208">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="9fca8-209">既定では、ユーザーの要求は認証 cookie に格納されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-209">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="9fca8-210">認証 cookie が大きすぎると、次の理由により、アプリのエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9fca8-210">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="9fca8-211">ブラウザーは cookie ヘッダーが長すぎることを検出します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-211">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="9fca8-212">要求の全体的なサイズが大きすぎます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-212">The overall size of the request is too large.</span></span>

<span data-ttu-id="9fca8-213">ユーザーの要求を処理するために大量のユーザーデータが必要な場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9fca8-213">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="9fca8-214">要求処理のユーザー要求の数とサイズは、アプリで必要なもののみに制限します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-214">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="9fca8-215">Cookie 認証ミドルウェアの <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> のカスタム <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> を使用して、要求間で id を格納します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-215">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="9fca8-216">小さいセッション識別子キーをクライアントに送信するだけで、サーバー上の大量の id 情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-216">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="9fca8-217">アクセストークンを保存する</span><span class="sxs-lookup"><span data-stu-id="9fca8-217">Save the access token</span></span>

<span data-ttu-id="9fca8-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> は、承認が正常に完了した後、アクセストークンと更新トークンを <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> に格納するかどうかを定義します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-218"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="9fca8-219">`SaveTokens` は、最終的な認証クッキーのサイズを小さくするために、既定で `false` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="9fca8-219">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="9fca8-220">サンプルアプリでは、`SaveTokens` の値が <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> の `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-220">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="9fca8-221">@No__t-0 を実行する場合は、外部プロバイダーのアクセストークン ([Externallogininfo. authenticationtokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) を @no__t の `AuthenticationProperties` に格納します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-221">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="9fca8-222">このサンプルアプリでは、アクセストークンを `OnPostConfirmationAsync` (新しいユーザー登録) に保存し、*アカウント/ExternalLogin. cshtml. cs*で `OnGetCallbackAsync` (以前に登録されたユーザー) に保存します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-222">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="9fca8-223">カスタムトークンを追加する方法</span><span class="sxs-lookup"><span data-stu-id="9fca8-223">How to add additional custom tokens</span></span>

<span data-ttu-id="9fca8-224">@No__t-0 の一部として格納されているカスタムトークンを追加する方法を示すために、サンプルアプリでは、`TicketCreated` の[AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*)のために、現在の <xref:System.DateTime> を含む <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> を追加します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-224">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="9fca8-225">要求の作成と追加</span><span class="sxs-lookup"><span data-stu-id="9fca8-225">Creating and adding claims</span></span>

<span data-ttu-id="9fca8-226">フレームワークには、要求を作成してコレクションに追加するための一般的なアクションと拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9fca8-226">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="9fca8-227">詳細については、「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> 」および「 <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-227">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="9fca8-228">ユーザーは、<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> から派生させ、抽象 <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> メソッドを実装することによって、カスタムアクションを定義できます。</span><span class="sxs-lookup"><span data-stu-id="9fca8-228">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="9fca8-229">詳細については、「<xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fca8-229">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="9fca8-230">要求アクションと要求の削除</span><span class="sxs-lookup"><span data-stu-id="9fca8-230">Removal of claim actions and claims</span></span>

<span data-ttu-id="9fca8-231">[Claimactioncollection。 Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*)は、指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> のすべての要求アクションをコレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-231">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="9fca8-232">[DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*)は、id から指定された <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> の要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-232">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="9fca8-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> は主に[OpenID connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc)と共に使用して、プロトコルによって生成された要求を削除します。</span><span class="sxs-lookup"><span data-stu-id="9fca8-233"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="9fca8-234">サンプルアプリの出力</span><span class="sxs-lookup"><span data-stu-id="9fca8-234">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="9fca8-235">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9fca8-235">Additional resources</span></span>

* <span data-ttu-id="9fca8-236">[aspnet/AspNetCore engineering のサンプルアプリ](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample)&ndash; リンクされたサンプルアプリは、 [Aspnet/AspNetCore GitHub リポジトリの](https://github.com/aspnet/AspNetCore)`master` エンジニアリングブランチにあります。</span><span class="sxs-lookup"><span data-stu-id="9fca8-236">[aspnet/AspNetCore engineering SocialSample app](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [aspnet/AspNetCore GitHub repo's](https://github.com/aspnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="9fca8-237">@No__t-0 分岐には、ASP.NET Core の次のリリースのアクティブな開発のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9fca8-237">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="9fca8-238">リリースされたバージョンの ASP.NET Core のサンプルアプリのバージョンを表示するには、 **[ブランチ]** ドロップダウンリストを使用してリリースブランチを選択します (例: `release/{X.Y}`)。</span><span class="sxs-lookup"><span data-stu-id="9fca8-238">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
