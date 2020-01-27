---
title: ASP.NET Core での応答圧縮
author: guardrex
description: 応答圧縮と ASP.NET Core アプリで応答圧縮ミドルウェアを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/response-compression
ms.openlocfilehash: b8a84418a3258e9ac43b4eadd8564c0708590bce
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726966"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="b817c-103">ASP.NET Core での応答圧縮</span><span class="sxs-lookup"><span data-stu-id="b817c-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="b817c-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b817c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b817c-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b817c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b817c-106">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="b817c-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="b817c-107">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="b817c-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="b817c-108">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="b817c-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="b817c-109">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="b817c-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="b817c-110">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="b817c-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="b817c-111">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="b817c-112">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="b817c-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="b817c-113">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="b817c-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="b817c-114">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="b817c-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="b817c-115">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="b817c-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="b817c-116">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="b817c-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="b817c-117">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="b817c-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="b817c-118">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="b817c-118">Hosting directly on:</span></span>
  * <span data-ttu-id="b817c-119">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="b817c-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="b817c-120">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="b817c-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="b817c-121">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="b817c-121">Response compression</span></span>

<span data-ttu-id="b817c-122">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="b817c-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="b817c-123">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b817c-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="b817c-124">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="b817c-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="b817c-125">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="b817c-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="b817c-126">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="b817c-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="b817c-127">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="b817c-128">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に `Accept-Encoding` ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="b817c-129">サーバーは、圧縮されたコンテンツを送信するときに、圧縮された応答のエンコード方法に関する情報を `Content-Encoding` ヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="b817c-130">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="b817c-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="b817c-131">ヘッダー値の `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="b817c-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="b817c-132">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="b817c-132">Middleware Supported</span></span> | <span data-ttu-id="b817c-133">説明</span><span class="sxs-lookup"><span data-stu-id="b817c-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="b817c-134">可 (既定)</span><span class="sxs-lookup"><span data-stu-id="b817c-134">Yes (default)</span></span>        | [<span data-ttu-id="b817c-135">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="b817c-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="b817c-136">いいえ</span><span class="sxs-lookup"><span data-stu-id="b817c-136">No</span></span>                   | [<span data-ttu-id="b817c-137">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="b817c-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="b817c-138">いいえ</span><span class="sxs-lookup"><span data-stu-id="b817c-138">No</span></span>                   | [<span data-ttu-id="b817c-139">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="b817c-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="b817c-140">○</span><span class="sxs-lookup"><span data-stu-id="b817c-140">Yes</span></span>                  | [<span data-ttu-id="b817c-141">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="b817c-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="b817c-142">○</span><span class="sxs-lookup"><span data-stu-id="b817c-142">Yes</span></span>                  | <span data-ttu-id="b817c-143">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b817c-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="b817c-144">いいえ</span><span class="sxs-lookup"><span data-stu-id="b817c-144">No</span></span>                   | [<span data-ttu-id="b817c-145">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="b817c-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="b817c-146">○</span><span class="sxs-lookup"><span data-stu-id="b817c-146">Yes</span></span>                  | <span data-ttu-id="b817c-147">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="b817c-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="b817c-148">ヘッダー値の `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="b817c-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="b817c-149">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="b817c-149">Middleware Supported</span></span> | <span data-ttu-id="b817c-150">説明</span><span class="sxs-lookup"><span data-stu-id="b817c-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="b817c-151">いいえ</span><span class="sxs-lookup"><span data-stu-id="b817c-151">No</span></span>                   | [<span data-ttu-id="b817c-152">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="b817c-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="b817c-153">いいえ</span><span class="sxs-lookup"><span data-stu-id="b817c-153">No</span></span>                   | [<span data-ttu-id="b817c-154">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="b817c-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="b817c-155">いいえ</span><span class="sxs-lookup"><span data-stu-id="b817c-155">No</span></span>                   | [<span data-ttu-id="b817c-156">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="b817c-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="b817c-157">可 (既定)</span><span class="sxs-lookup"><span data-stu-id="b817c-157">Yes (default)</span></span>        | [<span data-ttu-id="b817c-158">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="b817c-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="b817c-159">○</span><span class="sxs-lookup"><span data-stu-id="b817c-159">Yes</span></span>                  | <span data-ttu-id="b817c-160">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b817c-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="b817c-161">いいえ</span><span class="sxs-lookup"><span data-stu-id="b817c-161">No</span></span>                   | [<span data-ttu-id="b817c-162">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="b817c-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="b817c-163">○</span><span class="sxs-lookup"><span data-stu-id="b817c-163">Yes</span></span>                  | <span data-ttu-id="b817c-164">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="b817c-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="b817c-165">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b817c-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="b817c-166">ミドルウェアを使用すると、カスタム `Accept-Encoding` ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="b817c-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="b817c-167">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b817c-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="b817c-168">ミドルウェアは、圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue、`q`) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="b817c-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="b817c-169">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b817c-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="b817c-170">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="b817c-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="b817c-171">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="b817c-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="b817c-172">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="b817c-173">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="b817c-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="b817c-174">Header</span><span class="sxs-lookup"><span data-stu-id="b817c-174">Header</span></span>             | <span data-ttu-id="b817c-175">役割</span><span class="sxs-lookup"><span data-stu-id="b817c-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="b817c-176">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="b817c-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="b817c-177">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="b817c-178">圧縮が発生すると、応答が圧縮されるときに本文の内容が変更されるため、`Content-Length` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="b817c-179">圧縮が行われると、本文の内容が変更され、ハッシュが有効でなくなったため、`Content-MD5` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="b817c-180">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="b817c-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="b817c-181">すべての応答は、その `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="b817c-182">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="b817c-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="b817c-183">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="b817c-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="b817c-184">`Accept-Encoding` の値を持つサーバーからクライアントとプロキシに送信された場合、`Vary` ヘッダーは、要求の `Accept-Encoding` ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="b817c-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="b817c-185">`Vary: Accept-Encoding` ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="b817c-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="b817c-186">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="b817c-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="b817c-187">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="b817c-187">The sample illustrates:</span></span>

* <span data-ttu-id="b817c-188">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="b817c-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="b817c-189">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b817c-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="b817c-190">[パッケージ]</span><span class="sxs-lookup"><span data-stu-id="b817c-190">Package</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b817c-191">応答圧縮ミドルウェアは、ASP.NET Core アプリに暗黙的に含まれる [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-191">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b817c-192">ミドルウェアをプロジェクトに含めるには、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b817c-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="b817c-193">の構成</span><span class="sxs-lookup"><span data-stu-id="b817c-193">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b817c-194">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="b817c-194">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b817c-195">次のコードは、既定の MIME の種類と[Gzip 圧縮プロバイダー](#gzip-compression-provider)の応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="b817c-195">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

::: moniker-end

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

<span data-ttu-id="b817c-196">注:</span><span class="sxs-lookup"><span data-stu-id="b817c-196">Notes:</span></span>

* <span data-ttu-id="b817c-197">応答を圧縮するミドルウェアの前に `app.UseResponseCompression` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-197">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="b817c-198">詳細については、「 <xref:fundamentals/middleware/index#middleware-order>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b817c-198">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="b817c-199">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="b817c-199">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="b817c-200">`Accept-Encoding` ヘッダーを使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b817c-200">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="b817c-201">`Content-Encoding` ヘッダーと `Vary` ヘッダーが応答に存在しません。</span><span class="sxs-lookup"><span data-stu-id="b817c-201">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b817c-204">`Accept-Encoding: br` ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b817c-204">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="b817c-205">応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="b817c-205">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b817c-209">`Accept-Encoding: gzip` ヘッダーを使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b817c-209">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="b817c-210">応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="b817c-210">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと gzip の値で表示されます。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="b817c-214">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="b817c-214">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="b817c-215">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="b817c-215">Brotli Compression Provider</span></span>

<span data-ttu-id="b817c-216">[Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> を使用します。</span><span class="sxs-lookup"><span data-stu-id="b817c-216">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="b817c-217"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b817c-217">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="b817c-218">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-218">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="b817c-219">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="b817c-219">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="b817c-220">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="b817c-220">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="b817c-221">圧縮プロバイダーが明示的に追加されている場合は、Brotoli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-221">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b817c-222">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="b817c-222">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="b817c-223">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-223">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="b817c-224">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="b817c-224">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="b817c-225">圧縮レベル</span><span class="sxs-lookup"><span data-stu-id="b817c-225">Compression Level</span></span> | <span data-ttu-id="b817c-226">説明</span><span class="sxs-lookup"><span data-stu-id="b817c-226">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="b817c-227">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="b817c-227">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="b817c-228">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-228">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="b817c-229">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="b817c-229">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="b817c-230">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="b817c-230">No compression should be performed.</span></span> |
| [<span data-ttu-id="b817c-231">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="b817c-231">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="b817c-232">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-232">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

::: moniker-end

### <a name="gzip-compression-provider"></a><span data-ttu-id="b817c-233">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="b817c-233">Gzip Compression Provider</span></span>

<span data-ttu-id="b817c-234"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> を使用して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="b817c-234">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="b817c-235"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b817c-235">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b817c-236">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-236">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="b817c-237">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="b817c-237">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="b817c-238">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="b817c-238">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="b817c-239">Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-239">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="b817c-240">クライアントで Gzip 圧縮がサポートされている場合、圧縮の既定値は Gzip です。</span><span class="sxs-lookup"><span data-stu-id="b817c-240">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="b817c-241">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-241">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

<span data-ttu-id="b817c-242">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="b817c-242">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="b817c-243">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-243">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="b817c-244">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="b817c-244">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="b817c-245">圧縮レベル</span><span class="sxs-lookup"><span data-stu-id="b817c-245">Compression Level</span></span> | <span data-ttu-id="b817c-246">説明</span><span class="sxs-lookup"><span data-stu-id="b817c-246">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="b817c-247">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="b817c-247">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="b817c-248">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-248">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="b817c-249">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="b817c-249">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="b817c-250">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="b817c-250">No compression should be performed.</span></span> |
| [<span data-ttu-id="b817c-251">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="b817c-251">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="b817c-252">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-252">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a><span data-ttu-id="b817c-253">カスタムプロバイダー</span><span class="sxs-lookup"><span data-stu-id="b817c-253">Custom providers</span></span>

<span data-ttu-id="b817c-254"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>を使用して、カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="b817c-254">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="b817c-255">この `ICompressionProvider` が生成するコンテンツエンコーディングを表す <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>。</span><span class="sxs-lookup"><span data-stu-id="b817c-255">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="b817c-256">ミドルウェアは、この情報を使用して、要求の `Accept-Encoding` ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="b817c-256">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="b817c-257">このサンプルアプリを使用すると、クライアントは `Accept-Encoding: mycustomcompression` ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="b817c-257">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="b817c-258">ミドルウェアは、カスタム圧縮実装を使用して、`Content-Encoding: mycustomcompression` ヘッダーで応答を返します。</span><span class="sxs-lookup"><span data-stu-id="b817c-258">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="b817c-259">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-259">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b817c-260">`Accept-Encoding: mycustomcompression` ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="b817c-260">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="b817c-261">応答には、`Vary` ヘッダーと `Content-Encoding` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="b817c-261">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="b817c-262">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="b817c-262">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="b817c-263">このサンプルの `CustomCompressionProvider` クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="b817c-263">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="b817c-264">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="b817c-264">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="b817c-267">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="b817c-267">MIME types</span></span>

<span data-ttu-id="b817c-268">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="b817c-268">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="b817c-269">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="b817c-269">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="b817c-270">`text/*` などのワイルドカード MIME の種類はサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="b817c-270">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="b817c-271">このサンプルアプリでは、`image/svg+xml` 用の MIME の種類を追加し、ASP.NET Core バナー画像 (*横断幕*) を圧縮して機能させることができます。</span><span class="sxs-lookup"><span data-stu-id="b817c-271">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="b817c-272">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="b817c-272">Compression with secure protocol</span></span>

<span data-ttu-id="b817c-273">セキュリティで保護された接続での圧縮された応答は、`EnableForHttps` オプションを使用して制御できます。これは既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="b817c-273">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="b817c-274">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-274">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="b817c-275">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="b817c-275">Adding the Vary header</span></span>

<span data-ttu-id="b817c-276">`Accept-Encoding` ヘッダーに基づいて応答を圧縮する場合は、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-276">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="b817c-277">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、`Vary` ヘッダーが `Accept-Encoding` 値と共に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-277">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="b817c-278">ASP.NET Core 2.0 以降では、応答が圧縮されるときに、ミドルウェアによって `Vary` ヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-278">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="b817c-279">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="b817c-279">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="b817c-280">要求が Nginx によってプロキシされると、`Accept-Encoding` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-280">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="b817c-281">`Accept-Encoding` ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="b817c-281">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="b817c-282">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b817c-282">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="b817c-283">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="b817c-283">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="b817c-284">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="b817c-284">Working with IIS dynamic compression</span></span>

<span data-ttu-id="b817c-285">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="b817c-285">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="b817c-286">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b817c-286">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b817c-287">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="b817c-287">Troubleshooting</span></span>

<span data-ttu-id="b817c-288">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して、`Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="b817c-288">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="b817c-289">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="b817c-289">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="b817c-290">`Accept-Encoding` ヘッダーには、`br`、`gzip`、`*`、または独自に作成したカスタム圧縮プロバイダーに一致するカスタムエンコーディングの値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b817c-290">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="b817c-291">値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b817c-291">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="b817c-292">MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-292">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="b817c-293">要求に `Content-Range` ヘッダーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="b817c-293">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="b817c-294">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-294">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="b817c-295">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="b817c-295">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="b817c-296">`Accept-Encoding` ヘッダーには、`gzip`、`*`、またはユーザーが設定したカスタム圧縮プロバイダーに一致するカスタムエンコードの値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b817c-296">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="b817c-297">値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b817c-297">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="b817c-298">MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-298">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="b817c-299">要求に `Content-Range` ヘッダーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="b817c-299">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="b817c-300">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b817c-300">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="b817c-301">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="b817c-301">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b817c-302">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b817c-302">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="b817c-303">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="b817c-303">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="b817c-304">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="b817c-304">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="b817c-305">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="b817c-305">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="b817c-306">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="b817c-306">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)
