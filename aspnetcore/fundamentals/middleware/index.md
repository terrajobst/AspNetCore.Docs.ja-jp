---
title: ASP.NET Core のミドルウェア
author: rick-anderson
description: ASP.NET Core のミドルウェアと要求パイプラインについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: 674e89cd22ce113474dfbba44b57d9255446fc3e
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773783"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="54b70-103">ASP.NET Core のミドルウェア</span><span class="sxs-lookup"><span data-stu-id="54b70-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="54b70-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="54b70-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="54b70-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="54b70-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="54b70-106">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="54b70-106">Each component:</span></span>

* <span data-ttu-id="54b70-107">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="54b70-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="54b70-108">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="54b70-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="54b70-109">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="54b70-110">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="54b70-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="54b70-111">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="54b70-112">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="54b70-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="54b70-113">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="54b70-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="54b70-114">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="54b70-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="54b70-115">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="54b70-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="54b70-116"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="54b70-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="54b70-117">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="54b70-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="54b70-118">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="54b70-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="54b70-119">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="54b70-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="54b70-120">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="54b70-120">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="54b70-124">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="54b70-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="54b70-125">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="54b70-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="54b70-126">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="54b70-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="54b70-127">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="54b70-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="54b70-128">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="54b70-129">最初の <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートが、パイプラインを終了します。</span><span class="sxs-lookup"><span data-stu-id="54b70-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="54b70-130">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="54b70-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="54b70-131">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="54b70-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="54b70-132">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="54b70-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="54b70-133">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="54b70-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="54b70-134">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="54b70-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="54b70-135">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="54b70-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="54b70-136">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="54b70-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="54b70-137">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="54b70-138">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54b70-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="54b70-139">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="54b70-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="54b70-140">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="54b70-141">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="54b70-142">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="54b70-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="54b70-143">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="54b70-143">May cause a protocol violation.</span></span> <span data-ttu-id="54b70-144">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="54b70-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="54b70-145">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="54b70-145">May corrupt the body format.</span></span> <span data-ttu-id="54b70-146">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="54b70-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="54b70-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="54b70-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="54b70-148">注文</span><span class="sxs-lookup"><span data-stu-id="54b70-148">Order</span></span>

<span data-ttu-id="54b70-149">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="54b70-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="54b70-150">この順序は、セキュリティ、パフォーマンス、および機能にとって重要です。</span><span class="sxs-lookup"><span data-stu-id="54b70-150">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="54b70-151">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-151">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-3.0"

1. <span data-ttu-id="54b70-152">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="54b70-152">Exception/error handling</span></span>
   * <span data-ttu-id="54b70-153">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="54b70-153">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="54b70-154">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-154">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="54b70-155">データベース エラー ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) によりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-155">Database Error Page Middleware (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) reports database runtime errors.</span></span>
   * <span data-ttu-id="54b70-156">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="54b70-156">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="54b70-157">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-157">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="54b70-158">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-158">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="54b70-159">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-159">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="54b70-160">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-160">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="54b70-161">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="54b70-161">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="54b70-162">ルーティング ミドルウェア (`UseRouting`) により、要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-162">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="54b70-163">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-163">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="54b70-164">承認ミドルウェア (`UseAuthorization`) により、ユーザーがセキュリティで保護されたリソースにアクセスすることが承認されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-164">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="54b70-165">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-165">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="54b70-166">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="54b70-166">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="54b70-167">エンドポイント ルーティング ミドルウェア (`MapRazorPages` を含む `UseEndpoints`) により、Razor Pages エンドポイントが要求パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-167">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="54b70-168">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="54b70-168">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="54b70-169">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="54b70-169"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="54b70-170">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-170">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="54b70-171">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="54b70-171">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="54b70-172">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="54b70-172">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="54b70-173">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-173">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="54b70-174">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54b70-174">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="54b70-175">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-175">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="54b70-176">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="54b70-176">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="54b70-177">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="54b70-177">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="54b70-178">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="54b70-178">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="54b70-179">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="54b70-179">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="54b70-180">Razor Pages の応答は圧縮できます。</span><span class="sxs-lookup"><span data-stu-id="54b70-180">The Razor Pages responses can be compressed.</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. <span data-ttu-id="54b70-181">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="54b70-181">Exception/error handling</span></span>
   * <span data-ttu-id="54b70-182">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="54b70-182">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="54b70-183">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-183">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="54b70-184">データベース エラー ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) によりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-184">Database Error Page Middleware (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) reports database runtime errors.</span></span>
   * <span data-ttu-id="54b70-185">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="54b70-185">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="54b70-186">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-186">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="54b70-187">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-187">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="54b70-188">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-188">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="54b70-189">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-189">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="54b70-190">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="54b70-190">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="54b70-191">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-191">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="54b70-192">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-192">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="54b70-193">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="54b70-193">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="54b70-194">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) を使い、要求パイプラインに MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="54b70-194">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="54b70-195">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="54b70-195">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="54b70-196">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="54b70-196"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="54b70-197">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="54b70-197">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="54b70-198">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="54b70-198">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="54b70-199">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="54b70-199">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="54b70-200">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-200">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="54b70-201">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="54b70-201">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="54b70-202">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-202">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="54b70-203">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="54b70-203">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="54b70-204">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="54b70-204">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="54b70-205">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="54b70-205">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="54b70-206">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="54b70-206">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="54b70-207"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> からの MVC 応答を圧縮することができます。</span><span class="sxs-lookup"><span data-stu-id="54b70-207">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

## <a name="use-run-and-map"></a><span data-ttu-id="54b70-208">Use、Run、および Map</span><span class="sxs-lookup"><span data-stu-id="54b70-208">Use, Run, and Map</span></span>

<span data-ttu-id="54b70-209">HTTP パイプラインを構成するには、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> を使います。</span><span class="sxs-lookup"><span data-stu-id="54b70-209">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="54b70-210">`Use` メソッドは、パイプラインをショートさせることができます (つまり `next` 要求デリゲートを呼び出さない場合)。</span><span class="sxs-lookup"><span data-stu-id="54b70-210">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="54b70-211">`Run` は規則であり、一部のミドルウェア コンポーネントは、パイプラインの最後に実行される `Run[Middleware]` メソッドを公開することがあります。</span><span class="sxs-lookup"><span data-stu-id="54b70-211">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="54b70-212"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="54b70-212"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="54b70-213">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="54b70-213">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="54b70-214">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-214">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="54b70-215">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="54b70-215">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="54b70-216">要求</span><span class="sxs-lookup"><span data-stu-id="54b70-216">Request</span></span>             | <span data-ttu-id="54b70-217">応答</span><span class="sxs-lookup"><span data-stu-id="54b70-217">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="54b70-218">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="54b70-218">localhost:1234</span></span>      | <span data-ttu-id="54b70-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="54b70-219">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="54b70-220">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="54b70-220">localhost:1234/map1</span></span> | <span data-ttu-id="54b70-221">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="54b70-221">Map Test 1</span></span>                   |
| <span data-ttu-id="54b70-222">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="54b70-222">localhost:1234/map2</span></span> | <span data-ttu-id="54b70-223">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="54b70-223">Map Test 2</span></span>                   |
| <span data-ttu-id="54b70-224">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="54b70-224">localhost:1234/map3</span></span> | <span data-ttu-id="54b70-225">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="54b70-225">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="54b70-226">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-226">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="54b70-227"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="54b70-227"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="54b70-228">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="54b70-228">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="54b70-229">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="54b70-229">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="54b70-230">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="54b70-230">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="54b70-231">要求</span><span class="sxs-lookup"><span data-stu-id="54b70-231">Request</span></span>                       | <span data-ttu-id="54b70-232">応答</span><span class="sxs-lookup"><span data-stu-id="54b70-232">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="54b70-233">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="54b70-233">localhost:1234</span></span>                | <span data-ttu-id="54b70-234">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="54b70-234">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="54b70-235">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="54b70-235">localhost:1234/?branch=master</span></span> | <span data-ttu-id="54b70-236">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="54b70-236">Branch used = master</span></span>         |

<span data-ttu-id="54b70-237">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="54b70-237">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="54b70-238">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="54b70-238">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="54b70-239">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="54b70-239">Built-in middleware</span></span>

<span data-ttu-id="54b70-240">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="54b70-240">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="54b70-241">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="54b70-241">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="54b70-242">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="54b70-242">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="54b70-243">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="54b70-243">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="54b70-244">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="54b70-244">Middleware</span></span> | <span data-ttu-id="54b70-245">説明</span><span class="sxs-lookup"><span data-stu-id="54b70-245">Description</span></span> | <span data-ttu-id="54b70-246">注文</span><span class="sxs-lookup"><span data-stu-id="54b70-246">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="54b70-247">認証</span><span class="sxs-lookup"><span data-stu-id="54b70-247">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="54b70-248">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="54b70-248">Provides authentication support.</span></span> | <span data-ttu-id="54b70-249">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="54b70-249">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="54b70-250">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="54b70-250">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="54b70-251">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="54b70-251">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="54b70-252">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="54b70-252">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="54b70-253">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-253">Before middleware that issues cookies.</span></span> <span data-ttu-id="54b70-254">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="54b70-254">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="54b70-255">CORS</span><span class="sxs-lookup"><span data-stu-id="54b70-255">CORS</span></span>](xref:security/cors) | <span data-ttu-id="54b70-256">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="54b70-256">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="54b70-257">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-257">Before components that use CORS.</span></span> |
| [<span data-ttu-id="54b70-258">診断</span><span class="sxs-lookup"><span data-stu-id="54b70-258">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="54b70-259">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="54b70-259">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="54b70-260">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-260">Before components that generate errors.</span></span> <span data-ttu-id="54b70-261">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="54b70-261">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="54b70-262">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="54b70-262">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="54b70-263">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="54b70-263">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="54b70-264">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-264">Before components that consume the updated fields.</span></span> <span data-ttu-id="54b70-265">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="54b70-265">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="54b70-266">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="54b70-266">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="54b70-267">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="54b70-267">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="54b70-268">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="54b70-268">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="54b70-269">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="54b70-269">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="54b70-270">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="54b70-270">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="54b70-271">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-271">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="54b70-272">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="54b70-272">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="54b70-273">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="54b70-273">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="54b70-274">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-274">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="54b70-275">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="54b70-275">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="54b70-276">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="54b70-276">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="54b70-277">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="54b70-277">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="54b70-278">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="54b70-278">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="54b70-279">MVC</span><span class="sxs-lookup"><span data-stu-id="54b70-279">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="54b70-280">MVC/Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="54b70-280">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="54b70-281">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="54b70-281">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="54b70-282">OWIN</span><span class="sxs-lookup"><span data-stu-id="54b70-282">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="54b70-283">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="54b70-283">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="54b70-284">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="54b70-284">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="54b70-285">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="54b70-285">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="54b70-286">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="54b70-286">Provides support for caching responses.</span></span> | <span data-ttu-id="54b70-287">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-287">Before components that require caching.</span></span> |
| [<span data-ttu-id="54b70-288">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="54b70-288">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="54b70-289">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="54b70-289">Provides support for compressing responses.</span></span> | <span data-ttu-id="54b70-290">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-290">Before components that require compression.</span></span> |
| [<span data-ttu-id="54b70-291">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="54b70-291">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="54b70-292">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="54b70-292">Provides localization support.</span></span> | <span data-ttu-id="54b70-293">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-293">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="54b70-294">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="54b70-294">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="54b70-295">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="54b70-295">Defines and constrains request routes.</span></span> | <span data-ttu-id="54b70-296">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="54b70-296">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="54b70-297">セッション</span><span class="sxs-lookup"><span data-stu-id="54b70-297">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="54b70-298">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="54b70-298">Provides support for managing user sessions.</span></span> | <span data-ttu-id="54b70-299">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-299">Before components that require Session.</span></span> |
| [<span data-ttu-id="54b70-300">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="54b70-300">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="54b70-301">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="54b70-301">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="54b70-302">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="54b70-302">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="54b70-303">URL Rewrite (URL 書き換え)</span><span class="sxs-lookup"><span data-stu-id="54b70-303">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="54b70-304">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="54b70-304">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="54b70-305">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-305">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="54b70-306">WebSocket</span><span class="sxs-lookup"><span data-stu-id="54b70-306">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="54b70-307">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="54b70-307">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="54b70-308">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="54b70-308">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="54b70-309">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="54b70-309">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
