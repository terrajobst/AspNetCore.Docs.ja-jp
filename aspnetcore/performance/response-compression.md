---
title: ASP.NET Core で応答の圧縮
author: guardrex
description: 応答圧縮と ASP.NET Core アプリで応答圧縮ミドルウェアを使用する方法について説明します。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/response-compression
ms.openlocfilehash: d5d2da3dc0a8a452de97d98161d429389d2f7638
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815616"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="5117e-103">ASP.NET Core で応答の圧縮</span><span class="sxs-lookup"><span data-stu-id="5117e-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="5117e-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5117e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5117e-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5117e-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5117e-106">ネットワーク帯域幅は、限られたリソースです。</span><span class="sxs-lookup"><span data-stu-id="5117e-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="5117e-107">通常、応答のサイズを小さく、アプリの応答性を多くの場合、大幅に増加します。</span><span class="sxs-lookup"><span data-stu-id="5117e-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="5117e-108">ペイロードのサイズを小さく 1 つの方法は、アプリの応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="5117e-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="5117e-109">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="5117e-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="5117e-110">IIS、Apache、Nginx でサーバー ベースの応答の圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="5117e-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="5117e-111">ミドルウェアのパフォーマンス一致しないことのサーバー モジュール。</span><span class="sxs-lookup"><span data-stu-id="5117e-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="5117e-112">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[Kestrel](xref:fundamentals/servers/kestrel)サーバーは、組み込みの圧縮のサポートを提供現在はありません。</span><span class="sxs-lookup"><span data-stu-id="5117e-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="5117e-113">したら応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="5117e-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="5117e-114">次のサーバー ベースの圧縮テクノロジを使用することができません。</span><span class="sxs-lookup"><span data-stu-id="5117e-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="5117e-115">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="5117e-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="5117e-116">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="5117e-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="5117e-117">Nginx の圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="5117e-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="5117e-118">直接のホスティング。</span><span class="sxs-lookup"><span data-stu-id="5117e-118">Hosting directly on:</span></span>
  * <span data-ttu-id="5117e-119">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="5117e-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="5117e-120">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="5117e-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="5117e-121">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="5117e-121">Response compression</span></span>

<span data-ttu-id="5117e-122">通常は、ネイティブに圧縮された応答は、応答の圧縮を利用できます。</span><span class="sxs-lookup"><span data-stu-id="5117e-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="5117e-123">ネイティブに通常は圧縮された応答は次のとおりです。CSS、JavaScript、HTML、XML、および JSON。</span><span class="sxs-lookup"><span data-stu-id="5117e-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="5117e-124">PNG ファイルなどのネイティブ圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="5117e-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="5117e-125">場合にさらに、ネイティブ圧縮された応答を圧縮しようとすると、サイズと転送時間の小さなそれ以上は低下は可能性がありますする影が薄くなって、圧縮処理にかかった時間によって。</span><span class="sxs-lookup"><span data-stu-id="5117e-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="5117e-126">(ファイルの内容および圧縮の効率) に応じて約 150 ~ 1000 バイトよりも小さいファイルを圧縮されていません。</span><span class="sxs-lookup"><span data-stu-id="5117e-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="5117e-127">圧縮されたファイルのサイズが圧縮されていないファイルよりも小さいファイルの圧縮のオーバーヘッドがあります。</span><span class="sxs-lookup"><span data-stu-id="5117e-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="5117e-128">クライアントが送信することによってその機能のサーバーに通知する必要があります、クライアントは、圧縮されたコンテンツを処理できますが、ときに、`Accept-Encoding`ヘッダーを要求します。</span><span class="sxs-lookup"><span data-stu-id="5117e-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="5117e-129">内の情報を含める必要があります、サーバーは、圧縮されたコンテンツを送信するとき、`Content-Encoding`ヘッダーを圧縮された応答をエンコードする方法。</span><span class="sxs-lookup"><span data-stu-id="5117e-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="5117e-130">ミドルウェアによってサポートされているコンテンツのエンコードの指定は、次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="5117e-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="5117e-131">`Accept-Encoding` ヘッダーの値</span><span class="sxs-lookup"><span data-stu-id="5117e-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="5117e-132">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="5117e-132">Middleware Supported</span></span> | <span data-ttu-id="5117e-133">説明</span><span class="sxs-lookup"><span data-stu-id="5117e-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="5117e-134">[はい] (既定値)</span><span class="sxs-lookup"><span data-stu-id="5117e-134">Yes (default)</span></span>        | [<span data-ttu-id="5117e-135">Brotli 圧縮データの形式</span><span class="sxs-lookup"><span data-stu-id="5117e-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="5117e-136">いいえ</span><span class="sxs-lookup"><span data-stu-id="5117e-136">No</span></span>                   | [<span data-ttu-id="5117e-137">DEFLATE 圧縮データの形式</span><span class="sxs-lookup"><span data-stu-id="5117e-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="5117e-138">いいえ</span><span class="sxs-lookup"><span data-stu-id="5117e-138">No</span></span>                   | [<span data-ttu-id="5117e-139">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="5117e-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="5117e-140">[はい]</span><span class="sxs-lookup"><span data-stu-id="5117e-140">Yes</span></span>                  | [<span data-ttu-id="5117e-141">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="5117e-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="5117e-142">[はい]</span><span class="sxs-lookup"><span data-stu-id="5117e-142">Yes</span></span>                  | <span data-ttu-id="5117e-143">「エンコードなし」の識別子:応答をエンコードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5117e-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="5117e-144">いいえ</span><span class="sxs-lookup"><span data-stu-id="5117e-144">No</span></span>                   | [<span data-ttu-id="5117e-145">Java アーカイブのネットワーク転送の形式</span><span class="sxs-lookup"><span data-stu-id="5117e-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="5117e-146">[はい]</span><span class="sxs-lookup"><span data-stu-id="5117e-146">Yes</span></span>                  | <span data-ttu-id="5117e-147">明示的に要求されたエンコードは、利用可能なコンテンツ</span><span class="sxs-lookup"><span data-stu-id="5117e-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="5117e-148">`Accept-Encoding` ヘッダーの値</span><span class="sxs-lookup"><span data-stu-id="5117e-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="5117e-149">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="5117e-149">Middleware Supported</span></span> | <span data-ttu-id="5117e-150">説明</span><span class="sxs-lookup"><span data-stu-id="5117e-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="5117e-151">いいえ</span><span class="sxs-lookup"><span data-stu-id="5117e-151">No</span></span>                   | [<span data-ttu-id="5117e-152">Brotli 圧縮データの形式</span><span class="sxs-lookup"><span data-stu-id="5117e-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="5117e-153">いいえ</span><span class="sxs-lookup"><span data-stu-id="5117e-153">No</span></span>                   | [<span data-ttu-id="5117e-154">DEFLATE 圧縮データの形式</span><span class="sxs-lookup"><span data-stu-id="5117e-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="5117e-155">いいえ</span><span class="sxs-lookup"><span data-stu-id="5117e-155">No</span></span>                   | [<span data-ttu-id="5117e-156">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="5117e-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="5117e-157">[はい] (既定値)</span><span class="sxs-lookup"><span data-stu-id="5117e-157">Yes (default)</span></span>        | [<span data-ttu-id="5117e-158">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="5117e-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="5117e-159">[はい]</span><span class="sxs-lookup"><span data-stu-id="5117e-159">Yes</span></span>                  | <span data-ttu-id="5117e-160">「エンコードなし」の識別子:応答をエンコードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5117e-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="5117e-161">いいえ</span><span class="sxs-lookup"><span data-stu-id="5117e-161">No</span></span>                   | [<span data-ttu-id="5117e-162">Java アーカイブのネットワーク転送の形式</span><span class="sxs-lookup"><span data-stu-id="5117e-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="5117e-163">[はい]</span><span class="sxs-lookup"><span data-stu-id="5117e-163">Yes</span></span>                  | <span data-ttu-id="5117e-164">明示的に要求されたエンコードは、利用可能なコンテンツ</span><span class="sxs-lookup"><span data-stu-id="5117e-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="5117e-165">詳細については、次を参照してください。、 [IANA 公式コンテンツ コーディング リスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="5117e-166">カスタムの圧縮は追加のプロバイダーを追加することができます、ミドルウェア`Accept-Encoding`ヘッダー値。</span><span class="sxs-lookup"><span data-stu-id="5117e-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="5117e-167">詳細については、次を参照してください。[カスタム プロバイダー](#custom-providers)以下。</span><span class="sxs-lookup"><span data-stu-id="5117e-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="5117e-168">ミドルウェアが品質の値に対応できる (qvalue、 `q`) 圧縮スキームを優先するクライアントによって送信されたときに重み付けします。</span><span class="sxs-lookup"><span data-stu-id="5117e-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="5117e-169">詳細については、次を参照してください。 [RFC 7231。Accept-encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="5117e-170">圧縮アルゴリズムは、圧縮速度と、圧縮の効果のトレードオフが適用されます。</span><span class="sxs-lookup"><span data-stu-id="5117e-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="5117e-171">*有効性*このコンテキストの圧縮後の出力のサイズを指します。</span><span class="sxs-lookup"><span data-stu-id="5117e-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="5117e-172">最小サイズは、ほとんどによって実現*最適な*圧縮します。</span><span class="sxs-lookup"><span data-stu-id="5117e-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="5117e-173">要求に関連する、ヘッダーを送信する、キャッシュ、および圧縮されたコンテンツを受信は、次の表で説明されています。</span><span class="sxs-lookup"><span data-stu-id="5117e-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="5117e-174">Header</span><span class="sxs-lookup"><span data-stu-id="5117e-174">Header</span></span>             | <span data-ttu-id="5117e-175">ロール</span><span class="sxs-lookup"><span data-stu-id="5117e-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="5117e-176">エンコードをクライアントに許容される設定内容を示すクライアントからサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="5117e-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="5117e-177">ペイロード内のコンテンツのエンコードを示したりする、サーバーからクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="5117e-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="5117e-178">圧縮が発生したときに、`Content-Length`以降本文内容の変更、応答を圧縮するときに、ヘッダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="5117e-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="5117e-179">圧縮が発生したときに、`Content-MD5`ヘッダーが削除されたため、本文の内容が変更され、ハッシュが無効になっています。</span><span class="sxs-lookup"><span data-stu-id="5117e-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="5117e-180">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="5117e-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="5117e-181">すべての応答を指定する必要があります、`Content-Type`します。</span><span class="sxs-lookup"><span data-stu-id="5117e-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="5117e-182">ミドルウェアは、応答を圧縮するかどうかを判断するには、この値を確認します。</span><span class="sxs-lookup"><span data-stu-id="5117e-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="5117e-183">ミドルウェアのセットを指定する[既定の MIME の種類](#mime-types)をエンコードできますが、置き換えるか、MIME の種類を追加することができます。</span><span class="sxs-lookup"><span data-stu-id="5117e-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="5117e-184">値を使用して、サーバーによって送信されると`Accept-Encoding`クライアントと、プロキシ、`Vary`ヘッダーは、クライアントまたはキャッシュするプロキシすることを示します (異なる) の値に基づいて応答、`Accept-Encoding`要求のヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5117e-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="5117e-185">コンテンツを返すことの結果、`Vary: Accept-Encoding`ヘッダーが両方の圧縮を個別に圧縮されていない応答がキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="5117e-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="5117e-186">応答圧縮ミドルウェアの機能を試し、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="5117e-187">このサンプルを示しています。</span><span class="sxs-lookup"><span data-stu-id="5117e-187">The sample illustrates:</span></span>

* <span data-ttu-id="5117e-188">Gzip とカスタム圧縮プロバイダーを使用してアプリの応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="5117e-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="5117e-189">MIME の種類を圧縮する MIME の種類の既定の一覧に追加する方法。</span><span class="sxs-lookup"><span data-stu-id="5117e-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="5117e-190">Package</span><span class="sxs-lookup"><span data-stu-id="5117e-190">Package</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5117e-191">ミドルウェアをプロジェクトに含めるにへの参照を追加、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)が含まれています、 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="5117e-191">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5117e-192">ミドルウェアをプロジェクトに含めるにへの参照を追加、 [Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)が含まれています、 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="5117e-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="5117e-193">ミドルウェアをプロジェクトに含めるにへの参照を追加、 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="5117e-193">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="5117e-194">構成</span><span class="sxs-lookup"><span data-stu-id="5117e-194">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5117e-195">次のコードは、既定の MIME タイプと圧縮のプロバイダーの応答圧縮ミドルウェアを有効にする方法を示します ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider))。</span><span class="sxs-lookup"><span data-stu-id="5117e-195">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5117e-196">次のコードは、既定の MIME の種類の応答圧縮ミドルウェアを有効にする方法を示していますおよび[Gzip 圧縮プロバイダー](#gzip-compression-provider):。</span><span class="sxs-lookup"><span data-stu-id="5117e-196">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="5117e-197">メモ:</span><span class="sxs-lookup"><span data-stu-id="5117e-197">Notes:</span></span>

* <span data-ttu-id="5117e-198">`app.UseResponseCompression` 前に呼び出す必要があります`app.UseMvc`します。</span><span class="sxs-lookup"><span data-stu-id="5117e-198">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="5117e-199">などのツールを使用して[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)を設定する、`Accept-Encoding`要求ヘッダーおよび応答ヘッダー、サイズ、および本文を検討します。</span><span class="sxs-lookup"><span data-stu-id="5117e-199">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="5117e-200">なし、サンプル アプリに要求を送信、`Accept-Encoding`ヘッダーおよび応答の圧縮がないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5117e-200">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="5117e-201">`Content-Encoding`と`Vary`ヘッダーが応答に存在しません。</span><span class="sxs-lookup"><span data-stu-id="5117e-201">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler のウィンドウが Accept-encoding ヘッダーなしの要求の結果を表示します。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5117e-204">使ってサンプル アプリに要求を送信、`Accept-Encoding: br`ヘッダー (Brotli 圧縮) し、応答を圧縮することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5117e-204">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="5117e-205">`Content-Encoding`と`Vary`ヘッダーが応答に存在します。</span><span class="sxs-lookup"><span data-stu-id="5117e-205">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Accept-encoding ヘッダーで要求の結果と br の値を示す fiddler ウィンドウ。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5117e-209">使ってサンプル アプリに要求を送信、`Accept-Encoding: gzip`ヘッダー応答を圧縮することを確認します。</span><span class="sxs-lookup"><span data-stu-id="5117e-209">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="5117e-210">`Content-Encoding`と`Vary`ヘッダーが応答に存在します。</span><span class="sxs-lookup"><span data-stu-id="5117e-210">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Accept-encoding ヘッダーで要求の結果と gzip の値を示す fiddler ウィンドウ。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="5117e-214">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5117e-214">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="5117e-215">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5117e-215">Brotli Compression Provider</span></span>

<span data-ttu-id="5117e-216">使用して、<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>で応答を圧縮する、 [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-216">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="5117e-217">圧縮のプロバイダーが明示的に追加されていない場合に、 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="5117e-217">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="5117e-218">Brotli 圧縮プロバイダーは、既定で圧縮プロバイダーと共にの配列に追加されます、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-218">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="5117e-219">圧縮は、クライアントが Brotli 圧縮データの形式がサポートされている場合に、Brotli 圧縮既定値です。</span><span class="sxs-lookup"><span data-stu-id="5117e-219">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="5117e-220">Brotli がクライアントによってサポートされていない場合は、クライアントが Gzip 圧縮をサポートしている場合に、圧縮が Gzip に既定値です。</span><span class="sxs-lookup"><span data-stu-id="5117e-220">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="5117e-221">すべての圧縮のプロバイダーが明示的に追加されたときに、Brotoli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5117e-221">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="5117e-222">圧縮レベルを設定<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>します。</span><span class="sxs-lookup"><span data-stu-id="5117e-222">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="5117e-223">Brotli 圧縮プロバイダーの既定値は、最速の圧縮レベル ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))、最も効率的な圧縮を生成する可能性がありますされません。</span><span class="sxs-lookup"><span data-stu-id="5117e-223">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="5117e-224">最も効率的な圧縮が必要な場合は、最適に圧縮するためのミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="5117e-224">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="5117e-225">圧縮レベル</span><span class="sxs-lookup"><span data-stu-id="5117e-225">Compression Level</span></span> | <span data-ttu-id="5117e-226">説明</span><span class="sxs-lookup"><span data-stu-id="5117e-226">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="5117e-227">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="5117e-227">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="5117e-228">圧縮は、結果の出力が最適に圧縮されていない場合でも、できるだけ早く完了します。</span><span class="sxs-lookup"><span data-stu-id="5117e-228">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="5117e-229">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="5117e-229">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="5117e-230">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="5117e-230">No compression should be performed.</span></span> |
| [<span data-ttu-id="5117e-231">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="5117e-231">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="5117e-232">応答する必要がありますが、最適な圧縮、圧縮を完了する時間がかかる場合でもです。</span><span class="sxs-lookup"><span data-stu-id="5117e-232">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="5117e-233">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5117e-233">Gzip Compression Provider</span></span>

<span data-ttu-id="5117e-234">使用して、<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>で応答を圧縮する、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-234">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="5117e-235">圧縮のプロバイダーが明示的に追加されていない場合に、 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span><span class="sxs-lookup"><span data-stu-id="5117e-235">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5117e-236">Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーと共にの配列に追加されます、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-236">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="5117e-237">圧縮は、クライアントが Brotli 圧縮データの形式がサポートされている場合に、Brotli 圧縮既定値です。</span><span class="sxs-lookup"><span data-stu-id="5117e-237">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="5117e-238">Brotli がクライアントによってサポートされていない場合は、クライアントが Gzip 圧縮をサポートしている場合に、圧縮が Gzip に既定値です。</span><span class="sxs-lookup"><span data-stu-id="5117e-238">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5117e-239">Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5117e-239">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="5117e-240">圧縮は、クライアントが Gzip 圧縮をサポートしている場合に、Gzip 既定値です。</span><span class="sxs-lookup"><span data-stu-id="5117e-240">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="5117e-241">圧縮のプロバイダーが明示的に追加されたときに、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5117e-241">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

<span data-ttu-id="5117e-242">圧縮レベルを設定<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>します。</span><span class="sxs-lookup"><span data-stu-id="5117e-242">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="5117e-243">Gzip 圧縮プロバイダーの既定値は、最速の圧縮レベル ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))、最も効率的な圧縮を生成する可能性がありますされません。</span><span class="sxs-lookup"><span data-stu-id="5117e-243">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="5117e-244">最も効率的な圧縮が必要な場合は、最適に圧縮するためのミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="5117e-244">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="5117e-245">圧縮レベル</span><span class="sxs-lookup"><span data-stu-id="5117e-245">Compression Level</span></span> | <span data-ttu-id="5117e-246">説明</span><span class="sxs-lookup"><span data-stu-id="5117e-246">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="5117e-247">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="5117e-247">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="5117e-248">圧縮は、結果の出力が最適に圧縮されていない場合でも、できるだけ早く完了します。</span><span class="sxs-lookup"><span data-stu-id="5117e-248">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="5117e-249">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="5117e-249">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="5117e-250">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="5117e-250">No compression should be performed.</span></span> |
| [<span data-ttu-id="5117e-251">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="5117e-251">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="5117e-252">応答する必要がありますが、最適な圧縮、圧縮を完了する時間がかかる場合でもです。</span><span class="sxs-lookup"><span data-stu-id="5117e-252">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="5117e-253">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5117e-253">Custom providers</span></span>

<span data-ttu-id="5117e-254">カスタムの圧縮の実装を作成<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>です。</span><span class="sxs-lookup"><span data-stu-id="5117e-254">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="5117e-255"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>このエンコーディングの内容を表す`ICompressionProvider`が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5117e-255">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="5117e-256">ミドルウェアでは、この情報を使用して、指定されている一覧に基づいてプロバイダーを選択して、`Accept-Encoding`要求のヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5117e-256">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="5117e-257">サンプル アプリを使用して、クライアント要求を送信すると、`Accept-Encoding: mycustomcompression`ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5117e-257">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="5117e-258">ミドルウェアがカスタム圧縮の実装を使用し、と共に応答を返す、`Content-Encoding: mycustomcompression`ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5117e-258">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="5117e-259">クライアントは、カスタムの圧縮の実装が機能するためにはカスタム エンコーディングを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="5117e-259">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5117e-260">使ってサンプル アプリに要求を送信、`Accept-Encoding: mycustomcompression`ヘッダーおよび応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="5117e-260">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="5117e-261">`Vary`と`Content-Encoding`ヘッダーが応答に存在します。</span><span class="sxs-lookup"><span data-stu-id="5117e-261">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="5117e-262">(非表示)、応答本文は、このサンプルで圧縮します。</span><span class="sxs-lookup"><span data-stu-id="5117e-262">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="5117e-263">圧縮の実装ではありません、`CustomCompressionProvider`サンプルのクラス。</span><span class="sxs-lookup"><span data-stu-id="5117e-263">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="5117e-264">ただし、このような圧縮アルゴリズムを実装するかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="5117e-264">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Accept-encoding ヘッダーで要求の結果と mycustomcompression の値を示す fiddler ウィンドウ。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="5117e-267">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="5117e-267">MIME types</span></span>

<span data-ttu-id="5117e-268">ミドルウェアは、圧縮の MIME の種類の既定のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="5117e-268">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="5117e-269">置換または応答圧縮ミドルウェアのオプションで MIME の種類を追加します。</span><span class="sxs-lookup"><span data-stu-id="5117e-269">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="5117e-270">そのワイルドカードの MIME のメモなどの型`text/*`はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="5117e-270">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="5117e-271">サンプル アプリは、MIME の種類を追加します。`image/svg+xml`圧縮し、ASP.NET Core のバナー イメージを提供し、(*banner.svg*)。</span><span class="sxs-lookup"><span data-stu-id="5117e-271">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="5117e-272">セキュリティで保護されたプロトコルと圧縮</span><span class="sxs-lookup"><span data-stu-id="5117e-272">Compression with secure protocol</span></span>

<span data-ttu-id="5117e-273">セキュリティで保護された接続経由で圧縮された応答を制御することができます、`EnableForHttps`オプションは、既定で無効にします。</span><span class="sxs-lookup"><span data-stu-id="5117e-273">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="5117e-274">動的に生成されたページで圧縮を使用する可能性がセキュリティ上の問題など、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))と[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃です。</span><span class="sxs-lookup"><span data-stu-id="5117e-274">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="5117e-275">Vary ヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="5117e-275">Adding the Vary header</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="5117e-276">に基づいて応答を圧縮する場合、`Accept-Encoding`ヘッダー、可能性のある応答の複数の圧縮されたバージョンと、圧縮されていないバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="5117e-276">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="5117e-277">複数のバージョンが存在しを保存するか、クライアントおよびプロキシのキャッシュを指示するために、`Vary`でヘッダーを追加、`Accept-Encoding`値。</span><span class="sxs-lookup"><span data-stu-id="5117e-277">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="5117e-278">ASP.NET Core 2.0 以降では、ミドルウェアを追加、`Vary`応答を圧縮するときに自動的にヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5117e-278">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="5117e-279">に基づいて応答を圧縮する場合、`Accept-Encoding`ヘッダー、可能性のある応答の複数の圧縮されたバージョンと、圧縮されていないバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="5117e-279">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="5117e-280">複数のバージョンが存在しを保存するか、クライアントおよびプロキシのキャッシュを指示するために、`Vary`でヘッダーを追加、`Accept-Encoding`値。</span><span class="sxs-lookup"><span data-stu-id="5117e-280">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="5117e-281">ASP.NET core 1.x では、追加、`Vary`を応答にヘッダーを手動で行います。</span><span class="sxs-lookup"><span data-stu-id="5117e-281">In ASP.NET Core 1.x, adding the `Vary` header to the response is accomplished manually:</span></span>

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="5117e-282">Nginx のリバース プロキシの背後にあるときにミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="5117e-282">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="5117e-283">要求が、Nginx によってプロキシの場合、`Accept-Encoding`ヘッダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="5117e-283">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="5117e-284">削除、`Accept-Encoding`ヘッダーが応答を圧縮すると、ミドルウェアを防止します。</span><span class="sxs-lookup"><span data-stu-id="5117e-284">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="5117e-285">詳細については、「[NGINX:圧縮と圧縮解除](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-285">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="5117e-286">この問題を追跡する[Nginx のパススルー圧縮図 (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123)します。</span><span class="sxs-lookup"><span data-stu-id="5117e-286">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="5117e-287">IIS 動的圧縮を使用します。</span><span class="sxs-lookup"><span data-stu-id="5117e-287">Working with IIS dynamic compression</span></span>

<span data-ttu-id="5117e-288">モジュールの追加とを無効にする、アクティブな IIS 動的圧縮モジュールにアプリを無効にするサーバー レベルで構成した場合、 *web.config*ファイル。</span><span class="sxs-lookup"><span data-stu-id="5117e-288">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="5117e-289">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5117e-289">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5117e-290">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="5117e-290">Troubleshooting</span></span>

<span data-ttu-id="5117e-291">などのツールを使用して、 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)、設定できます、`Accept-Encoding`要求ヘッダーおよび応答ヘッダー、サイズ、および本文を検討します。</span><span class="sxs-lookup"><span data-stu-id="5117e-291">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="5117e-292">既定では、応答圧縮ミドルウェアは、次の条件を満たしている応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="5117e-292">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="5117e-293">`Accept-Encoding`の値を持つヘッダーがある`br`、 `gzip`、 `*`、または確立したカスタム圧縮プロバイダーに一致するカスタム エンコードします。</span><span class="sxs-lookup"><span data-stu-id="5117e-293">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="5117e-294">値がなければなりません`identity`または品質の値が (qvalue、 `q`) を 0 (ゼロ) を設定します。</span><span class="sxs-lookup"><span data-stu-id="5117e-294">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="5117e-295">MIME の種類 (`Content-Type`) 設定する必要がありで構成されている MIME の種類に一致する必要があります、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>します。</span><span class="sxs-lookup"><span data-stu-id="5117e-295">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="5117e-296">要求が含めることはできません、`Content-Range`ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5117e-296">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="5117e-297">セキュリティで保護されたプロトコル (https) が応答圧縮ミドルウェアのオプションで構成されていない場合、要求は安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5117e-297">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="5117e-298">*危険性に注意してください[上記で説明した](#compression-with-secure-protocol)セキュリティで保護されたコンテンツの圧縮を有効にする場合。*</span><span class="sxs-lookup"><span data-stu-id="5117e-298">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="5117e-299">`Accept-Encoding`の値を持つヘッダーがある`gzip`、 `*`、または確立したカスタム圧縮プロバイダーに一致するカスタム エンコードします。</span><span class="sxs-lookup"><span data-stu-id="5117e-299">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="5117e-300">値がなければなりません`identity`または品質の値が (qvalue、 `q`) を 0 (ゼロ) を設定します。</span><span class="sxs-lookup"><span data-stu-id="5117e-300">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="5117e-301">MIME の種類 (`Content-Type`) 設定する必要がありで構成されている MIME の種類に一致する必要があります、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>します。</span><span class="sxs-lookup"><span data-stu-id="5117e-301">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="5117e-302">要求が含めることはできません、`Content-Range`ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5117e-302">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="5117e-303">セキュリティで保護されたプロトコル (https) が応答圧縮ミドルウェアのオプションで構成されていない場合、要求は安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5117e-303">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="5117e-304">*危険性に注意してください[上記で説明した](#compression-with-secure-protocol)セキュリティで保護されたコンテンツの圧縮を有効にする場合。*</span><span class="sxs-lookup"><span data-stu-id="5117e-304">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5117e-305">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5117e-305">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="5117e-306">Mozilla 開発者ネットワーク:Accept-encoding</span><span class="sxs-lookup"><span data-stu-id="5117e-306">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="5117e-307">RFC 7231 セクション 3.1.2.1:コンテンツのコーディング</span><span class="sxs-lookup"><span data-stu-id="5117e-307">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="5117e-308">RFC 7230 セクション 4.2.3:Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="5117e-308">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="5117e-309">GZIP ファイル形式の仕様バージョン 4.3</span><span class="sxs-lookup"><span data-stu-id="5117e-309">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)
