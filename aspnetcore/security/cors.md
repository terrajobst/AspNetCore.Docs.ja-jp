---
title: ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする
author: rick-anderson
description: ASP.NET Core アプリでのクロスオリジン要求を許可または拒否するための標準として CORS を使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: a34b77ad799a00707048c923b82b48774ce91682
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108066"
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

* `https://example.net`&ndash;異なるドメイン
* `https://www.example.com/foo.html`&ndash;異なるサブドメイン
* `http://example.com/foo.html`&ndash;異なるスキーム
* `https://example.com:9000/foo.html`&ndash;別のポート

Internet Explorer は、生成元を比較するときにポートを考慮しません。

## <a name="cors-with-named-policy-and-middleware"></a>名前付きポリシーとミドルウェアを使用した CORS

CORS ミドルウェアは、クロスオリジン要求を処理します。 次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

上のコードでは以下の操作が行われます。

* ポリシー名を "\_myallow固有のオリジン" に設定します。 ポリシー名は任意です。
* CORS を有効にする拡張メソッドを呼び出します。<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用してを呼び出します。 ラムダは、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> オブジェクトをとります。 などの[構成オプション](#cors-policy-options)に`WithOrigins`ついては、この記事の後半で説明します。

メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しによって、アプリのサービスコンテナーに CORS サービスが追加されます。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。

メソッド<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>は、次のコードに示すようにメソッドを連結できます。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

メモ:URL の末尾にスラッシュ (`/`) を含めることは**できません**。 URL がで`/`終了した場合、比較`false`はを返し、ヘッダーは返されません。

::: moniker range=">= aspnetcore-3.0"

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
> エンドポイントルーティングでは、CORS ミドルウェアを`UseRouting`と`UseEndpoints`の呼び出しの間で実行するように構成する必要があります。 構成が正しくないと、ミドルウェアが正常に機能しなくなります。

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
メモ: `UseCors`の前に`UseMvc`を呼び出す必要があります。

::: moniker-end

ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[、Razor Pages、コントローラー、アクションメソッドで Cors を有効](#ecors)にする方法に関するページを参照してください。

前のコードをテストする手順については、「[テスト CORS](#test) 」を参照してください。

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a>エンドポイントルーティングで Cors を有効にする

エンドポイントルーティングでは、 `RequireCors`一連の拡張メソッドを使用して、CORS をエンドポイントごとに有効にすることができます。

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

[ &lbrack;Enablecors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用するための代替手段を提供します。 属性`[EnableCors]`を指定すると、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS が有効になります。

を`[EnableCors]`使用して、既定の`[EnableCors("{Policy String}")]`ポリシーを指定し、ポリシーを指定します。

属性`[EnableCors]`は次のものに適用できます。

* Razor ページ`PageModel`
* コントローラー
* コントローラーアクションメソッド

属性を使用して、 `[EnableCors]`コントローラー/ページモデル/アクションに異なるポリシーを適用できます。 `[EnableCors]`属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、両方のポリシーが適用されます。 ポリシーを組み合わせることはお勧めしません。 同じアプリ内ではなく、属性またはミドルウェアを使用します。`[EnableCors]`

次のコードは、各メソッドに異なるポリシーを適用します。

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

次のコードでは、CORS の既定のポリシーと`"AnotherPolicy"`という名前のポリシーを作成します。

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS を無効にする

[ &lbrack;Disablecors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS ポリシーのオプション

ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。

* [許可されるオリジンの設定](#set-the-allowed-origins)
* [許可される HTTP メソッドを設定する](#set-the-allowed-http-methods)
* [許可された要求ヘッダーを設定する](#set-the-allowed-request-headers)
* [公開された応答ヘッダーを設定する](#set-the-exposed-response-headers)
* [クロスオリジン要求の資格情報](#credentials-in-cross-origin-requests)
* [プレフライトの有効期限を設定する](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>はで`Startup.ConfigureServices`呼び出されます。 いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。

## <a name="set-the-allowed-origins"></a>許可されるオリジンの設定

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>任意のスキーム (`http`または`https`) を持つすべてのオリジンからの CORS 要求を許可します。 &ndash; `AllowAnyOrigin`*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、安全ではありません。

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> `AllowAnyOrigin` と`AllowCredentials`を指定すると、安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。 アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> `AllowAnyOrigin` と`AllowCredentials`を指定すると、安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。 セキュリティで保護されたアプリの場合は、クライアントがサーバーリソースへのアクセスを承認する必要がある場合は、オリジンの正確な一覧を指定します。

::: moniker-end

`AllowAnyOrigin`プレフライト要求とヘッダー `Access-Control-Allow-Origin`に影響します。 詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; ポリシーのプロパティを、オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインと一致させることができるようにする関数に<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>設定します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>許可される HTTP メソッドを設定する

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* すべての HTTP メソッドを許可します。
* プレフライト要求とヘッダー `Access-Control-Allow-Methods`に影響します。 詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。

### <a name="set-the-allowed-request-headers"></a>許可された要求ヘッダーを設定する

CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれるを呼び出し<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 、許可されているヘッダーを指定します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

すべての作成者要求ヘッダーを許可<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>するには、次のように呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

この設定は、プレフライト要求`Access-Control-Request-Headers`とヘッダーに影響します。 詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。

::: moniker range=">= aspnetcore-2.2"

によって指定された特定のヘッダー `WithHeaders`に対して CORS ミドルウェアポリシーが一致`Access-Control-Request-Headers`するのは、ヘッダーが`WithHeaders`で記述されているヘッダーと完全に一致する場合のみです。

たとえば、次のように構成されたアプリを考えてみます。

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS ミドルウェアは、次の要求ヘッダーを使用して`Content-Language`プレフライト要求を拒否します。 ([headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) は、に`WithHeaders`は記載されていないためです。

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。 そのため、ブラウザーはクロスオリジン要求を試行しません。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS ミドルウェアでは、CorsPolicy で構成`Access-Control-Request-Headers`されている値に関係なく、常にの4つのヘッダーを送信できます。 このヘッダーの一覧には次のものが含まれます。

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

たとえば、次のように構成されたアプリを考えてみます。

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

は常にホワイトリストに登録されているため`Content-Language` 、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求に正常に応答します。

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>公開された応答ヘッダーを設定する

既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。 詳細については[、次を参照してください。 W3C クロスオリジンリソース共有 (用語):単純な応答](https://www.w3.org/TR/cors/#simple-response-header)ヘッダー。

既定で使用できる応答ヘッダーは次のとおりです。

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。 アプリで他のヘッダーを使用できるように<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>するには、次のように呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>クロスオリジン要求の資格情報

資格情報では、CORS 要求で特別な処理を行う必要があります。 既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。 資格情報には、cookie と HTTP 認証スキームが含まれます。 クロスオリジン要求で資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials`に`true`設定されている必要があります。

直接`XMLHttpRequest`の使用:

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

サーバーは資格情報を許可する必要があります。 クロスオリジンの資格情報を許可する<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>には、次のように呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 応答には、 `Access-Control-Allow-Credentials`サーバーがクロスオリジン要求に対して資格情報を許可することをブラウザーに示すヘッダーが含まれています。

ブラウザーが資格情報を送信しても、応答に`Access-Control-Allow-Credentials`有効なヘッダーが含まれていない場合、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。

クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。 別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

また、CORS 仕様では、 `"*"` `Access-Control-Allow-Credentials`ヘッダーが存在する場合、[オリジン] を [(すべてのオリジン)] に設定することもできます。

### <a name="preflight-requests"></a>プレフライト要求

一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。 この要求は*プレフライト要求*と呼ばれます。 次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。

* 要求メソッドは GET、HEAD、または POST です。
* `Accept`アプリでは`Content-Type`、 `Accept-Language`、、 `Last-Event-ID`、、または以外の要求ヘッダーは設定されません。 `Content-Language`
* ヘッダー `Content-Type`には、設定されている場合、次のいずれかの値が含まれます。
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

クライアント要求に対して設定された要求ヘッダーに適用される規則は、 `setRequestHeader` `XMLHttpRequest`オブジェクトに対してを呼び出すことによって、アプリが設定するヘッダーに適用されます。 CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。 このルールは`User-Agent`、ブラウザーが設定できるヘッダー (、 `Host` `Content-Length`、など) には適用されません。

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

* `Access-Control-Request-Method`:実際の要求に使用される HTTP メソッド。
* `Access-Control-Request-Headers`:アプリが実際の要求に設定する要求ヘッダーの一覧。 前述のように、これにはブラウザーが設定するヘッダー (など`User-Agent`) は含まれません。

CORS のプレフライト要求には`Access-Control-Request-Headers` 、実際の要求と共に送信されるヘッダーをサーバーに示すヘッダーを含めることができます。

特定のヘッダーを許可する<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>には、次のように呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

すべての作成者要求ヘッダーを許可<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>するには、次のように呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

ブラウザーの設定`Access-Control-Request-Headers`方法が完全に一致しているわけではありません。 `"*"`ヘッダーを以外の任意の値 (またはを<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>使用) に設定する場合は`Accept`、 `Content-Type`少なくと`Origin`も、、、、およびサポートするカスタムヘッダーを含める必要があります。

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

応答には、 `Access-Control-Allow-Methods`許可されているメソッドと、 `Access-Control-Allow-Headers`必要に応じてヘッダーを一覧表示するヘッダーが含まれています。 プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。

プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。 そのため、ブラウザーはクロスオリジン要求を試行しません。

### <a name="set-the-preflight-expiration-time"></a>プレフライトの有効期限を設定する

ヘッダー `Access-Control-Max-Age`は、プレフライト要求への応答をキャッシュできる期間を指定します。 このヘッダーを設定するに<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>は、次のように呼び出します。

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
  * (CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。 CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。 JSONP は xhr を使用しないの`<script>`で、タグを使用して応答を受信します。 スクリプトをクロスオリジンで読み込むことができます。

[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。 ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。 CORS を有効にするためにカスタム JavaScript コードは必要ありません。

次に、クロスオリジン要求の例を示します。 ヘッダー `Origin`は、要求を行っているサイトのドメインを提供します。

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

サーバーが要求を許可している場合は`Access-Control-Allow-Origin` 、応答でヘッダーが設定されます。 このヘッダーの値は、要求の`Origin`ヘッダーと一致するか、またはワイルド`"*"`カード値です。つまり、すべての配信元が許可されます。

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

応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。 具体的には、ブラウザーは要求を許可しません。 サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。

<a name="test"></a>

## <a name="test-cors"></a>CORS のテスト

CORS をテストするには:

1. [API プロジェクトを作成](xref:tutorials/first-web-api)します。 または、[サンプルをダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。
1. このドキュメントのいずれかの方法を使用して CORS を有効にします。 例:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`サンプルアプリのテストにのみ使用してください[サンプルコードをダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)します。

1. Web アプリプロジェクト (Razor Pages または MVC) を作成します。 このサンプルでは、Razor Pages を使用します。 API プロジェクトと同じソリューションに web アプリを作成できます。
1. 次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 前のコードで、を`url: 'https://<web app>.azurewebsites.net/api/values/1',`デプロイされたアプリの URL に置き換えます。
1. API プロジェクトをデプロイします。 たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。
1. デスクトップから Razor Pages または MVC アプリを実行し、 **[テスト]** ボタンをクリックします。 F12 ツールを使用して、エラーメッセージを確認します。
1. から`WithOrigins` localhost の配信元を削除し、アプリをデプロイします。 または、別のポートを使用してクライアントアプリを実行します。 たとえば、Visual Studio からを実行します。
1. クライアントアプリでテストします。 CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。 F12 ツールの [コンソール] タブを使用して、エラーを確認します。 ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。

   * Microsoft Edge を使用する:

     **SEC7120: [CORS] オリジン`https://localhost:44375`は、次の場所にあるクロスオリジンリソースのアクセス制御-許可-オリジン応答ヘッダーで見つかり`https://localhost:44375`ませんでした`https://webapi.azurewebsites.net/api/values/1`**

   * Chrome を使用する:

     **`https://webapi.azurewebsites.net/api/values/1` オリジン`https://localhost:44375`からの XMLHttpRequest へのアクセスが CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御-許可元 ' ヘッダーが存在しません。**

## <a name="additional-resources"></a>その他の技術情報

* [クロスオリジンリソース共有 (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
