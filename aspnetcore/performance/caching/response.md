---
title: ASP.NET Core での応答のキャッシュ
author: rick-anderson
description: 応答キャッシュを使用し、帯域幅要件を下げ、ASP.NET Core アプリのパフォーマンスを上げる方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/15/2019
uid: performance/caching/response
ms.openlocfilehash: 4ebac97689347245d25e0954b33729d78dd1b516
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378831"
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="c827e-103">ASP.NET Core での応答のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="c827e-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="c827e-104">By [John Luo](https://github.com/JunTaoLuo)、 [Rick Anderson](https://twitter.com/RickAndMSFT)、[上田 Smith](https://ardalis.com/)、および[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c827e-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c827e-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c827e-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c827e-106">応答キャッシュを使用すると、クライアントまたはプロキシが web サーバーに対して行う要求の数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="c827e-106">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="c827e-107">応答キャッシュを使用すると、web サーバーが応答を生成するために実行する作業量も少なくなります。</span><span class="sxs-lookup"><span data-stu-id="c827e-107">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="c827e-108">応答のキャッシュは、クライアント、プロキシ、およびミドルウェアが応答をキャッシュする方法を指定するヘッダーによって制御されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-108">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="c827e-109">[ResponseCache 属性](#responsecache-attribute)は、応答をキャッシュするときにクライアントが受け入れることができる応答キャッシュヘッダーの設定に参加します。</span><span class="sxs-lookup"><span data-stu-id="c827e-109">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers, which clients may honor when caching responses.</span></span> <span data-ttu-id="c827e-110">[応答キャッシュミドルウェア](xref:performance/caching/middleware)は、サーバーで応答をキャッシュするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="c827e-110">[Response Caching Middleware](xref:performance/caching/middleware) can be used to cache responses on the server.</span></span> <span data-ttu-id="c827e-111">ミドルウェアは、@no__t 0 のプロパティを使用して、サーバー側のキャッシュ動作に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="c827e-111">The middleware can use <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="c827e-112">HTTP ベースの応答のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="c827e-112">HTTP-based response caching</span></span>

<span data-ttu-id="c827e-113">[HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234)では、インターネットキャッシュの動作方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c827e-113">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="c827e-114">キャッシュに使用されるプライマリ HTTP ヘッダーは[cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)で、キャッシュ*ディレクティブ*を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-114">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="c827e-115">ディレクティブは、要求に応じてキャッシュ動作を制御し、応答としてサーバーからクライアントへの応答を行います。</span><span class="sxs-lookup"><span data-stu-id="c827e-115">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="c827e-116">要求と応答はプロキシサーバーを経由して移動し、プロキシサーバーも HTTP 1.1 キャッシュ仕様に準拠している必要があります。</span><span class="sxs-lookup"><span data-stu-id="c827e-116">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="c827e-117">次の表に、一般的な `Cache-Control` ディレクティブを示します。</span><span class="sxs-lookup"><span data-stu-id="c827e-117">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="c827e-118">ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="c827e-118">Directive</span></span>                                                       | <span data-ttu-id="c827e-119">操作</span><span class="sxs-lookup"><span data-stu-id="c827e-119">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="c827e-120">public</span><span class="sxs-lookup"><span data-stu-id="c827e-120">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="c827e-121">キャッシュは応答を格納できます。</span><span class="sxs-lookup"><span data-stu-id="c827e-121">A cache may store the response.</span></span> |
| [<span data-ttu-id="c827e-122">private</span><span class="sxs-lookup"><span data-stu-id="c827e-122">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="c827e-123">応答は、共有キャッシュによって格納されていない必要があります。</span><span class="sxs-lookup"><span data-stu-id="c827e-123">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="c827e-124">プライベートキャッシュは、応答を格納して再利用できます。</span><span class="sxs-lookup"><span data-stu-id="c827e-124">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="c827e-125">最長有効期間</span><span class="sxs-lookup"><span data-stu-id="c827e-125">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="c827e-126">クライアントは、指定された秒数よりも有効期間が長い応答を受け入れません。</span><span class="sxs-lookup"><span data-stu-id="c827e-126">The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="c827e-127">例: `max-age=60` (60 秒)、`max-age=2592000` (1 か月)</span><span class="sxs-lookup"><span data-stu-id="c827e-127">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="c827e-128">キャッシュなし</span><span class="sxs-lookup"><span data-stu-id="c827e-128">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="c827e-129">**要求時**: キャッシュは、要求を満たすために格納された応答を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="c827e-129">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="c827e-130">配信元サーバーはクライアントの応答を再生成し、ミドルウェアはキャッシュに格納されている応答を更新します。</span><span class="sxs-lookup"><span data-stu-id="c827e-130">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="c827e-131">**応答時**: 配信元サーバーで検証を行わずに、後続の要求に応答を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="c827e-131">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="c827e-132">ストアなし</span><span class="sxs-lookup"><span data-stu-id="c827e-132">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="c827e-133">**要求時**: キャッシュは要求を格納できません。</span><span class="sxs-lookup"><span data-stu-id="c827e-133">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="c827e-134">**応答**の場合: キャッシュは、応答の一部を格納することはできません。</span><span class="sxs-lookup"><span data-stu-id="c827e-134">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="c827e-135">キャッシュでロールを果たすその他のキャッシュヘッダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="c827e-135">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="c827e-136">Header</span><span class="sxs-lookup"><span data-stu-id="c827e-136">Header</span></span>                                                     | <span data-ttu-id="c827e-137">機能</span><span class="sxs-lookup"><span data-stu-id="c827e-137">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="c827e-138">変更</span><span class="sxs-lookup"><span data-stu-id="c827e-138">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="c827e-139">配信元サーバーで応答が生成または正常に検証されてからの、秒単位の推定時間。</span><span class="sxs-lookup"><span data-stu-id="c827e-139">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="c827e-140">経過</span><span class="sxs-lookup"><span data-stu-id="c827e-140">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="c827e-141">応答が古くなったと見なされるまでの時間。</span><span class="sxs-lookup"><span data-stu-id="c827e-141">The time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="c827e-142">Unmanaged</span><span class="sxs-lookup"><span data-stu-id="c827e-142">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="c827e-143">@No__t-0 の動作を設定するための HTTP/1.0 キャッシュとの下位互換性を維持するために存在します。</span><span class="sxs-lookup"><span data-stu-id="c827e-143">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="c827e-144">@No__t-0 ヘッダーが存在する場合、`Pragma` ヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-144">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="c827e-145">要因</span><span class="sxs-lookup"><span data-stu-id="c827e-145">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="c827e-146">キャッシュされた応答の元の要求と新しい要求の両方ですべての `Vary` ヘッダーフィールドが一致しない限り、キャッシュされた応答を送信しないように指定します。</span><span class="sxs-lookup"><span data-stu-id="c827e-146">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="c827e-147">HTTP ベースのキャッシュは、要求のキャッシュ制御ディレクティブを尊重します。</span><span class="sxs-lookup"><span data-stu-id="c827e-147">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="c827e-148">[Cache-control ヘッダーの HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)では、クライアントから送信された有効な `Cache-Control` ヘッダーを優先するキャッシュが必要です。</span><span class="sxs-lookup"><span data-stu-id="c827e-148">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="c827e-149">クライアントは、@no__t 0 のヘッダー値を使用して要求を実行し、すべての要求に対してサーバーが新しい応答を生成するように強制できます。</span><span class="sxs-lookup"><span data-stu-id="c827e-149">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="c827e-150">HTTP キャッシュの目的を検討している場合は、常にクライアントの `Cache-Control` 要求ヘッダーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c827e-150">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="c827e-151">公式仕様では、キャッシュは、クライアント、プロキシ、およびサーバーのネットワーク経由で要求を満たすことの待機時間とネットワークオーバーヘッドを削減することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="c827e-151">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="c827e-152">配信元サーバーの負荷を制御する方法であるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="c827e-152">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="c827e-153">ミドルウェアが公式のキャッシュ仕様に準拠しているため、[応答キャッシュミドルウェア](xref:performance/caching/middleware)を使用する場合、開発者はこのキャッシュ動作を制御することはできません。</span><span class="sxs-lookup"><span data-stu-id="c827e-153">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="c827e-154">ミドルウェアの計画された[拡張機能](https://github.com/aspnet/AspNetCore/issues/2612)は、キャッシュされた応答の提供を決定するときに、要求の `Cache-Control` ヘッダーを無視するようにミドルウェアを構成する機会です。</span><span class="sxs-lookup"><span data-stu-id="c827e-154">[Planned enhancements to the middleware](https://github.com/aspnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="c827e-155">計画された拡張機能により、サーバーの負荷をより適切に制御できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c827e-155">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="c827e-156">ASP.NET Core のその他のキャッシュテクノロジ</span><span class="sxs-lookup"><span data-stu-id="c827e-156">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="c827e-157">メモリ内キャッシュ</span><span class="sxs-lookup"><span data-stu-id="c827e-157">In-memory caching</span></span>

<span data-ttu-id="c827e-158">インメモリキャッシュは、キャッシュされたデータを格納するためにサーバーメモリを使用します。</span><span class="sxs-lookup"><span data-stu-id="c827e-158">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="c827e-159">この種のキャッシュは、1台のサーバーまたは*固定セッション*を使用している複数のサーバーに適しています。</span><span class="sxs-lookup"><span data-stu-id="c827e-159">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="c827e-160">固定セッションとは、クライアントからの要求が常に同じサーバーにルーティングされて処理されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="c827e-160">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="c827e-161">詳細については、「<xref:performance/caching/memory>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c827e-161">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="c827e-162">分散キャッシュ</span><span class="sxs-lookup"><span data-stu-id="c827e-162">Distributed Cache</span></span>

<span data-ttu-id="c827e-163">アプリがクラウドまたはサーバーファームでホストされている場合は、分散キャッシュを使用してデータをメモリに格納します。</span><span class="sxs-lookup"><span data-stu-id="c827e-163">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="c827e-164">キャッシュは、要求を処理するサーバー間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-164">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="c827e-165">クライアントのキャッシュデータが使用可能な場合、クライアントは、グループ内の任意のサーバーによって処理される要求を送信できます。</span><span class="sxs-lookup"><span data-stu-id="c827e-165">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="c827e-166">ASP.NET Core は SQL Server と Redis 分散キャッシュを提供します。</span><span class="sxs-lookup"><span data-stu-id="c827e-166">ASP.NET Core offers SQL Server and Redis distributed caches.</span></span>

<span data-ttu-id="c827e-167">詳細については、「<xref:performance/caching/distributed>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c827e-167">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="c827e-168">キャッシュタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="c827e-168">Cache Tag Helper</span></span>

<span data-ttu-id="c827e-169">キャッシュタグヘルパーを使用して、MVC ビューまたは Razor ページからコンテンツをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="c827e-169">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="c827e-170">キャッシュタグヘルパーは、メモリ内キャッシュを使用してデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="c827e-170">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="c827e-171">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c827e-171">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="c827e-172">分散キャッシュ タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="c827e-172">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="c827e-173">分散キャッシュタグヘルパーを使用して、分散型クラウドまたは web ファームのシナリオで、MVC ビューまたは Razor ページからコンテンツをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="c827e-173">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="c827e-174">分散キャッシュタグヘルパーは、SQL Server または Redis を使用してデータを格納します。</span><span class="sxs-lookup"><span data-stu-id="c827e-174">The Distributed Cache Tag Helper uses SQL Server or Redis to store data.</span></span>

<span data-ttu-id="c827e-175">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c827e-175">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="c827e-176">ResponseCache 属性</span><span class="sxs-lookup"><span data-stu-id="c827e-176">ResponseCache attribute</span></span>

<span data-ttu-id="c827e-177">@No__t-0 は、応答のキャッシュに適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="c827e-177">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="c827e-178">認証されたクライアントの情報が含まれているコンテンツのキャッシュを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c827e-178">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="c827e-179">キャッシュは、ユーザーの id またはユーザーがサインインしているかどうかによって変更されないコンテンツに対してのみ有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c827e-179">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="c827e-180"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> は、指定されたクエリキーのリストの値によって、格納されている応答を変化させることができます。</span><span class="sxs-lookup"><span data-stu-id="c827e-180"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="c827e-181">@No__t-0 の1つの値を指定した場合、ミドルウェアはすべての要求クエリ文字列パラメーターによって応答を変化させることになります。</span><span class="sxs-lookup"><span data-stu-id="c827e-181">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="c827e-182">@No__t-1 プロパティを設定するには、[応答キャッシュミドルウェア](xref:performance/caching/middleware)を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c827e-182">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="c827e-183">それ以外の場合は、ランタイム例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="c827e-183">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="c827e-184">@No__t-0 プロパティに対応する HTTP ヘッダーがありません。</span><span class="sxs-lookup"><span data-stu-id="c827e-184">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="c827e-185">プロパティは、応答キャッシュミドルウェアによって処理される HTTP 機能です。</span><span class="sxs-lookup"><span data-stu-id="c827e-185">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="c827e-186">ミドルウェアがキャッシュされた応答を提供するには、クエリ文字列とクエリ文字列の値が以前の要求と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="c827e-186">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="c827e-187">たとえば、次の表に示すような一連の要求と結果を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="c827e-187">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="c827e-188">要求</span><span class="sxs-lookup"><span data-stu-id="c827e-188">Request</span></span>                          | <span data-ttu-id="c827e-189">結果</span><span class="sxs-lookup"><span data-stu-id="c827e-189">Result</span></span>                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | <span data-ttu-id="c827e-190">サーバーから返されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-190">Returned from the server.</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="c827e-191">ミドルウェアから返されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-191">Returned from middleware.</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="c827e-192">サーバーから返されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-192">Returned from the server.</span></span> |

<span data-ttu-id="c827e-193">最初の要求はサーバーによって返され、ミドルウェアにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="c827e-193">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="c827e-194">2番目の要求は、クエリ文字列が前の要求と一致するため、ミドルウェアによって返されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-194">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="c827e-195">クエリ文字列値が以前の要求と一致しないので、3番目の要求がミドルウェアキャッシュにありません。</span><span class="sxs-lookup"><span data-stu-id="c827e-195">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="c827e-196">@No__t-0 は、`Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` を使用して (@no__t で) を構成および作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-196">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="c827e-197">@No__t-0 は、適切な HTTP ヘッダーと応答の機能を更新する作業を実行します。</span><span class="sxs-lookup"><span data-stu-id="c827e-197">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="c827e-198">フィルター:</span><span class="sxs-lookup"><span data-stu-id="c827e-198">The filter:</span></span>

* <span data-ttu-id="c827e-199">@No__t-0、`Cache-Control`、`Pragma` の既存のヘッダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="c827e-199">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="c827e-200">@No__t-0 に設定されているプロパティに基づいて、適切なヘッダーを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="c827e-200">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="c827e-201">@No__t-0 が設定されている場合は、応答キャッシュ HTTP 機能を更新します。</span><span class="sxs-lookup"><span data-stu-id="c827e-201">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="c827e-202">要因</span><span class="sxs-lookup"><span data-stu-id="c827e-202">Vary</span></span>

<span data-ttu-id="c827e-203">このヘッダーは、<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> プロパティが設定されている場合にのみ書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c827e-203">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="c827e-204">@No__t-0 プロパティの値に設定されたプロパティ。</span><span class="sxs-lookup"><span data-stu-id="c827e-204">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="c827e-205">次の例では、<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="c827e-205">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="c827e-206">サンプルアプリを使用して、ブラウザーのネットワークツールで応答ヘッダーを表示します。</span><span class="sxs-lookup"><span data-stu-id="c827e-206">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="c827e-207">次の応答ヘッダーは、Cache1 ページの応答と共に送信されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-207">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="c827e-208">NoStore と Location。なし</span><span class="sxs-lookup"><span data-stu-id="c827e-208">NoStore and Location.None</span></span>

<span data-ttu-id="c827e-209"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> は、他のほとんどのプロパティをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="c827e-209"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="c827e-210">このプロパティが `true` に設定されている場合、`Cache-Control` ヘッダーは `no-store` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-210">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="c827e-211">@No__t-0 が `None` に設定されている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c827e-211">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="c827e-212">`Cache-Control` が `no-store,no-cache` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-212">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="c827e-213">`Pragma` が `no-cache` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-213">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="c827e-214">@No__t-0 が-1 @no__t、<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> が `None`、`Cache-Control`、`Pragma` が `no-cache` に設定されている場合。</span><span class="sxs-lookup"><span data-stu-id="c827e-214">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="c827e-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> は、通常、エラーページの `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-215"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="c827e-216">サンプルアプリの Cache2 ページには、応答を格納しないようにクライアントに指示する応答ヘッダーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-216">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="c827e-217">このサンプルアプリでは、次のヘッダーを含む Cache2 ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-217">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="c827e-218">場所と期間</span><span class="sxs-lookup"><span data-stu-id="c827e-218">Location and Duration</span></span>

<span data-ttu-id="c827e-219">キャッシュを有効にするには、@no__t 0 を正の値に設定し、<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> は `Any` (既定値) または `Client` のいずれかである必要があります。</span><span class="sxs-lookup"><span data-stu-id="c827e-219">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="c827e-220">この場合、`Cache-Control` ヘッダーは、応答の location 値の後に `max-age` を設定します。</span><span class="sxs-lookup"><span data-stu-id="c827e-220">In this case, the `Cache-Control` header is set to the location value followed by the `max-age` of the response.</span></span>

> [!NOTE]
> <span data-ttu-id="c827e-221">`Any` @no__t と `Client` のオプションは、それぞれ `public` および `private` の `Cache-Control` ヘッダー値に変換されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-221"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="c827e-222">前述のように、<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> を `None` に設定すると、`Cache-Control` ヘッダーと `Pragma` ヘッダーの両方が `no-cache` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-222">As noted previously, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="c827e-223">次の例では、サンプルアプリの Cache3 ページモデルと、<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> を設定して生成されたヘッダーを示し、既定の <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 値をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="c827e-223">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="c827e-224">このサンプルアプリでは、次のヘッダーを含む Cache3 ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="c827e-224">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="c827e-225">キャッシュプロファイル</span><span class="sxs-lookup"><span data-stu-id="c827e-225">Cache profiles</span></span>

<span data-ttu-id="c827e-226">多くのコントローラーアクション属性に対して応答キャッシュ設定を複製するのではなく、`Startup.ConfigureServices` で MVC/Razor Pages を設定するときに、キャッシュプロファイルをオプションとして構成できます。</span><span class="sxs-lookup"><span data-stu-id="c827e-226">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c827e-227">参照されるキャッシュプロファイルで見つかった値は、<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> によって既定値として使用され、属性で指定されたプロパティによって上書きされます。</span><span class="sxs-lookup"><span data-stu-id="c827e-227">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="c827e-228">キャッシュプロファイルを設定します。</span><span class="sxs-lookup"><span data-stu-id="c827e-228">Set up a cache profile.</span></span> <span data-ttu-id="c827e-229">次の例は、サンプルアプリの @no__t 0 の30秒のキャッシュプロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="c827e-229">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="c827e-230">サンプルアプリの Cache4 ページモデルは、`Default30` キャッシュプロファイルを参照します。</span><span class="sxs-lookup"><span data-stu-id="c827e-230">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="c827e-231">@No__t-0 は、次のものに適用できます。</span><span class="sxs-lookup"><span data-stu-id="c827e-231">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* <span data-ttu-id="c827e-232">Razor ページハンドラー (クラス) &ndash; 属性をハンドラーメソッドに適用することはできません。</span><span class="sxs-lookup"><span data-stu-id="c827e-232">Razor Page handlers (classes) &ndash; Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="c827e-233">MVC コントローラー (クラス)。</span><span class="sxs-lookup"><span data-stu-id="c827e-233">MVC controllers (classes).</span></span>
* <span data-ttu-id="c827e-234">MVC アクション (メソッド) &ndash; メソッドレベルの属性は、クラスレベルの属性で指定された設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="c827e-234">MVC actions (methods) &ndash; Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="c827e-235">@No__t-0 キャッシュプロファイルによる Cache4 ページ応答に適用される結果のヘッダー:</span><span class="sxs-lookup"><span data-stu-id="c827e-235">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="c827e-236">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c827e-236">Additional resources</span></span>

* [<span data-ttu-id="c827e-237">キャッシュへの応答の格納</span><span class="sxs-lookup"><span data-stu-id="c827e-237">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="c827e-238">Cache-control</span><span class="sxs-lookup"><span data-stu-id="c827e-238">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
