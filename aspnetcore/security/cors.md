---
title: ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする
author: rick-anderson
description: ASP.NET Core アプリでのクロスオリジン要求を許可または拒否するための標準として CORS を使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 09cc296ebf605907371619124cac00883beb6abb
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511575"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="12976-103">ASP.NET Core でのクロスオリジン要求 (CORS) を有効にする</span><span class="sxs-lookup"><span data-stu-id="12976-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="12976-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="12976-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="12976-105">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="12976-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="12976-106">ブラウザーのセキュリティは、web ページがその web ページを提供するものとは異なるドメインに要求を行うことを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="12976-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="12976-107">この制限は、*同じオリジンポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="12976-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="12976-108">同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="12976-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="12976-109">場合によっては、他のサイトからアプリへのクロス オリジン要求を許可する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="12976-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="12976-110">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="12976-111">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="12976-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="12976-112">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="12976-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="12976-113">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="12976-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="12976-114">CORS を許可しても、API は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="12976-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="12976-115">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="12976-116">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="12976-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="12976-117">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="12976-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="12976-118">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="12976-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="12976-119">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="12976-119">Same origin</span></span>

<span data-ttu-id="12976-120">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="12976-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="12976-121">次の 2 つの URL は生成元が同じです。</span><span class="sxs-lookup"><span data-stu-id="12976-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="12976-122">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="12976-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="12976-123">別のドメイン &ndash; `https://example.net`</span><span class="sxs-lookup"><span data-stu-id="12976-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="12976-124">`https://www.example.com/foo.html` &ndash; 異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="12976-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="12976-125">異なるスキーム &ndash; `http://example.com/foo.html`</span><span class="sxs-lookup"><span data-stu-id="12976-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="12976-126">別のポート &ndash; `https://example.com:9000/foo.html`</span><span class="sxs-lookup"><span data-stu-id="12976-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="12976-127">Internet Explorer は、生成元を比較するときにポートを考慮しません。</span><span class="sxs-lookup"><span data-stu-id="12976-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="12976-128">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="12976-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="12976-129">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="12976-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="12976-130">次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="12976-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="12976-131">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="12976-131">The preceding code:</span></span>

* <span data-ttu-id="12976-132">ポリシー名を "\_Myallow固有のオリジン" に設定します。</span><span class="sxs-lookup"><span data-stu-id="12976-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="12976-133">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="12976-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="12976-134"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 拡張メソッドを呼び出します。これにより CORS が有効になります。</span><span class="sxs-lookup"><span data-stu-id="12976-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="12976-135">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="12976-136">ラムダは、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> オブジェクトを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="12976-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="12976-137">`WithOrigins`などの[構成オプション](#cors-policy-options)については、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="12976-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="12976-138"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> メソッド呼び出しは、アプリのサービスコンテナーに CORS サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="12976-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="12976-139">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="12976-140"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> メソッドは、次のコードに示すようにメソッドを連結できます。</span><span class="sxs-lookup"><span data-stu-id="12976-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="12976-141">注: URL の末尾にスラッシュ (`/`) を含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="12976-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="12976-142">URL が `/`で終了した場合、比較は `false` を返し、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="12976-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="12976-143">CORS ポリシーをすべてのエンドポイントに適用する</span><span class="sxs-lookup"><span data-stu-id="12976-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="12976-144">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="12976-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code omitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code omitted.
}
```

> [!WARNING]
> <span data-ttu-id="12976-145">エンドポイントルーティングを使用する場合は、`UseRouting` と `UseEndpoints`の呼び出しの間で実行されるように CORS ミドルウェアを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="12976-146">構成が正しくないと、ミドルウェアが正常に機能しなくなります。</span><span class="sxs-lookup"><span data-stu-id="12976-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

<span data-ttu-id="12976-147">ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[、Razor Pages、コントローラー、アクションメソッドで Cors を有効](#ecors)にする方法に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-147">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="12976-148">前のコードをテストする手順については、「[テスト CORS](#test) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-148">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="12976-149">エンドポイントルーティングで Cors を有効にする</span><span class="sxs-lookup"><span data-stu-id="12976-149">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="12976-150">エンドポイントルーティングでは、`RequireCors` の拡張メソッドのセットを使用して、エンドポイントごとに CORS を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="12976-150">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="12976-151">同様に、すべてのコントローラーに対して CORS を有効にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="12976-151">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="12976-152">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="12976-152">Enable CORS with attributes</span></span>

<span data-ttu-id="12976-153">[&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="12976-153">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="12976-154">`[EnableCors]` 属性は、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="12976-154">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="12976-155">`[EnableCors]` を使用して、既定のポリシーを指定し、`[EnableCors("{Policy String}")]` ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="12976-155">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="12976-156">`[EnableCors]` 属性は、次のように適用できます。</span><span class="sxs-lookup"><span data-stu-id="12976-156">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="12976-157">Razor ページ `PageModel`</span><span class="sxs-lookup"><span data-stu-id="12976-157">Razor Page `PageModel`</span></span>
* <span data-ttu-id="12976-158">コントローラー</span><span class="sxs-lookup"><span data-stu-id="12976-158">Controller</span></span>
* <span data-ttu-id="12976-159">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="12976-159">Controller action method</span></span>

<span data-ttu-id="12976-160">`[EnableCors]` 属性を使用して、コントローラー/ページモデル/アクションに異なるポリシーを適用できます。</span><span class="sxs-lookup"><span data-stu-id="12976-160">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="12976-161">`[EnableCors]` 属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、両方のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="12976-161">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="12976-162">ポリシーを組み合わせることはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="12976-162">We recommend against combining policies.</span></span> <span data-ttu-id="12976-163">同じアプリ内ではなく、`[EnableCors]` の属性またはミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="12976-163">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="12976-164">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="12976-164">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="12976-165">次のコードでは、CORS の既定のポリシーと `"AnotherPolicy"`という名前のポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="12976-165">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="12976-166">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="12976-166">Disable CORS</span></span>

<span data-ttu-id="12976-167">[&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="12976-167">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="12976-168">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="12976-168">CORS policy options</span></span>

<span data-ttu-id="12976-169">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="12976-169">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="12976-170">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="12976-170">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="12976-171">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-171">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="12976-172">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-172">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="12976-173">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-173">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="12976-174">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="12976-174">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="12976-175">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="12976-175">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="12976-176"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> は `Startup.ConfigureServices`で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="12976-176"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12976-177">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-177">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="12976-178">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="12976-178">Set the allowed origins</span></span>

<span data-ttu-id="12976-179"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; では、任意のスキーム (`http` または `https`) を使用して、すべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="12976-179"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="12976-180">*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、`AllowAnyOrigin` は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="12976-180">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="12976-181">`AllowAnyOrigin` と `AllowCredentials` の指定は安全ではない構成であり、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="12976-181">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="12976-182">アプリが両方の方法で構成されている場合、CORS サービスは無効な CORS 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="12976-182">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="12976-183">`AllowAnyOrigin` は、プレフライト要求と `Access-Control-Allow-Origin` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="12976-183">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="12976-184">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-184">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="12976-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; は、ポリシーの <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> プロパティを、オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインと一致させることができるようにする関数に設定されます。</span><span class="sxs-lookup"><span data-stu-id="12976-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="12976-186">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-186">Set the allowed HTTP methods</span></span>

<span data-ttu-id="12976-187"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="12976-187"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="12976-188">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="12976-188">Allows any HTTP method:</span></span>
* <span data-ttu-id="12976-189">プレフライト要求と `Access-Control-Allow-Methods` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="12976-189">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="12976-190">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-190">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="12976-191">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-191">Set the allowed request headers</span></span>

<span data-ttu-id="12976-192">CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれますが、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> を呼び出し、許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="12976-192">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="12976-193">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-193">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="12976-194">この設定は、プレフライト要求と `Access-Control-Request-Headers` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="12976-194">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="12976-195">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-195">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>


<span data-ttu-id="12976-196">`WithHeaders` によって指定された特定のヘッダーに対して CORS ミドルウェアポリシーが一致するのは、`Access-Control-Request-Headers` で送信されたヘッダーが `WithHeaders`に記載されているヘッダーと完全に一致する場合のみです。</span><span class="sxs-lookup"><span data-stu-id="12976-196">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="12976-197">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="12976-197">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="12976-198">`Content-Language` ([Headernames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) が `WithHeaders`に一覧表示されていないため、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求を拒否します。</span><span class="sxs-lookup"><span data-stu-id="12976-198">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="12976-199">アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="12976-199">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="12976-200">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="12976-200">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="12976-201">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-201">Set the exposed response headers</span></span>

<span data-ttu-id="12976-202">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="12976-202">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="12976-203">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-203">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="12976-204">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="12976-204">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="12976-205">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-205">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="12976-206">アプリで他のヘッダーを使用できるようにするには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-206">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="12976-207">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="12976-207">Credentials in cross-origin requests</span></span>

<span data-ttu-id="12976-208">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-208">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="12976-209">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="12976-209">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="12976-210">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="12976-210">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="12976-211">クロスオリジン要求で資格情報を送信するには、クライアントで `XMLHttpRequest.withCredentials` を `true`に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-211">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="12976-212">`XMLHttpRequest` を直接使用する:</span><span class="sxs-lookup"><span data-stu-id="12976-212">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="12976-213">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="12976-213">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="12976-214">[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="12976-214">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="12976-215">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-215">The server must allow the credentials.</span></span> <span data-ttu-id="12976-216">クロスオリジンの資格情報を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-216">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="12976-217">HTTP 応答には `Access-Control-Allow-Credentials` ヘッダーが含まれています。これにより、サーバーがクロスオリジン要求の資格情報を許可していることがブラウザーに伝えられます。</span><span class="sxs-lookup"><span data-stu-id="12976-217">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="12976-218">ブラウザーが資格情報を送信しても、応答に有効な `Access-Control-Allow-Credentials` ヘッダーが含まれていない場合、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="12976-218">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="12976-219">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="12976-219">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="12976-220">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="12976-220">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="12976-221">また、CORS 仕様では、`Access-Control-Allow-Credentials` ヘッダーが存在する場合、[オリジン] を `"*"` (すべてのオリジン) に設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="12976-221">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="12976-222">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="12976-222">Preflight requests</span></span>

<span data-ttu-id="12976-223">一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="12976-223">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="12976-224">この要求は*プレフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="12976-224">This request is called a *preflight request*.</span></span> <span data-ttu-id="12976-225">次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="12976-225">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="12976-226">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="12976-226">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="12976-227">アプリで `Accept`、`Accept-Language`、`Content-Language`、`Content-Type`、または `Last-Event-ID`以外の要求ヘッダーが設定されていません。</span><span class="sxs-lookup"><span data-stu-id="12976-227">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="12976-228">`Content-Type` ヘッダーには、次のいずれかの値が設定されています。</span><span class="sxs-lookup"><span data-stu-id="12976-228">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="12976-229">クライアント要求に対して設定された要求ヘッダーに適用される規則は、`XMLHttpRequest` オブジェクトの `setRequestHeader` を呼び出すことによってアプリが設定するヘッダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="12976-229">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="12976-230">CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-230">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="12976-231">このルールは、ブラウザーが設定できるヘッダー (`User-Agent`、`Host`、`Content-Length`など) には適用されません。</span><span class="sxs-lookup"><span data-stu-id="12976-231">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="12976-232">プレフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="12976-232">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="12976-233">事前フライト要求では、HTTP OPTIONS メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="12976-233">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="12976-234">次の2つの特殊なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="12976-234">It includes two special headers:</span></span>

* <span data-ttu-id="12976-235">`Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="12976-235">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="12976-236">`Access-Control-Request-Headers`: アプリが実際の要求に対して設定する要求ヘッダーの一覧。</span><span class="sxs-lookup"><span data-stu-id="12976-236">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="12976-237">前述のように、これには、`User-Agent`など、ブラウザーによって設定されるヘッダーは含まれません。</span><span class="sxs-lookup"><span data-stu-id="12976-237">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="12976-238">CORS のプレフライト要求には、`Access-Control-Request-Headers` ヘッダーが含まれる場合があります。これは、実際の要求と共に送信されるヘッダーをサーバーに示します。</span><span class="sxs-lookup"><span data-stu-id="12976-238">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="12976-239">特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-239">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="12976-240">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-240">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="12976-241">ブラウザーの `Access-Control-Request-Headers`の設定方法が完全に一致しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="12976-241">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="12976-242">ヘッダーを `"*"` 以外に設定する (または <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>を使用する) 場合は、少なくとも `Accept`、`Content-Type`、および `Origin`と、サポートするカスタムヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-242">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="12976-243">プレフライト要求に対する応答の例を次に示します (サーバーが要求を許可していることを前提としています)。</span><span class="sxs-lookup"><span data-stu-id="12976-243">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="12976-244">応答には、許可されたメソッドと、必要に応じて `Access-Control-Allow-Headers` ヘッダーを一覧表示する `Access-Control-Allow-Methods` ヘッダーが含まれます。これには、許可されているヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="12976-244">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="12976-245">プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="12976-245">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="12976-246">プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="12976-246">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="12976-247">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="12976-247">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="12976-248">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="12976-248">Set the preflight expiration time</span></span>

<span data-ttu-id="12976-249">`Access-Control-Max-Age` ヘッダーは、プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="12976-249">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="12976-250">このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-250">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="12976-251">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="12976-251">How CORS works</span></span>

<span data-ttu-id="12976-252">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="12976-252">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="12976-253">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="12976-253">CORS is **not** a security feature.</span></span> <span data-ttu-id="12976-254">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="12976-254">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="12976-255">たとえば、悪意のあるアクターがを使用してサイトに対して[クロスサイトスクリプティング (XSS) を防止](xref:security/cross-site-scripting)し、CORS が有効になっているサイトに対してクロスサイト要求を実行して情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="12976-255">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="12976-256">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="12976-256">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="12976-257">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="12976-257">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="12976-258">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="12976-258">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="12976-259">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="12976-259">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="12976-260">Fiddler</span><span class="sxs-lookup"><span data-stu-id="12976-260">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="12976-261">Postman</span><span class="sxs-lookup"><span data-stu-id="12976-261">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="12976-262">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="12976-262">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="12976-263">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="12976-263">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="12976-264">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="12976-264">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="12976-265">(CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="12976-265">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="12976-266">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="12976-266">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="12976-267">JSONP は XHR を使用しないので、`<script>` タグを使用して応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="12976-267">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="12976-268">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="12976-268">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="12976-269">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="12976-269">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="12976-270">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="12976-270">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="12976-271">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="12976-271">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="12976-272">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="12976-272">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="12976-273">`Origin` ヘッダーは、要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="12976-273">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="12976-274">`Origin` ヘッダーは必須であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-274">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="12976-275">サーバーが要求を許可している場合は、応答に `Access-Control-Allow-Origin` ヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="12976-275">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="12976-276">このヘッダーの値は、要求の `Origin` ヘッダーと一致しているか、または `"*"`ワイルドカードの値であることを意味します。つまり、すべての配信元が許可されます。</span><span class="sxs-lookup"><span data-stu-id="12976-276">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="12976-277">応答に `Access-Control-Allow-Origin` ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="12976-277">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="12976-278">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="12976-278">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="12976-279">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="12976-279">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="12976-280">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="12976-280">Test CORS</span></span>

<span data-ttu-id="12976-281">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="12976-281">To test CORS:</span></span>

1. <span data-ttu-id="12976-282">[API プロジェクトを作成](xref:tutorials/first-web-api)します。</span><span class="sxs-lookup"><span data-stu-id="12976-282">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="12976-283">または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="12976-283">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="12976-284">このドキュメントのいずれかの方法を使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="12976-284">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="12976-285">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="12976-285">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="12976-286">`WithOrigins("https://localhost:<port>");` は、サンプル[コードのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)と同様のサンプルアプリのテストにのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="12976-286">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="12976-287">Web アプリプロジェクト (Razor Pages または MVC) を作成します。</span><span class="sxs-lookup"><span data-stu-id="12976-287">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="12976-288">このサンプルでは、Razor Pages を使用します。</span><span class="sxs-lookup"><span data-stu-id="12976-288">The sample uses Razor Pages.</span></span> <span data-ttu-id="12976-289">API プロジェクトと同じソリューションに web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="12976-289">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="12976-290">次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="12976-290">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="12976-291">上記のコードでは、`url: 'https://<web app>.azurewebsites.net/api/values/1',` を、デプロイされたアプリの URL に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="12976-291">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="12976-292">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="12976-292">Deploy the API project.</span></span> <span data-ttu-id="12976-293">たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。</span><span class="sxs-lookup"><span data-stu-id="12976-293">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="12976-294">デスクトップから Razor Pages または MVC アプリを実行し、 **[テスト]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="12976-294">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="12976-295">F12 ツールを使用して、エラーメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="12976-295">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="12976-296">`WithOrigins` から localhost の発信元を削除し、アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="12976-296">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="12976-297">または、別のポートを使用してクライアントアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="12976-297">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="12976-298">たとえば、Visual Studio からを実行します。</span><span class="sxs-lookup"><span data-stu-id="12976-298">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="12976-299">クライアントアプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="12976-299">Test with the client app.</span></span> <span data-ttu-id="12976-300">CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。</span><span class="sxs-lookup"><span data-stu-id="12976-300">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="12976-301">F12 ツールの [コンソール] タブを使用して、エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="12976-301">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="12976-302">ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="12976-302">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="12976-303">Microsoft Edge を使用する:</span><span class="sxs-lookup"><span data-stu-id="12976-303">Using Microsoft Edge:</span></span>

     <span data-ttu-id="12976-304">**SEC7120: [CORS] `https://webapi.azurewebsites.net/api/values/1` のクロスオリジンリソースのアクセス制御-`https://localhost:44375` 応答ヘッダーに、元の `https://localhost:44375` が見つかりませんでした**</span><span class="sxs-lookup"><span data-stu-id="12976-304">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="12976-305">Chrome を使用する:</span><span class="sxs-lookup"><span data-stu-id="12976-305">Using Chrome:</span></span>

     <span data-ttu-id="12976-306">**オリジン `https://localhost:44375` からの `https://webapi.azurewebsites.net/api/values/1` での XMLHttpRequest へのアクセスが、CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御-発信元 ' ヘッダーは存在しません。**</span><span class="sxs-lookup"><span data-stu-id="12976-306">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="12976-307">CORS が有効なエンドポイントは、 [Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="12976-307">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="12976-308">ツールを使用する場合は、`Origin` ヘッダーによって指定された要求の配信元が、要求を受信しているホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-308">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="12976-309">要求が `Origin` ヘッダーの値に基づいて*クロスオリジンで*ない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="12976-309">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="12976-310">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="12976-310">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="12976-311">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="12976-311">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="12976-312">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="12976-312">CORS in IIS</span></span>

<span data-ttu-id="12976-313">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-313">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="12976-314">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-314">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12976-315">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="12976-315">Additional resources</span></span>

* [<span data-ttu-id="12976-316">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="12976-316">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="12976-317">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="12976-317">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="12976-318">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="12976-318">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="12976-319">この記事では、ASP.NET Core アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="12976-319">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="12976-320">ブラウザーのセキュリティは、web ページがその web ページを提供するものとは異なるドメインに要求を行うことを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="12976-320">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="12976-321">この制限は、*同じオリジンポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="12976-321">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="12976-322">同一オリジン ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取ることを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="12976-322">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="12976-323">場合によっては、他のサイトからアプリへのクロス オリジン要求を許可する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="12976-323">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="12976-324">詳細については、「 [MOZILLA CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-324">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="12976-325">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="12976-325">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="12976-326">は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="12976-326">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="12976-327">は、CORS 緩和され security のセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="12976-327">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="12976-328">CORS を許可しても、API は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="12976-328">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="12976-329">詳細については、「 [CORS のしくみ](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-329">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="12976-330">サーバーが他のユーザーを拒否している間に、一部のクロスオリジン要求を明示的に許可することを許可します。</span><span class="sxs-lookup"><span data-stu-id="12976-330">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="12976-331">は、 [JSONP](/dotnet/framework/wcf/samples/jsonp)など、以前の手法よりも安全で柔軟性に優れています。</span><span class="sxs-lookup"><span data-stu-id="12976-331">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="12976-332">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="12976-332">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="12976-333">同じオリジン</span><span class="sxs-lookup"><span data-stu-id="12976-333">Same origin</span></span>

<span data-ttu-id="12976-334">同じスキーム、ホスト、およびポート ([RFC 6454](https://tools.ietf.org/html/rfc6454)) がある場合、2つの url のオリジンは同じになります。</span><span class="sxs-lookup"><span data-stu-id="12976-334">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="12976-335">次の 2 つの URL は生成元が同じです。</span><span class="sxs-lookup"><span data-stu-id="12976-335">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="12976-336">これらの Url には、前の2つの Url とは異なるオリジンがあります。</span><span class="sxs-lookup"><span data-stu-id="12976-336">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="12976-337">別のドメイン &ndash; `https://example.net`</span><span class="sxs-lookup"><span data-stu-id="12976-337">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="12976-338">`https://www.example.com/foo.html` &ndash; 異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="12976-338">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="12976-339">異なるスキーム &ndash; `http://example.com/foo.html`</span><span class="sxs-lookup"><span data-stu-id="12976-339">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="12976-340">別のポート &ndash; `https://example.com:9000/foo.html`</span><span class="sxs-lookup"><span data-stu-id="12976-340">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="12976-341">Internet Explorer は、生成元を比較するときにポートを考慮しません。</span><span class="sxs-lookup"><span data-stu-id="12976-341">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="12976-342">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="12976-342">CORS with named policy and middleware</span></span>

<span data-ttu-id="12976-343">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="12976-343">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="12976-344">次のコードでは、指定したオリジンのアプリ全体に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="12976-344">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="12976-345">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="12976-345">The preceding code:</span></span>

* <span data-ttu-id="12976-346">ポリシー名を "\_Myallow固有のオリジン" に設定します。</span><span class="sxs-lookup"><span data-stu-id="12976-346">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="12976-347">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="12976-347">The policy name is arbitrary.</span></span>
* <span data-ttu-id="12976-348"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 拡張メソッドを呼び出します。これにより CORS が有効になります。</span><span class="sxs-lookup"><span data-stu-id="12976-348">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="12976-349">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-349">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="12976-350">ラムダは、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> オブジェクトを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="12976-350">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="12976-351">`WithOrigins`などの[構成オプション](#cors-policy-options)については、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="12976-351">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="12976-352"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> メソッド呼び出しは、アプリのサービスコンテナーに CORS サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="12976-352">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="12976-353">詳細については、このドキュメントの「 [CORS ポリシーオプション](#cpo)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-353">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="12976-354"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> メソッドは、次のコードに示すようにメソッドを連結できます。</span><span class="sxs-lookup"><span data-stu-id="12976-354">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="12976-355">注: URL の末尾にスラッシュ (`/`) を含めることは**できません**。</span><span class="sxs-lookup"><span data-stu-id="12976-355">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="12976-356">URL が `/`で終了した場合、比較は `false` を返し、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="12976-356">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="12976-357">次のコードは、CORS ミドルウェアを使用してすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="12976-357">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="12976-358">注: `UseMvc`する前に `UseCors` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-358">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="12976-359">ページ/コントローラー/アクションレベルで CORS ポリシーを適用するには[、Razor Pages、コントローラー、アクションメソッドで Cors を有効](#ecors)にする方法に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-359">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="12976-360">前のコードをテストする手順については、「[テスト CORS](#test) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-360">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="12976-361">属性を使用して CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="12976-361">Enable CORS with attributes</span></span>

<span data-ttu-id="12976-362">[&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="12976-362">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="12976-363">`[EnableCors]` 属性は、すべてのエンドポイントではなく、選択したエンドポイントに対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="12976-363">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="12976-364">`[EnableCors]` を使用して、既定のポリシーを指定し、`[EnableCors("{Policy String}")]` ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="12976-364">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="12976-365">`[EnableCors]` 属性は、次のように適用できます。</span><span class="sxs-lookup"><span data-stu-id="12976-365">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="12976-366">Razor ページ `PageModel`</span><span class="sxs-lookup"><span data-stu-id="12976-366">Razor Page `PageModel`</span></span>
* <span data-ttu-id="12976-367">コントローラー</span><span class="sxs-lookup"><span data-stu-id="12976-367">Controller</span></span>
* <span data-ttu-id="12976-368">コントローラーアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="12976-368">Controller action method</span></span>

<span data-ttu-id="12976-369">`[EnableCors]` 属性を使用して、コントローラー/ページモデル/アクションに異なるポリシーを適用できます。</span><span class="sxs-lookup"><span data-stu-id="12976-369">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="12976-370">`[EnableCors]` 属性がコントローラー/ページモデル/アクションメソッドに適用され、CORS がミドルウェアで有効になっている場合、両方のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="12976-370">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="12976-371">ポリシーを組み合わせることはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="12976-371">We recommend against combining policies.</span></span> <span data-ttu-id="12976-372">同じアプリ内ではなく、`[EnableCors]` の属性またはミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="12976-372">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="12976-373">次のコードは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="12976-373">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="12976-374">次のコードでは、CORS の既定のポリシーと `"AnotherPolicy"`という名前のポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="12976-374">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="12976-375">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="12976-375">Disable CORS</span></span>

<span data-ttu-id="12976-376">[&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラー/ページモデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="12976-376">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="12976-377">CORS ポリシーのオプション</span><span class="sxs-lookup"><span data-stu-id="12976-377">CORS policy options</span></span>

<span data-ttu-id="12976-378">ここでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="12976-378">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="12976-379">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="12976-379">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="12976-380">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-380">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="12976-381">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-381">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="12976-382">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-382">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="12976-383">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="12976-383">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="12976-384">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="12976-384">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="12976-385"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> は `Startup.ConfigureServices`で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="12976-385"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="12976-386">いくつかのオプションについては、まず「 [CORS のしくみ](#how-cors)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-386">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="12976-387">許可されるオリジンの設定</span><span class="sxs-lookup"><span data-stu-id="12976-387">Set the allowed origins</span></span>

<span data-ttu-id="12976-388"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; では、任意のスキーム (`http` または `https`) を使用して、すべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="12976-388"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="12976-389">*web サイト*はアプリに対してクロスオリジン要求を行うことができるため、`AllowAnyOrigin` は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="12976-389">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="12976-390">`AllowAnyOrigin` と `AllowCredentials` の指定は安全ではない構成であり、クロスサイト要求の偽造が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="12976-390">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="12976-391">セキュリティで保護されたアプリの場合は、クライアントがサーバーリソースへのアクセスを承認する必要がある場合は、オリジンの正確な一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="12976-391">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="12976-392">`AllowAnyOrigin` は、プレフライト要求と `Access-Control-Allow-Origin` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="12976-392">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="12976-393">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-393">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="12976-394"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; は、ポリシーの <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> プロパティを、オリジンが許可されているかどうかを評価するときに、構成されたワイルドカードドメインと一致させることができるようにする関数に設定されます。</span><span class="sxs-lookup"><span data-stu-id="12976-394"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="12976-395">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-395">Set the allowed HTTP methods</span></span>

<span data-ttu-id="12976-396"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="12976-396"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="12976-397">すべての HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="12976-397">Allows any HTTP method:</span></span>
* <span data-ttu-id="12976-398">プレフライト要求と `Access-Control-Allow-Methods` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="12976-398">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="12976-399">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-399">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="12976-400">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-400">Set the allowed request headers</span></span>

<span data-ttu-id="12976-401">CORS 要求で特定のヘッダーを送信できるようにするには、 *author 要求ヘッダー*と呼ばれますが、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> を呼び出し、許可されているヘッダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="12976-401">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="12976-402">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-402">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="12976-403">この設定は、プレフライト要求と `Access-Control-Request-Headers` ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="12976-403">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="12976-404">詳細については、「[プレフライト要求](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-404">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="12976-405">CORS ミドルウェアでは、CorsPolicy で構成されている値に関係なく、常に `Access-Control-Request-Headers` の4つのヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="12976-405">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="12976-406">このヘッダーの一覧には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="12976-406">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="12976-407">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="12976-407">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="12976-408">`Content-Language` が常にホワイトリストに登録されているため、CORS ミドルウェアは次の要求ヘッダーを使用してプレフライト要求に正常に応答します。</span><span class="sxs-lookup"><span data-stu-id="12976-408">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="12976-409">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="12976-409">Set the exposed response headers</span></span>

<span data-ttu-id="12976-410">既定では、ブラウザーはすべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="12976-410">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="12976-411">詳細については、「 [W3C クロスオリジンリソース共有 (用語): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="12976-411">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="12976-412">既定で使用できる応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="12976-412">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="12976-413">CORS 仕様は、これらのヘッダーの*単純な応答ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-413">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="12976-414">アプリで他のヘッダーを使用できるようにするには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-414">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="12976-415">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="12976-415">Credentials in cross-origin requests</span></span>

<span data-ttu-id="12976-416">資格情報では、CORS 要求で特別な処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-416">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="12976-417">既定では、ブラウザーはクロスオリジン要求で資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="12976-417">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="12976-418">資格情報には、cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="12976-418">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="12976-419">クロスオリジン要求で資格情報を送信するには、クライアントで `XMLHttpRequest.withCredentials` を `true`に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-419">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="12976-420">`XMLHttpRequest` を直接使用する:</span><span class="sxs-lookup"><span data-stu-id="12976-420">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="12976-421">JQuery の使用:</span><span class="sxs-lookup"><span data-stu-id="12976-421">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="12976-422">[FETCH API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)の使用:</span><span class="sxs-lookup"><span data-stu-id="12976-422">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="12976-423">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-423">The server must allow the credentials.</span></span> <span data-ttu-id="12976-424">クロスオリジンの資格情報を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-424">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="12976-425">HTTP 応答には `Access-Control-Allow-Credentials` ヘッダーが含まれています。これにより、サーバーがクロスオリジン要求の資格情報を許可していることがブラウザーに伝えられます。</span><span class="sxs-lookup"><span data-stu-id="12976-425">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="12976-426">ブラウザーが資格情報を送信しても、応答に有効な `Access-Control-Allow-Credentials` ヘッダーが含まれていない場合、ブラウザーはアプリへの応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="12976-426">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="12976-427">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="12976-427">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="12976-428">別のドメインの web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="12976-428">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="12976-429">また、CORS 仕様では、`Access-Control-Allow-Credentials` ヘッダーが存在する場合、[オリジン] を `"*"` (すべてのオリジン) に設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="12976-429">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="12976-430">プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="12976-430">Preflight requests</span></span>

<span data-ttu-id="12976-431">一部の CORS 要求については、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="12976-431">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="12976-432">この要求は*プレフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="12976-432">This request is called a *preflight request*.</span></span> <span data-ttu-id="12976-433">次の条件に該当する場合、ブラウザーはプレフライト要求をスキップできます。</span><span class="sxs-lookup"><span data-stu-id="12976-433">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="12976-434">要求メソッドは GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="12976-434">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="12976-435">アプリで `Accept`、`Accept-Language`、`Content-Language`、`Content-Type`、または `Last-Event-ID`以外の要求ヘッダーが設定されていません。</span><span class="sxs-lookup"><span data-stu-id="12976-435">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="12976-436">`Content-Type` ヘッダーには、次のいずれかの値が設定されています。</span><span class="sxs-lookup"><span data-stu-id="12976-436">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="12976-437">クライアント要求に対して設定された要求ヘッダーに適用される規則は、`XMLHttpRequest` オブジェクトの `setRequestHeader` を呼び出すことによってアプリが設定するヘッダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="12976-437">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="12976-438">CORS 仕様は、これらのヘッダー*作成者要求ヘッダー*を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-438">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="12976-439">このルールは、ブラウザーが設定できるヘッダー (`User-Agent`、`Host`、`Content-Length`など) には適用されません。</span><span class="sxs-lookup"><span data-stu-id="12976-439">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="12976-440">プレフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="12976-440">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="12976-441">事前フライト要求では、HTTP OPTIONS メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="12976-441">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="12976-442">次の2つの特殊なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="12976-442">It includes two special headers:</span></span>

* <span data-ttu-id="12976-443">`Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="12976-443">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="12976-444">`Access-Control-Request-Headers`: アプリが実際の要求に対して設定する要求ヘッダーの一覧。</span><span class="sxs-lookup"><span data-stu-id="12976-444">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="12976-445">前述のように、これには、`User-Agent`など、ブラウザーによって設定されるヘッダーは含まれません。</span><span class="sxs-lookup"><span data-stu-id="12976-445">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="12976-446">CORS のプレフライト要求には、`Access-Control-Request-Headers` ヘッダーが含まれる場合があります。これは、実際の要求と共に送信されるヘッダーをサーバーに示します。</span><span class="sxs-lookup"><span data-stu-id="12976-446">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="12976-447">特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-447">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="12976-448">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-448">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="12976-449">ブラウザーの `Access-Control-Request-Headers`の設定方法が完全に一致しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="12976-449">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="12976-450">ヘッダーを `"*"` 以外に設定する (または <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>を使用する) 場合は、少なくとも `Accept`、`Content-Type`、および `Origin`と、サポートするカスタムヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-450">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="12976-451">プレフライト要求に対する応答の例を次に示します (サーバーが要求を許可していることを前提としています)。</span><span class="sxs-lookup"><span data-stu-id="12976-451">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="12976-452">応答には、許可されたメソッドと、必要に応じて `Access-Control-Allow-Headers` ヘッダーを一覧表示する `Access-Control-Allow-Methods` ヘッダーが含まれます。これには、許可されているヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="12976-452">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="12976-453">プレフライト要求が成功した場合、ブラウザーは実際の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="12976-453">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="12976-454">プレフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーを返信しません。</span><span class="sxs-lookup"><span data-stu-id="12976-454">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="12976-455">そのため、ブラウザーはクロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="12976-455">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="12976-456">プレフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="12976-456">Set the preflight expiration time</span></span>

<span data-ttu-id="12976-457">`Access-Control-Max-Age` ヘッダーは、プレフライト要求への応答をキャッシュできる期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="12976-457">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="12976-458">このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="12976-458">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="12976-459">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="12976-459">How CORS works</span></span>

<span data-ttu-id="12976-460">このセクションでは、HTTP メッセージレベルでの[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)要求の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="12976-460">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="12976-461">CORS はセキュリティ機能では**ありません**。</span><span class="sxs-lookup"><span data-stu-id="12976-461">CORS is **not** a security feature.</span></span> <span data-ttu-id="12976-462">CORS は、サーバーが同じオリジンポリシーを緩めることを可能にする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="12976-462">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="12976-463">たとえば、悪意のあるアクターがを使用してサイトに対して[クロスサイトスクリプティング (XSS) を防止](xref:security/cross-site-scripting)し、CORS が有効になっているサイトに対してクロスサイト要求を実行して情報を盗むことができます。</span><span class="sxs-lookup"><span data-stu-id="12976-463">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="12976-464">CORS を許可することで、API の安全性が向上します。</span><span class="sxs-lookup"><span data-stu-id="12976-464">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="12976-465">CORS を適用するには、クライアント (ブラウザー) が必要です。</span><span class="sxs-lookup"><span data-stu-id="12976-465">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="12976-466">サーバーは要求を実行し、応答を返します。これは、エラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="12976-466">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="12976-467">たとえば、次のツールを使用すると、サーバーの応答が表示されます。</span><span class="sxs-lookup"><span data-stu-id="12976-467">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="12976-468">Fiddler</span><span class="sxs-lookup"><span data-stu-id="12976-468">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="12976-469">Postman</span><span class="sxs-lookup"><span data-stu-id="12976-469">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="12976-470">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="12976-470">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="12976-471">アドレスバーに URL を入力して、web ブラウザーを表示します。</span><span class="sxs-lookup"><span data-stu-id="12976-471">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="12976-472">これは、ブラウザーがクロスオリジン[Xhr](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)要求を実行して、それ以外は禁止されるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="12976-472">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="12976-473">(CORS を使用しない) ブラウザーは、クロスオリジン要求を行うことができません。</span><span class="sxs-lookup"><span data-stu-id="12976-473">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="12976-474">CORS の前に、この制限を回避するために[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="12976-474">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="12976-475">JSONP は XHR を使用しないので、`<script>` タグを使用して応答を受信します。</span><span class="sxs-lookup"><span data-stu-id="12976-475">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="12976-476">スクリプトをクロスオリジンで読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="12976-476">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="12976-477">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にする新しい HTTP ヘッダーがいくつか導入されました。</span><span class="sxs-lookup"><span data-stu-id="12976-477">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="12976-478">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="12976-478">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="12976-479">CORS を有効にするためにカスタム JavaScript コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="12976-479">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="12976-480">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="12976-480">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="12976-481">`Origin` ヘッダーは、要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="12976-481">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="12976-482">`Origin` ヘッダーは必須であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-482">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="12976-483">サーバーが要求を許可している場合は、応答に `Access-Control-Allow-Origin` ヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="12976-483">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="12976-484">このヘッダーの値は、要求の `Origin` ヘッダーと一致しているか、または `"*"`ワイルドカードの値であることを意味します。つまり、すべての配信元が許可されます。</span><span class="sxs-lookup"><span data-stu-id="12976-484">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="12976-485">応答に `Access-Control-Allow-Origin` ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="12976-485">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="12976-486">具体的には、ブラウザーは要求を許可しません。</span><span class="sxs-lookup"><span data-stu-id="12976-486">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="12976-487">サーバーが応答を正常に返す場合でも、ブラウザーはクライアントアプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="12976-487">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="12976-488">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="12976-488">Test CORS</span></span>

<span data-ttu-id="12976-489">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="12976-489">To test CORS:</span></span>

1. <span data-ttu-id="12976-490">[API プロジェクトを作成](xref:tutorials/first-web-api)します。</span><span class="sxs-lookup"><span data-stu-id="12976-490">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="12976-491">または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="12976-491">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="12976-492">このドキュメントのいずれかの方法を使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="12976-492">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="12976-493">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="12976-493">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="12976-494">`WithOrigins("https://localhost:<port>");` は、サンプル[コードのダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)と同様のサンプルアプリのテストにのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="12976-494">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="12976-495">Web アプリプロジェクト (Razor Pages または MVC) を作成します。</span><span class="sxs-lookup"><span data-stu-id="12976-495">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="12976-496">このサンプルでは、Razor Pages を使用します。</span><span class="sxs-lookup"><span data-stu-id="12976-496">The sample uses Razor Pages.</span></span> <span data-ttu-id="12976-497">API プロジェクトと同じソリューションに web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="12976-497">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="12976-498">次の強調表示されたコードを*インデックスの cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="12976-498">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="12976-499">上記のコードでは、`url: 'https://<web app>.azurewebsites.net/api/values/1',` を、デプロイされたアプリの URL に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="12976-499">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="12976-500">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="12976-500">Deploy the API project.</span></span> <span data-ttu-id="12976-501">たとえば、 [Azure にデプロイ](xref:host-and-deploy/azure-apps/index)します。</span><span class="sxs-lookup"><span data-stu-id="12976-501">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="12976-502">デスクトップから Razor Pages または MVC アプリを実行し、 **[テスト]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="12976-502">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="12976-503">F12 ツールを使用して、エラーメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="12976-503">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="12976-504">`WithOrigins` から localhost の発信元を削除し、アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="12976-504">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="12976-505">または、別のポートを使用してクライアントアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="12976-505">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="12976-506">たとえば、Visual Studio からを実行します。</span><span class="sxs-lookup"><span data-stu-id="12976-506">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="12976-507">クライアントアプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="12976-507">Test with the client app.</span></span> <span data-ttu-id="12976-508">CORS エラーはエラーを返しますが、エラーメッセージは JavaScript では使用できません。</span><span class="sxs-lookup"><span data-stu-id="12976-508">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="12976-509">F12 ツールの [コンソール] タブを使用して、エラーを確認します。</span><span class="sxs-lookup"><span data-stu-id="12976-509">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="12976-510">ブラウザーによっては、次のようなエラー (F12 ツールコンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="12976-510">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="12976-511">Microsoft Edge を使用する:</span><span class="sxs-lookup"><span data-stu-id="12976-511">Using Microsoft Edge:</span></span>

     <span data-ttu-id="12976-512">**SEC7120: [CORS] `https://webapi.azurewebsites.net/api/values/1` のクロスオリジンリソースのアクセス制御-`https://localhost:44375` 応答ヘッダーに、元の `https://localhost:44375` が見つかりませんでした**</span><span class="sxs-lookup"><span data-stu-id="12976-512">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="12976-513">Chrome を使用する:</span><span class="sxs-lookup"><span data-stu-id="12976-513">Using Chrome:</span></span>

     <span data-ttu-id="12976-514">**オリジン `https://localhost:44375` からの `https://webapi.azurewebsites.net/api/values/1` での XMLHttpRequest へのアクセスが、CORS ポリシーによってブロックされました。要求されたリソースに ' アクセス制御-発信元 ' ヘッダーは存在しません。**</span><span class="sxs-lookup"><span data-stu-id="12976-514">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="12976-515">CORS が有効なエンドポイントは、 [Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="12976-515">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="12976-516">ツールを使用する場合は、`Origin` ヘッダーによって指定された要求の配信元が、要求を受信しているホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-516">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="12976-517">要求が `Origin` ヘッダーの値に基づいて*クロスオリジンで*ない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="12976-517">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="12976-518">CORS ミドルウェアが要求を処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="12976-518">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="12976-519">CORS ヘッダーが応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="12976-519">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="12976-520">IIS での CORS</span><span class="sxs-lookup"><span data-stu-id="12976-520">CORS in IIS</span></span>

<span data-ttu-id="12976-521">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合、CORS は Windows 認証の前に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-521">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="12976-522">このシナリオをサポートするには、アプリ用に[IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールして構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12976-522">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12976-523">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="12976-523">Additional resources</span></span>

* [<span data-ttu-id="12976-524">クロスオリジンリソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="12976-524">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="12976-525">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="12976-525">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end