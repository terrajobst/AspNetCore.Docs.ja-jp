---
title: HTTP ハンドラーとモジュールを ASP.NET Core ミドルウェアに移行する
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: bdf27ccb742d4bc05bac71e6c96d71c38dcb4b62
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975491"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>HTTP ハンドラーとモジュールを ASP.NET Core ミドルウェアに移行する

この記事では、既存の ASP.NET [HTTP モジュールとハンドラーを system.webserver から](/iis/configuration/system.webserver/)ASP.NET Core[ミドルウェア](xref:fundamentals/middleware/index)に移行する方法について説明します。

## <a name="modules-and-handlers-revisited"></a>再検討されたモジュールとハンドラー

ミドルウェアの ASP.NET Core に進む前に、まず HTTP モジュールとハンドラーがどのように機能するかについて説明します。

![モジュールハンドラー](http-modules/_static/moduleshandlers.png)

**ハンドラーは次のとおりです。**

* [IHttpHandler](/dotnet/api/system.web.ihttphandler)を実装するクラス

* 指定されたファイル名または拡張子を持つ要求を処理するために使用され*ます。レポートなどです。*

* *Web.config で*[構成済み](/iis/configuration/system.webserver/handlers/)

**モジュールは次のとおりです。**

* [IHttpModule](/dotnet/api/system.web.ihttpmodule)を実装するクラス

* すべての要求に対して呼び出されます

* ショートサーキットを可能にする (要求の後続の処理を停止する)

* HTTP 応答に追加することも、独自の応答を作成することもできます。

* *Web.config で*[構成済み](/iis/configuration/system.webserver/modules/)

**受信要求を処理するモジュールの順序は、次のように決定されます。**

1. [アプリケーションライフサイクル](https://msdn.microsoft.com/library/ms227673.aspx)は、ASP.NET によって発生するシリーズイベントです。[BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest)、 [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)、その他各モジュールは、1つまたは複数のイベントのハンドラーを作成できます。

2. 同じイベントについて、web.config で構成されている*順序。*

モジュールに加えて、ライフサイクルイベントのハンドラーを*Global.asax.cs*ファイルに追加することができます。 これらのハンドラーは、構成されているモジュールのハンドラーの後に実行されます。

## <a name="from-handlers-and-modules-to-middleware"></a>ハンドラーとモジュールからミドルウェアへ

**ミドルウェアは、HTTP モジュールとハンドラーよりも簡単です。**

* モジュール、ハンドラー、 *Global.asax.cs*、WEB.CONFIG (IIS 構成を除く)、およびアプリケーションのライフサイクルが失われています *。*

* ミドルウェアによってモジュールとハンドラーの両方のロールが取得されています。

* ミドルウェアは、web.config ではなくコードを使用して構成され*ます。*

* [パイプライン分岐](xref:fundamentals/middleware/index#use-run-and-map)を使用すると、URL だけでなく、要求ヘッダー、クエリ文字列などに基づいて、特定のミドルウェアに要求を送信できます。

**ミドルウェアはモジュールとよく似ています。**

* すべての要求に対して、原則として呼び出されます。

* 要求を[次のミドルウェアに渡す](#http-modules-shortcircuiting-middleware)ことによって、要求をショートサーキットできます。

* 独自の HTTP 応答を作成できる

**ミドルウェアとモジュールは、別の順序で処理されます。**

* ミドルウェアの順序は、要求パイプラインに挿入される順序に基づいていますが、モジュールの順序は主に[アプリケーションライフサイクル](https://msdn.microsoft.com/library/ms227673.aspx)イベントに基づいています。

* 応答のミドルウェアの順序は、要求の場合と逆になりますが、モジュールの順序は要求と応答に対して同じです。

* 「 [IApplicationBuilder を使用したミドルウェアパイプラインの作成](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)」を参照してください。

![ミドルウェア](http-modules/_static/middleware.png)

上の図のように、認証ミドルウェアは要求のショートサーキットを行います。

## <a name="migrating-module-code-to-middleware"></a>ミドルウェアへのモジュールコードの移行

既存の HTTP モジュールは次のようになります。

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

[ミドルウェア](xref:fundamentals/middleware/index)ページに示されているように、ASP.NET Core ミドルウェアは、を取得`Invoke` `HttpContext`してを`Task`返すメソッドを公開するクラスです。 新しいミドルウェアは次のようになります。

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

前のミドルウェアテンプレートは、[ミドルウェアの作成](xref:fundamentals/middleware/write)に関するセクションから取得されました。

*MyMiddlewareExtensions* helper クラスを使用すると、 `Startup`クラスでミドルウェアを簡単に構成できます。 メソッド`UseMyMiddleware`は、要求パイプラインにミドルウェアクラスを追加します。 ミドルウェアが必要とするサービスは、ミドルウェアのコンストラクターに挿入されます。

<a name="http-modules-shortcircuiting-middleware"></a>

ユーザーが承認されていないなど、モジュールが要求を終了する場合があります。

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

ミドルウェアは、パイプラインの次の`Invoke`ミドルウェアでを呼び出さずにこの処理を行います。 これは、要求を完全に終了しないことに注意してください。これは、応答がパイプラインを通過するときに前のミドルウェアが呼び出されるためです。

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

モジュールの機能を新しいミドルウェアに移行するときに、 `HttpContext`クラスが ASP.NET Core で大幅に変更されているため、コードがコンパイルされないことがあります。 [後](#migrating-to-the-new-httpcontext)で、新しい ASP.NET Core HttpContext に移行する方法について説明します。

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>要求パイプラインへのモジュール挿入の移行

HTTP モジュールは通常、web.config を使用して要求パイプラインに追加さ*れます。*

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

`Startup`クラスの要求パイプラインに[新しいミドルウェアを追加](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)して、これを変換します。

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

新しいミドルウェアを挿入するパイプライン内の正確な位置は、モジュール (`BeginRequest`、 `EndRequest`など) として処理されたイベントと、web.config 内のモジュールの一覧での順序によって異なります。

前述のように、ASP.NET Core にはアプリケーションのライフサイクルがなく、ミドルウェアによって応答が処理される順序は、モジュールで使用される順序とは異なります。 これにより、順序を決定することが困難になる可能性があります。

順序付けが問題になった場合は、個別に並べ替えることのできる複数のミドルウェアコンポーネントにモジュールを分割できます。

## <a name="migrating-handler-code-to-middleware"></a>ミドルウェアへのハンドラーコードの移行

HTTP ハンドラーは次のようになります。

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

ASP.NET Core プロジェクトでは、これを次のようなミドルウェアに変換します。

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

このミドルウェアは、モジュールに対応するミドルウェアと非常によく似ています。 実際の違いは、ここではへ`_next.Invoke(context)`の呼び出しがないことです。 これは、ハンドラーが要求パイプラインの最後にあるため、次に呼び出すミドルウェアが存在しないため、意味があります。

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>要求パイプラインへのハンドラー挿入の移行

HTTP ハンドラーの構成は web.config で行われ、次のように*なります。*

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

これを変換するには、新しいハンドラーミドルウェアを`Startup`クラスの要求パイプラインに追加します。これは、モジュールから変換されたミドルウェアに似ています。 この方法の問題は、すべての要求を新しいハンドラーミドルウェアに送信することです。 ただし、特定の拡張機能を持つ要求のみがミドルウェアに到着するようにします。 これにより、HTTP ハンドラーと同じ機能が提供されます。

1つの解決策は、 `MapWhen`拡張メソッドを使用して、特定の拡張機能を持つ要求に対してパイプラインを分岐することです。 これは、もう一方の`Configure`ミドルウェアを追加するのと同じ方法で行います。

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen`次のパラメーターを取得します。

1. を受け取る`HttpContext`ラムダ。要求が分岐`true`を下位に移動する必要がある場合は、を返します。 これは、拡張機能だけでなく、要求ヘッダー、クエリ文字列パラメーターなどにもブランチ要求を分岐できることを意味します。

2. を受け取り`IApplicationBuilder` 、分岐のすべてのミドルウェアを追加するラムダ。 これは、ハンドラーミドルウェアの前に分岐にミドルウェアを追加できることを意味します。

ブランチがすべての要求で呼び出される前に、ミドルウェアがパイプラインに追加されました。ブランチには影響がありません。

## <a name="loading-middleware-options-using-the-options-pattern"></a>オプションパターンを使用したミドルウェアオプションの読み込み

一部のモジュールとハンドラーには、 *web.config に格納*されている構成オプションがあります。ただし ASP.NET Core では、web.config の代わりに新しい構成モデルが使用さ*れます。*

新しい[構成システム](xref:fundamentals/configuration/index)には、これを解決するための次のオプションが用意されています。

* [次のセクション](#loading-middleware-options-through-direct-injection)で示すように、ミドルウェアにオプションを直接挿入します。

* オプションの[パターン](xref:fundamentals/configuration/options)を使用します。

1. ミドルウェアオプションを保持するクラスを作成します。次に例を示します。

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. オプションの値を格納する

   構成システムでは、オプションの値を任意の場所に格納できます。 ただし、ほとんどのサイトでは*appsettings*を使用しているので、次の方法を使用します。

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection*ここにはセクション名があります。 オプションクラスの名前と同じである必要はありません。

3. オプションの値を options クラスに関連付ける

    オプションのパターンでは ASP.NET Core の依存関係挿入フレームワークを使用して`MyMiddlewareOptions`、オプションの種類 (など) を実際のオプションを持つ`MyMiddlewareOptions`オブジェクトに関連付けます。

    クラスを`Startup`更新します。

   1. *Appsettings*を使用している場合は、 `Startup`コンストラクターの構成ビルダーに追加します。

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. オプションサービスを構成します。

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. オプションをオプションクラスに関連付けます。

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. ミドルウェアコンストラクターにオプションを挿入します。 これは、コントローラーにオプションを挿入するのと似ています。

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   にミドルウェアを`IApplicationBuilder`追加する [UseMiddleware](#http-modules-usemiddleware) 拡張メソッドは、依存関係の挿入を行います。

   これはオブジェクトに`IOptions`限定されません。 ミドルウェアが必要とするその他のオブジェクトは、この方法で挿入できます。

## <a name="loading-middleware-options-through-direct-injection"></a>直接挿入によるミドルウェアオプションの読み込み

オプションのパターンには、オプションの値とコンシューマーの間に疎結合が作成されるという利点があります。 オプションクラスを実際のオプション値に関連付けた後、他のクラスは、依存関係挿入フレームワークを通じてオプションにアクセスできます。 オプションの値を渡す必要はありません。

これは、異なるオプションを使用して同じミドルウェアを2回使用する場合には、このようになります。 たとえば、異なるブランチで使用される承認ミドルウェアは、さまざまなロールを許可します。 2つの異なる options オブジェクトを1つの options クラスに関連付けることはできません。

ソリューションでは、オプションオブジェクトを`Startup`クラスの実際のオプション値で取得し、ミドルウェアの各インスタンスに直接渡すことができます。

1. *Appsettings*に2番目のキーを追加する

   *Appsettings*ファイルに2つ目のオプションセットを追加するには、新しいキーを使用してそれを一意に識別します。

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. オプションの値を取得し、ミドルウェアに渡します。 (ミドルウェアをパイプラインに追加する)拡張メソッドは、オプション値を渡す論理的な場所です。`Use...` 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. ミドルウェアがオプションパラメーターを受け取ることができるようにします。 `Use...`拡張メソッド (options パラメーターを受け取り、に`UseMiddleware`渡す) のオーバーロードを指定します。 パラメーター `UseMiddleware`を使用してを呼び出すと、ミドルウェアオブジェクトをインスタンス化するときに、ミドルウェアコンストラクターにパラメーターが渡されます。

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   これにより、 `OptionsWrapper`オブジェクトの options オブジェクトがどのようにラップされるかに注目してください。 これは`IOptions`、ミドルウェアコンストラクターによって想定されているとおりにを実装します。

## <a name="migrating-to-the-new-httpcontext"></a>新しい HttpContext への移行

前に、ミドルウェア内`Invoke`のメソッドが型`HttpContext`のパラメーターを受け取ることを確認しました。

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext`は ASP.NET Core で大幅に変更されました。 このセクションでは、 [system.web](/dotnet/api/system.web.httpcontext)の最もよく使用されるプロパティを新しい`Microsoft.AspNetCore.Http.HttpContext`に変換する方法について説明します。

### <a name="httpcontext"></a>HttpContext

HttpContext は次のように変換さ**れます。**

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**一意の要求 ID (対応する System.web)**

要求ごとに一意の id を提供します。 ログに含めるのに非常に便利です。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpMethod**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**Httpcontext.current**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**Httpcontext. url**と**httpcontext.current url**はに変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. IsSecureConnection**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. UserHostAddress**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext。クッキー**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext。ヘッダー**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**UserAgent**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**Httpcontext.current**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext。 ContentType**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext。フォーム**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> コンテンツサブタイプが*url エンコード*または*フォームデータ*の場合にのみ、フォーム値を読み取ります。

**InputStream**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> このコードは、パイプラインの最後のハンドラー型ミドルウェアでのみ使用してください。
>
>前に示したように、未加工の本文は、要求ごとに1回だけ読み取ることができます。 最初の読み取り後に、ミドルウェアが本文を読み取ろうとすると、空の本文が読み取られます。
>
>これは、前に示したように、フォームの読み取りには適用されません。これは、バッファーから実行されるためです。

### <a name="httpcontextresponse"></a>HttpContext.Response

**Httpcontext.current**は、次のように変換されます。 **statusdescription** :

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**Httpcontext.current**は、次のように**変換します。**

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

また、その独自の**ContentType**にも、次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**出力**は次のように変換されます。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

ファイルの提供については、[こちら](../fundamentals/request-features.md#middleware-and-request-features)を参照してください。

**HttpContext.Response.Headers**

応答ヘッダーの送信は、応答本文に何かが書き込まれた後に設定した場合、送信されないという事実により複雑になります。

解決策として、応答の書き込みが開始される前に、右に呼び出されるコールバックメソッドを設定します。 これは、ミドルウェアの`Invoke`メソッドの開始時に実行することをお勧めします。 応答ヘッダーを設定するコールバックメソッドです。

次のコードは、という`SetHeaders`コールバックメソッドを設定します。

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

コール`SetHeaders`バックメソッドは次のようになります。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

Cookie は、cookie の*セットクッキー*応答ヘッダーでブラウザーに移動します。 そのため、cookie を送信するには、応答ヘッダーの送信に使用するのと同じコールバックが必要です。

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

コール`SetCookies`バックメソッドは次のようになります。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>その他の技術情報

* [HTTP ハンドラーと HTTP モジュールの概要](/iis/configuration/system.webserver/)
* [構成](xref:fundamentals/configuration/index)
* [アプリケーションの起動](xref:fundamentals/startup)
* [ミドルウェア](xref:fundamentals/middleware/index)
