---
title: ASP.NET Core のミドルウェア
author: rick-anderson
description: ASP.NET Core のミドルウェアと要求パイプラインについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 6bf8ed823386ca4e1cf78982f7fba41fba429db8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751084"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="d63f3-103">ASP.NET Core のミドルウェア</span><span class="sxs-lookup"><span data-stu-id="d63f3-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d63f3-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d63f3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d63f3-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="d63f3-106">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-106">Each component:</span></span>

* <span data-ttu-id="d63f3-107">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="d63f3-108">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="d63f3-109">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="d63f3-110">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="d63f3-111">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="d63f3-112">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="d63f3-113">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="d63f3-114">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="d63f3-115">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="d63f3-116"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="d63f3-117">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="d63f3-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="d63f3-118">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="d63f3-119">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="d63f3-120">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-120">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="d63f3-124">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="d63f3-125">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="d63f3-126">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="d63f3-127">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="d63f3-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="d63f3-128">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="d63f3-129">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="d63f3-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="d63f3-130">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="d63f3-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="d63f3-131">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="d63f3-132">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="d63f3-133">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="d63f3-134">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="d63f3-135">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="d63f3-136">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="d63f3-137">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="d63f3-138">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="d63f3-139">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="d63f3-140">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-140">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="d63f3-141">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="d63f3-142">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-142">May cause a protocol violation.</span></span> <span data-ttu-id="d63f3-143">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="d63f3-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="d63f3-144">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-144">May corrupt the body format.</span></span> <span data-ttu-id="d63f3-145">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="d63f3-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="d63f3-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="d63f3-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートでは、`next` パラメーターは受け取られません。</span><span class="sxs-lookup"><span data-stu-id="d63f3-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="d63f3-148">最初の `Run` デリゲートが常に終点となり、パイプラインが終了されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="d63f3-149">`Run` は規則です。</span><span class="sxs-lookup"><span data-stu-id="d63f3-149">`Run` is a convention.</span></span> <span data-ttu-id="d63f3-150">一部のミドルウェア コンポーネントでは、パイプラインの最後に実行される `Run[Middleware]` メソッドが公開されることがあります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d63f3-151">前の例では、`Run` デリゲートによって応答に `"Hello from 2nd delegate."` が書き込まれ、その後、パイプラインが終了となります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="d63f3-152">別の `Use` または `Run` デリゲートが `Run` デリゲートの後に追加される場合、そのデリゲートは呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="d63f3-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="d63f3-153">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="d63f3-153">Middleware order</span></span>

<span data-ttu-id="d63f3-154">次の図は、ASP.NET Core MVC と Razor Pages アプリの完全な要求処理パイプラインを示しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="d63f3-155">一般的なアプリでどのように既存のミドルウェアが順序付けされ、どこにカスタム ミドルウェアが追加されるかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="d63f3-156">シナリオでの必要性に応じて、既存のミドルウェアの順序を変更したり、新しいカスタム ミドルウェアを挿入したりする方法については、完全に制御できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core のミドルウェア パイプライン](index/_static/middleware-pipeline.svg)

<span data-ttu-id="d63f3-158">前の図の**エンドポイント** ミドルウェアでは、対応するアプリの種類 (MVC または Razor Pages) のフィルター パイプラインが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core のフィルター パイプライン](index/_static/mvc-endpoint.svg)

<span data-ttu-id="d63f3-160">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="d63f3-161">この順序は、セキュリティ、パフォーマンス、および機能にとって**重要**です。</span><span class="sxs-lookup"><span data-stu-id="d63f3-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="d63f3-162">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="d63f3-163">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-163">In the preceding code:</span></span>

* <span data-ttu-id="d63f3-164">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="d63f3-165">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="d63f3-166">たとえば、`UseCors`、`UseAuthentication`、`UseAuthorization` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-166">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="d63f3-167">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-167">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="d63f3-168">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="d63f3-168">Exception/error handling</span></span>
   * <span data-ttu-id="d63f3-169">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="d63f3-169">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="d63f3-170">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-170">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="d63f3-171">データベース エラー ページ ミドルウェアによりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-171">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="d63f3-172">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="d63f3-172">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="d63f3-173">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-173">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="d63f3-174">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-174">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="d63f3-175">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-175">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="d63f3-176">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-176">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="d63f3-177">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-177">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="d63f3-178">ルーティング ミドルウェア (`UseRouting`) により、要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-178">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="d63f3-179">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-179">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="d63f3-180">承認ミドルウェア (`UseAuthorization`) により、ユーザーがセキュリティで保護されたリソースにアクセスすることが承認されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-180">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="d63f3-181">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-181">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="d63f3-182">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-182">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="d63f3-183">エンドポイント ルーティング ミドルウェア (`MapRazorPages` を含む `UseEndpoints`) により、Razor Pages エンドポイントが要求パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-183">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="d63f3-184">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-184">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="d63f3-185">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="d63f3-185"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="d63f3-186">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-186">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="d63f3-187">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-187">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="d63f3-188">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="d63f3-188">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="d63f3-189">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-189">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="d63f3-190">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-190">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="d63f3-191">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-191">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="d63f3-192">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="d63f3-192">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="d63f3-193">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-193">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="d63f3-194">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-194">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="d63f3-195">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="d63f3-195">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="d63f3-196">Razor Pages の応答は圧縮できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-196">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="d63f3-197">シングルページ アプリケーション (SPA) の場合、通常はミドルウェア パイプラインの最後に SPA ミドルウェア <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> を配置します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-197">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="d63f3-198">SPA ミドルウェアは、次のために最後になります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-198">The SPA middleware comes last:</span></span>

* <span data-ttu-id="d63f3-199">対応する要求に最初にその他のミドルウェアが応答するため。</span><span class="sxs-lookup"><span data-stu-id="d63f3-199">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="d63f3-200">クライアント側ルーティングを使用する SPA がサーバー アプリに認識されないすべてのルートを実行するため。</span><span class="sxs-lookup"><span data-stu-id="d63f3-200">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="d63f3-201">SPA の詳細については、[React](xref:spa/react) と [Angular](xref:spa/angular) プロジェクト テンプレートのガイドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-201">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="d63f3-202">ミドルウェア パイプラインを分岐する</span><span class="sxs-lookup"><span data-stu-id="d63f3-202">Branch the middleware pipeline</span></span>

<span data-ttu-id="d63f3-203"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-203"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="d63f3-204">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-204">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="d63f3-205">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-205">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="d63f3-206">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-206">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="d63f3-207">要求</span><span class="sxs-lookup"><span data-stu-id="d63f3-207">Request</span></span>             | <span data-ttu-id="d63f3-208">応答</span><span class="sxs-lookup"><span data-stu-id="d63f3-208">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="d63f3-209">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="d63f3-209">localhost:1234</span></span>      | <span data-ttu-id="d63f3-210">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d63f3-210">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="d63f3-211">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="d63f3-211">localhost:1234/map1</span></span> | <span data-ttu-id="d63f3-212">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="d63f3-212">Map Test 1</span></span>                   |
| <span data-ttu-id="d63f3-213">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="d63f3-213">localhost:1234/map2</span></span> | <span data-ttu-id="d63f3-214">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="d63f3-214">Map Test 2</span></span>                   |
| <span data-ttu-id="d63f3-215">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="d63f3-215">localhost:1234/map3</span></span> | <span data-ttu-id="d63f3-216">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d63f3-216">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="d63f3-217">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-217">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="d63f3-218">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-218">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="d63f3-219">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-219">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="d63f3-220"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-220"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="d63f3-221">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-221">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="d63f3-222">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-222">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="d63f3-223">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-223">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="d63f3-224">要求</span><span class="sxs-lookup"><span data-stu-id="d63f3-224">Request</span></span>                       | <span data-ttu-id="d63f3-225">応答</span><span class="sxs-lookup"><span data-stu-id="d63f3-225">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="d63f3-226">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="d63f3-226">localhost:1234</span></span>                | <span data-ttu-id="d63f3-227">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d63f3-227">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="d63f3-228">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="d63f3-228">localhost:1234/?branch=master</span></span> | <span data-ttu-id="d63f3-229">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="d63f3-229">Branch used = master</span></span>         |

<span data-ttu-id="d63f3-230">また <xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> では、指定された述語の結果に基づいて、要求パイプラインが分岐されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-230"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="d63f3-231">`MapWhen` とは異なり、この分岐は、ショートしたり、ターミナル ミドルウェアが含まれたりしなければ、メイン パイプラインに再参加します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-231">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="d63f3-232">前の例では、"Hello from main pipeline." の応答が</span><span class="sxs-lookup"><span data-stu-id="d63f3-232">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="d63f3-233">すべての要求に対して書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-233">is written for all requests.</span></span> <span data-ttu-id="d63f3-234">要求にクエリ文字列変数 `branch` が含まれる場合、メイン パイプラインの再参加前にその値がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-234">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="d63f3-235">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="d63f3-235">Built-in middleware</span></span>

<span data-ttu-id="d63f3-236">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-236">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="d63f3-237">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-237">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="d63f3-238">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-238">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="d63f3-239">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-239">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="d63f3-240">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="d63f3-240">Middleware</span></span> | <span data-ttu-id="d63f3-241">説明</span><span class="sxs-lookup"><span data-stu-id="d63f3-241">Description</span></span> | <span data-ttu-id="d63f3-242">順番</span><span class="sxs-lookup"><span data-stu-id="d63f3-242">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="d63f3-243">認証</span><span class="sxs-lookup"><span data-stu-id="d63f3-243">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="d63f3-244">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-244">Provides authentication support.</span></span> | <span data-ttu-id="d63f3-245">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-245">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="d63f3-246">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-246">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="d63f3-247">承認</span><span class="sxs-lookup"><span data-stu-id="d63f3-247">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | <span data-ttu-id="d63f3-248">承認のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-248">Provides authorization support.</span></span> | <span data-ttu-id="d63f3-249">認証ミドルウェアの直後。</span><span class="sxs-lookup"><span data-stu-id="d63f3-249">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="d63f3-250">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="d63f3-250">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="d63f3-251">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-251">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="d63f3-252">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-252">Before middleware that issues cookies.</span></span> <span data-ttu-id="d63f3-253">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="d63f3-253">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="d63f3-254">CORS</span><span class="sxs-lookup"><span data-stu-id="d63f3-254">CORS</span></span>](xref:security/cors) | <span data-ttu-id="d63f3-255">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-255">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="d63f3-256">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-256">Before components that use CORS.</span></span> |
| [<span data-ttu-id="d63f3-257">診断</span><span class="sxs-lookup"><span data-stu-id="d63f3-257">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="d63f3-258">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="d63f3-258">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="d63f3-259">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-259">Before components that generate errors.</span></span> <span data-ttu-id="d63f3-260">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-260">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="d63f3-261">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="d63f3-261">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="d63f3-262">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-262">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="d63f3-263">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-263">Before components that consume the updated fields.</span></span> <span data-ttu-id="d63f3-264">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="d63f3-264">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="d63f3-265">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="d63f3-265">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="d63f3-266">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-266">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="d63f3-267">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-267">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="d63f3-268">ヘッダーの伝達</span><span class="sxs-lookup"><span data-stu-id="d63f3-268">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="d63f3-269">HTTP ヘッダーを受信要求から送信 HTTP クライアント要求に伝達します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-269">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="d63f3-270">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="d63f3-270">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="d63f3-271">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-271">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="d63f3-272">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-272">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="d63f3-273">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="d63f3-273">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="d63f3-274">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="d63f3-274">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="d63f3-275">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-275">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="d63f3-276">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="d63f3-276">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="d63f3-277">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="d63f3-277">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="d63f3-278">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="d63f3-278">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="d63f3-279">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="d63f3-279">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="d63f3-280">MVC</span><span class="sxs-lookup"><span data-stu-id="d63f3-280">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="d63f3-281">MVC/Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-281">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="d63f3-282">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-282">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="d63f3-283">OWIN</span><span class="sxs-lookup"><span data-stu-id="d63f3-283">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="d63f3-284">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-284">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="d63f3-285">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-285">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="d63f3-286">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="d63f3-286">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="d63f3-287">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-287">Provides support for caching responses.</span></span> | <span data-ttu-id="d63f3-288">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-288">Before components that require caching.</span></span> |
| [<span data-ttu-id="d63f3-289">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="d63f3-289">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="d63f3-290">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-290">Provides support for compressing responses.</span></span> | <span data-ttu-id="d63f3-291">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-291">Before components that require compression.</span></span> |
| [<span data-ttu-id="d63f3-292">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="d63f3-292">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="d63f3-293">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-293">Provides localization support.</span></span> | <span data-ttu-id="d63f3-294">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-294">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="d63f3-295">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="d63f3-295">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="d63f3-296">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-296">Defines and constrains request routes.</span></span> | <span data-ttu-id="d63f3-297">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-297">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="d63f3-298">SPA</span><span class="sxs-lookup"><span data-stu-id="d63f3-298">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | <span data-ttu-id="d63f3-299">シングルページ アプリケーション (SPA) の既定のページを返し、ミドルウェア チェーン内のこの時点以降のすべての要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-299">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="d63f3-300">チェーンの終わりで、静的ファイルや MVC アクションなどにサービスを提供する他のミドルウェアが優先されるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-300">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="d63f3-301">セッション</span><span class="sxs-lookup"><span data-stu-id="d63f3-301">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="d63f3-302">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-302">Provides support for managing user sessions.</span></span> | <span data-ttu-id="d63f3-303">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-303">Before components that require Session.</span></span> | 
| [<span data-ttu-id="d63f3-304">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="d63f3-304">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="d63f3-305">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-305">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="d63f3-306">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-306">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="d63f3-307">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="d63f3-307">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="d63f3-308">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-308">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="d63f3-309">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-309">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="d63f3-310">WebSocket</span><span class="sxs-lookup"><span data-stu-id="d63f3-310">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="d63f3-311">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="d63f3-311">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="d63f3-312">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-312">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d63f3-313">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d63f3-313">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d63f3-314">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d63f3-314">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d63f3-315">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-315">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="d63f3-316">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-316">Each component:</span></span>

* <span data-ttu-id="d63f3-317">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-317">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="d63f3-318">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-318">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="d63f3-319">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-319">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="d63f3-320">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-320">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="d63f3-321">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-321">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="d63f3-322">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-322">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="d63f3-323">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-323">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="d63f3-324">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-324">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="d63f3-325">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-325">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="d63f3-326"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-326"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="d63f3-327">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="d63f3-327">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="d63f3-328">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-328">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="d63f3-329">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-329">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="d63f3-330">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-330">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="d63f3-334">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-334">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="d63f3-335">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-335">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="d63f3-336">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-336">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="d63f3-337">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="d63f3-337">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="d63f3-338">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-338">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="d63f3-339">最初の <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートが、パイプラインを終了します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-339">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="d63f3-340">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="d63f3-340">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="d63f3-341">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="d63f3-341">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="d63f3-342">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-342">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="d63f3-343">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-343">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="d63f3-344">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-344">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="d63f3-345">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-345">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="d63f3-346">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-346">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="d63f3-347">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-347">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="d63f3-348">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-348">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="d63f3-349">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-349">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="d63f3-350">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-350">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="d63f3-351">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-351">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="d63f3-352">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-352">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="d63f3-353">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-353">May cause a protocol violation.</span></span> <span data-ttu-id="d63f3-354">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="d63f3-354">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="d63f3-355">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-355">May corrupt the body format.</span></span> <span data-ttu-id="d63f3-356">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="d63f3-356">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="d63f3-357"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="d63f3-357"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="d63f3-358">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="d63f3-358">Middleware order</span></span>

<span data-ttu-id="d63f3-359">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-359">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="d63f3-360">この順序は、セキュリティ、パフォーマンス、および機能にとって**重要**です。</span><span class="sxs-lookup"><span data-stu-id="d63f3-360">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="d63f3-361">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-361">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="d63f3-362">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-362">In the preceding code:</span></span>

* <span data-ttu-id="d63f3-363">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-363">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="d63f3-364">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-364">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="d63f3-365">たとえば、`UseCors` と `UseAuthentication` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-365">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="d63f3-366">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-366">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="d63f3-367">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="d63f3-367">Exception/error handling</span></span>
   * <span data-ttu-id="d63f3-368">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="d63f3-368">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="d63f3-369">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-369">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="d63f3-370">データベース エラー ページ ミドルウェア (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) によりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-370">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="d63f3-371">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="d63f3-371">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="d63f3-372">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-372">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="d63f3-373">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-373">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="d63f3-374">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-374">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="d63f3-375">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-375">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="d63f3-376">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-376">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="d63f3-377">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-377">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="d63f3-378">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-378">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="d63f3-379">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-379">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="d63f3-380">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) を使い、要求パイプラインに MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-380">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="d63f3-381">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-381">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="d63f3-382">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="d63f3-382"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="d63f3-383">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-383">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="d63f3-384">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-384">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="d63f3-385">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="d63f3-385">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="d63f3-386">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-386">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="d63f3-387">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-387">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="d63f3-388">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-388">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="d63f3-389">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="d63f3-389">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="d63f3-390">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-390">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="d63f3-391">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-391">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="d63f3-392">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="d63f3-392">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="d63f3-393"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> からの MVC 応答を圧縮することができます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-393">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="d63f3-394">Use、Run、および Map</span><span class="sxs-lookup"><span data-stu-id="d63f3-394">Use, Run, and Map</span></span>

<span data-ttu-id="d63f3-395">HTTP パイプラインを構成するには、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> を使います。</span><span class="sxs-lookup"><span data-stu-id="d63f3-395">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="d63f3-396">`Use` メソッドは、パイプラインをショートさせることができます (つまり `next` 要求デリゲートを呼び出さない場合)。</span><span class="sxs-lookup"><span data-stu-id="d63f3-396">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="d63f3-397">`Run` は規則であり、一部のミドルウェア コンポーネントは、パイプラインの最後に実行される `Run[Middleware]` メソッドを公開することがあります。</span><span class="sxs-lookup"><span data-stu-id="d63f3-397">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="d63f3-398"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-398"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="d63f3-399">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-399">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="d63f3-400">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-400">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="d63f3-401">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-401">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="d63f3-402">要求</span><span class="sxs-lookup"><span data-stu-id="d63f3-402">Request</span></span>             | <span data-ttu-id="d63f3-403">応答</span><span class="sxs-lookup"><span data-stu-id="d63f3-403">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="d63f3-404">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="d63f3-404">localhost:1234</span></span>      | <span data-ttu-id="d63f3-405">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d63f3-405">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="d63f3-406">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="d63f3-406">localhost:1234/map1</span></span> | <span data-ttu-id="d63f3-407">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="d63f3-407">Map Test 1</span></span>                   |
| <span data-ttu-id="d63f3-408">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="d63f3-408">localhost:1234/map2</span></span> | <span data-ttu-id="d63f3-409">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="d63f3-409">Map Test 2</span></span>                   |
| <span data-ttu-id="d63f3-410">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="d63f3-410">localhost:1234/map3</span></span> | <span data-ttu-id="d63f3-411">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d63f3-411">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="d63f3-412">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-412">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="d63f3-413"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-413"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="d63f3-414">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-414">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="d63f3-415">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-415">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="d63f3-416">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-416">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="d63f3-417">要求</span><span class="sxs-lookup"><span data-stu-id="d63f3-417">Request</span></span>                       | <span data-ttu-id="d63f3-418">応答</span><span class="sxs-lookup"><span data-stu-id="d63f3-418">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="d63f3-419">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="d63f3-419">localhost:1234</span></span>                | <span data-ttu-id="d63f3-420">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="d63f3-420">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="d63f3-421">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="d63f3-421">localhost:1234/?branch=master</span></span> | <span data-ttu-id="d63f3-422">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="d63f3-422">Branch used = master</span></span>         |

<span data-ttu-id="d63f3-423">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-423">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="d63f3-424">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-424">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="d63f3-425">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="d63f3-425">Built-in middleware</span></span>

<span data-ttu-id="d63f3-426">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-426">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="d63f3-427">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="d63f3-427">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="d63f3-428">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d63f3-428">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="d63f3-429">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d63f3-429">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="d63f3-430">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="d63f3-430">Middleware</span></span> | <span data-ttu-id="d63f3-431">説明</span><span class="sxs-lookup"><span data-stu-id="d63f3-431">Description</span></span> | <span data-ttu-id="d63f3-432">順番</span><span class="sxs-lookup"><span data-stu-id="d63f3-432">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="d63f3-433">認証</span><span class="sxs-lookup"><span data-stu-id="d63f3-433">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="d63f3-434">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-434">Provides authentication support.</span></span> | <span data-ttu-id="d63f3-435">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-435">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="d63f3-436">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-436">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="d63f3-437">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="d63f3-437">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="d63f3-438">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-438">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="d63f3-439">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-439">Before middleware that issues cookies.</span></span> <span data-ttu-id="d63f3-440">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="d63f3-440">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="d63f3-441">CORS</span><span class="sxs-lookup"><span data-stu-id="d63f3-441">CORS</span></span>](xref:security/cors) | <span data-ttu-id="d63f3-442">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-442">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="d63f3-443">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-443">Before components that use CORS.</span></span> |
| [<span data-ttu-id="d63f3-444">診断</span><span class="sxs-lookup"><span data-stu-id="d63f3-444">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="d63f3-445">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="d63f3-445">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="d63f3-446">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-446">Before components that generate errors.</span></span> <span data-ttu-id="d63f3-447">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-447">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="d63f3-448">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="d63f3-448">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="d63f3-449">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-449">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="d63f3-450">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-450">Before components that consume the updated fields.</span></span> <span data-ttu-id="d63f3-451">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="d63f3-451">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="d63f3-452">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="d63f3-452">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="d63f3-453">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-453">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="d63f3-454">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-454">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="d63f3-455">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="d63f3-455">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="d63f3-456">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-456">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="d63f3-457">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-457">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="d63f3-458">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="d63f3-458">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="d63f3-459">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="d63f3-459">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="d63f3-460">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-460">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="d63f3-461">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="d63f3-461">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="d63f3-462">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="d63f3-462">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="d63f3-463">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="d63f3-463">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="d63f3-464">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="d63f3-464">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="d63f3-465">MVC</span><span class="sxs-lookup"><span data-stu-id="d63f3-465">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="d63f3-466">MVC/Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-466">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="d63f3-467">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-467">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="d63f3-468">OWIN</span><span class="sxs-lookup"><span data-stu-id="d63f3-468">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="d63f3-469">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-469">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="d63f3-470">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-470">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="d63f3-471">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="d63f3-471">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="d63f3-472">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-472">Provides support for caching responses.</span></span> | <span data-ttu-id="d63f3-473">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-473">Before components that require caching.</span></span> |
| [<span data-ttu-id="d63f3-474">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="d63f3-474">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="d63f3-475">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-475">Provides support for compressing responses.</span></span> | <span data-ttu-id="d63f3-476">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-476">Before components that require compression.</span></span> |
| [<span data-ttu-id="d63f3-477">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="d63f3-477">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="d63f3-478">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-478">Provides localization support.</span></span> | <span data-ttu-id="d63f3-479">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-479">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="d63f3-480">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="d63f3-480">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="d63f3-481">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-481">Defines and constrains request routes.</span></span> | <span data-ttu-id="d63f3-482">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-482">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="d63f3-483">セッション</span><span class="sxs-lookup"><span data-stu-id="d63f3-483">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="d63f3-484">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-484">Provides support for managing user sessions.</span></span> | <span data-ttu-id="d63f3-485">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-485">Before components that require Session.</span></span> |
| [<span data-ttu-id="d63f3-486">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="d63f3-486">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="d63f3-487">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-487">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="d63f3-488">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="d63f3-488">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="d63f3-489">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="d63f3-489">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="d63f3-490">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="d63f3-490">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="d63f3-491">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-491">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="d63f3-492">WebSocket</span><span class="sxs-lookup"><span data-stu-id="d63f3-492">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="d63f3-493">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="d63f3-493">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="d63f3-494">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="d63f3-494">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d63f3-495">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d63f3-495">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
