---
title: ASP.NET Core での応答圧縮
author: rick-anderson
description: 応答圧縮と ASP.NET Core アプリで応答圧縮ミドルウェアを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/response-compression
ms.openlocfilehash: aae0b8d74fc424cc81c046e9042279856865bf6a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654344"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="590af-103">ASP.NET Core での応答圧縮</span><span class="sxs-lookup"><span data-stu-id="590af-103">Response compression in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="590af-104">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="590af-104">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="590af-105">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="590af-105">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="590af-106">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="590af-106">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="590af-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="590af-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="590af-108">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="590af-108">When to use Response Compression Middleware</span></span>

<span data-ttu-id="590af-109">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="590af-109">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="590af-110">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="590af-110">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="590af-111">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="590af-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="590af-112">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="590af-112">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="590af-113">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="590af-113">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="590af-114">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="590af-114">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="590af-115">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="590af-115">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="590af-116">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="590af-116">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="590af-117">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="590af-117">Hosting directly on:</span></span>
  * <span data-ttu-id="590af-118">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="590af-118">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="590af-119">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="590af-119">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="590af-120">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="590af-120">Response compression</span></span>

<span data-ttu-id="590af-121">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-121">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="590af-122">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="590af-122">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="590af-123">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-123">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="590af-124">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="590af-124">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="590af-125">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="590af-125">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="590af-126">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-126">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="590af-127">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に `Accept-Encoding` ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-127">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="590af-128">サーバーは、圧縮されたコンテンツを送信するときに、圧縮された応答のエンコード方法に関する情報を `Content-Encoding` ヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-128">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="590af-129">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="590af-129">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="590af-130">ヘッダー値の `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="590af-130">`Accept-Encoding` header values</span></span> | <span data-ttu-id="590af-131">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="590af-131">Middleware Supported</span></span> | <span data-ttu-id="590af-132">説明</span><span class="sxs-lookup"><span data-stu-id="590af-132">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="590af-133">可 (既定)</span><span class="sxs-lookup"><span data-stu-id="590af-133">Yes (default)</span></span>        | [<span data-ttu-id="590af-134">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="590af-134">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="590af-135">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-135">No</span></span>                   | [<span data-ttu-id="590af-136">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="590af-136">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="590af-137">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-137">No</span></span>                   | [<span data-ttu-id="590af-138">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="590af-138">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="590af-139">はい</span><span class="sxs-lookup"><span data-stu-id="590af-139">Yes</span></span>                  | [<span data-ttu-id="590af-140">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="590af-140">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="590af-141">はい</span><span class="sxs-lookup"><span data-stu-id="590af-141">Yes</span></span>                  | <span data-ttu-id="590af-142">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-142">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="590af-143">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-143">No</span></span>                   | [<span data-ttu-id="590af-144">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="590af-144">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="590af-145">はい</span><span class="sxs-lookup"><span data-stu-id="590af-145">Yes</span></span>                  | <span data-ttu-id="590af-146">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="590af-146">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="590af-147">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-147">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="590af-148">ミドルウェアを使用すると、カスタム `Accept-Encoding` ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="590af-148">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="590af-149">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-149">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="590af-150">ミドルウェアは、圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue、`q`) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="590af-150">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="590af-151">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-151">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="590af-152">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="590af-152">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="590af-153">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-153">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="590af-154">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="590af-154">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="590af-155">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="590af-155">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="590af-156">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="590af-156">Header</span></span>             | <span data-ttu-id="590af-157">役割</span><span class="sxs-lookup"><span data-stu-id="590af-157">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="590af-158">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-158">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="590af-159">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="590af-159">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="590af-160">圧縮が発生すると、応答が圧縮されるときに本文の内容が変更されるため、`Content-Length` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-160">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="590af-161">圧縮が行われると、本文の内容が変更され、ハッシュが有効でなくなったため、`Content-MD5` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-161">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="590af-162">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="590af-162">Specifies the MIME type of the content.</span></span> <span data-ttu-id="590af-163">すべての応答は、その `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-163">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="590af-164">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="590af-164">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="590af-165">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-165">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="590af-166">`Accept-Encoding` の値を持つサーバーからクライアントとプロキシに送信された場合、`Vary` ヘッダーは、要求の `Accept-Encoding` ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="590af-166">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="590af-167">`Vary: Accept-Encoding` ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="590af-167">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="590af-168">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="590af-168">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="590af-169">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-169">The sample illustrates:</span></span>

* <span data-ttu-id="590af-170">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="590af-170">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="590af-171">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="590af-171">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="590af-172">[パッケージ]</span><span class="sxs-lookup"><span data-stu-id="590af-172">Package</span></span>

<span data-ttu-id="590af-173">応答圧縮ミドルウェアは、AspNetCore アプリ ASP.NET Core に暗黙的に含まれる[ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="590af-173">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="configuration"></a><span data-ttu-id="590af-174">構成</span><span class="sxs-lookup"><span data-stu-id="590af-174">Configuration</span></span>

<span data-ttu-id="590af-175">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="590af-175">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="590af-176">注:</span><span class="sxs-lookup"><span data-stu-id="590af-176">Notes:</span></span>

* <span data-ttu-id="590af-177">応答を圧縮するミドルウェアの前に `app.UseResponseCompression` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-177">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="590af-178">詳細については、<xref:fundamentals/middleware/index#middleware-order> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-178">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="590af-179">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="590af-179">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="590af-180">`Accept-Encoding` ヘッダーを使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-180">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="590af-181">`Content-Encoding` ヘッダーと `Vary` ヘッダーが応答に存在しません。</span><span class="sxs-lookup"><span data-stu-id="590af-181">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="590af-184">`Accept-Encoding: br` ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-184">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="590af-185">応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="590af-185">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="590af-189">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-189">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="590af-190">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-190">Brotli Compression Provider</span></span>

<span data-ttu-id="590af-191">[Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> を使用します。</span><span class="sxs-lookup"><span data-stu-id="590af-191">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="590af-192"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590af-192">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="590af-193">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-193">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="590af-194">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="590af-194">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="590af-195">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="590af-195">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="590af-196">圧縮プロバイダーが明示的に追加されている場合は、Brotoli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-196">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="590af-197">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="590af-197">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="590af-198">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-198">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="590af-199">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="590af-199">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="590af-200">Compression Level</span><span class="sxs-lookup"><span data-stu-id="590af-200">Compression Level</span></span> | <span data-ttu-id="590af-201">説明</span><span class="sxs-lookup"><span data-stu-id="590af-201">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="590af-202">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-202">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-203">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-203">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="590af-204">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-204">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-205">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="590af-205">No compression should be performed.</span></span> |
| [<span data-ttu-id="590af-206">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-206">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-207">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-207">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="590af-208">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-208">Gzip Compression Provider</span></span>

<span data-ttu-id="590af-209"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> を使用して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="590af-209">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="590af-210"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590af-210">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="590af-211">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-211">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="590af-212">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="590af-212">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="590af-213">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="590af-213">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="590af-214">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-214">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="590af-215">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="590af-215">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="590af-216">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-216">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="590af-217">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="590af-217">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="590af-218">Compression Level</span><span class="sxs-lookup"><span data-stu-id="590af-218">Compression Level</span></span> | <span data-ttu-id="590af-219">説明</span><span class="sxs-lookup"><span data-stu-id="590af-219">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="590af-220">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-220">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-221">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-221">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="590af-222">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-222">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-223">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="590af-223">No compression should be performed.</span></span> |
| [<span data-ttu-id="590af-224">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-224">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-225">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-225">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="590af-226">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-226">Custom providers</span></span>

<span data-ttu-id="590af-227"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>を使用して、カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="590af-227">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="590af-228">この `ICompressionProvider` が生成するコンテンツエンコーディングを表す <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>。</span><span class="sxs-lookup"><span data-stu-id="590af-228">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="590af-229">ミドルウェアは、この情報を使用して、要求の `Accept-Encoding` ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="590af-229">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="590af-230">このサンプルアプリを使用すると、クライアントは `Accept-Encoding: mycustomcompression` ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="590af-230">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="590af-231">ミドルウェアは、カスタム圧縮実装を使用して、`Content-Encoding: mycustomcompression` ヘッダーで応答を返します。</span><span class="sxs-lookup"><span data-stu-id="590af-231">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="590af-232">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-232">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


<span data-ttu-id="590af-233">`Accept-Encoding: mycustomcompression` ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-233">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="590af-234">応答には、`Vary` ヘッダーと `Content-Encoding` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="590af-234">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="590af-235">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="590af-235">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="590af-236">このサンプルの `CustomCompressionProvider` クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="590af-236">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="590af-237">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="590af-237">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="590af-240">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="590af-240">MIME types</span></span>

<span data-ttu-id="590af-241">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="590af-241">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="590af-242">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="590af-242">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="590af-243">`text/*` などのワイルドカード MIME の種類はサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="590af-243">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="590af-244">このサンプルアプリでは、`image/svg+xml` 用の MIME の種類を追加し、ASP.NET Core バナー画像 (*横断幕*) を圧縮して機能させることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-244">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="590af-245">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="590af-245">Compression with secure protocol</span></span>

<span data-ttu-id="590af-246">セキュリティで保護された接続での圧縮された応答は、`EnableForHttps` オプションを使用して制御できます。これは既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="590af-246">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="590af-247">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-247">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="590af-248">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="590af-248">Adding the Vary header</span></span>

<span data-ttu-id="590af-249">`Accept-Encoding` ヘッダーに基づいて応答を圧縮する場合は、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-249">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="590af-250">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、`Vary` ヘッダーが `Accept-Encoding` 値と共に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-250">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="590af-251">ASP.NET Core 2.0 以降では、応答が圧縮されるときに、ミドルウェアによって `Vary` ヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-251">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="590af-252">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="590af-252">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="590af-253">要求が Nginx によってプロキシされると、`Accept-Encoding` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-253">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="590af-254">`Accept-Encoding` ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="590af-254">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="590af-255">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-255">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="590af-256">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="590af-256">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="590af-257">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="590af-257">Working with IIS dynamic compression</span></span>

<span data-ttu-id="590af-258">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="590af-258">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="590af-259">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-259">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="590af-260">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="590af-260">Troubleshooting</span></span>

<span data-ttu-id="590af-261">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して、`Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-261">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="590af-262">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="590af-262">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="590af-263">`Accept-Encoding` ヘッダーには、`br`、`gzip`、`*`、または独自に作成したカスタム圧縮プロバイダーに一致するカスタムエンコーディングの値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="590af-263">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="590af-264">値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-264">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="590af-265">MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-265">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="590af-266">要求に `Content-Range` ヘッダーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-266">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="590af-267">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-267">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="590af-268">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="590af-268">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="590af-269">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="590af-269">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="590af-270">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="590af-270">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="590af-271">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="590af-271">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="590af-272">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="590af-272">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="590af-273">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="590af-273">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="590af-274">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="590af-274">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="590af-275">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="590af-275">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="590af-276">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="590af-276">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="590af-277">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="590af-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="590af-278">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="590af-278">When to use Response Compression Middleware</span></span>

<span data-ttu-id="590af-279">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="590af-279">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="590af-280">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="590af-280">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="590af-281">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="590af-281">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="590af-282">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="590af-282">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="590af-283">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="590af-283">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="590af-284">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="590af-284">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="590af-285">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="590af-285">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="590af-286">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="590af-286">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="590af-287">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="590af-287">Hosting directly on:</span></span>
  * <span data-ttu-id="590af-288">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="590af-288">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="590af-289">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="590af-289">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="590af-290">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="590af-290">Response compression</span></span>

<span data-ttu-id="590af-291">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-291">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="590af-292">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="590af-292">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="590af-293">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-293">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="590af-294">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="590af-294">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="590af-295">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="590af-295">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="590af-296">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-296">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="590af-297">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に `Accept-Encoding` ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-297">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="590af-298">サーバーは、圧縮されたコンテンツを送信するときに、圧縮された応答のエンコード方法に関する情報を `Content-Encoding` ヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-298">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="590af-299">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="590af-299">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="590af-300">ヘッダー値の `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="590af-300">`Accept-Encoding` header values</span></span> | <span data-ttu-id="590af-301">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="590af-301">Middleware Supported</span></span> | <span data-ttu-id="590af-302">説明</span><span class="sxs-lookup"><span data-stu-id="590af-302">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="590af-303">可 (既定)</span><span class="sxs-lookup"><span data-stu-id="590af-303">Yes (default)</span></span>        | [<span data-ttu-id="590af-304">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="590af-304">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="590af-305">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-305">No</span></span>                   | [<span data-ttu-id="590af-306">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="590af-306">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="590af-307">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-307">No</span></span>                   | [<span data-ttu-id="590af-308">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="590af-308">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="590af-309">はい</span><span class="sxs-lookup"><span data-stu-id="590af-309">Yes</span></span>                  | [<span data-ttu-id="590af-310">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="590af-310">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="590af-311">はい</span><span class="sxs-lookup"><span data-stu-id="590af-311">Yes</span></span>                  | <span data-ttu-id="590af-312">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-312">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="590af-313">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-313">No</span></span>                   | [<span data-ttu-id="590af-314">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="590af-314">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="590af-315">はい</span><span class="sxs-lookup"><span data-stu-id="590af-315">Yes</span></span>                  | <span data-ttu-id="590af-316">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="590af-316">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="590af-317">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-317">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="590af-318">ミドルウェアを使用すると、カスタム `Accept-Encoding` ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="590af-318">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="590af-319">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-319">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="590af-320">ミドルウェアは、圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue、`q`) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="590af-320">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="590af-321">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-321">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="590af-322">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="590af-322">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="590af-323">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-323">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="590af-324">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="590af-324">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="590af-325">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="590af-325">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="590af-326">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="590af-326">Header</span></span>             | <span data-ttu-id="590af-327">役割</span><span class="sxs-lookup"><span data-stu-id="590af-327">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="590af-328">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-328">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="590af-329">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="590af-329">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="590af-330">圧縮が発生すると、応答が圧縮されるときに本文の内容が変更されるため、`Content-Length` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-330">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="590af-331">圧縮が行われると、本文の内容が変更され、ハッシュが有効でなくなったため、`Content-MD5` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-331">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="590af-332">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="590af-332">Specifies the MIME type of the content.</span></span> <span data-ttu-id="590af-333">すべての応答は、その `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-333">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="590af-334">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="590af-334">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="590af-335">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-335">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="590af-336">`Accept-Encoding` の値を持つサーバーからクライアントとプロキシに送信された場合、`Vary` ヘッダーは、要求の `Accept-Encoding` ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="590af-336">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="590af-337">`Vary: Accept-Encoding` ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="590af-337">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="590af-338">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="590af-338">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="590af-339">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-339">The sample illustrates:</span></span>

* <span data-ttu-id="590af-340">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="590af-340">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="590af-341">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="590af-341">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="590af-342">[パッケージ]</span><span class="sxs-lookup"><span data-stu-id="590af-342">Package</span></span>

<span data-ttu-id="590af-343">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="590af-343">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="590af-344">構成</span><span class="sxs-lookup"><span data-stu-id="590af-344">Configuration</span></span>

<span data-ttu-id="590af-345">次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="590af-345">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

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

<span data-ttu-id="590af-346">注:</span><span class="sxs-lookup"><span data-stu-id="590af-346">Notes:</span></span>

* <span data-ttu-id="590af-347">応答を圧縮するミドルウェアの前に `app.UseResponseCompression` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-347">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="590af-348">詳細については、<xref:fundamentals/middleware/index#middleware-order> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-348">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="590af-349">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="590af-349">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="590af-350">`Accept-Encoding` ヘッダーを使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-350">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="590af-351">`Content-Encoding` ヘッダーと `Vary` ヘッダーが応答に存在しません。</span><span class="sxs-lookup"><span data-stu-id="590af-351">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="590af-354">`Accept-Encoding: br` ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-354">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="590af-355">応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="590af-355">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示されます。](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a><span data-ttu-id="590af-359">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-359">Providers</span></span>

### <a name="brotli-compression-provider"></a><span data-ttu-id="590af-360">Brotli 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-360">Brotli Compression Provider</span></span>

<span data-ttu-id="590af-361">[Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> を使用します。</span><span class="sxs-lookup"><span data-stu-id="590af-361">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="590af-362"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590af-362">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="590af-363">既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-363">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="590af-364">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="590af-364">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="590af-365">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="590af-365">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="590af-366">圧縮プロバイダーが明示的に追加されている場合は、Brotoli 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-366">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="590af-367">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="590af-367">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="590af-368">Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-368">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="590af-369">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="590af-369">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="590af-370">Compression Level</span><span class="sxs-lookup"><span data-stu-id="590af-370">Compression Level</span></span> | <span data-ttu-id="590af-371">説明</span><span class="sxs-lookup"><span data-stu-id="590af-371">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="590af-372">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-372">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-373">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-373">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="590af-374">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-374">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-375">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="590af-375">No compression should be performed.</span></span> |
| [<span data-ttu-id="590af-376">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-376">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-377">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-377">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="590af-378">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-378">Gzip Compression Provider</span></span>

<span data-ttu-id="590af-379"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> を使用して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="590af-379">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="590af-380"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590af-380">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="590af-381">Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-381">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="590af-382">Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。</span><span class="sxs-lookup"><span data-stu-id="590af-382">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="590af-383">Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。</span><span class="sxs-lookup"><span data-stu-id="590af-383">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="590af-384">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-384">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="590af-385">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="590af-385">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="590af-386">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-386">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="590af-387">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="590af-387">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="590af-388">Compression Level</span><span class="sxs-lookup"><span data-stu-id="590af-388">Compression Level</span></span> | <span data-ttu-id="590af-389">説明</span><span class="sxs-lookup"><span data-stu-id="590af-389">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="590af-390">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-390">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-391">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-391">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="590af-392">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-392">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-393">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="590af-393">No compression should be performed.</span></span> |
| [<span data-ttu-id="590af-394">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-394">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-395">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-395">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="590af-396">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-396">Custom providers</span></span>

<span data-ttu-id="590af-397"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>を使用して、カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="590af-397">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="590af-398">この `ICompressionProvider` が生成するコンテンツエンコーディングを表す <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>。</span><span class="sxs-lookup"><span data-stu-id="590af-398">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="590af-399">ミドルウェアは、この情報を使用して、要求の `Accept-Encoding` ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="590af-399">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="590af-400">このサンプルアプリを使用すると、クライアントは `Accept-Encoding: mycustomcompression` ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="590af-400">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="590af-401">ミドルウェアは、カスタム圧縮実装を使用して、`Content-Encoding: mycustomcompression` ヘッダーで応答を返します。</span><span class="sxs-lookup"><span data-stu-id="590af-401">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="590af-402">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-402">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="590af-403">`Accept-Encoding: mycustomcompression` ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-403">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="590af-404">応答には、`Vary` ヘッダーと `Content-Encoding` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="590af-404">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="590af-405">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="590af-405">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="590af-406">このサンプルの `CustomCompressionProvider` クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="590af-406">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="590af-407">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="590af-407">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="590af-410">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="590af-410">MIME types</span></span>

<span data-ttu-id="590af-411">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="590af-411">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="590af-412">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="590af-412">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="590af-413">`text/*` などのワイルドカード MIME の種類はサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="590af-413">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="590af-414">このサンプルアプリでは、`image/svg+xml` 用の MIME の種類を追加し、ASP.NET Core バナー画像 (*横断幕*) を圧縮して機能させることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-414">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="590af-415">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="590af-415">Compression with secure protocol</span></span>

<span data-ttu-id="590af-416">セキュリティで保護された接続での圧縮された応答は、`EnableForHttps` オプションを使用して制御できます。これは既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="590af-416">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="590af-417">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-417">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="590af-418">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="590af-418">Adding the Vary header</span></span>

<span data-ttu-id="590af-419">`Accept-Encoding` ヘッダーに基づいて応答を圧縮する場合は、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-419">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="590af-420">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、`Vary` ヘッダーが `Accept-Encoding` 値と共に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-420">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="590af-421">ASP.NET Core 2.0 以降では、応答が圧縮されるときに、ミドルウェアによって `Vary` ヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-421">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="590af-422">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="590af-422">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="590af-423">要求が Nginx によってプロキシされると、`Accept-Encoding` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-423">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="590af-424">`Accept-Encoding` ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="590af-424">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="590af-425">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-425">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="590af-426">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="590af-426">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="590af-427">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="590af-427">Working with IIS dynamic compression</span></span>

<span data-ttu-id="590af-428">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="590af-428">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="590af-429">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-429">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="590af-430">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="590af-430">Troubleshooting</span></span>

<span data-ttu-id="590af-431">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して、`Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-431">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="590af-432">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="590af-432">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="590af-433">`Accept-Encoding` ヘッダーには、`br`、`gzip`、`*`、または独自に作成したカスタム圧縮プロバイダーに一致するカスタムエンコーディングの値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="590af-433">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="590af-434">値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-434">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="590af-435">MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-435">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="590af-436">要求に `Content-Range` ヘッダーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-436">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="590af-437">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-437">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="590af-438">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="590af-438">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="590af-439">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="590af-439">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="590af-440">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="590af-440">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="590af-441">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="590af-441">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="590af-442">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="590af-442">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="590af-443">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="590af-443">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="590af-444">ネットワーク帯域幅はリソースが限られています。</span><span class="sxs-lookup"><span data-stu-id="590af-444">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="590af-445">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="590af-445">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="590af-446">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="590af-446">One way to reduce payload sizes is to compress an app's responses.</span></span>

<span data-ttu-id="590af-447">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="590af-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="590af-448">応答圧縮ミドルウェアを使用する場合</span><span class="sxs-lookup"><span data-stu-id="590af-448">When to use Response Compression Middleware</span></span>

<span data-ttu-id="590af-449">IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。</span><span class="sxs-lookup"><span data-stu-id="590af-449">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="590af-450">ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="590af-450">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="590af-451">現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。</span><span class="sxs-lookup"><span data-stu-id="590af-451">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="590af-452">次の場合に応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="590af-452">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="590af-453">次のサーバーベースの圧縮テクノロジは使用できません。</span><span class="sxs-lookup"><span data-stu-id="590af-453">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="590af-454">IIS 動的圧縮モジュール</span><span class="sxs-lookup"><span data-stu-id="590af-454">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="590af-455">Apache mod_deflate モジュール</span><span class="sxs-lookup"><span data-stu-id="590af-455">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="590af-456">Nginx 圧縮と圧縮解除</span><span class="sxs-lookup"><span data-stu-id="590af-456">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="590af-457">直接ホストする:</span><span class="sxs-lookup"><span data-stu-id="590af-457">Hosting directly on:</span></span>
  * <span data-ttu-id="590af-458">Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)</span><span class="sxs-lookup"><span data-stu-id="590af-458">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="590af-459">Kestrel サーバー</span><span class="sxs-lookup"><span data-stu-id="590af-459">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="590af-460">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="590af-460">Response compression</span></span>

<span data-ttu-id="590af-461">通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-461">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="590af-462">ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。</span><span class="sxs-lookup"><span data-stu-id="590af-462">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="590af-463">PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-463">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="590af-464">ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="590af-464">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="590af-465">ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="590af-465">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="590af-466">小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-466">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="590af-467">クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に `Accept-Encoding` ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-467">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="590af-468">サーバーは、圧縮されたコンテンツを送信するときに、圧縮された応答のエンコード方法に関する情報を `Content-Encoding` ヘッダーに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-468">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="590af-469">次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。</span><span class="sxs-lookup"><span data-stu-id="590af-469">Content encoding designations supported by the middleware are shown in the following table.</span></span>

| <span data-ttu-id="590af-470">ヘッダー値の `Accept-Encoding`</span><span class="sxs-lookup"><span data-stu-id="590af-470">`Accept-Encoding` header values</span></span> | <span data-ttu-id="590af-471">サポートされているミドルウェア</span><span class="sxs-lookup"><span data-stu-id="590af-471">Middleware Supported</span></span> | <span data-ttu-id="590af-472">説明</span><span class="sxs-lookup"><span data-stu-id="590af-472">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="590af-473">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-473">No</span></span>                   | [<span data-ttu-id="590af-474">Brotli 圧縮データ形式</span><span class="sxs-lookup"><span data-stu-id="590af-474">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="590af-475">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-475">No</span></span>                   | [<span data-ttu-id="590af-476">圧縮データ形式の DEFLATE</span><span class="sxs-lookup"><span data-stu-id="590af-476">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="590af-477">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-477">No</span></span>                   | [<span data-ttu-id="590af-478">W3C の効率的な XML インターチェンジ</span><span class="sxs-lookup"><span data-stu-id="590af-478">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="590af-479">可 (既定)</span><span class="sxs-lookup"><span data-stu-id="590af-479">Yes (default)</span></span>        | [<span data-ttu-id="590af-480">Gzip ファイル形式</span><span class="sxs-lookup"><span data-stu-id="590af-480">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="590af-481">はい</span><span class="sxs-lookup"><span data-stu-id="590af-481">Yes</span></span>                  | <span data-ttu-id="590af-482">"エンコードなし" 識別子: 応答をエンコードすることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-482">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="590af-483">いいえ</span><span class="sxs-lookup"><span data-stu-id="590af-483">No</span></span>                   | [<span data-ttu-id="590af-484">Java アーカイブのネットワーク転送形式</span><span class="sxs-lookup"><span data-stu-id="590af-484">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="590af-485">はい</span><span class="sxs-lookup"><span data-stu-id="590af-485">Yes</span></span>                  | <span data-ttu-id="590af-486">明示的に要求されていない利用可能なコンテンツエンコーディング</span><span class="sxs-lookup"><span data-stu-id="590af-486">Any available content encoding not explicitly requested</span></span> |

<span data-ttu-id="590af-487">詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-487">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="590af-488">ミドルウェアを使用すると、カスタム `Accept-Encoding` ヘッダー値の圧縮プロバイダーを追加できます。</span><span class="sxs-lookup"><span data-stu-id="590af-488">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="590af-489">詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-489">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="590af-490">ミドルウェアは、圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue、`q`) の重み付けに対応できます。</span><span class="sxs-lookup"><span data-stu-id="590af-490">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="590af-491">詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-491">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="590af-492">圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="590af-492">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="590af-493">このコンテキストの*効果*は、圧縮後の出力のサイズを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-493">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="590af-494">最小サイズは*最適*な圧縮によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="590af-494">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="590af-495">次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。</span><span class="sxs-lookup"><span data-stu-id="590af-495">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="590af-496">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="590af-496">Header</span></span>             | <span data-ttu-id="590af-497">役割</span><span class="sxs-lookup"><span data-stu-id="590af-497">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="590af-498">クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-498">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="590af-499">ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="590af-499">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="590af-500">圧縮が発生すると、応答が圧縮されるときに本文の内容が変更されるため、`Content-Length` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-500">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="590af-501">圧縮が行われると、本文の内容が変更され、ハッシュが有効でなくなったため、`Content-MD5` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-501">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="590af-502">コンテンツの MIME の種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="590af-502">Specifies the MIME type of the content.</span></span> <span data-ttu-id="590af-503">すべての応答は、その `Content-Type`を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-503">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="590af-504">ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="590af-504">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="590af-505">ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-505">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="590af-506">`Accept-Encoding` の値を持つサーバーからクライアントとプロキシに送信された場合、`Vary` ヘッダーは、要求の `Accept-Encoding` ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。</span><span class="sxs-lookup"><span data-stu-id="590af-506">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="590af-507">`Vary: Accept-Encoding` ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。</span><span class="sxs-lookup"><span data-stu-id="590af-507">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="590af-508">[サンプルアプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。</span><span class="sxs-lookup"><span data-stu-id="590af-508">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="590af-509">このサンプルでは、次のことを示します。</span><span class="sxs-lookup"><span data-stu-id="590af-509">The sample illustrates:</span></span>

* <span data-ttu-id="590af-510">Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。</span><span class="sxs-lookup"><span data-stu-id="590af-510">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="590af-511">圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="590af-511">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="590af-512">[パッケージ]</span><span class="sxs-lookup"><span data-stu-id="590af-512">Package</span></span>

<span data-ttu-id="590af-513">ミドルウェアをプロジェクトに含めるには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。</span><span class="sxs-lookup"><span data-stu-id="590af-513">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

## <a name="configuration"></a><span data-ttu-id="590af-514">構成</span><span class="sxs-lookup"><span data-stu-id="590af-514">Configuration</span></span>

<span data-ttu-id="590af-515">次のコードは、既定の MIME の種類と[Gzip 圧縮プロバイダー](#gzip-compression-provider)の応答圧縮ミドルウェアを有効にする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="590af-515">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="590af-516">注:</span><span class="sxs-lookup"><span data-stu-id="590af-516">Notes:</span></span>

* <span data-ttu-id="590af-517">応答を圧縮するミドルウェアの前に `app.UseResponseCompression` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-517">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="590af-518">詳細については、<xref:fundamentals/middleware/index#middleware-order> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-518">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="590af-519">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。</span><span class="sxs-lookup"><span data-stu-id="590af-519">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="590af-520">`Accept-Encoding` ヘッダーを使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-520">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="590af-521">`Content-Encoding` ヘッダーと `Vary` ヘッダーが応答に存在しません。</span><span class="sxs-lookup"><span data-stu-id="590af-521">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。](response-compression/_static/request-uncompressed.png)

<span data-ttu-id="590af-524">`Accept-Encoding: gzip` ヘッダーを使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-524">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="590af-525">応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="590af-525">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと gzip の値で表示されます。](response-compression/_static/request-compressed.png)

## <a name="providers"></a><span data-ttu-id="590af-529">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-529">Providers</span></span>

### <a name="gzip-compression-provider"></a><span data-ttu-id="590af-530">Gzip 圧縮プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-530">Gzip Compression Provider</span></span>

<span data-ttu-id="590af-531"><xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> を使用して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="590af-531">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="590af-532"><xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="590af-532">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="590af-533">Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-533">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="590af-534">クライアントで Gzip 圧縮がサポートされている場合、圧縮の既定値は Gzip です。</span><span class="sxs-lookup"><span data-stu-id="590af-534">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="590af-535">圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-535">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="590af-536">圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>に設定します。</span><span class="sxs-lookup"><span data-stu-id="590af-536">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="590af-537">Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-537">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="590af-538">最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="590af-538">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="590af-539">Compression Level</span><span class="sxs-lookup"><span data-stu-id="590af-539">Compression Level</span></span> | <span data-ttu-id="590af-540">説明</span><span class="sxs-lookup"><span data-stu-id="590af-540">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="590af-541">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-541">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-542">圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-542">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="590af-543">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-543">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-544">圧縮は実行されません。</span><span class="sxs-lookup"><span data-stu-id="590af-544">No compression should be performed.</span></span> |
| [<span data-ttu-id="590af-545">CompressionLevel</span><span class="sxs-lookup"><span data-stu-id="590af-545">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="590af-546">圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-546">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="590af-547">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="590af-547">Custom providers</span></span>

<span data-ttu-id="590af-548"><xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>を使用して、カスタム圧縮実装を作成します。</span><span class="sxs-lookup"><span data-stu-id="590af-548">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="590af-549">この `ICompressionProvider` が生成するコンテンツエンコーディングを表す <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>。</span><span class="sxs-lookup"><span data-stu-id="590af-549">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="590af-550">ミドルウェアは、この情報を使用して、要求の `Accept-Encoding` ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="590af-550">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="590af-551">このサンプルアプリを使用すると、クライアントは `Accept-Encoding: mycustomcompression` ヘッダーを使用して要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="590af-551">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="590af-552">ミドルウェアは、カスタム圧縮実装を使用して、`Content-Encoding: mycustomcompression` ヘッダーで応答を返します。</span><span class="sxs-lookup"><span data-stu-id="590af-552">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="590af-553">カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-553">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

<span data-ttu-id="590af-554">`Accept-Encoding: mycustomcompression` ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="590af-554">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="590af-555">応答には、`Vary` ヘッダーと `Content-Encoding` ヘッダーが存在します。</span><span class="sxs-lookup"><span data-stu-id="590af-555">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="590af-556">このサンプルでは、応答本文 (表示されません) は圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="590af-556">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="590af-557">このサンプルの `CustomCompressionProvider` クラスには圧縮実装がありません。</span><span class="sxs-lookup"><span data-stu-id="590af-557">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="590af-558">ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。</span><span class="sxs-lookup"><span data-stu-id="590af-558">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="590af-561">MIME タイプ</span><span class="sxs-lookup"><span data-stu-id="590af-561">MIME types</span></span>

<span data-ttu-id="590af-562">ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。</span><span class="sxs-lookup"><span data-stu-id="590af-562">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="590af-563">応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。</span><span class="sxs-lookup"><span data-stu-id="590af-563">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="590af-564">`text/*` などのワイルドカード MIME の種類はサポートされていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="590af-564">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="590af-565">このサンプルアプリでは、`image/svg+xml` 用の MIME の種類を追加し、ASP.NET Core バナー画像 (*横断幕*) を圧縮して機能させることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-565">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="590af-566">セキュリティで保護されたプロトコルを使用した圧縮</span><span class="sxs-lookup"><span data-stu-id="590af-566">Compression with secure protocol</span></span>

<span data-ttu-id="590af-567">セキュリティで保護された接続での圧縮された応答は、`EnableForHttps` オプションを使用して制御できます。これは既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="590af-567">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="590af-568">動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-568">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="590af-569">Vary ヘッダーを追加する</span><span class="sxs-lookup"><span data-stu-id="590af-569">Adding the Vary header</span></span>

<span data-ttu-id="590af-570">`Accept-Encoding` ヘッダーに基づいて応答を圧縮する場合は、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="590af-570">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="590af-571">複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、`Vary` ヘッダーが `Accept-Encoding` 値と共に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-571">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="590af-572">ASP.NET Core 2.0 以降では、応答が圧縮されるときに、ミドルウェアによって `Vary` ヘッダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="590af-572">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="590af-573">Nginx リバースプロキシの背後にあるミドルウェアの問題</span><span class="sxs-lookup"><span data-stu-id="590af-573">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="590af-574">要求が Nginx によってプロキシされると、`Accept-Encoding` ヘッダーが削除されます。</span><span class="sxs-lookup"><span data-stu-id="590af-574">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="590af-575">`Accept-Encoding` ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。</span><span class="sxs-lookup"><span data-stu-id="590af-575">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="590af-576">詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-576">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="590af-577">この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="590af-577">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="590af-578">IIS 動的圧縮の使用</span><span class="sxs-lookup"><span data-stu-id="590af-578">Working with IIS dynamic compression</span></span>

<span data-ttu-id="590af-579">アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="590af-579">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="590af-580">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="590af-580">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="590af-581">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="590af-581">Troubleshooting</span></span>

<span data-ttu-id="590af-582">[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して、`Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="590af-582">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="590af-583">既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="590af-583">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

* <span data-ttu-id="590af-584">`Accept-Encoding` ヘッダーには、`gzip`、`*`、またはユーザーが設定したカスタム圧縮プロバイダーに一致するカスタムエンコードの値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="590af-584">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="590af-585">値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-585">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="590af-586">MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-586">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="590af-587">要求に `Content-Range` ヘッダーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="590af-587">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="590af-588">応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="590af-588">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="590af-589">*セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*</span><span class="sxs-lookup"><span data-stu-id="590af-589">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="590af-590">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="590af-590">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="590af-591">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="590af-591">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="590af-592">RFC 7231 セクション 3.1.2.1: Content Codings</span><span class="sxs-lookup"><span data-stu-id="590af-592">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="590af-593">RFC 7230 セクション 4.2.3: Gzip コーディング</span><span class="sxs-lookup"><span data-stu-id="590af-593">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="590af-594">GZIP ファイル形式仕様バージョン4.3</span><span class="sxs-lookup"><span data-stu-id="590af-594">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
