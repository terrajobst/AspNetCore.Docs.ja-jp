---
title: ASP.NET Core のエラーを処理する
author: rick-anderson
description: ASP.NET Core アプリでエラーを処理する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: c20d8757eef80fdbb73b1b7a9933a3c0be9bb8ed
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358978"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="0bb0b-103">ASP.NET Core のエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="0bb0b-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="0bb0b-104">作成者: [Tom Dykstra](https://github.com/tdykstra/)、[Luke Latham](https://github.com/guardrex)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0bb0b-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0bb0b-105">この記事では、ASP.NET Core Web アプリでエラーを処理するための一般的な手法について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="0bb0b-106">Web API については、<xref:web-api/handle-errors> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="0bb0b-107">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="0bb0b-108">([ダウンロード方法](xref:index#how-to-download-a-sample)。)この記事には、サンプル アプリでプリプロセッサ ディレクティブ (`#if`、`#endif`、`#define`) を設定して、異なるシナリオを有効にする方法についての説明が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="0bb0b-109">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="0bb0b-109">Developer Exception Page</span></span>

<span data-ttu-id="0bb0b-110">"*開発者例外ページ*" には、要求の例外に関する詳細な情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="0bb0b-111">そのページは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="0bb0b-112">アプリを開発[環境](xref:fundamentals/environments)で実行するときにページを有効にするには、`Startup.Configure` メソッドにコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="0bb0b-113">例外をキャッチしたいミドルウェアの前に、<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> の呼び出しを配置します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="0bb0b-114">**アプリを開発環境で実行するときにのみ**、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="0bb0b-115">アプリを実稼働環境で実行するときは、詳細な例外情報を公開しません。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="0bb0b-116">環境の構成について詳しくは、「<xref:fundamentals/environments>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0bb0b-117">このページには、例外と要求に関する次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="0bb0b-118">スタック トレース</span><span class="sxs-lookup"><span data-stu-id="0bb0b-118">Stack trace</span></span>
* <span data-ttu-id="0bb0b-119">クエリ文字列のパラメーター (ある場合)</span><span class="sxs-lookup"><span data-stu-id="0bb0b-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="0bb0b-120">Cookie (ある場合)</span><span class="sxs-lookup"><span data-stu-id="0bb0b-120">Cookies (if any)</span></span>
* <span data-ttu-id="0bb0b-121">ヘッダー</span><span class="sxs-lookup"><span data-stu-id="0bb0b-121">Headers</span></span>

<span data-ttu-id="0bb0b-122">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で開発者例外ページを表示するには、`DevEnvironment` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-122">To see the Developer Exception Page in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="0bb0b-123">例外ハンドラー ページ</span><span class="sxs-lookup"><span data-stu-id="0bb0b-123">Exception handler page</span></span>

<span data-ttu-id="0bb0b-124">運用環境用のカスタム エラー処理ページを構成するには、例外処理ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="0bb0b-125">ミドルウェアでは次を行います。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-125">The middleware:</span></span>

* <span data-ttu-id="0bb0b-126">例外をキャッチしてログに記録します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="0bb0b-127">ページ用の、またはコントローラーが指定した別のパイプラインで、要求を再実行します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="0bb0b-128">応答が始まっていた場合、要求は再実行されません。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="0bb0b-129">次の例では、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> により非開発環境に例外処理ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="0bb0b-130">Razor Pages アプリのテンプレートには、エラー ページ ( *.cshtml*) と <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> クラス (`ErrorModel`) が *Pages* フォルダー内に用意されています。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="0bb0b-131">MVC アプリの場合、プロジェクト テンプレートにはエラー アクション メソッドとエラー ビューが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="0bb0b-132">アクション メソッドを次に示します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="0bb0b-133">`HttpGet` などの HTTP メソッド属性を使ってエラー ハンドラー アクション メソッドをマークしないでください。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="0bb0b-134">明示的な動詞を使用すると、要求がメソッドに届かないことがあります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="0bb0b-135">認証されていないユーザーがエラー ビューを受信できるように、メソッドへの匿名アクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="0bb0b-136">例外にアクセスする</span><span class="sxs-lookup"><span data-stu-id="0bb0b-136">Access the exception</span></span>

<span data-ttu-id="0bb0b-137">エラー ハンドラー コントローラーまたはページ内で例外や元の要求パスにアクセスするには、<xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> を使います。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="0bb0b-138">機密性の高いエラー情報をクライアントに提供**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="0bb0b-139">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="0bb0b-140">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で例外処理ページを表示するには、`ProdEnvironment` および `ErrorHandlerPage` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-140">To see the exception handling page in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="0bb0b-141">例外ハンドラー ラムダ</span><span class="sxs-lookup"><span data-stu-id="0bb0b-141">Exception handler lambda</span></span>

<span data-ttu-id="0bb0b-142">[カスタム例外ハンドラー ページ](#exception-handler-page)の代わりになるのは、<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> にラムダを提供することです。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="0bb0b-143">ラムダを使うと、応答を返す前にエラーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="0bb0b-144">例外処理にラムダを使う例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="0bb0b-145">上記のコードでは `await context.Response.WriteAsync(new string(' ', 512));` が追加されるため、Internet Explorer ブラウザーには IE のエラー メッセージではなく、このエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="0bb0b-146">詳細については、次を参照してください。[この GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/16144)します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-146">For more information, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="0bb0b-147"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> または <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> からの機密性の高いエラー情報をクライアントに提供**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="0bb0b-148">エラーの提供はセキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="0bb0b-149">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)で例外処理ラムダの結果を表示するには、`ProdEnvironment` および `ErrorHandlerLambda` プリプロセッサ ディレクティブを使用して、ホーム ページで **[Trigger an exception]\(例外をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-149">To see the result of the exception handling lambda in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="0bb0b-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0bb0b-150">UseStatusCodePages</span></span>

<span data-ttu-id="0bb0b-151">ASP.NET Core アプリでは、既定で、"*404 - 見つかりません*" などの HTTP 状態コードの状態コード ページが表示されません。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="0bb0b-152">アプリでは、状態コードと空の応答本文が返されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="0bb0b-153">状態コード ページを提供するには、状態コード ページ ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="0bb0b-154">そのミドルウェアは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内にある [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="0bb0b-155">一般的なエラー状態コード用に既定のテキスト専用ハンドラーを有効にするには、`Startup.Configure` メソッドで <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="0bb0b-156">要求処理ミドルウェア (たとえば、静的ファイル ミドルウェアや MVC ミドルウェア) の前に `UseStatusCodePages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="0bb0b-157">既定のハンドラーによって表示されるテキストの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="0bb0b-158">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)でさまざまな状態コード ページ形式のいずれかを表示するには、`StatusCodePages` で始まるプリプロセッサ ディレクティブのいずれかを使用して、ホーム ページの **[Trigger a 404]\(404 をトリガーする\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-158">To see one of the various status code page formats in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="0bb0b-159">書式設定文字列での UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0bb0b-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="0bb0b-160">応答の内容の種類とテキストをカスタマイズするには、内容の種類と書式文字列を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="0bb0b-161">ラムダでの UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0bb0b-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="0bb0b-162">カスタム エラー処理と応答書き込みコードを指定するには、ラムダ式を受け取る <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> のオーバーロードを使います。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="0bb0b-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="0bb0b-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="0bb0b-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="0bb0b-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="0bb0b-165">クライアントに *302 - Found* 状態コードを送信します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="0bb0b-166">URL テンプレートで指定された場所にクライアントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="0bb0b-167">次の例で示すように、URL テンプレートには状態コード用の `{0}` プレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="0bb0b-168">URL テンプレートがチルダ (~) で始まっている場合、チルダはアプリの `PathBase` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="0bb0b-169">アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="0bb0b-170">Razor Pages の例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="0bb0b-171">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="0bb0b-172">クライアントを別のエンドポイントにリダイレクトする必要がある場合 (通常は、別のアプリがエラーを処理する場合)。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="0bb0b-173">Web アプリの場合は、クライアントのブラウザーのアドレス バーにリダイレクトされたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="0bb0b-174">元の状態コードを保持し、初回のリダイレクト応答で返してはいけない場合。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="0bb0b-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="0bb0b-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="0bb0b-176"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> 拡張メソッド:</span><span class="sxs-lookup"><span data-stu-id="0bb0b-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="0bb0b-177">元の状態コードをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="0bb0b-178">代替パスを使用して要求パイプラインを再実行することで、応答本文を生成します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="0bb0b-179">アプリ内でエンドポイントを指し示す場合は、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="0bb0b-180">Razor Pages の例については、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)にある *Pages/StatusCode.cshtml* をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="0bb0b-181">この方法は、次のようなアプリで一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="0bb0b-182">別のエンドポイントにリダイレクトすることなく要求を処理する。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="0bb0b-183">Web アプリの場合は、クライアントのブラウザーのアドレス バーに、初めに要求されていたエンドポイントが反映されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="0bb0b-184">元の状態コードを保持し、応答で返す。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="0bb0b-185">URL とクエリ文字列のテンプレートには、状態コード用のプレースホルダー (`{0}`) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="0bb0b-186">URL テンプレートの先頭には、スラッシュ (`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="0bb0b-187">パスでプレースホルダーを使う場合は、エンドポイント (ページまたはコントローラー) でパスのセグメントを処理できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="0bb0b-188">たとえば、エラー用の Razor ページでは、`@page` ディレクティブの付いた省略可能なパスのセグメント値を受け入れる必要があります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="0bb0b-189">次の例で示すように、エラーを処理するエンドポイントでは、エラーを生成した元の URL を取得できます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="0bb0b-190">状態コード ページを無効にする</span><span class="sxs-lookup"><span data-stu-id="0bb0b-190">Disable status code pages</span></span>

<span data-ttu-id="0bb0b-191">MVC コントローラーまたはアクション メソッドの状態コード ページを無効にするには、[`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="0bb0b-192">Razor Pages ハンドラー メソッドまたは MVC コントローラーの特定の要求に対して状態コード ページを無効にするには、<xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> を使用します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="0bb0b-193">例外処理コード</span><span class="sxs-lookup"><span data-stu-id="0bb0b-193">Exception-handling code</span></span>

<span data-ttu-id="0bb0b-194">例外処理ページのコードは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="0bb0b-195">実稼働のエラー ページは純粋に静的なコンテンツで構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="0bb0b-196">応答ヘッダー</span><span class="sxs-lookup"><span data-stu-id="0bb0b-196">Response headers</span></span>

<span data-ttu-id="0bb0b-197">応答のヘッダーが送信された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="0bb0b-198">アプリで応答の状態コードを変更できません。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="0bb0b-199">すべての例外ページやハンドラーを実行できません。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="0bb0b-200">応答は完了している必要があります。あるいは、接続が中止となっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="0bb0b-201">サーバー例外処理</span><span class="sxs-lookup"><span data-stu-id="0bb0b-201">Server exception handling</span></span>

<span data-ttu-id="0bb0b-202">アプリ内の例外処理ロジックに加えて、[HTTP サーバーの実装](xref:fundamentals/servers/index)でも一部の例外を処理できます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="0bb0b-203">応答ヘッダーの送信前にサーバーで例外がキャッチされると、サーバーによって "*500 - 内部サーバー エラーです*" 応答が応答本文なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="0bb0b-204">応答ヘッダーの送信後にサーバーで例外がキャッチされた場合、サーバーは接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="0bb0b-205">アプリで処理されない要求はサーバーで処理されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="0bb0b-206">サーバーが要求を処理しているときに発生した例外は、すべてサーバーの例外処理によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="0bb0b-207">この動作は、アプリのカスタム エラー ページ、例外処理ミドルウェア、およびフィルターから影響を受けません。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="0bb0b-208">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="0bb0b-208">Startup exception handling</span></span>

<span data-ttu-id="0bb0b-209">アプリの起動中に起こる例外はホスティング層だけが処理できます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="0bb0b-210">[起動時のエラーをキャプチャ](xref:fundamentals/host/web-host#capture-startup-errors)したり、[詳細なエラーをキャプチャ](xref:fundamentals/host/web-host#detailed-errors)したりするように、ホストを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="0bb0b-211">ホスティング レイヤーでは、ホスト アドレス/ポート バインド後にエラーが発生した場合にのみ、キャプチャされた起動時エラーに対するエラー ページを表示できます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="0bb0b-212">バインドが失敗した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-212">If binding fails:</span></span>

* <span data-ttu-id="0bb0b-213">ホスティング レイヤーにより重大な例外がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="0bb0b-214">dotnet プロセスがクラッシュします。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="0bb0b-215">HTTP サーバーが [Kestrel](xref:fundamentals/servers/kestrel) のときは、エラー ページは表示されません。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="0bb0b-216">[IIS](/iis) (または Azure App Service) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上で実行している場合、プロセスを開始できなければ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)から "*502.5 - 処理エラー*" が返されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="0bb0b-217">詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="0bb0b-218">データベース エラー ページ</span><span class="sxs-lookup"><span data-stu-id="0bb0b-218">Database error page</span></span>

<span data-ttu-id="0bb0b-219">データベース エラー ページ ミドルウェアでは、Entity Framework の移行を使って解決できるデータベース関連の例外がキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="0bb0b-220">これらの例外が発生すると、問題が解決する可能性のあるアクションの詳細を含む HTML 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="0bb0b-221">このページは、開発環境でのみ有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="0bb0b-222">ページを有効にするには、コードを `Startup.Configure` に追加します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="0bb0b-223">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="0bb0b-223">Exception filters</span></span>

<span data-ttu-id="0bb0b-224">MVC アプリでは、例外フィルターをグローバルに、またはコントローラーやアクションの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="0bb0b-225">Razor Pages アプリでは、グローバルに、またはページ モデルの単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="0bb0b-226">このようなフィルターはコントローラー アクションや別のフィルターの実行中に発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="0bb0b-227">詳細については、「<xref:mvc/controllers/filters#exception-filters>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="0bb0b-228">例外フィルターは、MVC アクション内で発生した例外をトラップする場合は便利ですが、例外処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="0bb0b-229">ミドルウェアの使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-229">We recommend using the middleware.</span></span> <span data-ttu-id="0bb0b-230">選択された MVC アクションに応じて異なる方法でエラー処理を実行する必要がある場合にのみ、フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="0bb0b-231">モデル状態エラー</span><span class="sxs-lookup"><span data-stu-id="0bb0b-231">Model state errors</span></span>

<span data-ttu-id="0bb0b-232">モデル状態エラーを処理する方法については、[モデル バインド](xref:mvc/models/model-binding)および[モデルの検証](xref:mvc/models/validation)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0bb0b-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0bb0b-233">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0bb0b-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
