---
title: ASP.NET Core での応答圧縮
author: guardrex
description: 応答圧縮と ASP.NET Core アプリで応答圧縮ミドルウェアを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/response-compression
ms.openlocfilehash: d37b05edd55ac0d3910855563b819114cf815b43
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114810"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="094b2-103">ASP.NET Core での応答圧縮</span><span class="sxs-lookup"><span data-stu-id="094b2-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="094b2-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="094b2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="094b2-105">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="094b2-105">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="094b2-106">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="094b2-106">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="094b2-107">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="094b2-107">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="094b2-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="094b2-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="094b2-109">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="094b2-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="094b2-110">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="094b2-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="094b2-111">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="094b2-112">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="094b2-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="094b2-113">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="094b2-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="094b2-114">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="094b2-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="094b2-115">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="094b2-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="094b2-116">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="094b2-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="094b2-117">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="094b2-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="094b2-118">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="094b2-118">Hosting directly on:</span></span>
  * <span data-ttu-id="094b2-119">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="094b2-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="094b2-120">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="094b2-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="094b2-121">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="094b2-121">Response compression</span></span>

<span data-ttu-id="094b2-122">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="094b2-123">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="094b2-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="094b2-124">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="094b2-125">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="094b2-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="094b2-126">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="094b2-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="094b2-127">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="094b2-128">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に `Accept-Encoding` ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="094b2-129">サーバーは、圧縮されたコンテンツを送信するときに、圧縮された応答のエンコード方法に関する情報を `Content-Encoding` ヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="094b2-130">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="094b2-131">ヘッダー値の `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="094b2-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="094b2-132">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="094b2-132">Middleware Supported</span></span> | <span data-ttu-id="094b2-133">[説明]</span><span class="sxs-lookup"><span data-stu-id="094b2-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="094b2-134">可 (既定)</span><span class="sxs-lookup"><span data-stu-id="094b2-134">Yes (default)</span></span>        | [<span data-ttu-id="094b2-135">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="094b2-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="094b2-136">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-136">No</span></span>                   | [<span data-ttu-id="094b2-137">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="094b2-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="094b2-138">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-138">No</span></span>                   | [<span data-ttu-id="094b2-139">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="094b2-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="094b2-140">はい</span><span class="sxs-lookup"><span data-stu-id="094b2-140">Yes</span></span>                  | [<span data-ttu-id="094b2-141">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="094b2-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="094b2-142">はい</span><span class="sxs-lookup"><span data-stu-id="094b2-142">Yes</span></span>                  | <span data-ttu-id="094b2-143">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="094b2-144">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-144">No</span></span>                   | [<span data-ttu-id="094b2-145">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="094b2-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="094b2-146">はい</span><span class="sxs-lookup"><span data-stu-id="094b2-146">Yes</span></span>                  | <span data-ttu-id="094b2-147">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="094b2-147">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="094b2-148">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-148">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="094b2-149">ミドルウェアを使用すると、カスタム `Accept-Encoding` ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="094b2-149">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="094b2-150">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-150">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="094b2-151">ミドルウェアは、圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue、`q`) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="094b2-151">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="094b2-152">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-152">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="094b2-153">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="094b2-153">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="094b2-154">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-154">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="094b2-155">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-155">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="094b2-156">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="094b2-156">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="094b2-157">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="094b2-157">Header</span></span>             | <span data-ttu-id="094b2-158">Role</span><span class="sxs-lookup"><span data-stu-id="094b2-158">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="094b2-159">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-159">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="094b2-160">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-160">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="094b2-161">圧縮が発生すると、応答が圧縮されるときに本文の内容が変更されるため、`Content-Length` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-161">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="094b2-162">圧縮が行われると、本文の内容が変更され、ハッシュが有効でなくなったため、`Content-MD5` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-162">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="094b2-163">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-163">Specifies the MIME type of the content.</span></span> <span data-ttu-id="094b2-164">すべての応答は、その `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-164">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="094b2-165">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="094b2-165">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="094b2-166">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-166">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="094b2-167">`Accept-Encoding` の値を持つサーバーからクライアントとプロキシに送信された場合、`Vary` ヘッダーは、要求の `Accept-Encoding` ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-167">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="094b2-168">`Vary: Accept-Encoding` ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="094b2-168">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="094b2-169">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="094b2-169">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="094b2-170">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-170">The sample illustrates:</span></span>

* <span data-ttu-id="094b2-171">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="094b2-171">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="094b2-172">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="094b2-172">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="094b2-173">Package</span><span class="sxs-lookup"><span data-stu-id="094b2-173">Package</span></span>

<span data-ttu-id="094b2-174">応答圧縮ミドルウェアは、AspNetCore アプリ ASP.NET Core に暗黙的に含まれる[ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-174">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="094b2-175">構成</span><span class="sxs-lookup"><span data-stu-id="094b2-175">Configuration</span></span>

<span data-ttu-id="094b2-176">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="094b2-176">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="094b2-177">注:</span><span class="sxs-lookup"><span data-stu-id="094b2-177">Notes:</span></span>

* <span data-ttu-id="094b2-178">応答を圧縮するミドルウェアの前に `app.UseResponseCompression` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-178">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="094b2-179">詳細については、<xref:fundamentals/middleware/index#middleware-order> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-179">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="094b2-180">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="094b2-180">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="094b2-181">`Accept-Encoding` ヘッダーを使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-181">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="094b2-182">`Content-Encoding` ヘッダーと `Vary` ヘッダーが応答に存在しません。</span><span class="sxs-lookup"><span data-stu-id="094b2-182">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="094b2-185">`Accept-Encoding: br` ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-185">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="094b2-186">応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="094b2-186">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="094b2-190">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-190">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="094b2-191">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-191">Brotli Compression Provider</span></span>

<span data-ttu-id="094b2-192">[Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> を使用します。</span><span class="sxs-lookup"><span data-stu-id="094b2-192">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="094b2-193"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="094b2-193">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="094b2-194">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-194">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="094b2-195">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="094b2-195">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="094b2-196">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="094b2-196">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="094b2-197">圧縮プロバイダーが明示的に追加されている場合は、Brotoli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-197">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="094b2-198">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-198">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="094b2-199">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-199">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="094b2-200">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="094b2-200">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="094b2-201">Compression Level</span><span class="sxs-lookup"><span data-stu-id="094b2-201">Compression Level</span></span> | <span data-ttu-id="094b2-202">[説明]</span><span class="sxs-lookup"><span data-stu-id="094b2-202">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="094b2-203">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-203">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-204">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-204">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="094b2-205">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-205">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-206">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="094b2-206">No compression should be performed.</span></span> |
| [<span data-ttu-id="094b2-207">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-207">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-208">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-208">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="094b2-209">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-209">Gzip Compression Provider</span></span>

<span data-ttu-id="094b2-210"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> を使用して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="094b2-210">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="094b2-211"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="094b2-211">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="094b2-212">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-212">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="094b2-213">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="094b2-213">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="094b2-214">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="094b2-214">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="094b2-215">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-215">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="094b2-216">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-216">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="094b2-217">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-217">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="094b2-218">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="094b2-218">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="094b2-219">Compression Level</span><span class="sxs-lookup"><span data-stu-id="094b2-219">Compression Level</span></span> | <span data-ttu-id="094b2-220">[説明]</span><span class="sxs-lookup"><span data-stu-id="094b2-220">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="094b2-221">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-221">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-222">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-222">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="094b2-223">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-223">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-224">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="094b2-224">No compression should be performed.</span></span> |
| [<span data-ttu-id="094b2-225">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-225">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-226">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-226">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="094b2-227">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-227">Custom providers</span></span>

<span data-ttu-id="094b2-228"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>を使用して、カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="094b2-228">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="094b2-229">この `ICompressionProvider` が生成するコンテンツエンコーディングを表す <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>。</span><span class="sxs-lookup"><span data-stu-id="094b2-229">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="094b2-230">ミドルウェアは、この情報を使用して、要求の `Accept-Encoding` ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="094b2-230">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="094b2-231">このサンプルアプリを使用すると、クライアントは `Accept-Encoding: mycustomcompression` ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="094b2-231">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="094b2-232">ミドルウェアは、カスタム圧縮実装を使用して、`Content-Encoding: mycustomcompression` ヘッダーで応答を返します。</span><span class="sxs-lookup"><span data-stu-id="094b2-232">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="094b2-233">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-233">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="094b2-234">`Accept-Encoding: mycustomcompression` ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-234">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="094b2-235">応答には、`Vary` ヘッダーと `Content-Encoding` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="094b2-235">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="094b2-236">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="094b2-236">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="094b2-237">このサンプルの `CustomCompressionProvider` クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="094b2-237">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="094b2-238">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="094b2-238">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="094b2-241">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="094b2-241">MIME types</span></span>

<span data-ttu-id="094b2-242">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-242">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="094b2-243">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="094b2-243">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="094b2-244">`text/*` などのワイルドカード MIME の種類はサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-244">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="094b2-245">このサンプルアプリでは、`image/svg+xml` 用の MIME の種類を追加し、ASP.NET Core バナー画像 (*横断幕*) を圧縮して機能させることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-245">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="094b2-246">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="094b2-246">Compression with secure protocol</span></span>

<span data-ttu-id="094b2-247">セキュリティで保護された接続での圧縮された応答は、`EnableForHttps` オプションを使用して制御できます。これは既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="094b2-247">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="094b2-248">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-248">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="094b2-249">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="094b2-249">Adding the Vary header</span></span>

<span data-ttu-id="094b2-250">`Accept-Encoding` ヘッダーに基づいて応答を圧縮する場合は、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-250">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="094b2-251">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、`Vary` ヘッダーが `Accept-Encoding` 値と共に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-251">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="094b2-252">ASP.NET Core 2.0 以降では、応答が圧縮されるときに、ミドルウェアによって `Vary` ヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-252">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="094b2-253">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="094b2-253">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="094b2-254">要求が Nginx によってプロキシされると、`Accept-Encoding` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-254">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="094b2-255">`Accept-Encoding` ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="094b2-255">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="094b2-256">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-256">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="094b2-257">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-257">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="094b2-258">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="094b2-258">Working with IIS dynamic compression</span></span>

<span data-ttu-id="094b2-259">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="094b2-259">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="094b2-260">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-260">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="094b2-261">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="094b2-261">Troubleshooting</span></span>

<span data-ttu-id="094b2-262">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して、`Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-262">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="094b2-263">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="094b2-263">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="094b2-264">`Accept-Encoding` ヘッダーには、`br`、`gzip`、`*`、または独自に作成したカスタム圧縮プロバイダーに一致するカスタムエンコーディングの値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="094b2-264">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="094b2-265">値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-265">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="094b2-266">MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-266">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="094b2-267">要求に `Content-Range` ヘッダーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-267">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="094b2-268">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-268">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="094b2-269">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="094b2-269">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="094b2-270">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="094b2-270">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="094b2-271">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="094b2-271">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="094b2-272">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="094b2-272">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="094b2-273">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="094b2-273">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="094b2-274">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="094b2-274">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="094b2-275">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="094b2-275">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="094b2-276">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="094b2-276">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="094b2-277">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="094b2-277">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="094b2-278">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="094b2-278">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="094b2-279">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="094b2-279">When to use Response Compression Middleware</span></span>

<span data-ttu-id="094b2-280">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="094b2-280">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="094b2-281">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-281">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="094b2-282">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="094b2-282">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="094b2-283">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="094b2-283">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="094b2-284">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="094b2-284">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="094b2-285">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="094b2-285">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="094b2-286">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="094b2-286">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="094b2-287">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="094b2-287">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="094b2-288">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="094b2-288">Hosting directly on:</span></span>
  * <span data-ttu-id="094b2-289">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="094b2-289">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="094b2-290">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="094b2-290">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="094b2-291">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="094b2-291">Response compression</span></span>

<span data-ttu-id="094b2-292">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-292">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="094b2-293">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="094b2-293">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="094b2-294">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-294">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="094b2-295">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="094b2-295">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="094b2-296">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="094b2-296">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="094b2-297">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-297">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="094b2-298">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に `Accept-Encoding` ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-298">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="094b2-299">サーバーは、圧縮されたコンテンツを送信するときに、圧縮された応答のエンコード方法に関する情報を `Content-Encoding` ヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-299">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="094b2-300">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-300">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="094b2-301">ヘッダー値の `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="094b2-301">`Accept-Encoding` header values</span></span> | <span data-ttu-id="094b2-302">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="094b2-302">Middleware Supported</span></span> | <span data-ttu-id="094b2-303">[説明]</span><span class="sxs-lookup"><span data-stu-id="094b2-303">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="094b2-304">可 (既定)</span><span class="sxs-lookup"><span data-stu-id="094b2-304">Yes (default)</span></span>        | [<span data-ttu-id="094b2-305">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="094b2-305">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="094b2-306">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-306">No</span></span>                   | [<span data-ttu-id="094b2-307">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="094b2-307">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="094b2-308">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-308">No</span></span>                   | [<span data-ttu-id="094b2-309">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="094b2-309">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="094b2-310">はい</span><span class="sxs-lookup"><span data-stu-id="094b2-310">Yes</span></span>                  | [<span data-ttu-id="094b2-311">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="094b2-311">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="094b2-312">はい</span><span class="sxs-lookup"><span data-stu-id="094b2-312">Yes</span></span>                  | <span data-ttu-id="094b2-313">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-313">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="094b2-314">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-314">No</span></span>                   | [<span data-ttu-id="094b2-315">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="094b2-315">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="094b2-316">はい</span><span class="sxs-lookup"><span data-stu-id="094b2-316">Yes</span></span>                  | <span data-ttu-id="094b2-317">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="094b2-317">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="094b2-318">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-318">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="094b2-319">ミドルウェアを使用すると、カスタム `Accept-Encoding` ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="094b2-319">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="094b2-320">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-320">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="094b2-321">ミドルウェアは、圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue、`q`) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="094b2-321">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="094b2-322">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-322">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="094b2-323">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="094b2-323">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="094b2-324">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-324">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="094b2-325">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-325">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="094b2-326">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="094b2-326">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="094b2-327">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="094b2-327">Header</span></span>             | <span data-ttu-id="094b2-328">Role</span><span class="sxs-lookup"><span data-stu-id="094b2-328">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="094b2-329">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-329">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="094b2-330">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-330">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="094b2-331">圧縮が発生すると、応答が圧縮されるときに本文の内容が変更されるため、`Content-Length` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-331">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="094b2-332">圧縮が行われると、本文の内容が変更され、ハッシュが有効でなくなったため、`Content-MD5` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-332">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="094b2-333">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-333">Specifies the MIME type of the content.</span></span> <span data-ttu-id="094b2-334">すべての応答は、その `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-334">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="094b2-335">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="094b2-335">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="094b2-336">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-336">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="094b2-337">`Accept-Encoding` の値を持つサーバーからクライアントとプロキシに送信された場合、`Vary` ヘッダーは、要求の `Accept-Encoding` ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-337">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="094b2-338">`Vary: Accept-Encoding` ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="094b2-338">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="094b2-339">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="094b2-339">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="094b2-340">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-340">The sample illustrates:</span></span>

* <span data-ttu-id="094b2-341">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="094b2-341">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="094b2-342">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="094b2-342">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="094b2-343">Package</span><span class="sxs-lookup"><span data-stu-id="094b2-343">Package</span></span>

<span data-ttu-id="094b2-344">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="094b2-344">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="094b2-345">構成</span><span class="sxs-lookup"><span data-stu-id="094b2-345">Configuration</span></span>

<span data-ttu-id="094b2-346">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="094b2-346">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="094b2-347">注:</span><span class="sxs-lookup"><span data-stu-id="094b2-347">Notes:</span></span>

* <span data-ttu-id="094b2-348">応答を圧縮するミドルウェアの前に `app.UseResponseCompression` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-348">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="094b2-349">詳細については、<xref:fundamentals/middleware/index#middleware-order> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-349">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="094b2-350">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="094b2-350">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="094b2-351">`Accept-Encoding` ヘッダーを使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-351">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="094b2-352">`Content-Encoding` ヘッダーと `Vary` ヘッダーが応答に存在しません。</span><span class="sxs-lookup"><span data-stu-id="094b2-352">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="094b2-355">`Accept-Encoding: br` ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-355">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="094b2-356">応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="094b2-356">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="094b2-360">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-360">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="094b2-361">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-361">Brotli Compression Provider</span></span>

<span data-ttu-id="094b2-362">[Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> を使用します。</span><span class="sxs-lookup"><span data-stu-id="094b2-362">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="094b2-363"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="094b2-363">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="094b2-364">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-364">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="094b2-365">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="094b2-365">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="094b2-366">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="094b2-366">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="094b2-367">圧縮プロバイダーが明示的に追加されている場合は、Brotoli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-367">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="094b2-368">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-368">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="094b2-369">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-369">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="094b2-370">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="094b2-370">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="094b2-371">Compression Level</span><span class="sxs-lookup"><span data-stu-id="094b2-371">Compression Level</span></span> | <span data-ttu-id="094b2-372">[説明]</span><span class="sxs-lookup"><span data-stu-id="094b2-372">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="094b2-373">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-373">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-374">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-374">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="094b2-375">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-375">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-376">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="094b2-376">No compression should be performed.</span></span> |
| [<span data-ttu-id="094b2-377">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-377">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-378">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-378">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="094b2-379">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-379">Gzip Compression Provider</span></span>

<span data-ttu-id="094b2-380"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> を使用して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="094b2-380">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="094b2-381"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="094b2-381">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="094b2-382">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-382">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="094b2-383">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="094b2-383">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="094b2-384">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="094b2-384">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="094b2-385">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-385">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="094b2-386">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-386">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="094b2-387">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-387">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="094b2-388">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="094b2-388">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="094b2-389">Compression Level</span><span class="sxs-lookup"><span data-stu-id="094b2-389">Compression Level</span></span> | <span data-ttu-id="094b2-390">[説明]</span><span class="sxs-lookup"><span data-stu-id="094b2-390">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="094b2-391">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-391">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-392">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-392">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="094b2-393">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-393">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-394">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="094b2-394">No compression should be performed.</span></span> |
| [<span data-ttu-id="094b2-395">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-395">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-396">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-396">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="094b2-397">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-397">Custom providers</span></span>

<span data-ttu-id="094b2-398"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>を使用して、カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="094b2-398">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="094b2-399">この `ICompressionProvider` が生成するコンテンツエンコーディングを表す <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>。</span><span class="sxs-lookup"><span data-stu-id="094b2-399">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="094b2-400">ミドルウェアは、この情報を使用して、要求の `Accept-Encoding` ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="094b2-400">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="094b2-401">このサンプルアプリを使用すると、クライアントは `Accept-Encoding: mycustomcompression` ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="094b2-401">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="094b2-402">ミドルウェアは、カスタム圧縮実装を使用して、`Content-Encoding: mycustomcompression` ヘッダーで応答を返します。</span><span class="sxs-lookup"><span data-stu-id="094b2-402">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="094b2-403">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-403">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="094b2-404">`Accept-Encoding: mycustomcompression` ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-404">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="094b2-405">応答には、`Vary` ヘッダーと `Content-Encoding` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="094b2-405">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="094b2-406">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="094b2-406">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="094b2-407">このサンプルの `CustomCompressionProvider` クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="094b2-407">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="094b2-408">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="094b2-408">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="094b2-411">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="094b2-411">MIME types</span></span>

<span data-ttu-id="094b2-412">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-412">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="094b2-413">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="094b2-413">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="094b2-414">`text/*` などのワイルドカード MIME の種類はサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-414">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="094b2-415">このサンプルアプリでは、`image/svg+xml` 用の MIME の種類を追加し、ASP.NET Core バナー画像 (*横断幕*) を圧縮して機能させることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-415">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="094b2-416">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="094b2-416">Compression with secure protocol</span></span>

<span data-ttu-id="094b2-417">セキュリティで保護された接続での圧縮された応答は、`EnableForHttps` オプションを使用して制御できます。これは既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="094b2-417">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="094b2-418">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-418">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="094b2-419">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="094b2-419">Adding the Vary header</span></span>

<span data-ttu-id="094b2-420">`Accept-Encoding` ヘッダーに基づいて応答を圧縮する場合は、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-420">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="094b2-421">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、`Vary` ヘッダーが `Accept-Encoding` 値と共に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-421">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="094b2-422">ASP.NET Core 2.0 以降では、応答が圧縮されるときに、ミドルウェアによって `Vary` ヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-422">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="094b2-423">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="094b2-423">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="094b2-424">要求が Nginx によってプロキシされると、`Accept-Encoding` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-424">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="094b2-425">`Accept-Encoding` ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="094b2-425">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="094b2-426">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-426">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="094b2-427">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-427">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="094b2-428">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="094b2-428">Working with IIS dynamic compression</span></span>

<span data-ttu-id="094b2-429">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="094b2-429">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="094b2-430">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-430">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="094b2-431">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="094b2-431">Troubleshooting</span></span>

<span data-ttu-id="094b2-432">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して、`Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-432">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="094b2-433">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="094b2-433">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="094b2-434">`Accept-Encoding` ヘッダーには、`br`、`gzip`、`*`、または独自に作成したカスタム圧縮プロバイダーに一致するカスタムエンコーディングの値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="094b2-434">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="094b2-435">値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-435">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="094b2-436">MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-436">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="094b2-437">要求に `Content-Range` ヘッダーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-437">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="094b2-438">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-438">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="094b2-439">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="094b2-439">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="094b2-440">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="094b2-440">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="094b2-441">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="094b2-441">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="094b2-442">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="094b2-442">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="094b2-443">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="094b2-443">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="094b2-444">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="094b2-444">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="094b2-445">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="094b2-445">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="094b2-446">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="094b2-446">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="094b2-447">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="094b2-447">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="094b2-448">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="094b2-448">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="094b2-449">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="094b2-449">When to use Response Compression Middleware</span></span>

<span data-ttu-id="094b2-450">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="094b2-450">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="094b2-451">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-451">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="094b2-452">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="094b2-452">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="094b2-453">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="094b2-453">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="094b2-454">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="094b2-454">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="094b2-455">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="094b2-455">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="094b2-456">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="094b2-456">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="094b2-457">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="094b2-457">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="094b2-458">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="094b2-458">Hosting directly on:</span></span>
  * <span data-ttu-id="094b2-459">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="094b2-459">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="094b2-460">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="094b2-460">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="094b2-461">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="094b2-461">Response compression</span></span>

<span data-ttu-id="094b2-462">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-462">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="094b2-463">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="094b2-463">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="094b2-464">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-464">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="094b2-465">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="094b2-465">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="094b2-466">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="094b2-466">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="094b2-467">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-467">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="094b2-468">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に `Accept-Encoding` ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-468">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="094b2-469">サーバーは、圧縮されたコンテンツを送信するときに、圧縮された応答のエンコード方法に関する情報を `Content-Encoding` ヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-469">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="094b2-470">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-470">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="094b2-471">ヘッダー値の `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="094b2-471">`Accept-Encoding` header values</span></span> | <span data-ttu-id="094b2-472">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="094b2-472">Middleware Supported</span></span> | <span data-ttu-id="094b2-473">[説明]</span><span class="sxs-lookup"><span data-stu-id="094b2-473">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="094b2-474">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-474">No</span></span>                   | [<span data-ttu-id="094b2-475">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="094b2-475">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="094b2-476">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-476">No</span></span>                   | [<span data-ttu-id="094b2-477">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="094b2-477">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="094b2-478">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-478">No</span></span>                   | [<span data-ttu-id="094b2-479">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="094b2-479">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="094b2-480">可 (既定)</span><span class="sxs-lookup"><span data-stu-id="094b2-480">Yes (default)</span></span>        | [<span data-ttu-id="094b2-481">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="094b2-481">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="094b2-482">はい</span><span class="sxs-lookup"><span data-stu-id="094b2-482">Yes</span></span>                  | <span data-ttu-id="094b2-483">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-483">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="094b2-484">いいえ</span><span class="sxs-lookup"><span data-stu-id="094b2-484">No</span></span>                   | [<span data-ttu-id="094b2-485">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="094b2-485">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="094b2-486">はい</span><span class="sxs-lookup"><span data-stu-id="094b2-486">Yes</span></span>                  | <span data-ttu-id="094b2-487">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="094b2-487">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="094b2-488">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-488">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="094b2-489">ミドルウェアを使用すると、カスタム `Accept-Encoding` ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="094b2-489">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="094b2-490">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-490">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="094b2-491">ミドルウェアは、圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue、`q`) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="094b2-491">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="094b2-492">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-492">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="094b2-493">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="094b2-493">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="094b2-494">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-494">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="094b2-495">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-495">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="094b2-496">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="094b2-496">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="094b2-497">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="094b2-497">Header</span></span>             | <span data-ttu-id="094b2-498">Role</span><span class="sxs-lookup"><span data-stu-id="094b2-498">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="094b2-499">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-499">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="094b2-500">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-500">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="094b2-501">圧縮が発生すると、応答が圧縮されるときに本文の内容が変更されるため、`Content-Length` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-501">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="094b2-502">圧縮が行われると、本文の内容が変更され、ハッシュが有効でなくなったため、`Content-MD5` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-502">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="094b2-503">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-503">Specifies the MIME type of the content.</span></span> <span data-ttu-id="094b2-504">すべての応答は、その `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-504">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="094b2-505">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="094b2-505">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="094b2-506">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-506">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="094b2-507">`Accept-Encoding` の値を持つサーバーからクライアントとプロキシに送信された場合、`Vary` ヘッダーは、要求の `Accept-Encoding` ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-507">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="094b2-508">`Vary: Accept-Encoding` ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="094b2-508">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="094b2-509">[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="094b2-509">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="094b2-510">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="094b2-510">The sample illustrates:</span></span>

* <span data-ttu-id="094b2-511">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="094b2-511">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="094b2-512">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="094b2-512">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="094b2-513">Package</span><span class="sxs-lookup"><span data-stu-id="094b2-513">Package</span></span>

<span data-ttu-id="094b2-514">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="094b2-514">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="094b2-515">構成</span><span class="sxs-lookup"><span data-stu-id="094b2-515">Configuration</span></span>

<span data-ttu-id="094b2-516">次のコードは、既定の MIME の種類と[Gzip 圧縮プロバイダー](#gzip-compression-provider)の応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="094b2-516">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="094b2-517">注:</span><span class="sxs-lookup"><span data-stu-id="094b2-517">Notes:</span></span>

* <span data-ttu-id="094b2-518">応答を圧縮するミドルウェアの前に `app.UseResponseCompression` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-518">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="094b2-519">詳細については、<xref:fundamentals/middleware/index#middleware-order> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-519">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="094b2-520">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="094b2-520">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="094b2-521">`Accept-Encoding` ヘッダーを使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-521">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="094b2-522">`Content-Encoding` ヘッダーと `Vary` ヘッダーが応答に存在しません。</span><span class="sxs-lookup"><span data-stu-id="094b2-522">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="094b2-525">`Accept-Encoding: gzip` ヘッダーを使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-525">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="094b2-526">応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="094b2-526">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと gzip の値で表示されます。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="094b2-530">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-530">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="094b2-531">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-531">Gzip Compression Provider</span></span>

<span data-ttu-id="094b2-532"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> を使用して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="094b2-532">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="094b2-533"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="094b2-533">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="094b2-534">Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-534">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="094b2-535">クライアントで Gzip 圧縮がサポートされている場合、圧縮の既定値は Gzip です。</span><span class="sxs-lookup"><span data-stu-id="094b2-535">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="094b2-536">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-536">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="094b2-537">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-537">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="094b2-538">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-538">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="094b2-539">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="094b2-539">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="094b2-540">Compression Level</span><span class="sxs-lookup"><span data-stu-id="094b2-540">Compression Level</span></span> | <span data-ttu-id="094b2-541">[説明]</span><span class="sxs-lookup"><span data-stu-id="094b2-541">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="094b2-542">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-542">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-543">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-543">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="094b2-544">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-544">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-545">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="094b2-545">No compression should be performed.</span></span> |
| [<span data-ttu-id="094b2-546">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="094b2-546">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="094b2-547">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-547">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="094b2-548">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="094b2-548">Custom providers</span></span>

<span data-ttu-id="094b2-549"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>を使用して、カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="094b2-549">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="094b2-550">この `ICompressionProvider` が生成するコンテンツエンコーディングを表す <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>。</span><span class="sxs-lookup"><span data-stu-id="094b2-550">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="094b2-551">ミドルウェアは、この情報を使用して、要求の `Accept-Encoding` ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="094b2-551">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="094b2-552">このサンプルアプリを使用すると、クライアントは `Accept-Encoding: mycustomcompression` ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="094b2-552">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="094b2-553">ミドルウェアは、カスタム圧縮実装を使用して、`Content-Encoding: mycustomcompression` ヘッダーで応答を返します。</span><span class="sxs-lookup"><span data-stu-id="094b2-553">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="094b2-554">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-554">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="094b2-555">`Accept-Encoding: mycustomcompression` ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="094b2-555">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="094b2-556">応答には、`Vary` ヘッダーと `Content-Encoding` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="094b2-556">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="094b2-557">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="094b2-557">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="094b2-558">このサンプルの `CustomCompressionProvider` クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="094b2-558">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="094b2-559">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="094b2-559">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="094b2-562">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="094b2-562">MIME types</span></span>

<span data-ttu-id="094b2-563">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="094b2-563">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="094b2-564">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="094b2-564">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="094b2-565">`text/*` などのワイルドカード MIME の種類はサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-565">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="094b2-566">このサンプルアプリでは、`image/svg+xml` 用の MIME の種類を追加し、ASP.NET Core バナー画像 (*横断幕*) を圧縮して機能させることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-566">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="094b2-567">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="094b2-567">Compression with secure protocol</span></span>

<span data-ttu-id="094b2-568">セキュリティで保護された接続での圧縮された応答は、`EnableForHttps` オプションを使用して制御できます。これは既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="094b2-568">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="094b2-569">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-569">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="094b2-570">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="094b2-570">Adding the Vary header</span></span>

<span data-ttu-id="094b2-571">`Accept-Encoding` ヘッダーに基づいて応答を圧縮する場合は、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-571">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="094b2-572">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、`Vary` ヘッダーが `Accept-Encoding` 値と共に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-572">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="094b2-573">ASP.NET Core 2.0 以降では、応答が圧縮されるときに、ミドルウェアによって `Vary` ヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-573">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="094b2-574">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="094b2-574">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="094b2-575">要求が Nginx によってプロキシされると、`Accept-Encoding` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-575">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="094b2-576">`Accept-Encoding` ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="094b2-576">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="094b2-577">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-577">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="094b2-578">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="094b2-578">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="094b2-579">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="094b2-579">Working with IIS dynamic compression</span></span>

<span data-ttu-id="094b2-580">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="094b2-580">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="094b2-581">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="094b2-581">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="094b2-582">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="094b2-582">Troubleshooting</span></span>

<span data-ttu-id="094b2-583">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して、`Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="094b2-583">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="094b2-584">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="094b2-584">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="094b2-585">`Accept-Encoding` ヘッダーには、`gzip`、`*`、またはユーザーが設定したカスタム圧縮プロバイダーに一致するカスタムエンコードの値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="094b2-585">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="094b2-586">値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-586">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="094b2-587">MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-587">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="094b2-588">要求に `Content-Range` ヘッダーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="094b2-588">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="094b2-589">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="094b2-589">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="094b2-590">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="094b2-590">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="094b2-591">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="094b2-591">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="094b2-592">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="094b2-592">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="094b2-593">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="094b2-593">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="094b2-594">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="094b2-594">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="094b2-595">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="094b2-595">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
