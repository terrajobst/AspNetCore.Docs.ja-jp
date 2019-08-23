---
title: ASP.NET Core の URL リライト ミドルウェア
author: guardrex
description: ASP.NET Core アプリケーションの URL リライト ミドルウェアを使用した URL の書き換えとリダイレクトについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
uid: fundamentals/url-rewriting
ms.openlocfilehash: e284d2172af723bb80a7be9f6e6f1a87ebe5208e
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886504"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="b1b1e-103">ASP.NET Core の URL リライト ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="b1b1e-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="b1b1e-104">作成者: [Luke Latham](https://github.com/guardrex) および [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="b1b1e-104">By [Luke Latham](https://github.com/guardrex) and [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1b1e-105">このドキュメントでは、ASP.NET Core アプリの URL リライト ミドルウェアを使用して URL を書き換える手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="b1b1e-106">URL の書き換えは、1 つまたは複数の事前定義された規則に基づいて URL 要求を変更する操作です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="b1b1e-107">URL 書き換えは、リソースの場所とそれらのアドレスの間の抽象化を作成し、場所とアドレスが緊密にリンクされていないようにします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="b1b1e-108">URL 書き換えは複数のシナリオで使用できます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="b1b1e-109">サーバー リソースを一時的または永続的に移動するか置き換えて、リソースに対する安定したロケーターを維持します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="b1b1e-110">要求処理を異なる複数のアプリまたは 1 つのアプリの複数の区分に分割します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="b1b1e-111">受信した要求の URL セグメントを削除、追加、または再編成します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="b1b1e-112">検索エンジン最適化 (SEO) のためにパブリック URL を最適化します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="b1b1e-113">ビジターがリソースの要求から返される内容を予測しやすいように、フレンドリなパブリック URL の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="b1b1e-114">セキュリティで保護されていない要求をセキュリティで保護されたエンドポイントにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="b1b1e-115">外部サイトが別のサイトでホストされている静的資産を独自のコンテンツにリンクすることによってその資産を使用するホットリンクを防止します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="b1b1e-116">URL の書き換えによってアプリのパフォーマンスが低下することがあります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="b1b1e-117">可能であれば、ルールの複雑さと数を制限します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="b1b1e-118">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="b1b1e-119">URL リダイレクトと URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="b1b1e-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="b1b1e-120">"*URL リダイレクト*" と "*URL 書き換え*" では表現上はあまり違いがないように見えますが、クライアントへのリソースの提供に関しては重要な意味を持っています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="b1b1e-121">ASP.NET Core の URL リライト ミドルウェアは、両方のニーズを満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="b1b1e-122">"*URL リダイレクト*" にはクライアント側の操作が関係しており、クライアントはもともと要求したものとは異なるアドレスにあるリソースにアクセスするよう指示されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="b1b1e-123">これによりサーバーへのラウンドトリップが必要になります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-123">This requires a round trip to the server.</span></span> <span data-ttu-id="b1b1e-124">クライアントが、リソースの新しい要求を実行するときに、クライアントに返されたリダイレクト URL がブラウザーのアドレス バーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="b1b1e-125">`/resource` が `/different-resource` に "*リダイレクトされる*" 場合、サーバーからの応答では、クライアントが `/different-resource` にあるリソースを取得する必要があることと、リダイレクトが一時的または永続的のどちらかを示す状態コードが示されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI サービス エンドポイントは、サーバー上でバージョン 1 (v1) からバージョン 2 (v2) に一時的に変更されました。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="b1b1e-131">要求を別の URL にリダイレクトする場合は、応答で状態コードを指定することにより、リダイレクトが永続的か一時的かのどちらかを示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="b1b1e-132">"*301 - 完全な移動*" 状態コードは、リソースに新しい永続的な URL があり、リソースに対する将来のすべての要求で新しい URL を使用する必要があることをクライアントに指示したい場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="b1b1e-133">"*301 状態コードを受信すると、クライアントは応答をキャッシュに入れて再利用することができます。* "</span><span class="sxs-lookup"><span data-stu-id="b1b1e-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="b1b1e-134">"*302 - 検出*" 状態コードは、リダイレクトが一時的である場合、または一般に変更される可能性がある場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="b1b1e-135">302 状態コードは、URL を保存して後で再利用しないようクライアントに指示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="b1b1e-136">状態コードの詳細については、[RFC 2616: 状態コードの定義](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="b1b1e-137">"*URL 書き換え*" はサーバー側の操作であり、クライアントが要求したものとは異なるリソース アドレスからリソースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="b1b1e-138">URL 書き換えでは、サーバーへのラウンドトリップは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="b1b1e-139">書き換えられた URL は、クライアントに返されず、ブラウザーのアドレス バーに表示されません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="b1b1e-140">`/resource` が `/different-resource` に "*書き換えられた*" 場合、サーバーは `/different-resource` にあるリソースを "*内部的に*" 取得して返します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="b1b1e-141">クライアントは、書き換えられた URL にあるリソースを取得できる場合がありますが、クライアントは、その要求を実行して応答を受信したときに、書き換えられた URL にリソースが存在することは通知されません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI サービス エンドポイントは、サーバー上でバージョン 1 (v1) からバージョン 2 (v2) に変更されました。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="b1b1e-146">URL リライト サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="b1b1e-146">URL rewriting sample app</span></span>

<span data-ttu-id="b1b1e-147">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)を使用して、URL リライト ミドルウェアの機能を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="b1b1e-148">そのアプリは、リダイレクトと書き換えのルールを適用し、複数のシナリオについて、リダイレクトされた URL または書き換えられた URL を表示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="b1b1e-149">URL リライト ミドルウェアを使用する状況</span><span class="sxs-lookup"><span data-stu-id="b1b1e-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="b1b1e-150">次の方法を使用できない場合は、URL リライト ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="b1b1e-151">Windows Server 上の IIS での URL リライト モジュール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="b1b1e-152">Apache サーバー上の Apache mod_rewrite モジュール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="b1b1e-153">Nginx での URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="b1b1e-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="b1b1e-154">また、アプリが [HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) でホストされているときも、ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="b1b1e-155">IIS、Apache、Nginx でサーバー ベースの URL 書き換えテクノロジが使用される主な理由は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="b1b1e-156">ミドルウェアでは、これらのモジュールの完全な機能がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="b1b1e-157">IIS 書き換えモジュールの `IsFile` 制約や `IsDirectory` 制約など、サーバー モジュールの一部の機能は、ASP.NET Core プロジェクトでは動作しません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="b1b1e-158">これらのシナリオでは、代わりにミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="b1b1e-159">通常、ミドルウェアのパフォーマンスはモジュールより劣ります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="b1b1e-160">どちらのアプローチの方がパフォーマンスの低下が大きいか、またはパフォーマンスが低下した場合でもごくわずかかどうかを確実に知るには、ベンチマークが唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="b1b1e-161">Package</span><span class="sxs-lookup"><span data-stu-id="b1b1e-161">Package</span></span>

<span data-ttu-id="b1b1e-162">URL リライト ミドルウェアは、[Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) パッケージによって提供されます。このパッケージは ASP.NET Core アプリに暗黙的に含まれています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="b1b1e-163">拡張機能とオプション</span><span class="sxs-lookup"><span data-stu-id="b1b1e-163">Extension and options</span></span>

<span data-ttu-id="b1b1e-164">各書き換えルールに対する拡張メソッドを含む [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) クラスのインスタンスを作成することにより、URL 書き換えとリダイレクトのルールを設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="b1b1e-165">処理したい順序で複数のルールを連結します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="b1b1e-166"><xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> によって URL リライト ミドルウェアが要求パイプラインに追加されると、`RewriteOptions` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="b1b1e-167">非 WWW を WWW にリダイレクトする</span><span class="sxs-lookup"><span data-stu-id="b1b1e-167">Redirect non-www to www</span></span>

<span data-ttu-id="b1b1e-168">アプリで非 `www` の要求を `www` にリダイレクトできる 3 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="b1b1e-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; 要求が非 `www` の場合、要求を `www` サブドメインに永続的にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="b1b1e-170">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状態コードでリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="b1b1e-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; 受信した要求が非 `www` の場合、要求を `www` サブドメインにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="b1b1e-172">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状態コードでリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="b1b1e-173">オーバーロードにより、応答に対する状態コードを提供することができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="b1b1e-174">状態コードの割り当てには、<xref:Microsoft.AspNetCore.Http.StatusCodes> クラスのフィールドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="b1b1e-175">URL リダイレクト</span><span class="sxs-lookup"><span data-stu-id="b1b1e-175">URL redirect</span></span>

<span data-ttu-id="b1b1e-176">要求をリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="b1b1e-177">最初のパラメーターには、着信 URL のパスに一致する正規表現が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="b1b1e-178">2 番目のパラメーターは、置換文字列です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="b1b1e-179">3 番目のパラメーター (存在する場合) は、状態コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="b1b1e-180">状態コードを指定しない場合、状態コードは既定で "*302 - 検出*" に設定されます。これは、リソースが一時的に移動されるか置き換えられることを示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="b1b1e-181">開発者ツールが有効になっているブラウザーで、パス `/redirect-rule/1234/5678` を指定してサンプル アプリに対する要求を実行します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="b1b1e-182">正規表現が `redirect-rule/(.*)` の要求のパスに一致し、パスが `/redirected/1234/5678` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="b1b1e-183">リダイレクト URL が、"*302 - 検出*" 状態コードでクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="b1b1e-184">ブラウザーは、ブラウザーのアドレス バーに表示されるリダイレクト URL に新しい要求を実行します</span><span class="sxs-lookup"><span data-stu-id="b1b1e-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="b1b1e-185">リダイレクト URL にはサンプル アプリに一致するルールがないため、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="b1b1e-186">2 番目の要求は、アプリから *200 - OK* 応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="b1b1e-187">応答の本文では、リダイレクト URL が示されています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="b1b1e-188">URL が "*リダイレクト*" されるときに、サーバーへのラウンドトリップが実行されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="b1b1e-189">リダイレクト ルールを設定するときは注意してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="b1b1e-190">リダイレクト ルールは、リダイレクト後を含めて、アプリへのすべての要求で評価されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="b1b1e-191">"*無限リダイレクトのループ*" が誤って作成されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="b1b1e-192">元の要求: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="b1b1e-194">かっこ内に含まれる式の一部は、*キャプチャ グループ*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="b1b1e-195">式のドット (`.`) は、*どの文字とも一致する*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="b1b1e-196">アスタリスク (`*`) は、*直前の文字に 0 回以上一致する*ことを示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="b1b1e-197">そのため、URL の最後の 2 つのパス セグメント `1234/5678` は、キャプチャ グループ `(.*)` によってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="b1b1e-198">要求 URL で `redirect-rule/` の後に指定した任意の値が、この単一のキャプチャ グループによってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="b1b1e-199">置換文字列では、キャプチャされたグループがドル記号 (`$`) を使用して文字列に挿入され、その後にキャプチャのシーケンス番号が続きます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="b1b1e-200">最初のキャプチャ グループ値は、`$1` で取得され、2 番目は `$2` で取得され、これらは正規表現内のキャプチャ グループの順序で続行されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="b1b1e-201">サンプル アプリでは、リダイレクト ルールの正規表現内に 1 つのキャプチャ グループだけがあるので、置換文字列に 1 つの挿入されたグループ (`$1`) だけがあります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="b1b1e-202">ルールが適用されるときに、URL `/redirected/1234/5678` になります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="b1b1e-203">セキュリティで保護されたエンドポイントへの URL リダイレクト</span><span class="sxs-lookup"><span data-stu-id="b1b1e-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="b1b1e-204">HTTP 要求を、同じホストとパスに HTTPS プロトコルを使用してリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="b1b1e-205">状態コードが指定されていない場合、ミドルウェアは既定で "*302 - 検出*" に設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="b1b1e-206">ポートが指定されていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="b1b1e-207">ミドルウェアの既定値 `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="b1b1e-208">スキームは `https` (HTTPS プロトコル) に変更されて、クライアントはポート 443 でリソースにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="b1b1e-209">次の例では、状態コードを "*301 - 完全な移動*" に設定し、ポートを 5001 に変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="b1b1e-210">セキュリティで保護されていない要求を、セキュリティで保護された HTTPS プロトコル (ポート 443) を使用して同じホストとパスにリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="b1b1e-211">ミドルウェアは状態コードを "*301 -完全な移動"* に設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="b1b1e-212">リダイレクト規則を追加せずにセキュリティで保護されたエンドポイントにリダイレクトするときは、HTTPS リダイレクト ミドルウェアを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="b1b1e-213">詳細については、「[HTTPS の適用](xref:security/enforcing-ssl#require-https)」のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="b1b1e-214">サンプル アプリは、`AddRedirectToHttps` または `AddRedirectToHttpsPermanent` の使用方法の例を示すことができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="b1b1e-215">拡張メソッドを `RewriteOptions` に追加します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="b1b1e-216">任意の URL でセキュリティ保護されていない要求をアプリに対して行います。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="b1b1e-217">自己署名証明書が信頼できないことを示すブラウザーのセキュリティ警告を消去するか、証明書を信頼する例外を作成します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="b1b1e-218">`AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure` を使用する元の要求</span><span class="sxs-lookup"><span data-stu-id="b1b1e-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="b1b1e-220">`AddRedirectToHttpsPermanent`: `http://localhost:5000/secure` を使用する元の要求</span><span class="sxs-lookup"><span data-stu-id="b1b1e-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="b1b1e-222">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="b1b1e-222">URL rewrite</span></span>

<span data-ttu-id="b1b1e-223">URL 書き換えのルールを作成するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="b1b1e-224">最初のパラメーターには、着信 URL のパスに一致する正規表現が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="b1b1e-225">2 番目のパラメーターは、置換文字列です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="b1b1e-226">3 番目のパラメーター `skipRemainingRules: {true|false}` は、現在のルールが適用される場合に追加の書き換えルールをスキップするかどうかをミドルウェアに示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="b1b1e-227">元の要求: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="b1b1e-229">式の先頭にあるキャレット (`^`) は、URL パスの先頭からマッチングが開始されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="b1b1e-230">前のリダイレクト ルール `redirect-rule/(.*)` の例では、正規表現の先頭にキャレット (`^`) がありません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="b1b1e-231">したがって、パスの `redirect-rule/` の前にどのような文字があっても一致します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="b1b1e-232">パス</span><span class="sxs-lookup"><span data-stu-id="b1b1e-232">Path</span></span>                               | <span data-ttu-id="b1b1e-233">一致したもの</span><span class="sxs-lookup"><span data-stu-id="b1b1e-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="b1b1e-234">はい</span><span class="sxs-lookup"><span data-stu-id="b1b1e-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="b1b1e-235">はい</span><span class="sxs-lookup"><span data-stu-id="b1b1e-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="b1b1e-236">はい</span><span class="sxs-lookup"><span data-stu-id="b1b1e-236">Yes</span></span>   |

<span data-ttu-id="b1b1e-237">書き換えルール `^rewrite-rule/(\d+)/(\d+)` は、`rewrite-rule/` で始まる場合のみパスと一致します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="b1b1e-238">次の表では、一致の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="b1b1e-239">パス</span><span class="sxs-lookup"><span data-stu-id="b1b1e-239">Path</span></span>                              | <span data-ttu-id="b1b1e-240">一致したもの</span><span class="sxs-lookup"><span data-stu-id="b1b1e-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="b1b1e-241">はい</span><span class="sxs-lookup"><span data-stu-id="b1b1e-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="b1b1e-242">×</span><span class="sxs-lookup"><span data-stu-id="b1b1e-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="b1b1e-243">いいえ</span><span class="sxs-lookup"><span data-stu-id="b1b1e-243">No</span></span>    |

<span data-ttu-id="b1b1e-244">式の `^rewrite-rule/` に続く部分に、2 つのキャプチャ グループ `(\d+)/(\d+)` があります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="b1b1e-245">`\d` は、*1 桁 (数字) に一致する*ことを示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="b1b1e-246">プラス記号 (`+`) は、*直前の 1 つ以上の文字に一致する*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="b1b1e-247">そのため、URL は、数字とその後に続くスラッシュ、さらにその後に続く別の数字を含んでいる必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="b1b1e-248">これらのキャプチャ グループが `$1` および `$2` として書き換えられた URL に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="b1b1e-249">書き換えルールの置換文字列は、クエリ文字列にキャプチャされたグループを配置します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="b1b1e-250">`/rewrite-rule/1234/5678` の要求されたパスは、`/rewritten?var1=1234&var2=5678` でリソースを取得するように書き換えられます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="b1b1e-251">クエリ文字列が元の要求に存在する場合、URL が書き換えられるときに保持されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="b1b1e-252">リソースを取得するためのサーバーへのラウンドトリップはありません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="b1b1e-253">リソースが存在する場合は、取得され、*200 - OK* 状態コードと共にクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="b1b1e-254">クライアントはリダイレクトされていないため、ブラウザーのアドレス バーの URL は変更されません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="b1b1e-255">クライアントは、URL の書き換え操作がサーバーで発生したことを検出できません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b1b1e-256">式内の照合ルールは計算量が多く、アプリの応答時間が長くなるので、可能な限り `skipRemainingRules: true` を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="b1b1e-257">最も高速なアプリの応答を実現するには以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="b1b1e-258">一致する頻度が最も多いルールから一致頻度が最も少ないルールへの順番で書き換えルールを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="b1b1e-259">一致が発生し、追加の処理が必要ないときに残りのルールの処理をスキップします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="b1b1e-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="b1b1e-260">Apache mod_rewrite</span></span>

<span data-ttu-id="b1b1e-261"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> を使用して Apache mod_rewrite ルールを適用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="b1b1e-262">ルール ファイルがアプリと共に展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="b1b1e-263">mod_rewrite ルールの情報と例については、「[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="b1b1e-264">*ApacheModRewrite.txt* ルール ファイルからルールを読み取るには、<xref:System.IO.StreamReader> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="b1b1e-265">サンプル アプリは、要求を `/apache-mod-rules-redirect/(.\*)` から `/redirected?id=$1` にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="b1b1e-266">応答の状態コードは "*302 -検出*" です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="b1b1e-267">元の要求: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="b1b1e-269">ミドルウェアは、次の Apache mod_rewrite サーバー変数をサポートします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="b1b1e-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="b1b1e-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="b1b1e-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="b1b1e-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="b1b1e-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="b1b1e-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="b1b1e-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="b1b1e-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="b1b1e-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="b1b1e-275">HTTP_HOST</span></span>
* <span data-ttu-id="b1b1e-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="b1b1e-276">HTTP_REFERER</span></span>
* <span data-ttu-id="b1b1e-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="b1b1e-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b1b1e-278">HTTPS</span></span>
* <span data-ttu-id="b1b1e-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="b1b1e-279">IPV6</span></span>
* <span data-ttu-id="b1b1e-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="b1b1e-280">QUERY_STRING</span></span>
* <span data-ttu-id="b1b1e-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="b1b1e-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-282">REMOTE_PORT</span></span>
* <span data-ttu-id="b1b1e-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="b1b1e-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="b1b1e-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="b1b1e-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="b1b1e-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="b1b1e-286">REQUEST_URI</span></span>
* <span data-ttu-id="b1b1e-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="b1b1e-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-288">SERVER_ADDR</span></span>
* <span data-ttu-id="b1b1e-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-289">SERVER_PORT</span></span>
* <span data-ttu-id="b1b1e-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="b1b1e-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="b1b1e-291">TIME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-291">TIME</span></span>
* <span data-ttu-id="b1b1e-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="b1b1e-292">TIME_DAY</span></span>
* <span data-ttu-id="b1b1e-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-293">TIME_HOUR</span></span>
* <span data-ttu-id="b1b1e-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="b1b1e-294">TIME_MIN</span></span>
* <span data-ttu-id="b1b1e-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="b1b1e-295">TIME_MON</span></span>
* <span data-ttu-id="b1b1e-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="b1b1e-296">TIME_SEC</span></span>
* <span data-ttu-id="b1b1e-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="b1b1e-297">TIME_WDAY</span></span>
* <span data-ttu-id="b1b1e-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="b1b1e-299">IIS URL リライト モジュールのルール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="b1b1e-300">IIS URL リライト モジュールに適用される同じルール セットを使用するには、<xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="b1b1e-301">ルール ファイルがアプリと共に展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="b1b1e-302">Windows Server IIS で実行されているときにミドルウェアでアプリの *web.config* ファイルを使用するように指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="b1b1e-303">IIS を使用する場合、IIS リライト モジュールとの競合を避けるため、これらのルールをアプリの *web.config* の外部に保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="b1b1e-304">IIS URL リライト モジュールのルールの詳細と例については、「[Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)」(URL リライト モジュール 2.0 の使用 ) と「[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)」(URL リライト モジュール構成リファレンス) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="b1b1e-305">*IISUrlRewrite.xml* ルール ファイルからルールを読み取るには、<xref:System.IO.StreamReader> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="b1b1e-306">サンプル アプリは、要求を `/iis-rules-rewrite/(.*)` から `/rewritten?id=$1` に書き換えます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="b1b1e-307">応答は、*200 - OK* 状態コードでクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="b1b1e-308">元の要求: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="b1b1e-310">望ましくない方法でアプリに影響するように構成されたサーバー レベル ルールを使用するアクティブな IIS リライト モジュールがある場合は、アプリに対して IIS リライト モジュールを無効にできます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="b1b1e-311">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="b1b1e-312">サポートされていない機能</span><span class="sxs-lookup"><span data-stu-id="b1b1e-312">Unsupported features</span></span>

<span data-ttu-id="b1b1e-313">ASP.NET Core 2.x でリリースされたミドルウェアは、次の IIS URL リライト モジュールの機能をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="b1b1e-314">送信ルール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-314">Outbound Rules</span></span>
* <span data-ttu-id="b1b1e-315">カスタム サーバー変数</span><span class="sxs-lookup"><span data-stu-id="b1b1e-315">Custom Server Variables</span></span>
* <span data-ttu-id="b1b1e-316">ワイルドカード</span><span class="sxs-lookup"><span data-stu-id="b1b1e-316">Wildcards</span></span>
* <span data-ttu-id="b1b1e-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="b1b1e-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="b1b1e-318">サポートされるサーバー変数</span><span class="sxs-lookup"><span data-stu-id="b1b1e-318">Supported server variables</span></span>

<span data-ttu-id="b1b1e-319">ミドルウェアは、次の IIS URL リライト モジュール サーバー変数をサポートします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="b1b1e-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="b1b1e-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="b1b1e-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="b1b1e-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="b1b1e-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="b1b1e-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="b1b1e-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="b1b1e-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="b1b1e-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="b1b1e-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="b1b1e-325">HTTP_HOST</span></span>
* <span data-ttu-id="b1b1e-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="b1b1e-326">HTTP_REFERER</span></span>
* <span data-ttu-id="b1b1e-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="b1b1e-327">HTTP_URL</span></span>
* <span data-ttu-id="b1b1e-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="b1b1e-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b1b1e-329">HTTPS</span></span>
* <span data-ttu-id="b1b1e-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="b1b1e-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="b1b1e-331">QUERY_STRING</span></span>
* <span data-ttu-id="b1b1e-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="b1b1e-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-333">REMOTE_PORT</span></span>
* <span data-ttu-id="b1b1e-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="b1b1e-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="b1b1e-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="b1b1e-336"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> を介して <xref:Microsoft.Extensions.FileProviders.IFileProvider> を取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="b1b1e-337">このアプローチは、書き換えルール ファイルの場所に関する大きな柔軟性を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="b1b1e-338">書き換えルール ファイルを指定したパスでサーバーに導入されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="b1b1e-339">メソッド ベースのルール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-339">Method-based rule</span></span>

<span data-ttu-id="b1b1e-340">メソッド内で独自のルール ロジックを実装するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="b1b1e-341">`Add` は <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> を公開し、メソッドで <xref:Microsoft.AspNetCore.Http.HttpContext> を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="b1b1e-342">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) は、追加のパイプライン処理の実行方法を決定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="b1b1e-343">次の表で説明する <xref:Microsoft.AspNetCore.Rewrite.RuleResult> フィールドのいずれかに値を設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="b1b1e-344">アクション</span><span class="sxs-lookup"><span data-stu-id="b1b1e-344">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="b1b1e-345">`RuleResult.ContinueRules` (既定値)</span><span class="sxs-lookup"><span data-stu-id="b1b1e-345">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="b1b1e-346">ルールの適用を続けます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-346">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="b1b1e-347">ルールの適用を停止し、応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-347">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="b1b1e-348">ルールの適用を停止し、次のミドルウェアにコンテキストを送信します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-348">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="b1b1e-349">サンプル アプリは、 *.xml* で終了するパスの要求をリダイレクトするメソッドの例を示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-349">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="b1b1e-350">`/file.xml` に対して要求が行われた場合、要求は `/xmlfiles/file.xml` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-350">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="b1b1e-351">状態コードは "*301 - 完全な移動*" に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-351">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="b1b1e-352">ブラウザーが */xmlfiles/file.xml* に対する新しい要求を行うと、静的ファイル ミドルウェアはクライアントに *wwwroot/xmlfiles* フォルダーからファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-352">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="b1b1e-353">リダイレクトの場合は、応答の状態コードを明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-353">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="b1b1e-354">それ以外の場合は、*200 - OK* 状態コードが返され、クライアントでのリダイレクトは発生しません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-354">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="b1b1e-355">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="b1b1e-355">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="b1b1e-356">このアプローチでは、要求を書き換えることもできます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-356">This approach can also rewrite requests.</span></span> <span data-ttu-id="b1b1e-357">サンプル アプリでは、*wwwroot* フォルダーから *file.txt* テキスト ファイルを提供するためにテキスト ファイル要求のパスを書き換える処理が示されています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-357">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="b1b1e-358">静的ファイル ミドルウェアでは、更新された要求パスに基づいてファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-358">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="b1b1e-359">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="b1b1e-359">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="b1b1e-360">IRule ベースのルール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-360">IRule-based rule</span></span>

<span data-ttu-id="b1b1e-361"><xref:Microsoft.AspNetCore.Rewrite.IRule> インターフェイスを実装するクラス内のルール ロジックを使用するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-361">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="b1b1e-362">`IRule` では、メソッド ベースのルール アプローチを使用する場合より高い柔軟性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-362">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="b1b1e-363">実装クラスには、<xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> メソッドに対してパラメーターを渡すことができるコンストラクターを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-363">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="b1b1e-364">サンプル アプリの `extension` と `newPath` のパラメーターの値は、いくつかの条件を満たすかチェックされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-364">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="b1b1e-365">`extension` は、値を含んでいる必要があり、値は *.png*、 *.jpg*、または *.gif* でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-365">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="b1b1e-366">`newPath` が有効ではない場合、<xref:System.ArgumentException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-366">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="b1b1e-367">*image.png* に対して行われた要求は、`/png-images/image.png` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-367">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="b1b1e-368">*image.jpg* に対して行われた要求は、`/jpg-images/image.jpg` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-368">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="b1b1e-369">状態コードは "*301 - 完全な移動*" に設定され、`context.Result` はルールの処理を停止して応答を送信するように設定されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-369">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="b1b1e-370">元の要求: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-370">Original Request: `/image.png`</span></span>

![image.png の要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="b1b1e-372">元の要求: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-372">Original Request: `/image.jpg`</span></span>

![image.jpg の要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="b1b1e-374">正規表現の例</span><span class="sxs-lookup"><span data-stu-id="b1b1e-374">Regex examples</span></span>

| <span data-ttu-id="b1b1e-375">Goal</span><span class="sxs-lookup"><span data-stu-id="b1b1e-375">Goal</span></span> | <span data-ttu-id="b1b1e-376">正規表現文字列と</span><span class="sxs-lookup"><span data-stu-id="b1b1e-376">Regex String &</span></span><br><span data-ttu-id="b1b1e-377">一致の例</span><span class="sxs-lookup"><span data-stu-id="b1b1e-377">Match Example</span></span> | <span data-ttu-id="b1b1e-378">置換文字列と</span><span class="sxs-lookup"><span data-stu-id="b1b1e-378">Replacement String &</span></span><br><span data-ttu-id="b1b1e-379">出力の例</span><span class="sxs-lookup"><span data-stu-id="b1b1e-379">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="b1b1e-380">クエリ文字列内のパスを書き換える</span><span class="sxs-lookup"><span data-stu-id="b1b1e-380">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="b1b1e-381">末尾のスラッシュを除去する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-381">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="b1b1e-382">末尾のスラッシュを強制する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-382">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="b1b1e-383">特定の要求を書き換えを回避する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-383">Avoid rewriting specific requests</span></span> | <span data-ttu-id="b1b1e-384">`^(.*)(?<!\.axd)$` または `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-384">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="b1b1e-385">はい: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-385">Yes: `/resource.htm`</span></span><br><span data-ttu-id="b1b1e-386">いいえ: `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-386">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="b1b1e-387">URL セグメントを再配置する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-387">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="b1b1e-388">URL セグメントを置き換える</span><span class="sxs-lookup"><span data-stu-id="b1b1e-388">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1b1e-389">このドキュメントでは、ASP.NET Core アプリの URL リライト ミドルウェアを使用して URL を書き換える手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-389">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="b1b1e-390">URL の書き換えは、1 つまたは複数の事前定義された規則に基づいて URL 要求を変更する操作です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-390">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="b1b1e-391">URL 書き換えは、リソースの場所とそれらのアドレスの間の抽象化を作成し、場所とアドレスが緊密にリンクされていないようにします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-391">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="b1b1e-392">URL 書き換えは複数のシナリオで使用できます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-392">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="b1b1e-393">サーバー リソースを一時的または永続的に移動するか置き換えて、リソースに対する安定したロケーターを維持します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-393">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="b1b1e-394">要求処理を異なる複数のアプリまたは 1 つのアプリの複数の区分に分割します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-394">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="b1b1e-395">受信した要求の URL セグメントを削除、追加、または再編成します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-395">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="b1b1e-396">検索エンジン最適化 (SEO) のためにパブリック URL を最適化します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-396">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="b1b1e-397">ビジターがリソースの要求から返される内容を予測しやすいように、フレンドリなパブリック URL の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-397">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="b1b1e-398">セキュリティで保護されていない要求をセキュリティで保護されたエンドポイントにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-398">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="b1b1e-399">外部サイトが別のサイトでホストされている静的資産を独自のコンテンツにリンクすることによってその資産を使用するホットリンクを防止します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-399">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="b1b1e-400">URL の書き換えによってアプリのパフォーマンスが低下することがあります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-400">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="b1b1e-401">可能であれば、ルールの複雑さと数を制限します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-401">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="b1b1e-402">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-402">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="b1b1e-403">URL リダイレクトと URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="b1b1e-403">URL redirect and URL rewrite</span></span>

<span data-ttu-id="b1b1e-404">"*URL リダイレクト*" と "*URL 書き換え*" では表現上はあまり違いがないように見えますが、クライアントへのリソースの提供に関しては重要な意味を持っています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-404">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="b1b1e-405">ASP.NET Core の URL リライト ミドルウェアは、両方のニーズを満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-405">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="b1b1e-406">"*URL リダイレクト*" にはクライアント側の操作が関係しており、クライアントはもともと要求したものとは異なるアドレスにあるリソースにアクセスするよう指示されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-406">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="b1b1e-407">これによりサーバーへのラウンドトリップが必要になります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-407">This requires a round trip to the server.</span></span> <span data-ttu-id="b1b1e-408">クライアントが、リソースの新しい要求を実行するときに、クライアントに返されたリダイレクト URL がブラウザーのアドレス バーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-408">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="b1b1e-409">`/resource` が `/different-resource` に "*リダイレクトされる*" 場合、サーバーからの応答では、クライアントが `/different-resource` にあるリソースを取得する必要があることと、リダイレクトが一時的または永続的のどちらかを示す状態コードが示されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-409">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI サービス エンドポイントは、サーバー上でバージョン 1 (v1) からバージョン 2 (v2) に一時的に変更されました。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="b1b1e-415">要求を別の URL にリダイレクトする場合は、応答で状態コードを指定することにより、リダイレクトが永続的か一時的かのどちらかを示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-415">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="b1b1e-416">"*301 - 完全な移動*" 状態コードは、リソースに新しい永続的な URL があり、リソースに対する将来のすべての要求で新しい URL を使用する必要があることをクライアントに指示したい場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-416">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="b1b1e-417">"*301 状態コードを受信すると、クライアントは応答をキャッシュに入れて再利用することができます。* "</span><span class="sxs-lookup"><span data-stu-id="b1b1e-417">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="b1b1e-418">"*302 - 検出*" 状態コードは、リダイレクトが一時的である場合、または一般に変更される可能性がある場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-418">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="b1b1e-419">302 状態コードは、URL を保存して後で再利用しないようクライアントに指示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-419">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="b1b1e-420">状態コードの詳細については、[RFC 2616: 状態コードの定義](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-420">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="b1b1e-421">"*URL 書き換え*" はサーバー側の操作であり、クライアントが要求したものとは異なるリソース アドレスからリソースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-421">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="b1b1e-422">URL 書き換えでは、サーバーへのラウンドトリップは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-422">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="b1b1e-423">書き換えられた URL は、クライアントに返されず、ブラウザーのアドレス バーに表示されません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-423">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="b1b1e-424">`/resource` が `/different-resource` に "*書き換えられた*" 場合、サーバーは `/different-resource` にあるリソースを "*内部的に*" 取得して返します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-424">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="b1b1e-425">クライアントは、書き換えられた URL にあるリソースを取得できる場合がありますが、クライアントは、その要求を実行して応答を受信したときに、書き換えられた URL にリソースが存在することは通知されません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-425">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI サービス エンドポイントは、サーバー上でバージョン 1 (v1) からバージョン 2 (v2) に変更されました。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="b1b1e-430">URL リライト サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="b1b1e-430">URL rewriting sample app</span></span>

<span data-ttu-id="b1b1e-431">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)を使用して、URL リライト ミドルウェアの機能を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-431">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="b1b1e-432">そのアプリは、リダイレクトと書き換えのルールを適用し、複数のシナリオについて、リダイレクトされた URL または書き換えられた URL を表示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-432">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="b1b1e-433">URL リライト ミドルウェアを使用する状況</span><span class="sxs-lookup"><span data-stu-id="b1b1e-433">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="b1b1e-434">次の方法を使用できない場合は、URL リライト ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-434">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="b1b1e-435">Windows Server 上の IIS での URL リライト モジュール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-435">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="b1b1e-436">Apache サーバー上の Apache mod_rewrite モジュール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-436">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="b1b1e-437">Nginx での URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="b1b1e-437">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="b1b1e-438">また、アプリが [HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) でホストされているときも、ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-438">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="b1b1e-439">IIS、Apache、Nginx でサーバー ベースの URL 書き換えテクノロジが使用される主な理由は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-439">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="b1b1e-440">ミドルウェアでは、これらのモジュールの完全な機能がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-440">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="b1b1e-441">IIS 書き換えモジュールの `IsFile` 制約や `IsDirectory` 制約など、サーバー モジュールの一部の機能は、ASP.NET Core プロジェクトでは動作しません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-441">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="b1b1e-442">これらのシナリオでは、代わりにミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-442">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="b1b1e-443">通常、ミドルウェアのパフォーマンスはモジュールより劣ります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-443">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="b1b1e-444">どちらのアプローチの方がパフォーマンスの低下が大きいか、またはパフォーマンスが低下した場合でもごくわずかかどうかを確実に知るには、ベンチマークが唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-444">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="b1b1e-445">Package</span><span class="sxs-lookup"><span data-stu-id="b1b1e-445">Package</span></span>

<span data-ttu-id="b1b1e-446">ミドルウェアをプロジェクトに組み込むには、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)へのパッケージ参照をプロジェクト ファイルに追加します。これには、[Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) パッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-446">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="b1b1e-447">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、`Microsoft.AspNetCore.Rewrite` パッケージへのプロジェクト参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-447">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="b1b1e-448">拡張機能とオプション</span><span class="sxs-lookup"><span data-stu-id="b1b1e-448">Extension and options</span></span>

<span data-ttu-id="b1b1e-449">各書き換えルールに対する拡張メソッドを含む [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) クラスのインスタンスを作成することにより、URL 書き換えとリダイレクトのルールを設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-449">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="b1b1e-450">処理したい順序で複数のルールを連結します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-450">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="b1b1e-451"><xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> によって URL リライト ミドルウェアが要求パイプラインに追加されると、`RewriteOptions` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-451">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="b1b1e-452">非 WWW を WWW にリダイレクトする</span><span class="sxs-lookup"><span data-stu-id="b1b1e-452">Redirect non-www to www</span></span>

<span data-ttu-id="b1b1e-453">アプリで非 `www` の要求を `www` にリダイレクトできる 3 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-453">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="b1b1e-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; 要求が非 `www` の場合、要求を `www` サブドメインに永続的にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="b1b1e-455">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状態コードでリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-455">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="b1b1e-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; 受信した要求が非 `www` の場合、要求を `www` サブドメインにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="b1b1e-457">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状態コードでリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-457">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="b1b1e-458">オーバーロードにより、応答に対する状態コードを提供することができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-458">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="b1b1e-459">状態コードの割り当てには、<xref:Microsoft.AspNetCore.Http.StatusCodes> クラスのフィールドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-459">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="b1b1e-460">URL リダイレクト</span><span class="sxs-lookup"><span data-stu-id="b1b1e-460">URL redirect</span></span>

<span data-ttu-id="b1b1e-461">要求をリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-461">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="b1b1e-462">最初のパラメーターには、着信 URL のパスに一致する正規表現が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-462">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="b1b1e-463">2 番目のパラメーターは、置換文字列です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-463">The second parameter is the replacement string.</span></span> <span data-ttu-id="b1b1e-464">3 番目のパラメーター (存在する場合) は、状態コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-464">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="b1b1e-465">状態コードを指定しない場合、状態コードは既定で "*302 - 検出*" に設定されます。これは、リソースが一時的に移動されるか置き換えられることを示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-465">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="b1b1e-466">開発者ツールが有効になっているブラウザーで、パス `/redirect-rule/1234/5678` を指定してサンプル アプリに対する要求を実行します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-466">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="b1b1e-467">正規表現が `redirect-rule/(.*)` の要求のパスに一致し、パスが `/redirected/1234/5678` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-467">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="b1b1e-468">リダイレクト URL が、"*302 - 検出*" 状態コードでクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-468">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="b1b1e-469">ブラウザーは、ブラウザーのアドレス バーに表示されるリダイレクト URL に新しい要求を実行します</span><span class="sxs-lookup"><span data-stu-id="b1b1e-469">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="b1b1e-470">リダイレクト URL にはサンプル アプリに一致するルールがないため、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-470">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="b1b1e-471">2 番目の要求は、アプリから *200 - OK* 応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-471">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="b1b1e-472">応答の本文では、リダイレクト URL が示されています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-472">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="b1b1e-473">URL が "*リダイレクト*" されるときに、サーバーへのラウンドトリップが実行されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-473">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="b1b1e-474">リダイレクト ルールを設定するときは注意してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-474">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="b1b1e-475">リダイレクト ルールは、リダイレクト後を含めて、アプリへのすべての要求で評価されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-475">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="b1b1e-476">"*無限リダイレクトのループ*" が誤って作成されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-476">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="b1b1e-477">元の要求: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-477">Original Request: `/redirect-rule/1234/5678`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="b1b1e-479">かっこ内に含まれる式の一部は、*キャプチャ グループ*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-479">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="b1b1e-480">式のドット (`.`) は、*どの文字とも一致する*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-480">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="b1b1e-481">アスタリスク (`*`) は、*直前の文字に 0 回以上一致する*ことを示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-481">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="b1b1e-482">そのため、URL の最後の 2 つのパス セグメント `1234/5678` は、キャプチャ グループ `(.*)` によってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-482">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="b1b1e-483">要求 URL で `redirect-rule/` の後に指定した任意の値が、この単一のキャプチャ グループによってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-483">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="b1b1e-484">置換文字列では、キャプチャされたグループがドル記号 (`$`) を使用して文字列に挿入され、その後にキャプチャのシーケンス番号が続きます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-484">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="b1b1e-485">最初のキャプチャ グループ値は、`$1` で取得され、2 番目は `$2` で取得され、これらは正規表現内のキャプチャ グループの順序で続行されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-485">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="b1b1e-486">サンプル アプリでは、リダイレクト ルールの正規表現内に 1 つのキャプチャ グループだけがあるので、置換文字列に 1 つの挿入されたグループ (`$1`) だけがあります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-486">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="b1b1e-487">ルールが適用されるときに、URL `/redirected/1234/5678` になります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-487">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="b1b1e-488">セキュリティで保護されたエンドポイントへの URL リダイレクト</span><span class="sxs-lookup"><span data-stu-id="b1b1e-488">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="b1b1e-489">HTTP 要求を、同じホストとパスに HTTPS プロトコルを使用してリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-489">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="b1b1e-490">状態コードが指定されていない場合、ミドルウェアは既定で "*302 - 検出*" に設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-490">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="b1b1e-491">ポートが指定されていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-491">If the port isn't supplied:</span></span>

* <span data-ttu-id="b1b1e-492">ミドルウェアの既定値 `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-492">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="b1b1e-493">スキームは `https` (HTTPS プロトコル) に変更されて、クライアントはポート 443 でリソースにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-493">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="b1b1e-494">次の例では、状態コードを "*301 - 完全な移動*" に設定し、ポートを 5001 に変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-494">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="b1b1e-495">セキュリティで保護されていない要求を、セキュリティで保護された HTTPS プロトコル (ポート 443) を使用して同じホストとパスにリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-495">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="b1b1e-496">ミドルウェアは状態コードを "*301 -完全な移動"* に設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-496">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="b1b1e-497">リダイレクト規則を追加せずにセキュリティで保護されたエンドポイントにリダイレクトするときは、HTTPS リダイレクト ミドルウェアを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-497">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="b1b1e-498">詳細については、「[HTTPS の適用](xref:security/enforcing-ssl#require-https)」のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-498">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="b1b1e-499">サンプル アプリは、`AddRedirectToHttps` または `AddRedirectToHttpsPermanent` の使用方法の例を示すことができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-499">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="b1b1e-500">拡張メソッドを `RewriteOptions` に追加します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-500">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="b1b1e-501">任意の URL でセキュリティ保護されていない要求をアプリに対して行います。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-501">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="b1b1e-502">自己署名証明書が信頼できないことを示すブラウザーのセキュリティ警告を消去するか、証明書を信頼する例外を作成します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-502">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="b1b1e-503">`AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure` を使用する元の要求</span><span class="sxs-lookup"><span data-stu-id="b1b1e-503">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="b1b1e-505">`AddRedirectToHttpsPermanent`: `http://localhost:5000/secure` を使用する元の要求</span><span class="sxs-lookup"><span data-stu-id="b1b1e-505">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="b1b1e-507">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="b1b1e-507">URL rewrite</span></span>

<span data-ttu-id="b1b1e-508">URL 書き換えのルールを作成するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-508">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="b1b1e-509">最初のパラメーターには、着信 URL のパスに一致する正規表現が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-509">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="b1b1e-510">2 番目のパラメーターは、置換文字列です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-510">The second parameter is the replacement string.</span></span> <span data-ttu-id="b1b1e-511">3 番目のパラメーター `skipRemainingRules: {true|false}` は、現在のルールが適用される場合に追加の書き換えルールをスキップするかどうかをミドルウェアに示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-511">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="b1b1e-512">元の要求: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-512">Original Request: `/rewrite-rule/1234/5678`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="b1b1e-514">式の先頭にあるキャレット (`^`) は、URL パスの先頭からマッチングが開始されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-514">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="b1b1e-515">前のリダイレクト ルール `redirect-rule/(.*)` の例では、正規表現の先頭にキャレット (`^`) がありません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-515">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="b1b1e-516">したがって、パスの `redirect-rule/` の前にどのような文字があっても一致します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-516">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="b1b1e-517">パス</span><span class="sxs-lookup"><span data-stu-id="b1b1e-517">Path</span></span>                               | <span data-ttu-id="b1b1e-518">一致したもの</span><span class="sxs-lookup"><span data-stu-id="b1b1e-518">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="b1b1e-519">はい</span><span class="sxs-lookup"><span data-stu-id="b1b1e-519">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="b1b1e-520">はい</span><span class="sxs-lookup"><span data-stu-id="b1b1e-520">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="b1b1e-521">はい</span><span class="sxs-lookup"><span data-stu-id="b1b1e-521">Yes</span></span>   |

<span data-ttu-id="b1b1e-522">書き換えルール `^rewrite-rule/(\d+)/(\d+)` は、`rewrite-rule/` で始まる場合のみパスと一致します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-522">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="b1b1e-523">次の表では、一致の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-523">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="b1b1e-524">パス</span><span class="sxs-lookup"><span data-stu-id="b1b1e-524">Path</span></span>                              | <span data-ttu-id="b1b1e-525">一致したもの</span><span class="sxs-lookup"><span data-stu-id="b1b1e-525">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="b1b1e-526">はい</span><span class="sxs-lookup"><span data-stu-id="b1b1e-526">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="b1b1e-527">×</span><span class="sxs-lookup"><span data-stu-id="b1b1e-527">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="b1b1e-528">いいえ</span><span class="sxs-lookup"><span data-stu-id="b1b1e-528">No</span></span>    |

<span data-ttu-id="b1b1e-529">式の `^rewrite-rule/` に続く部分に、2 つのキャプチャ グループ `(\d+)/(\d+)` があります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-529">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="b1b1e-530">`\d` は、*1 桁 (数字) に一致する*ことを示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-530">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="b1b1e-531">プラス記号 (`+`) は、*直前の 1 つ以上の文字に一致する*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-531">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="b1b1e-532">そのため、URL は、数字とその後に続くスラッシュ、さらにその後に続く別の数字を含んでいる必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-532">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="b1b1e-533">これらのキャプチャ グループが `$1` および `$2` として書き換えられた URL に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-533">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="b1b1e-534">書き換えルールの置換文字列は、クエリ文字列にキャプチャされたグループを配置します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-534">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="b1b1e-535">`/rewrite-rule/1234/5678` の要求されたパスは、`/rewritten?var1=1234&var2=5678` でリソースを取得するように書き換えられます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-535">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="b1b1e-536">クエリ文字列が元の要求に存在する場合、URL が書き換えられるときに保持されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-536">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="b1b1e-537">リソースを取得するためのサーバーへのラウンドトリップはありません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-537">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="b1b1e-538">リソースが存在する場合は、取得され、*200 - OK* 状態コードと共にクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-538">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="b1b1e-539">クライアントはリダイレクトされていないため、ブラウザーのアドレス バーの URL は変更されません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-539">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="b1b1e-540">クライアントは、URL の書き換え操作がサーバーで発生したことを検出できません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-540">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b1b1e-541">式内の照合ルールは計算量が多く、アプリの応答時間が長くなるので、可能な限り `skipRemainingRules: true` を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-541">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="b1b1e-542">最も高速なアプリの応答を実現するには以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-542">For the fastest app response:</span></span>
>
> * <span data-ttu-id="b1b1e-543">一致する頻度が最も多いルールから一致頻度が最も少ないルールへの順番で書き換えルールを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-543">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="b1b1e-544">一致が発生し、追加の処理が必要ないときに残りのルールの処理をスキップします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-544">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="b1b1e-545">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="b1b1e-545">Apache mod_rewrite</span></span>

<span data-ttu-id="b1b1e-546"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> を使用して Apache mod_rewrite ルールを適用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-546">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="b1b1e-547">ルール ファイルがアプリと共に展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-547">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="b1b1e-548">mod_rewrite ルールの情報と例については、「[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-548">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="b1b1e-549">*ApacheModRewrite.txt* ルール ファイルからルールを読み取るには、<xref:System.IO.StreamReader> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-549">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="b1b1e-550">サンプル アプリは、要求を `/apache-mod-rules-redirect/(.\*)` から `/redirected?id=$1` にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-550">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="b1b1e-551">応答の状態コードは "*302 -検出*" です。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-551">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="b1b1e-552">元の要求: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-552">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="b1b1e-554">ミドルウェアは、次の Apache mod_rewrite サーバー変数をサポートします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-554">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="b1b1e-555">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-555">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="b1b1e-556">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-556">HTTP_ACCEPT</span></span>
* <span data-ttu-id="b1b1e-557">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="b1b1e-557">HTTP_CONNECTION</span></span>
* <span data-ttu-id="b1b1e-558">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="b1b1e-558">HTTP_COOKIE</span></span>
* <span data-ttu-id="b1b1e-559">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="b1b1e-559">HTTP_FORWARDED</span></span>
* <span data-ttu-id="b1b1e-560">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="b1b1e-560">HTTP_HOST</span></span>
* <span data-ttu-id="b1b1e-561">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="b1b1e-561">HTTP_REFERER</span></span>
* <span data-ttu-id="b1b1e-562">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-562">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="b1b1e-563">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b1b1e-563">HTTPS</span></span>
* <span data-ttu-id="b1b1e-564">IPV6</span><span class="sxs-lookup"><span data-stu-id="b1b1e-564">IPV6</span></span>
* <span data-ttu-id="b1b1e-565">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="b1b1e-565">QUERY_STRING</span></span>
* <span data-ttu-id="b1b1e-566">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-566">REMOTE_ADDR</span></span>
* <span data-ttu-id="b1b1e-567">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-567">REMOTE_PORT</span></span>
* <span data-ttu-id="b1b1e-568">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-568">REQUEST_FILENAME</span></span>
* <span data-ttu-id="b1b1e-569">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="b1b1e-569">REQUEST_METHOD</span></span>
* <span data-ttu-id="b1b1e-570">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-570">REQUEST_SCHEME</span></span>
* <span data-ttu-id="b1b1e-571">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="b1b1e-571">REQUEST_URI</span></span>
* <span data-ttu-id="b1b1e-572">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-572">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="b1b1e-573">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-573">SERVER_ADDR</span></span>
* <span data-ttu-id="b1b1e-574">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-574">SERVER_PORT</span></span>
* <span data-ttu-id="b1b1e-575">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="b1b1e-575">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="b1b1e-576">TIME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-576">TIME</span></span>
* <span data-ttu-id="b1b1e-577">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="b1b1e-577">TIME_DAY</span></span>
* <span data-ttu-id="b1b1e-578">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-578">TIME_HOUR</span></span>
* <span data-ttu-id="b1b1e-579">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="b1b1e-579">TIME_MIN</span></span>
* <span data-ttu-id="b1b1e-580">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="b1b1e-580">TIME_MON</span></span>
* <span data-ttu-id="b1b1e-581">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="b1b1e-581">TIME_SEC</span></span>
* <span data-ttu-id="b1b1e-582">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="b1b1e-582">TIME_WDAY</span></span>
* <span data-ttu-id="b1b1e-583">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-583">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="b1b1e-584">IIS URL リライト モジュールのルール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-584">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="b1b1e-585">IIS URL リライト モジュールに適用される同じルール セットを使用するには、<xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-585">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="b1b1e-586">ルール ファイルがアプリと共に展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-586">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="b1b1e-587">Windows Server IIS で実行されているときにミドルウェアでアプリの *web.config* ファイルを使用するように指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-587">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="b1b1e-588">IIS を使用する場合、IIS リライト モジュールとの競合を避けるため、これらのルールをアプリの *web.config* の外部に保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-588">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="b1b1e-589">IIS URL リライト モジュールのルールの詳細と例については、「[Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)」(URL リライト モジュール 2.0 の使用 ) と「[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)」(URL リライト モジュール構成リファレンス) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-589">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="b1b1e-590">*IISUrlRewrite.xml* ルール ファイルからルールを読み取るには、<xref:System.IO.StreamReader> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-590">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="b1b1e-591">サンプル アプリは、要求を `/iis-rules-rewrite/(.*)` から `/rewritten?id=$1` に書き換えます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-591">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="b1b1e-592">応答は、*200 - OK* 状態コードでクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-592">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="b1b1e-593">元の要求: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-593">Original Request: `/iis-rules-rewrite/1234`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="b1b1e-595">望ましくない方法でアプリに影響するように構成されたサーバー レベル ルールを使用するアクティブな IIS リライト モジュールがある場合は、アプリに対して IIS リライト モジュールを無効にできます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-595">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="b1b1e-596">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-596">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="b1b1e-597">サポートされていない機能</span><span class="sxs-lookup"><span data-stu-id="b1b1e-597">Unsupported features</span></span>

<span data-ttu-id="b1b1e-598">ASP.NET Core 2.x でリリースされたミドルウェアは、次の IIS URL リライト モジュールの機能をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-598">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="b1b1e-599">送信ルール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-599">Outbound Rules</span></span>
* <span data-ttu-id="b1b1e-600">カスタム サーバー変数</span><span class="sxs-lookup"><span data-stu-id="b1b1e-600">Custom Server Variables</span></span>
* <span data-ttu-id="b1b1e-601">ワイルドカード</span><span class="sxs-lookup"><span data-stu-id="b1b1e-601">Wildcards</span></span>
* <span data-ttu-id="b1b1e-602">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="b1b1e-602">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="b1b1e-603">サポートされるサーバー変数</span><span class="sxs-lookup"><span data-stu-id="b1b1e-603">Supported server variables</span></span>

<span data-ttu-id="b1b1e-604">ミドルウェアは、次の IIS URL リライト モジュール サーバー変数をサポートします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-604">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="b1b1e-605">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="b1b1e-605">CONTENT_LENGTH</span></span>
* <span data-ttu-id="b1b1e-606">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="b1b1e-606">CONTENT_TYPE</span></span>
* <span data-ttu-id="b1b1e-607">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-607">HTTP_ACCEPT</span></span>
* <span data-ttu-id="b1b1e-608">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="b1b1e-608">HTTP_CONNECTION</span></span>
* <span data-ttu-id="b1b1e-609">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="b1b1e-609">HTTP_COOKIE</span></span>
* <span data-ttu-id="b1b1e-610">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="b1b1e-610">HTTP_HOST</span></span>
* <span data-ttu-id="b1b1e-611">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="b1b1e-611">HTTP_REFERER</span></span>
* <span data-ttu-id="b1b1e-612">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="b1b1e-612">HTTP_URL</span></span>
* <span data-ttu-id="b1b1e-613">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-613">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="b1b1e-614">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b1b1e-614">HTTPS</span></span>
* <span data-ttu-id="b1b1e-615">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-615">LOCAL_ADDR</span></span>
* <span data-ttu-id="b1b1e-616">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="b1b1e-616">QUERY_STRING</span></span>
* <span data-ttu-id="b1b1e-617">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="b1b1e-617">REMOTE_ADDR</span></span>
* <span data-ttu-id="b1b1e-618">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="b1b1e-618">REMOTE_PORT</span></span>
* <span data-ttu-id="b1b1e-619">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="b1b1e-619">REQUEST_FILENAME</span></span>
* <span data-ttu-id="b1b1e-620">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="b1b1e-620">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="b1b1e-621"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> を介して <xref:Microsoft.Extensions.FileProviders.IFileProvider> を取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-621">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="b1b1e-622">このアプローチは、書き換えルール ファイルの場所に関する大きな柔軟性を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-622">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="b1b1e-623">書き換えルール ファイルを指定したパスでサーバーに導入されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-623">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="b1b1e-624">メソッド ベースのルール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-624">Method-based rule</span></span>

<span data-ttu-id="b1b1e-625">メソッド内で独自のルール ロジックを実装するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-625">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="b1b1e-626">`Add` は <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> を公開し、メソッドで <xref:Microsoft.AspNetCore.Http.HttpContext> を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-626">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="b1b1e-627">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) は、追加のパイプライン処理の実行方法を決定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-627">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="b1b1e-628">次の表で説明する <xref:Microsoft.AspNetCore.Rewrite.RuleResult> フィールドのいずれかに値を設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-628">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="b1b1e-629">アクション</span><span class="sxs-lookup"><span data-stu-id="b1b1e-629">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="b1b1e-630">`RuleResult.ContinueRules` (既定値)</span><span class="sxs-lookup"><span data-stu-id="b1b1e-630">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="b1b1e-631">ルールの適用を続けます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-631">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="b1b1e-632">ルールの適用を停止し、応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-632">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="b1b1e-633">ルールの適用を停止し、次のミドルウェアにコンテキストを送信します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-633">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="b1b1e-634">サンプル アプリは、 *.xml* で終了するパスの要求をリダイレクトするメソッドの例を示します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-634">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="b1b1e-635">`/file.xml` に対して要求が行われた場合、要求は `/xmlfiles/file.xml` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-635">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="b1b1e-636">状態コードは "*301 - 完全な移動*" に設定されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-636">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="b1b1e-637">ブラウザーが */xmlfiles/file.xml* に対する新しい要求を行うと、静的ファイル ミドルウェアはクライアントに *wwwroot/xmlfiles* フォルダーからファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-637">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="b1b1e-638">リダイレクトの場合は、応答の状態コードを明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-638">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="b1b1e-639">それ以外の場合は、*200 - OK* 状態コードが返され、クライアントでのリダイレクトは発生しません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-639">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="b1b1e-640">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="b1b1e-640">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="b1b1e-641">このアプローチでは、要求を書き換えることもできます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-641">This approach can also rewrite requests.</span></span> <span data-ttu-id="b1b1e-642">サンプル アプリでは、*wwwroot* フォルダーから *file.txt* テキスト ファイルを提供するためにテキスト ファイル要求のパスを書き換える処理が示されています。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-642">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="b1b1e-643">静的ファイル ミドルウェアでは、更新された要求パスに基づいてファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-643">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="b1b1e-644">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="b1b1e-644">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="b1b1e-645">IRule ベースのルール</span><span class="sxs-lookup"><span data-stu-id="b1b1e-645">IRule-based rule</span></span>

<span data-ttu-id="b1b1e-646"><xref:Microsoft.AspNetCore.Rewrite.IRule> インターフェイスを実装するクラス内のルール ロジックを使用するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-646">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="b1b1e-647">`IRule` では、メソッド ベースのルール アプローチを使用する場合より高い柔軟性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-647">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="b1b1e-648">実装クラスには、<xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> メソッドに対してパラメーターを渡すことができるコンストラクターを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-648">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="b1b1e-649">サンプル アプリの `extension` と `newPath` のパラメーターの値は、いくつかの条件を満たすかチェックされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-649">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="b1b1e-650">`extension` は、値を含んでいる必要があり、値は *.png*、 *.jpg*、または *.gif* でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-650">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="b1b1e-651">`newPath` が有効ではない場合、<xref:System.ArgumentException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-651">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="b1b1e-652">*image.png* に対して行われた要求は、`/png-images/image.png` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-652">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="b1b1e-653">*image.jpg* に対して行われた要求は、`/jpg-images/image.jpg` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-653">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="b1b1e-654">状態コードは "*301 - 完全な移動*" に設定され、`context.Result` はルールの処理を停止して応答を送信するように設定されます。</span><span class="sxs-lookup"><span data-stu-id="b1b1e-654">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="b1b1e-655">元の要求: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-655">Original Request: `/image.png`</span></span>

![image.png の要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="b1b1e-657">元の要求: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-657">Original Request: `/image.jpg`</span></span>

![image.jpg の要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="b1b1e-659">正規表現の例</span><span class="sxs-lookup"><span data-stu-id="b1b1e-659">Regex examples</span></span>

| <span data-ttu-id="b1b1e-660">Goal</span><span class="sxs-lookup"><span data-stu-id="b1b1e-660">Goal</span></span> | <span data-ttu-id="b1b1e-661">正規表現文字列と</span><span class="sxs-lookup"><span data-stu-id="b1b1e-661">Regex String &</span></span><br><span data-ttu-id="b1b1e-662">一致の例</span><span class="sxs-lookup"><span data-stu-id="b1b1e-662">Match Example</span></span> | <span data-ttu-id="b1b1e-663">置換文字列と</span><span class="sxs-lookup"><span data-stu-id="b1b1e-663">Replacement String &</span></span><br><span data-ttu-id="b1b1e-664">出力の例</span><span class="sxs-lookup"><span data-stu-id="b1b1e-664">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="b1b1e-665">クエリ文字列内のパスを書き換える</span><span class="sxs-lookup"><span data-stu-id="b1b1e-665">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="b1b1e-666">末尾のスラッシュを除去する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-666">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="b1b1e-667">末尾のスラッシュを強制する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-667">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="b1b1e-668">特定の要求を書き換えを回避する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-668">Avoid rewriting specific requests</span></span> | <span data-ttu-id="b1b1e-669">`^(.*)(?<!\.axd)$` または `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-669">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="b1b1e-670">はい: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-670">Yes: `/resource.htm`</span></span><br><span data-ttu-id="b1b1e-671">いいえ: `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="b1b1e-671">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="b1b1e-672">URL セグメントを再配置する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-672">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="b1b1e-673">URL セグメントを置き換える</span><span class="sxs-lookup"><span data-stu-id="b1b1e-673">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b1b1e-674">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b1b1e-674">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="b1b1e-675">.NET の正規表現</span><span class="sxs-lookup"><span data-stu-id="b1b1e-675">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="b1b1e-676">正規表現言語 - クイック リファレンス</span><span class="sxs-lookup"><span data-stu-id="b1b1e-676">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="b1b1e-677">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="b1b1e-677">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="b1b1e-678">URL リライト モジュール 2.0 (IIS 用) の使用</span><span class="sxs-lookup"><span data-stu-id="b1b1e-678">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="b1b1e-679">URL リライト モジュール構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="b1b1e-679">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="b1b1e-680">IIS URL リライト モジュール フォーラム</span><span class="sxs-lookup"><span data-stu-id="b1b1e-680">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="b1b1e-681">単純な URL 構造を保持する</span><span class="sxs-lookup"><span data-stu-id="b1b1e-681">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="b1b1e-682">URL 書き換えの 10 のヒントとテクニック</span><span class="sxs-lookup"><span data-stu-id="b1b1e-682">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="b1b1e-683">スラッシュを使用すべきかどうか</span><span class="sxs-lookup"><span data-stu-id="b1b1e-683">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
