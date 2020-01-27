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
# <a name="response-compression-in-aspnet-core"></a>ASP.NET Core での応答圧縮

作成者: [Luke Latham](https://github.com/guardrex)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

ネットワーク帯域幅はリソースが限られています。 通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。 ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。

## <a name="when-to-use-response-compression-middleware"></a>応答圧縮ミドルウェアを使用する場合

IIS、Apache、または Nginx のサーバーベースの応答圧縮テクノロジを使用します。 ミドルウェアのパフォーマンスがサーバーモジュールのパフォーマンスと一致しない場合があります。 現在、 [http.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[kestrel](xref:fundamentals/servers/kestrel)サーバーでは、組み込みの圧縮サポートは提供されていません。

次の場合に応答圧縮ミドルウェアを使用します。

* 次のサーバーベースの圧縮テクノロジは使用できません。
  * [IIS 動的圧縮モジュール](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate モジュール](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx 圧縮と圧縮解除](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 直接ホストする:
  * Http.sys[サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)
  * [Kestrel サーバー](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>応答圧縮

通常、ネイティブに圧縮されていない応答は、応答圧縮の恩恵を受けることができます。 ネイティブに圧縮されていない応答には、通常、CSS、JavaScript、HTML、XML、JSON が含まれます。 PNG ファイルなど、ネイティブに圧縮されたアセットを圧縮することはできません。 ネイティブ圧縮応答をさらに圧縮しようとすると、サイズが小さく、転送時間が短縮されると、圧縮の処理に要する時間が大きくなります。 ファイルの内容や圧縮の効率によっては、約150-1000 バイトを下回るファイルを圧縮しないようにしてください。 小さいファイルを圧縮すると、圧縮されていないファイルよりも大きな圧縮ファイルが生成される可能性があります。

クライアントは、圧縮されたコンテンツを処理できる場合、要求と共に `Accept-Encoding` ヘッダーを送信することによって、その機能をサーバーに通知する必要があります。 サーバーは、圧縮されたコンテンツを送信するときに、圧縮された応答のエンコード方法に関する情報を `Content-Encoding` ヘッダーに含める必要があります。 次の表に、ミドルウェアでサポートされているコンテンツエンコードの表記を示します。

::: moniker range=">= aspnetcore-2.2"

| ヘッダー値の `Accept-Encoding` | サポートされているミドルウェア | 説明 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | 可 (既定)        | [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | いいえ                   | [圧縮データ形式の DEFLATE](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | いいえ                   | [W3C の効率的な XML インターチェンジ](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | ○                  | [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | ○                  | "エンコードなし" 識別子: 応答をエンコードすることはできません。 |
| `pack200-gzip`                  | いいえ                   | [Java アーカイブのネットワーク転送形式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | ○                  | 明示的に要求されていない利用可能なコンテンツエンコーディング |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| ヘッダー値の `Accept-Encoding` | サポートされているミドルウェア | 説明 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | いいえ                   | [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | いいえ                   | [圧縮データ形式の DEFLATE](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | いいえ                   | [W3C の効率的な XML インターチェンジ](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | 可 (既定)        | [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | ○                  | "エンコードなし" 識別子: 応答をエンコードすることはできません。 |
| `pack200-gzip`                  | いいえ                   | [Java アーカイブのネットワーク転送形式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | ○                  | 明示的に要求されていない利用可能なコンテンツエンコーディング |

::: moniker-end

詳細については、「 [IANA 公式コンテンツコーディングリスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)」を参照してください。

ミドルウェアを使用すると、カスタム `Accept-Encoding` ヘッダー値の圧縮プロバイダーを追加できます。 詳細については、以下の「[カスタムプロバイダー](#custom-providers) 」を参照してください。

ミドルウェアは、圧縮スキームの優先順位を決定するためにクライアントから送信されるときに、品質の値 (qvalue、`q`) の重み付けに対応できます。 詳細については、「 [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)」を参照してください。

圧縮アルゴリズムは、圧縮速度と圧縮の効果のトレードオフの影響を受けます。 このコンテキストの*効果*は、圧縮後の出力のサイズを示します。 最小サイズは*最適*な圧縮によって実現されます。

次の表では、圧縮されたコンテンツの要求、送信、キャッシュ、および受信に関連するヘッダーについて説明します。

| Header             | 役割 |
| ------------------ | ---- |
| `Accept-Encoding`  | クライアントからサーバーに送信され、クライアントが使用できるコンテンツエンコーディングスキームを示します。 |
| `Content-Encoding` | ペイロード内のコンテンツのエンコードを示すために、サーバーからクライアントに送信されます。 |
| `Content-Length`   | 圧縮が発生すると、応答が圧縮されるときに本文の内容が変更されるため、`Content-Length` ヘッダーが削除されます。 |
| `Content-MD5`      | 圧縮が行われると、本文の内容が変更され、ハッシュが有効でなくなったため、`Content-MD5` ヘッダーが削除されます。 |
| `Content-Type`     | コンテンツの MIME の種類を指定します。 すべての応答は、その `Content-Type`を指定する必要があります。 ミドルウェアは、この値をチェックして、応答を圧縮する必要があるかどうかを判断します。 ミドルウェアはエンコードできる既定の[mime の種類](#mime-types)のセットを指定しますが、mime の種類を置き換えたり追加したりすることができます。 |
| `Vary`             | `Accept-Encoding` の値を持つサーバーからクライアントとプロキシに送信された場合、`Vary` ヘッダーは、要求の `Accept-Encoding` ヘッダーの値に基づいて応答をキャッシュ (変更) する必要があることをクライアントまたはプロキシに示します。 `Vary: Accept-Encoding` ヘッダーを使用してコンテンツを返すと、圧縮された応答と圧縮されていない応答の両方が個別にキャッシュされることになります。 |

[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)を使用して、応答圧縮ミドルウェアの機能を検証します。 このサンプルでは、次のことを示します。

* Gzip およびカスタム圧縮プロバイダーを使用したアプリ応答の圧縮。
* 圧縮する mime の種類の既定の一覧に MIME の種類を追加する方法について説明します。

## <a name="package"></a>[パッケージ]

::: moniker range=">= aspnetcore-3.0"

応答圧縮ミドルウェアは、ASP.NET Core アプリに暗黙的に含まれる [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) パッケージによって提供されます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ミドルウェアをプロジェクトに含めるには、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)への参照を追加します。これには、 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージが含まれています。

::: moniker-end

## <a name="configuration"></a>の構成

::: moniker range=">= aspnetcore-2.2"

次のコードは、既定の MIME の種類と圧縮プロバイダー ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider)) に対して応答圧縮ミドルウェアを有効にする方法を示しています。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

次のコードは、既定の MIME の種類と[Gzip 圧縮プロバイダー](#gzip-compression-provider)の応答圧縮ミドルウェアを有効にする方法を示しています。

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

注:

* 応答を圧縮するミドルウェアの前に `app.UseResponseCompression` を呼び出す必要があります。 詳細については、「 <xref:fundamentals/middleware/index#middleware-order>」を参照してください。
* [Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して `Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、および本文を調査します。

`Accept-Encoding` ヘッダーを使用せずにサンプルアプリに要求を送信し、応答が圧縮されていないことを確認します。 `Content-Encoding` ヘッダーと `Vary` ヘッダーが応答に存在しません。

![Fiddler ウィンドウには、要求の結果が表示され、エンコーディングヘッダーはありません。 応答が圧縮されていません。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

`Accept-Encoding: br` ヘッダー (Brotli compression) を使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。 応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。

![Fiddler ウィンドウには、要求の結果が表示されます。 Vary ヘッダーと Content-type ヘッダーが応答に追加されます。 応答は圧縮されます。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

`Accept-Encoding: gzip` ヘッダーを使用してサンプルアプリに要求を送信し、応答が圧縮されていることを確認します。 応答には、`Content-Encoding` ヘッダーと `Vary` ヘッダーが存在します。

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと gzip の値で表示されます。 Vary ヘッダーと Content-type ヘッダーが応答に追加されます。 応答は圧縮されます。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>プロバイダー

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Brotli 圧縮プロバイダー

[Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)で応答を圧縮するには、<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> を使用します。

<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。

* 既定では、Brotli 圧縮プロバイダーは、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。
* Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。 Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

圧縮プロバイダーが明示的に追加されている場合は、Brotoli 圧縮プロバイダーを追加する必要があります。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>に設定します。 Brotli 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。 最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。

| 圧縮レベル | 説明 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。 |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 圧縮は実行されません。 |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。 |

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

### <a name="gzip-compression-provider"></a>Gzip 圧縮プロバイダー

<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> を使用して、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)で応答を圧縮します。

<xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>に明示的に追加された圧縮プロバイダーがない場合は、次のようになります。

::: moniker range=">= aspnetcore-2.2"

* Gzip 圧縮プロバイダーは、既定では、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)と共に、圧縮プロバイダーの配列に追加されます。
* Brotli 圧縮データ形式がクライアントでサポートされている場合、圧縮は既定で Brotli 圧縮になります。 Brotli がクライアントでサポートされていない場合、クライアントで Gzip 圧縮がサポートされていると、圧縮の既定値は Gzip になります。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。
* クライアントで Gzip 圧縮がサポートされている場合、圧縮の既定値は Gzip です。

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

圧縮プロバイダーが明示的に追加されている場合は、Gzip 圧縮プロバイダーを追加する必要があります。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

圧縮レベルを <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>に設定します。 Gzip 圧縮プロバイダーは、既定で最も高速な圧縮レベル ([CompressionLevel](xref:System.IO.Compression.CompressionLevel)) に設定されています。これにより、圧縮率が最も高くなる可能性があります。 最も効率的な圧縮が必要な場合は、最適な圧縮のためにミドルウェアを構成します。

| 圧縮レベル | 説明 |
| ----------------- | ----------- |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 圧縮は、結果の出力が最適に圧縮されない場合でも、できるだけ早く完了する必要があります。 |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 圧縮は実行されません。 |
| [CompressionLevel](xref:System.IO.Compression.CompressionLevel) | 圧縮が完了するまでに時間がかかる場合でも、応答は最適に圧縮される必要があります。 |

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

### <a name="custom-providers"></a>カスタムプロバイダー

<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>を使用して、カスタム圧縮実装を作成します。 この `ICompressionProvider` が生成するコンテンツエンコーディングを表す <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>。 ミドルウェアは、この情報を使用して、要求の `Accept-Encoding` ヘッダーに指定されている一覧に基づいてプロバイダーを選択します。

このサンプルアプリを使用すると、クライアントは `Accept-Encoding: mycustomcompression` ヘッダーを使用して要求を送信します。 ミドルウェアは、カスタム圧縮実装を使用して、`Content-Encoding: mycustomcompression` ヘッダーで応答を返します。 カスタム圧縮実装を機能させるには、クライアントがカスタムエンコードを圧縮解除できる必要があります。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

`Accept-Encoding: mycustomcompression` ヘッダーを使用してサンプルアプリに要求を送信し、応答ヘッダーを確認します。 応答には、`Vary` ヘッダーと `Content-Encoding` ヘッダーが存在します。 このサンプルでは、応答本文 (表示されません) は圧縮されません。 このサンプルの `CustomCompressionProvider` クラスには圧縮実装がありません。 ただし、このサンプルでは、このような圧縮アルゴリズムを実装する場所を示しています。

![Fiddler ウィンドウには、要求の結果がエンコーディングヘッダーと mycustomcompression の値と共に表示されます。 Vary ヘッダーと Content-type ヘッダーが応答に追加されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME タイプ

ミドルウェアは、圧縮のために既定の MIME の種類のセットを指定します。

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

応答の圧縮ミドルウェアオプションを使用して、MIME の種類を置換または追加します。 `text/*` などのワイルドカード MIME の種類はサポートされていないことに注意してください。 このサンプルアプリでは、`image/svg+xml` 用の MIME の種類を追加し、ASP.NET Core バナー画像 (*横断幕*) を圧縮して機能させることができます。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>セキュリティで保護されたプロトコルを使用した圧縮

セキュリティで保護された接続での圧縮された応答は、`EnableForHttps` オプションを使用して制御できます。これは既定では無効になっています。 動的に生成されたページで圧縮を使用すると、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))や[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃などのセキュリティ上の問題が発生する可能性があります。

## <a name="adding-the-vary-header"></a>Vary ヘッダーを追加する

`Accept-Encoding` ヘッダーに基づいて応答を圧縮する場合は、応答の複数の圧縮バージョンと圧縮されていないバージョンが存在する可能性があります。 複数のバージョンが存在し、格納する必要があることをクライアントキャッシュとプロキシキャッシュに指示するために、`Vary` ヘッダーが `Accept-Encoding` 値と共に追加されます。 ASP.NET Core 2.0 以降では、応答が圧縮されるときに、ミドルウェアによって `Vary` ヘッダーが自動的に追加されます。

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx リバースプロキシの背後にあるミドルウェアの問題

要求が Nginx によってプロキシされると、`Accept-Encoding` ヘッダーが削除されます。 `Accept-Encoding` ヘッダーを削除すると、ミドルウェアが応答を圧縮できなくなります。 詳細については、「 [NGINX: Compression And 伸張](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)」を参照してください。 この問題は、 [Nginx (aspnet/BasicMiddleware #123) のパススルー圧縮](https://github.com/aspnet/BasicMiddleware/issues/123)によって追跡されます。

## <a name="working-with-iis-dynamic-compression"></a>IIS 動的圧縮の使用

アプリケーションに対して無効にするサーバーレベルで構成されたアクティブな IIS 動的圧縮モジュールがある場合は、 *web.config ファイルに追加して*モジュールを無効にします。 詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

[Fiddler](https://www.telerik.com/fiddler)、[消火バグ](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)などのツールを使用して、`Accept-Encoding` 要求ヘッダーを設定し、応答ヘッダー、サイズ、本文を調べることができます。 既定では、応答圧縮ミドルウェアは、次の条件を満たす応答を圧縮します。

::: moniker range=">= aspnetcore-2.2"

* `Accept-Encoding` ヘッダーには、`br`、`gzip`、`*`、または独自に作成したカスタム圧縮プロバイダーに一致するカスタムエンコーディングの値が含まれています。 値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。
* MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。
* 要求に `Content-Range` ヘッダーを含めることはできません。
* 応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。 *セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* `Accept-Encoding` ヘッダーには、`gzip`、`*`、またはユーザーが設定したカスタム圧縮プロバイダーに一致するカスタムエンコードの値が含まれています。 値を `identity` したり、品質値 (qvalue、`q`) の設定を 0 (ゼロ) にしたりすることはできません。
* MIME の種類 (`Content-Type`) を設定し、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>で構成されている MIME の種類と一致させる必要があります。
* 要求に `Content-Range` ヘッダーを含めることはできません。
* 応答圧縮ミドルウェアのオプションで secure protocol (https) が構成されている場合を除き、要求では安全でないプロトコル (http) を使用する必要があります。 *セキュリティで保護されたコンテンツの圧縮を有効にする場合は、[前述](#compression-with-secure-protocol)の危険に注意してください。*

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla Developer Network: Accept-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 セクション 3.1.2.1: Content Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 セクション 4.2.3: Gzip コーディング](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP ファイル形式仕様バージョン4.3](https://www.ietf.org/rfc/rfc1952.txt)
