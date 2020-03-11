---
title: ASP.NET Core での SameSite cookie の使用
author: rick-anderson
description: を使用して ASP.NET Core で cookie を SameSite する方法について説明します
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Electron
uid: security/samesite
ms.openlocfilehash: eeba2c4403d33312692ed187021a125c22df5d08
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654986"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="71b05-103">ASP.NET Core での SameSite cookie の使用</span><span class="sxs-lookup"><span data-stu-id="71b05-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="71b05-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71b05-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71b05-105">SameSite は、クロスサイトリクエスト偽造 (CSRF) 攻撃に対して何らかの保護を提供するように設計された[IETF](https://ietf.org/about/)ドラフト標準です。</span><span class="sxs-lookup"><span data-stu-id="71b05-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="71b05-106">最初は[2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)でドラフトされていましたが、draft standard は[2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)で更新されました。</span><span class="sxs-lookup"><span data-stu-id="71b05-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="71b05-107">更新された標準は以前の標準との下位互換性はありませんが、次のように最も顕著な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="71b05-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="71b05-108">SameSite ヘッダーのない cookie は、既定では `SameSite=Lax` として扱われます。</span><span class="sxs-lookup"><span data-stu-id="71b05-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="71b05-109">クロスサイトクッキーの使用を許可するには、`SameSite=None` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71b05-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="71b05-110">`SameSite=None` をアサートする cookie も `Secure`としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="71b05-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="71b05-111">[`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe)を使用するアプリケーションでは、`<iframe>` はクロスサイトのシナリオとして扱われるため、`sameSite=Lax` または `sameSite=Strict` cookie に関する問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="71b05-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="71b05-112">値 `SameSite=None` は[2016 標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07)では許可されていないため、一部の実装では、このような cookie を `SameSite=Strict`として扱います。</span><span class="sxs-lookup"><span data-stu-id="71b05-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="71b05-113">このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71b05-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="71b05-114">`SameSite=Lax` 設定は、ほとんどのアプリケーション cookie に対して機能します。</span><span class="sxs-lookup"><span data-stu-id="71b05-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="71b05-115">[OpenID connect](https://openid.net/connect/) (oidc) や[ws-federation](https://auth0.com/docs/protocols/ws-fed)のような認証形式では、既定で POST ベースのリダイレクトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="71b05-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="71b05-116">POST ベースのリダイレクトによって SameSite ブラウザーの保護がトリガーされるため、これらのコンポーネントの SameSite は無効になります。</span><span class="sxs-lookup"><span data-stu-id="71b05-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="71b05-117">ほとんどの[OAuth](https://oauth.net/)ログインは、要求フローの違いによって影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="71b05-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="71b05-118">Cookie を生成する各 ASP.NET Core コンポーネントは、SameSite が適切かどうかを判断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71b05-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="71b05-119">SameSite テストサンプルコード</span><span class="sxs-lookup"><span data-stu-id="71b05-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="71b05-120">次のサンプルをダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="71b05-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="71b05-121">サンプル</span><span class="sxs-lookup"><span data-stu-id="71b05-121">Sample</span></span>               | <span data-ttu-id="71b05-122">ドキュメント</span><span class="sxs-lookup"><span data-stu-id="71b05-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="71b05-123">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="71b05-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="71b05-124">.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="71b05-124">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71b05-125">次のサンプルをダウンロードしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="71b05-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="71b05-126">サンプル</span><span class="sxs-lookup"><span data-stu-id="71b05-126">Sample</span></span>               | <span data-ttu-id="71b05-127">ドキュメント</span><span class="sxs-lookup"><span data-stu-id="71b05-127">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="71b05-128">.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="71b05-128">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="71b05-129">SameSite 属性の .NET Core のサポート</span><span class="sxs-lookup"><span data-stu-id="71b05-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="71b05-130">.NET Core 2.2 では、年 12 2019 月に更新プログラムがリリースされたため、SameSite の2019ドラフト標準がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="71b05-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="71b05-131">開発者は、`HttpCookie.SameSite` プロパティを使用して、sameSite 属性の値をプログラムで制御できます。</span><span class="sxs-lookup"><span data-stu-id="71b05-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="71b05-132">`SameSite` プロパティを Strict、厳密でない、または None に設定すると、これらの値はネットワーク上でクッキーと共に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="71b05-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="71b05-133">これを (SameSiteMode) (-1) に設定すると、cookie を使用してネットワークに含まれる属性がないことを示します。</span><span class="sxs-lookup"><span data-stu-id="71b05-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="71b05-134">.NET Core 3.0 では、更新された SameSite 値をサポートし、`SameSiteMode` 列挙型に `SameSiteMode.Unspecified` 追加の列挙値を追加します。</span><span class="sxs-lookup"><span data-stu-id="71b05-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="71b05-135">この新しい値は、クッキーと共に sameSite を送信しないことを示します。</span><span class="sxs-lookup"><span data-stu-id="71b05-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="71b05-136">12月のパッチ動作の変更</span><span class="sxs-lookup"><span data-stu-id="71b05-136">December patch behavior changes</span></span>

<span data-ttu-id="71b05-137">.NET Framework および .NET Core 2.1 の特定の動作変更は、`SameSite` プロパティが `None` 値を解釈する方法です。</span><span class="sxs-lookup"><span data-stu-id="71b05-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="71b05-138">パッチの前に、"属性をまったく出力しない" という意味の `None` には、パッチの後に "値 `None`を持つ属性を生成する" という意味があります。</span><span class="sxs-lookup"><span data-stu-id="71b05-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="71b05-139">パッチの後に `(SameSiteMode)(-1)` の `SameSite` 値を指定すると、属性は出力されません。</span><span class="sxs-lookup"><span data-stu-id="71b05-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="71b05-140">フォーム認証およびセッション状態 cookie の既定の SameSite 値は、`None` から `Lax`に変更されました。</span><span class="sxs-lookup"><span data-stu-id="71b05-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="71b05-141">SameSite を使用した API の使用</span><span class="sxs-lookup"><span data-stu-id="71b05-141">API usage with SameSite</span></span>

<span data-ttu-id="71b05-142">SameSite は、既定では `Unspecified`[に設定されます。つまり](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)、cookie に追加された属性はありません。また、クライアントは既定の動作を使用します (新しいブラウザーではなく、古いブラウザーではありません)。</span><span class="sxs-lookup"><span data-stu-id="71b05-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="71b05-143">次のコードは、cookie の SameSite 値を `SameSiteMode.Lax`に変更する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="71b05-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="71b05-144">Cookie を出力するすべての ASP.NET Core コンポーネントは、前述の既定値よりも、そのシナリオに適した設定を上書きします。</span><span class="sxs-lookup"><span data-stu-id="71b05-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="71b05-145">オーバーライドされた前の既定値は変更されていません。</span><span class="sxs-lookup"><span data-stu-id="71b05-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="71b05-146">コンポーネント</span><span class="sxs-lookup"><span data-stu-id="71b05-146">Component</span></span> | <span data-ttu-id="71b05-147">Cookie</span><span class="sxs-lookup"><span data-stu-id="71b05-147">cookie</span></span> | <span data-ttu-id="71b05-148">既定値</span><span class="sxs-lookup"><span data-stu-id="71b05-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="71b05-149">SessionOptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="71b05-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="71b05-150">CookieTempDataProviderOptions</span><span class="sxs-lookup"><span data-stu-id="71b05-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="71b05-151">アンチ Forgeryoptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="71b05-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="71b05-152">Cookie 認証</span><span class="sxs-lookup"><span data-stu-id="71b05-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="71b05-153">CookieAuthenticationOptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="71b05-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="71b05-154">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="71b05-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="71b05-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Remoteauthenticationoptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="71b05-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="71b05-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="71b05-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="71b05-157">HttpContext. Cookies. Append</span><span class="sxs-lookup"><span data-stu-id="71b05-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="71b05-158">ASP.NET Core 3.1 以降では、次の SameSite サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="71b05-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="71b05-159">`SameSiteMode.None` が出力される動作を再定義し `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="71b05-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="71b05-160">新しい値 `SameSiteMode.Unspecified` を追加して、SameSite 属性を省略します。</span><span class="sxs-lookup"><span data-stu-id="71b05-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="71b05-161">すべての cookie Api の既定値は `Unspecified`です。</span><span class="sxs-lookup"><span data-stu-id="71b05-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="71b05-162">Cookie を使用する一部のコンポーネントでは、そのシナリオにより具体的な値が設定されています。</span><span class="sxs-lookup"><span data-stu-id="71b05-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="71b05-163">例については、上の表を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71b05-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71b05-164">ASP.NET Core 3.0 以降では、一貫性のないクライアントの既定値と競合しないように、SameSite の既定値が変更されました。</span><span class="sxs-lookup"><span data-stu-id="71b05-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="71b05-165">次の Api が既定値を `SameSiteMode.Lax ` から `-1` に変更し、これらの cookie の SameSite 属性が出力されないようにしました。</span><span class="sxs-lookup"><span data-stu-id="71b05-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="71b05-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> 使用され[ます。](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="71b05-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="71b05-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder> `CookieOptions` のファクトリとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="71b05-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="71b05-168">CookiePolicyOptions. MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="71b05-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="71b05-169">履歴と変更</span><span class="sxs-lookup"><span data-stu-id="71b05-169">History and changes</span></span>

<span data-ttu-id="71b05-170">SameSite サポートは、2.0 の ASP.NET Core で最初に[2016 ドラフト標準](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)を使用して実装されました。</span><span class="sxs-lookup"><span data-stu-id="71b05-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="71b05-171">2016標準はオプトインでした。</span><span class="sxs-lookup"><span data-stu-id="71b05-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="71b05-172">ASP.NET Core、いくつかの cookie を既定で `Lax` するように設定することによってオプトインできます。</span><span class="sxs-lookup"><span data-stu-id="71b05-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="71b05-173">認証でいくつかの[問題](https://github.com/aspnet/Announcements/issues/318)が発生すると、ほとんどの SameSite の使用が[無効に](https://github.com/aspnet/Announcements/issues/348)なりました。</span><span class="sxs-lookup"><span data-stu-id="71b05-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="71b05-174">2016標準から2019標準に更新するために、2019年11月に[修正プログラム](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)が発行されました。</span><span class="sxs-lookup"><span data-stu-id="71b05-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="71b05-175">[SameSite 仕様の2019ドラフト](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="71b05-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="71b05-176">は、2016ドラフトとの下位互換性が**ありません**。</span><span class="sxs-lookup"><span data-stu-id="71b05-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="71b05-177">詳細については、このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71b05-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="71b05-178">Cookie を既定で `SameSite=Lax` として扱うことを指定します。</span><span class="sxs-lookup"><span data-stu-id="71b05-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="71b05-179">クロスサイト配信を有効にするために `SameSite=None` を明示的にアサートする cookie を `Secure`としてマークする必要があることを指定します。</span><span class="sxs-lookup"><span data-stu-id="71b05-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="71b05-180">`None` は、オプトアウトする新しいエントリです。</span><span class="sxs-lookup"><span data-stu-id="71b05-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="71b05-181">は、ASP.NET Core 2.1、2.2、および3.0 に対して発行された修正プログラムによってサポートされています。</span><span class="sxs-lookup"><span data-stu-id="71b05-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="71b05-182">ASP.NET Core 3.1 には、追加の SameSite サポートがあります。</span><span class="sxs-lookup"><span data-stu-id="71b05-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="71b05-183">[2 月 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)日に既定で[Chrome](https://chromestatus.com/feature/5088147346030592)によって有効になるようにスケジュールされています。</span><span class="sxs-lookup"><span data-stu-id="71b05-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="71b05-184">ブラウザーは2019でこの標準への移行を開始しました。</span><span class="sxs-lookup"><span data-stu-id="71b05-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="71b05-185">2016 SameSite draft standard から2019ドラフト標準への変更によって影響を受ける Api</span><span class="sxs-lookup"><span data-stu-id="71b05-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="71b05-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="71b05-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="71b05-187">CookieOptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="71b05-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="71b05-188">CookieBuilder. SameSite</span><span class="sxs-lookup"><span data-stu-id="71b05-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="71b05-189">CookiePolicyOptions. MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="71b05-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="71b05-190">古いブラウザーのサポート</span><span class="sxs-lookup"><span data-stu-id="71b05-190">Supporting older browsers</span></span>

<span data-ttu-id="71b05-191">2016 SameSite 標準では、不明な値を `SameSite=Strict` 値として処理する必要があることが義務付けられています。</span><span class="sxs-lookup"><span data-stu-id="71b05-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="71b05-192">2016 SameSite 標準をサポートする古いブラウザーからアクセスされるアプリは、値が `None`の SameSite プロパティを取得すると壊れます。</span><span class="sxs-lookup"><span data-stu-id="71b05-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="71b05-193">Web apps が古いブラウザーをサポートする予定の場合は、ブラウザーの検出を実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71b05-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="71b05-194">ユーザーエージェントの値が変動し、頻繁に変更されるため、ASP.NET Core はブラウザーの検出を実装しません。</span><span class="sxs-lookup"><span data-stu-id="71b05-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="71b05-195"><xref:Microsoft.AspNetCore.CookiePolicy> の拡張ポイントを使用すると、ユーザーエージェント固有のロジックに接続できます。</span><span class="sxs-lookup"><span data-stu-id="71b05-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="71b05-196">`Startup.Configure`で、<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> または cookie を書き込む*任意の*メソッドを呼び出す前に <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> を呼び出すコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="71b05-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="71b05-197">`Startup.ConfigureServices`で、次のようなコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="71b05-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="71b05-198">前のサンプルの `MyUserAgentDetectionLib.DisallowsSameSiteNone` は、ユーザーエージェントが SameSite `None`をサポートしていないかどうかを検出するユーザー指定のライブラリです。</span><span class="sxs-lookup"><span data-stu-id="71b05-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="71b05-199">次のコードは、`DisallowsSameSiteNone` メソッドの例を示しています。</span><span class="sxs-lookup"><span data-stu-id="71b05-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="71b05-200">次のコードは、デモンストレーションのみを対象としています。</span><span class="sxs-lookup"><span data-stu-id="71b05-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="71b05-201">完全であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="71b05-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="71b05-202">管理されていないか、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="71b05-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="71b05-203">SameSite 問題のアプリをテストする</span><span class="sxs-lookup"><span data-stu-id="71b05-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="71b05-204">サードパーティのログインによってなどのリモートサイトと対話するアプリでは、次の操作を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="71b05-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="71b05-205">複数のブラウザーで相互作用をテストします。</span><span class="sxs-lookup"><span data-stu-id="71b05-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="71b05-206">このドキュメントで説明され[ている Cookiepolicy browser の検出と軽減策](#sob)を適用します。</span><span class="sxs-lookup"><span data-stu-id="71b05-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="71b05-207">新しい SameSite 動作にオプトインできるクライアントバージョンを使用して、web アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="71b05-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="71b05-208">Chrome、Firefox、Chromium Edge には、テストに使用できる新しいオプトイン機能フラグがあります。</span><span class="sxs-lookup"><span data-stu-id="71b05-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="71b05-209">アプリが SameSite パッチを適用したら、古いクライアントバージョン (特に Safari) を使用してテストします。</span><span class="sxs-lookup"><span data-stu-id="71b05-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="71b05-210">詳細については、このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71b05-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="71b05-211">Chrome を使用したテスト</span><span class="sxs-lookup"><span data-stu-id="71b05-211">Test with Chrome</span></span>

<span data-ttu-id="71b05-212">Chrome 78 + には一時的な軽減策があるため、誤解を招く結果になります。</span><span class="sxs-lookup"><span data-stu-id="71b05-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="71b05-213">Chrome 78 + 一時的な軽減策では、2分前よりも少ない cookie を使用できます。</span><span class="sxs-lookup"><span data-stu-id="71b05-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="71b05-214">適切なテストフラグが有効になっている Chrome 76 または77では、より正確な結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="71b05-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="71b05-215">新しい SameSite の動作をテストするには、`chrome://flags/#same-site-by-default-cookies` を**有効**に切り替えます。</span><span class="sxs-lookup"><span data-stu-id="71b05-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="71b05-216">新しい `None` 設定で失敗するように、Chrome の旧バージョン (75 以降) が報告されます。</span><span class="sxs-lookup"><span data-stu-id="71b05-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="71b05-217">このドキュメントの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71b05-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="71b05-218">Google では、以前のバージョンの chrome は使用できません。</span><span class="sxs-lookup"><span data-stu-id="71b05-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="71b05-219">「 [Chromium のダウンロード](https://www.chromium.org/getting-involved/download-chromium)」の手順に従って、Chrome の旧バージョンをテストします。</span><span class="sxs-lookup"><span data-stu-id="71b05-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="71b05-220">以前のバージョンの chrome を検索することによって提供されるリンクから Chrome**をダウンロードしないでください**。</span><span class="sxs-lookup"><span data-stu-id="71b05-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="71b05-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="71b05-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="71b05-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="71b05-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="71b05-223">カナリアバージョン `80.0.3975.0`以降では、新しいフラグ `--enable-features=SameSiteDefaultChecksMethodRigorously` を使用することにより、テスト用に厳密ではない + POST 一時軽減を無効にすることができます。これは、軽減策が削除された機能の最終的な終了状態でサイトとサービスをテストできるようにするためのものです。</span><span class="sxs-lookup"><span data-stu-id="71b05-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="71b05-224">詳細については、「Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71b05-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="71b05-225">Safari を使用したテスト</span><span class="sxs-lookup"><span data-stu-id="71b05-225">Test with Safari</span></span>

<span data-ttu-id="71b05-226">Safari 12 では以前のドラフトが厳密に実装されており、新しい `None` 値が cookie に含まれていると失敗します。</span><span class="sxs-lookup"><span data-stu-id="71b05-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="71b05-227">このドキュメントでは、[古いブラウザーをサポート](#sob)するブラウザーの検出コードを使用して `None` を回避します。</span><span class="sxs-lookup"><span data-stu-id="71b05-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="71b05-228">MSAL、ADAL、使用している任意のライブラリを使用して、Safari 12、Safari 13、WebKit ベースの OS スタイルのログインをテストします。</span><span class="sxs-lookup"><span data-stu-id="71b05-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="71b05-229">この問題は、基盤の OS バージョンによって変わります。</span><span class="sxs-lookup"><span data-stu-id="71b05-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="71b05-230">OSX Mojave (10.14) と iOS 12 には、新しい SameSite の動作に互換性の問題があることがわかっています。</span><span class="sxs-lookup"><span data-stu-id="71b05-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="71b05-231">OS を OSX Catalina.properties (10.15) または iOS 13 にアップグレードすると、問題が解決されます。</span><span class="sxs-lookup"><span data-stu-id="71b05-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="71b05-232">Safari には、現在、新しい仕様動作をテストするオプトインフラグがありません。</span><span class="sxs-lookup"><span data-stu-id="71b05-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="71b05-233">Firefox でのテスト</span><span class="sxs-lookup"><span data-stu-id="71b05-233">Test with Firefox</span></span>

<span data-ttu-id="71b05-234">新しい標準の Firefox サポートは、バージョン68以降で、[`about:config`] ページで機能フラグ `network.cookie.sameSite.laxByDefault`をオンにしてテストできます。</span><span class="sxs-lookup"><span data-stu-id="71b05-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="71b05-235">以前のバージョンの Firefox との互換性に関する問題は報告されていません。</span><span class="sxs-lookup"><span data-stu-id="71b05-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="71b05-236">Edge ブラウザーを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="71b05-236">Test with Edge browser</span></span>

<span data-ttu-id="71b05-237">Edge では、古い SameSite 標準がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="71b05-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="71b05-238">Edge バージョン44には、新しい標準との互換性に関する既知の問題はありません。</span><span class="sxs-lookup"><span data-stu-id="71b05-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="71b05-239">Edge でテストする (Chromium)</span><span class="sxs-lookup"><span data-stu-id="71b05-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="71b05-240">SameSite フラグは `edge://flags/#same-site-by-default-cookies` ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="71b05-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="71b05-241">Edge Chromium で互換性の問題は検出されませんでした。</span><span class="sxs-lookup"><span data-stu-id="71b05-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="71b05-242">電子を使用したテスト</span><span class="sxs-lookup"><span data-stu-id="71b05-242">Test with Electron</span></span>

<span data-ttu-id="71b05-243">Electron の複数のバージョンには、Chromium の古いバージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="71b05-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="71b05-244">たとえば、チームによって使用されている電子 66 Chromium のバージョンは、以前の動作を示しています。</span><span class="sxs-lookup"><span data-stu-id="71b05-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="71b05-245">製品で使用されている電子版を使用して、独自の互換性テストを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71b05-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="71b05-246">次のセクションの「[古いブラウザーのサポート](#sob)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="71b05-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71b05-247">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="71b05-247">Additional resources</span></span>

* [<span data-ttu-id="71b05-248">Chromium ブログ: 開発者: 新しい SameSite の準備 = None;セキュリティで保護された Cookie の設定</span><span class="sxs-lookup"><span data-stu-id="71b05-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="71b05-249">SameSite cookie の説明</span><span class="sxs-lookup"><span data-stu-id="71b05-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="71b05-250">2019年11月の修正プログラム</span><span class="sxs-lookup"><span data-stu-id="71b05-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="71b05-251">サンプル</span><span class="sxs-lookup"><span data-stu-id="71b05-251">Sample</span></span>               | <span data-ttu-id="71b05-252">ドキュメント</span><span class="sxs-lookup"><span data-stu-id="71b05-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="71b05-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="71b05-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="71b05-254">.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="71b05-254">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="71b05-255">サンプル</span><span class="sxs-lookup"><span data-stu-id="71b05-255">Sample</span></span>               | <span data-ttu-id="71b05-256">ドキュメント</span><span class="sxs-lookup"><span data-stu-id="71b05-256">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="71b05-257">.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="71b05-257">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
