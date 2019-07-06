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
# <a name="response-caching-middleware-in-aspnet-core"></a>応答キャッシュ ミドルウェアで ASP.NET Core

によって[Luke Latham](https://github.com/guardrex)と[John ルオ語](https://github.com/JunTaoLuo)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

この記事では、ASP.NET Core アプリの応答キャッシュ ミドルウェアを構成する方法について説明します。 応答がキャッシュ可能な場合、ストアの応答、およびキャッシュから応答を返す役割を果たし、ミドルウェアを決定します。 HTTP キャッシュの概要について、 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性は、「[応答のキャッシュ](xref:performance/caching/response)します。

## <a name="configuration"></a>構成

使用して、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)へのパッケージ参照を追加したり、 [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージ。

`Startup.ConfigureServices`、応答キャッシュ ミドルウェアをサービス コレクションに追加します。

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

ミドルウェアを使用するアプリの構成、<xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>で要求処理パイプラインにミドルウェアを追加する拡張メソッド`Startup.Configure`:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

サンプル アプリは、後続の要求でキャッシュを制御するヘッダーを追加します。

* [キャッシュ制御](https://tools.ietf.org/html/rfc7234#section-5.2)&ndash;に最大で 10 秒間キャッシュ可能な応答をキャッシュします。
* [異なる](https://tools.ietf.org/html/rfc7231#section-7.1.4)&ndash;キャッシュされた応答の場合にのみを処理するためにミドルウェアを構成、 [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4)後続の要求のヘッダーの元の要求と一致します。

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

応答キャッシュ ミドルウェアは、200 (OK) 状態コードと、サーバーの応答のみをキャッシュします。 その他の応答を含む[エラー ページ](xref:fundamentals/error-handling)、ミドルウェアによって無視されます。

> [!WARNING]
> 認証されたクライアントのコンテンツを含む応答は、格納して、それらの応答の処理からミドルウェアを防ぐためにキャッシュ不可としてマークする必要があります。 参照してください[キャッシュ用の条件](#conditions-for-caching)については、応答がキャッシュ可能なかどうかに、ミドルウェアが決定します。

## <a name="options"></a>オプション

応答のキャッシュ オプションは、次の表に表示されます。

| オプション | 説明 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | (バイト単位)、応答本文の最大キャッシュ サイズ。 既定値は`64 * 1024 * 1024`(64 MB)。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | 応答キャッシュ ミドルウェア (バイト単位) のサイズ制限。 既定値は`100 * 1024 * 1024`(100 MB)。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | 大文字のパスで応答がキャッシュされるかどうかを決定します。 既定値は `false` です。 |

次の例では、ミドルウェアを構成します。

* 本文のサイズが 1,024 バイト以下で応答をキャッシュします。
* パスの大文字小文字を区別して、応答を格納します。 たとえば、`/page1`と`/Page1`別に格納されます。

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

MVC を使用したときに web API コント ローラーまたは Razor ページのページ モデル、/、 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性が応答のキャッシュ用の適切なヘッダーを設定するために必要なパラメーターを指定します。 唯一のパラメーター、`[ResponseCache]`ミドルウェアを厳密に必要な属性が<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>、する実際の HTTP ヘッダーに対応していません。 詳細については、「 <xref:performance/caching/response#responsecache-attribute> 」を参照してください。

使用しない場合、`[ResponseCache]`属性と変化する応答のキャッシュ`VaryByQueryKeys`します。 使用して、<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>から直接、 [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

等しい 1 つの値を使用して`*`で`VaryByQueryKeys`キャッシュをすべての要求のクエリ パラメーターによって異なります。

## <a name="http-headers-used-by-response-caching-middleware"></a>応答キャッシュ ミドルウェアで使用される HTTP ヘッダー

次の表では、応答がキャッシュに影響する HTTP ヘッダーの情報を提供します。

| Header | 説明 |
| ------ | ------- |
| `Authorization` | ヘッダーが存在する場合、応答はキャッシュされません。 |
| `Cache-Control` | マークされた応答をキャッシュにのみ考慮、ミドルウェア、`public`キャッシュ ディレクティブ。 次のパラメーターでキャッシュを制御します。<ul><li>max-age</li><li>max-stale&#8224;</li><li>min 新規</li><li>必要があります revalidate</li><li>キャッシュなし</li><li>no ストア</li><li>専用の場合-キャッシュ</li><li>private</li><li>public</li><li>s maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;制限が指定されていない場合`max-stale`ミドルウェアは行われません。<br>&#8225;`proxy-revalidate`同じ効果`must-revalidate`します。<br><br>詳細については、次を参照してください。 [RFC 7231。要求のキャッシュ制御ディレクティブ](https://tools.ietf.org/html/rfc7234#section-5.2.1)します。 |
| `Pragma` | A`Pragma: no-cache`要求のヘッダーと同じ効果を生成する`Cache-Control: no-cache`します。 このヘッダーは、関連するディレクティブによってオーバーライドされる、`Cache-Control`ヘッダー、存在する場合。 Http/1.0 との下位互換性と見なされます。 |
| `Set-Cookie` | ヘッダーが存在する場合、応答はキャッシュされません。 1 つまたは複数の cookie を設定する要求処理パイプラインですべてのミドルウェアが応答をキャッシュから応答キャッシュ ミドルウェアを防ぎます (たとえば、 [cookie ベース TempData プロバイダー](xref:fundamentals/app-state#tempdata))。  |
| `Vary` | `Vary`別ヘッダーによってヘッダーが、キャッシュされた応答の変更に使用されます。 などを含めることによってエンコードすることによって応答をキャッシュ、`Vary: Accept-Encoding`ヘッダーの要求の応答をキャッシュするヘッダー`Accept-Encoding: gzip`と`Accept-Encoding: text/plain`とは別にします。 応答のヘッダー値が`*`は保存されません。 |
| `Expires` | このヘッダーが古いと見なされる応答はありません格納、またはその他によってオーバーライドされない限りを取得`Cache-Control`ヘッダー。 |
| `If-None-Match` | 値がない場合、完全な応答がキャッシュから提供された`*`、`ETag`の応答と一致しない指定された値のいずれか。 それ以外の場合、304 (変更なし) の応答が提供されます。 |
| `If-Modified-Since` | 場合、`If-None-Match`ヘッダーが含まれていない、キャッシュされた応答の日付が指定された値よりも新しい場合は、完全な応答がキャッシュから提供されます。 それ以外の場合、 *304 - 変更なし*応答を提供します。 |
| `Date` | キャッシュから提供するときに、`Date`元からの応答で指定されなかった場合、ミドルウェアによってヘッダーが設定されます。 |
| `Content-Length` | キャッシュから提供するときに、`Content-Length`元からの応答で指定されなかった場合、ミドルウェアによってヘッダーが設定されます。 |
| `Age` | `Age`元の応答で送信されたヘッダーは無視されます。 ミドルウェアは、キャッシュされた応答を提供するときに、新しい値を計算します。 |

## <a name="caching-respects-request-cache-control-directives"></a>要求キャッシュ コントロール ディレクティブを尊重キャッシュ

ミドルウェアの規則を尊重する、[仕様の HTTP 1.1 キャッシュ](https://tools.ietf.org/html/rfc7234#section-5.2)します。 規則を優先する有効なキャッシュを必要と`Cache-Control`クライアントによって送信されたヘッダー。 仕様では、クライアントがで要求を実行できる、`no-cache`ヘッダーの値と force、サーバー要求のたびに新しい応答を生成します。 現時点ではありませんこのキャッシュの動作を開発者の制御、ミドルウェアは、公式のキャッシュの仕様に準拠するために、ミドルウェアを使用する場合。

キャッシュの動作をより細かく制御は、ASP.NET Core の他のキャッシュ機能を探索します。 次のトピックを参照してください。

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>トラブルシューティング

キャッシュの動作が期待どおりにしていない場合は、応答がキャッシュとキャッシュから提供される機能のことを確認します。 要求の受信ヘッダーおよび応答の送信ヘッダーを調べます。 有効にする[ログ](xref:fundamentals/logging/index)デバッグを支援します。

テストとキャッシュ動作のトラブルシューティング、ブラウザーが好ましくない方法でキャッシュに影響する要求ヘッダーを設定できます。 たとえば、ブラウザー設定、`Cache-Control`ヘッダーを`no-cache`または`max-age=0`ページを更新するときにします。 次のツールでは、要求ヘッダーを明示的に設定することができ、キャッシュのテストに適しています。

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>キャッシュの条件

* 要求は、200 (OK) 状態コードでサーバーの応答になる必要があります。
* 要求メソッドは、GET または HEAD である必要があります。
* `Startup.Configure`、応答キャッシュ ミドルウェアは、キャッシュが必要なミドルウェアの前に配置する必要があります。 詳細については、「 <xref:fundamentals/middleware/index> 」を参照してください。
* `Authorization`ヘッダーが存在しない場合があります。
* `Cache-Control` ヘッダー パラメーターが有効である必要があり、応答をマークする必要があります`public`としてマークされていないと`private`します。
* `Pragma: no-cache`ヘッダーが存在しない場合がある場合、`Cache-Control`ヘッダーが存在しない、として、`Cache-Control`ヘッダーの上書き、`Pragma`ヘッダーが存在する場合。
* `Set-Cookie`ヘッダーが存在しない場合があります。
* `Vary` ヘッダーのパラメーターが有効であり、等しくする必要があります`*`します。
* `Content-Length`ヘッダーの値 (場合に設定)、応答本文のサイズに一致する必要があります。
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>は使用されません。
* 応答で指定された古いすることはできません、`Expires`ヘッダーと`max-age`と`s-maxage`ディレクティブをキャッシュします。
* 応答バッファリングと、成功したことがあります。 応答のサイズが、構成されているよりも小さくする必要がありますまたは既定の<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>します。 応答の本文のサイズが、構成されているよりも小さくする必要がありますまたは既定の<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>します。
* 応答はに従ってキャッシュ可能である必要があります、 [RFC 7234](https://tools.ietf.org/html/rfc7234)仕様。 たとえば、`no-store`ディレクティブが要求または応答のヘッダー フィールドに存在する必要があります。 参照してください*セクション 3。応答をキャッシュに格納する*の[RFC 7234](https://tools.ietf.org/html/rfc7234)詳細についてはします。

> [!NOTE]
> 偽造防止システムをクロスサイト リクエスト フォージェリ (CSRF) を防ぐためにセキュリティで保護されたトークンを生成するセットの攻撃、`Cache-Control`と`Pragma`ヘッダーを`no-cache`応答がキャッシュされないようにします。 HTML フォーム要素を偽造防止トークンを無効にする方法については、次を参照してください。<xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>します。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
