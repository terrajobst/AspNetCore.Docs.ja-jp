---
title: ASP.NET Core Web API の応答データの書式設定
author: ardalis
description: ASP.NET Core Web API で応答データを書式設定する方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 8/22/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: e503df3d81efbb2800503c0cb4ff5ae093b6e1ac
ms.sourcegitcommit: 023495344053dc59115c80538f0ece935e7490a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2019
ms.locfileid: "71592348"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="6c44f-103">ASP.NET Core Web API の応答データの書式設定</span><span class="sxs-lookup"><span data-stu-id="6c44f-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="6c44f-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6c44f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6c44f-105">ASP.NET Core MVC では、応答データの書式設定がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="6c44f-106">応答データは、特定の形式を使用して、またはクライアントが要求した形式に応じて書式設定できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="6c44f-107">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="6c44f-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="6c44f-108">書式固有アクションの結果</span><span class="sxs-lookup"><span data-stu-id="6c44f-108">Format-specific Action Results</span></span>

<span data-ttu-id="6c44f-109">アクションの結果には、<xref:Microsoft.AspNetCore.Mvc.JsonResult> や <xref:Microsoft.AspNetCore.Mvc.ContentResult> のように、特定の形式に固有となる型があります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="6c44f-110">アクションでは、クライアントの設定に関係なく、特定の形式で書式設定された結果を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="6c44f-111">たとえば、`JsonResult` を返すと、JSON 形式のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="6c44f-112">`ContentResult` または文字列を返すと、プレーンテキスト形式の文字列データが返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="6c44f-113">アクションが特定の型を返す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6c44f-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="6c44f-114">ASP.NET Core によって、すべてのオブジェクトの戻り値がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="6c44f-115">型が <xref:Microsoft.AspNetCore.Mvc.IActionResult> ではないオブジェクトを返すアクションからの結果は、適切な <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 実装を利用してシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="6c44f-116">詳細については、<xref:web-api/action-return-types> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c44f-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="6c44f-117">組み込みヘルパー メソッド <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> では、JSON 形式のデータが返されます。[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="6c44f-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="6c44f-118">サンプル ダウンロードでは作成者の一覧が返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="6c44f-119">F12 ブラウザー開発者ツールまたは [Postman](https://www.getpostman.com/tools) と前のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="6c44f-120">**content-type:** `application/json; charset=utf-8` を含む応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="6c44f-121">要求ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-121">The request headers are displayed.</span></span> <span data-ttu-id="6c44f-122">たとえば、`Accept` ヘッダーです。</span><span class="sxs-lookup"><span data-stu-id="6c44f-122">For example, the `Accept` header.</span></span> <span data-ttu-id="6c44f-123">`Accept` ヘッダーは前のコードでは無視されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="6c44f-124">プレーンテキストで書式設定されたデータを返すには、<xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> と <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> ヘルパーを使用します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="6c44f-125">前のコードで、返される `Content-Type` は `text/plain` です。</span><span class="sxs-lookup"><span data-stu-id="6c44f-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="6c44f-126">文字列を返すと、`text/plain` の `Content-Type` が送られます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="6c44f-127">戻り値の型が複数あるアクションの場合は、`IActionResult` を返します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="6c44f-128">たとえば、実行された操作の結果に基づいて、さまざまな HTTP 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="6c44f-129">コンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="6c44f-129">Content negotiation</span></span>

<span data-ttu-id="6c44f-130">クライアントにより [Accept ヘッダー](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)が指定されると、コンテンツ ネゴシエーションが発生します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="6c44f-131">ASP.NET Core で使用される既定の形式は [JSON](https://json.org/) です。</span><span class="sxs-lookup"><span data-stu-id="6c44f-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="6c44f-132">コンテンツ ネゴシエーションの説明を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-132">Content negotiation is:</span></span>

* <span data-ttu-id="6c44f-133"><xref:Microsoft.AspNetCore.Mvc.ObjectResult> によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="6c44f-134">ヘルパー メソッドから返されるステータス コード固有のアクションの結果に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="6c44f-135">アクションの結果のヘルパー メソッドは `ObjectResult` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="6c44f-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="6c44f-136">モデルの型類が返されると、戻り値の型は `ObjectResult` になります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="6c44f-137">次のアクション メソッドでは、ヘルパー メソッドの `Ok` と `NotFound` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="6c44f-138">別の形式が要求され、その要求された形式をサーバーが返せる場合を除き、JSON で書式設定された応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-138">A JSON-formatted response is returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="6c44f-139">[Fiddler](https://www.telerik.com/fiddler) や [Postman](https://www.getpostman.com/tools) のようなツールは、`Accept` ヘッダーを設定して戻り値の形式を指定できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` header to specify the return format.</span></span> <span data-ttu-id="6c44f-140">`Accept` に含まれる型がサーバーでサポートされるときは、その型が返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-140">When the `Accept` contains a type the server supports, that type is returned.</span></span>

<span data-ttu-id="6c44f-141">既定では、ASP.NET Core では JSON のみがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-141">By default, ASP.NET Core only supports JSON.</span></span> <span data-ttu-id="6c44f-142">既定値を変更しないアプリの場合は、クライアントの要求に関係なく、JSON で書式設定された応答が常に返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-142">For apps not changing the default, JSON-formatted responses are alway returned regardless of the client request.</span></span> <span data-ttu-id="6c44f-143">フォーマッタの追加方法は次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-143">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="6c44f-144">コントローラー アクションは POCO (単純な従来の CLR オブジェクト) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-144">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="6c44f-145">POCO が返されると、ランタイムはそのオブジェクトをラップする `ObjectResult` を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-145">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="6c44f-146">クライアントは、書式設定されたシリアル化オブジェクトを取得します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-146">The client gets the formatted serialized object.</span></span> <span data-ttu-id="6c44f-147">返されるオブジェクトが `null` の場合は、`204 No Content` という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-147">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="6c44f-148">オブジェクトの型を返す:</span><span class="sxs-lookup"><span data-stu-id="6c44f-148">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="6c44f-149">前のコードでは、有効な作成者エイリアスの要求に対して、`200 OK` という応答と作成者のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-149">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="6c44f-150">無効なエイリアスの要求に対しては、`204 No Content` という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-150">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="6c44f-151">Accept ヘッダー</span><span class="sxs-lookup"><span data-stu-id="6c44f-151">The Accept header</span></span>

<span data-ttu-id="6c44f-152">コンテンツ "*ネゴシエーション*" は、`Accept` ヘッダーが要求に含まれるときに発生します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-152">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="6c44f-153">要求に Accept ヘッダーが含まれるとき、ASP.NET Core は次のように処理します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-153">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="6c44f-154">Accept ヘッダーのメディアの種類を優先順で列挙します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-154">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="6c44f-155">指定された形式のいずれかで応答を生成できるフォーマッタを見つけようとします。</span><span class="sxs-lookup"><span data-stu-id="6c44f-155">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="6c44f-156">クライアントの要求を満たすことができるフォーマッタが見つからない場合は、ASP.NET Core は次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6c44f-156">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="6c44f-157"><xref:Microsoft.AspNetCore.Mvc.MvcOptions> が設定されていた場合は、`406 Not Acceptable` を返します。それ以外の場合は次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6c44f-157">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="6c44f-158">応答を生成できる最初のフォーマッタを見つけようとします。</span><span class="sxs-lookup"><span data-stu-id="6c44f-158">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="6c44f-159">要求された形式に対応するフォーマッタが構成されていない場合、オブジェクトを書式設定できる最初のフォーマッタが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-159">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="6c44f-160">要求に `Accept` ヘッダーが表示されない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-160">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="6c44f-161">オブジェクトを処理できる最初のフォーマッタが使用されて、応答をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-161">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="6c44f-162">ネゴシエーションは発生しません。</span><span class="sxs-lookup"><span data-stu-id="6c44f-162">There isn't any negotiation taking place.</span></span> <span data-ttu-id="6c44f-163">サーバーが返す形式を決定します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-163">The server is determining what format to return.</span></span>

<span data-ttu-id="6c44f-164">Accept ヘッダーに `*/*` が含まれる場合、`RespectBrowserAcceptHeader` が <xref:Microsoft.AspNetCore.Mvc.MvcOptions> で true に設定されていない限り、ヘッダーが無視されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-164">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="6c44f-165">ブラウザーとコンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="6c44f-165">Browsers and content negotiation</span></span>

<span data-ttu-id="6c44f-166">一般的な API クライアントとは異なり、Web ブラウザーは `Accept` ヘッダーを提供します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-166">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="6c44f-167">Web ブラウザーでは、ワイルドカードを含む多くの形式が指定されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-167">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="6c44f-168">既定では、要求がブラウザーから送信されていることをフレームワークが検出すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-168">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="6c44f-169">`Accept` ヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-169">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="6c44f-170">特に構成されていない限り、コンテンツは JSON で返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-170">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="6c44f-171">このため、API を使用するときにブラウザー間でさらに一貫性の高いエクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-171">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="6c44f-172">ブラウザーの Accept ヘッダーを優先するようにアプリを構成するには、<xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-172">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="6c44f-173">フォーマッタの構成</span><span class="sxs-lookup"><span data-stu-id="6c44f-173">Configure formatters</span></span>

<span data-ttu-id="6c44f-174">追加の形式をサポートする必要があるアプリは、適切な NuGet パッケージを追加し、サポートを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-174">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="6c44f-175">入力と出力で別々のフォーマッタがあります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-175">There are separate formatters for input and output.</span></span> <span data-ttu-id="6c44f-176">入力フォーマッタは、[モデル バインド](xref:mvc/models/model-binding)によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-176">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="6c44f-177">出力フォーマッタは、応答の書式設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-177">Output formatters are used to format responses.</span></span> <span data-ttu-id="6c44f-178">カスタム フォーマッタの作成の詳細については、[カスタム フォーマッタ](xref:web-api/advanced/custom-formatters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c44f-178">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="6c44f-179">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="6c44f-179">Add XML format support</span></span>

<span data-ttu-id="6c44f-180"><xref:System.Xml.Serialization.XmlSerializer> を使用して実装された XML フォーマッタは、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-180">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="6c44f-181">前のコードは、`XmlSerializer` を使用して結果をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-181">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="6c44f-182">前のコードを使用する場合、コントローラー メソッドは要求の `Accept` ヘッダーに基づいて適切な形式を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-182">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="6c44f-183">System.Text.Json ベースのフォーマッタを構成する</span><span class="sxs-lookup"><span data-stu-id="6c44f-183">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="6c44f-184">`System.Text.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-184">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="6c44f-185">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-185">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="6c44f-186">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-186">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="6c44f-187">Newtonsoft.Json ベースの JSON 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="6c44f-187">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="6c44f-188">ASP.NET Core 3.0 より前、既定では、`Newtonsoft.Json` パッケージを使用して実装された JSON フォーマッタが使用されていました。</span><span class="sxs-lookup"><span data-stu-id="6c44f-188">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="6c44f-189">ASP.NET Core 3.0 以降、既定の JSON フォーマッタは `System.Text.Json` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="6c44f-189">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="6c44f-190">`Newtonsoft.Json` ベースのフォーマッタと機能のサポートは、[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet パッケージをインストールして `Startup.ConfigureServices` で構成することで利用できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-190">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="6c44f-191">一部の機能は `System.Text.Json` ベースのフォーマッタでうまく動作せず、`Newtonsoft.Json` ベースのフォーマッタの参照が必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-191">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="6c44f-192">アプリが以下の場合には、`Newtonsoft.Json` ベースのフォーマッタの使用を続けます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-192">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="6c44f-193">`Newtonsoft.Json` 属性を使用する。</span><span class="sxs-lookup"><span data-stu-id="6c44f-193">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="6c44f-194">たとえば、`[JsonProperty]` または `[JsonIgnore]` のようにします。</span><span class="sxs-lookup"><span data-stu-id="6c44f-194">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="6c44f-195">シリアル化の設定をカスタマイズする。</span><span class="sxs-lookup"><span data-stu-id="6c44f-195">Customizes the serialization settings.</span></span>
* <span data-ttu-id="6c44f-196">`Newtonsoft.Json` で提供される機能に依存する。</span><span class="sxs-lookup"><span data-stu-id="6c44f-196">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="6c44f-197">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` を構成する。</span><span class="sxs-lookup"><span data-stu-id="6c44f-197">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="6c44f-198">ASP.NET Core 3.0 より前は、`JsonResult.SerializerSettings`が `Newtonsoft.Json` に固有の `JsonSerializerSettings` のインスタンスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-198">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="6c44f-199">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) ドキュメントを生成する。</span><span class="sxs-lookup"><span data-stu-id="6c44f-199">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="6c44f-200">`Newtonsoft.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-200">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="6c44f-201">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-201">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="6c44f-202">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-202">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="6c44f-203">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="6c44f-203">Add XML format support</span></span>

<span data-ttu-id="6c44f-204">XML の書式設定には、[Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="6c44f-204">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="6c44f-205"><xref:System.Xml.Serialization.XmlSerializer> を使用して実装された XML フォーマッタは、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-205">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="6c44f-206">前のコードは、`XmlSerializer` を使用して結果をシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-206">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="6c44f-207">前のコードを使用する場合、コントローラー メソッドは要求の `Accept` ヘッダーに基づいて適切な形式を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-207">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="6c44f-208">形式を指定する</span><span class="sxs-lookup"><span data-stu-id="6c44f-208">Specify a format</span></span>

<span data-ttu-id="6c44f-209">応答の形式を制限するには、[`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) フィルターを適用します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-209">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="6c44f-210">ほとんどの[フィルター](xref:mvc/controllers/filters)と同様に、`[Produces]` をアクション、コントローラー、グローバル スコープに適用できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-210">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="6c44f-211">前の [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) フィルターでは以下の処理が行われます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-211">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="6c44f-212">コントローラー内のすべてのアクションが、JSON で書式設定された応答を返すように強制します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-212">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="6c44f-213">他のフォーマッタが構成されていて、クライアントが別の形式を指定した場合でも、JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-213">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="6c44f-214">詳細については、[フィルター](xref:mvc/controllers/filters)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c44f-214">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="6c44f-215">特殊なケースのフォーマッタ</span><span class="sxs-lookup"><span data-stu-id="6c44f-215">Special case formatters</span></span>

<span data-ttu-id="6c44f-216">一部の特殊なケースが組み込みのフォーマッタで実装されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-216">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="6c44f-217">既定では、戻り値の型 `string` は *text/plain* として書式設定されます (`Accept` ヘッダー経由で要求された場合は *text/html*)。</span><span class="sxs-lookup"><span data-stu-id="6c44f-217">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="6c44f-218">この動作は <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-218">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter>.</span></span> <span data-ttu-id="6c44f-219">フォーマッタは `Configure` メソッドで削除します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-219">Formatters are removed in the `Configure` method.</span></span> <span data-ttu-id="6c44f-220">戻り値の型としてモデル オブジェクトをともなうアクションは、`null` を返すとき、`204 No Content` を返します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-220">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="6c44f-221">この動作は <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-221">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="6c44f-222">次のコードでは、`TextOutputFormatter` と `HttpNoContentOutputFormatter` が削除されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-222">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupTextOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="6c44f-223">`TextOutputFormatter` がないと、戻り値の型 `string` は `406 Not Acceptable` を返します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-223">Without the `TextOutputFormatter`, `string` return types return `406 Not Acceptable`.</span></span> <span data-ttu-id="6c44f-224">XML フォーマッタが存在するときは、`TextOutputFormatter` が削除された場合、戻り値の型 `string` が書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-224">If an XML formatter exists, it formats `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="6c44f-225">`HttpNoContentOutputFormatter` がない場合、構成されているフォーマッタを利用し、null オブジェクトが書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-225">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="6c44f-226">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-226">For example:</span></span>

* <span data-ttu-id="6c44f-227">JSON フォーマッタは、本文が `null` の応答を返します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-227">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="6c44f-228">XML フォーマッタは、属性 `xsi:nil="true"` が設定された空の XML 要素を返します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-228">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="6c44f-229">応答形式の URL マッピング</span><span class="sxs-lookup"><span data-stu-id="6c44f-229">Response format URL mappings</span></span>

<span data-ttu-id="6c44f-230">クライアントは、URL の一部として特定の形式を要求できます。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-230">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="6c44f-231">クエリ文字列またはパスの一部。</span><span class="sxs-lookup"><span data-stu-id="6c44f-231">In the query string or part of the path.</span></span>
* <span data-ttu-id="6c44f-232">.xml または .json など形式固有のファイル拡張子の使用。</span><span class="sxs-lookup"><span data-stu-id="6c44f-232">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="6c44f-233">要求パスからのマッピングは、API で使用されるルートに指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6c44f-233">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="6c44f-234">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="6c44f-234">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="6c44f-235">前のルートを使用すると、要求された形式をオプションのファイル拡張子として指定できます。</span><span class="sxs-lookup"><span data-stu-id="6c44f-235">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="6c44f-236">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) 属性は `RouteData` に形式値がないか確認し、応答が作成されると、応答形式を適切なフォーマッタにマッピングします。</span><span class="sxs-lookup"><span data-stu-id="6c44f-236">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="6c44f-237">ルート</span><span class="sxs-lookup"><span data-stu-id="6c44f-237">Route</span></span>        |             <span data-ttu-id="6c44f-238">フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="6c44f-238">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="6c44f-239">既定の出力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="6c44f-239">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="6c44f-240">JSON フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="6c44f-240">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="6c44f-241">XML フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="6c44f-241">The XML formatter (if configured)</span></span>  |
