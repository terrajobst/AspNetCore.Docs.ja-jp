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
# <a name="response-compression-in-aspnet-core"></a>ASP.NET Core で応答の圧縮

作成者: [Luke Latham](https://github.com/guardrex)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

ネットワーク帯域幅は、限られたリソースです。 通常、応答のサイズを小さく、アプリの応答性を多くの場合、大幅に増加します。 ペイロードのサイズを小さく 1 つの方法は、アプリの応答を圧縮します。

## <a name="when-to-use-response-compression-middleware"></a>応答圧縮ミドルウェアを使用する場合

IIS、Apache、Nginx でサーバー ベースの応答の圧縮テクノロジを使用します。 ミドルウェアのパフォーマンス一致しないことのサーバー モジュール。 [HTTP.sys サーバー](xref:fundamentals/servers/httpsys)サーバーと[Kestrel](xref:fundamentals/servers/kestrel)サーバーは、組み込みの圧縮のサポートを提供現在はありません。

したら応答圧縮ミドルウェアを使用します。

* 次のサーバー ベースの圧縮テクノロジを使用することができません。
  * [IIS 動的圧縮モジュール](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate モジュール](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx の圧縮と圧縮解除](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* 直接のホスティング。
  * [HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener)
  * [Kestrel サーバー](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>応答圧縮

通常は、ネイティブに圧縮された応答は、応答の圧縮を利用できます。 ネイティブに通常は圧縮された応答は次のとおりです。CSS、JavaScript、HTML、XML、および JSON。 PNG ファイルなどのネイティブ圧縮されたアセットを圧縮することはできません。 場合にさらに、ネイティブ圧縮された応答を圧縮しようとすると、サイズと転送時間の小さなそれ以上は低下は可能性がありますする影が薄くなって、圧縮処理にかかった時間によって。 (ファイルの内容および圧縮の効率) に応じて約 150 ~ 1000 バイトよりも小さいファイルを圧縮されていません。 圧縮されたファイルのサイズが圧縮されていないファイルよりも小さいファイルの圧縮のオーバーヘッドがあります。

クライアントが送信することによってその機能のサーバーに通知する必要があります、クライアントは、圧縮されたコンテンツを処理できますが、ときに、`Accept-Encoding`ヘッダーを要求します。 内の情報を含める必要があります、サーバーは、圧縮されたコンテンツを送信するとき、`Content-Encoding`ヘッダーを圧縮された応答をエンコードする方法。 ミドルウェアによってサポートされているコンテンツのエンコードの指定は、次の表に表示されます。

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` ヘッダーの値 | サポートされているミドルウェア | 説明 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | [はい] (既定値)        | [Brotli 圧縮データの形式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | いいえ                   | [DEFLATE 圧縮データの形式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | いいえ                   | [W3C の効率的な XML インターチェンジ](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | [はい]                  | [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | [はい]                  | 「エンコードなし」の識別子:応答をエンコードする必要があります。 |
| `pack200-gzip`                  | いいえ                   | [Java アーカイブのネットワーク転送の形式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | [はい]                  | 明示的に要求されたエンコードは、利用可能なコンテンツ |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` ヘッダーの値 | サポートされているミドルウェア | 説明 |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | いいえ                   | [Brotli 圧縮データの形式](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | いいえ                   | [DEFLATE 圧縮データの形式](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | いいえ                   | [W3C の効率的な XML インターチェンジ](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | [はい] (既定値)        | [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | [はい]                  | 「エンコードなし」の識別子:応答をエンコードする必要があります。 |
| `pack200-gzip`                  | いいえ                   | [Java アーカイブのネットワーク転送の形式](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | [はい]                  | 明示的に要求されたエンコードは、利用可能なコンテンツ |

::: moniker-end

詳細については、次を参照してください。、 [IANA 公式コンテンツ コーディング リスト](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)します。

カスタムの圧縮は追加のプロバイダーを追加することができます、ミドルウェア`Accept-Encoding`ヘッダー値。 詳細については、次を参照してください。[カスタム プロバイダー](#custom-providers)以下。

ミドルウェアが品質の値に対応できる (qvalue、 `q`) 圧縮スキームを優先するクライアントによって送信されたときに重み付けします。 詳細については、次を参照してください。 [RFC 7231。Accept-encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4)します。

圧縮アルゴリズムは、圧縮速度と、圧縮の効果のトレードオフが適用されます。 *有効性*このコンテキストの圧縮後の出力のサイズを指します。 最小サイズは、ほとんどによって実現*最適な*圧縮します。

要求に関連する、ヘッダーを送信する、キャッシュ、および圧縮されたコンテンツを受信は、次の表で説明されています。

| Header             | ロール |
| ------------------ | ---- |
| `Accept-Encoding`  | エンコードをクライアントに許容される設定内容を示すクライアントからサーバーに送信します。 |
| `Content-Encoding` | ペイロード内のコンテンツのエンコードを示したりする、サーバーからクライアントに送信します。 |
| `Content-Length`   | 圧縮が発生したときに、`Content-Length`以降本文内容の変更、応答を圧縮するときに、ヘッダーを削除します。 |
| `Content-MD5`      | 圧縮が発生したときに、`Content-MD5`ヘッダーが削除されたため、本文の内容が変更され、ハッシュが無効になっています。 |
| `Content-Type`     | コンテンツの MIME の種類を指定します。 すべての応答を指定する必要があります、`Content-Type`します。 ミドルウェアは、応答を圧縮するかどうかを判断するには、この値を確認します。 ミドルウェアのセットを指定する[既定の MIME の種類](#mime-types)をエンコードできますが、置き換えるか、MIME の種類を追加することができます。 |
| `Vary`             | 値を使用して、サーバーによって送信されると`Accept-Encoding`クライアントと、プロキシ、`Vary`ヘッダーは、クライアントまたはキャッシュするプロキシすることを示します (異なる) の値に基づいて応答、`Accept-Encoding`要求のヘッダー。 コンテンツを返すことの結果、`Vary: Accept-Encoding`ヘッダーが両方の圧縮を個別に圧縮されていない応答がキャッシュされます。 |

応答圧縮ミドルウェアの機能を試し、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples)します。 このサンプルを示しています。

* Gzip とカスタム圧縮プロバイダーを使用してアプリの応答の圧縮。
* MIME の種類を圧縮する MIME の種類の既定の一覧に追加する方法。

## <a name="package"></a>Package

::: moniker range=">= aspnetcore-2.1"

ミドルウェアをプロジェクトに含めるにへの参照を追加、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)が含まれています、 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージ。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ミドルウェアをプロジェクトに含めるにへの参照を追加、 [Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)が含まれています、 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージ。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

ミドルウェアをプロジェクトに含めるにへの参照を追加、 [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/)パッケージ。

::: moniker-end

## <a name="configuration"></a>構成

::: moniker range=">= aspnetcore-2.2"

次のコードは、既定の MIME タイプと圧縮のプロバイダーの応答圧縮ミドルウェアを有効にする方法を示します ([Brotli](#brotli-compression-provider)と[Gzip](#gzip-compression-provider))。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

次のコードは、既定の MIME の種類の応答圧縮ミドルウェアを有効にする方法を示していますおよび[Gzip 圧縮プロバイダー](#gzip-compression-provider):。

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

メモ:

* `app.UseResponseCompression` 前に呼び出す必要があります`app.UseMvc`します。
* などのツールを使用して[Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)を設定する、`Accept-Encoding`要求ヘッダーおよび応答ヘッダー、サイズ、および本文を検討します。

なし、サンプル アプリに要求を送信、`Accept-Encoding`ヘッダーおよび応答の圧縮がないことを確認します。 `Content-Encoding`と`Vary`ヘッダーが応答に存在しません。

![Fiddler のウィンドウが Accept-encoding ヘッダーなしの要求の結果を表示します。 応答は圧縮されません。](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

使ってサンプル アプリに要求を送信、`Accept-Encoding: br`ヘッダー (Brotli 圧縮) し、応答を圧縮することを確認します。 `Content-Encoding`と`Vary`ヘッダーが応答に存在します。

![Accept-encoding ヘッダーで要求の結果と br の値を示す fiddler ウィンドウ。 Vary と Content-encoding ヘッダーは、応答に追加されます。 応答を圧縮します。](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

使ってサンプル アプリに要求を送信、`Accept-Encoding: gzip`ヘッダー応答を圧縮することを確認します。 `Content-Encoding`と`Vary`ヘッダーが応答に存在します。

![Accept-encoding ヘッダーで要求の結果と gzip の値を示す fiddler ウィンドウ。 Vary と Content-encoding ヘッダーは、応答に追加されます。 応答を圧縮します。](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>プロバイダー

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Brotli 圧縮プロバイダー

使用して、<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>で応答を圧縮する、 [Brotli 圧縮データ形式](https://tools.ietf.org/html/rfc7932)します。

圧縮のプロバイダーが明示的に追加されていない場合に、 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Brotli 圧縮プロバイダーは、既定で圧縮プロバイダーと共にの配列に追加されます、 [Gzip 圧縮プロバイダー](#gzip-compression-provider)します。
* 圧縮は、クライアントが Brotli 圧縮データの形式がサポートされている場合に、Brotli 圧縮既定値です。 Brotli がクライアントによってサポートされていない場合は、クライアントが Gzip 圧縮をサポートしている場合に、圧縮が Gzip に既定値です。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

すべての圧縮のプロバイダーが明示的に追加されたときに、Brotoli 圧縮プロバイダーを追加する必要があります。

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

圧縮レベルを設定<xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>します。 Brotli 圧縮プロバイダーの既定値は、最速の圧縮レベル ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))、最も効率的な圧縮を生成する可能性がありますされません。 最も効率的な圧縮が必要な場合は、最適に圧縮するためのミドルウェアを構成します。

| 圧縮レベル | 説明 |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | 圧縮は、結果の出力が最適に圧縮されていない場合でも、できるだけ早く完了します。 |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | 圧縮は実行されません。 |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | 応答する必要がありますが、最適な圧縮、圧縮を完了する時間がかかる場合でもです。 |

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

使用して、<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>で応答を圧縮する、 [Gzip ファイル形式](https://tools.ietf.org/html/rfc1952)します。

圧縮のプロバイダーが明示的に追加されていない場合に、 <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

::: moniker range=">= aspnetcore-2.2"

* Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーと共にの配列に追加されます、 [Brotli 圧縮プロバイダー](#brotli-compression-provider)します。
* 圧縮は、クライアントが Brotli 圧縮データの形式がサポートされている場合に、Brotli 圧縮既定値です。 Brotli がクライアントによってサポートされていない場合は、クライアントが Gzip 圧縮をサポートしている場合に、圧縮が Gzip に既定値です。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Gzip 圧縮プロバイダーは、既定で圧縮プロバイダーの配列に追加されます。
* 圧縮は、クライアントが Gzip 圧縮をサポートしている場合に、Gzip 既定値です。

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

圧縮のプロバイダーが明示的に追加されたときに、Gzip 圧縮プロバイダーを追加する必要があります。

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

圧縮レベルを設定<xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>します。 Gzip 圧縮プロバイダーの既定値は、最速の圧縮レベル ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel))、最も効率的な圧縮を生成する可能性がありますされません。 最も効率的な圧縮が必要な場合は、最適に圧縮するためのミドルウェアを構成します。

| 圧縮レベル | 説明 |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | 圧縮は、結果の出力が最適に圧縮されていない場合でも、できるだけ早く完了します。 |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | 圧縮は実行されません。 |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | 応答する必要がありますが、最適な圧縮、圧縮を完了する時間がかかる場合でもです。 |

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

### <a name="custom-providers"></a>カスタム プロバイダー

カスタムの圧縮の実装を作成<xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>です。 <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>このエンコーディングの内容を表す`ICompressionProvider`が生成されます。 ミドルウェアでは、この情報を使用して、指定されている一覧に基づいてプロバイダーを選択して、`Accept-Encoding`要求のヘッダー。

サンプル アプリを使用して、クライアント要求を送信すると、`Accept-Encoding: mycustomcompression`ヘッダー。 ミドルウェアがカスタム圧縮の実装を使用し、と共に応答を返す、`Content-Encoding: mycustomcompression`ヘッダー。 クライアントは、カスタムの圧縮の実装が機能するためにはカスタム エンコーディングを圧縮解除できる必要があります。

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

使ってサンプル アプリに要求を送信、`Accept-Encoding: mycustomcompression`ヘッダーおよび応答ヘッダーを確認します。 `Vary`と`Content-Encoding`ヘッダーが応答に存在します。 (非表示)、応答本文は、このサンプルで圧縮します。 圧縮の実装ではありません、`CustomCompressionProvider`サンプルのクラス。 ただし、このような圧縮アルゴリズムを実装するかどうかを示します。

![Accept-encoding ヘッダーで要求の結果と mycustomcompression の値を示す fiddler ウィンドウ。 Vary と Content-encoding ヘッダーは、応答に追加されます。](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME タイプ

ミドルウェアは、圧縮の MIME の種類の既定のセットを指定します。

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

置換または応答圧縮ミドルウェアのオプションで MIME の種類を追加します。 そのワイルドカードの MIME のメモなどの型`text/*`はサポートされていません。 サンプル アプリは、MIME の種類を追加します。`image/svg+xml`圧縮し、ASP.NET Core のバナー イメージを提供し、(*banner.svg*)。

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>セキュリティで保護されたプロトコルと圧縮

セキュリティで保護された接続経由で圧縮された応答を制御することができます、`EnableForHttps`オプションは、既定で無効にします。 動的に生成されたページで圧縮を使用する可能性がセキュリティ上の問題など、[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))と[侵害](https://wikipedia.org/wiki/BREACH_(security_exploit))攻撃です。

## <a name="adding-the-vary-header"></a>Vary ヘッダーを追加します。

::: moniker range=">= aspnetcore-2.0"

に基づいて応答を圧縮する場合、`Accept-Encoding`ヘッダー、可能性のある応答の複数の圧縮されたバージョンと、圧縮されていないバージョンがあります。 複数のバージョンが存在しを保存するか、クライアントおよびプロキシのキャッシュを指示するために、`Vary`でヘッダーを追加、`Accept-Encoding`値。 ASP.NET Core 2.0 以降では、ミドルウェアを追加、`Vary`応答を圧縮するときに自動的にヘッダー。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

に基づいて応答を圧縮する場合、`Accept-Encoding`ヘッダー、可能性のある応答の複数の圧縮されたバージョンと、圧縮されていないバージョンがあります。 複数のバージョンが存在しを保存するか、クライアントおよびプロキシのキャッシュを指示するために、`Vary`でヘッダーを追加、`Accept-Encoding`値。 ASP.NET core 1.x では、追加、`Vary`を応答にヘッダーを手動で行います。

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx のリバース プロキシの背後にあるときにミドルウェアの問題

要求が、Nginx によってプロキシの場合、`Accept-Encoding`ヘッダーを削除します。 削除、`Accept-Encoding`ヘッダーが応答を圧縮すると、ミドルウェアを防止します。 詳細については、「[NGINX:圧縮と圧縮解除](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)します。 この問題を追跡する[Nginx のパススルー圧縮図 (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123)します。

## <a name="working-with-iis-dynamic-compression"></a>IIS 動的圧縮を使用します。

モジュールの追加とを無効にする、アクティブな IIS 動的圧縮モジュールにアプリを無効にするサーバー レベルで構成した場合、 *web.config*ファイル。 詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

などのツールを使用して、 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)、または[Postman](https://www.getpostman.com/)、設定できます、`Accept-Encoding`要求ヘッダーおよび応答ヘッダー、サイズ、および本文を検討します。 既定では、応答圧縮ミドルウェアは、次の条件を満たしている応答を圧縮します。

::: moniker range=">= aspnetcore-2.2"

* `Accept-Encoding`の値を持つヘッダーがある`br`、 `gzip`、 `*`、または確立したカスタム圧縮プロバイダーに一致するカスタム エンコードします。 値がなければなりません`identity`または品質の値が (qvalue、 `q`) を 0 (ゼロ) を設定します。
* MIME の種類 (`Content-Type`) 設定する必要がありで構成されている MIME の種類に一致する必要があります、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>します。
* 要求が含めることはできません、`Content-Range`ヘッダー。
* セキュリティで保護されたプロトコル (https) が応答圧縮ミドルウェアのオプションで構成されていない場合、要求は安全でないプロトコル (http) を使用する必要があります。 *危険性に注意してください[上記で説明した](#compression-with-secure-protocol)セキュリティで保護されたコンテンツの圧縮を有効にする場合。*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* `Accept-Encoding`の値を持つヘッダーがある`gzip`、 `*`、または確立したカスタム圧縮プロバイダーに一致するカスタム エンコードします。 値がなければなりません`identity`または品質の値が (qvalue、 `q`) を 0 (ゼロ) を設定します。
* MIME の種類 (`Content-Type`) 設定する必要がありで構成されている MIME の種類に一致する必要があります、<xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>します。
* 要求が含めることはできません、`Content-Range`ヘッダー。
* セキュリティで保護されたプロトコル (https) が応答圧縮ミドルウェアのオプションで構成されていない場合、要求は安全でないプロトコル (http) を使用する必要があります。 *危険性に注意してください[上記で説明した](#compression-with-secure-protocol)セキュリティで保護されたコンテンツの圧縮を有効にする場合。*

::: moniker-end

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla 開発者ネットワーク:Accept-encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 セクション 3.1.2.1:コンテンツのコーディング](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 セクション 4.2.3:Gzip コーディング](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP ファイル形式の仕様バージョン 4.3](https://www.ietf.org/rfc/rfc1952.txt)
