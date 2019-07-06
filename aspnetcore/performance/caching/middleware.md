---
title: 応答キャッシュ ミドルウェアで ASP.NET Core
author: guardrex
description: ASP.NET Core で応答キャッシュ ミドルウェアを構成し、使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: performance/caching/middleware
ms.openlocfilehash: d6756ce16396133da643cc08ca0f48369479ad3a
ms.sourcegitcommit: b9e914ef274b5ec359582f299724af6234dce135
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596152"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="ab258-103">応答キャッシュ ミドルウェアで ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab258-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="ab258-104">によって[Luke Latham](https://github.com/guardrex)と[John ルオ語](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="ab258-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="ab258-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ab258-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ab258-106">この記事では、ASP.NET Core アプリの応答キャッシュ ミドルウェアを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ab258-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="ab258-107">応答がキャッシュ可能な場合、ストアの応答、およびキャッシュから応答を返す役割を果たし、ミドルウェアを決定します。</span><span class="sxs-lookup"><span data-stu-id="ab258-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="ab258-108">HTTP キャッシュの概要について、 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性は、「[応答のキャッシュ](xref:performance/caching/response)します。</span><span class="sxs-lookup"><span data-stu-id="ab258-108">For an introduction to HTTP caching and the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="configuration"></a><span data-ttu-id="ab258-109">構成</span><span class="sxs-lookup"><span data-stu-id="ab258-109">Configuration</span></span>

<span data-ttu-id="ab258-110">使用して、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)へのパッケージ参照を追加したり、 [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="ab258-110">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="ab258-111">`Startup.ConfigureServices`、応答キャッシュ ミドルウェアをサービス コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="ab258-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="ab258-112">ミドルウェアを使用するアプリの構成、<xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>で要求処理パイプラインにミドルウェアを追加する拡張メソッド`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ab258-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="ab258-113">サンプル アプリは、後続の要求でキャッシュを制御するヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="ab258-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="ab258-114">[キャッシュ制御](https://tools.ietf.org/html/rfc7234#section-5.2)&ndash;に最大で 10 秒間キャッシュ可能な応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="ab258-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="ab258-115">[異なる](https://tools.ietf.org/html/rfc7231#section-7.1.4)&ndash;キャッシュされた応答の場合にのみを処理するためにミドルウェアを構成、 [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4)後続の要求のヘッダーの元の要求と一致します。</span><span class="sxs-lookup"><span data-stu-id="ab258-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="ab258-116">応答キャッシュ ミドルウェアは、200 (OK) 状態コードと、サーバーの応答のみをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="ab258-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="ab258-117">その他の応答を含む[エラー ページ](xref:fundamentals/error-handling)、ミドルウェアによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="ab258-118">認証されたクライアントのコンテンツを含む応答は、格納して、それらの応答の処理からミドルウェアを防ぐためにキャッシュ不可としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab258-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="ab258-119">参照してください[キャッシュ用の条件](#conditions-for-caching)については、応答がキャッシュ可能なかどうかに、ミドルウェアが決定します。</span><span class="sxs-lookup"><span data-stu-id="ab258-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="ab258-120">オプション</span><span class="sxs-lookup"><span data-stu-id="ab258-120">Options</span></span>

<span data-ttu-id="ab258-121">応答のキャッシュ オプションは、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-121">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="ab258-122">オプション</span><span class="sxs-lookup"><span data-stu-id="ab258-122">Option</span></span> | <span data-ttu-id="ab258-123">説明</span><span class="sxs-lookup"><span data-stu-id="ab258-123">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="ab258-124">(バイト単位)、応答本文の最大キャッシュ サイズ。</span><span class="sxs-lookup"><span data-stu-id="ab258-124">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="ab258-125">既定値は`64 * 1024 * 1024`(64 MB)。</span><span class="sxs-lookup"><span data-stu-id="ab258-125">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="ab258-126">応答キャッシュ ミドルウェア (バイト単位) のサイズ制限。</span><span class="sxs-lookup"><span data-stu-id="ab258-126">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="ab258-127">既定値は`100 * 1024 * 1024`(100 MB)。</span><span class="sxs-lookup"><span data-stu-id="ab258-127">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="ab258-128">大文字のパスで応答がキャッシュされるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="ab258-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="ab258-129">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="ab258-129">The default value is `false`.</span></span> |

<span data-ttu-id="ab258-130">次の例では、ミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="ab258-130">The following example configures the middleware to:</span></span>

* <span data-ttu-id="ab258-131">本文のサイズが 1,024 バイト以下で応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="ab258-131">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="ab258-132">パスの大文字小文字を区別して、応答を格納します。</span><span class="sxs-lookup"><span data-stu-id="ab258-132">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="ab258-133">たとえば、`/page1`と`/Page1`別に格納されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-133">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="ab258-134">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="ab258-134">VaryByQueryKeys</span></span>

<span data-ttu-id="ab258-135">MVC を使用したときに web API コント ローラーまたは Razor ページのページ モデル、/、 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性が応答のキャッシュ用の適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="ab258-135">When using MVC / web API controllers or Razor Pages page models, the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="ab258-136">唯一のパラメーター、`[ResponseCache]`ミドルウェアを厳密に必要な属性が<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>、する実際の HTTP ヘッダーに対応していません。</span><span class="sxs-lookup"><span data-stu-id="ab258-136">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="ab258-137">詳細については、「 <xref:performance/caching/response#responsecache-attribute> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab258-137">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="ab258-138">使用しない場合、`[ResponseCache]`属性と変化する応答のキャッシュ`VaryByQueryKeys`します。</span><span class="sxs-lookup"><span data-stu-id="ab258-138">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="ab258-139">使用して、<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>から直接、 [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span><span class="sxs-lookup"><span data-stu-id="ab258-139">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="ab258-140">等しい 1 つの値を使用して`*`で`VaryByQueryKeys`キャッシュをすべての要求のクエリ パラメーターによって異なります。</span><span class="sxs-lookup"><span data-stu-id="ab258-140">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="ab258-141">応答キャッシュ ミドルウェアで使用される HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="ab258-141">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="ab258-142">次の表では、応答がキャッシュに影響する HTTP ヘッダーの情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="ab258-142">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="ab258-143">Header</span><span class="sxs-lookup"><span data-stu-id="ab258-143">Header</span></span> | <span data-ttu-id="ab258-144">説明</span><span class="sxs-lookup"><span data-stu-id="ab258-144">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="ab258-145">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="ab258-145">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="ab258-146">マークされた応答をキャッシュにのみ考慮、ミドルウェア、`public`キャッシュ ディレクティブ。</span><span class="sxs-lookup"><span data-stu-id="ab258-146">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="ab258-147">次のパラメーターでキャッシュを制御します。</span><span class="sxs-lookup"><span data-stu-id="ab258-147">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="ab258-148">max-age</span><span class="sxs-lookup"><span data-stu-id="ab258-148">max-age</span></span></li><li><span data-ttu-id="ab258-149">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="ab258-149">max-stale&#8224;</span></span></li><li><span data-ttu-id="ab258-150">min 新規</span><span class="sxs-lookup"><span data-stu-id="ab258-150">min-fresh</span></span></li><li><span data-ttu-id="ab258-151">必要があります revalidate</span><span class="sxs-lookup"><span data-stu-id="ab258-151">must-revalidate</span></span></li><li><span data-ttu-id="ab258-152">キャッシュなし</span><span class="sxs-lookup"><span data-stu-id="ab258-152">no-cache</span></span></li><li><span data-ttu-id="ab258-153">no ストア</span><span class="sxs-lookup"><span data-stu-id="ab258-153">no-store</span></span></li><li><span data-ttu-id="ab258-154">専用の場合-キャッシュ</span><span class="sxs-lookup"><span data-stu-id="ab258-154">only-if-cached</span></span></li><li><span data-ttu-id="ab258-155">private</span><span class="sxs-lookup"><span data-stu-id="ab258-155">private</span></span></li><li><span data-ttu-id="ab258-156">public</span><span class="sxs-lookup"><span data-stu-id="ab258-156">public</span></span></li><li><span data-ttu-id="ab258-157">s maxage</span><span class="sxs-lookup"><span data-stu-id="ab258-157">s-maxage</span></span></li><li><span data-ttu-id="ab258-158">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="ab258-158">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="ab258-159">&#8224;制限が指定されていない場合`max-stale`ミドルウェアは行われません。</span><span class="sxs-lookup"><span data-stu-id="ab258-159">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="ab258-160">&#8225;`proxy-revalidate`同じ効果`must-revalidate`します。</span><span class="sxs-lookup"><span data-stu-id="ab258-160">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="ab258-161">詳細については、次を参照してください。 [RFC 7231。要求のキャッシュ制御ディレクティブ](https://tools.ietf.org/html/rfc7234#section-5.2.1)します。</span><span class="sxs-lookup"><span data-stu-id="ab258-161">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="ab258-162">A`Pragma: no-cache`要求のヘッダーと同じ効果を生成する`Cache-Control: no-cache`します。</span><span class="sxs-lookup"><span data-stu-id="ab258-162">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="ab258-163">このヘッダーは、関連するディレクティブによってオーバーライドされる、`Cache-Control`ヘッダー、存在する場合。</span><span class="sxs-lookup"><span data-stu-id="ab258-163">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="ab258-164">Http/1.0 との下位互換性と見なされます。</span><span class="sxs-lookup"><span data-stu-id="ab258-164">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="ab258-165">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="ab258-165">The response isn't cached if the header exists.</span></span> <span data-ttu-id="ab258-166">1 つまたは複数の cookie を設定する要求処理パイプラインですべてのミドルウェアが応答をキャッシュから応答キャッシュ ミドルウェアを防ぎます (たとえば、 [cookie ベース TempData プロバイダー](xref:fundamentals/app-state#tempdata))。</span><span class="sxs-lookup"><span data-stu-id="ab258-166">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="ab258-167">`Vary`別ヘッダーによってヘッダーが、キャッシュされた応答の変更に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-167">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="ab258-168">などを含めることによってエンコードすることによって応答をキャッシュ、`Vary: Accept-Encoding`ヘッダーの要求の応答をキャッシュするヘッダー`Accept-Encoding: gzip`と`Accept-Encoding: text/plain`とは別にします。</span><span class="sxs-lookup"><span data-stu-id="ab258-168">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="ab258-169">応答のヘッダー値が`*`は保存されません。</span><span class="sxs-lookup"><span data-stu-id="ab258-169">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="ab258-170">このヘッダーが古いと見なされる応答はありません格納、またはその他によってオーバーライドされない限りを取得`Cache-Control`ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="ab258-170">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="ab258-171">値がない場合、完全な応答がキャッシュから提供された`*`、`ETag`の応答と一致しない指定された値のいずれか。</span><span class="sxs-lookup"><span data-stu-id="ab258-171">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="ab258-172">それ以外の場合、304 (変更なし) の応答が提供されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-172">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="ab258-173">場合、`If-None-Match`ヘッダーが含まれていない、キャッシュされた応答の日付が指定された値よりも新しい場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-173">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="ab258-174">それ以外の場合、 *304 - 変更なし*応答を提供します。</span><span class="sxs-lookup"><span data-stu-id="ab258-174">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="ab258-175">キャッシュから提供するときに、`Date`元からの応答で指定されなかった場合、ミドルウェアによってヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-175">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="ab258-176">キャッシュから提供するときに、`Content-Length`元からの応答で指定されなかった場合、ミドルウェアによってヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-176">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="ab258-177">`Age`元の応答で送信されたヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="ab258-177">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="ab258-178">ミドルウェアは、キャッシュされた応答を提供するときに、新しい値を計算します。</span><span class="sxs-lookup"><span data-stu-id="ab258-178">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="ab258-179">要求キャッシュ コントロール ディレクティブを尊重キャッシュ</span><span class="sxs-lookup"><span data-stu-id="ab258-179">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="ab258-180">ミドルウェアの規則を尊重する、[仕様の HTTP 1.1 キャッシュ](https://tools.ietf.org/html/rfc7234#section-5.2)します。</span><span class="sxs-lookup"><span data-stu-id="ab258-180">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="ab258-181">規則を優先する有効なキャッシュを必要と`Cache-Control`クライアントによって送信されたヘッダー。</span><span class="sxs-lookup"><span data-stu-id="ab258-181">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="ab258-182">仕様では、クライアントがで要求を実行できる、`no-cache`ヘッダーの値と force、サーバー要求のたびに新しい応答を生成します。</span><span class="sxs-lookup"><span data-stu-id="ab258-182">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="ab258-183">現時点ではありませんこのキャッシュの動作を開発者の制御、ミドルウェアは、公式のキャッシュの仕様に準拠するために、ミドルウェアを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="ab258-183">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="ab258-184">キャッシュの動作をより細かく制御は、ASP.NET Core の他のキャッシュ機能を探索します。</span><span class="sxs-lookup"><span data-stu-id="ab258-184">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="ab258-185">次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab258-185">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="ab258-186">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="ab258-186">Troubleshooting</span></span>

<span data-ttu-id="ab258-187">キャッシュの動作が期待どおりにしていない場合は、応答がキャッシュとキャッシュから提供される機能のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ab258-187">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="ab258-188">要求の受信ヘッダーおよび応答の送信ヘッダーを調べます。</span><span class="sxs-lookup"><span data-stu-id="ab258-188">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="ab258-189">有効にする[ログ](xref:fundamentals/logging/index)デバッグを支援します。</span><span class="sxs-lookup"><span data-stu-id="ab258-189">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="ab258-190">テストとキャッシュ動作のトラブルシューティング、ブラウザーが好ましくない方法でキャッシュに影響する要求ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="ab258-190">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="ab258-191">たとえば、ブラウザー設定、`Cache-Control`ヘッダーを`no-cache`または`max-age=0`ページを更新するときにします。</span><span class="sxs-lookup"><span data-stu-id="ab258-191">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="ab258-192">次のツールでは、要求ヘッダーを明示的に設定することができ、キャッシュのテストに適しています。</span><span class="sxs-lookup"><span data-stu-id="ab258-192">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="ab258-193">Fiddler</span><span class="sxs-lookup"><span data-stu-id="ab258-193">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="ab258-194">Postman</span><span class="sxs-lookup"><span data-stu-id="ab258-194">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="ab258-195">キャッシュの条件</span><span class="sxs-lookup"><span data-stu-id="ab258-195">Conditions for caching</span></span>

* <span data-ttu-id="ab258-196">要求は、200 (OK) 状態コードでサーバーの応答になる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab258-196">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="ab258-197">要求メソッドは、GET または HEAD である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab258-197">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="ab258-198">`Startup.Configure`、応答キャッシュ ミドルウェアは、キャッシュが必要なミドルウェアの前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab258-198">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="ab258-199">詳細については、「 <xref:fundamentals/middleware/index> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ab258-199">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="ab258-200">`Authorization`ヘッダーが存在しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ab258-200">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="ab258-201">`Cache-Control` ヘッダー パラメーターが有効である必要があり、応答をマークする必要があります`public`としてマークされていないと`private`します。</span><span class="sxs-lookup"><span data-stu-id="ab258-201">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="ab258-202">`Pragma: no-cache`ヘッダーが存在しない場合がある場合、`Cache-Control`ヘッダーが存在しない、として、`Cache-Control`ヘッダーの上書き、`Pragma`ヘッダーが存在する場合。</span><span class="sxs-lookup"><span data-stu-id="ab258-202">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="ab258-203">`Set-Cookie`ヘッダーが存在しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ab258-203">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="ab258-204">`Vary` ヘッダーのパラメーターが有効であり、等しくする必要があります`*`します。</span><span class="sxs-lookup"><span data-stu-id="ab258-204">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="ab258-205">`Content-Length`ヘッダーの値 (場合に設定)、応答本文のサイズに一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab258-205">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="ab258-206"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>は使用されません。</span><span class="sxs-lookup"><span data-stu-id="ab258-206">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="ab258-207">応答で指定された古いすることはできません、`Expires`ヘッダーと`max-age`と`s-maxage`ディレクティブをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="ab258-207">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="ab258-208">応答バッファリングと、成功したことがあります。</span><span class="sxs-lookup"><span data-stu-id="ab258-208">Response buffering must be successful.</span></span> <span data-ttu-id="ab258-209">応答のサイズが、構成されているよりも小さくする必要がありますまたは既定の<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>します。</span><span class="sxs-lookup"><span data-stu-id="ab258-209">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="ab258-210">応答の本文のサイズが、構成されているよりも小さくする必要がありますまたは既定の<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>します。</span><span class="sxs-lookup"><span data-stu-id="ab258-210">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="ab258-211">応答はに従ってキャッシュ可能である必要があります、 [RFC 7234](https://tools.ietf.org/html/rfc7234)仕様。</span><span class="sxs-lookup"><span data-stu-id="ab258-211">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="ab258-212">たとえば、`no-store`ディレクティブが要求または応答のヘッダー フィールドに存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ab258-212">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="ab258-213">参照してください*セクション 3。応答をキャッシュに格納する*の[RFC 7234](https://tools.ietf.org/html/rfc7234)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="ab258-213">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="ab258-214">偽造防止システムをクロスサイト リクエスト フォージェリ (CSRF) を防ぐためにセキュリティで保護されたトークンを生成するセットの攻撃、`Cache-Control`と`Pragma`ヘッダーを`no-cache`応答がキャッシュされないようにします。</span><span class="sxs-lookup"><span data-stu-id="ab258-214">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="ab258-215">HTML フォーム要素を偽造防止トークンを無効にする方法については、次を参照してください。<xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>します。</span><span class="sxs-lookup"><span data-stu-id="ab258-215">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab258-216">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ab258-216">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
