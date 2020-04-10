---
title: ASP.NETコアでのクロスオリジンリクエスト(CORS)の有効化
author: rick-anderson
description: ASP.NET Core アプリでクロスオリジン要求を許可または拒否するための標準として CORS を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 601e26e1990a86ad60aa50c8c93ffa490ff6b708
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007185"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="af45e-103">ASP.NETコアでのクロスオリジンリクエスト(CORS)の有効化</span><span class="sxs-lookup"><span data-stu-id="af45e-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af45e-104">[リック・アンダーソン](https://twitter.com/RickAndMSFT)と[カーク・ラーキン](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="af45e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="af45e-105">この記事では、ASP.NET コア アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="af45e-106">ブラウザのセキュリティにより、Web ページが Web ページを提供したドメインとは異なるドメインに対して要求を行えないようにします。</span><span class="sxs-lookup"><span data-stu-id="af45e-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="af45e-107">この制限は、*同一生成元ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="af45e-108">同一生成元ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取るのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="af45e-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="af45e-109">場合によっては、他のサイトでアプリに対してクロスオリジン要求を行うことを許可する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="af45e-110">詳細については、 [Mozilla CORS の記事](https://developer.mozilla.org/docs/Web/HTTP/CORS)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="af45e-111">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="af45e-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="af45e-112">サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="af45e-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="af45e-113">セキュリティ機能**ではない**、CORS はセキュリティを緩和します。</span><span class="sxs-lookup"><span data-stu-id="af45e-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="af45e-114">API は CORS を許可することで安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="af45e-115">詳細については[、「CORS の仕組み](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="af45e-116">サーバーが、一部のクロスオリジン要求を許可し、その他の要求を拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="af45e-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="af45e-117">[JSONP](/dotnet/framework/wcf/samples/jsonp)などの以前の手法よりも安全で柔軟性があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="af45e-118">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="af45e-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="af45e-119">同じ起源</span><span class="sxs-lookup"><span data-stu-id="af45e-119">Same origin</span></span>

<span data-ttu-id="af45e-120">2 つの URL が同じスキーム、ホスト、およびポートを持つ場合は、同じオリジンを持ちます ([RFC 6454](https://tools.ietf.org/html/rfc6454))。</span><span class="sxs-lookup"><span data-stu-id="af45e-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="af45e-121">これらの 2 つの URL のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="af45e-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="af45e-122">これらの URL のオリジンは、前の 2 つの URL とは異なります。</span><span class="sxs-lookup"><span data-stu-id="af45e-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="af45e-123">`https://example.net`&ndash;異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="af45e-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="af45e-124">`https://www.example.com/foo.html`&ndash;異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="af45e-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="af45e-125">`http://example.com/foo.html`&ndash;異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="af45e-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="af45e-126">`https://example.com:9000/foo.html`&ndash;異なるポート</span><span class="sxs-lookup"><span data-stu-id="af45e-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="af45e-127">CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="af45e-127">Enable CORS</span></span>

<span data-ttu-id="af45e-128">CORS を有効にするには、次の 3 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="af45e-129">ミドルウェアで[名前付きポリシー](#np)または[デフォルトポリシー](#dp)を使用する 。</span><span class="sxs-lookup"><span data-stu-id="af45e-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="af45e-130">[エンドポイント ルーティング](#ecors)を使用する :</span><span class="sxs-lookup"><span data-stu-id="af45e-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="af45e-131">[[EnableCors] 属性を使用します](#attr)。</span><span class="sxs-lookup"><span data-stu-id="af45e-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="af45e-132">名前付きポリシーで[[EnableCors]](#attr)属性を使用すると、CORS をサポートするエンドポイントを制限する際に、最も優れた制御が提供されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="af45e-133">各方法については、以下のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="af45e-134">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="af45e-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="af45e-135">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="af45e-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="af45e-136">次のコードでは、指定したオリジンを持つすべてのアプリのエンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="af45e-137">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="af45e-137">The preceding code:</span></span>

* <span data-ttu-id="af45e-138">ポリシー名を に`_myAllowSpecificOrigins`設定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="af45e-139">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="af45e-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="af45e-140">拡張メソッド<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>を呼び出し`_myAllowSpecificOrigins`、CORS ポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="af45e-141">`UseCors`CORS ミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="af45e-141">`UseCors` adds the CORS middleware.</span></span>
* <span data-ttu-id="af45e-142"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-142">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="af45e-143">ラムダはオブジェクト<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="af45e-143">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="af45e-144">などの`WithOrigins`[構成オプション](#cors-policy-options)については、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-144">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="af45e-145">すべてのコントローラー`_myAllowSpecificOrigins`エンドポイントの CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-145">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="af45e-146">特定[のエンドポイント](#ecors)に CORS ポリシーを適用するには、エンドポイントルーティングを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-146">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="af45e-147">エンドポイント ルーティングでは、CORS ミドルウェアが 呼び出しと`UseRouting`の`UseEndpoints`間で実行されるように構成する***必要があります***。</span><span class="sxs-lookup"><span data-stu-id="af45e-147">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="af45e-148">上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-148">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="af45e-149">メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しは、アプリのサービス コンテナーに CORS サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="af45e-149">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="af45e-150">詳細については、このドキュメントの[CORS ポリシー オプション](#cpo)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-150">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="af45e-151">次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>のコードに示すように、メソッドは連鎖できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-151">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="af45e-152">注意 : 指定された**not**URL に末尾の`/`スラッシュ ( ) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="af45e-152">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="af45e-153">URL が で`/`終わる場合、比較`false`は返され、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="af45e-153">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="af45e-154">デフォルトポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="af45e-154">CORS with default policy and middleware</span></span>

<span data-ttu-id="af45e-155">次の強調表示されたコードは、既定の CORS ポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-155">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="af45e-156">上記のコードでは、すべてのコントローラ エンドポイントにデフォルトの CORS ポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-156">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="af45e-157">エンドポイント ルーティングで CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="af45e-157">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="af45e-158">現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にしても、[自動プリフライト要求](#apf)はサポート***されません***。</span><span class="sxs-lookup"><span data-stu-id="af45e-158">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="af45e-159">詳細については、この[GitHub の問題](https://github.com/dotnet/aspnetcore/issues/20709)と[エンドポイント ルーティングを使用した CORS のテストと [HttpOptions]](#tcer)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-159">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="af45e-160">エンドポイント ルーティングを使用すると、次の拡張メソッドを使用してエンドポイントごとに<xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*>CORS を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="af45e-160">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,41,44)]

<span data-ttu-id="af45e-161">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="af45e-161">In the preceding code:</span></span>

* <span data-ttu-id="af45e-162">`app.UseCors`を使用して、CORS ミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-162">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="af45e-163">デフォルトポリシーが設定されていないため、`app.UseCors()`単独では CORS を有効にしません。</span><span class="sxs-lookup"><span data-stu-id="af45e-163">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="af45e-164">および`/echo`コントローラ エンドポイントは、指定されたポリシーを使用して、クロス オリジン要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="af45e-164">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="af45e-165">`/echo2`および Razor ページ エンドポイントでは、既定のポリシーが指定されていないため、クロス オリジン要求は許可***されません***。</span><span class="sxs-lookup"><span data-stu-id="af45e-165">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="af45e-166">[[DisableCors]](#dc)属性は、エンドポイント ルーティングによって有効になっている CORS を`RequireCors`無効***にしません***。</span><span class="sxs-lookup"><span data-stu-id="af45e-166">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="af45e-167">上記のようなコードのテスト手順については、「[エンドポイントルーティングを使用した CORS のテスト」および「HttpOptions」](#tcer)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-167">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="af45e-168">属性を使用した CORS の有効化</span><span class="sxs-lookup"><span data-stu-id="af45e-168">Enable CORS with attributes</span></span>

<span data-ttu-id="af45e-169">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性を使用して CORS を有効にし、CORS を必要とするエンドポイントにのみ名前付きポリシーを適用すると、最高の制御が提供されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-169">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="af45e-170">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-170">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="af45e-171">この`[EnableCors]`属性は、すべてのエンドポイントではなく、選択したエンドポイントに CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-171">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="af45e-172">`[EnableCors]`は、既定のポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-172">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="af45e-173">`[EnableCors("{Policy String}")]`は、名前付きポリシーを指定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-173">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="af45e-174">属性`[EnableCors]`は、次の場合に適用できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-174">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="af45e-175">カミソリページ`PageModel`</span><span class="sxs-lookup"><span data-stu-id="af45e-175">Razor Page `PageModel`</span></span>
* <span data-ttu-id="af45e-176">コントローラー</span><span class="sxs-lookup"><span data-stu-id="af45e-176">Controller</span></span>
* <span data-ttu-id="af45e-177">コントローラアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="af45e-177">Controller action method</span></span>

<span data-ttu-id="af45e-178">属性を使用して、コントローラ、ページ モデル、アクション メソッドに異なる`[EnableCors]`ポリシーを適用できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-178">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="af45e-179">属性が`[EnableCors]`コントローラー、ページ・モデル、またはアクション・メソッドに適用され、CORS がミドルウェアで有効になっている場合、***両方の***ポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-179">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="af45e-180">***ポリシーの組み合わせに対しては、お勧めします。***`[EnableCors]`***同じアプリで属性またはミドルウェアを使用する必要はありません。***</span><span class="sxs-lookup"><span data-stu-id="af45e-180">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="af45e-181">次のコードでは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-181">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="af45e-182">次のコードでは、2 つの CORS ポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="af45e-182">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="af45e-183">CORS 要求を制限する最も優れた制御を行う場合:</span><span class="sxs-lookup"><span data-stu-id="af45e-183">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="af45e-184">名前`[EnableCors("MyPolicy")]`付きポリシーで使用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-184">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="af45e-185">既定のポリシーを定義しないでください。</span><span class="sxs-lookup"><span data-stu-id="af45e-185">Don't define a default policy.</span></span>
* <span data-ttu-id="af45e-186">[エンドポイント ルーティング](#ecors)を使用しない。</span><span class="sxs-lookup"><span data-stu-id="af45e-186">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="af45e-187">次のセクションのコードは、上記の一覧を満たしています。</span><span class="sxs-lookup"><span data-stu-id="af45e-187">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="af45e-188">上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-188">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="af45e-189">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="af45e-189">Disable CORS</span></span>

<span data-ttu-id="af45e-190">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は[、エンドポイント ルーティング](#ecors)によって有効にされた CORS を無効***にしません***。</span><span class="sxs-lookup"><span data-stu-id="af45e-190">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="af45e-191">次のコードは、CORS`"MyPolicy"`ポリシーを定義します。</span><span class="sxs-lookup"><span data-stu-id="af45e-191">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="af45e-192">次のコードは、アクションの CORS を無効にします`GetValues2`。</span><span class="sxs-lookup"><span data-stu-id="af45e-192">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="af45e-193">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="af45e-193">The preceding code:</span></span>

* <span data-ttu-id="af45e-194">[エンドポイント ルーティング](#ecors)で CORS を有効にしません。</span><span class="sxs-lookup"><span data-stu-id="af45e-194">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="af45e-195">既定の[CORS ポリシー](#dp)を定義しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-195">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="af45e-196">[[EnableCors("MyPolicy")]を](#attr)使用して`"MyPolicy"`、コントローラのCORSポリシーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-196">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="af45e-197">メソッドの CORS`GetValues2`を無効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-197">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="af45e-198">上記のコードのテスト手順については、「[テスト CORS」](#testc)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-198">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="af45e-199">CORS ポリシー オプション</span><span class="sxs-lookup"><span data-stu-id="af45e-199">CORS policy options</span></span>

<span data-ttu-id="af45e-200">このセクションでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-200">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="af45e-201">許可された原点を設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-201">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="af45e-202">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-202">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="af45e-203">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-203">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="af45e-204">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-204">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="af45e-205">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="af45e-205">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="af45e-206">プリフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-206">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="af45e-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>が で`Startup.ConfigureServices`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="af45e-208">いくつかのオプションについては、[最初に「CORS の仕組み](#how-cors)」のセクションを読んでも役に立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-208">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="af45e-209">許可された原点を設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-209">Set the allowed origins</span></span>

<span data-ttu-id="af45e-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;任意のスキーム ( または`http``https`) を持つすべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="af45e-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="af45e-211">`AllowAnyOrigin`*どのウェブサイトも*アプリにクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-211">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="af45e-212">指定され`AllowAnyOrigin`、`AllowCredentials`安全でない構成であり、クロスサイトリクエストフォージェリにつながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-212">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="af45e-213">アプリが両方のメソッドで構成されている場合、CORS サービスは無効な CORS 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="af45e-213">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="af45e-214">`AllowAnyOrigin`はプリフライト要求とヘッダー`Access-Control-Allow-Origin`に影響します。</span><span class="sxs-lookup"><span data-stu-id="af45e-214">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="af45e-215">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-215">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="af45e-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;ポリシーの<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>プロパティを、オリジンが許可されているかどうかを評価するときに、設定されたワイルドカード ドメインとオリジンが一致するように設定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="af45e-217">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-217">Set the allowed HTTP methods</span></span>

<span data-ttu-id="af45e-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="af45e-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="af45e-219">任意の HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="af45e-219">Allows any HTTP method:</span></span>
* <span data-ttu-id="af45e-220">プリフライト要求とヘッダーに`Access-Control-Allow-Methods`影響します。</span><span class="sxs-lookup"><span data-stu-id="af45e-220">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="af45e-221">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="af45e-222">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-222">Set the allowed request headers</span></span>

<span data-ttu-id="af45e-223">特定のヘッダーを CORS 要求で送信できるようにするには[、author 要求ヘッダー](https://xhr.spec.whatwg.org/#request)と呼<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>びます。</span><span class="sxs-lookup"><span data-stu-id="af45e-223">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="af45e-224">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-224">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="af45e-225">`AllowAnyHeader`は、プリフライト要求および[アクセス制御要求ヘッダーヘッダーに影響します](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)。</span><span class="sxs-lookup"><span data-stu-id="af45e-225">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="af45e-226">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-226">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="af45e-227">CORS ミドルウェア ポリシーは、 で`WithHeaders`指定された特定のヘッダー`Access-Control-Request-Headers`と一致する場合にのみ可能です。 `WithHeaders`</span><span class="sxs-lookup"><span data-stu-id="af45e-227">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="af45e-228">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="af45e-228">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="af45e-229">CORS ミドルウェアは、`Content-Language`[に一](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)覧されていないため、次の要求ヘッダーを使用してプレフライト要求を`WithHeaders`拒否します。</span><span class="sxs-lookup"><span data-stu-id="af45e-229">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="af45e-230">アプリは*200 OK*応答を返しますが、CORS ヘッダーを返しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-230">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="af45e-231">したがって、ブラウザーは、クロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-231">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="af45e-232">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-232">Set the exposed response headers</span></span>

<span data-ttu-id="af45e-233">既定では、ブラウザーは、すべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-233">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="af45e-234">詳細については、「 [W3C クロス オリジン リソース共有 (用語集): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-234">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="af45e-235">デフォルトで使用可能な応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="af45e-235">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="af45e-236">CORS 仕様では、これらのヘッダーの*単純な応答ヘッダーを*呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-236">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="af45e-237">他のヘッダーをアプリで使用できるようにするには、次の<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="af45e-237">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="af45e-238">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="af45e-238">Credentials in cross-origin requests</span></span>

<span data-ttu-id="af45e-239">資格情報には、CORS 要求での特別な処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="af45e-239">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="af45e-240">既定では、ブラウザーはクロスオリジン要求を含む資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-240">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="af45e-241">資格情報には、Cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-241">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="af45e-242">クロスオリジン要求を使用して資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials``true`に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-242">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="af45e-243">直接`XMLHttpRequest`使用:</span><span class="sxs-lookup"><span data-stu-id="af45e-243">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="af45e-244">jQuery を使用する:</span><span class="sxs-lookup"><span data-stu-id="af45e-244">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="af45e-245">フェッチ[API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用 :</span><span class="sxs-lookup"><span data-stu-id="af45e-245">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="af45e-246">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-246">The server must allow the credentials.</span></span> <span data-ttu-id="af45e-247">クロスオリジンの資格情報を許可するには、次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="af45e-247">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="af45e-248">HTTP 応答には`Access-Control-Allow-Credentials`ヘッダーが含まれており、サーバーがクロスオリジン要求の資格情報を許可することをブラウザーに伝えます。</span><span class="sxs-lookup"><span data-stu-id="af45e-248">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="af45e-249">ブラウザーが資格情報を送信しても、応答に有効な`Access-Control-Allow-Credentials`ヘッダーが含まれていない場合、ブラウザーはアプリに対する応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="af45e-249">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="af45e-250">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="af45e-250">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="af45e-251">別のドメインの Web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-251">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="af45e-252">CORS 仕様では、ヘッダーが存在する場合`"*"`、(すべての起点) に原点`Access-Control-Allow-Credentials`を設定することは無効であるとも述べています。</span><span class="sxs-lookup"><span data-stu-id="af45e-252">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="af45e-253">プリフライトリクエスト</span><span class="sxs-lookup"><span data-stu-id="af45e-253">Preflight requests</span></span>

<span data-ttu-id="af45e-254">一部の CORS 要求では、ブラウザーは実際の要求を行う前に追加[の OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="af45e-254">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="af45e-255">この要求は[、プリフライト要求](https://developer.mozilla.org/docs/Glossary/Preflight_request)と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-255">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="af45e-256">次***の条件がすべて***満たしている場合、ブラウザはプリフライトリクエストをスキップできます。</span><span class="sxs-lookup"><span data-stu-id="af45e-256">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="af45e-257">要求メソッドは、GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="af45e-257">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="af45e-258">アプリ`Accept`は、 、 、 `Accept-Language`、 、、`Content-Language``Content-Type`または`Last-Event-ID`以外の要求ヘッダーを設定しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-258">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="af45e-259">ヘッダー`Content-Type`が設定されている場合、次のいずれかの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-259">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="af45e-260">クライアント要求に設定された要求ヘッダーのルールは、アプリケーションがオブジェクトを呼び出`setRequestHeader`すことによって設定するヘッダーに`XMLHttpRequest`適用されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-260">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="af45e-261">CORS 仕様では、これらのヘッダー[の作成者要求ヘッダーを](https://www.w3.org/TR/cors/#author-request-headers)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-261">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="af45e-262">ルールは、 、 、 など、`User-Agent``Host`ブラウザで設定できるヘッダーには適用されません。 `Content-Length`</span><span class="sxs-lookup"><span data-stu-id="af45e-262">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="af45e-263">次に示す応答は、このドキュメントの[「テスト CORS」](#testc)セクションの **[Put test]** ボタンから行われたプリフライト要求に似た応答です。</span><span class="sxs-lookup"><span data-stu-id="af45e-263">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="af45e-264">プリフライト要求では[、HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-264">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="af45e-265">次のヘッダーが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-265">It may include the following headers:</span></span>

* <span data-ttu-id="af45e-266">[アクセス制御要求メソッド](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="af45e-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="af45e-267">[アクセスコントロール要求ヘッダー](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): アプリが実際の要求に設定する要求ヘッダーのリスト。</span><span class="sxs-lookup"><span data-stu-id="af45e-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="af45e-268">前述のように、ブラウザーが設定するヘッダーは含みません`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="af45e-268">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="af45e-269">アクセス制御許可メソッド</span><span class="sxs-lookup"><span data-stu-id="af45e-269">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="af45e-270">プリフライト要求が拒否された場合、アプリは応答を`200 OK`返しますが、CORS ヘッダーは設定しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-270">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="af45e-271">したがって、ブラウザーは、クロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-271">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="af45e-272">拒否されたプリフライト要求の例については、このドキュメントの[「CORS](#testc)のテスト」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-272">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="af45e-273">F12 ツールを使用すると、コンソール アプリはブラウザーに応じて、次のいずれかと同様のエラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-273">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="af45e-274">Firefox: クロスオリジンリクエストブロック: 同じオリジンポリシーでは、 で`https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`リモートリソースの読み取りを禁止します。</span><span class="sxs-lookup"><span data-stu-id="af45e-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="af45e-275">(理由: CORS 要求が成功しませんでした)。</span><span class="sxs-lookup"><span data-stu-id="af45e-275">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="af45e-276">詳細情報</span><span class="sxs-lookup"><span data-stu-id="af45e-276">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="af45e-277">クロムベース: ''https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' でのフェッチへのアクセスが CORS ポリシーによってブロックされました: プリフライト要求への応答はアクセス制御チェックに合格しません: 要求されたリソースに 'アクセス制御-許可-Origin' ヘッダーがありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-277">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="af45e-278">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="af45e-278">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="af45e-279">特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-279">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="af45e-280">すべての[作成者要求ヘッダー](https://www.w3.org/TR/cors/#author-request-headers)を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-280">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="af45e-281">ブラウザは、 の設定`Access-Control-Request-Headers`方法に一貫性がありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-281">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="af45e-282">次のいずれかの場合:</span><span class="sxs-lookup"><span data-stu-id="af45e-282">If either:</span></span>

* <span data-ttu-id="af45e-283">ヘッダーは、次以外の値に設定されます。`"*"`</span><span class="sxs-lookup"><span data-stu-id="af45e-283">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="af45e-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>呼び出し:`Accept`少`Content-Type`なくとも`Origin`、 、および を含め、サポートするカスタム ヘッダーを含めます。</span><span class="sxs-lookup"><span data-stu-id="af45e-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="af45e-285">自動プリフライトリクエストコード</span><span class="sxs-lookup"><span data-stu-id="af45e-285">Automatic preflight request code</span></span>

<span data-ttu-id="af45e-286">CORS ポリシーが適用されると、次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-286">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="af45e-287">を呼び出`app.UseCors`すことによって`Startup.Configure`グローバルに.</span><span class="sxs-lookup"><span data-stu-id="af45e-287">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="af45e-288">属性を`[EnableCors]`使用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-288">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="af45e-289">ASP.NETコアは、プレフライトの OPTIONS 要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="af45e-289">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="af45e-290">現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にすることは、自動プリフライト要求をサポート***していません***。</span><span class="sxs-lookup"><span data-stu-id="af45e-290">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="af45e-291">このドキュメントの[「テスト CORS」](#testc)セクションでは、この動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-291">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="af45e-292">プリフライト要求の [Http オプション] 属性</span><span class="sxs-lookup"><span data-stu-id="af45e-292">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="af45e-293">適切なポリシーで CORS が有効になっている場合、ASP.NETコアは通常 CORS プリフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="af45e-293">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="af45e-294">シナリオによっては、この場合に該当しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-294">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="af45e-295">たとえば、エンドポイント[ルーティングで CORS](#ecors)を使用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-295">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="af45e-296">次のコードでは、OPTIONS 要求のエンドポイントを作成するために[[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-296">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="af45e-297">上記のコードのテスト手順については、「[エンドポイント ルーティングを使用した CORS のテスト」および「HttpOptions」](#tcer)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-297">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="af45e-298">プリフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-298">Set the preflight expiration time</span></span>

<span data-ttu-id="af45e-299">ヘッダー`Access-Control-Max-Age`は、プリフライト要求に対する応答をキャッシュできる時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-299">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="af45e-300">このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-300">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="af45e-301">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="af45e-301">How CORS works</span></span>

<span data-ttu-id="af45e-302">このセクションでは、HTTP メッセージのレベルで[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求で何が起こるかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-302">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="af45e-303">CORS はセキュリティ機能**ではありません**。</span><span class="sxs-lookup"><span data-stu-id="af45e-303">CORS is **not** a security feature.</span></span> <span data-ttu-id="af45e-304">CORS は、サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="af45e-304">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="af45e-305">たとえば、悪意のあるアクターがサイトに対して[クロスサイト スクリプティング (XSS) を](xref:security/cross-site-scripting)使用し、CORS 対応サイトに対してクロスサイト要求を実行して情報を盗む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-305">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="af45e-306">API は CORS を許可することで安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-306">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="af45e-307">CORS を強制するのはクライアント (ブラウザー) の管理です。</span><span class="sxs-lookup"><span data-stu-id="af45e-307">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="af45e-308">サーバーは要求を実行し、応答を返します、それはエラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="af45e-308">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="af45e-309">たとえば、次のツールのどれでもサーバーの応答を表示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-309">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="af45e-310">Fiddler</span><span class="sxs-lookup"><span data-stu-id="af45e-310">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="af45e-311">Postman</span><span class="sxs-lookup"><span data-stu-id="af45e-311">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="af45e-312">.NET Http クライアント</span><span class="sxs-lookup"><span data-stu-id="af45e-312">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="af45e-313">アドレス バーに URL を入力して Web ブラウザを表示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-313">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="af45e-314">これは、ブラウザがクロスオリジン[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行することを許可するサーバーの方法です。</span><span class="sxs-lookup"><span data-stu-id="af45e-314">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="af45e-315">CORS を使用しないブラウザーは、クロスオリジン要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="af45e-315">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="af45e-316">CORS より前では[、JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)はこの制限を回避するために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="af45e-316">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="af45e-317">JSONP は XHR を使用せず、`<script>`タグを使用して応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="af45e-317">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="af45e-318">スクリプトは、クロスオリジンでロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="af45e-318">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="af45e-319">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にするいくつかの新しい HTTP ヘッダーが導入されました。</span><span class="sxs-lookup"><span data-stu-id="af45e-319">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="af45e-320">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-320">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="af45e-321">カスタム JavaScript コードは、CORS を有効にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-321">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="af45e-322">展開された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[PUT テスト ボタン](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="af45e-322">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="af45e-323">次に示しているのは、[値](https://cors3.azurewebsites.net/)テスト ボタンから に対するクロスオリジン要求`https://cors1.azurewebsites.net/api/values`の例です。</span><span class="sxs-lookup"><span data-stu-id="af45e-323">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="af45e-324">`Origin`ヘッダー:</span><span class="sxs-lookup"><span data-stu-id="af45e-324">The `Origin` header:</span></span>

* <span data-ttu-id="af45e-325">要求を行うサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="af45e-325">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="af45e-326">必要であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-326">Is required and must be different from the host.</span></span>

<span data-ttu-id="af45e-327">**一般ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="af45e-327">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="af45e-328">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="af45e-328">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="af45e-329">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="af45e-329">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="af45e-330">要求`OPTIONS`では、サーバーは応答ヘッダー**ヘッダーを**`Access-Control-Allow-Origin: {allowed origin}`応答に設定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-330">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="af45e-331">たとえば、デプロイされた[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[[削除 [EnableCors]](https://cors1.azurewebsites.net/test?number=2)ボタン`OPTIONS`要求には、次のヘッダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="af45e-331">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="af45e-332">**一般ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="af45e-332">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="af45e-333">**応答ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="af45e-333">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="af45e-334">**要求ヘッダー**</span><span class="sxs-lookup"><span data-stu-id="af45e-334">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="af45e-335">上記の**応答ヘッダーでは、** サーバーは応答に[アクセス制御-許可-オリジン](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-335">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="af45e-336">この`https://cors1.azurewebsites.net`ヘッダーの値は、要求`Origin`のヘッダーと一致します。</span><span class="sxs-lookup"><span data-stu-id="af45e-336">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="af45e-337">が<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>呼び出されると`Access-Control-Allow-Origin: *`、ワイルドカード値が返されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-337">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="af45e-338">`AllowAnyOrigin`任意の原点を許可します。</span><span class="sxs-lookup"><span data-stu-id="af45e-338">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="af45e-339">応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="af45e-339">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="af45e-340">具体的には、ブラウザーは要求を拒否します。</span><span class="sxs-lookup"><span data-stu-id="af45e-340">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="af45e-341">サーバーが成功した応答を返した場合でも、ブラウザーはクライアント アプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="af45e-341">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="af45e-342">オプション要求の表示</span><span class="sxs-lookup"><span data-stu-id="af45e-342">Display OPTIONS requests</span></span>

<span data-ttu-id="af45e-343">デフォルトでは、Chrome と Edge のブラウザでは、F12 ツールのネットワーク タブに OPTIONS リクエストは表示されません。</span><span class="sxs-lookup"><span data-stu-id="af45e-343">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="af45e-344">これらのブラウザで OPTIONS 要求を表示するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="af45e-344">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="af45e-345">`chrome://flags/#out-of-blink-cors` または `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="af45e-345">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="af45e-346">フラグを無効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-346">disable the flag.</span></span>
* <span data-ttu-id="af45e-347">[再起動] をタップします。</span><span class="sxs-lookup"><span data-stu-id="af45e-347">restart.</span></span>

<span data-ttu-id="af45e-348">デフォルトでは、オプションリクエストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-348">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="af45e-349">IIS の CORS</span><span class="sxs-lookup"><span data-stu-id="af45e-349">CORS in IIS</span></span>

<span data-ttu-id="af45e-350">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合は、Windows 認証の前に CORS を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-350">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="af45e-351">このシナリオをサポートするには[、IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールし、アプリ用に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-351">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="af45e-352">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="af45e-352">Test CORS</span></span>

<span data-ttu-id="af45e-353">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)には、CORS をテストするためのコードがあります。</span><span class="sxs-lookup"><span data-stu-id="af45e-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="af45e-354">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-354">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="af45e-355">サンプルは、Razor ページが追加された API プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="af45e-355">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="af45e-356">`WithOrigins("https://localhost:<port>");`ダウンロード サンプル[コード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)に似たサンプル アプリのテストにのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-356">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="af45e-357">テストの`ValuesController`エンドポイントを次に示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-357">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="af45e-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)は[、リック.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet パッケージによって提供され、ルート情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="af45e-359">次のいずれかの方法を使用して、上記のサンプル コードをテストします。</span><span class="sxs-lookup"><span data-stu-id="af45e-359">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="af45e-360">デプロイされたサンプル アプリを使用[https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)します。</span><span class="sxs-lookup"><span data-stu-id="af45e-360">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="af45e-361">サンプルをダウンロードする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-361">There is no need to download the sample.</span></span>
* <span data-ttu-id="af45e-362">サンプルを実行するには`dotnet run`、既定の`https://localhost:5001`URL を使用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-362">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="af45e-363">の URL に対してポートを 44398 に設定して、Visual Studio からサンプルを実行`https://localhost:44398`します。</span><span class="sxs-lookup"><span data-stu-id="af45e-363">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="af45e-364">F12 ツールでブラウザを使用する場合:</span><span class="sxs-lookup"><span data-stu-id="af45e-364">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="af45e-365">**[値**] ボタンを選択し、[**ネットワーク**] タブでヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="af45e-365">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="af45e-366">PUT**テスト**ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="af45e-366">Select the **PUT test** button.</span></span> <span data-ttu-id="af45e-367">[OPTIONS 要求の表示](#options)手順については、「OPTIONS 要求の表示」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-367">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="af45e-368">**PUT テスト**では、オプションプリフライト要求と PUT 要求の 2 つの要求が作成されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-368">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="af45e-369">失敗した**`GetValues2 [DisableCors]`** CORS 要求をトリガーするボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="af45e-369">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="af45e-370">ドキュメントで説明したように、応答は 200 成功を返しますが、CORS 要求は行いません。</span><span class="sxs-lookup"><span data-stu-id="af45e-370">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="af45e-371">**[コンソール**]タブを選択して、CORS エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-371">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="af45e-372">ブラウザによっては、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-372">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="af45e-373">ORIGIN`'https://cors1.azurewebsites.net/api/values/GetValues2'``'https://cors3.azurewebsites.net'`からのフェッチへのアクセスが CORS ポリシーによってブロックされました: 要求されたリソースに 'アクセス制御-許可-元の元のヘッダーがありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-373">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="af45e-374">非透過の応答が要求に対応している場合は、要求のモードを 'no-cors' に設定し、CORS を無効にしてリソースをフェッチしてください。</span><span class="sxs-lookup"><span data-stu-id="af45e-374">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="af45e-375">CORS 対応のエンドポイントは[、curl](https://curl.haxx.se/) [、Fiddler](https://www.telerik.com/fiddler) [、Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="af45e-375">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="af45e-376">ツールを使用する場合、ヘッダーで指定された要求の発信`Origin`元は、要求を受け取るホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-376">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="af45e-377">要求がヘッダーの値に基づいて*クロスオリジン*でない場合: `Origin`</span><span class="sxs-lookup"><span data-stu-id="af45e-377">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="af45e-378">要求を処理するために CORS ミドルウェアは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-378">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="af45e-379">CORS ヘッダーは応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="af45e-379">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="af45e-380">次のコマンドは`curl`、情報を含む OPTIONS 要求を発行するために使用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-380">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="af45e-381">エンドポイント ルーティングを使用して CORS をテストし、[HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="af45e-381">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="af45e-382">現在の使用を使用して`RequireCors`エンドポイントごとに CORS を有効にしても、[自動プリフライト要求](#apf)はサポート***されません***。</span><span class="sxs-lookup"><span data-stu-id="af45e-382">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="af45e-383">エンドポイント ルーティングを使用して[CORS を有効にする次のコードを](#ecors)検討してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-383">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="af45e-384">テスト用`TodoItems1Controller`のエンドポイントを次に示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-384">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="af45e-385">展開された[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)の[テスト ページ](https://cors1.azurewebsites.net/test?number=1)から、上記のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="af45e-385">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="af45e-386">エンドポイントがプリフライト要求を持ち、応答するため **、[EnableCors]** ボタンと GET `[EnableCors]` **[EnableCors]** ボタンは成功します。</span><span class="sxs-lookup"><span data-stu-id="af45e-386">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="af45e-387">他のエンドポイントは失敗します。</span><span class="sxs-lookup"><span data-stu-id="af45e-387">The other endpoints fails.</span></span> <span data-ttu-id="af45e-388">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)が送信するため **、GET**ボタンが失敗します。</span><span class="sxs-lookup"><span data-stu-id="af45e-388">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="af45e-389">以下`TodoItems2Controller`に、同様のエンドポイントを示しますが、OPTIONS 要求に応答する明示的なコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="af45e-389">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="af45e-390">展開されたサンプルの[テスト ページ](https://cors1.azurewebsites.net/test?number=2)から、上記のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="af45e-390">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="af45e-391">[**コントローラ**] ドロップダウン リストで、[**プリフライト**] を選択し、[**コントローラの設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="af45e-391">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="af45e-392">`TodoItems2Controller`エンドポイントへの CORS 呼び出しはすべて成功します。</span><span class="sxs-lookup"><span data-stu-id="af45e-392">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af45e-393">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="af45e-393">Additional resources</span></span>

* [<span data-ttu-id="af45e-394">クロスオリジンリソース共有(CORS)</span><span class="sxs-lookup"><span data-stu-id="af45e-394">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="af45e-395">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="af45e-395">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="af45e-396">著者 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="af45e-396">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="af45e-397">この記事では、ASP.NET コア アプリで CORS を有効にする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-397">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="af45e-398">ブラウザのセキュリティにより、Web ページが Web ページを提供したドメインとは異なるドメインに対して要求を行えないようにします。</span><span class="sxs-lookup"><span data-stu-id="af45e-398">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="af45e-399">この制限は、*同一生成元ポリシー*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-399">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="af45e-400">同一生成元ポリシーは、悪意のあるサイトが別のサイトから機密データを読み取るのを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="af45e-400">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="af45e-401">他のサイトがアプリに対してクロスオリジン要求を行うことを許可したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-401">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="af45e-402">詳細については、 [Mozilla CORS の記事](https://developer.mozilla.org/docs/Web/HTTP/CORS)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-402">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="af45e-403">[クロスオリジンリソース共有](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="af45e-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="af45e-404">サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="af45e-404">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="af45e-405">セキュリティ機能**ではない**、CORS はセキュリティを緩和します。</span><span class="sxs-lookup"><span data-stu-id="af45e-405">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="af45e-406">API は CORS を許可することで安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-406">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="af45e-407">詳細については[、「CORS の仕組み](#how-cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-407">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="af45e-408">サーバーが、一部のクロスオリジン要求を許可し、その他の要求を拒否できるようにします。</span><span class="sxs-lookup"><span data-stu-id="af45e-408">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="af45e-409">[JSONP](/dotnet/framework/wcf/samples/jsonp)などの以前の手法よりも安全で柔軟性があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-409">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="af45e-410">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="af45e-410">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="af45e-411">同じ起源</span><span class="sxs-lookup"><span data-stu-id="af45e-411">Same origin</span></span>

<span data-ttu-id="af45e-412">2 つの URL が同じスキーム、ホスト、およびポートを持つ場合は、同じオリジンを持ちます ([RFC 6454](https://tools.ietf.org/html/rfc6454))。</span><span class="sxs-lookup"><span data-stu-id="af45e-412">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="af45e-413">これらの 2 つの URL のオリジンは同じです。</span><span class="sxs-lookup"><span data-stu-id="af45e-413">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="af45e-414">これらの URL のオリジンは、前の 2 つの URL とは異なります。</span><span class="sxs-lookup"><span data-stu-id="af45e-414">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="af45e-415">`https://example.net`&ndash;異なるドメイン</span><span class="sxs-lookup"><span data-stu-id="af45e-415">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="af45e-416">`https://www.example.com/foo.html`&ndash;異なるサブドメイン</span><span class="sxs-lookup"><span data-stu-id="af45e-416">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="af45e-417">`http://example.com/foo.html`&ndash;異なるスキーム</span><span class="sxs-lookup"><span data-stu-id="af45e-417">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="af45e-418">`https://example.com:9000/foo.html`&ndash;異なるポート</span><span class="sxs-lookup"><span data-stu-id="af45e-418">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="af45e-419">オリジンを比較する際に、このポートは考慮されません。</span><span class="sxs-lookup"><span data-stu-id="af45e-419">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="af45e-420">名前付きポリシーとミドルウェアを使用した CORS</span><span class="sxs-lookup"><span data-stu-id="af45e-420">CORS with named policy and middleware</span></span>

<span data-ttu-id="af45e-421">CORS ミドルウェアは、クロスオリジン要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="af45e-421">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="af45e-422">次のコードでは、指定したオリジンを持つアプリ全体の CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-422">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="af45e-423">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="af45e-423">The preceding code:</span></span>

* <span data-ttu-id="af45e-424">ポリシー名を "myAllowSpecificOrigins"\_に設定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-424">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="af45e-425">ポリシー名は任意です。</span><span class="sxs-lookup"><span data-stu-id="af45e-425">The policy name is arbitrary.</span></span>
* <span data-ttu-id="af45e-426">拡張メソッド<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>を呼び出して、CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-426">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="af45e-427"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-427">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="af45e-428">ラムダはオブジェクト<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="af45e-428">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="af45e-429">などの`WithOrigins`[構成オプション](#cors-policy-options)については、この記事の後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-429">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="af45e-430">メソッド<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>呼び出しは、アプリのサービス コンテナーに CORS サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="af45e-430">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="af45e-431">詳細については、このドキュメントの[CORS ポリシー オプション](#cpo)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-431">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="af45e-432">メソッド<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>は、次のコードに示すように、メソッドをチェインできます。</span><span class="sxs-lookup"><span data-stu-id="af45e-432">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="af45e-433">注意: URL**not**に末尾のスラッシュ (`/`) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="af45e-433">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="af45e-434">URL が で`/`終わる場合、比較`false`は返され、ヘッダーは返されません。</span><span class="sxs-lookup"><span data-stu-id="af45e-434">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="af45e-435">次のコードでは、CORS ミドルウェアを介してすべてのアプリ エンドポイントに CORS ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="af45e-436">注:`UseCors`前に`UseMvc`呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-436">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="af45e-437">ページ/コント ローラー/アクション レベルで CORS ポリシーを適用するには[、Razor ページ、コント ローラー、およびアクション メソッド](#ecors)で CORS を有効にするを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-437">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="af45e-438">上記のコードと同様のコードのテスト手順については、「[テスト CORS」](#test)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-438">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="af45e-439">属性を使用した CORS の有効化</span><span class="sxs-lookup"><span data-stu-id="af45e-439">Enable CORS with attributes</span></span>

<span data-ttu-id="af45e-440">[ &lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性は、CORS をグローバルに適用する代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-440">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="af45e-441">この`[EnableCors]`属性は、すべての終点ではなく、選択した終点に対して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-441">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="af45e-442">既定`[EnableCors]`のポリシーを指定し、`[EnableCors("{Policy String}")]`ポリシーを指定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-442">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="af45e-443">属性`[EnableCors]`は、次の場合に適用できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-443">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="af45e-444">カミソリページ`PageModel`</span><span class="sxs-lookup"><span data-stu-id="af45e-444">Razor Page `PageModel`</span></span>
* <span data-ttu-id="af45e-445">コントローラー</span><span class="sxs-lookup"><span data-stu-id="af45e-445">Controller</span></span>
* <span data-ttu-id="af45e-446">コントローラアクションメソッド</span><span class="sxs-lookup"><span data-stu-id="af45e-446">Controller action method</span></span>

<span data-ttu-id="af45e-447">属性を使用して、コントローラ/ページモデル/アクションに異なるポリシー`[EnableCors]`を適用できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-447">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="af45e-448">属性が`[EnableCors]`コントローラ/ページ モデル/アクション メソッドに適用され、CORS がミドルウェアで有効になっている場合、***両方***のポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-448">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="af45e-449">ポリシーを組み合わせることはお勧め***しません***。</span><span class="sxs-lookup"><span data-stu-id="af45e-449">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="af45e-450">属性または`[EnableCors]`ミドルウェアを使用します。**not both**</span><span class="sxs-lookup"><span data-stu-id="af45e-450">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="af45e-451">を使用`[EnableCors]`する場合は、既定のポリシーを定義**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="af45e-451">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="af45e-452">次のコードでは、各メソッドに異なるポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-452">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="af45e-453">次のコードでは、CORS のデフォルト ポリシーと`"AnotherPolicy"`名前のポリシーを作成します。</span><span class="sxs-lookup"><span data-stu-id="af45e-453">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="af45e-454">CORS を無効にする</span><span class="sxs-lookup"><span data-stu-id="af45e-454">Disable CORS</span></span>

<span data-ttu-id="af45e-455">[ &lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性は、コントローラ/ページ モデル/アクションの CORS を無効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-455">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="af45e-456">CORS ポリシー オプション</span><span class="sxs-lookup"><span data-stu-id="af45e-456">CORS policy options</span></span>

<span data-ttu-id="af45e-457">このセクションでは、CORS ポリシーで設定できるさまざまなオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-457">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="af45e-458">許可された原点を設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-458">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="af45e-459">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-459">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="af45e-460">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-460">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="af45e-461">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-461">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="af45e-462">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="af45e-462">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="af45e-463">プリフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-463">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="af45e-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>が で`Startup.ConfigureServices`呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="af45e-465">いくつかのオプションについては、[最初に「CORS の仕組み](#how-cors)」のセクションを読んでも役に立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-465">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="af45e-466">許可された原点を設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-466">Set the allowed origins</span></span>

<span data-ttu-id="af45e-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;任意のスキーム ( または`http``https`) を持つすべてのオリジンからの CORS 要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="af45e-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="af45e-468">`AllowAnyOrigin`*どのウェブサイトも*アプリにクロスオリジン要求を行うことができるため、安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-468">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="af45e-469">指定され`AllowAnyOrigin`、`AllowCredentials`安全でない構成であり、クロスサイトリクエストフォージェリにつながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-469">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="af45e-470">セキュリティで保護されたアプリの場合、クライアントがサーバー リソースにアクセスする権限を持つ必要がある場合は、発信元の正確な一覧を指定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-470">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="af45e-471">`AllowAnyOrigin`はプリフライト要求とヘッダー`Access-Control-Allow-Origin`に影響します。</span><span class="sxs-lookup"><span data-stu-id="af45e-471">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="af45e-472">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-472">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="af45e-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;ポリシーの<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>プロパティを、オリジンが許可されているかどうかを評価するときに、設定されたワイルドカード ドメインとオリジンが一致するように設定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="af45e-474">許可される HTTP メソッドを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-474">Set the allowed HTTP methods</span></span>

<span data-ttu-id="af45e-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="af45e-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="af45e-476">任意の HTTP メソッドを許可します。</span><span class="sxs-lookup"><span data-stu-id="af45e-476">Allows any HTTP method:</span></span>
* <span data-ttu-id="af45e-477">プリフライト要求とヘッダーに`Access-Control-Allow-Methods`影響します。</span><span class="sxs-lookup"><span data-stu-id="af45e-477">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="af45e-478">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="af45e-479">許可された要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-479">Set the allowed request headers</span></span>

<span data-ttu-id="af45e-480">特定のヘッダーを CORS 要求で送信できるようにするには *、author 要求ヘッダー*と呼<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>びます。</span><span class="sxs-lookup"><span data-stu-id="af45e-480">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="af45e-481">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-481">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="af45e-482">この設定は、プリフライト要求と`Access-Control-Request-Headers`ヘッダーに影響します。</span><span class="sxs-lookup"><span data-stu-id="af45e-482">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="af45e-483">詳細については、「[プリフライトリクエスト](#preflight-requests)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-483">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="af45e-484">CORS ミドルウェアでは、CorsPolicy.Headers で構成されている値`Access-Control-Request-Headers`に関係なく、常に 4 つのヘッダーを送信できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-484">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="af45e-485">このヘッダーのリストには、次の項目が含まれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-485">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="af45e-486">たとえば、次のように構成されたアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="af45e-486">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="af45e-487">CORS ミドルウェアは、常にホワイトリストに登録されているため`Content-Language`、次の要求ヘッダーを使用してプリフライト要求に正常に応答します。</span><span class="sxs-lookup"><span data-stu-id="af45e-487">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="af45e-488">公開された応答ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-488">Set the exposed response headers</span></span>

<span data-ttu-id="af45e-489">既定では、ブラウザーは、すべての応答ヘッダーをアプリに公開しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-489">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="af45e-490">詳細については、「 [W3C クロス オリジン リソース共有 (用語集): 単純な応答ヘッダー](https://www.w3.org/TR/cors/#simple-response-header)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-490">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="af45e-491">デフォルトで使用可能な応答ヘッダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="af45e-491">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="af45e-492">CORS 仕様では、これらのヘッダーの*単純な応答ヘッダーを*呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-492">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="af45e-493">他のヘッダーをアプリで使用できるようにするには、次の<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="af45e-493">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="af45e-494">クロスオリジン要求の資格情報</span><span class="sxs-lookup"><span data-stu-id="af45e-494">Credentials in cross-origin requests</span></span>

<span data-ttu-id="af45e-495">資格情報には、CORS 要求での特別な処理が必要です。</span><span class="sxs-lookup"><span data-stu-id="af45e-495">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="af45e-496">既定では、ブラウザーはクロスオリジン要求を含む資格情報を送信しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-496">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="af45e-497">資格情報には、Cookie と HTTP 認証スキームが含まれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-497">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="af45e-498">クロスオリジン要求を使用して資格情報を送信するには、クライアントが`XMLHttpRequest.withCredentials``true`に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-498">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="af45e-499">直接`XMLHttpRequest`使用:</span><span class="sxs-lookup"><span data-stu-id="af45e-499">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="af45e-500">jQuery を使用する:</span><span class="sxs-lookup"><span data-stu-id="af45e-500">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="af45e-501">フェッチ[API](https://developer.mozilla.org/docs/Web/API/Fetch_API)の使用 :</span><span class="sxs-lookup"><span data-stu-id="af45e-501">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="af45e-502">サーバーは資格情報を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-502">The server must allow the credentials.</span></span> <span data-ttu-id="af45e-503">クロスオリジンの資格情報を許可するには、次<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>の呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="af45e-503">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="af45e-504">HTTP 応答には`Access-Control-Allow-Credentials`ヘッダーが含まれており、サーバーがクロスオリジン要求の資格情報を許可することをブラウザーに伝えます。</span><span class="sxs-lookup"><span data-stu-id="af45e-504">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="af45e-505">ブラウザーが資格情報を送信しても、応答に有効な`Access-Control-Allow-Credentials`ヘッダーが含まれていない場合、ブラウザーはアプリに対する応答を公開せず、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="af45e-505">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="af45e-506">クロスオリジンの資格情報を許可することは、セキュリティ上のリスクです。</span><span class="sxs-lookup"><span data-stu-id="af45e-506">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="af45e-507">別のドメインの Web サイトは、ユーザーの知らないうちに、サインインしているユーザーの資格情報をユーザーの代わりにアプリに送信できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-507">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="af45e-508">CORS 仕様では、ヘッダーが存在する場合`"*"`、(すべての起点) に原点`Access-Control-Allow-Credentials`を設定することは無効であるとも述べています。</span><span class="sxs-lookup"><span data-stu-id="af45e-508">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="af45e-509">プリフライトリクエスト</span><span class="sxs-lookup"><span data-stu-id="af45e-509">Preflight requests</span></span>

<span data-ttu-id="af45e-510">一部の CORS 要求では、ブラウザーは実際の要求を行う前に追加の要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="af45e-510">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="af45e-511">この要求は *、プリフライト要求*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-511">This request is called a *preflight request*.</span></span> <span data-ttu-id="af45e-512">次の条件に該当する場合、ブラウザはプリフライトリクエストをスキップできます。</span><span class="sxs-lookup"><span data-stu-id="af45e-512">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="af45e-513">要求メソッドは、GET、HEAD、または POST です。</span><span class="sxs-lookup"><span data-stu-id="af45e-513">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="af45e-514">アプリ`Accept`は、 、 、 `Accept-Language`、 、、`Content-Language``Content-Type`または`Last-Event-ID`以外の要求ヘッダーを設定しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-514">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="af45e-515">ヘッダー`Content-Type`が設定されている場合、次のいずれかの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-515">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="af45e-516">クライアント要求に設定された要求ヘッダーのルールは、アプリケーションがオブジェクトを呼び出`setRequestHeader`すことによって設定するヘッダーに`XMLHttpRequest`適用されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-516">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="af45e-517">CORS 仕様では、これらのヘッダー*の作成者要求ヘッダーを*呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-517">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="af45e-518">ルールは、 、 、 など、`User-Agent``Host`ブラウザで設定できるヘッダーには適用されません。 `Content-Length`</span><span class="sxs-lookup"><span data-stu-id="af45e-518">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="af45e-519">プリフライト要求の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-519">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="af45e-520">プレフライト要求では、HTTP OPTIONS メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-520">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="af45e-521">これには、次の 2 つの特別なヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-521">It includes two special headers:</span></span>

* <span data-ttu-id="af45e-522">`Access-Control-Request-Method`: 実際の要求に使用される HTTP メソッド。</span><span class="sxs-lookup"><span data-stu-id="af45e-522">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="af45e-523">`Access-Control-Request-Headers`: アプリが実際の要求に設定する要求ヘッダーの一覧。</span><span class="sxs-lookup"><span data-stu-id="af45e-523">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="af45e-524">前述のように、ブラウザーが設定するヘッダーは含みません`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="af45e-524">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="af45e-525">適切なポリシーで CORS が有効になっている場合、通常、ASP.NETコアは CORS プリフライト要求に自動的に応答します。</span><span class="sxs-lookup"><span data-stu-id="af45e-525">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="af45e-526">[プリフライト要求の場合は[HttpOptions] 属性を](#pro)参照してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-526">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="af45e-527">CORS プリフライト要求には、実際`Access-Control-Request-Headers`の要求と共に送信されるヘッダーをサーバーに示すヘッダーが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-527">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="af45e-528">特定のヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-528">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="af45e-529">すべての作成者要求ヘッダーを許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-529">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="af45e-530">ブラウザは、設定方法に完全に一貫`Access-Control-Request-Headers`しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-530">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="af45e-531">ヘッダーを ( または を使用`"*"`<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>) 以外に設定する場合は`Accept`、 `Content-Type`、 `Origin`、および 、 、および をサポートするカスタム ヘッダーを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-531">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="af45e-532">次に、プリフライト要求に対する応答の例を示します (サーバーが要求を許可すると仮定します)。</span><span class="sxs-lookup"><span data-stu-id="af45e-532">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="af45e-533">応答には、`Access-Control-Allow-Methods`許可されたメソッドと、許可されたヘッダーをリストする`Access-Control-Allow-Headers`ヘッダー (オプション) を示すヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="af45e-533">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="af45e-534">プリフライト要求が成功すると、ブラウザは実際のリクエストを送信します。</span><span class="sxs-lookup"><span data-stu-id="af45e-534">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="af45e-535">プリフライト要求が拒否された場合、アプリは*200 OK*応答を返しますが、CORS ヘッダーは返しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-535">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="af45e-536">したがって、ブラウザーは、クロスオリジン要求を試行しません。</span><span class="sxs-lookup"><span data-stu-id="af45e-536">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="af45e-537">プリフライトの有効期限を設定する</span><span class="sxs-lookup"><span data-stu-id="af45e-537">Set the preflight expiration time</span></span>

<span data-ttu-id="af45e-538">ヘッダー`Access-Control-Max-Age`は、プリフライト要求に対する応答をキャッシュできる時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-538">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="af45e-539">このヘッダーを設定するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af45e-539">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="af45e-540">CORS のしくみ</span><span class="sxs-lookup"><span data-stu-id="af45e-540">How CORS works</span></span>

<span data-ttu-id="af45e-541">このセクションでは、HTTP メッセージのレベルで[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)要求で何が起こるかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="af45e-541">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="af45e-542">CORS はセキュリティ機能**ではありません**。</span><span class="sxs-lookup"><span data-stu-id="af45e-542">CORS is **not** a security feature.</span></span> <span data-ttu-id="af45e-543">CORS は、サーバーが同じ発信元ポリシーを緩和できるようにする W3C 標準です。</span><span class="sxs-lookup"><span data-stu-id="af45e-543">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="af45e-544">たとえば、悪意のあるアクターがサイトに対して[クロスサイト スクリプティング (XSS) を](xref:security/cross-site-scripting)使用し、CORS 対応サイトに対してクロスサイト要求を実行して情報を盗む可能性があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-544">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="af45e-545">API は CORS を許可することで安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-545">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="af45e-546">CORS を強制するのはクライアント (ブラウザー) の管理です。</span><span class="sxs-lookup"><span data-stu-id="af45e-546">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="af45e-547">サーバーは要求を実行し、応答を返します、それはエラーを返し、応答をブロックするクライアントです。</span><span class="sxs-lookup"><span data-stu-id="af45e-547">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="af45e-548">たとえば、次のツールのどれでもサーバーの応答を表示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-548">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="af45e-549">Fiddler</span><span class="sxs-lookup"><span data-stu-id="af45e-549">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="af45e-550">Postman</span><span class="sxs-lookup"><span data-stu-id="af45e-550">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="af45e-551">.NET Http クライアント</span><span class="sxs-lookup"><span data-stu-id="af45e-551">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="af45e-552">アドレス バーに URL を入力して Web ブラウザを表示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-552">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="af45e-553">これは、ブラウザがクロスオリジン[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)または[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)要求を実行することを許可するサーバーの方法です。</span><span class="sxs-lookup"><span data-stu-id="af45e-553">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="af45e-554">ブラウザ (CORS を使用しない) は、クロスオリジン要求を行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="af45e-554">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="af45e-555">CORS より前では[、JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)はこの制限を回避するために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="af45e-555">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="af45e-556">JSONP は XHR を使用せず、`<script>`タグを使用して応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="af45e-556">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="af45e-557">スクリプトは、クロスオリジンでロードすることができます。</span><span class="sxs-lookup"><span data-stu-id="af45e-557">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="af45e-558">[CORS 仕様](https://www.w3.org/TR/cors/)では、クロスオリジン要求を有効にするいくつかの新しい HTTP ヘッダーが導入されました。</span><span class="sxs-lookup"><span data-stu-id="af45e-558">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="af45e-559">ブラウザーが CORS をサポートしている場合、クロスオリジン要求に対してこれらのヘッダーが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-559">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="af45e-560">カスタム JavaScript コードは、CORS を有効にする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-560">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="af45e-561">次に、クロスオリジン要求の例を示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-561">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="af45e-562">ヘッダー`Origin`は、要求を行っているサイトのドメインを提供します。</span><span class="sxs-lookup"><span data-stu-id="af45e-562">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="af45e-563">ヘッダー`Origin`は必須であり、ホストとは異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-563">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="af45e-564">サーバーが要求を許可する場合、応答の`Access-Control-Allow-Origin`ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="af45e-564">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="af45e-565">このヘッダーの値は、要求の`Origin`ヘッダーと一致するか、ワイルドカード値`"*"`で、任意のオリジンが許可されていることを意味します。</span><span class="sxs-lookup"><span data-stu-id="af45e-565">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="af45e-566">応答に`Access-Control-Allow-Origin`ヘッダーが含まれていない場合、クロスオリジン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="af45e-566">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="af45e-567">具体的には、ブラウザーは要求を拒否します。</span><span class="sxs-lookup"><span data-stu-id="af45e-567">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="af45e-568">サーバーが成功した応答を返した場合でも、ブラウザーはクライアント アプリで応答を使用できません。</span><span class="sxs-lookup"><span data-stu-id="af45e-568">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="af45e-569">CORS のテスト</span><span class="sxs-lookup"><span data-stu-id="af45e-569">Test CORS</span></span>

<span data-ttu-id="af45e-570">CORS をテストするには:</span><span class="sxs-lookup"><span data-stu-id="af45e-570">To test CORS:</span></span>

1. <span data-ttu-id="af45e-571">[API プロジェクトを作成する](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="af45e-571">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="af45e-572">または、[サンプルをダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)することもできます。</span><span class="sxs-lookup"><span data-stu-id="af45e-572">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="af45e-573">このドキュメントの方法のいずれかを使用して CORS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="af45e-573">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="af45e-574">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-574">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="af45e-575">`WithOrigins("https://localhost:<port>");`ダウンロード サンプル[コード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)に似たサンプル アプリのテストにのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="af45e-575">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="af45e-576">Web アプリ プロジェクト (Razor ページまたは MVC) を作成します。</span><span class="sxs-lookup"><span data-stu-id="af45e-576">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="af45e-577">サンプルでは、Razor ページを使用します。</span><span class="sxs-lookup"><span data-stu-id="af45e-577">The sample uses Razor Pages.</span></span> <span data-ttu-id="af45e-578">API プロジェクトと同じソリューションで Web アプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="af45e-578">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="af45e-579">次の強調表示されたコードを*Index.cshtml*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="af45e-579">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="af45e-580">上記のコードで、デプロイされた`url: 'https://<web app>.azurewebsites.net/api/values/1',`アプリの URL で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="af45e-580">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="af45e-581">API プロジェクトをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="af45e-581">Deploy the API project.</span></span> <span data-ttu-id="af45e-582">たとえば、 [Azure にデプロイします](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="af45e-582">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="af45e-583">デスクトップから Razor ページまたは MVC アプリを実行し、[**テスト**] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="af45e-583">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="af45e-584">F12 ツールを使用して、エラー メッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="af45e-584">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="af45e-585">ローカルホストのオリジンをアプリ`WithOrigins`から削除し、アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="af45e-585">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="af45e-586">または、別のポートでクライアント アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="af45e-586">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="af45e-587">たとえば、Visual Studio から実行します。</span><span class="sxs-lookup"><span data-stu-id="af45e-587">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="af45e-588">クライアント アプリでテストします。</span><span class="sxs-lookup"><span data-stu-id="af45e-588">Test with the client app.</span></span> <span data-ttu-id="af45e-589">CORS のエラーはエラーを返しますが、エラー メッセージは JavaScript で使用できません。</span><span class="sxs-lookup"><span data-stu-id="af45e-589">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="af45e-590">F12 ツールのコンソール タブを使用して、エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="af45e-590">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="af45e-591">ブラウザーによっては、次のようなエラー (F12 ツール コンソール) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="af45e-591">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="af45e-592">マイクロソフトエッジを使用する:</span><span class="sxs-lookup"><span data-stu-id="af45e-592">Using Microsoft Edge:</span></span>

     <span data-ttu-id="af45e-593">**SEC7120: [CORS]`https://localhost:44375`オリジンが、`https://localhost:44375`クロスオリジンリソースのアクセス制御許可-オリジン応答ヘッダーで見つかりませんでした。`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="af45e-593">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="af45e-594">クロムの使用:</span><span class="sxs-lookup"><span data-stu-id="af45e-594">Using Chrome:</span></span>

     <span data-ttu-id="af45e-595">**元`https://webapi.azurewebsites.net/api/values/1``https://localhost:44375`の XMLHttpRequest へのアクセスが CORS ポリシーによってブロックされました: 要求されたリソースに 'アクセス制御-許可-元のヘッダー' が存在しません。**</span><span class="sxs-lookup"><span data-stu-id="af45e-595">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="af45e-596">CORS 対応のエンドポイントは[、Fiddler](https://www.telerik.com/fiddler)や[Postman](https://www.getpostman.com/)などのツールを使用してテストできます。</span><span class="sxs-lookup"><span data-stu-id="af45e-596">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="af45e-597">ツールを使用する場合、ヘッダーで指定された要求の発信`Origin`元は、要求を受け取るホストと異なる必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-597">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="af45e-598">要求がヘッダーの値に基づいて*クロスオリジン*でない場合: `Origin`</span><span class="sxs-lookup"><span data-stu-id="af45e-598">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="af45e-599">要求を処理するために CORS ミドルウェアは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="af45e-599">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="af45e-600">CORS ヘッダーは応答で返されません。</span><span class="sxs-lookup"><span data-stu-id="af45e-600">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="af45e-601">IIS の CORS</span><span class="sxs-lookup"><span data-stu-id="af45e-601">CORS in IIS</span></span>

<span data-ttu-id="af45e-602">IIS に展開する場合、サーバーが匿名アクセスを許可するように構成されていない場合は、Windows 認証の前に CORS を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-602">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="af45e-603">このシナリオをサポートするには[、IIS CORS モジュール](https://www.iis.net/downloads/microsoft/iis-cors-module)をインストールし、アプリ用に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af45e-603">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af45e-604">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="af45e-604">Additional resources</span></span>

* [<span data-ttu-id="af45e-605">クロスオリジンリソース共有(CORS)</span><span class="sxs-lookup"><span data-stu-id="af45e-605">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="af45e-606">IIS CORS モジュールの概要</span><span class="sxs-lookup"><span data-stu-id="af45e-606">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
