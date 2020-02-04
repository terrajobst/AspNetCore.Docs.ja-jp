---
title: ASP.NET Core のミドルウェア
author: rick-anderson
description: ASP.NET Core のミドルウェアと要求パイプラインについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 5c8e9e58ab222e482ef029f5099d0a8acd07d8a6
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972033"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="86840-103">ASP.NET Core のミドルウェア</span><span class="sxs-lookup"><span data-stu-id="86840-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86840-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="86840-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="86840-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="86840-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="86840-106">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="86840-106">Each component:</span></span>

* <span data-ttu-id="86840-107">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="86840-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="86840-108">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="86840-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="86840-109">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="86840-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="86840-110">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="86840-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="86840-111">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="86840-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="86840-112">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="86840-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="86840-113">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="86840-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="86840-114">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="86840-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="86840-115">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="86840-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="86840-116"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="86840-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="86840-117">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="86840-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="86840-118">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="86840-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="86840-119">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="86840-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="86840-120">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="86840-120">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="86840-124">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="86840-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="86840-125">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="86840-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="86840-126">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="86840-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="86840-127">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="86840-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="86840-128">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="86840-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="86840-129">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="86840-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="86840-130">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="86840-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="86840-131">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="86840-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="86840-132">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="86840-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="86840-133">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="86840-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="86840-134">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="86840-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="86840-135">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="86840-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="86840-136">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="86840-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="86840-137">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86840-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="86840-138">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="86840-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="86840-139">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="86840-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="86840-140">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="86840-140">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="86840-141">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="86840-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="86840-142">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="86840-142">May cause a protocol violation.</span></span> <span data-ttu-id="86840-143">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="86840-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="86840-144">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="86840-144">May corrupt the body format.</span></span> <span data-ttu-id="86840-145">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="86840-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="86840-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="86840-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="86840-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートでは、`next` パラメーターは受け取られません。</span><span class="sxs-lookup"><span data-stu-id="86840-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="86840-148">最初の `Run` デリゲートが常に終点となり、パイプラインが終了されます。</span><span class="sxs-lookup"><span data-stu-id="86840-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="86840-149">`Run` は規則です。</span><span class="sxs-lookup"><span data-stu-id="86840-149">`Run` is a convention.</span></span> <span data-ttu-id="86840-150">一部のミドルウェア コンポーネントでは、パイプラインの最後に実行される `Run[Middleware]` メソッドが公開されることがあります。</span><span class="sxs-lookup"><span data-stu-id="86840-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]

<span data-ttu-id="86840-151">前の例では、`Run` デリゲートによって応答に `"Hello from 2nd delegate."` が書き込まれ、その後、パイプラインが終了となります。</span><span class="sxs-lookup"><span data-stu-id="86840-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="86840-152">別の `Use` または `Run` デリゲートが `Run` デリゲートの後に追加される場合、そのデリゲートは呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="86840-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="86840-153">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="86840-153">Middleware order</span></span>

<span data-ttu-id="86840-154">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="86840-154">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="86840-155">この順序は、セキュリティ、パフォーマンス、および機能にとって**重要**です。</span><span class="sxs-lookup"><span data-stu-id="86840-155">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="86840-156">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="86840-156">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="86840-157">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="86840-157">In the preceding code:</span></span>

* <span data-ttu-id="86840-158">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="86840-158">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="86840-159">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="86840-159">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="86840-160">たとえば、`UseCors`、`UseAuthentication`、`UseAuthorization` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="86840-160">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="86840-161">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="86840-161">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="86840-162">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="86840-162">Exception/error handling</span></span>
   * <span data-ttu-id="86840-163">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="86840-163">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="86840-164">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="86840-164">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="86840-165">データベース エラー ページ ミドルウェアによりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="86840-165">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="86840-166">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="86840-166">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="86840-167">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="86840-167">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="86840-168">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="86840-168">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="86840-169">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="86840-169">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="86840-170">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="86840-170">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="86840-171">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="86840-171">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="86840-172">ルーティング ミドルウェア (`UseRouting`) により、要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="86840-172">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="86840-173">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="86840-173">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="86840-174">承認ミドルウェア (`UseAuthorization`) により、ユーザーがセキュリティで保護されたリソースにアクセスすることが承認されます。</span><span class="sxs-lookup"><span data-stu-id="86840-174">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="86840-175">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="86840-175">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="86840-176">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="86840-176">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="86840-177">エンドポイント ルーティング ミドルウェア (`MapRazorPages` を含む `UseEndpoints`) により、Razor Pages エンドポイントが要求パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="86840-177">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="86840-178">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="86840-178">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="86840-179">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="86840-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="86840-180">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="86840-180">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="86840-181">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="86840-181">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="86840-182">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="86840-182">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="86840-183">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="86840-183">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="86840-184">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86840-184">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="86840-185">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="86840-185">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="86840-186">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="86840-186">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="86840-187">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="86840-187">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="86840-188">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="86840-188">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="86840-189">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="86840-189">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="86840-190">Razor Pages の応答は圧縮できます。</span><span class="sxs-lookup"><span data-stu-id="86840-190">The Razor Pages responses can be compressed.</span></span>

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

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="86840-191">ミドルウェア パイプラインを分岐する</span><span class="sxs-lookup"><span data-stu-id="86840-191">Branch the middleware pipeline</span></span>

<span data-ttu-id="86840-192"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="86840-192"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="86840-193">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="86840-193">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="86840-194">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="86840-194">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="86840-195">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="86840-195">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="86840-196">要求</span><span class="sxs-lookup"><span data-stu-id="86840-196">Request</span></span>             | <span data-ttu-id="86840-197">応答</span><span class="sxs-lookup"><span data-stu-id="86840-197">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="86840-198">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="86840-198">localhost:1234</span></span>      | <span data-ttu-id="86840-199">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86840-199">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="86840-200">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="86840-200">localhost:1234/map1</span></span> | <span data-ttu-id="86840-201">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="86840-201">Map Test 1</span></span>                   |
| <span data-ttu-id="86840-202">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="86840-202">localhost:1234/map2</span></span> | <span data-ttu-id="86840-203">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="86840-203">Map Test 2</span></span>                   |
| <span data-ttu-id="86840-204">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="86840-204">localhost:1234/map3</span></span> | <span data-ttu-id="86840-205">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86840-205">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="86840-206">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="86840-206">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="86840-207">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="86840-207">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="86840-208">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="86840-208">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="86840-209"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="86840-209"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="86840-210">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="86840-210">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="86840-211">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="86840-211">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="86840-212">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="86840-212">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="86840-213">要求</span><span class="sxs-lookup"><span data-stu-id="86840-213">Request</span></span>                       | <span data-ttu-id="86840-214">応答</span><span class="sxs-lookup"><span data-stu-id="86840-214">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="86840-215">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="86840-215">localhost:1234</span></span>                | <span data-ttu-id="86840-216">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86840-216">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="86840-217">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="86840-217">localhost:1234/?branch=master</span></span> | <span data-ttu-id="86840-218">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="86840-218">Branch used = master</span></span>         |

<span data-ttu-id="86840-219">また <xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> では、指定された述語の結果に基づいて、要求パイプラインが分岐されます。</span><span class="sxs-lookup"><span data-stu-id="86840-219"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="86840-220">`MapWhen` とは異なり、ショートしたり、ターミナル ミドルウェアが含まれたりする場合、この分岐はメイン パイプラインに再参加します。</span><span class="sxs-lookup"><span data-stu-id="86840-220">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it does short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=23-24)]

<span data-ttu-id="86840-221">前の例では、"Hello from main pipeline." の応答が</span><span class="sxs-lookup"><span data-stu-id="86840-221">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="86840-222">すべての要求に対して書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="86840-222">is written for all requests.</span></span> <span data-ttu-id="86840-223">要求にクエリ文字列変数 `branch` が含まれる場合、メイン パイプラインの再参加前にその値がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="86840-223">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="86840-224">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="86840-224">Built-in middleware</span></span>

<span data-ttu-id="86840-225">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="86840-225">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="86840-226">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="86840-226">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="86840-227">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="86840-227">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="86840-228">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="86840-228">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="86840-229">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="86840-229">Middleware</span></span> | <span data-ttu-id="86840-230">説明</span><span class="sxs-lookup"><span data-stu-id="86840-230">Description</span></span> | <span data-ttu-id="86840-231">注文</span><span class="sxs-lookup"><span data-stu-id="86840-231">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="86840-232">認証</span><span class="sxs-lookup"><span data-stu-id="86840-232">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="86840-233">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-233">Provides authentication support.</span></span> | <span data-ttu-id="86840-234">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="86840-234">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="86840-235">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="86840-235">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="86840-236">承認</span><span class="sxs-lookup"><span data-stu-id="86840-236">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | <span data-ttu-id="86840-237">承認のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-237">Provides authorization support.</span></span> | <span data-ttu-id="86840-238">認証ミドルウェアの直後。</span><span class="sxs-lookup"><span data-stu-id="86840-238">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="86840-239">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="86840-239">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="86840-240">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="86840-240">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="86840-241">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="86840-241">Before middleware that issues cookies.</span></span> <span data-ttu-id="86840-242">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="86840-242">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="86840-243">CORS</span><span class="sxs-lookup"><span data-stu-id="86840-243">CORS</span></span>](xref:security/cors) | <span data-ttu-id="86840-244">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="86840-244">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="86840-245">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-245">Before components that use CORS.</span></span> |
| [<span data-ttu-id="86840-246">診断</span><span class="sxs-lookup"><span data-stu-id="86840-246">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="86840-247">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="86840-247">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="86840-248">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-248">Before components that generate errors.</span></span> <span data-ttu-id="86840-249">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-249">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="86840-250">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="86840-250">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="86840-251">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="86840-251">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="86840-252">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-252">Before components that consume the updated fields.</span></span> <span data-ttu-id="86840-253">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="86840-253">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="86840-254">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="86840-254">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="86840-255">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="86840-255">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="86840-256">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-256">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="86840-257">ヘッダーの伝達</span><span class="sxs-lookup"><span data-stu-id="86840-257">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="86840-258">HTTP ヘッダーを受信要求から送信 HTTP クライアント要求に伝達します。</span><span class="sxs-lookup"><span data-stu-id="86840-258">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="86840-259">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="86840-259">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="86840-260">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="86840-260">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="86840-261">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-261">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="86840-262">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="86840-262">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="86840-263">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="86840-263">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="86840-264">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-264">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="86840-265">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="86840-265">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="86840-266">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="86840-266">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="86840-267">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="86840-267">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="86840-268">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="86840-268">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="86840-269">MVC</span><span class="sxs-lookup"><span data-stu-id="86840-269">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="86840-270">MVC/Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="86840-270">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="86840-271">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-271">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="86840-272">OWIN</span><span class="sxs-lookup"><span data-stu-id="86840-272">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="86840-273">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="86840-273">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="86840-274">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-274">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="86840-275">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="86840-275">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="86840-276">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-276">Provides support for caching responses.</span></span> | <span data-ttu-id="86840-277">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-277">Before components that require caching.</span></span> |
| [<span data-ttu-id="86840-278">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="86840-278">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="86840-279">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-279">Provides support for compressing responses.</span></span> | <span data-ttu-id="86840-280">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-280">Before components that require compression.</span></span> |
| [<span data-ttu-id="86840-281">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="86840-281">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="86840-282">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-282">Provides localization support.</span></span> | <span data-ttu-id="86840-283">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-283">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="86840-284">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="86840-284">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="86840-285">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="86840-285">Defines and constrains request routes.</span></span> | <span data-ttu-id="86840-286">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="86840-286">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="86840-287">セッション</span><span class="sxs-lookup"><span data-stu-id="86840-287">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="86840-288">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-288">Provides support for managing user sessions.</span></span> | <span data-ttu-id="86840-289">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-289">Before components that require Session.</span></span> |
| [<span data-ttu-id="86840-290">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="86840-290">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="86840-291">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-291">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="86840-292">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-292">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="86840-293">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="86840-293">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="86840-294">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-294">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="86840-295">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-295">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="86840-296">WebSocket</span><span class="sxs-lookup"><span data-stu-id="86840-296">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="86840-297">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="86840-297">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="86840-298">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-298">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="86840-299">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="86840-299">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="86840-300">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="86840-300">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="86840-301">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="86840-301">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="86840-302">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="86840-302">Each component:</span></span>

* <span data-ttu-id="86840-303">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="86840-303">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="86840-304">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="86840-304">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="86840-305">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="86840-305">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="86840-306">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="86840-306">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="86840-307">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="86840-307">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="86840-308">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="86840-308">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="86840-309">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="86840-309">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="86840-310">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="86840-310">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="86840-311">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="86840-311">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="86840-312"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="86840-312"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="86840-313">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="86840-313">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="86840-314">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="86840-314">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="86840-315">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="86840-315">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="86840-316">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="86840-316">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="86840-320">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="86840-320">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="86840-321">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="86840-321">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="86840-322">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="86840-322">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="86840-323">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="86840-323">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="86840-324">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="86840-324">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="86840-325">最初の <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートが、パイプラインを終了します。</span><span class="sxs-lookup"><span data-stu-id="86840-325">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="86840-326">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="86840-326">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="86840-327">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="86840-327">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="86840-328">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="86840-328">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="86840-329">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="86840-329">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="86840-330">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="86840-330">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="86840-331">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="86840-331">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="86840-332">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="86840-332">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="86840-333">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="86840-333">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="86840-334">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86840-334">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="86840-335">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="86840-335">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="86840-336">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="86840-336">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="86840-337">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="86840-337">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="86840-338">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="86840-338">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="86840-339">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="86840-339">May cause a protocol violation.</span></span> <span data-ttu-id="86840-340">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="86840-340">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="86840-341">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="86840-341">May corrupt the body format.</span></span> <span data-ttu-id="86840-342">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="86840-342">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="86840-343"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="86840-343"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="86840-344">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="86840-344">Middleware order</span></span>

<span data-ttu-id="86840-345">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="86840-345">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="86840-346">この順序は、セキュリティ、パフォーマンス、および機能にとって**重要**です。</span><span class="sxs-lookup"><span data-stu-id="86840-346">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="86840-347">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="86840-347">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="86840-348">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="86840-348">In the preceding code:</span></span>

* <span data-ttu-id="86840-349">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="86840-349">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="86840-350">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="86840-350">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="86840-351">たとえば、`UseCors` と `UseAuthentication` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="86840-351">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="86840-352">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="86840-352">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="86840-353">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="86840-353">Exception/error handling</span></span>
   * <span data-ttu-id="86840-354">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="86840-354">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="86840-355">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="86840-355">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="86840-356">データベース エラー ページ ミドルウェア (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) によりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="86840-356">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="86840-357">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="86840-357">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="86840-358">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="86840-358">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="86840-359">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="86840-359">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="86840-360">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="86840-360">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="86840-361">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="86840-361">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="86840-362">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="86840-362">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="86840-363">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="86840-363">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="86840-364">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="86840-364">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="86840-365">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="86840-365">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="86840-366">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) を使い、要求パイプラインに MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="86840-366">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="86840-367">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="86840-367">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="86840-368">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="86840-368"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="86840-369">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="86840-369">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="86840-370">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="86840-370">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="86840-371">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="86840-371">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="86840-372">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="86840-372">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="86840-373">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="86840-373">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="86840-374">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="86840-374">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="86840-375">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="86840-375">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="86840-376">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="86840-376">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="86840-377">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="86840-377">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="86840-378">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="86840-378">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="86840-379"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> からの MVC 応答を圧縮することができます。</span><span class="sxs-lookup"><span data-stu-id="86840-379">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="86840-380">Use、Run、および Map</span><span class="sxs-lookup"><span data-stu-id="86840-380">Use, Run, and Map</span></span>

<span data-ttu-id="86840-381">HTTP パイプラインを構成するには、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> を使います。</span><span class="sxs-lookup"><span data-stu-id="86840-381">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="86840-382">`Use` メソッドは、パイプラインをショートさせることができます (つまり `next` 要求デリゲートを呼び出さない場合)。</span><span class="sxs-lookup"><span data-stu-id="86840-382">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="86840-383">`Run` は規則であり、一部のミドルウェア コンポーネントは、パイプラインの最後に実行される `Run[Middleware]` メソッドを公開することがあります。</span><span class="sxs-lookup"><span data-stu-id="86840-383">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="86840-384"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="86840-384"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="86840-385">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="86840-385">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="86840-386">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="86840-386">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="86840-387">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="86840-387">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="86840-388">要求</span><span class="sxs-lookup"><span data-stu-id="86840-388">Request</span></span>             | <span data-ttu-id="86840-389">応答</span><span class="sxs-lookup"><span data-stu-id="86840-389">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="86840-390">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="86840-390">localhost:1234</span></span>      | <span data-ttu-id="86840-391">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86840-391">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="86840-392">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="86840-392">localhost:1234/map1</span></span> | <span data-ttu-id="86840-393">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="86840-393">Map Test 1</span></span>                   |
| <span data-ttu-id="86840-394">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="86840-394">localhost:1234/map2</span></span> | <span data-ttu-id="86840-395">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="86840-395">Map Test 2</span></span>                   |
| <span data-ttu-id="86840-396">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="86840-396">localhost:1234/map3</span></span> | <span data-ttu-id="86840-397">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86840-397">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="86840-398">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="86840-398">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="86840-399"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="86840-399"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="86840-400">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="86840-400">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="86840-401">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="86840-401">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="86840-402">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="86840-402">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="86840-403">要求</span><span class="sxs-lookup"><span data-stu-id="86840-403">Request</span></span>                       | <span data-ttu-id="86840-404">応答</span><span class="sxs-lookup"><span data-stu-id="86840-404">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="86840-405">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="86840-405">localhost:1234</span></span>                | <span data-ttu-id="86840-406">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="86840-406">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="86840-407">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="86840-407">localhost:1234/?branch=master</span></span> | <span data-ttu-id="86840-408">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="86840-408">Branch used = master</span></span>         |

<span data-ttu-id="86840-409">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="86840-409">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="86840-410">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="86840-410">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="86840-411">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="86840-411">Built-in middleware</span></span>

<span data-ttu-id="86840-412">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="86840-412">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="86840-413">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="86840-413">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="86840-414">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="86840-414">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="86840-415">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="86840-415">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="86840-416">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="86840-416">Middleware</span></span> | <span data-ttu-id="86840-417">説明</span><span class="sxs-lookup"><span data-stu-id="86840-417">Description</span></span> | <span data-ttu-id="86840-418">注文</span><span class="sxs-lookup"><span data-stu-id="86840-418">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="86840-419">認証</span><span class="sxs-lookup"><span data-stu-id="86840-419">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="86840-420">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-420">Provides authentication support.</span></span> | <span data-ttu-id="86840-421">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="86840-421">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="86840-422">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="86840-422">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="86840-423">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="86840-423">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="86840-424">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="86840-424">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="86840-425">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="86840-425">Before middleware that issues cookies.</span></span> <span data-ttu-id="86840-426">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="86840-426">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="86840-427">CORS</span><span class="sxs-lookup"><span data-stu-id="86840-427">CORS</span></span>](xref:security/cors) | <span data-ttu-id="86840-428">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="86840-428">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="86840-429">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-429">Before components that use CORS.</span></span> |
| [<span data-ttu-id="86840-430">診断</span><span class="sxs-lookup"><span data-stu-id="86840-430">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="86840-431">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="86840-431">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="86840-432">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-432">Before components that generate errors.</span></span> <span data-ttu-id="86840-433">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-433">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="86840-434">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="86840-434">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="86840-435">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="86840-435">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="86840-436">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-436">Before components that consume the updated fields.</span></span> <span data-ttu-id="86840-437">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="86840-437">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="86840-438">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="86840-438">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="86840-439">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="86840-439">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="86840-440">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-440">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="86840-441">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="86840-441">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="86840-442">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="86840-442">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="86840-443">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-443">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="86840-444">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="86840-444">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="86840-445">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="86840-445">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="86840-446">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-446">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="86840-447">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="86840-447">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="86840-448">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="86840-448">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="86840-449">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="86840-449">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="86840-450">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="86840-450">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="86840-451">MVC</span><span class="sxs-lookup"><span data-stu-id="86840-451">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="86840-452">MVC/Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="86840-452">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="86840-453">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-453">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="86840-454">OWIN</span><span class="sxs-lookup"><span data-stu-id="86840-454">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="86840-455">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="86840-455">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="86840-456">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-456">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="86840-457">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="86840-457">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="86840-458">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-458">Provides support for caching responses.</span></span> | <span data-ttu-id="86840-459">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-459">Before components that require caching.</span></span> |
| [<span data-ttu-id="86840-460">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="86840-460">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="86840-461">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-461">Provides support for compressing responses.</span></span> | <span data-ttu-id="86840-462">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-462">Before components that require compression.</span></span> |
| [<span data-ttu-id="86840-463">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="86840-463">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="86840-464">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-464">Provides localization support.</span></span> | <span data-ttu-id="86840-465">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-465">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="86840-466">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="86840-466">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="86840-467">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="86840-467">Defines and constrains request routes.</span></span> | <span data-ttu-id="86840-468">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="86840-468">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="86840-469">セッション</span><span class="sxs-lookup"><span data-stu-id="86840-469">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="86840-470">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-470">Provides support for managing user sessions.</span></span> | <span data-ttu-id="86840-471">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-471">Before components that require Session.</span></span> |
| [<span data-ttu-id="86840-472">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="86840-472">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="86840-473">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-473">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="86840-474">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="86840-474">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="86840-475">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="86840-475">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="86840-476">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="86840-476">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="86840-477">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-477">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="86840-478">WebSocket</span><span class="sxs-lookup"><span data-stu-id="86840-478">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="86840-479">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="86840-479">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="86840-480">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="86840-480">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="86840-481">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="86840-481">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
