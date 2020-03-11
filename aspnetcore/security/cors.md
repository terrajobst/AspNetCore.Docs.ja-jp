---
title: ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする
author: rick-anderson
description: ASP.NET Core アプリでのクロスオリジン要求を許可または拒否するための標準として CORS を使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e0e0e1abf1ecaa12038b3ee1bdaa384d979be254
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654446"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="09b71-103">ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする</span><span class="sxs-lookup"><span data-stu-id="09b71-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="09b71-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="09b71-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="09b71-105">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="09b71-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="09b71-106">ブラウザーのセキュリティは、web ページがその web ページを提供するものとは異なるドメインに要求を行うことを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="09b71-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="09b71-107">この制限は、*同じオリジンポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="09b71-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="09b71-108">同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="09b71-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="09b71-109">場合によっては、他のサイトからアプリへのクロス オリジン要求を許可する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="09b71-110">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="09b71-111">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="09b71-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="09b71-112">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="09b71-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="09b71-113">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="09b71-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="09b71-114">CORS を許可しても、API は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="09b71-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="09b71-115">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="09b71-116">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="09b71-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="09b71-117">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="09b71-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="09b71-118">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="09b71-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="09b71-119">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="09b71-119">Same origin</span></span>

<span data-ttu-id="09b71-120">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="09b71-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="09b71-121">次の 2 つの URL は生成元が同じです。</span><span class="sxs-lookup"><span data-stu-id="09b71-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="09b71-122">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="09b71-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="09b71-123">別のドメイン &ndash; `https://example.net`</span><span class="sxs-lookup"><span data-stu-id="09b71-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="09b71-124">`https://www.example.com/foo.html` &ndash; 異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="09b71-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="09b71-125">異なるスキーム &ndash; `http://example.com/foo.html`</span><span class="sxs-lookup"><span data-stu-id="09b71-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="09b71-126">別のポート &ndash; `https://example.com:9000/foo.html`</span><span class="sxs-lookup"><span data-stu-id="09b71-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="09b71-127">Internet Explorer は、生成元を比較するときにポートを考慮しません。</span><span class="sxs-lookup"><span data-stu-id="09b71-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="09b71-128">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="09b71-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="09b71-129">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="09b71-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="09b71-130">次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="09b71-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="09b71-131">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="09b71-131">The preceding code:</span></span>

* <span data-ttu-id="09b71-132">ポリシー名を "\_Myallow固有のオリジン" に設定します。</span><span class="sxs-lookup"><span data-stu-id="09b71-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="09b71-133">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="09b71-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="09b71-134"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 拡張メソッドを呼び出します。これにより CORS が有効になります。</span><span class="sxs-lookup"><span data-stu-id="09b71-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="09b71-135">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="09b71-136">ラムダは、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> オブジェクトを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="09b71-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="09b71-137">`WithOrigins`などの[構成オプション](#cors-policy-options)については、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="09b71-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="09b71-138"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> メソッド呼び出しは、アプリのサービスコンテナーに CORS サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="09b71-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="09b71-139">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="09b71-140"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> メソッドは、次のコードに示すようにメソッドを連結できます。</span><span class="sxs-lookup"><span data-stu-id="09b71-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="09b71-141">注: URL の末尾にスラッシュ (`/`) を含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="09b71-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="09b71-142">URL が `/`で終了した場合、比較は `false` を返し、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="09b71-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="09b71-143">CORS ポリシーをすべてのエンドポイントに適用する</span><span class="sxs-lookup"><span data-stu-id="09b71-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="09b71-144">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="09b71-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> <span data-ttu-id="09b71-145">エンドポイントルーティングを使用する場合は、`UseRouting` と `UseEndpoints`の呼び出しの間で実行されるように CORS ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="09b71-146">構成が正しくないと、ミドルウェアが正常に機能しなくなります。</span><span class="sxs-lookup"><span data-stu-id="09b71-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="09b71-147">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="09b71-147">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="09b71-148">注: `UseMvc`する前に `UseCors` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-148">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="09b71-149">ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[、Razor Pages、コントローラー、アクションメソッドで Cors を有効](#ecors)にする方法に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-149">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="09b71-150">前のコードをテストする手順については、「[テスト CORS](#test) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-150">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="09b71-151">エンドポイントルーティングで Cors を有効にする</span><span class="sxs-lookup"><span data-stu-id="09b71-151">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="09b71-152">エンドポイントルーティングでは、`RequireCors` の拡張メソッドのセットを使用して、エンドポイントごとに CORS を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="09b71-152">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="09b71-153">同様に、すべてのコントローラーに対して CORS を有効にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="09b71-153">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="09b71-154">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="09b71-154">Enable CORS with attributes</span></span>

<span data-ttu-id="09b71-155">[&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="09b71-155">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="09b71-156">`[EnableCors]` 属性は、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="09b71-156">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="09b71-157">`[EnableCors]` を使用して、既定のポリシーを指定し、`[EnableCors("{Policy String}")]` ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="09b71-157">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="09b71-158">`[EnableCors]` 属性は、次のように適用できます。</span><span class="sxs-lookup"><span data-stu-id="09b71-158">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="09b71-159">Razor ページ `PageModel`</span><span class="sxs-lookup"><span data-stu-id="09b71-159">Razor Page `PageModel`</span></span>
* <span data-ttu-id="09b71-160">コントローラー</span><span class="sxs-lookup"><span data-stu-id="09b71-160">Controller</span></span>
* <span data-ttu-id="09b71-161">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="09b71-161">Controller action method</span></span>

<span data-ttu-id="09b71-162">`[EnableCors]` 属性を使用して、コントローラー/ページモデル/アクションに異なるポリシーを適用できます。</span><span class="sxs-lookup"><span data-stu-id="09b71-162">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="09b71-163">`[EnableCors]` 属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、両方のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-163">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="09b71-164">ポリシーを組み合わせることはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="09b71-164">We recommend against combining policies.</span></span> <span data-ttu-id="09b71-165">同じアプリ内ではなく、`[EnableCors]` の属性またはミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="09b71-165">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="09b71-166">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="09b71-166">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="09b71-167">次のコードでは、CORS の既定のポリシーと `"AnotherPolicy"`という名前のポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="09b71-167">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="09b71-168">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="09b71-168">Disable CORS</span></span>

<span data-ttu-id="09b71-169">[&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="09b71-169">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="09b71-170">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="09b71-170">CORS policy options</span></span>

<span data-ttu-id="09b71-171">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="09b71-171">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="09b71-172">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="09b71-172">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="09b71-173">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="09b71-173">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="09b71-174">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="09b71-174">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="09b71-175">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="09b71-175">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="09b71-176">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="09b71-176">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="09b71-177">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="09b71-177">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="09b71-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> は `Startup.ConfigureServices`で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="09b71-179">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-179">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="09b71-180">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="09b71-180">Set the allowed origins</span></span>

<span data-ttu-id="09b71-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; では、任意のスキーム (`http` または `https`) を使用して、すべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="09b71-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="09b71-182">*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、`AllowAnyOrigin` は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="09b71-182">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="09b71-183">`AllowAnyOrigin` と `AllowCredentials` の指定は安全ではない構成であり、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-183">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="09b71-184">アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="09b71-184">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="09b71-185">`AllowAnyOrigin` と `AllowCredentials` の指定は安全ではない構成であり、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-185">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="09b71-186">セキュリティで保護されたアプリの場合は、クライアントがサーバーリソースへのアクセスを承認する必要がある場合は、オリジンの正確な一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="09b71-186">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="09b71-187">`AllowAnyOrigin` は、プレフライト要求と `Access-Control-Allow-Origin` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="09b71-187">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="09b71-188">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="09b71-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; は、ポリシーの <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> プロパティを、オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインと一致させることができるようにする関数に設定されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="09b71-190">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="09b71-190">Set the allowed HTTP methods</span></span>

<span data-ttu-id="09b71-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="09b71-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="09b71-192">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="09b71-192">Allows any HTTP method:</span></span>
* <span data-ttu-id="09b71-193">プレフライト要求と `Access-Control-Allow-Methods` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="09b71-193">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="09b71-194">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-194">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="09b71-195">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="09b71-195">Set the allowed request headers</span></span>

<span data-ttu-id="09b71-196">CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれますが、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> を呼び出し、許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="09b71-196">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="09b71-197">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-197">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="09b71-198">この設定は、プレフライト要求と `Access-Control-Request-Headers` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="09b71-198">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="09b71-199">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-199">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="09b71-200">`WithHeaders` によって指定された特定のヘッダーに対して CORS ミドルウェアポリシーが一致するのは、`Access-Control-Request-Headers` で送信されたヘッダーが `WithHeaders`に記載されているヘッダーと完全に一致する場合のみです。</span><span class="sxs-lookup"><span data-stu-id="09b71-200">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="09b71-201">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="09b71-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="09b71-202">`Content-Language` ([Headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) が `WithHeaders`に一覧表示されていないため、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求を拒否します。</span><span class="sxs-lookup"><span data-stu-id="09b71-202">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="09b71-203">アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="09b71-203">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="09b71-204">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="09b71-204">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="09b71-205">CORS ミドルウェアでは、CorsPolicy で構成されている値に関係なく、常に `Access-Control-Request-Headers` の4つのヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="09b71-205">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="09b71-206">このヘッダーの一覧には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="09b71-206">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="09b71-207">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="09b71-207">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="09b71-208">`Content-Language` が常にホワイトリストに登録されているため、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求に正常に応答します。</span><span class="sxs-lookup"><span data-stu-id="09b71-208">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="09b71-209">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="09b71-209">Set the exposed response headers</span></span>

<span data-ttu-id="09b71-210">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="09b71-210">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="09b71-211">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-211">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="09b71-212">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="09b71-212">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="09b71-213">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-213">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="09b71-214">アプリで他のヘッダーを使用できるようにするには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-214">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="09b71-215">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="09b71-215">Credentials in cross-origin requests</span></span>

<span data-ttu-id="09b71-216">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-216">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="09b71-217">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="09b71-217">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="09b71-218">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="09b71-218">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="09b71-219">クロスオリジン要求で資格情報を送信するには、クライアントで `XMLHttpRequest.withCredentials` を `true`に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-219">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="09b71-220">`XMLHttpRequest` を直接使用する:</span><span class="sxs-lookup"><span data-stu-id="09b71-220">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="09b71-221">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="09b71-221">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="09b71-222">[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="09b71-222">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="09b71-223">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-223">The server must allow the credentials.</span></span> <span data-ttu-id="09b71-224">クロスオリジンの資格情報を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-224">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="09b71-225">HTTP 応答には `Access-Control-Allow-Credentials` ヘッダーが含まれています。これにより、サーバーがクロスオリジン要求の資格情報を許可していることがブラウザーに伝えられます。</span><span class="sxs-lookup"><span data-stu-id="09b71-225">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="09b71-226">ブラウザーが資格情報を送信しても、応答に有効な `Access-Control-Allow-Credentials` ヘッダーが含まれていない場合、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="09b71-226">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="09b71-227">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="09b71-227">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="09b71-228">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="09b71-228">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="09b71-229">また、CORS 仕様では、`Access-Control-Allow-Credentials` ヘッダーが存在する場合、[オリジン] を `"*"` (すべてのオリジン) に設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="09b71-229">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="09b71-230">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="09b71-230">Preflight requests</span></span>

<span data-ttu-id="09b71-231">一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="09b71-231">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="09b71-232">この要求は*プレフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="09b71-232">This request is called a *preflight request*.</span></span> <span data-ttu-id="09b71-233">次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="09b71-233">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="09b71-234">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="09b71-234">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="09b71-235">アプリで `Accept`、`Accept-Language`、`Content-Language`、`Content-Type`、または `Last-Event-ID`以外の要求ヘッダーが設定されていません。</span><span class="sxs-lookup"><span data-stu-id="09b71-235">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="09b71-236">`Content-Type` ヘッダーには、次のいずれかの値が設定されています。</span><span class="sxs-lookup"><span data-stu-id="09b71-236">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="09b71-237">クライアント要求に対して設定された要求ヘッダーに適用される規則は、`XMLHttpRequest` オブジェクトの `setRequestHeader` を呼び出すことによってアプリが設定するヘッダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-237">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="09b71-238">CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-238">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="09b71-239">このルールは、ブラウザーが設定できるヘッダー (`User-Agent`、`Host`、`Content-Length`など) には適用されません。</span><span class="sxs-lookup"><span data-stu-id="09b71-239">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="09b71-240">プレフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="09b71-240">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="09b71-241">事前フライト要求では、HTTP OPTIONS メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="09b71-241">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="09b71-242">次の2つの特殊なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="09b71-242">It includes two special headers:</span></span>

* <span data-ttu-id="09b71-243">`Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="09b71-243">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="09b71-244">`Access-Control-Request-Headers`: アプリが実際の要求に対して設定する要求ヘッダーの一覧。</span><span class="sxs-lookup"><span data-stu-id="09b71-244">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="09b71-245">前述のように、これには、`User-Agent`など、ブラウザーによって設定されるヘッダーは含まれません。</span><span class="sxs-lookup"><span data-stu-id="09b71-245">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="09b71-246">CORS のプレフライト要求には、`Access-Control-Request-Headers` ヘッダーが含まれる場合があります。これは、実際の要求と共に送信されるヘッダーをサーバーに示します。</span><span class="sxs-lookup"><span data-stu-id="09b71-246">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="09b71-247">特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-247">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="09b71-248">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-248">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="09b71-249">ブラウザーの `Access-Control-Request-Headers`の設定方法が完全に一致しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="09b71-249">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="09b71-250">ヘッダーを `"*"` 以外に設定する (または <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>を使用する) 場合は、少なくとも `Accept`、`Content-Type`、および `Origin`と、サポートするカスタムヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-250">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="09b71-251">プレフライト要求に対する応答の例を次に示します (サーバーが要求を許可していることを前提としています)。</span><span class="sxs-lookup"><span data-stu-id="09b71-251">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="09b71-252">応答には、許可されたメソッドと、必要に応じて `Access-Control-Allow-Headers` ヘッダーを一覧表示する `Access-Control-Allow-Methods` ヘッダーが含まれます。これには、許可されているヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-252">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="09b71-253">プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="09b71-253">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="09b71-254">プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="09b71-254">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="09b71-255">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="09b71-255">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="09b71-256">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="09b71-256">Set the preflight expiration time</span></span>

<span data-ttu-id="09b71-257">`Access-Control-Max-Age` ヘッダーは、プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="09b71-257">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="09b71-258">このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09b71-258">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="09b71-259">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="09b71-259">How CORS works</span></span>

<span data-ttu-id="09b71-260">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="09b71-260">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="09b71-261">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="09b71-261">CORS is **not** a security feature.</span></span> <span data-ttu-id="09b71-262">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="09b71-262">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="09b71-263">たとえば、悪意のあるアクターがを使用してサイトに対して[クロスサイトスクリプティング (XSS) を防止](xref:security/cross-site-scripting)し、CORS が有効になっているサイトに対してクロスサイト要求を実行して情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="09b71-263">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="09b71-264">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="09b71-264">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="09b71-265">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="09b71-265">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="09b71-266">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="09b71-266">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="09b71-267">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-267">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="09b71-268">Fiddler</span><span class="sxs-lookup"><span data-stu-id="09b71-268">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="09b71-269">Postman</span><span class="sxs-lookup"><span data-stu-id="09b71-269">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="09b71-270">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="09b71-270">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="09b71-271">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="09b71-271">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="09b71-272">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="09b71-272">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="09b71-273">(CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="09b71-273">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="09b71-274">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="09b71-274">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="09b71-275">JSONP は XHR を使用しないので、`<script>` タグを使用して応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="09b71-275">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="09b71-276">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="09b71-276">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="09b71-277">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="09b71-277">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="09b71-278">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-278">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="09b71-279">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="09b71-279">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="09b71-280">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="09b71-280">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="09b71-281">`Origin` ヘッダーは、要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="09b71-281">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="09b71-282">`Origin` ヘッダーは必須であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-282">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="09b71-283">サーバーが要求を許可している場合は、応答に `Access-Control-Allow-Origin` ヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-283">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="09b71-284">このヘッダーの値は、要求の `Origin` ヘッダーと一致しているか、または `"*"`ワイルドカードの値であることを意味します。つまり、すべての配信元が許可されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-284">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="09b71-285">応答に `Access-Control-Allow-Origin` ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="09b71-285">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="09b71-286">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="09b71-286">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="09b71-287">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="09b71-287">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="09b71-288">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="09b71-288">Test CORS</span></span>

<span data-ttu-id="09b71-289">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="09b71-289">To test CORS:</span></span>

1. <span data-ttu-id="09b71-290">[API プロジェクトを作成](xref:tutorials/first-web-api)します。</span><span class="sxs-lookup"><span data-stu-id="09b71-290">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="09b71-291">または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="09b71-291">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="09b71-292">このドキュメントのいずれかの方法を使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="09b71-292">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="09b71-293">例 :</span><span class="sxs-lookup"><span data-stu-id="09b71-293">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="09b71-294">`WithOrigins("https://localhost:<port>");` は、サンプル[コードのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)と同様のサンプルアプリのテストにのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="09b71-294">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="09b71-295">Web アプリプロジェクト (Razor Pages または MVC) を作成します。</span><span class="sxs-lookup"><span data-stu-id="09b71-295">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="09b71-296">このサンプルでは、Razor Pages を使用します。</span><span class="sxs-lookup"><span data-stu-id="09b71-296">The sample uses Razor Pages.</span></span> <span data-ttu-id="09b71-297">API プロジェクトと同じソリューションに web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="09b71-297">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="09b71-298">次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="09b71-298">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="09b71-299">上記のコードでは、`url: 'https://<web app>.azurewebsites.net/api/values/1',` を、デプロイされたアプリの URL に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="09b71-299">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="09b71-300">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="09b71-300">Deploy the API project.</span></span> <span data-ttu-id="09b71-301">たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。</span><span class="sxs-lookup"><span data-stu-id="09b71-301">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="09b71-302">デスクトップから Razor Pages または MVC アプリを実行し、 **[テスト]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="09b71-302">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="09b71-303">F12 ツールを使用して、エラーメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="09b71-303">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="09b71-304">`WithOrigins` から localhost の発信元を削除し、アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="09b71-304">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="09b71-305">または、別のポートを使用してクライアントアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="09b71-305">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="09b71-306">たとえば、Visual Studio からを実行します。</span><span class="sxs-lookup"><span data-stu-id="09b71-306">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="09b71-307">クライアントアプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="09b71-307">Test with the client app.</span></span> <span data-ttu-id="09b71-308">CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。</span><span class="sxs-lookup"><span data-stu-id="09b71-308">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="09b71-309">F12 ツールの [コンソール] タブを使用して、エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="09b71-309">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="09b71-310">ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="09b71-310">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="09b71-311">Microsoft Edge を使用する:</span><span class="sxs-lookup"><span data-stu-id="09b71-311">Using Microsoft Edge:</span></span>

     <span data-ttu-id="09b71-312">**SEC7120: [CORS] `https://webapi.azurewebsites.net/api/values/1` のクロスオリジンリソースのアクセス制御-`https://localhost:44375` 応答ヘッダーに、元の `https://localhost:44375` が見つかりませんでした**</span><span class="sxs-lookup"><span data-stu-id="09b71-312">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="09b71-313">Chrome を使用する:</span><span class="sxs-lookup"><span data-stu-id="09b71-313">Using Chrome:</span></span>

     <span data-ttu-id="09b71-314">**オリジン `https://localhost:44375` からの `https://webapi.azurewebsites.net/api/values/1` での XMLHttpRequest へのアクセスが、CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御-発信元 ' ヘッダーは存在しません。**</span><span class="sxs-lookup"><span data-stu-id="09b71-314">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="09b71-315">CORS が有効なエンドポイントは、 [Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="09b71-315">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="09b71-316">ツールを使用する場合は、`Origin` ヘッダーによって指定された要求の配信元が、要求を受信しているホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-316">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="09b71-317">要求が `Origin` ヘッダーの値に基づいて*クロスオリジンで*ない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="09b71-317">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="09b71-318">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="09b71-318">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="09b71-319">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="09b71-319">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="09b71-320">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="09b71-320">CORS in IIS</span></span>

<span data-ttu-id="09b71-321">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-321">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="09b71-322">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="09b71-322">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09b71-323">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="09b71-323">Additional resources</span></span>

* [<span data-ttu-id="09b71-324">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="09b71-324">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="09b71-325">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="09b71-325">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)
