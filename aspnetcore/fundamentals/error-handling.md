---
title: ASP.NET Core のエラーを処理する
author: rick-anderson
description: ASP.NET Core アプリでエラーを処理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/error-handling
ms.openlocfilehash: a610c42d75864259b609e11b8bf0776c5ab8e507
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288848"
---
# <a name="handle-errors-in-aspnet-core"></a>ASP.NET Core のエラーを処理する

作成者: [Tom Dykstra](https://github.com/tdykstra/)、[Luke Latham](https://github.com/guardrex)、[Steve Smith](https://ardalis.com/)

この記事では、ASP.NET Core アプリでエラーを処理するための一般的な手法について取り上げます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)します。 ([ダウンロード方法](xref:index#how-to-download-a-sample)。)この記事には、サンプル アプリでプリプロセッサ ディレクティブ (`#if`、`#endif`、`#define`) を設定して、異なるシナリオを有効にする方法についての説明が含まれます。

## <a name="developer-exception-page"></a>開発者例外ページ

"*開発者例外ページ*" には、要求の例外に関する詳細な情報が表示されます。 そのページは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。 アプリを開発[環境](xref:fundamentals/environments)で実行するときにページを有効にするには、`Startup.Configure` メソッドにコードを追加します。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

例外をキャッチしたいミドルウェアの前に、<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> の呼び出しを配置します。

> [!WARNING]
> **アプリを開発環境で実行するときにのみ**、開発者例外ページを有効にします。 アプリを実稼働環境で実行するときは、詳細な例外情報を公開しません。 環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。

このページには、例外と要求に関する次の情報が含まれています。

* スタック トレース
* クエリ文字列のパラメーター (ある場合)
* Cookie (ある場合)
* ヘッダー

[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で開発者例外ページを表示するには、`DevEnvironment` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。

## <a name="exception-handler-page"></a>例外ハンドラー ページ

運用環境用のカスタム エラー処理ページを構成するには、例外処理ミドルウェアを使用します。 ミドルウェアでは次を行います。

* 例外をキャッチしてログに記録します。
* ページ用の、またはコントローラーが指定した別のパイプラインで、要求を再実行します。 応答が始まっていた場合、要求は再実行されません。

次の例では、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> により非開発環境に例外処理ミドルウェアが追加されます。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Razor Pages アプリのテンプレートには、エラー ページ ( *.cshtml*) と <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> クラス (`ErrorModel`) が *Pages* フォルダー内に用意されています。 MVC アプリの場合、プロジェクト テンプレートにはエラー アクション メソッドとエラー ビューが含まれています。 アクション メソッドを次に示します。

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

`HttpGet` などの HTTP メソッド属性を使ってエラー ハンドラー アクション メソッドを修飾しないでください。 明示的な動詞を使用すると、要求がメソッドに届かないことがあります。 認証されていないユーザーがエラー ビューを受信できるように、メソッドへの匿名アクセスを許可します。

### <a name="access-the-exception"></a>例外にアクセスする

エラー ハンドラー コントローラーまたはページ内で例外や元の要求パスにアクセスするには、<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> を使います。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> 機密性の高いエラー情報をクライアントに提供**しないでください**。 エラーの提供はセキュリティ上のリスクです。

[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で例外処理ページを表示するには、`ProdEnvironment` および `ErrorHandlerPage` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。

## <a name="exception-handler-lambda"></a>例外ハンドラー ラムダ

[カスタム例外ハンドラー ページ](#exception-handler-page)の代わりになるのは、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> にラムダを提供することです。 ラムダを使うと、応答を返す前にエラーにアクセスできます。

例外処理にラムダを使う例を次に示します。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> または <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> からの機密性の高いエラー情報をクライアントに提供**しないでください**。 エラーの提供はセキュリティ上のリスクです。

[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で例外処理ラムダの結果を表示するには、`ProdEnvironment` および `ErrorHandlerLambda` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。

## <a name="usestatuscodepages"></a>UseStatusCodePages

ASP.NET Core アプリでは、既定で、"*404 - 見つかりません*" などの HTTP 状態コードの状態コード ページが表示されません。 アプリでは、状態コードと空の応答本文が返されます。 状態コード ページを提供するには、状態コード ページ ミドルウェアを使用します。

そのミドルウェアは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内にある [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。

一般的なエラー状態コード用に既定のテキスト専用ハンドラーを有効にするには、`Startup.Configure` メソッドで <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> を呼び出します。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

要求処理ミドルウェア (たとえば、静的ファイル ミドルウェアや MVC ミドルウェア) の前に `UseStatusCodePages` を呼び出します。

既定のハンドラーによって表示されるテキストの例を次に示します。

```
Status Code: 404; Not Found
```

[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)でさまざまな状態コード ページ形式のいずれかを表示するには、`StatusCodePages` で始まるプリプロセッサ ディレクティブのいずれかを使用して、ホーム ページの **[Trigger a 404]\(404 をトリガーする\)** を選択します。

## <a name="usestatuscodepages-with-format-string"></a>書式設定文字列での UseStatusCodePages

応答の内容の種類とテキストをカスタマイズするには、内容の種類と書式文字列を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> のオーバーロードを使います。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>ラムダでの UseStatusCodePages

カスタム エラー処理と応答書き込みコードを指定するには、ラムダ式を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> のオーバーロードを使います。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> 拡張メソッド:

* クライアントに *302 - Found* 状態コードを送信します。
* URL テンプレートで指定された場所にクライアントをリダイレクトします。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

次の例で示すように、URL テンプレートには状態コード用の `{0}` プレースホルダーを含めることができます。 URL テンプレートがチルダ (~) で始まっている場合、チルダはアプリの `PathBase` に置き換えられます。 アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。 Razor Pages の例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。

この方法は、次のようなアプリで一般的に使用されます。

* クライアントを別のエンドポイントにリダイレクトする必要がある場合 (通常は、別のアプリがエラーを処理する場合)。 Web アプリの場合は、クライアントのブラウザーのアドレス バーにリダイレクトされたエンドポイントが反映されます。
* 元の状態コードを保持し、初回のリダイレクト応答で返してはいけない場合。

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> 拡張メソッド:

* 元の状態コードをクライアントに返します。
* 代替パスを使用して要求パイプラインを再実行することで、応答本文を生成します。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。 Razor Pages の例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。

この方法は、次のようなアプリで一般的に使用されます。

* 別のエンドポイントにリダイレクトすることなく要求を処理する。 Web アプリの場合は、クライアントのブラウザーのアドレス バーに、初めに要求されていたエンドポイントが反映されます。
* 元の状態コードを保持し、応答で返す。

URL とクエリ文字列のテンプレートには、状態コード用のプレースホルダー (`{0}`) を含めることができます。 URL テンプレートの先頭には、スラッシュ (`/`) を付ける必要があります。 パスでプレースホルダーを使う場合は、エンドポイント (ページまたはコントローラー) でパスのセグメントを処理できることを確認します。 たとえば、エラー用の Razor ページでは、`@page` ディレクティブの付いた省略可能なパスのセグメント値を受け入れる必要があります。

```cshtml
@page "{code?}"
```

次の例で示すように、エラーを処理するエンドポイントでは、エラーを生成した元の URL を取得できます。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>状態コード ページを無効にする

MVC コントローラーまたはアクション メソッドの状態コード ページを無効にするには、[[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 属性を使用します。

Razor Pages ハンドラー メソッドまたは MVC コントローラーの特定の要求に対して状態コード ページを無効にするには、<xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> を使用します。

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>例外処理コード

例外処理ページのコードは例外をスローすることがあります。 実稼働のエラー ページは純粋に静的なコンテンツで構成することをお勧めします。

### <a name="response-headers"></a>応答ヘッダー

応答のヘッダーが送信された後は、次のようになります。

* アプリで応答の状態コードを変更できません。
* すべての例外ページやハンドラーを実行できません。 応答は完了している必要があります。あるいは、接続が中止となっている必要があります。

## <a name="server-exception-handling"></a>サーバー例外処理

アプリ内の例外処理ロジックに加えて、[HTTP サーバーの実装](xref:fundamentals/servers/index)でも一部の例外を処理できます。 応答ヘッダーの送信前にサーバーで例外がキャッチされると、サーバーによって "*500 - 内部サーバー エラーです*" 応答が応答本文なしで送信されます。 応答ヘッダーの送信後にサーバーで例外がキャッチされた場合、サーバーは接続を閉じます。 アプリで処理されない要求はサーバーで処理されます。 サーバーが要求を処理しているときに発生した例外は、すべてサーバーの例外処理によって処理されます。 この動作は、アプリのカスタム エラー ページ、例外処理ミドルウェア、およびフィルターから影響を受けません。

## <a name="startup-exception-handling"></a>起動時の例外処理

アプリの起動中に起こる例外はホスティング層だけが処理できます。 [起動時のエラーをキャプチャ](xref:fundamentals/host/web-host#capture-startup-errors)したり、[詳細なエラーをキャプチャ](xref:fundamentals/host/web-host#detailed-errors)したりするように、ホストを構成することができます。

ホスティング レイヤーでは、ホスト アドレス/ポート バインド後にエラーが発生した場合にのみ、キャプチャされた起動時エラーに対するエラー ページを表示できます。 バインドが失敗した場合は、次のようになります。

* ホスティング レイヤーにより重大な例外がログに記録されます。
* dotnet プロセスがクラッシュします。
* HTTP サーバーが [Kestrel](xref:fundamentals/servers/kestrel) のときは、エラー ページは表示されません。

[IIS](/iis) (または Azure App Service) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上で実行している場合、プロセスを開始できなければ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)から "*502.5 - 処理エラー*" が返されます。 詳細については、<xref:test/troubleshoot-azure-iis> を参照してください。

## <a name="database-error-page"></a>データベース エラー ページ

データベース エラー ページ ミドルウェアでは、Entity Framework の移行を使って解決できるデータベース関連の例外がキャプチャされます。 これらの例外が発生すると、問題が解決する可能性のあるアクションの詳細を含む HTML 応答が生成されます。 このページは、開発環境でのみ有効にする必要があります。 ページを有効にするには、コードを `Startup.Configure` に追加します。

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>例外フィルター

MVC アプリでは、例外フィルターをグローバルに、またはコントローラーやアクションの単位で構成できます。 Razor Pages アプリでは、グローバルに、またはページ モデルの単位で構成できます。 このようなフィルターはコントローラー アクションや別のフィルターの実行中に発生する未処理の例外を処理します。 詳細については、<xref:mvc/controllers/filters#exception-filters> を参照してください。

> [!TIP]
> 例外フィルターは、MVC アクション内で発生した例外をトラップする場合は便利ですが、例外処理ミドルウェアほど柔軟ではありません。 ミドルウェアの使用をお勧めします。 選択された MVC アクションに応じて異なる方法でエラー処理を実行する必要がある場合にのみ、フィルターを使用します。

## <a name="model-state-errors"></a>モデル状態エラー

モデル状態エラーを処理する方法については、[モデル バインド](xref:mvc/models/model-binding)および[モデルの検証](xref:mvc/models/validation)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
