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
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="73de5-103">ASP.NET Core Web API のエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="73de5-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="73de5-104">この記事では、ASP.NET Core Web API を使用したエラーの処理方法とエラー処理のカスタマイズ方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="73de5-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="73de5-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="73de5-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="73de5-106">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="73de5-106">Developer Exception Page</span></span>

<span data-ttu-id="73de5-107">[開発者例外ページ](xref:fundamentals/error-handling)は、サーバー エラーの詳しいスタック トレースを取得するために役立つツールです。</span><span class="sxs-lookup"><span data-stu-id="73de5-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span>

<span data-ttu-id="73de5-108">クライアントで HTML 形式の出力が受け入れられない場合、開発者例外ページにはテキスト形式の応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="73de5-108">The Developer Exception Page displays a plain-text response if the client doesn't accept HTML-formatted output.</span></span> <span data-ttu-id="73de5-109">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="73de5-109">For example:</span></span>

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> <span data-ttu-id="73de5-110">**アプリを開発環境で実行するときにのみ**、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="73de5-110">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="73de5-111">アプリを実稼働環境で実行するときは、詳細な例外情報を公開しません。</span><span class="sxs-lookup"><span data-stu-id="73de5-111">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="73de5-112">環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73de5-112">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="73de5-113">例外ハンドラー</span><span class="sxs-lookup"><span data-stu-id="73de5-113">Exception handler</span></span>

<span data-ttu-id="73de5-114">開発以外の環境では、[例外処理ミドルウェア](xref:fundamentals/error-handling)を使用してエラー ペイロードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="73de5-114">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="73de5-115">`Startup.Configure` で、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> を呼び出してミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="73de5-115">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="73de5-116">`/error` ルートに応答するようにコントローラー アクションを構成します。</span><span class="sxs-lookup"><span data-stu-id="73de5-116">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="73de5-117">前の `Error` アクションは、[RFC7807](https://tools.ietf.org/html/rfc7807) 準拠のペイロードをクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="73de5-117">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="73de5-118">ローカル開発環境では、例外処理ミドルウェアによって、さらに詳しいコンテンツ ネゴシエーション結果も提供されます。</span><span class="sxs-lookup"><span data-stu-id="73de5-118">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="73de5-119">次の手順に従い、開発環境と運用環境で一貫性のあるペイロード形式を生成します。</span><span class="sxs-lookup"><span data-stu-id="73de5-119">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="73de5-120">`Startup.Configure` で、環境固有の例外処理ミドルウェア インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="73de5-120">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

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

    <span data-ttu-id="73de5-121">前のコードでは、ミドルウェアは次のように登録されます。</span><span class="sxs-lookup"><span data-stu-id="73de5-121">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="73de5-122">開発環境では `/error-local-development` のルート。</span><span class="sxs-lookup"><span data-stu-id="73de5-122">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="73de5-123">開発以外の環境では `/error` のルート。</span><span class="sxs-lookup"><span data-stu-id="73de5-123">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="73de5-124">属性ルーティングをコントローラー アクションに適用します。</span><span class="sxs-lookup"><span data-stu-id="73de5-124">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="73de5-125">例外を使用して応答を変更する</span><span class="sxs-lookup"><span data-stu-id="73de5-125">Use exceptions to modify the response</span></span>

<span data-ttu-id="73de5-126">応答の内容は、コントローラーの外部で変更できます。</span><span class="sxs-lookup"><span data-stu-id="73de5-126">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="73de5-127">ASP.NET 4.x Web API の場合、これを行う方法の 1 つが <xref:System.Web.Http.HttpResponseException> 型の使用でした。</span><span class="sxs-lookup"><span data-stu-id="73de5-127">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="73de5-128">ASP.NET Core には同等の型が含まれていません。</span><span class="sxs-lookup"><span data-stu-id="73de5-128">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="73de5-129">`HttpResponseException` のサポートは以下の手順で追加することができます。</span><span class="sxs-lookup"><span data-stu-id="73de5-129">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="73de5-130">`HttpResponseException` という名前の一般的な例外の種類を作成します。</span><span class="sxs-lookup"><span data-stu-id="73de5-130">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="73de5-131">`HttpResponseExceptionFilter` という名前のアクション フィルターを作成します。</span><span class="sxs-lookup"><span data-stu-id="73de5-131">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="73de5-132">`Startup.ConfigureServices` に、フィルター コレクションに対するアクション フィルターを追加します。</span><span class="sxs-lookup"><span data-stu-id="73de5-132">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="73de5-133">検証失敗のエラー応答</span><span class="sxs-lookup"><span data-stu-id="73de5-133">Validation failure error response</span></span>

<span data-ttu-id="73de5-134">Web API コントローラーでは、モデルの検証が失敗すると、MVC が <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> という応答の種類で応答します。</span><span class="sxs-lookup"><span data-stu-id="73de5-134">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="73de5-135">MVC は <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> の結果を使用して、検証失敗に対するエラー応答を作成します。</span><span class="sxs-lookup"><span data-stu-id="73de5-135">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="73de5-136">次の例では、`Startup.ConfigureServices` で、ファクトリを使用して応答の既定の種類を <xref:Microsoft.AspNetCore.Mvc.SerializableError> に変更します。</span><span class="sxs-lookup"><span data-stu-id="73de5-136">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="73de5-137">クライアントのエラー応答</span><span class="sxs-lookup"><span data-stu-id="73de5-137">Client error response</span></span>

<span data-ttu-id="73de5-138">"*エラー結果*" は、HTTP 状態コードが 400 以上の結果として定義されます。</span><span class="sxs-lookup"><span data-stu-id="73de5-138">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="73de5-139">Web API コントローラーの場合、MVC によってエラー結果が <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> を含む結果に変換されます。</span><span class="sxs-lookup"><span data-stu-id="73de5-139">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73de5-140">エラー応答は、次のいずれかの方法で構成できます。</span><span class="sxs-lookup"><span data-stu-id="73de5-140">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="73de5-141">ProblemDetailsFactory の実装</span><span class="sxs-lookup"><span data-stu-id="73de5-141">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="73de5-142">ApiBehaviorOptions.ClientErrorMapping の使用</span><span class="sxs-lookup"><span data-stu-id="73de5-142">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="73de5-143">ProblemDetailsFactory の実装</span><span class="sxs-lookup"><span data-stu-id="73de5-143">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="73de5-144">MVC は、`Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` を使用して、<xref:Microsoft.AspNetCore.Mvc.ProblemDetails> と <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> のすべてのインスタンスを生成します。</span><span class="sxs-lookup"><span data-stu-id="73de5-144">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="73de5-145">これには、クライアント エラー応答および検証失敗エラー応答と、`Microsoft.AspNetCore.Mvc.ControllerBase.Problem` および <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> ヘルパー メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="73de5-145">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="73de5-146">問題の詳しい応答をカスタマイズするには、`Startup.ConfigureServices`で `ProblemDetailsFactory` のカスタム実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="73de5-146">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="73de5-147">エラー応答は、「[ApiBehaviorOptions.ClientErrorMapping の使用](#use-apibehavioroptionsclienterrormapping)」セクションの説明に従って構成できます。</span><span class="sxs-lookup"><span data-stu-id="73de5-147">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="73de5-148">ApiBehaviorOptions.ClientErrorMapping の使用</span><span class="sxs-lookup"><span data-stu-id="73de5-148">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="73de5-149">`ProblemDetails` の応答の内容を構成するには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="73de5-149">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="73de5-150">たとえば、`Startup.ConfigureServices`の次のコードにより、404 応答の `type` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="73de5-150">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
