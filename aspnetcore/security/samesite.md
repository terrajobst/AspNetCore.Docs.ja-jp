---
title: ASP.NET Core での SameSite cookie の使用
author: rick-anderson
description: を使用して ASP.NET Core で cookie を SameSite する方法について説明します
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite
ms.openlocfilehash: 988069a66cc4772583444303948bff2e47ff4310
ms.sourcegitcommit: 169ea5116de729c803685725d96450a270bc55b7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74733987"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="236ff-103">ASP.NET Core での SameSite cookie の使用</span><span class="sxs-lookup"><span data-stu-id="236ff-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="236ff-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="236ff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="236ff-105">[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07)は、クロスサイトリクエスト偽造 (csrf) 攻撃に対して何らかの保護を提供するように設計された[IETF](https://ietf.org/about/)ドラフトです。</span><span class="sxs-lookup"><span data-stu-id="236ff-105">[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) is an [IETF](https://ietf.org/about/) draft designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="236ff-106">[SameSite 2019 のドラフト](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):</span><span class="sxs-lookup"><span data-stu-id="236ff-106">The [SameSite 2019 draft](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):</span></span>

* <span data-ttu-id="236ff-107">クッキーを既定で `SameSite=Lax` として扱います。</span><span class="sxs-lookup"><span data-stu-id="236ff-107">Treats cookies as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="236ff-108">クロスサイト配信を有効にするために `SameSite=None` を明示的にアサートする cookie の状態を `Secure`としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="236ff-108">States cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span>

<span data-ttu-id="236ff-109">`Lax` は、ほとんどのアプリ cookie で機能します。</span><span class="sxs-lookup"><span data-stu-id="236ff-109">`Lax` works for most app cookies.</span></span> <span data-ttu-id="236ff-110">[OpenID connect](https://openid.net/connect/) (oidc) や[ws-federation](https://auth0.com/docs/protocols/ws-fed)のような認証形式では、既定で POST ベースのリダイレクトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="236ff-110">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="236ff-111">POST ベースのリダイレクトによって SameSite ブラウザーの保護がトリガーされるため、これらのコンポーネントの SameSite は無効になります。</span><span class="sxs-lookup"><span data-stu-id="236ff-111">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="236ff-112">ほとんどの[OAuth](https://oauth.net/)ログインは、要求フローの違いによって影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="236ff-112">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="236ff-113">`None` パラメーターを指定すると、以前の2016ドラフト標準 (iOS 12 など) を実装したクライアントとの互換性の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="236ff-113">The `None` parameter causes compatibility problems with clients that implemented the prior 2016 draft standard (for example, iOS 12).</span></span> <span data-ttu-id="236ff-114">このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="236ff-114">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="236ff-115">Cookie を生成する各 ASP.NET Core コンポーネントは、SameSite が適切かどうかを判断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="236ff-115">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="api-usage-with-samesite"></a><span data-ttu-id="236ff-116">SameSite を使用した API の使用</span><span class="sxs-lookup"><span data-stu-id="236ff-116">API usage with SameSite</span></span>

<span data-ttu-id="236ff-117">SameSite は、既定では `Unspecified`[に設定されます。つまり](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)、cookie に追加された属性はありません。また、クライアントは既定の動作を使用します (新しいブラウザーではなく、古いブラウザーではありません)。</span><span class="sxs-lookup"><span data-stu-id="236ff-117">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="236ff-118">次のコードは、cookie の SameSite 値を `SameSiteMode.Lax`に変更する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="236ff-118">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="236ff-119">Cookie を出力するすべての ASP.NET Core コンポーネントは、前述の既定値よりも、そのシナリオに適した設定を上書きします。</span><span class="sxs-lookup"><span data-stu-id="236ff-119">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="236ff-120">オーバーライドされた前の既定値は変更されていません。</span><span class="sxs-lookup"><span data-stu-id="236ff-120">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="236ff-121">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="236ff-121">Component</span></span> | <span data-ttu-id="236ff-122">cookie</span><span class="sxs-lookup"><span data-stu-id="236ff-122">cookie</span></span> | <span data-ttu-id="236ff-123">[既定値]</span><span class="sxs-lookup"><span data-stu-id="236ff-123">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="236ff-124">SessionOptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="236ff-124">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="236ff-125">CookieTempDataProviderOptions</span><span class="sxs-lookup"><span data-stu-id="236ff-125">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="236ff-126">アンチ Forgeryoptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="236ff-126">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="236ff-127">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="236ff-127">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="236ff-128">CookieAuthenticationOptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="236ff-128">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="236ff-129">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="236ff-129">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="236ff-130"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Remoteauthenticationoptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="236ff-130"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="236ff-131">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="236ff-131">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="236ff-132">HttpContext. Cookies. Append</span><span class="sxs-lookup"><span data-stu-id="236ff-132">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="236ff-133">ASP.NET Core 3.1 以降では、次の SameSite サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="236ff-133">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="236ff-134">`SameSiteMode.None` が出力される動作を再定義し `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="236ff-134">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="236ff-135">新しい値 `SameSiteMode.Unspecified` を追加して、SameSite 属性を省略します。</span><span class="sxs-lookup"><span data-stu-id="236ff-135">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="236ff-136">すべての cookie Api の既定値は `Unspecified`です。</span><span class="sxs-lookup"><span data-stu-id="236ff-136">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="236ff-137">Cookie を使用する一部のコンポーネントでは、そのシナリオにより具体的な値が設定されています。</span><span class="sxs-lookup"><span data-stu-id="236ff-137">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="236ff-138">例については、上の表を参照してください。</span><span class="sxs-lookup"><span data-stu-id="236ff-138">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="236ff-139">ASP.NET Core 3.0 以降では、一貫性のないクライアントの既定値と競合しないように、SameSite の既定値が変更されました。</span><span class="sxs-lookup"><span data-stu-id="236ff-139">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="236ff-140">次の Api が既定値を `SameSiteMode.Lax ` から `-1` に変更し、これらの cookie の SameSite 属性が出力されないようにしました。</span><span class="sxs-lookup"><span data-stu-id="236ff-140">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="236ff-141"><xref:Microsoft.AspNetCore.Http.CookieOptions> 使用され[ます。](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="236ff-141"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="236ff-142"><xref:Microsoft.AspNetCore.Http.CookieBuilder> `CookieOptions` のファクトリとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="236ff-142"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="236ff-143">CookiePolicyOptions. MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="236ff-143">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="236ff-144">履歴と変更</span><span class="sxs-lookup"><span data-stu-id="236ff-144">History and changes</span></span>

<span data-ttu-id="236ff-145">SameSite サポートは、2.0 の ASP.NET Core で最初に[2016 ドラフト標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)を使用して実装されました。</span><span class="sxs-lookup"><span data-stu-id="236ff-145">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="236ff-146">2016標準はオプトインでした。</span><span class="sxs-lookup"><span data-stu-id="236ff-146">The 2016 standard was opt-in.</span></span> <span data-ttu-id="236ff-147">ASP.NET Core、いくつかの cookie を既定で `Lax` するように設定することによってオプトインできます。</span><span class="sxs-lookup"><span data-stu-id="236ff-147">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="236ff-148">認証でいくつかの[問題](https://github.com/aspnet/Announcements/issues/318)が発生すると、ほとんどの SameSite の使用が[無効に](https://github.com/aspnet/Announcements/issues/348)なりました。</span><span class="sxs-lookup"><span data-stu-id="236ff-148">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="236ff-149">2016標準から2019標準に更新するために、2019年11月に修正プログラムが発行されました。</span><span class="sxs-lookup"><span data-stu-id="236ff-149">Patches were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="236ff-150">[SameSite 仕様の2019ドラフト](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="236ff-150">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="236ff-151">は、2016ドラフトとの下位互換性が**ありません**。</span><span class="sxs-lookup"><span data-stu-id="236ff-151">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="236ff-152">詳細については、このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="236ff-152">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="236ff-153">Cookie を既定で `SameSite=Lax` として扱うことを指定します。</span><span class="sxs-lookup"><span data-stu-id="236ff-153">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="236ff-154">クロスサイト配信を有効にするために `SameSite=None` を明示的にアサートする cookie を `Secure`としてマークする必要があることを指定します。</span><span class="sxs-lookup"><span data-stu-id="236ff-154">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="236ff-155">`None` は、オプトアウトする新しいエントリです。</span><span class="sxs-lookup"><span data-stu-id="236ff-155">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="236ff-156">は、ASP.NET Core 2.1、2.2、および3.0 に対して発行された修正プログラムによってサポートされています。</span><span class="sxs-lookup"><span data-stu-id="236ff-156">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="236ff-157">ASP.NET Core 3.1 には、追加の SameSite サポートがあります。</span><span class="sxs-lookup"><span data-stu-id="236ff-157">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="236ff-158">[2 月 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)日に既定で[Chrome](https://chromestatus.com/feature/5088147346030592)によって有効になるようにスケジュールされています。</span><span class="sxs-lookup"><span data-stu-id="236ff-158">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="236ff-159">ブラウザーは2019でこの標準への移行を開始しました。</span><span class="sxs-lookup"><span data-stu-id="236ff-159">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="236ff-160">2016 SameSite draft standard から2019ドラフト標準への変更によって影響を受ける Api</span><span class="sxs-lookup"><span data-stu-id="236ff-160">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="236ff-161">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="236ff-161">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="236ff-162">CookieOptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="236ff-162">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="236ff-163">CookieBuilder. SameSite</span><span class="sxs-lookup"><span data-stu-id="236ff-163">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="236ff-164">CookiePolicyOptions. MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="236ff-164">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="236ff-165">古いブラウザーのサポート</span><span class="sxs-lookup"><span data-stu-id="236ff-165">Supporting older browsers</span></span>

<span data-ttu-id="236ff-166">2016 SameSite 標準では、不明な値を `SameSite=Strict` 値として処理する必要があることが義務付けられています。</span><span class="sxs-lookup"><span data-stu-id="236ff-166">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="236ff-167">2016 SameSite 標準をサポートする古いブラウザーからアクセスされるアプリは、値が `None`の SameSite プロパティを取得すると壊れます。</span><span class="sxs-lookup"><span data-stu-id="236ff-167">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="236ff-168">Web apps が古いブラウザーをサポートする予定の場合は、ブラウザーの検出を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="236ff-168">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="236ff-169">ユーザーエージェントの値が変動し、頻繁に変更されるため、ASP.NET Core はブラウザーの検出を実装しません。</span><span class="sxs-lookup"><span data-stu-id="236ff-169">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="236ff-170"><xref:Microsoft.AspNetCore.CookiePolicy> の拡張ポイントを使用すると、ユーザーエージェント固有のロジックに接続できます。</span><span class="sxs-lookup"><span data-stu-id="236ff-170">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="236ff-171">`Startup.Configure`で、<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> または cookie を書き込む*任意の*メソッドを呼び出す前に <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> を呼び出すコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="236ff-171">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="236ff-172">`Startup.ConfigureServices`で、次のようなコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="236ff-172">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="236ff-173">前のサンプルの `MyUserAgentDetectionLib.DisallowsSameSiteNone` は、ユーザーエージェントが SameSite `None`をサポートしていないかどうかを検出するユーザー指定のライブラリです。</span><span class="sxs-lookup"><span data-stu-id="236ff-173">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="236ff-174">次のコードは、`DisallowsSameSiteNone` メソッドの例を示しています。</span><span class="sxs-lookup"><span data-stu-id="236ff-174">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="236ff-175">次のコードは、デモンストレーションのみを対象としています。</span><span class="sxs-lookup"><span data-stu-id="236ff-175">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="236ff-176">完全であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="236ff-176">It should not be considered complete.</span></span>
> * <span data-ttu-id="236ff-177">管理されていないか、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="236ff-177">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="236ff-178">SameSite 問題のアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="236ff-178">Test apps for SameSite problems</span></span>

<span data-ttu-id="236ff-179">サードパーティのログインによってなどのリモートサイトと対話するアプリでは、次の操作を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="236ff-179">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="236ff-180">複数のブラウザーで相互作用をテストします。</span><span class="sxs-lookup"><span data-stu-id="236ff-180">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="236ff-181">このドキュメントで説明され[ている Cookiepolicy browser の検出と軽減策](#sob)を適用します。</span><span class="sxs-lookup"><span data-stu-id="236ff-181">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="236ff-182">新しい SameSite 動作にオプトインできるクライアントバージョンを使用して、web アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="236ff-182">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="236ff-183">Chrome、Firefox、Chromium Edge には、テストに使用できる新しいオプトイン機能フラグがあります。</span><span class="sxs-lookup"><span data-stu-id="236ff-183">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="236ff-184">アプリが SameSite パッチを適用したら、古いクライアントバージョン (特に Safari) を使用してテストします。</span><span class="sxs-lookup"><span data-stu-id="236ff-184">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="236ff-185">詳細については、このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="236ff-185">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="236ff-186">Chrome を使用したテスト</span><span class="sxs-lookup"><span data-stu-id="236ff-186">Test with Chrome</span></span>

<span data-ttu-id="236ff-187">Chrome 78 + には一時的な軽減策があるため、誤解を招く結果になります。</span><span class="sxs-lookup"><span data-stu-id="236ff-187">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="236ff-188">Chrome 78 + 一時的な軽減策では、2分前よりも少ない cookie を使用できます。</span><span class="sxs-lookup"><span data-stu-id="236ff-188">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="236ff-189">適切なテストフラグが有効になっている Chrome 76 または77では、より正確な結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="236ff-189">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="236ff-190">新しい SameSite の動作をテストするには、`chrome://flags/#same-site-by-default-cookies` を**有効**に切り替えます。</span><span class="sxs-lookup"><span data-stu-id="236ff-190">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="236ff-191">新しい `None` 設定で失敗するように、Chrome の旧バージョン (75 以降) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="236ff-191">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="236ff-192">このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="236ff-192">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="236ff-193">Google では、以前のバージョンの chrome は使用できません。</span><span class="sxs-lookup"><span data-stu-id="236ff-193">Google does not make older chrome versions available.</span></span> <span data-ttu-id="236ff-194">「 [Chromium のダウンロード](https://www.chromium.org/getting-involved/download-chromium)」の手順に従って、Chrome の旧バージョンをテストします。</span><span class="sxs-lookup"><span data-stu-id="236ff-194">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="236ff-195">以前のバージョンの chrome を検索することによって提供されるリンクから Chrome**をダウンロードしないでください**。</span><span class="sxs-lookup"><span data-stu-id="236ff-195">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="236ff-196">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="236ff-196">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="236ff-197">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="236ff-197">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

### <a name="test-with-safari"></a><span data-ttu-id="236ff-198">Safari を使用したテスト</span><span class="sxs-lookup"><span data-stu-id="236ff-198">Test with Safari</span></span>

<span data-ttu-id="236ff-199">Safari 12 では以前のドラフトが厳密に実装されており、新しい `None` 値が cookie に含まれていると失敗します。</span><span class="sxs-lookup"><span data-stu-id="236ff-199">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="236ff-200">このドキュメントでは、[古いブラウザーをサポート](#sob)するブラウザーの検出コードを使用して `None` を回避します。</span><span class="sxs-lookup"><span data-stu-id="236ff-200">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="236ff-201">MSAL、ADAL、使用している任意のライブラリを使用して、Safari 12、Safari 13、WebKit ベースの OS スタイルのログインをテストします。</span><span class="sxs-lookup"><span data-stu-id="236ff-201">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="236ff-202">この問題は、基になる OS バージョンによって異なります。</span><span class="sxs-lookup"><span data-stu-id="236ff-202">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="236ff-203">OSX Mojave (10.14) と iOS 12 には、新しい SameSite の動作に互換性の問題があることがわかっています。</span><span class="sxs-lookup"><span data-stu-id="236ff-203">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="236ff-204">OS を OSX Catalina.properties (10.15) または iOS 13 にアップグレードすると、問題が解決されます。</span><span class="sxs-lookup"><span data-stu-id="236ff-204">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="236ff-205">Safari には、現在、新しい仕様動作をテストするオプトインフラグがありません。</span><span class="sxs-lookup"><span data-stu-id="236ff-205">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="236ff-206">Firefox でのテスト</span><span class="sxs-lookup"><span data-stu-id="236ff-206">Test with Firefox</span></span>

<span data-ttu-id="236ff-207">新しい標準の Firefox サポートは、バージョン68以降で、[`about:config`] ページで機能フラグ `network.cookie.sameSite.laxByDefault`をオンにしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="236ff-207">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="236ff-208">以前のバージョンの Firefox との互換性に関する問題は報告されていません。</span><span class="sxs-lookup"><span data-stu-id="236ff-208">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="236ff-209">Edge ブラウザーを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="236ff-209">Test with Edge browser</span></span>

<span data-ttu-id="236ff-210">Edge では、古い SameSite 標準がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="236ff-210">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="236ff-211">Edge バージョン44には、新しい標準との互換性に関する既知の問題はありません。</span><span class="sxs-lookup"><span data-stu-id="236ff-211">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="236ff-212">Edge でテストする (Chromium)</span><span class="sxs-lookup"><span data-stu-id="236ff-212">Test with Edge (Chromium)</span></span>

<span data-ttu-id="236ff-213">SameSite フラグは `edge://flags/#same-site-by-default-cookies` ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="236ff-213">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="236ff-214">Edge Chromium で互換性の問題は検出されませんでした。</span><span class="sxs-lookup"><span data-stu-id="236ff-214">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="236ff-215">電子を使用したテスト</span><span class="sxs-lookup"><span data-stu-id="236ff-215">Test with Electron</span></span>

<span data-ttu-id="236ff-216">電子バージョンには、古いバージョンの Chromium が含まれています。</span><span class="sxs-lookup"><span data-stu-id="236ff-216">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="236ff-217">たとえば、チームによって使用されている電子 66 Chromium のバージョンは、以前の動作を示しています。</span><span class="sxs-lookup"><span data-stu-id="236ff-217">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="236ff-218">製品で使用されている電子版を使用して、独自の互換性テストを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="236ff-218">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="236ff-219">次のセクションの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="236ff-219">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="236ff-220">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="236ff-220">Additional resources</span></span>

* [<span data-ttu-id="236ff-221">Chromium ブログ: 開発者: 新しい SameSite の準備 = None;セキュリティで保護された Cookie の設定</span><span class="sxs-lookup"><span data-stu-id="236ff-221">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="236ff-222">SameSite cookie の説明</span><span class="sxs-lookup"><span data-stu-id="236ff-222">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
