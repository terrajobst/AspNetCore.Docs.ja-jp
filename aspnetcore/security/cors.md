---
title: ASP.NETコアでのクロスオリジンリクエスト(CORS)の有効化
author: rick-anderson
description: ASP.NET Core アプリでクロスオリジン要求を許可または拒否するための標準として CORS を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 601e26e1990a86ad60aa50c8c93ffa490ff6b708
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007185"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>ASP.NETコアでのクロスオリジンリクエスト(CORS)の有効化

::: moniker range=">= aspnetcore-3.0"

[リック・アンダーソン](https://twitter.com/RickAndMSFT)と[カーク・ラーキン](https://twitter.com/serpent5)

この記事では、ASP.NET コア アプリで CORS を有効にする方法について説明します。

ブラウザのセキュリティにより、Web ページが Web ページを提供したドメインとは異なるドメインに対して要求を行えないようにします。 この制限は、*同一生成元ポリシー*と呼ばれます。 同一生成元ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取るのを防ぎます。 場合によっては、他のサイトでアプリに対してクロスオリジン要求を行うことを許可する必要がある場合があります。 詳細については、 [Mozilla CORS の記事](https://developer.mozilla.org/docs/Web/HTTP/CORS)を参照してください。

[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):

* サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。
* セキュリティ機能**ではない**、CORS はセキュリティを緩和します。 API は CORS を許可することで安全ではありません。 詳細については[、「CORS の仕組み](#how-cors)」を参照してください。
* サーバーが、一部のクロスオリジン要求を許可し、その他の要求を拒否できるようにします。
* [JSONP](/dotnet/framework/wcf/samples/jsonp)などの以前の手法よりも安全で柔軟性があります。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="same-origin"></a>同じ起源

2 つの URL が同じスキーム、ホスト、およびポートを持つ場合は、同じオリジンを持ちます ([RFC 6454](https://tools.ietf.org/html/rfc6454))。

これらの 2 つの URL のオリジンは同じです。

* `https://example.com/foo.html`
* `https://example.com/bar.html`

これらの URL のオリジンは、前の 2 つの URL とは異なります。

* `https://example.net`&ndash;異なるドメイン
* `https://www.example.com/foo.html`&ndash;異なるサブドメイン
* `http://example.com/foo.html`&ndash;異なるスキーム
* `https://example.com:9000/foo.html`&ndash;異なるポート

## <a name="enable-cors"></a>CORS を有効にする

CORS を有効にするには、次の 3 つの方法があります。

* ミドルウェアで[名前付きポリシー](#np)または[デフォルトポリシー](#dp)を使用する 。
* [エンドポイント ルーティング](#ecors)を使用する :
* [[EnableCors] 属性を使用します](#attr)。

名前付きポリシーで[[EnableCors]](#attr)属性を使用すると、CORS をサポートするエンドポイントを制限する際に、最も優れた制御が提供されます。

各方法については、以下のセクションで詳しく説明します。

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>名前付きポリシーとミドルウェアを使用した CORS

CORS ミドルウェアは、クロスオリジン要求を処理します。 次のコードでは、指定したオリジンを持つすべてのアプリのエンドポイントに CORS ポリシーを適用します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

上のコードでは以下の操作が行われます。

* ポリシー名を に`_myAllowSpecificOrigins`設定します。 ポリシー名は任意です。
* 拡張メソッド<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>を呼び出し`_myAllowSpecificOrigins`、CORS ポリシーを指定します。 `UseCors`CORS ミドルウェアを追加します。
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して呼び出します。 ラムダはオブジェクト<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>を受け取ります。 などの`WithOrigins`[構成オプション](#cors-policy-options)については、この記事の後半で説明します。
* すべてのコントローラー`_myAllowSpecificOrigins`エンドポイントの CORS ポリシーを有効にします。 特定[のエンドポイント](#ecors)に CORS ポリシーを適用するには、エンドポイントルーティングを参照してください。

エンドポイント ルーティングでは、CORS ミドルウェアが 呼び出しと`UseRouting`の`UseEndpoints`間で実行されるように構成する***必要があります***。

上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。

メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しは、アプリのサービス コンテナーに CORS サービスを追加します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

詳細については、このドキュメントの[CORS ポリシー オプション](#cpo)を参照してください。

次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>のコードに示すように、メソッドは連鎖できます。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

注意 : 指定された**not**URL に末尾の`/`スラッシュ ( ) を含めることはできません。 URL が で`/`終わる場合、比較`false`は返され、ヘッダーは返されません。

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>デフォルトポリシーとミドルウェアを使用した CORS

次の強調表示されたコードは、既定の CORS ポリシーを有効にします。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

上記のコードでは、すべてのコントローラ エンドポイントにデフォルトの CORS ポリシーが適用されます。

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>エンドポイント ルーティングで CORS を有効にする

現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にしても、[自動プリフライト要求](#apf)はサポート***されません***。 詳細については、この[GitHub の問題](https://github.com/dotnet/aspnetcore/issues/20709)と[エンドポイント ルーティングを使用した CORS のテストと [HttpOptions]](#tcer)を参照してください。

エンドポイント ルーティングを使用すると、次の拡張メソッドを使用してエンドポイントごとに<xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*>CORS を有効にすることができます。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,41,44)]

上のコードでは以下の操作が行われます。

* `app.UseCors`を使用して、CORS ミドルウェアを有効にします。 デフォルトポリシーが設定されていないため、`app.UseCors()`単独では CORS を有効にしません。
* および`/echo`コントローラ エンドポイントは、指定されたポリシーを使用して、クロス オリジン要求を許可します。
* `/echo2`および Razor ページ エンドポイントでは、既定のポリシーが指定されていないため、クロス オリジン要求は許可***されません***。

[[DisableCors]](#dc)属性は、エンドポイント ルーティングによって有効になっている CORS を`RequireCors`無効***にしません***。

上記のようなコードのテスト手順については、「[エンドポイントルーティングを使用した CORS のテスト」および「HttpOptions」](#tcer)を参照してください。

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>属性を使用した CORS の有効化

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性を使用して CORS を有効にし、CORS を必要とするエンドポイントにのみ名前付きポリシーを適用すると、最高の制御が提供されます。

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。 この`[EnableCors]`属性は、すべてのエンドポイントではなく、選択したエンドポイントに CORS を有効にします。

* `[EnableCors]`は、既定のポリシーを指定します。
* `[EnableCors("{Policy String}")]`は、名前付きポリシーを指定します。

属性`[EnableCors]`は、次の場合に適用できます。

* カミソリページ`PageModel`
* コントローラー
* コントローラアクションメソッド

属性を使用して、コントローラ、ページ モデル、アクション メソッドに異なる`[EnableCors]`ポリシーを適用できます。 属性が`[EnableCors]`コントローラー、ページ・モデル、またはアクション・メソッドに適用され、CORS がミドルウェアで有効になっている場合、***両方の***ポリシーが適用されます。 ***ポリシーの組み合わせに対しては、お勧めします。***`[EnableCors]`***同じアプリで属性またはミドルウェアを使用する必要はありません。***

次のコードでは、各メソッドに異なるポリシーを適用します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

次のコードでは、2 つの CORS ポリシーを作成します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

CORS 要求を制限する最も優れた制御を行う場合:

* 名前`[EnableCors("MyPolicy")]`付きポリシーで使用します。
* 既定のポリシーを定義しないでください。
* [エンドポイント ルーティング](#ecors)を使用しない。

次のセクションのコードは、上記の一覧を満たしています。

上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。

<a name="dc"></a>

### <a name="disable-cors"></a>CORS を無効にする

[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は[、エンドポイント ルーティング](#ecors)によって有効にされた CORS を無効***にしません***。

次のコードは、CORS`"MyPolicy"`ポリシーを定義します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

次のコードは、アクションの CORS を無効にします`GetValues2`。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

上のコードでは以下の操作が行われます。

* [エンドポイント ルーティング](#ecors)で CORS を有効にしません。
* 既定の[CORS ポリシー](#dp)を定義しません。
* [[EnableCors("MyPolicy")]を](#attr)使用して`"MyPolicy"`、コントローラのCORSポリシーを有効にします。
* メソッドの CORS`GetValues2`を無効にします。

上記のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS ポリシー オプション

このセクションでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。

* [許可された原点を設定する](#set-the-allowed-origins)
* [許可される HTTP メソッドを設定する](#set-the-allowed-http-methods)
* [許可された要求ヘッダーを設定する](#set-the-allowed-request-headers)
* [公開された応答ヘッダーを設定する](#set-the-exposed-response-headers)
* [クロスオリジン要求の資格情報](#credentials-in-cross-origin-requests)
* [プリフライトの有効期限を設定する](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>が で`Startup.ConfigureServices`呼び出されます。 いくつかのオプションについては、[最初に「CORS の仕組み](#how-cors)」のセクションを読んでも役に立つ場合があります。

## <a name="set-the-allowed-origins"></a>許可された原点を設定する

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;任意のスキーム ( または`http``https`) を持つすべてのオリジンからの CORS 要求を許可します。 `AllowAnyOrigin`*どのウェブサイトも*アプリにクロスオリジン要求を行うことができるため、安全ではありません。

> [!NOTE]
> 指定され`AllowAnyOrigin`、`AllowCredentials`安全でない構成であり、クロスサイトリクエストフォージェリにつながる可能性があります。 アプリが両方のメソッドで構成されている場合、CORS サービスは無効な CORS 応答を返します。

`AllowAnyOrigin`はプリフライト要求とヘッダー`Access-Control-Allow-Origin`に影響します。 詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;ポリシーの<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>プロパティを、オリジンが許可されているかどうかを評価するときに、設定されたワイルドカード ドメインとオリジンが一致するように設定します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>許可される HTTP メソッドを設定する

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 任意の HTTP メソッドを許可します。
* プリフライト要求とヘッダーに`Access-Control-Allow-Methods`影響します。 詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。

### <a name="set-the-allowed-request-headers"></a>許可された要求ヘッダーを設定する

特定のヘッダーを CORS 要求で送信できるようにするには[、author 要求ヘッダー](https://xhr.spec.whatwg.org/#request)と呼<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>びます。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`は、プリフライト要求および[アクセス制御要求ヘッダーヘッダーに影響します](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)。 詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。

CORS ミドルウェア ポリシーは、 で`WithHeaders`指定された特定のヘッダー`Access-Control-Request-Headers`と一致する場合にのみ可能です。 `WithHeaders`

たとえば、次のように構成されたアプリを考えてみます。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS ミドルウェアは、`Content-Language`[に一](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)覧されていないため、次の要求ヘッダーを使用してプレフライト要求を`WithHeaders`拒否します。

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

アプリは*200 OK*応答を返しますが、CORS ヘッダーを返しません。 したがって、ブラウザーは、クロスオリジン要求を試行しません。

### <a name="set-the-exposed-response-headers"></a>公開された応答ヘッダーを設定する

既定では、ブラウザーは、すべての応答ヘッダーをアプリに公開しません。 詳細については、「 [W3C クロス オリジン リソース共有 (用語集): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。

デフォルトで使用可能な応答ヘッダーは次のとおりです。

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 仕様では、これらのヘッダーの*単純な応答ヘッダーを*呼び出します。 他のヘッダーをアプリで使用できるようにするには、次の<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>呼び出しを行います。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>クロスオリジン要求の資格情報

資格情報には、CORS 要求での特別な処理が必要です。 既定では、ブラウザーはクロスオリジン要求を含む資格情報を送信しません。 資格情報には、Cookie と HTTP 認証スキームが含まれます。 クロスオリジン要求を使用して資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials``true`に設定する必要があります。

直接`XMLHttpRequest`使用:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

jQuery を使用する:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

フェッチ[API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用 :

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

サーバーは資格情報を許可する必要があります。 クロスオリジンの資格情報を許可するには、次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>の呼び出しを行います。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

HTTP 応答には`Access-Control-Allow-Credentials`ヘッダーが含まれており、サーバーがクロスオリジン要求の資格情報を許可することをブラウザーに伝えます。

ブラウザーが資格情報を送信しても、応答に有効な`Access-Control-Allow-Credentials`ヘッダーが含まれていない場合、ブラウザーはアプリに対する応答を公開せず、クロスオリジン要求は失敗します。

クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。 別のドメインの Web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 仕様では、ヘッダーが存在する場合`"*"`、(すべての起点) に原点`Access-Control-Allow-Credentials`を設定することは無効であるとも述べています。

<a name="pref"></a>

## <a name="preflight-requests"></a>プリフライトリクエスト

一部の CORS 要求では、ブラウザーは実際の要求を行う前に追加[の OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)要求を送信します。 この要求は[、プリフライト要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)と呼ばれます。 次***の条件がすべて***満たしている場合、ブラウザはプリフライトリクエストをスキップできます。

* 要求メソッドは、GET、HEAD、または POST です。
* アプリ`Accept`は、 、 、 `Accept-Language`、 、、`Content-Language``Content-Type`または`Last-Event-ID`以外の要求ヘッダーを設定しません。
* ヘッダー`Content-Type`が設定されている場合、次のいずれかの値が設定されます。
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

クライアント要求に設定された要求ヘッダーのルールは、アプリケーションがオブジェクトを呼び出`setRequestHeader`すことによって設定するヘッダーに`XMLHttpRequest`適用されます。 CORS 仕様では、これらのヘッダー[の作成者要求ヘッダーを](https://www.w3.org/TR/cors/#author-request-headers)呼び出します。 ルールは、 、 、 など、`User-Agent``Host`ブラウザで設定できるヘッダーには適用されません。 `Content-Length`

次に示す応答は、このドキュメントの[「テスト CORS」](#testc)セクションの **[Put test]** ボタンから行われたプリフライト要求に似た応答です。

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

プリフライト要求では[、HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)メソッドが使用されます。 次のヘッダーが含まれる場合があります。

* [アクセス制御要求メソッド](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 実際の要求に使用される HTTP メソッド。
* [アクセスコントロール要求ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): アプリが実際の要求に設定する要求ヘッダーのリスト。 前述のように、ブラウザーが設定するヘッダーは含みません`User-Agent`。
* [アクセス制御許可メソッド](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

プリフライト要求が拒否された場合、アプリは応答を`200 OK`返しますが、CORS ヘッダーは設定しません。 したがって、ブラウザーは、クロスオリジン要求を試行しません。 拒否されたプリフライト要求の例については、このドキュメントの[「CORS](#testc)のテスト」セクションを参照してください。

F12 ツールを使用すると、コンソール アプリはブラウザーに応じて、次のいずれかと同様のエラーを表示します。

* Firefox: クロスオリジンリクエストブロック: 同じオリジンポリシーでは、 で`https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`リモートリソースの読み取りを禁止します。 (理由: CORS 要求が成功しませんでした)。 [詳細情報](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* クロムベース: ''https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' でのフェッチへのアクセスが CORS ポリシーによってブロックされました: プリフライト要求への応答はアクセス制御チェックに合格しません: 要求されたリソースに 'アクセス制御-許可-Origin' ヘッダーがありません。 非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。

特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

ブラウザは、 の設定`Access-Control-Request-Headers`方法に一貫性がありません。 次のいずれかの場合:

* ヘッダーは、次以外の値に設定されます。`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>呼び出し:`Accept`少`Content-Type`なくとも`Origin`、 、および を含め、サポートするカスタム ヘッダーを含めます。

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>自動プリフライトリクエストコード

CORS ポリシーが適用されると、次の手順が実行されます。

* を呼び出`app.UseCors`すことによって`Startup.Configure`グローバルに.
* 属性を`[EnableCors]`使用します。

ASP.NETコアは、プレフライトの OPTIONS 要求に応答します。

現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にすることは、自動プリフライト要求をサポート***していません***。

このドキュメントの[「テスト CORS」](#testc)セクションでは、この動作について説明します。

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>プリフライト要求の [Http オプション] 属性

適切なポリシーで CORS が有効になっている場合、ASP.NETコアは通常 CORS プリフライト要求に自動的に応答します。 シナリオによっては、この場合に該当しない場合があります。 たとえば、エンドポイント[ルーティングで CORS](#ecors)を使用します。

次のコードでは、OPTIONS 要求のエンドポイントを作成するために[[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)属性を使用します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

上記のコードのテスト手順については、「[エンドポイント ルーティングを使用した CORS のテスト」および「HttpOptions」](#tcer)を参照してください。

### <a name="set-the-preflight-expiration-time"></a>プリフライトの有効期限を設定する

ヘッダー`Access-Control-Max-Age`は、プリフライト要求に対する応答をキャッシュできる時間を指定します。 このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS のしくみ

このセクションでは、HTTP メッセージのレベルで[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求で何が起こるかについて説明します。

* CORS はセキュリティ機能**ではありません**。 CORS は、サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。
  * たとえば、悪意のあるアクターがサイトに対して[クロスサイト スクリプティング (XSS) を](xref:security/cross-site-scripting)使用し、CORS 対応サイトに対してクロスサイト要求を実行して情報を盗む可能性があります。
* API は CORS を許可することで安全ではありません。
  * CORS を強制するのはクライアント (ブラウザー) の管理です。 サーバーは要求を実行し、応答を返します、それはエラーを返し、応答をブロックするクライアントです。 たとえば、次のツールのどれでもサーバーの応答を表示します。
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET Http クライアント](/dotnet/csharp/tutorials/console-webapiclient)
    * アドレス バーに URL を入力して Web ブラウザを表示します。
* これは、ブラウザがクロスオリジン[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行することを許可するサーバーの方法です。
  * CORS を使用しないブラウザーは、クロスオリジン要求を行うことはできません。 CORS より前では[、JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)はこの制限を回避するために使用されていました。 JSONP は XHR を使用せず、`<script>`タグを使用して応答を受け取ります。 スクリプトは、クロスオリジンでロードすることができます。

[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にするいくつかの新しい HTTP ヘッダーが導入されました。 ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。 カスタム JavaScript コードは、CORS を有効にする必要はありません。

展開された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[PUT テスト ボタン](https://cors3.azurewebsites.net/test)

次に示しているのは、[値](https://cors3.azurewebsites.net/)テスト ボタンから に対するクロスオリジン要求`https://cors1.azurewebsites.net/api/values`の例です。 `Origin`ヘッダー:

* 要求を行うサイトのドメインを提供します。
* 必要であり、ホストとは異なる必要があります。

**一般ヘッダー**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**応答ヘッダー**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**要求ヘッダー**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

要求`OPTIONS`では、サーバーは応答ヘッダー**ヘッダーを**`Access-Control-Allow-Origin: {allowed origin}`応答に設定します。 たとえば、デプロイされた[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[[削除 [EnableCors]](https://cors1.azurewebsites.net/test?number=2)ボタン`OPTIONS`要求には、次のヘッダーが含まれています。

**一般ヘッダー**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**応答ヘッダー**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**要求ヘッダー**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

上記の**応答ヘッダーでは、** サーバーは応答に[アクセス制御-許可-オリジン](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)ヘッダーを設定します。 この`https://cors1.azurewebsites.net`ヘッダーの値は、要求`Origin`のヘッダーと一致します。

が<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>呼び出されると`Access-Control-Allow-Origin: *`、ワイルドカード値が返されます。 `AllowAnyOrigin`任意の原点を許可します。

応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。 具体的には、ブラウザーは要求を拒否します。 サーバーが成功した応答を返した場合でも、ブラウザーはクライアント アプリで応答を使用できません。

<a name="options"></a>

### <a name="display-options-requests"></a>オプション要求の表示

デフォルトでは、Chrome と Edge のブラウザでは、F12 ツールのネットワーク タブに OPTIONS リクエストは表示されません。 これらのブラウザで OPTIONS 要求を表示するには、次の手順を実行します。

* `chrome://flags/#out-of-blink-cors` または `edge://flags/#out-of-blink-cors`
* フラグを無効にします。
* [再起動] をタップします。

デフォルトでは、オプションリクエストが表示されます。

## <a name="cors-in-iis"></a>IIS の CORS

IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合は、Windows 認証の前に CORS を実行する必要があります。 このシナリオをサポートするには[、IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールし、アプリ用に構成する必要があります。

<a name="testc"></a>

## <a name="test-cors"></a>CORS のテスト

[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)には、CORS をテストするためのコードがあります。 [ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。 サンプルは、Razor ページが追加された API プロジェクトです。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`ダウンロード サンプル[コード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)に似たサンプル アプリのテストにのみ使用してください。

テストの`ValuesController`エンドポイントを次に示します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)は[、リック.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet パッケージによって提供され、ルート情報を表示します。

次のいずれかの方法を使用して、上記のサンプル コードをテストします。

* デプロイされたサンプル アプリを使用[https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)します。 サンプルをダウンロードする必要はありません。
* サンプルを実行するには`dotnet run`、既定の`https://localhost:5001`URL を使用します。
* の URL に対してポートを 44398 に設定して、Visual Studio からサンプルを実行`https://localhost:44398`します。

F12 ツールでブラウザを使用する場合:

* **[値**] ボタンを選択し、[**ネットワーク**] タブでヘッダーを確認します。
* PUT**テスト**ボタンを選択します。 [OPTIONS 要求の表示](#options)手順については、「OPTIONS 要求の表示」を参照してください。 **PUT テスト**では、オプションプリフライト要求と PUT 要求の 2 つの要求が作成されます。
* 失敗した**`GetValues2 [DisableCors]`** CORS 要求をトリガーするボタンを選択します。 ドキュメントで説明したように、応答は 200 成功を返しますが、CORS 要求は行いません。 **[コンソール**]タブを選択して、CORS エラーを表示します。 ブラウザによっては、次のようなエラーが表示されます。

     ORIGIN`'https://cors1.azurewebsites.net/api/values/GetValues2'``'https://cors3.azurewebsites.net'`からのフェッチへのアクセスが CORS ポリシーによってブロックされました: 要求されたリソースに 'アクセス制御-許可-元の元のヘッダーがありません。 非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。
     
CORS 対応のエンドポイントは[、curl](https://curl.haxx.se/) [、Fiddler](https://www.telerik.com/fiddler) [、Postman](https://www.getpostman.com/)などのツールを使用してテストできます。 ツールを使用する場合、ヘッダーで指定された要求の発信`Origin`元は、要求を受け取るホストと異なる必要があります。 要求がヘッダーの値に基づいて*クロスオリジン*でない場合: `Origin`

* 要求を処理するために CORS ミドルウェアは必要ありません。
* CORS ヘッダーは応答で返されません。

次のコマンドは`curl`、情報を含む OPTIONS 要求を発行するために使用します。

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>エンドポイント ルーティングを使用して CORS をテストし、[HttpOptions]

現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にしても、[自動プリフライト要求](#apf)はサポート***されません***。 エンドポイント ルーティングを使用して[CORS を有効にする次のコードを](#ecors)検討してください。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

テスト用`TodoItems1Controller`のエンドポイントを次に示します。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

展開された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[テスト ページ](https://cors1.azurewebsites.net/test?number=1)から、上記のコードをテストします。

エンドポイントがプリフライト要求を持ち、応答するため **、[EnableCors]** ボタンと GET `[EnableCors]` **[EnableCors]** ボタンは成功します。 他のエンドポイントは失敗します。 [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)が送信するため **、GET**ボタンが失敗します。

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

以下`TodoItems2Controller`に、同様のエンドポイントを示しますが、OPTIONS 要求に応答する明示的なコードが含まれています。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

展開されたサンプルの[テスト ページ](https://cors1.azurewebsites.net/test?number=2)から、上記のコードをテストします。 [**コントローラ**] ドロップダウン リストで、[**プリフライト**] を選択し、[**コントローラの設定]** を選択します。 `TodoItems2Controller`エンドポイントへの CORS 呼び出しはすべて成功します。

## <a name="additional-resources"></a>その他のリソース

* [クロスオリジンリソース共有(CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS モジュールの概要](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

著者 [Rick Anderson](https://twitter.com/RickAndMSFT)

この記事では、ASP.NET コア アプリで CORS を有効にする方法について説明します。

ブラウザのセキュリティにより、Web ページが Web ページを提供したドメインとは異なるドメインに対して要求を行えないようにします。 この制限は、*同一生成元ポリシー*と呼ばれます。 同一生成元ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取るのを防ぎます。 他のサイトがアプリに対してクロスオリジン要求を行うことを許可したい場合があります。 詳細については、 [Mozilla CORS の記事](https://developer.mozilla.org/docs/Web/HTTP/CORS)を参照してください。

[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):

* サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。
* セキュリティ機能**ではない**、CORS はセキュリティを緩和します。 API は CORS を許可することで安全ではありません。 詳細については[、「CORS の仕組み](#how-cors)」を参照してください。
* サーバーが、一部のクロスオリジン要求を許可し、その他の要求を拒否できるようにします。
* [JSONP](/dotnet/framework/wcf/samples/jsonp)などの以前の手法よりも安全で柔軟性があります。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="same-origin"></a>同じ起源

2 つの URL が同じスキーム、ホスト、およびポートを持つ場合は、同じオリジンを持ちます ([RFC 6454](https://tools.ietf.org/html/rfc6454))。

これらの 2 つの URL のオリジンは同じです。

* `https://example.com/foo.html`
* `https://example.com/bar.html`

これらの URL のオリジンは、前の 2 つの URL とは異なります。

* `https://example.net`&ndash;異なるドメイン
* `https://www.example.com/foo.html`&ndash;異なるサブドメイン
* `http://example.com/foo.html`&ndash;異なるスキーム
* `https://example.com:9000/foo.html`&ndash;異なるポート

オリジンを比較する際に、このポートは考慮されません。

## <a name="cors-with-named-policy-and-middleware"></a>名前付きポリシーとミドルウェアを使用した CORS

CORS ミドルウェアは、クロスオリジン要求を処理します。 次のコードでは、指定したオリジンを持つアプリ全体の CORS を有効にします。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

上のコードでは以下の操作が行われます。

* ポリシー名を "myAllowSpecificOrigins"\_に設定します。 ポリシー名は任意です。
* 拡張メソッド<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>を呼び出して、CORS を有効にします。
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して呼び出します。 ラムダはオブジェクト<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>を受け取ります。 などの`WithOrigins`[構成オプション](#cors-policy-options)については、この記事の後半で説明します。

メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しは、アプリのサービス コンテナーに CORS サービスを追加します。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

詳細については、このドキュメントの[CORS ポリシー オプション](#cpo)を参照してください。

メソッド<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>は、次のコードに示すように、メソッドをチェインできます。

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

注意: URL**not**に末尾のスラッシュ (`/`) を含めることはできません。 URL が で`/`終わる場合、比較`false`は返され、ヘッダーは返されません。

次のコードでは、CORS ミドルウェアを介してすべてのアプリ エンドポイントに CORS ポリシーを適用します。
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
注:`UseCors`前に`UseMvc`呼び出す必要があります。

ページ/コント ローラー/アクション レベルで CORS ポリシーを適用するには[、Razor ページ、コント ローラー、およびアクション メソッド](#ecors)で CORS を有効にするを参照してください。

上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#test)を参照してください。

## <a name="enable-cors-with-attributes"></a>属性を使用した CORS の有効化

[ &lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。 この`[EnableCors]`属性は、すべての終点ではなく、選択した終点に対して CORS を有効にします。

既定`[EnableCors]`のポリシーを指定し、`[EnableCors("{Policy String}")]`ポリシーを指定するために使用します。

属性`[EnableCors]`は、次の場合に適用できます。

* カミソリページ`PageModel`
* コントローラー
* コントローラアクションメソッド

属性を使用して、コントローラ/ページモデル/アクションに異なるポリシー`[EnableCors]`を適用できます。 属性が`[EnableCors]`コントローラ/ページ モデル/アクション メソッドに適用され、CORS がミドルウェアで有効になっている場合、***両方***のポリシーが適用されます。 ポリシーを組み合わせることはお勧め***しません***。 属性または`[EnableCors]`ミドルウェアを使用します。**not both** を使用`[EnableCors]`する場合は、既定のポリシーを定義**しないでください**。

次のコードでは、各メソッドに異なるポリシーを適用します。

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

次のコードでは、CORS のデフォルト ポリシーと`"AnotherPolicy"`名前のポリシーを作成します。

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS を無効にする

[ &lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラ/ページ モデル/アクションの CORS を無効にします。

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS ポリシー オプション

このセクションでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。

* [許可された原点を設定する](#set-the-allowed-origins)
* [許可される HTTP メソッドを設定する](#set-the-allowed-http-methods)
* [許可された要求ヘッダーを設定する](#set-the-allowed-request-headers)
* [公開された応答ヘッダーを設定する](#set-the-exposed-response-headers)
* [クロスオリジン要求の資格情報](#credentials-in-cross-origin-requests)
* [プリフライトの有効期限を設定する](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>が で`Startup.ConfigureServices`呼び出されます。 いくつかのオプションについては、[最初に「CORS の仕組み](#how-cors)」のセクションを読んでも役に立つ場合があります。

## <a name="set-the-allowed-origins"></a>許可された原点を設定する

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;任意のスキーム ( または`http``https`) を持つすべてのオリジンからの CORS 要求を許可します。 `AllowAnyOrigin`*どのウェブサイトも*アプリにクロスオリジン要求を行うことができるため、安全ではありません。

> [!NOTE]
> 指定され`AllowAnyOrigin`、`AllowCredentials`安全でない構成であり、クロスサイトリクエストフォージェリにつながる可能性があります。 セキュリティで保護されたアプリの場合、クライアントがサーバー リソースにアクセスする権限を持つ必要がある場合は、発信元の正確な一覧を指定します。

`AllowAnyOrigin`はプリフライト要求とヘッダー`Access-Control-Allow-Origin`に影響します。 詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;ポリシーの<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>プロパティを、オリジンが許可されているかどうかを評価するときに、設定されたワイルドカード ドメインとオリジンが一致するように設定します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>許可される HTTP メソッドを設定する

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 任意の HTTP メソッドを許可します。
* プリフライト要求とヘッダーに`Access-Control-Allow-Methods`影響します。 詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。

### <a name="set-the-allowed-request-headers"></a>許可された要求ヘッダーを設定する

特定のヘッダーを CORS 要求で送信できるようにするには *、author 要求ヘッダー*と呼<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>びます。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

この設定は、プリフライト要求と`Access-Control-Request-Headers`ヘッダーに影響します。 詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。

CORS ミドルウェアでは、CorsPolicy.Headers で構成されている値`Access-Control-Request-Headers`に関係なく、常に 4 つのヘッダーを送信できます。 このヘッダーのリストには、次の項目が含まれます。

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

たとえば、次のように構成されたアプリを考えてみます。

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS ミドルウェアは、常にホワイトリストに登録されているため`Content-Language`、次の要求ヘッダーを使用してプリフライト要求に正常に応答します。

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>公開された応答ヘッダーを設定する

既定では、ブラウザーは、すべての応答ヘッダーをアプリに公開しません。 詳細については、「 [W3C クロス オリジン リソース共有 (用語集): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。

デフォルトで使用可能な応答ヘッダーは次のとおりです。

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 仕様では、これらのヘッダーの*単純な応答ヘッダーを*呼び出します。 他のヘッダーをアプリで使用できるようにするには、次の<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>呼び出しを行います。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>クロスオリジン要求の資格情報

資格情報には、CORS 要求での特別な処理が必要です。 既定では、ブラウザーはクロスオリジン要求を含む資格情報を送信しません。 資格情報には、Cookie と HTTP 認証スキームが含まれます。 クロスオリジン要求を使用して資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials``true`に設定する必要があります。

直接`XMLHttpRequest`使用:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

jQuery を使用する:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

フェッチ[API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用 :

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

サーバーは資格情報を許可する必要があります。 クロスオリジンの資格情報を許可するには、次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>の呼び出しを行います。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 応答には`Access-Control-Allow-Credentials`ヘッダーが含まれており、サーバーがクロスオリジン要求の資格情報を許可することをブラウザーに伝えます。

ブラウザーが資格情報を送信しても、応答に有効な`Access-Control-Allow-Credentials`ヘッダーが含まれていない場合、ブラウザーはアプリに対する応答を公開せず、クロスオリジン要求は失敗します。

クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。 別のドメインの Web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 仕様では、ヘッダーが存在する場合`"*"`、(すべての起点) に原点`Access-Control-Allow-Credentials`を設定することは無効であるとも述べています。

### <a name="preflight-requests"></a>プリフライトリクエスト

一部の CORS 要求では、ブラウザーは実際の要求を行う前に追加の要求を送信します。 この要求は *、プリフライト要求*と呼ばれます。 次の条件に該当する場合、ブラウザはプリフライトリクエストをスキップできます。

* 要求メソッドは、GET、HEAD、または POST です。
* アプリ`Accept`は、 、 、 `Accept-Language`、 、、`Content-Language``Content-Type`または`Last-Event-ID`以外の要求ヘッダーを設定しません。
* ヘッダー`Content-Type`が設定されている場合、次のいずれかの値が設定されます。
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

クライアント要求に設定された要求ヘッダーのルールは、アプリケーションがオブジェクトを呼び出`setRequestHeader`すことによって設定するヘッダーに`XMLHttpRequest`適用されます。 CORS 仕様では、これらのヘッダー*の作成者要求ヘッダーを*呼び出します。 ルールは、 、 、 など、`User-Agent``Host`ブラウザで設定できるヘッダーには適用されません。 `Content-Length`

プリフライト要求の例を次に示します。

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

プレフライト要求では、HTTP OPTIONS メソッドが使用されます。 これには、次の 2 つの特別なヘッダーが含まれます。

* `Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。
* `Access-Control-Request-Headers`: アプリが実際の要求に設定する要求ヘッダーの一覧。 前述のように、ブラウザーが設定するヘッダーは含みません`User-Agent`。

<!-- I think this needs to be removed, was put here accidently -->

適切なポリシーで CORS が有効になっている場合、通常、ASP.NETコアは CORS プリフライト要求に自動的に応答します。 [プリフライト要求の場合は[HttpOptions] 属性を](#pro)参照してください。

CORS プリフライト要求には、実際`Access-Control-Request-Headers`の要求と共に送信されるヘッダーをサーバーに示すヘッダーが含まれる場合があります。

特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

ブラウザは、設定方法に完全に一貫`Access-Control-Request-Headers`しているわけではありません。 ヘッダーを ( または を使用`"*"`<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>) 以外に設定する場合は`Accept`、 `Content-Type`、 `Origin`、および 、 、および をサポートするカスタム ヘッダーを含める必要があります。

次に、プリフライト要求に対する応答の例を示します (サーバーが要求を許可すると仮定します)。

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

応答には、`Access-Control-Allow-Methods`許可されたメソッドと、許可されたヘッダーをリストする`Access-Control-Allow-Headers`ヘッダー (オプション) を示すヘッダーが含まれます。 プリフライト要求が成功すると、ブラウザは実際のリクエストを送信します。

プリフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーは返しません。 したがって、ブラウザーは、クロスオリジン要求を試行しません。

### <a name="set-the-preflight-expiration-time"></a>プリフライトの有効期限を設定する

ヘッダー`Access-Control-Max-Age`は、プリフライト要求に対する応答をキャッシュできる時間を指定します。 このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS のしくみ

このセクションでは、HTTP メッセージのレベルで[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求で何が起こるかについて説明します。

* CORS はセキュリティ機能**ではありません**。 CORS は、サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。
  * たとえば、悪意のあるアクターがサイトに対して[クロスサイト スクリプティング (XSS) を](xref:security/cross-site-scripting)使用し、CORS 対応サイトに対してクロスサイト要求を実行して情報を盗む可能性があります。
* API は CORS を許可することで安全ではありません。
  * CORS を強制するのはクライアント (ブラウザー) の管理です。 サーバーは要求を実行し、応答を返します、それはエラーを返し、応答をブロックするクライアントです。 たとえば、次のツールのどれでもサーバーの応答を表示します。
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET Http クライアント](/dotnet/csharp/tutorials/console-webapiclient)
    * アドレス バーに URL を入力して Web ブラウザを表示します。
* これは、ブラウザがクロスオリジン[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行することを許可するサーバーの方法です。
  * ブラウザ (CORS を使用しない) は、クロスオリジン要求を行うことはできません。 CORS より前では[、JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)はこの制限を回避するために使用されていました。 JSONP は XHR を使用せず、`<script>`タグを使用して応答を受け取ります。 スクリプトは、クロスオリジンでロードすることができます。

[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にするいくつかの新しい HTTP ヘッダーが導入されました。 ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。 カスタム JavaScript コードは、CORS を有効にする必要はありません。

次に、クロスオリジン要求の例を示します。 ヘッダー`Origin`は、要求を行っているサイトのドメインを提供します。 ヘッダー`Origin`は必須であり、ホストとは異なる必要があります。

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

サーバーが要求を許可する場合、応答の`Access-Control-Allow-Origin`ヘッダーを設定します。 このヘッダーの値は、要求の`Origin`ヘッダーと一致するか、ワイルドカード値`"*"`で、任意のオリジンが許可されていることを意味します。

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

応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。 具体的には、ブラウザーは要求を拒否します。 サーバーが成功した応答を返した場合でも、ブラウザーはクライアント アプリで応答を使用できません。

<a name="test"></a>

## <a name="test-cors"></a>CORS のテスト

CORS をテストするには:

1. [API プロジェクトを作成する](xref:tutorials/first-web-api) または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。
1. このドキュメントの方法のいずれかを使用して CORS を有効にします。 次に例を示します。

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`ダウンロード サンプル[コード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)に似たサンプル アプリのテストにのみ使用してください。

1. Web アプリ プロジェクト (Razor ページまたは MVC) を作成します。 サンプルでは、Razor ページを使用します。 API プロジェクトと同じソリューションで Web アプリを作成できます。
1. 次の強調表示されたコードを*Index.cshtml*ファイルに追加します。

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 上記のコードで、デプロイされた`url: 'https://<web app>.azurewebsites.net/api/values/1',`アプリの URL で置き換えます。
1. API プロジェクトをデプロイします。 たとえば、 [Azure にデプロイします](xref:host-and-deploy/azure-apps/index)。
1. デスクトップから Razor ページまたは MVC アプリを実行し、[**テスト**] ボタンをクリックします。 F12 ツールを使用して、エラー メッセージを確認します。
1. ローカルホストのオリジンをアプリ`WithOrigins`から削除し、アプリを展開します。 または、別のポートでクライアント アプリを実行します。 たとえば、Visual Studio から実行します。
1. クライアント アプリでテストします。 CORS のエラーはエラーを返しますが、エラー メッセージは JavaScript で使用できません。 F12 ツールのコンソール タブを使用して、エラーを表示します。 ブラウザーによっては、次のようなエラー (F12 ツール コンソール) が表示されます。

   * マイクロソフトエッジを使用する:

     **SEC7120: [CORS]`https://localhost:44375`オリジンが、`https://localhost:44375`クロスオリジンリソースのアクセス制御許可-オリジン応答ヘッダーで見つかりませんでした。`https://webapi.azurewebsites.net/api/values/1`**

   * クロムの使用:

     **元`https://webapi.azurewebsites.net/api/values/1``https://localhost:44375`の XMLHttpRequest へのアクセスが CORS ポリシーによってブロックされました: 要求されたリソースに 'アクセス制御-許可-元のヘッダー' が存在しません。**
     
CORS 対応のエンドポイントは[、Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。 ツールを使用する場合、ヘッダーで指定された要求の発信`Origin`元は、要求を受け取るホストと異なる必要があります。 要求がヘッダーの値に基づいて*クロスオリジン*でない場合: `Origin`

* 要求を処理するために CORS ミドルウェアは必要ありません。
* CORS ヘッダーは応答で返されません。

## <a name="cors-in-iis"></a>IIS の CORS

IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合は、Windows 認証の前に CORS を実行する必要があります。 このシナリオをサポートするには[、IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールし、アプリ用に構成する必要があります。

## <a name="additional-resources"></a>その他のリソース

* [クロスオリジンリソース共有(CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS モジュールの概要](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
