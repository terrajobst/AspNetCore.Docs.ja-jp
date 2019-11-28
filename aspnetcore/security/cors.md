---
title: ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする
author: rick-anderson
description: ASP.NET Core アプリでのクロスオリジン要求を許可または拒否するための標準として CORS を使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: security/cors
ms.openlocfilehash: 3a51d365626c858ad48298a1108e37eba9050fe7
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391295"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。

ブラウザーのセキュリティは、web ページがその web ページを提供するものとは異なるドメインに要求を行うことを防ぎます。 この制限は、*同一オリジン ポリシー*と呼ばれます。 同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。 場合によっては、他のサイトからアプリへのクロス オリジン要求を許可する可能性があります。 詳細については、[Mozilla CORS の記事](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) を参照してください。

[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):

* は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。
* CORS はセキュリティ機能では **なく**、セキュリティを緩和するものです。 CORS を許可しても、API は安全ではありません。 詳細については、 [CORS の仕組み](#how-cors) を参照してください。
* サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。
* は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="same-origin"></a>同じオリジン

同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。

次の 2 つの URL は生成元が同じです。

* `https://example.com/foo.html`
* `https://example.com/bar.html`

これらの Url には、前の2つの Url とは異なるオリジンがあります。

* 別のドメイン &ndash; `https://example.net`
* `https://www.example.com/foo.html` &ndash; 異なるサブドメイン
* 異なるスキーム &ndash; `http://example.com/foo.html`
* 別のポート &ndash; `https://example.com:9000/foo.html`

Internet Explorer は、生成元を比較するときにポートを考慮しません。

## <a name="cors-with-named-policy-and-middleware"></a>名前付きポリシーとミドルウェアを使用した CORS

CORS ミドルウェアは、クロスオリジン要求を処理します。 次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

上のコードでは以下の操作が行われます。

* ポリシー名を "\_Myallow固有のオリジン" に設定します。 ポリシー名は任意です。
* <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 拡張メソッドを呼び出します。これにより CORS が有効になります。
* [ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> を呼び出します。 ラムダは、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> オブジェクトをとります。 `WithOrigins`などの[構成オプション](#cors-policy-options)については、この記事の後半で説明します。

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> メソッド呼び出しは、アプリのサービスコンテナーに CORS サービスを追加します。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> メソッドは、次のコードに示すようにメソッドを連結できます。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

注: URL の末尾にスラッシュ (`/`) を含めることは**できません**。 URL が `/`で終了した場合、比較は `false` を返し、ヘッダーは返されません。

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a>CORS ポリシーをすべてのエンドポイントに適用する

次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> エンドポイントルーティングを使用する場合は、`UseRouting` と `UseEndpoints`の呼び出しの間で実行されるように CORS ミドルウェアを構成する必要があります。 構成が正しくないと、ミドルウェアが正常に機能しなくなります。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
注: `UseMvc`する前に `UseCors` を呼び出す必要があります。

::: moniker-end

ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[、Razor Pages、コントローラー、アクションメソッドで Cors を有効](#ecors)にする方法に関するページを参照してください。

前のコードをテストする手順については、「[テスト CORS](#test) 」を参照してください。

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a>エンドポイントルーティングで Cors を有効にする

エンドポイントルーティングでは、`RequireCors` の拡張メソッドのセットを使用して、エンドポイントごとに CORS を有効にすることができます。

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

同様に、すべてのコントローラーに対して CORS を有効にすることもできます。

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a>属性を使用して CORS を有効にする

[&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。 `[EnableCors]` 属性は、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS を有効にします。

`[EnableCors]` を使用して、既定のポリシーを指定し、`[EnableCors("{Policy String}")]` ポリシーを指定します。

`[EnableCors]` 属性は、次のように適用できます。

* Razor ページ `PageModel`
* コントローラー
* コントローラーアクションメソッド

`[EnableCors]` 属性を使用して、コントローラー/ページモデル/アクションに異なるポリシーを適用できます。 `[EnableCors]` 属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、両方のポリシーが適用されます。 ポリシーを組み合わせることはお勧めしません。 同じアプリ内ではなく、`[EnableCors]` の属性またはミドルウェアを使用します。

次のコードは、各メソッドに異なるポリシーを適用します。

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

次のコードでは、CORS の既定のポリシーと `"AnotherPolicy"`という名前のポリシーを作成します。

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS を無効にする

[&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS ポリシーのオプション

ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。

* [許可されるオリジンの設定](#set-the-allowed-origins)
* [許可される HTTP メソッドを設定する](#set-the-allowed-http-methods)
* [許可された要求ヘッダーを設定する](#set-the-allowed-request-headers)
* [公開された応答ヘッダーを設定する](#set-the-exposed-response-headers)
* [クロスオリジン要求の資格情報](#credentials-in-cross-origin-requests)
* [プレフライトの有効期限を設定する](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> は `Startup.ConfigureServices`で呼び出されます。 いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。

## <a name="set-the-allowed-origins"></a>許可されるオリジンの設定

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; では、任意のスキーム (`http` または `https`) を使用して、すべてのオリジンからの CORS 要求を許可します。 *web サイト*はアプリに対してクロスオリジン要求を行うことができるため、`AllowAnyOrigin` は安全ではありません。

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> `AllowAnyOrigin` と `AllowCredentials` の指定は安全ではない構成であり、クロスサイト要求の偽造が発生する可能性があります。 アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> `AllowAnyOrigin` と `AllowCredentials` の指定は安全ではない構成であり、クロスサイト要求の偽造が発生する可能性があります。 セキュリティで保護されたアプリの場合は、クライアントがサーバーリソースへのアクセスを承認する必要がある場合は、オリジンの正確な一覧を指定します。

::: moniker-end

`AllowAnyOrigin` は、プレフライト要求と `Access-Control-Allow-Origin` ヘッダーに影響します。 詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; は、ポリシーの <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> プロパティを、オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインと一致させることができるようにする関数に設定されます。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>許可される HTTP メソッドを設定する

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* すべての HTTP メソッドを許可します。
* プレフライト要求と `Access-Control-Allow-Methods` ヘッダーに影響します。 詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。

### <a name="set-the-allowed-request-headers"></a>許可された要求ヘッダーを設定する

CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれますが、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> を呼び出し、許可されているヘッダーを指定します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

この設定は、プレフライト要求と `Access-Control-Request-Headers` ヘッダーに影響します。 詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。

::: moniker range=">= aspnetcore-2.2"

`WithHeaders` によって指定された特定のヘッダーに対して CORS ミドルウェアポリシーが一致するのは、`Access-Control-Request-Headers` で送信されたヘッダーが `WithHeaders`に記載されているヘッダーと完全に一致する場合のみです。

たとえば、次のように構成されたアプリを考えてみます。

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

`Content-Language` ([Headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) が `WithHeaders`に一覧表示されていないため、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求を拒否します。

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。 そのため、ブラウザーはクロスオリジン要求を試行しません。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS ミドルウェアでは、CorsPolicy で構成されている値に関係なく、常に `Access-Control-Request-Headers` の4つのヘッダーを送信できます。 このヘッダーの一覧には次のものが含まれます。

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

たとえば、次のように構成されたアプリを考えてみます。

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

`Content-Language` が常にホワイトリストに登録されているため、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求に正常に応答します。

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>公開された応答ヘッダーを設定する

既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。 詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。

既定で使用できる応答ヘッダーは次のとおりです。

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。 アプリで他のヘッダーを使用できるようにするには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>クロスオリジン要求の資格情報

資格情報では、CORS 要求で特別な処理を行う必要があります。 既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。 資格情報には、cookie と HTTP 認証スキームが含まれます。 クロスオリジン要求で資格情報を送信するには、クライアントで `XMLHttpRequest.withCredentials` を `true`に設定する必要があります。

`XMLHttpRequest` を直接使用する:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

JQuery の使用:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)の使用:

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

サーバーは資格情報を許可する必要があります。 クロスオリジンの資格情報を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 応答には `Access-Control-Allow-Credentials` ヘッダーが含まれています。これにより、サーバーがクロスオリジン要求の資格情報を許可していることがブラウザーに伝えられます。

ブラウザーが資格情報を送信しても、応答に有効な `Access-Control-Allow-Credentials` ヘッダーが含まれていない場合、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。

クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。 別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

また、CORS 仕様では、`Access-Control-Allow-Credentials` ヘッダーが存在する場合、[オリジン] を `"*"` (すべてのオリジン) に設定することもできます。

### <a name="preflight-requests"></a>プレフライト要求

一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。 この要求は*プレフライト要求*と呼ばれます。 次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。

* 要求メソッドは GET、HEAD、または POST です。
* アプリで `Accept`、`Accept-Language`、`Content-Language`、`Content-Type`、または `Last-Event-ID`以外の要求ヘッダーが設定されていません。
* `Content-Type` ヘッダーには、次のいずれかの値が設定されています。
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

クライアント要求に対して設定された要求ヘッダーに適用される規則は、`XMLHttpRequest` オブジェクトの `setRequestHeader` を呼び出すことによってアプリが設定するヘッダーに適用されます。 CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。 このルールは、ブラウザーが設定できるヘッダー (`User-Agent`、`Host`、`Content-Length`など) には適用されません。

プレフライト要求の例を次に示します。

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

事前フライト要求では、HTTP OPTIONS メソッドを使用します。 次の2つの特殊なヘッダーが含まれます。

* `Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。
* `Access-Control-Request-Headers`: アプリが実際の要求に対して設定する要求ヘッダーの一覧。 前述のように、これには、`User-Agent`など、ブラウザーによって設定されるヘッダーは含まれません。

CORS のプレフライト要求には、`Access-Control-Request-Headers` ヘッダーが含まれる場合があります。これは、実際の要求と共に送信されるヘッダーをサーバーに示します。

特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

ブラウザーの `Access-Control-Request-Headers`の設定方法が完全に一致しているわけではありません。 ヘッダーを `"*"` 以外に設定する (または <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>を使用する) 場合は、少なくとも `Accept`、`Content-Type`、および `Origin`と、サポートするカスタムヘッダーを含める必要があります。

プレフライト要求に対する応答の例を次に示します (サーバーが要求を許可していることを前提としています)。

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

応答には、許可されたメソッドと、必要に応じて `Access-Control-Allow-Headers` ヘッダーを一覧表示する `Access-Control-Allow-Methods` ヘッダーが含まれます。これには、許可されているヘッダーが表示されます。 プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。

プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。 そのため、ブラウザーはクロスオリジン要求を試行しません。

### <a name="set-the-preflight-expiration-time"></a>プレフライトの有効期限を設定する

`Access-Control-Max-Age` ヘッダーは、プレフライト要求への応答をキャッシュできる期間を指定します。 このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS のしくみ

このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)要求の動作について説明します。

* CORS はセキュリティ機能では**ありません**。 CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。
  * たとえば、悪意のあるアクターがを使用してサイトに対して[クロスサイトスクリプティング (XSS) を防止](xref:security/cross-site-scripting)し、CORS が有効になっているサイトに対してクロスサイト要求を実行して情報を盗むことができます。
* CORS を許可することで、API の安全性が向上します。
  * CORS を適用するには、クライアント (ブラウザー) が必要です。 サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。 たとえば、次のツールを使用すると、サーバーの応答が表示されます。
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * アドレスバーに URL を入力して、web ブラウザーを表示します。
* これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。
  * (CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。 CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。 JSONP は XHR を使用しないので、`<script>` タグを使用して応答を受信します。 スクリプトをクロスオリジンで読み込むことができます。

[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。 ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。 CORS を有効にするためにカスタム JavaScript コードは必要ありません。

次に、クロスオリジン要求の例を示します。 `Origin` ヘッダーは、要求を行っているサイトのドメインを提供します。 `Origin` ヘッダーは必須であり、ホストとは異なる必要があります。

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

サーバーが要求を許可している場合は、応答に `Access-Control-Allow-Origin` ヘッダーが設定されます。 このヘッダーの値は、要求の `Origin` ヘッダーと一致しているか、または `"*"`ワイルドカードの値であることを意味します。つまり、すべての配信元が許可されます。

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

応答に `Access-Control-Allow-Origin` ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。 具体的には、ブラウザーは要求を許可しません。 サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。

<a name="test"></a>

## <a name="test-cors"></a>CORS のテスト

CORS をテストするには:

1. [API プロジェクトを作成](xref:tutorials/first-web-api)します。 または、[サンプルをダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。
1. このドキュメントのいずれかの方法を使用して CORS を有効にします。 例 :

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` は、サンプル[コードのダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)と同様のサンプルアプリのテストにのみ使用してください。

1. Web アプリプロジェクト (Razor Pages または MVC) を作成します。 このサンプルでは、Razor Pages を使用します。 API プロジェクトと同じソリューションに web アプリを作成できます。
1. 次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 上記のコードでは、`url: 'https://<web app>.azurewebsites.net/api/values/1',` を、デプロイされたアプリの URL に置き換えます。
1. API プロジェクトをデプロイします。 たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。
1. デスクトップから Razor Pages または MVC アプリを実行し、 **[テスト]** ボタンをクリックします。 F12 ツールを使用して、エラーメッセージを確認します。
1. `WithOrigins` から localhost の発信元を削除し、アプリをデプロイします。 または、別のポートを使用してクライアントアプリを実行します。 たとえば、Visual Studio からを実行します。
1. クライアントアプリでテストします。 CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。 F12 ツールの [コンソール] タブを使用して、エラーを確認します。 ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。

   * Microsoft Edge を使用する:

     **SEC7120: [CORS] `https://webapi.azurewebsites.net/api/values/1` のクロスオリジンリソースのアクセス制御-`https://localhost:44375` 応答ヘッダーに、元の `https://localhost:44375` が見つかりませんでした**

   * Chrome を使用する:

     **オリジン `https://localhost:44375` からの `https://webapi.azurewebsites.net/api/values/1` での XMLHttpRequest へのアクセスが、CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御-発信元 ' ヘッダーは存在しません。**
     
CORS が有効なエンドポイントは、 [Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。 ツールを使用する場合は、`Origin` ヘッダーによって指定された要求の配信元が、要求を受信しているホストと異なる必要があります。 要求が `Origin` ヘッダーの値に基づいて*クロスオリジンで*ない場合は、次のようになります。

* CORS ミドルウェアが要求を処理する必要はありません。
* CORS ヘッダーが応答で返されません。

## <a name="additional-resources"></a>その他のリソース

* [クロスオリジンリソース共有 (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
