---
title: ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする
author: rick-anderson
description: ASP.NET Core アプリでのクロスオリジン要求を許可または拒否するための標準として CORS を使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: a02b3497684979c1a9e792437f9f1a4c467600f0
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187254"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="5a049-103">ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする</span><span class="sxs-lookup"><span data-stu-id="5a049-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="5a049-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a049-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5a049-105">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5a049-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="5a049-106">ブラウザーのセキュリティは、web ページがその web ページを提供するものとは異なるドメインに要求を行うことを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="5a049-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="5a049-107">この制限は、*同一オリジン ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="5a049-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="5a049-108">同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="5a049-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="5a049-109">場合によっては、他のサイトからアプリへのクロス オリジン要求を許可する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="5a049-110">詳細については、[Mozilla CORS の記事](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="5a049-111">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="5a049-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="5a049-112">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="5a049-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="5a049-113">CORS はセキュリティ機能では **なく**、セキュリティを緩和するものです。</span><span class="sxs-lookup"><span data-stu-id="5a049-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="5a049-114">CORS を許可しても、API は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="5a049-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="5a049-115">詳細については、 [CORS の仕組み](#how-cors) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="5a049-116">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="5a049-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="5a049-117">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="5a049-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="5a049-118">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5a049-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="5a049-119">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="5a049-119">Same origin</span></span>

<span data-ttu-id="5a049-120">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="5a049-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="5a049-121">次の 2 つの URL は生成元が同じです。</span><span class="sxs-lookup"><span data-stu-id="5a049-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="5a049-122">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="5a049-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="5a049-123">`https://example.net`&ndash;異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="5a049-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="5a049-124">`https://www.example.com/foo.html`&ndash;異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="5a049-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="5a049-125">`http://example.com/foo.html`&ndash;異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="5a049-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="5a049-126">`https://example.com:9000/foo.html`&ndash;別のポート</span><span class="sxs-lookup"><span data-stu-id="5a049-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="5a049-127">Internet Explorer は、生成元を比較するときにポートを考慮しません。</span><span class="sxs-lookup"><span data-stu-id="5a049-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="5a049-128">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="5a049-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="5a049-129">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="5a049-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="5a049-130">次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="5a049-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="5a049-131">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="5a049-131">The preceding code:</span></span>

* <span data-ttu-id="5a049-132">ポリシー名を "\_myallow固有のオリジン" に設定します。</span><span class="sxs-lookup"><span data-stu-id="5a049-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="5a049-133">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="5a049-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="5a049-134">CORS を有効にする拡張メソッドを呼び出します。<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*></span><span class="sxs-lookup"><span data-stu-id="5a049-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="5a049-135"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="5a049-136">ラムダは、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> オブジェクトをとります。</span><span class="sxs-lookup"><span data-stu-id="5a049-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="5a049-137">などの[構成オプション](#cors-policy-options)に`WithOrigins`ついては、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="5a049-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="5a049-138">メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しによって、アプリのサービスコンテナーに CORS サービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="5a049-139">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="5a049-140">メソッド<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>は、次のコードに示すようにメソッドを連結できます。</span><span class="sxs-lookup"><span data-stu-id="5a049-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="5a049-141">メモ:URL の末尾にスラッシュ (`/`) を含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="5a049-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="5a049-142">URL がで`/`終了した場合、比較`false`はを返し、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="5a049-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a049-143">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="5a049-143">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
> <span data-ttu-id="5a049-144">エンドポイントルーティングでは、CORS ミドルウェアを`UseRouting`と`UseEndpoints`の呼び出しの間で実行するように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-144">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="5a049-145">構成が正しくないと、ミドルウェアが正常に機能しなくなります。</span><span class="sxs-lookup"><span data-stu-id="5a049-145">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="5a049-146">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="5a049-146">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="5a049-147">メモ: `UseCors`の前に`UseMvc`を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-147">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="5a049-148">ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[、Razor Pages、コントローラー、アクションメソッドで Cors を有効](#ecors)にする方法に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-148">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="5a049-149">前のコードをテストする手順については、「[テスト CORS](#test) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-149">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="5a049-150">エンドポイントルーティングで Cors を有効にする</span><span class="sxs-lookup"><span data-stu-id="5a049-150">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="5a049-151">エンドポイントルーティングでは、 `RequireCors`一連の拡張メソッドを使用して、CORS をエンドポイントごとに有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="5a049-151">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="5a049-152">同様に、すべてのコントローラーに対して CORS を有効にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="5a049-152">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="5a049-153">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="5a049-153">Enable CORS with attributes</span></span>

<span data-ttu-id="5a049-154">[ &lbrack;Enablecors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用するための代替手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="5a049-154">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="5a049-155">属性`[EnableCors]`を指定すると、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS が有効になります。</span><span class="sxs-lookup"><span data-stu-id="5a049-155">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="5a049-156">を`[EnableCors]`使用して、既定の`[EnableCors("{Policy String}")]`ポリシーを指定し、ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="5a049-156">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="5a049-157">属性`[EnableCors]`は次のものに適用できます。</span><span class="sxs-lookup"><span data-stu-id="5a049-157">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="5a049-158">Razor ページ`PageModel`</span><span class="sxs-lookup"><span data-stu-id="5a049-158">Razor Page `PageModel`</span></span>
* <span data-ttu-id="5a049-159">コントローラー</span><span class="sxs-lookup"><span data-stu-id="5a049-159">Controller</span></span>
* <span data-ttu-id="5a049-160">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="5a049-160">Controller action method</span></span>

<span data-ttu-id="5a049-161">属性を使用して、 `[EnableCors]`コントローラー/ページモデル/アクションに異なるポリシーを適用できます。</span><span class="sxs-lookup"><span data-stu-id="5a049-161">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="5a049-162">`[EnableCors]`属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、両方のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-162">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="5a049-163">ポリシーを組み合わせることはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="5a049-163">We recommend against combining policies.</span></span> <span data-ttu-id="5a049-164">同じアプリ内ではなく、属性またはミドルウェアを使用します。`[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="5a049-164">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="5a049-165">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="5a049-165">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="5a049-166">次のコードでは、CORS の既定のポリシーと`"AnotherPolicy"`という名前のポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="5a049-166">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="5a049-167">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="5a049-167">Disable CORS</span></span>

<span data-ttu-id="5a049-168">[ &lbrack;Disablecors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="5a049-168">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="5a049-169">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="5a049-169">CORS policy options</span></span>

<span data-ttu-id="5a049-170">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="5a049-170">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="5a049-171">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="5a049-171">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="5a049-172">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="5a049-172">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="5a049-173">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="5a049-173">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="5a049-174">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="5a049-174">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="5a049-175">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="5a049-175">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="5a049-176">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="5a049-176">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="5a049-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>はで`Startup.ConfigureServices`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5a049-178">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-178">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="5a049-179">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="5a049-179">Set the allowed origins</span></span>

<span data-ttu-id="5a049-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>任意のスキーム (`http`または`https`) を持つすべてのオリジンからの CORS 要求を許可します。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="5a049-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="5a049-181">`AllowAnyOrigin`*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="5a049-181">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="5a049-182">`AllowAnyOrigin` と`AllowCredentials`を指定すると、安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-182">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="5a049-183">アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="5a049-183">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="5a049-184">`AllowAnyOrigin` と`AllowCredentials`を指定すると、安全ではない構成で、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-184">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="5a049-185">セキュリティで保護されたアプリの場合は、クライアントがサーバーリソースへのアクセスを承認する必要がある場合は、オリジンの正確な一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="5a049-185">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="5a049-186">`AllowAnyOrigin`プレフライト要求とヘッダー `Access-Control-Allow-Origin`に影響します。</span><span class="sxs-lookup"><span data-stu-id="5a049-186">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="5a049-187">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-187">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="5a049-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; ポリシーのプロパティを、オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインと一致させることができるようにする関数に<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>設定します。</span><span class="sxs-lookup"><span data-stu-id="5a049-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="5a049-189">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="5a049-189">Set the allowed HTTP methods</span></span>

<span data-ttu-id="5a049-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="5a049-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="5a049-191">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="5a049-191">Allows any HTTP method:</span></span>
* <span data-ttu-id="5a049-192">プレフライト要求とヘッダー `Access-Control-Allow-Methods`に影響します。</span><span class="sxs-lookup"><span data-stu-id="5a049-192">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="5a049-193">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="5a049-194">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="5a049-194">Set the allowed request headers</span></span>

<span data-ttu-id="5a049-195">CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれるを呼び出し<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 、許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="5a049-195">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="5a049-196">すべての作成者要求ヘッダーを許可<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-196">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="5a049-197">この設定は、プレフライト要求`Access-Control-Request-Headers`とヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="5a049-197">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="5a049-198">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a049-198">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5a049-199">によって指定された特定のヘッダー `WithHeaders`に対して CORS ミドルウェアポリシーが一致`Access-Control-Request-Headers`するのは、ヘッダーが`WithHeaders`で記述されているヘッダーと完全に一致する場合のみです。</span><span class="sxs-lookup"><span data-stu-id="5a049-199">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="5a049-200">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="5a049-200">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="5a049-201">CORS ミドルウェアは、次の要求ヘッダーを使用して`Content-Language`プレフライト要求を拒否します。 ([headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) は、に`WithHeaders`は記載されていないためです。</span><span class="sxs-lookup"><span data-stu-id="5a049-201">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="5a049-202">アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="5a049-202">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="5a049-203">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="5a049-203">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5a049-204">CORS ミドルウェアでは、CorsPolicy で構成`Access-Control-Request-Headers`されている値に関係なく、常にの4つのヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="5a049-204">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="5a049-205">このヘッダーの一覧には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a049-205">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="5a049-206">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="5a049-206">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="5a049-207">は常にホワイトリストに登録されているため`Content-Language` 、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求に正常に応答します。</span><span class="sxs-lookup"><span data-stu-id="5a049-207">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="5a049-208">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="5a049-208">Set the exposed response headers</span></span>

<span data-ttu-id="5a049-209">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="5a049-209">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="5a049-210">詳細については[、次を参照してください。 W3C クロスオリジンリソース共有 (用語):単純な応答](https://www.w3.org/TR/cors/#simple-response-header)ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="5a049-210">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="5a049-211">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5a049-211">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="5a049-212">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-212">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="5a049-213">アプリで他のヘッダーを使用できるように<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-213">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="5a049-214">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="5a049-214">Credentials in cross-origin requests</span></span>

<span data-ttu-id="5a049-215">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-215">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="5a049-216">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="5a049-216">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="5a049-217">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a049-217">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="5a049-218">クロスオリジン要求で資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials`に`true`設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-218">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="5a049-219">直接`XMLHttpRequest`の使用:</span><span class="sxs-lookup"><span data-stu-id="5a049-219">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="5a049-220">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="5a049-220">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="5a049-221">[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="5a049-221">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="5a049-222">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-222">The server must allow the credentials.</span></span> <span data-ttu-id="5a049-223">クロスオリジンの資格情報を許可する<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>には、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-223">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="5a049-224">HTTP 応答には、 `Access-Control-Allow-Credentials`サーバーがクロスオリジン要求に対して資格情報を許可することをブラウザーに示すヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5a049-224">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="5a049-225">ブラウザーが資格情報を送信しても、応答に`Access-Control-Allow-Credentials`有効なヘッダーが含まれていない場合、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5a049-225">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="5a049-226">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="5a049-226">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="5a049-227">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="5a049-227">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="5a049-228">また、CORS 仕様では、 `"*"` `Access-Control-Allow-Credentials`ヘッダーが存在する場合、[オリジン] を [(すべてのオリジン)] に設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="5a049-228">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="5a049-229">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="5a049-229">Preflight requests</span></span>

<span data-ttu-id="5a049-230">一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="5a049-230">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="5a049-231">この要求は*プレフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="5a049-231">This request is called a *preflight request*.</span></span> <span data-ttu-id="5a049-232">次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="5a049-232">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="5a049-233">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="5a049-233">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="5a049-234">`Accept`アプリでは`Content-Type`、 `Accept-Language`、、 `Last-Event-ID`、、または以外の要求ヘッダーは設定されません。 `Content-Language`</span><span class="sxs-lookup"><span data-stu-id="5a049-234">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="5a049-235">ヘッダー `Content-Type`には、設定されている場合、次のいずれかの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a049-235">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="5a049-236">クライアント要求に対して設定された要求ヘッダーに適用される規則は、 `setRequestHeader` `XMLHttpRequest`オブジェクトに対してを呼び出すことによって、アプリが設定するヘッダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-236">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="5a049-237">CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-237">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="5a049-238">このルールは`User-Agent`、ブラウザーが設定できるヘッダー (、 `Host` `Content-Length`、など) には適用されません。</span><span class="sxs-lookup"><span data-stu-id="5a049-238">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="5a049-239">プレフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5a049-239">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="5a049-240">事前フライト要求では、HTTP OPTIONS メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a049-240">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="5a049-241">次の2つの特殊なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a049-241">It includes two special headers:</span></span>

* <span data-ttu-id="5a049-242">`Access-Control-Request-Method`:実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="5a049-242">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="5a049-243">`Access-Control-Request-Headers`:アプリが実際の要求に設定する要求ヘッダーの一覧。</span><span class="sxs-lookup"><span data-stu-id="5a049-243">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="5a049-244">前述のように、これにはブラウザーが設定するヘッダー (など`User-Agent`) は含まれません。</span><span class="sxs-lookup"><span data-stu-id="5a049-244">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="5a049-245">CORS のプレフライト要求には`Access-Control-Request-Headers` 、実際の要求と共に送信されるヘッダーをサーバーに示すヘッダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="5a049-245">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="5a049-246">特定のヘッダーを許可する<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>には、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-246">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="5a049-247">すべての作成者要求ヘッダーを許可<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>するには、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-247">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="5a049-248">ブラウザーの設定`Access-Control-Request-Headers`方法が完全に一致しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="5a049-248">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="5a049-249">`"*"`ヘッダーを以外の任意の値 (またはを<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>使用) に設定する場合は`Accept`、 `Content-Type`少なくと`Origin`も、、、、およびサポートするカスタムヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a049-249">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="5a049-250">プレフライト要求に対する応答の例を次に示します (サーバーが要求を許可していることを前提としています)。</span><span class="sxs-lookup"><span data-stu-id="5a049-250">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="5a049-251">応答には、 `Access-Control-Allow-Methods`許可されているメソッドと、 `Access-Control-Allow-Headers`必要に応じてヘッダーを一覧表示するヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5a049-251">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="5a049-252">プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="5a049-252">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="5a049-253">プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="5a049-253">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="5a049-254">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="5a049-254">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="5a049-255">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="5a049-255">Set the preflight expiration time</span></span>

<span data-ttu-id="5a049-256">ヘッダー `Access-Control-Max-Age`は、プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="5a049-256">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="5a049-257">このヘッダーを設定するに<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>は、次のように呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a049-257">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="5a049-258">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="5a049-258">How CORS works</span></span>

<span data-ttu-id="5a049-259">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="5a049-259">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="5a049-260">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="5a049-260">CORS is **not** a security feature.</span></span> <span data-ttu-id="5a049-261">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="5a049-261">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="5a049-262">たとえば、悪意のあるアクターがを使用してサイトに対して[クロスサイトスクリプティング (XSS) を防止](xref:security/cross-site-scripting)し、CORS が有効になっているサイトに対してクロスサイト要求を実行して情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="5a049-262">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="5a049-263">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="5a049-263">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="5a049-264">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="5a049-264">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="5a049-265">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="5a049-265">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="5a049-266">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-266">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="5a049-267">Fiddler</span><span class="sxs-lookup"><span data-stu-id="5a049-267">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="5a049-268">Postman</span><span class="sxs-lookup"><span data-stu-id="5a049-268">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="5a049-269">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="5a049-269">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="5a049-270">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="5a049-270">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="5a049-271">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="5a049-271">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="5a049-272">(CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="5a049-272">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="5a049-273">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="5a049-273">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="5a049-274">JSONP は xhr を使用しないの`<script>`で、タグを使用して応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="5a049-274">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="5a049-275">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="5a049-275">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="5a049-276">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="5a049-276">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="5a049-277">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-277">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="5a049-278">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5a049-278">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="5a049-279">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a049-279">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="5a049-280">ヘッダー `Origin`は、要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="5a049-280">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="5a049-281">サーバーが要求を許可している場合は`Access-Control-Allow-Origin` 、応答でヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-281">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="5a049-282">このヘッダーの値は、要求の`Origin`ヘッダーと一致するか、またはワイルド`"*"`カード値です。つまり、すべての配信元が許可されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-282">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="5a049-283">応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5a049-283">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="5a049-284">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="5a049-284">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="5a049-285">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="5a049-285">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="5a049-286">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="5a049-286">Test CORS</span></span>

<span data-ttu-id="5a049-287">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="5a049-287">To test CORS:</span></span>

1. <span data-ttu-id="5a049-288">[API プロジェクトを作成](xref:tutorials/first-web-api)します。</span><span class="sxs-lookup"><span data-stu-id="5a049-288">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="5a049-289">または、[サンプルをダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="5a049-289">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="5a049-290">このドキュメントのいずれかの方法を使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="5a049-290">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="5a049-291">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a049-291">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="5a049-292">`WithOrigins("https://localhost:<port>");`サンプルアプリのテストにのみ使用してください[サンプルコードをダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)します。</span><span class="sxs-lookup"><span data-stu-id="5a049-292">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="5a049-293">Web アプリプロジェクト (Razor Pages または MVC) を作成します。</span><span class="sxs-lookup"><span data-stu-id="5a049-293">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="5a049-294">このサンプルでは、Razor Pages を使用します。</span><span class="sxs-lookup"><span data-stu-id="5a049-294">The sample uses Razor Pages.</span></span> <span data-ttu-id="5a049-295">API プロジェクトと同じソリューションに web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="5a049-295">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="5a049-296">次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="5a049-296">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="5a049-297">前のコードで、を`url: 'https://<web app>.azurewebsites.net/api/values/1',`デプロイされたアプリの URL に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5a049-297">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="5a049-298">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="5a049-298">Deploy the API project.</span></span> <span data-ttu-id="5a049-299">たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。</span><span class="sxs-lookup"><span data-stu-id="5a049-299">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="5a049-300">デスクトップから Razor Pages または MVC アプリを実行し、 **[テスト]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="5a049-300">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="5a049-301">F12 ツールを使用して、エラーメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="5a049-301">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="5a049-302">から`WithOrigins` localhost の配信元を削除し、アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="5a049-302">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="5a049-303">または、別のポートを使用してクライアントアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5a049-303">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="5a049-304">たとえば、Visual Studio からを実行します。</span><span class="sxs-lookup"><span data-stu-id="5a049-304">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="5a049-305">クライアントアプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="5a049-305">Test with the client app.</span></span> <span data-ttu-id="5a049-306">CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。</span><span class="sxs-lookup"><span data-stu-id="5a049-306">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="5a049-307">F12 ツールの [コンソール] タブを使用して、エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="5a049-307">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="5a049-308">ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5a049-308">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="5a049-309">Microsoft Edge を使用する:</span><span class="sxs-lookup"><span data-stu-id="5a049-309">Using Microsoft Edge:</span></span>

     <span data-ttu-id="5a049-310">**SEC7120: [CORS] オリジン`https://localhost:44375`は、次の場所にあるクロスオリジンリソースのアクセス制御-許可-オリジン応答ヘッダーで見つかり`https://localhost:44375`ませんでした`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="5a049-310">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="5a049-311">Chrome を使用する:</span><span class="sxs-lookup"><span data-stu-id="5a049-311">Using Chrome:</span></span>

     <span data-ttu-id="5a049-312">**`https://webapi.azurewebsites.net/api/values/1` オリジン`https://localhost:44375`からの XMLHttpRequest へのアクセスが CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御-許可元 ' ヘッダーが存在しません。**</span><span class="sxs-lookup"><span data-stu-id="5a049-312">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a049-313">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5a049-313">Additional resources</span></span>

* [<span data-ttu-id="5a049-314">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="5a049-314">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
