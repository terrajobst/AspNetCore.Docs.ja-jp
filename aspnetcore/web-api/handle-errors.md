---
title: ASP.NET Core Web API のエラーを処理する
author: pranavkm
description: ASP.NET Core Web API を使用したエラー処理について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/25/2019
uid: web-api/handle-errors
ms.openlocfilehash: 9c5dd2f89e7351f386d1f0633c831952dc58e568
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278729"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a>ASP.NET Core Web API のエラーを処理する

この記事では、ASP.NET Core Web API を使用したエラーの処理方法とエラー処理のカスタマイズ方法について説明します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="developer-exception-page"></a>開発者例外ページ

[開発者例外ページ](xref:fundamentals/error-handling)は、サーバー エラーの詳しいスタック トレースを取得するために役立つツールです。

クライアントで HTML 形式の出力が受け入れられない場合、開発者例外ページにはテキスト形式の応答が表示されます。 次に例を示します。

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> **アプリを開発環境で実行するときにのみ**、開発者例外ページを有効にします。 アプリを実稼働環境で実行するときは、詳細な例外情報を公開しません。 環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。

## <a name="exception-handler"></a>例外ハンドラー

開発以外の環境では、[例外処理ミドルウェア](xref:fundamentals/error-handling)を使用してエラー ペイロードを生成できます。

1. `Startup.Configure` で、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> を呼び出してミドルウェアを使用します。

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. `/error` ルートに応答するようにコントローラー アクションを構成します。

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

前の `Error` アクションは、[RFC7807](https://tools.ietf.org/html/rfc7807) 準拠のペイロードをクライアントに送信します。

ローカル開発環境では、例外処理ミドルウェアによって、さらに詳しいコンテンツ ネゴシエーション結果も提供されます。 次の手順に従い、開発環境と運用環境で一貫性のあるペイロード形式を生成します。

1. `Startup.Configure` で、環境固有の例外処理ミドルウェア インスタンスを登録します。

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    前のコードでは、ミドルウェアは次のように登録されます。

    * 開発環境では `/error-local-development` のルート。
    * 開発以外の環境では `/error` のルート。
    
1. 属性ルーティングをコントローラー アクションに適用します。

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a>例外を使用して応答を変更する

応答の内容は、コントローラーの外部で変更できます。 ASP.NET 4.x Web API の場合、これを行う方法の 1 つが <xref:System.Web.Http.HttpResponseException> 型の使用でした。 ASP.NET Core には同等の型が含まれていません。 `HttpResponseException` のサポートは以下の手順で追加することができます。

1. `HttpResponseException` という名前の一般的な例外の種類を作成します。

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. `HttpResponseExceptionFilter` という名前のアクション フィルターを作成します。

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. `Startup.ConfigureServices` に、フィルター コレクションに対するアクション フィルターを追加します。

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>検証失敗のエラー応答

Web API コントローラーでは、モデルの検証が失敗すると、MVC が <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> という応答の種類で応答します。 MVC は <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> の結果を使用して、検証失敗に対するエラー応答を作成します。 次の例では、`Startup.ConfigureServices` で、ファクトリを使用して応答の既定の種類を <xref:Microsoft.AspNetCore.Mvc.SerializableError> に変更します。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>クライアントのエラー応答

"*エラー結果*" は、HTTP 状態コードが 400 以上の結果として定義されます。 Web API コントローラーの場合、MVC によってエラー結果が <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> を含む結果に変換されます。

::: moniker range=">= aspnetcore-3.0"

エラー応答は、次のいずれかの方法で構成できます。

1. [ProblemDetailsFactory の実装](#implement-problemdetailsfactory)
1. [ApiBehaviorOptions.ClientErrorMapping の使用](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>ProblemDetailsFactory の実装

MVC は、`Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` を使用して、<xref:Microsoft.AspNetCore.Mvc.ProblemDetails> と <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> のすべてのインスタンスを生成します。 これには、クライアント エラー応答および検証失敗エラー応答と、`Microsoft.AspNetCore.Mvc.ControllerBase.Problem` および <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> ヘルパー メソッドが含まれます。

問題の詳しい応答をカスタマイズするには、`Startup.ConfigureServices`で `ProblemDetailsFactory` のカスタム実装を登録します。

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

エラー応答は、「[ApiBehaviorOptions.ClientErrorMapping の使用](#use-apibehavioroptionsclienterrormapping)」セクションの説明に従って構成できます。

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>ApiBehaviorOptions.ClientErrorMapping の使用

`ProblemDetails` の応答の内容を構成するには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> プロパティを使用します。 たとえば、`Startup.ConfigureServices`の次のコードにより、404 応答の `type` プロパティが更新されます。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
