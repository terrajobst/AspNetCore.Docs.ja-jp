---
title: ASP.NET Core のミドルウェア
author: rick-anderson
description: ASP.NET Core のミドルウェアと要求パイプラインについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: d678f3d1f6ca10e486543a2965506236e4e61b82
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239850"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="e58d0-103">ASP.NET Core のミドルウェア</span><span class="sxs-lookup"><span data-stu-id="e58d0-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="e58d0-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e58d0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e58d0-105">ミドルウェアとは、要求と応答を処理するために、アプリのパイプラインに組み込まれたソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="e58d0-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="e58d0-106">各コンポーネントで次の処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-106">Each component:</span></span>

* <span data-ttu-id="e58d0-107">要求をパイプライン内の次のコンポーネントに渡すかどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="e58d0-108">パイプラインの次のコンポーネントの前または後に作業を実行できます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="e58d0-109">要求デリゲートは、要求パイプラインの構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="e58d0-110">要求デリゲートは、各 HTTP 要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="e58d0-111">要求デリゲートは、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> の各拡張メソッドを使って構成されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="e58d0-112">個々の要求デリゲートは、匿名メソッドとしてインラインで指定するか (インライン ミドルウェアと呼ばれます)、または再利用可能なクラスで定義することができます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="e58d0-113">これらの再利用可能なクラスとインラインの匿名メソッドは、"*ミドルウェア*" です。"*ミドルウェア コンポーネント*" とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="e58d0-114">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、パイプラインをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="e58d0-115">ショートサーキットしたミドルウェアは "*ターミナル ミドルウェア*" と呼ばれます。これによってさらなるミドルウェアによる要求の処理が回避されるためです。</span><span class="sxs-lookup"><span data-stu-id="e58d0-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="e58d0-116"><xref:migration/http-modules> では、ASP.NET Core と ASP.NET 4.x の要求パイプラインの違いについて説明し、その他のミドルウェア サンプルが提供されています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="e58d0-117">IApplicationBuilder を使用したミドルウェア パイプラインの作成</span><span class="sxs-lookup"><span data-stu-id="e58d0-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="e58d0-118">ASP.NET Core 要求パイプラインは、順番に呼び出される一連の要求デリゲートで構成されています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="e58d0-119">次の図は、その概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="e58d0-120">実行のスレッドは黒い矢印をたどります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-120">The thread of execution follows the black arrows.</span></span>

![要求の到着、3 つのミドルウェアによる処理、アプリからの応答の送信を示す要求処理パターン。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="e58d0-124">各デリゲートは、次のデリゲートの前と後に操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="e58d0-125">例外処理デリゲートは、パイプラインの後のステージで発生する例外をキャッチできるように、パイプラインの早い段階で呼び出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="e58d0-126">考えられる最も簡単な ASP.NET Core アプリは、1 つの要求デリゲートを設定してすべての要求を処理するものです。</span><span class="sxs-lookup"><span data-stu-id="e58d0-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="e58d0-127">この場合、実際の要求パイプラインは含まれません。</span><span class="sxs-lookup"><span data-stu-id="e58d0-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="e58d0-128">代わりに、すべての HTTP 要求に対応して単一の匿名関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="e58d0-129">最初の <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> デリゲートが、パイプラインを終了します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="e58d0-130">複数の要求デリゲートを <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> と一緒にチェーンします。</span><span class="sxs-lookup"><span data-stu-id="e58d0-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="e58d0-131">`next` パラメーターは、パイプラインの次のデリゲートを表します</span><span class="sxs-lookup"><span data-stu-id="e58d0-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="e58d0-132">*next* パラメーターを "*呼び出さない*" ことで、パイプラインをショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="e58d0-133">次の例で示すように、通常は、次のデリゲートの前と後の両方でアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="e58d0-134">デリゲートが次のデリゲートに要求を渡さない場合、これは "*要求パイプラインのショートサーキット*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="e58d0-135">不要な処理を回避するために、ショートさせることが望ましい場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="e58d0-136">たとえば、[静的ファイル ミドルウェア](xref:fundamentals/static-files)は、静的ファイルの要求を処理して残りのパイプラインをショートサーキットすることにより、"*ターミナル ミドルウェア*" として動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="e58d0-137">後続の処理を終了させるミドルウェアの前にパイプラインに追加されたミドルウェアでは、その `next.Invoke` ステートメントの後も引き続きコードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="e58d0-138">ただし、既に送信された応答に対する書き込みの試行については、次の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e58d0-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="e58d0-139">応答がクライアントに送信された後に、`next.Invoke` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="e58d0-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="e58d0-140">応答が開始した後で <xref:Microsoft.AspNetCore.Http.HttpResponse> を変更すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="e58d0-141">たとえば、ヘッダーや状態コードの設定などの変更があると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="e58d0-142">`next` を呼び出した後で応答本文に書き込むと、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="e58d0-143">プロトコル違反が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-143">May cause a protocol violation.</span></span> <span data-ttu-id="e58d0-144">たとえば、示されている `Content-Length` より多くを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="e58d0-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="e58d0-145">本文の形式が破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-145">May corrupt the body format.</span></span> <span data-ttu-id="e58d0-146">たとえば、CSS ファイルに HTML フッターを書き込んだ場合。</span><span class="sxs-lookup"><span data-stu-id="e58d0-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="e58d0-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> は、ヘッダーが送信されたかどうかや本文が書き込まれたかどうかを示すために役立つヒントです。</span><span class="sxs-lookup"><span data-stu-id="e58d0-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="e58d0-148">ミドルウェアの順序</span><span class="sxs-lookup"><span data-stu-id="e58d0-148">Middleware order</span></span>

<span data-ttu-id="e58d0-149">`Startup.Configure` メソッドでミドルウェア コンポーネントを追加する順序は、要求でミドルウェア コンポーネントが呼び出される順序および応答での逆の順序を定義します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="e58d0-150">この順序は、セキュリティ、パフォーマンス、および機能にとって**重要**です。</span><span class="sxs-lookup"><span data-stu-id="e58d0-150">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="e58d0-151">次の `Startup.Configure` メソッドでは、セキュリティ関連のミドルウェア コンポーネントが推奨される順序で追加されています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-151">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="e58d0-152">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-152">In the preceding code:</span></span>

* <span data-ttu-id="e58d0-153">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-153">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="e58d0-154">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-154">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="e58d0-155">たとえば、`UseCors`、`UseAuthentication`、`UseAuthorization` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-155">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="e58d0-156">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-156">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="e58d0-157">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="e58d0-157">Exception/error handling</span></span>
   * <span data-ttu-id="e58d0-158">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="e58d0-158">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="e58d0-159">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-159">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="e58d0-160">データベース エラー ページ ミドルウェアによりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-160">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="e58d0-161">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="e58d0-161">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="e58d0-162">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-162">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="e58d0-163">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-163">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="e58d0-164">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-164">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="e58d0-165">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-165">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="e58d0-166">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-166">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="e58d0-167">ルーティング ミドルウェア (`UseRouting`) により、要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-167">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="e58d0-168">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-168">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="e58d0-169">承認ミドルウェア (`UseAuthorization`) により、ユーザーがセキュリティで保護されたリソースにアクセスすることが承認されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-169">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="e58d0-170">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-170">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="e58d0-171">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-171">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="e58d0-172">エンドポイント ルーティング ミドルウェア (`MapRazorPages` を含む `UseEndpoints`) により、Razor Pages エンドポイントが要求パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-172">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="e58d0-173">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-173">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="e58d0-174">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="e58d0-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="e58d0-175">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-175">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="e58d0-176">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-176">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="e58d0-177">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="e58d0-177">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="e58d0-178">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-178">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="e58d0-179">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e58d0-179">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="e58d0-180">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-180">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="e58d0-181">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="e58d0-181">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="e58d0-182">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-182">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="e58d0-183">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-183">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="e58d0-184">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="e58d0-184">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="e58d0-185">Razor Pages の応答は圧縮できます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-185">The Razor Pages responses can be compressed.</span></span>

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

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="e58d0-186">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-186">In the preceding code:</span></span>

* <span data-ttu-id="e58d0-187">[個人のユーザー アカウント](xref:security/authentication/identity)を使用して新しい Web アプリを作成するときに追加されないミドルウェアは、コメント アウトされています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-187">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="e58d0-188">すべてのミドルウェアを厳密にこの順序で追加する必要があるわけではありませんが、多くはそうする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-188">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="e58d0-189">たとえば、`UseCors` と `UseAuthentication` は、示されている順序で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-189">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="e58d0-190">次の `Startup.Configure` メソッドにより、共通アプリ シナリオのためのミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-190">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="e58d0-191">例外/エラー処理</span><span class="sxs-lookup"><span data-stu-id="e58d0-191">Exception/error handling</span></span>
   * <span data-ttu-id="e58d0-192">開発環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="e58d0-192">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="e58d0-193">開発者例外ページ ミドルウェア (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) によりアプリの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-193">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="e58d0-194">データベース エラー ページ ミドルウェア (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) によりデータベースの実行時エラーが報告されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-194">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="e58d0-195">運用環境でアプリを実行する場合:</span><span class="sxs-lookup"><span data-stu-id="e58d0-195">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="e58d0-196">例外ハンドラー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) によって、後続のミドルウェアによってスローされた例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-196">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="e58d0-197">HTTP Strict Transport Security プロトコル (HSTS) ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) により `Strict-Transport-Security` ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-197">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="e58d0-198">HTTPS リダイレクト ミドルウェア (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) により、HTTP 要求が HTTPS にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-198">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="e58d0-199">静的ファイル ミドルウェア (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) によって静的ファイルが返され、さらなる要求の処理がスキップされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-199">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="e58d0-200">Cookie ポリシー ミドルウェア (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) により、アプリを EU の一般データ保護規制 (GDPR) に準拠させます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-200">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="e58d0-201">認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) により、ユーザーがセキュリティで保護されたリソースにアクセスする前に、ユーザーの認証が試行されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-201">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="e58d0-202">セッション ミドルウェア (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) により、セッション状態が確立され保持されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-202">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="e58d0-203">アプリでセッション状態が使用されている場合は、Cookie ポリシー ミドルウェアの後、MVC ミドルウェアの前に、セッション ミドルウェアを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-203">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="e58d0-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) を使い、要求パイプラインに MVC を追加します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="e58d0-205">前のコード例では、各ミドルウェアの拡張メソッドが <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 名前空間を通じて <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で公開されています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-205">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="e58d0-206">パイプラインに追加された最初のミドルウェア コンポーネントは <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> です。</span><span class="sxs-lookup"><span data-stu-id="e58d0-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="e58d0-207">そのため、例外ハンドラー ミドルウェアでは、以降の呼び出しで発生するすべての例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-207">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="e58d0-208">静的ファイル ミドルウェアはパイプラインの早い段階で呼び出されるので、要求を処理し、残りのコンポーネントを通過せずにショートさせることができます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-208">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="e58d0-209">静的ファイル ミドルウェアでは、承認チェックは提供**されません**。</span><span class="sxs-lookup"><span data-stu-id="e58d0-209">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="e58d0-210">*wwwroot* の下にあるものも含め、この静的ファイル ミドルウェアによって提供されるすべてのファイルは、一般に公開されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-210">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="e58d0-211">静的ファイルを保護する方法については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e58d0-211">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="e58d0-212">要求が静的ファイル ミドルウェアによって処理されない場合、要求は認証を実行する認証ミドルウェア (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) に渡されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-212">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="e58d0-213">認証は、認証されない要求をショートさせません。</span><span class="sxs-lookup"><span data-stu-id="e58d0-213">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="e58d0-214">認証ミドルウェアは要求を認証しますが、承認 (および却下) は、MVC が特定の Razor ページまたは MVC コントローラーとアクションを選んだ後でのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-214">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="e58d0-215">次の例は、静的ファイルの要求が応答圧縮ミドルウェアの前に静的ファイル ミドルウェアによって処理される、ミドルウェアの順序を示します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-215">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="e58d0-216">静的ファイルは、このミドルウェアの順序では圧縮されません。</span><span class="sxs-lookup"><span data-stu-id="e58d0-216">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="e58d0-217"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> からの MVC 応答を圧縮することができます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-217">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

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

## <a name="use-run-and-map"></a><span data-ttu-id="e58d0-218">Use、Run、および Map</span><span class="sxs-lookup"><span data-stu-id="e58d0-218">Use, Run, and Map</span></span>

<span data-ttu-id="e58d0-219">HTTP パイプラインを構成するには、<xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>、<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> を使います。</span><span class="sxs-lookup"><span data-stu-id="e58d0-219">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="e58d0-220">`Use` メソッドは、パイプラインをショートさせることができます (つまり `next` 要求デリゲートを呼び出さない場合)。</span><span class="sxs-lookup"><span data-stu-id="e58d0-220">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="e58d0-221">`Run` は規則であり、一部のミドルウェア コンポーネントは、パイプラインの最後に実行される `Run[Middleware]` メソッドを公開することがあります。</span><span class="sxs-lookup"><span data-stu-id="e58d0-221">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="e58d0-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 拡張メソッドは、パイプラインを分岐する規則として使われます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="e58d0-223">`Map` は、指定された要求パスの一致に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-223">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="e58d0-224">要求パスが指定されたパスで開始する場合、分岐が実行されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-224">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="e58d0-225">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-225">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="e58d0-226">要求</span><span class="sxs-lookup"><span data-stu-id="e58d0-226">Request</span></span>             | <span data-ttu-id="e58d0-227">応答</span><span class="sxs-lookup"><span data-stu-id="e58d0-227">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="e58d0-228">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="e58d0-228">localhost:1234</span></span>      | <span data-ttu-id="e58d0-229">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="e58d0-229">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="e58d0-230">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="e58d0-230">localhost:1234/map1</span></span> | <span data-ttu-id="e58d0-231">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="e58d0-231">Map Test 1</span></span>                   |
| <span data-ttu-id="e58d0-232">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="e58d0-232">localhost:1234/map2</span></span> | <span data-ttu-id="e58d0-233">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="e58d0-233">Map Test 2</span></span>                   |
| <span data-ttu-id="e58d0-234">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="e58d0-234">localhost:1234/map3</span></span> | <span data-ttu-id="e58d0-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="e58d0-235">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="e58d0-236">`Map` を使用すると、一致したパス セグメントが `HttpRequest.Path` から削除され、要求ごとに `HttpRequest.PathBase` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-236">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="e58d0-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> は、指定された述語の結果に基づいて、要求パイプラインを分岐します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="e58d0-238">`Func<HttpContext, bool>` という型の任意の述語を使って、要求をパイプラインの新しい分岐にマップできます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-238">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="e58d0-239">次の例では、クエリ文字列変数 `branch` の存在を検出するために術後が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-239">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="e58d0-240">次の表は、前のコードを使用した `http://localhost:1234` からの要求および応答を示しています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-240">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="e58d0-241">要求</span><span class="sxs-lookup"><span data-stu-id="e58d0-241">Request</span></span>                       | <span data-ttu-id="e58d0-242">応答</span><span class="sxs-lookup"><span data-stu-id="e58d0-242">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="e58d0-243">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="e58d0-243">localhost:1234</span></span>                | <span data-ttu-id="e58d0-244">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="e58d0-244">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="e58d0-245">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="e58d0-245">localhost:1234/?branch=master</span></span> | <span data-ttu-id="e58d0-246">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="e58d0-246">Branch used = master</span></span>         |

<span data-ttu-id="e58d0-247">`Map` は入れ子をサポートします。次にその例を示します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-247">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="e58d0-248">`Map` では、次のように一度に複数のセグメントを照合できます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-248">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="e58d0-249">組み込みミドルウェア</span><span class="sxs-lookup"><span data-stu-id="e58d0-249">Built-in middleware</span></span>

<span data-ttu-id="e58d0-250">ASP.NET Core には、次のミドルウェア コンポーネントが付属しています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-250">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="e58d0-251">"*順番*" 列には、要求を処理するパイプライン内のミドルウェアの配置と、ミドルウェアが要求処理を終了する条件に関するメモが記載されています。</span><span class="sxs-lookup"><span data-stu-id="e58d0-251">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="e58d0-252">ミドルウェアが要求を処理するパイプラインをショートサーキットし、下流のさらなるミドルウェアによる要求の処理を回避する場合、これは "*ターミナル ミドルウェア*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e58d0-252">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="e58d0-253">ショートサーキットについて詳しくは、「[IApplicationBuilder を使用したミドルウェア パイプラインの作成](#create-a-middleware-pipeline-with-iapplicationbuilder)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e58d0-253">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="e58d0-254">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="e58d0-254">Middleware</span></span> | <span data-ttu-id="e58d0-255">説明</span><span class="sxs-lookup"><span data-stu-id="e58d0-255">Description</span></span> | <span data-ttu-id="e58d0-256">注文</span><span class="sxs-lookup"><span data-stu-id="e58d0-256">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="e58d0-257">認証</span><span class="sxs-lookup"><span data-stu-id="e58d0-257">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="e58d0-258">認証のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-258">Provides authentication support.</span></span> | <span data-ttu-id="e58d0-259">`HttpContext.User` が必要な場所の前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-259">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="e58d0-260">OAuth コールバックの終端。</span><span class="sxs-lookup"><span data-stu-id="e58d0-260">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="e58d0-261">Cookie のポリシー</span><span class="sxs-lookup"><span data-stu-id="e58d0-261">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="e58d0-262">個人情報の保存に関してユーザーからの同意を追跡し、`secure` や `SameSite` など、Cookie フィールドの最小要件を適用します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-262">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="e58d0-263">Cookie を発行するミドルウェアの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-263">Before middleware that issues cookies.</span></span> <span data-ttu-id="e58d0-264">次に例を示します。 認証、セッション、MVC (TempData)</span><span class="sxs-lookup"><span data-stu-id="e58d0-264">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="e58d0-265">CORS</span><span class="sxs-lookup"><span data-stu-id="e58d0-265">CORS</span></span>](xref:security/cors) | <span data-ttu-id="e58d0-266">クロス オリジン リソース共有を構成します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-266">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="e58d0-267">CORS を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-267">Before components that use CORS.</span></span> |
| [<span data-ttu-id="e58d0-268">診断</span><span class="sxs-lookup"><span data-stu-id="e58d0-268">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="e58d0-269">開発者の例外ページ、例外処理、状態コード ページ、および新しいアプリの既定の Web ページを提供する複数の独立したミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="e58d0-269">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="e58d0-270">エラーを生成するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-270">Before components that generate errors.</span></span> <span data-ttu-id="e58d0-271">例外または新しいアプリ用の既定の Web ページの提供の終端。</span><span class="sxs-lookup"><span data-stu-id="e58d0-271">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="e58d0-272">転送されるヘッダー</span><span class="sxs-lookup"><span data-stu-id="e58d0-272">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="e58d0-273">プロキシされたヘッダーを現在の要求に転送します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-273">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="e58d0-274">更新されたフィールドを使用するコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-274">Before components that consume the updated fields.</span></span> <span data-ttu-id="e58d0-275">例: スキーム、ホスト、クライアント IP、メソッド。</span><span class="sxs-lookup"><span data-stu-id="e58d0-275">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="e58d0-276">正常性チェック</span><span class="sxs-lookup"><span data-stu-id="e58d0-276">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="e58d0-277">ASP.NET Core アプリとその依存関係の正常性を、データベースの可用性などで確認します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-277">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="e58d0-278">要求が正常性チェックのエンドポイントと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="e58d0-278">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="e58d0-279">HTTP メソッドのオーバーライド</span><span class="sxs-lookup"><span data-stu-id="e58d0-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="e58d0-280">メソッドをオーバーライドする受信 POST 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="e58d0-281">更新されたメソッドを使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="e58d0-282">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="e58d0-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="e58d0-283">すべての HTTP 要求を HTTPS にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="e58d0-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="e58d0-284">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="e58d0-285">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="e58d0-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="e58d0-286">特殊な応答ヘッダーを追加するセキュリティ拡張機能のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="e58d0-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="e58d0-287">応答が送信される前と要求を変更するコンポーネントの後。</span><span class="sxs-lookup"><span data-stu-id="e58d0-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="e58d0-288">次に例を示します。 転送されるヘッダー、URL リライト。</span><span class="sxs-lookup"><span data-stu-id="e58d0-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="e58d0-289">MVC</span><span class="sxs-lookup"><span data-stu-id="e58d0-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="e58d0-290">MVC/Razor Pages で要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="e58d0-291">要求がルートと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="e58d0-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="e58d0-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="e58d0-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="e58d0-293">OWIN ベースのアプリ、サーバー、およびミドルウェアと相互運用します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="e58d0-294">OWIN ミドルウェアが要求を完全に処理した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="e58d0-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="e58d0-295">応答キャッシュ</span><span class="sxs-lookup"><span data-stu-id="e58d0-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="e58d0-296">応答のキャッシュのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-296">Provides support for caching responses.</span></span> | <span data-ttu-id="e58d0-297">キャッシュが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-297">Before components that require caching.</span></span> |
| [<span data-ttu-id="e58d0-298">応答圧縮</span><span class="sxs-lookup"><span data-stu-id="e58d0-298">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="e58d0-299">応答の圧縮のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-299">Provides support for compressing responses.</span></span> | <span data-ttu-id="e58d0-300">圧縮が必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-300">Before components that require compression.</span></span> |
| [<span data-ttu-id="e58d0-301">要求のローカライズ</span><span class="sxs-lookup"><span data-stu-id="e58d0-301">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="e58d0-302">ローカライズのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-302">Provides localization support.</span></span> | <span data-ttu-id="e58d0-303">ローカリゼーションが重要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-303">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="e58d0-304">エンドポイント ルーティング</span><span class="sxs-lookup"><span data-stu-id="e58d0-304">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="e58d0-305">要求のルートを定義および制約します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-305">Defines and constrains request routes.</span></span> | <span data-ttu-id="e58d0-306">一致するルートの終端。</span><span class="sxs-lookup"><span data-stu-id="e58d0-306">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="e58d0-307">セッション</span><span class="sxs-lookup"><span data-stu-id="e58d0-307">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="e58d0-308">ユーザー セッションの管理のサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-308">Provides support for managing user sessions.</span></span> | <span data-ttu-id="e58d0-309">セッションが必要なコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-309">Before components that require Session.</span></span> |
| [<span data-ttu-id="e58d0-310">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="e58d0-310">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="e58d0-311">静的ファイルとディレクトリ参照に対応するサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-311">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="e58d0-312">要求がファイルと一致した場合の終端。</span><span class="sxs-lookup"><span data-stu-id="e58d0-312">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="e58d0-313">URL Rewrite (URL 書き換え)</span><span class="sxs-lookup"><span data-stu-id="e58d0-313">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="e58d0-314">URL の書き換えと要求のリダイレクトのサポートを提供します。</span><span class="sxs-lookup"><span data-stu-id="e58d0-314">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="e58d0-315">URL を使うコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-315">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="e58d0-316">WebSocket</span><span class="sxs-lookup"><span data-stu-id="e58d0-316">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="e58d0-317">WebSocket プロトコルを有効にします。</span><span class="sxs-lookup"><span data-stu-id="e58d0-317">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="e58d0-318">WebSocket 要求を受け入れる必要があるコンポーネントの前。</span><span class="sxs-lookup"><span data-stu-id="e58d0-318">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="e58d0-319">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e58d0-319">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
