---
title: ASP.NET Core での応答キャッシュミドルウェア
author: guardrex
description: ASP.NET Core で応答キャッシュ ミドルウェアを構成し、使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/09/2019
uid: performance/caching/middleware
ms.openlocfilehash: 838a08c12316d218501f26d5905f9e31ab93dfc9
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994226"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="28bf7-103">ASP.NET Core での応答キャッシュミドルウェア</span><span class="sxs-lookup"><span data-stu-id="28bf7-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="28bf7-104">[Luke Latham](https://github.com/guardrex)および[John luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="28bf7-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="28bf7-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="28bf7-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="28bf7-106">この記事では、ASP.NET Core アプリで応答キャッシュミドルウェアを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="28bf7-107">ミドルウェアは、応答をキャッシュ可能にするタイミングを決定し、応答を格納して、キャッシュからの応答を提供します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="28bf7-108">HTTP キャッシュと[[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性の概要については、「[応答キャッシュ](xref:performance/caching/response)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="28bf7-108">For an introduction to HTTP caching and the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="configuration"></a><span data-ttu-id="28bf7-109">構成</span><span class="sxs-lookup"><span data-stu-id="28bf7-109">Configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="28bf7-110">応答キャッシュミドルウェアは、使用できアプリ ASP.NET Core に暗黙的に追加される[AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-110">Response Caching Middleware is made avaiable by the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package, which is implicitly added to ASP.NET Core apps.</span></span>

<span data-ttu-id="28bf7-111">で`Startup.ConfigureServices`、応答キャッシュミドルウェアをサービスコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="28bf7-112"><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>拡張メソッドと共にミドルウェアを使用するようにアプリを構成します。これにより、の`Startup.Configure`要求処理パイプラインにミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

<span data-ttu-id="28bf7-113">サンプルアプリでは、後続の要求でキャッシュを制御するためのヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-113">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="28bf7-114">[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)&ndash;キャッシュ可能な応答を最大10秒間キャッシュします。</span><span class="sxs-lookup"><span data-stu-id="28bf7-114">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="28bf7-115">[変化](https://tools.ietf.org/html/rfc7231#section-7.1.4)後続の要求のヘッダーが[`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4)元の要求のヘッダーと一致する場合にのみ、キャッシュされた応答を提供するようにミドルウェアを構成します。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="28bf7-115">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="28bf7-116">応答キャッシュミドルウェアは、200 (OK) 状態コードを生成するサーバー応答のみをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="28bf7-116">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="28bf7-117">[エラーページ](xref:fundamentals/error-handling)を含むその他の応答は、ミドルウェアによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-117">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="28bf7-118">認証されたクライアントのコンテンツを含む応答は、ミドルウェアがそれらの応答を格納してサービスを提供しないように、キャッシュ不可能としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-118">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="28bf7-119">応答がキャッシュ可能かどうかをミドルウェアが決定する方法の詳細については、「[キャッシュの条件](#conditions-for-caching)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="28bf7-119">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="28bf7-120">[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を使用するか、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-120">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="28bf7-121">で`Startup.ConfigureServices`、応答キャッシュミドルウェアをサービスコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-121">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="28bf7-122"><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>拡張メソッドと共にミドルウェアを使用するようにアプリを構成します。これにより、の`Startup.Configure`要求処理パイプラインにミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-122">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="28bf7-123">サンプルアプリでは、後続の要求でキャッシュを制御するためのヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-123">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="28bf7-124">[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)&ndash;キャッシュ可能な応答を最大10秒間キャッシュします。</span><span class="sxs-lookup"><span data-stu-id="28bf7-124">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="28bf7-125">[変化](https://tools.ietf.org/html/rfc7231#section-7.1.4)後続の要求のヘッダーが[`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4)元の要求のヘッダーと一致する場合にのみ、キャッシュされた応答を提供するようにミドルウェアを構成します。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="28bf7-125">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; Configures the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="28bf7-126">応答キャッシュミドルウェアは、200 (OK) 状態コードを生成するサーバー応答のみをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="28bf7-126">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="28bf7-127">[エラーページ](xref:fundamentals/error-handling)を含むその他の応答は、ミドルウェアによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-127">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="28bf7-128">認証されたクライアントのコンテンツを含む応答は、ミドルウェアがそれらの応答を格納してサービスを提供しないように、キャッシュ不可能としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-128">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="28bf7-129">応答がキャッシュ可能かどうかをミドルウェアが決定する方法の詳細については、「[キャッシュの条件](#conditions-for-caching)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="28bf7-129">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

::: moniker-end

## <a name="options"></a><span data-ttu-id="28bf7-130">オプション</span><span class="sxs-lookup"><span data-stu-id="28bf7-130">Options</span></span>

<span data-ttu-id="28bf7-131">応答キャッシュのオプションを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-131">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="28bf7-132">オプション</span><span class="sxs-lookup"><span data-stu-id="28bf7-132">Option</span></span> | <span data-ttu-id="28bf7-133">説明</span><span class="sxs-lookup"><span data-stu-id="28bf7-133">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="28bf7-134">応答本文の最大キャッシュ可能サイズ (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="28bf7-134">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="28bf7-135">既定値は`64 * 1024 * 1024` (64 MB) です。</span><span class="sxs-lookup"><span data-stu-id="28bf7-135">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="28bf7-136">応答キャッシュミドルウェアのサイズ制限 (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="28bf7-136">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="28bf7-137">既定値は`100 * 1024 * 1024` (100 MB) です。</span><span class="sxs-lookup"><span data-stu-id="28bf7-137">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="28bf7-138">大文字と小文字が区別されるパスに応答をキャッシュするかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-138">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="28bf7-139">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="28bf7-139">The default value is `false`.</span></span> |

<span data-ttu-id="28bf7-140">次の例では、ミドルウェアをに構成します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-140">The following example configures the middleware to:</span></span>

* <span data-ttu-id="28bf7-141">本文のサイズが1024バイト以下の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="28bf7-141">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="28bf7-142">大文字と小文字を区別するパスで応答を格納します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-142">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="28bf7-143">たとえば、 `/page1`と`/Page1`は別々に格納されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-143">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="28bf7-144">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="28bf7-144">VaryByQueryKeys</span></span>

<span data-ttu-id="28bf7-145">MVC/web API コントローラーまたは Razor Pages ページモデルを使用する場合、 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性では、応答キャッシュ用に適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-145">When using MVC / web API controllers or Razor Pages page models, the [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="28bf7-146">ミドルウェアを厳密に必要`[ResponseCache]`とする属性の唯一のパラメーター <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>は、実際の HTTP ヘッダーに対応していません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-146">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="28bf7-147">詳細については、「 <xref:performance/caching/response#responsecache-attribute> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="28bf7-147">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="28bf7-148">`[ResponseCache]`属性を使用しない場合は、を`VaryByQueryKeys`使用して応答のキャッシュを変化させることができます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-148">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="28bf7-149"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> を [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) から直接使用します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-149">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="28bf7-150">`*` で`VaryByQueryKeys` 1 つの値を使用すると、すべての要求クエリパラメーターによってキャッシュが異なります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-150">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="28bf7-151">応答キャッシュミドルウェアによって使用される HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="28bf7-151">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="28bf7-152">次の表は、応答のキャッシュに影響を与える HTTP ヘッダーに関する情報を示しています。</span><span class="sxs-lookup"><span data-stu-id="28bf7-152">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="28bf7-153">Header</span><span class="sxs-lookup"><span data-stu-id="28bf7-153">Header</span></span> | <span data-ttu-id="28bf7-154">説明</span><span class="sxs-lookup"><span data-stu-id="28bf7-154">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="28bf7-155">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-155">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="28bf7-156">ミドルウェアは、 `public` cache ディレクティブでマークされたキャッシュ応答のみを考慮します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-156">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="28bf7-157">次のパラメーターを使用してキャッシュを制御します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-157">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="28bf7-158">max-age</span><span class="sxs-lookup"><span data-stu-id="28bf7-158">max-age</span></span></li><li><span data-ttu-id="28bf7-159">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="28bf7-159">max-stale&#8224;</span></span></li><li><span data-ttu-id="28bf7-160">最小-新規</span><span class="sxs-lookup"><span data-stu-id="28bf7-160">min-fresh</span></span></li><li><span data-ttu-id="28bf7-161">再検証が必要</span><span class="sxs-lookup"><span data-stu-id="28bf7-161">must-revalidate</span></span></li><li><span data-ttu-id="28bf7-162">キャッシュなし</span><span class="sxs-lookup"><span data-stu-id="28bf7-162">no-cache</span></span></li><li><span data-ttu-id="28bf7-163">ストアなし</span><span class="sxs-lookup"><span data-stu-id="28bf7-163">no-store</span></span></li><li><span data-ttu-id="28bf7-164">-if-キャッシュ済み</span><span class="sxs-lookup"><span data-stu-id="28bf7-164">only-if-cached</span></span></li><li><span data-ttu-id="28bf7-165">private</span><span class="sxs-lookup"><span data-stu-id="28bf7-165">private</span></span></li><li><span data-ttu-id="28bf7-166">public</span><span class="sxs-lookup"><span data-stu-id="28bf7-166">public</span></span></li><li><span data-ttu-id="28bf7-167">s-maxage</span><span class="sxs-lookup"><span data-stu-id="28bf7-167">s-maxage</span></span></li><li><span data-ttu-id="28bf7-168">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="28bf7-168">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="28bf7-169">&#8224;に`max-stale`制限が指定されていない場合、ミドルウェアは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-169">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="28bf7-170">&#8225;`proxy-revalidate`と`must-revalidate`同じ効果があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-170">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="28bf7-171">詳細については[、RFC 7231 を参照してください。キャッシュ制御ディレクティブ](https://tools.ietf.org/html/rfc7234#section-5.2.1)を要求します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-171">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="28bf7-172">要求`Pragma: no-cache`のヘッダーでは、と`Cache-Control: no-cache`同じ効果が得られます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-172">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="28bf7-173">このヘッダーは、 `Cache-Control`ヘッダー内の関連するディレクティブによってオーバーライドされます (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="28bf7-173">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="28bf7-174">HTTP/1.0 との下位互換性のために考慮されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-174">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="28bf7-175">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-175">The response isn't cached if the header exists.</span></span> <span data-ttu-id="28bf7-176">1つ以上の cookie を設定する要求処理パイプライン内のミドルウェアは、応答キャッシュミドルウェアが応答をキャッシュしないようにします ( [cookie ベースの TempData プロバイダー](xref:fundamentals/app-state#tempdata)など)。</span><span class="sxs-lookup"><span data-stu-id="28bf7-176">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="28bf7-177">ヘッダー `Vary`は、キャッシュされた応答を別のヘッダーによって変更するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-177">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="28bf7-178">たとえば、 `Vary: Accept-Encoding`ヘッダーを含めることによって、エンコードによって応答をキャッシュします。 `Accept-Encoding: text/plain`ヘッダー `Accept-Encoding: gzip`とは別に要求の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="28bf7-178">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="28bf7-179">ヘッダー値がの`*`応答は格納されません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-179">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="28bf7-180">このヘッダーによって古いと見なされる応答は、他の`Cache-Control`ヘッダーでオーバーライドされない限り、格納または取得されません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-180">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="28bf7-181">値`*`がではなく、応答のが`ETag`指定された値と一致しない場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-181">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="28bf7-182">それ以外の場合は、304 (変更なし) の応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-182">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="28bf7-183">`If-None-Match`ヘッダーが存在しない場合、キャッシュされた応答の日付が指定した値より新しい場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-183">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="28bf7-184">それ以外の場合は、 *304-変更されていない*応答が提供されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-184">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="28bf7-185">キャッシュからサービスを提供し`Date`ている場合、ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="28bf7-185">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="28bf7-186">キャッシュからサービスを提供し`Content-Length`ている場合、ヘッダーはミドルウェアによって設定されます (元の応答で指定されていない場合)。</span><span class="sxs-lookup"><span data-stu-id="28bf7-186">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="28bf7-187">元`Age`の応答で送信されたヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-187">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="28bf7-188">ミドルウェアは、キャッシュされた応答を提供するときに新しい値を計算します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-188">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="28bf7-189">キャッシュは、要求のキャッシュ制御ディレクティブに従います。</span><span class="sxs-lookup"><span data-stu-id="28bf7-189">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="28bf7-190">ミドルウェアは、 [HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)の規則を尊重します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-190">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="28bf7-191">規則では、クライアントによって送信`Cache-Control`された有効なヘッダーを受け入れるためにキャッシュが必要です。</span><span class="sxs-lookup"><span data-stu-id="28bf7-191">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="28bf7-192">この仕様では、クライアントは`no-cache`ヘッダー値を使用して要求を行い、すべての要求に対してサーバーに新しい応答を強制的に生成させることができます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-192">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="28bf7-193">現時点では、ミドルウェアが公式のキャッシュ仕様に準拠しているため、ミドルウェアを使用する場合、このキャッシュ動作を開発者が制御することはありません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-193">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="28bf7-194">キャッシュの動作を詳細に制御するには、ASP.NET Core の他のキャッシュ機能を調べます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-194">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="28bf7-195">次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="28bf7-195">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="28bf7-196">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="28bf7-196">Troubleshooting</span></span>

<span data-ttu-id="28bf7-197">キャッシュの動作が想定どおりでない場合は、応答がキャッシュ可能であり、キャッシュから提供できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-197">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="28bf7-198">要求の受信ヘッダーと応答の送信ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="28bf7-198">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="28bf7-199">デバッグに役立つように[ログ記録](xref:fundamentals/logging/index)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="28bf7-199">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="28bf7-200">キャッシュ動作のテストとトラブルシューティングを行う場合、ブラウザーでは、望ましくない方法でキャッシュに影響を与える要求ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-200">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="28bf7-201">たとえば、ページを更新するときに`Cache-Control` 、ブラウザー `no-cache`で`max-age=0`ヘッダーをまたはに設定できます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-201">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="28bf7-202">次のツールでは、要求ヘッダーを明示的に設定でき、キャッシュのテストに適しています。</span><span class="sxs-lookup"><span data-stu-id="28bf7-202">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="28bf7-203">Fiddler</span><span class="sxs-lookup"><span data-stu-id="28bf7-203">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="28bf7-204">Postman</span><span class="sxs-lookup"><span data-stu-id="28bf7-204">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="28bf7-205">キャッシュの条件</span><span class="sxs-lookup"><span data-stu-id="28bf7-205">Conditions for caching</span></span>

* <span data-ttu-id="28bf7-206">要求は、200 (OK) 状態コードでサーバー応答を生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-206">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="28bf7-207">要求メソッドは GET または HEAD である必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-207">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="28bf7-208">で`Startup.Configure`は、応答キャッシュミドルウェアは、キャッシュを必要とするミドルウェアの前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-208">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="28bf7-209">詳細については、「 <xref:fundamentals/middleware/index> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="28bf7-209">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="28bf7-210">ヘッダー `Authorization`を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-210">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="28bf7-211">`Cache-Control`ヘッダーパラメーターは有効である必要があり、応答は`public`とマークさ`private`れている必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-211">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="28bf7-212">ヘッダーが存在する場合、ヘッダーは`Cache-Control`ヘッダーをオーバーライド`Pragma`するので`Cache-Control` 、ヘッダーが存在しない場合はヘッダーが存在しない必要があります。 `Pragma: no-cache`</span><span class="sxs-lookup"><span data-stu-id="28bf7-212">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="28bf7-213">ヘッダー `Set-Cookie`を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-213">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="28bf7-214">`Vary`ヘッダーパラメーターはと同じ`*`である必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-214">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="28bf7-215">ヘッダー `Content-Length`値 (設定されている場合) は、応答本文のサイズと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-215">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="28bf7-216">は<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>使用されません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-216">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="28bf7-217">`Expires`ヘッダー、および、および`s-maxage`の`max-age`各キャッシュディレクティブで指定されているとおり、応答を古いものにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-217">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="28bf7-218">応答バッファリングを成功させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-218">Response buffering must be successful.</span></span> <span data-ttu-id="28bf7-219">応答のサイズは、構成済みまたは既定値<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>よりも小さくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-219">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="28bf7-220">応答の本文のサイズは、構成済みまたは既定値<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>よりも小さくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-220">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="28bf7-221">応答は、 [RFC 7234](https://tools.ietf.org/html/rfc7234)仕様に従ってキャッシュ可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="28bf7-221">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="28bf7-222">たとえば、 `no-store`ディレクティブは、要求または応答のヘッダーフィールドに存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="28bf7-222">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="28bf7-223">セクション*3 を参照してください。詳細について*は、 [RFC 7234](https://tools.ietf.org/html/rfc7234)のキャッシュに応答を格納する。</span><span class="sxs-lookup"><span data-stu-id="28bf7-223">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="28bf7-224">クロスサイト要求偽造 (csrf) 攻撃を防ぐために、セキュリティで保護されたトークンを`Cache-Control`生成するため`no-cache`の偽造防止システムは、応答がキャッシュされないようにヘッダーとヘッダーをに設定し`Pragma`ます。</span><span class="sxs-lookup"><span data-stu-id="28bf7-224">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="28bf7-225">HTML フォーム要素の偽造防止トークンを無効にする方法について<xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="28bf7-225">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28bf7-226">その他の資料</span><span class="sxs-lookup"><span data-stu-id="28bf7-226">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
