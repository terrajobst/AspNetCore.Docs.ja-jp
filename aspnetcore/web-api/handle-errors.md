---
title: ASP.NET Core Web API のエラーを処理する
author: pranavkm
description: ASP.NET Core Web API を使用したエラー処理について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/10/2019
uid: web-api/handle-errors
ms.openlocfilehash: e445fb3d50973643c9cea60395d1ed02c2f5f675
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652394"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="f89c3-103">ASP.NET Core Web API のエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="f89c3-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="f89c3-104">この記事では、ASP.NET Core Web API を使用したエラーの処理方法とエラー処理のカスタマイズ方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="f89c3-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f89c3-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f89c3-106">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="f89c3-106">Developer Exception Page</span></span>

<span data-ttu-id="f89c3-107">[開発者例外ページ](xref:fundamentals/error-handling)は、サーバー エラーの詳しいスタック トレースを取得するために役立つツールです。</span><span class="sxs-lookup"><span data-stu-id="f89c3-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="f89c3-108"><xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> を使用して、HTTP パイプラインから同期および非同期例外をキャプチャし、エラー応答を生成します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="f89c3-109">これを説明するために、次のコントローラー アクションがあるとします。</span><span class="sxs-lookup"><span data-stu-id="f89c3-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="f89c3-110">次の `curl` コマンドを実行して、上記のアクションをテストします。</span><span class="sxs-lookup"><span data-stu-id="f89c3-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f89c3-111">ASP.NET Core 3.0 以降では、クライアントにより HTML 形式の出力が要求されない場合、開発者例外ページにはテキスト形式の応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="f89c3-112">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="f89c3-113">代わりに HTML 形式の応答を表示するには、`Accept` HTTP 要求ヘッダーを `text/html` のメディアの種類に設定します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="f89c3-114">例 :</span><span class="sxs-lookup"><span data-stu-id="f89c3-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="f89c3-115">HTTP 応答からの次の抜粋を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f89c3-116">ASP.NET Core 2.2 以前では、開発者例外ページには HTML 形式の応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="f89c3-117">たとえば、HTTP 応答からの次の抜粋を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f89c3-118">HTML 形式の応答は、Postman などのツールを使用してテストするときに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="f89c3-119">次の画面キャプチャは、Postman のテキスト形式と HTML 形式の両方の応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="f89c3-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Postman での開発者例外ページのテスト](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="f89c3-121">**アプリを開発環境で実行するときにのみ**、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="f89c3-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="f89c3-122">アプリを実稼働環境で実行するときは、詳細な例外情報を公開しません。</span><span class="sxs-lookup"><span data-stu-id="f89c3-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="f89c3-123">環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f89c3-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="f89c3-124">例外ハンドラー</span><span class="sxs-lookup"><span data-stu-id="f89c3-124">Exception handler</span></span>

<span data-ttu-id="f89c3-125">開発以外の環境では、[例外処理ミドルウェア](xref:fundamentals/error-handling)を使用してエラー ペイロードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="f89c3-126">`Startup.Configure` で、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> を呼び出してミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="f89c3-127">`/error` ルートに応答するようにコントローラー アクションを構成します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="f89c3-128">前の `Error` アクションは、[RFC 7807](https://tools.ietf.org/html/rfc7807) 準拠のペイロードをクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-128">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="f89c3-129">ローカル開発環境では、例外処理ミドルウェアによって、さらに詳しいコンテンツ ネゴシエーション結果も提供されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="f89c3-130">次の手順に従い、開発環境と運用環境で一貫性のあるペイロード形式を生成します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="f89c3-131">`Startup.Configure` で、環境固有の例外処理ミドルウェア インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

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

    <span data-ttu-id="f89c3-132">前のコードでは、ミドルウェアは次のように登録されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="f89c3-133">開発環境では `/error-local-development` のルート。</span><span class="sxs-lookup"><span data-stu-id="f89c3-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="f89c3-134">開発以外の環境では `/error` のルート。</span><span class="sxs-lookup"><span data-stu-id="f89c3-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="f89c3-135">属性ルーティングをコントローラー アクションに適用します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="f89c3-136">例外を使用して応答を変更する</span><span class="sxs-lookup"><span data-stu-id="f89c3-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="f89c3-137">応答の内容は、コントローラーの外部で変更できます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="f89c3-138">ASP.NET 4.x Web API の場合、これを行う方法の 1 つが <xref:System.Web.Http.HttpResponseException> 型の使用でした。</span><span class="sxs-lookup"><span data-stu-id="f89c3-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="f89c3-139">ASP.NET Core には同等の型が含まれていません。</span><span class="sxs-lookup"><span data-stu-id="f89c3-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="f89c3-140">`HttpResponseException` のサポートは以下の手順で追加することができます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="f89c3-141">`HttpResponseException` という名前の一般的な例外の種類を作成します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="f89c3-142">`HttpResponseExceptionFilter` という名前のアクション フィルターを作成します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="f89c3-143">`Startup.ConfigureServices` に、フィルター コレクションに対するアクション フィルターを追加します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="f89c3-144">検証失敗のエラー応答</span><span class="sxs-lookup"><span data-stu-id="f89c3-144">Validation failure error response</span></span>

<span data-ttu-id="f89c3-145">Web API コントローラーでは、モデルの検証が失敗すると、MVC が <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> という応答の種類で応答します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="f89c3-146">MVC は <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> の結果を使用して、検証失敗に対するエラー応答を作成します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="f89c3-147">次の例では、<xref:Microsoft.AspNetCore.Mvc.SerializableError> で、ファクトリを使用して応答の既定の種類を `Startup.ConfigureServices` に変更します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="f89c3-148">クライアントのエラー応答</span><span class="sxs-lookup"><span data-stu-id="f89c3-148">Client error response</span></span>

<span data-ttu-id="f89c3-149">"*エラー結果*" は、HTTP 状態コードが 400 以上の結果として定義されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="f89c3-150">Web API コントローラーの場合、MVC によってエラー結果が <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> を含む結果に変換されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="f89c3-151">ASP.NET Core 2.1 では、RFC 7807 にほぼ準拠した、問題の詳しい応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-151">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="f89c3-152">100% のコンプライアンスが重要な場合は、プロジェクトを ASP.NET Core 2.2 以降にアップグレードしてください。</span><span class="sxs-lookup"><span data-stu-id="f89c3-152">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f89c3-153">エラー応答は、次のいずれかの方法で構成できます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-153">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="f89c3-154">ProblemDetailsFactory の実装</span><span class="sxs-lookup"><span data-stu-id="f89c3-154">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="f89c3-155">ApiBehaviorOptions.ClientErrorMapping の使用</span><span class="sxs-lookup"><span data-stu-id="f89c3-155">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="f89c3-156">ProblemDetailsFactory の実装</span><span class="sxs-lookup"><span data-stu-id="f89c3-156">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="f89c3-157">MVC は、`Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` を使用して、<xref:Microsoft.AspNetCore.Mvc.ProblemDetails> と <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> のすべてのインスタンスを生成します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-157">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="f89c3-158">これには、クライアント エラー応答および検証失敗エラー応答と、`Microsoft.AspNetCore.Mvc.ControllerBase.Problem` および <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> ヘルパー メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-158">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="f89c3-159">問題の詳しい応答をカスタマイズするには、`ProblemDetailsFactory`で `Startup.ConfigureServices` のカスタム実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-159">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f89c3-160">エラー応答は、「[ApiBehaviorOptions.ClientErrorMapping の使用](#use-apibehavioroptionsclienterrormapping)」セクションの説明に従って構成できます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-160">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="f89c3-161">ApiBehaviorOptions.ClientErrorMapping の使用</span><span class="sxs-lookup"><span data-stu-id="f89c3-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="f89c3-162"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> の応答の内容を構成するには、`ProblemDetails` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="f89c3-162">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="f89c3-163">たとえば、`Startup.ConfigureServices`の次のコードにより、404 応答の `type` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="f89c3-163">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
