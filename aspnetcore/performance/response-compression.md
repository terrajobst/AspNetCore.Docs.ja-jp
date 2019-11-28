---
title: ASP.NET Core での応答圧縮
author: guardrex
description: 応答圧縮と ASP.NET Core アプリで応答圧縮ミドルウェアを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/09/2019
uid: performance/response-compression
ms.openlocfilehash: e320e87179f9f1b9773a55c380684a3f3f712632
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993473"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="0f71f-103">ASP.NET Core での応答圧縮</span><span class="sxs-lookup"><span data-stu-id="0f71f-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="0f71f-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0f71f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0f71f-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0f71f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0f71f-106">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="0f71f-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="0f71f-107">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="0f71f-108">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="0f71f-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="0f71f-109">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="0f71f-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="0f71f-110">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="0f71f-111">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="0f71f-112">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="0f71f-113">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="0f71f-114">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="0f71f-115">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="0f71f-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="0f71f-116">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="0f71f-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="0f71f-117">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="0f71f-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="0f71f-118">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="0f71f-118">Hosting directly on:</span></span>
  * <span data-ttu-id="0f71f-119">Http.sys[サーバー](xref:fundamentals/servers/httpsys)(旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="0f71f-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="0f71f-120">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="0f71f-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="0f71f-121">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="0f71f-121">Response compression</span></span>

<span data-ttu-id="0f71f-122">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="0f71f-123">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、および JSON。</span><span class="sxs-lookup"><span data-stu-id="0f71f-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="0f71f-124">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="0f71f-125">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="0f71f-126">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="0f71f-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="0f71f-127">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="0f71f-128">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に`Accept-Encoding`ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="0f71f-129">サーバーは、圧縮されたコンテンツを送信するときに`Content-Encoding` 、圧縮された応答のエンコード方法に関する情報をヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="0f71f-130">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="0f71f-131">`Accept-Encoding`ヘッダー値</span><span class="sxs-lookup"><span data-stu-id="0f71f-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="0f71f-132">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="0f71f-132">Middleware Supported</span></span> | <span data-ttu-id="0f71f-133">説明</span><span class="sxs-lookup"><span data-stu-id="0f71f-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="0f71f-134">はい (既定値)</span><span class="sxs-lookup"><span data-stu-id="0f71f-134">Yes (default)</span></span>        | [<span data-ttu-id="0f71f-135">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="0f71f-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="0f71f-136">いいえ</span><span class="sxs-lookup"><span data-stu-id="0f71f-136">No</span></span>                   | [<span data-ttu-id="0f71f-137">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="0f71f-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="0f71f-138">いいえ</span><span class="sxs-lookup"><span data-stu-id="0f71f-138">No</span></span>                   | [<span data-ttu-id="0f71f-139">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="0f71f-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="0f71f-140">[はい]</span><span class="sxs-lookup"><span data-stu-id="0f71f-140">Yes</span></span>                  | [<span data-ttu-id="0f71f-141">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="0f71f-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="0f71f-142">[はい]</span><span class="sxs-lookup"><span data-stu-id="0f71f-142">Yes</span></span>                  | <span data-ttu-id="0f71f-143">"エンコードなし" 識別子:応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="0f71f-144">いいえ</span><span class="sxs-lookup"><span data-stu-id="0f71f-144">No</span></span>                   | [<span data-ttu-id="0f71f-145">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="0f71f-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="0f71f-146">[はい]</span><span class="sxs-lookup"><span data-stu-id="0f71f-146">Yes</span></span>                  | <span data-ttu-id="0f71f-147">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="0f71f-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="0f71f-148">`Accept-Encoding`ヘッダー値</span><span class="sxs-lookup"><span data-stu-id="0f71f-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="0f71f-149">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="0f71f-149">Middleware Supported</span></span> | <span data-ttu-id="0f71f-150">説明</span><span class="sxs-lookup"><span data-stu-id="0f71f-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="0f71f-151">いいえ</span><span class="sxs-lookup"><span data-stu-id="0f71f-151">No</span></span>                   | [<span data-ttu-id="0f71f-152">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="0f71f-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="0f71f-153">いいえ</span><span class="sxs-lookup"><span data-stu-id="0f71f-153">No</span></span>                   | [<span data-ttu-id="0f71f-154">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="0f71f-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="0f71f-155">いいえ</span><span class="sxs-lookup"><span data-stu-id="0f71f-155">No</span></span>                   | [<span data-ttu-id="0f71f-156">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="0f71f-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="0f71f-157">はい (既定値)</span><span class="sxs-lookup"><span data-stu-id="0f71f-157">Yes (default)</span></span>        | [<span data-ttu-id="0f71f-158">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="0f71f-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="0f71f-159">[はい]</span><span class="sxs-lookup"><span data-stu-id="0f71f-159">Yes</span></span>                  | <span data-ttu-id="0f71f-160">"エンコードなし" 識別子:応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="0f71f-161">いいえ</span><span class="sxs-lookup"><span data-stu-id="0f71f-161">No</span></span>                   | [<span data-ttu-id="0f71f-162">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="0f71f-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="0f71f-163">[はい]</span><span class="sxs-lookup"><span data-stu-id="0f71f-163">Yes</span></span>                  | <span data-ttu-id="0f71f-164">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="0f71f-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="0f71f-165">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0f71f-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="0f71f-166">ミドルウェアを使用すると、カスタム`Accept-Encoding`ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="0f71f-167">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0f71f-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="0f71f-168">ミドルウェアは、圧縮スキームの優先順位を決定するために`q`クライアントから送信されるときに、品質の値 (qvalue,) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="0f71f-169">詳細については[、RFC 7231 を参照してください。Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)。</span><span class="sxs-lookup"><span data-stu-id="0f71f-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="0f71f-170">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="0f71f-171">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="0f71f-172">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="0f71f-173">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="0f71f-174">Header</span><span class="sxs-lookup"><span data-stu-id="0f71f-174">Header</span></span>             | <span data-ttu-id="0f71f-175">ロール</span><span class="sxs-lookup"><span data-stu-id="0f71f-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="0f71f-176">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="0f71f-177">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="0f71f-178">圧縮が発生`Content-Length`すると、応答が圧縮されるときに本文の内容が変更されるため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="0f71f-179">圧縮が行わ`Content-MD5`れると、本文の内容が変更され、ハッシュが有効でなくなったため、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="0f71f-180">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="0f71f-181">すべての応答で、 `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="0f71f-182">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="0f71f-183">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="0f71f-184">の`Accept-Encoding`値がであるサーバーからクライアントとプロキシに送信された`Vary`場合、ヘッダーは、要求の`Accept-Encoding`ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="0f71f-185">`Vary: Accept-Encoding`ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="0f71f-186">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="0f71f-187">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-187">The sample illustrates:</span></span>

* <span data-ttu-id="0f71f-188">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="0f71f-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="0f71f-189">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="0f71f-190">パッケージ</span><span class="sxs-lookup"><span data-stu-id="0f71f-190">Package</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f71f-191">応答圧縮ミドルウェアは、ASP.NET Core アプリに暗黙的に含まれる [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-191">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0f71f-192">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0f71f-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="0f71f-193">構成</span><span class="sxs-lookup"><span data-stu-id="0f71f-193">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0f71f-194">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0f71f-194">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0f71f-195">次のコードは、既定の MIME の種類と[Gzip 圧縮プロバイダー](#gzip-compression-provider)の応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0f71f-195">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="0f71f-196">メモ:</span><span class="sxs-lookup"><span data-stu-id="0f71f-196">Notes:</span></span>

* <span data-ttu-id="0f71f-197">`app.UseResponseCompression`の前に`app.UseMvc`を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-197">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="0f71f-198">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 `Accept-Encoding` [Postman](https://www.getpostman.com/)などのツールを使用して要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-198">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="0f71f-199">ヘッダーを`Accept-Encoding`使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-199">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="0f71f-200">応答`Content-Encoding`に`Vary`ヘッダーとヘッダーが存在しません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-200">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0f71f-203">`Accept-Encoding: br`ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-203">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="0f71f-204">応答`Content-Encoding`に`Vary`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-204">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0f71f-208">`Accept-Encoding: gzip`ヘッダーを使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-208">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="0f71f-209">応答`Content-Encoding`に`Vary`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-209">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと gzip の値で表示されます。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="0f71f-213">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0f71f-213">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="0f71f-214">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0f71f-214">Brotli Compression Provider</span></span>

<span data-ttu-id="0f71f-215"><xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-215">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="0f71f-216">圧縮プロバイダーがに明示的に追加さ<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>れていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-216">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="0f71f-217">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-217">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="0f71f-218">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-218">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="0f71f-219">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-219">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="0f71f-220">圧縮プロバイダーが明示的に追加されている場合は、Brotoli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-220">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0f71f-221">圧縮レベルをに<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>設定します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-221">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="0f71f-222">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-222">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="0f71f-223">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-223">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="0f71f-224">圧縮レベル</span><span class="sxs-lookup"><span data-stu-id="0f71f-224">Compression Level</span></span> | <span data-ttu-id="0f71f-225">説明</span><span class="sxs-lookup"><span data-stu-id="0f71f-225">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="0f71f-226">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="0f71f-226">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="0f71f-227">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-227">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="0f71f-228">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="0f71f-228">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="0f71f-229">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-229">No compression should be performed.</span></span> |
| [<span data-ttu-id="0f71f-230">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="0f71f-230">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="0f71f-231">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-231">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="0f71f-232">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="0f71f-232">Gzip Compression Provider</span></span>

<span data-ttu-id="0f71f-233">を使用<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-233">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="0f71f-234">圧縮プロバイダーがに明示的に追加さ<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>れていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-234">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="0f71f-235">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-235">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="0f71f-236">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-236">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="0f71f-237">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-237">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="0f71f-238">Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-238">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="0f71f-239">クライアントで Gzip 圧縮がサポートされている場合、圧縮の既定値は Gzip です。</span><span class="sxs-lookup"><span data-stu-id="0f71f-239">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="0f71f-240">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-240">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

<span data-ttu-id="0f71f-241">圧縮レベルをに<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>設定します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-241">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="0f71f-242">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-242">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="0f71f-243">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-243">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="0f71f-244">圧縮レベル</span><span class="sxs-lookup"><span data-stu-id="0f71f-244">Compression Level</span></span> | <span data-ttu-id="0f71f-245">説明</span><span class="sxs-lookup"><span data-stu-id="0f71f-245">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="0f71f-246">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="0f71f-246">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="0f71f-247">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-247">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="0f71f-248">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="0f71f-248">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="0f71f-249">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-249">No compression should be performed.</span></span> |
| [<span data-ttu-id="0f71f-250">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="0f71f-250">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="0f71f-251">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-251">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="0f71f-252">カスタムプロバイダー</span><span class="sxs-lookup"><span data-stu-id="0f71f-252">Custom providers</span></span>

<span data-ttu-id="0f71f-253">を使用して、 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-253">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="0f71f-254">は<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> 、この`ICompressionProvider`によって生成されるコンテンツエンコーディングを表します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-254">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="0f71f-255">ミドルウェアは、この情報を使用して、要求の`Accept-Encoding`ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-255">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="0f71f-256">サンプルアプリを使用して、クライアントは`Accept-Encoding: mycustomcompression`ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-256">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="0f71f-257">ミドルウェアはカスタム圧縮実装を使用して、応答を`Content-Encoding: mycustomcompression`ヘッダーと共に返します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-257">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="0f71f-258">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-258">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0f71f-259">`Accept-Encoding: mycustomcompression`ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-259">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="0f71f-260">応答`Vary`に`Content-Encoding`は、ヘッダーとヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-260">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="0f71f-261">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-261">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="0f71f-262">サンプルの`CustomCompressionProvider`クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-262">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="0f71f-263">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="0f71f-263">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="0f71f-266">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="0f71f-266">MIME types</span></span>

<span data-ttu-id="0f71f-267">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-267">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="0f71f-268">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-268">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="0f71f-269">などの`text/*`ワイルドカードの MIME 型はサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="0f71f-269">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="0f71f-270">このサンプルアプリでは、の MIME `image/svg+xml`の種類を追加し、ASP.NET Core バナーイメージ (*横断幕*) を圧縮して提供します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-270">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="0f71f-271">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="0f71f-271">Compression with secure protocol</span></span>

<span data-ttu-id="0f71f-272">セキュリティで保護された接続での圧縮`EnableForHttps`された応答は、既定では無効になっているオプションを使用して制御できます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-272">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="0f71f-273">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-273">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="0f71f-274">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="0f71f-274">Adding the Vary header</span></span>

<span data-ttu-id="0f71f-275">`Accept-Encoding`ヘッダーに基づいて応答を圧縮する場合、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-275">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="0f71f-276">複数のバージョンが存在し、格納`Vary`する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、ヘッダーに`Accept-Encoding`値を追加します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-276">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="0f71f-277">ASP.NET Core 2.0 以降では、応答が圧縮さ`Vary`れるときに、ミドルウェアによってヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-277">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="0f71f-278">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="0f71f-278">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="0f71f-279">要求が Nginx によってプロキシされ`Accept-Encoding`ている場合、ヘッダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-279">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="0f71f-280">`Accept-Encoding`ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-280">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="0f71f-281">詳細については、「[NGINX:圧縮と](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)圧縮解除。</span><span class="sxs-lookup"><span data-stu-id="0f71f-281">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="0f71f-282">この問題は、 [Nginx のパススルー圧縮 (aspnet/ \#basicmiddleware 123)](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-282">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="0f71f-283">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="0f71f-283">Working with IIS dynamic compression</span></span>

<span data-ttu-id="0f71f-284">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、web.config ファイルに追加してモジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="0f71f-284">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="0f71f-285">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0f71f-285">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0f71f-286">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0f71f-286">Troubleshooting</span></span>

<span data-ttu-id="0f71f-287">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、 [Postman](https://www.getpostman.com/)などのツールを使用します。これに`Accept-Encoding`より、要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="0f71f-287">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="0f71f-288">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="0f71f-288">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="0f71f-289">ヘッダー `Accept-Encoding`には`br`、設定したカスタム圧縮`gzip`プロバイダー `*`に一致する値、、、またはカスタムエンコーディングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0f71f-289">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="0f71f-290">値は、 `identity`または品質値 (qvalue, `q`) の 0 (ゼロ) に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-290">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="0f71f-291">Mime の種類 (`Content-Type`) を設定し、 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている mime の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-291">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="0f71f-292">要求にヘッダーを`Content-Range`含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-292">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="0f71f-293">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-293">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="0f71f-294">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="0f71f-294">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="0f71f-295">ヘッダー `Accept-Encoding`には`gzip`、設定したカスタム圧縮`*`プロバイダーに一致する値、、またはカスタムエンコーディングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0f71f-295">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="0f71f-296">値は、 `identity`または品質値 (qvalue, `q`) の 0 (ゼロ) に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-296">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="0f71f-297">Mime の種類 (`Content-Type`) を設定し、 <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている mime の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-297">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="0f71f-298">要求にヘッダーを`Content-Range`含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="0f71f-298">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="0f71f-299">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f71f-299">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="0f71f-300">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="0f71f-300">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0f71f-301">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0f71f-301">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="0f71f-302">Mozilla Developer Network:Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="0f71f-302">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="0f71f-303">RFC 7231 セクション 3.1.2.1:コンテンツの Codings</span><span class="sxs-lookup"><span data-stu-id="0f71f-303">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="0f71f-304">RFC 7230 セクション 4.2.3:Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="0f71f-304">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="0f71f-305">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="0f71f-305">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)
