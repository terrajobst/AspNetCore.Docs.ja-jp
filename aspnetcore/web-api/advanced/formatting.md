---
title: ASP.NET Core Web API の応答データの書式設定
author: ardalis
description: ASP.NET Core Web API で応答データを書式設定する方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 8bee4efdae5341ddab5bd3aec278ecfef37f0c08
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082357"
---
<!-- DO NOT EDIT BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12077 MERGES -->
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="443b4-103">ASP.NET Core Web API の応答データの書式設定</span><span class="sxs-lookup"><span data-stu-id="443b4-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="443b4-104">作成者: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="443b4-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="443b4-105">ASP.NET Core MVC には、応答データを書式設定するためのサポートが組み込まれています。固定の書式を利用するか、クライアントの仕様に合わせます。</span><span class="sxs-lookup"><span data-stu-id="443b4-105">ASP.NET Core MVC has built-in support for formatting response data, using fixed formats or in response to client specifications.</span></span>

<span data-ttu-id="443b4-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="443b4-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="443b4-107">書式固有アクションの結果</span><span class="sxs-lookup"><span data-stu-id="443b4-107">Format-Specific Action Results</span></span>

<span data-ttu-id="443b4-108">アクションの結果には、`JsonResult` や `ContentResult` のように、特定の形式に固有となる型があります。</span><span class="sxs-lookup"><span data-stu-id="443b4-108">Some action result types are specific to a particular format, such as `JsonResult` and `ContentResult`.</span></span> <span data-ttu-id="443b4-109">アクションは、常に特定の方法で書式設定された結果を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="443b4-109">Actions can return specific results that are always formatted in a particular manner.</span></span> <span data-ttu-id="443b4-110">たとえば、`JsonResult` を返すとき、クライアントの設定に関係なく、JSON で書式設定されたデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-110">For example, returning a `JsonResult` will return JSON-formatted data, regardless of client preferences.</span></span> <span data-ttu-id="443b4-111">同様に、`ContentResult` を返すとき、プレーンテキストで書式設定された文字列データが返されます (単純に文字列が返されます)。</span><span class="sxs-lookup"><span data-stu-id="443b4-111">Likewise, returning a `ContentResult` will return plain-text-formatted string data (as will simply returning a string).</span></span>

> [!NOTE]
> <span data-ttu-id="443b4-112">特定の型を返すためにアクションは必要ありません。MVC ではあらゆるオブジェクト戻り値を利用できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-112">An action isn't required to return any particular type; MVC supports any object return value.</span></span> <span data-ttu-id="443b4-113">アクションが `IActionResult` 実装を返し、コントローラーが `Controller` から継承する場合、開発者は選択肢の多くに対応するさまざまなヘルパー メソッドを利用できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-113">If an action returns an `IActionResult` implementation and the controller inherits from `Controller`, developers have many helper methods corresponding to many of the choices.</span></span> <span data-ttu-id="443b4-114">型が `IActionResult` ではないオブジェクトを返すアクションからの結果は、適切な `IOutputFormatter` 実装を利用してシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-114">Results from actions that return objects that are not `IActionResult` types will be serialized using the appropriate `IOutputFormatter` implementation.</span></span>

<span data-ttu-id="443b4-115">`Controller` 基底クラスから継承するコントローラーから特定の書式でデータを返すには、組み込みのヘルパー メソッド `Json` を使用して JSON を返し、`Content` を使用してプレーンテキストを返します。</span><span class="sxs-lookup"><span data-stu-id="443b4-115">To return data in a specific format from a controller that inherits from the `Controller` base class, use the built-in helper method `Json` to return JSON and `Content` for plain text.</span></span> <span data-ttu-id="443b4-116">アクション メソッドでは、特定の結果の型を返す必要があります (`JsonResult` や `IActionResult` など)。</span><span class="sxs-lookup"><span data-stu-id="443b4-116">Your action method should return either the specific result type (for instance, `JsonResult`) or `IActionResult`.</span></span>

<span data-ttu-id="443b4-117">JSON で書式設定されたデータを返す:</span><span class="sxs-lookup"><span data-stu-id="443b4-117">Returning JSON-formatted data:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

<span data-ttu-id="443b4-118">このアクションからのサンプル応答:</span><span class="sxs-lookup"><span data-stu-id="443b4-118">Sample response from this action:</span></span>

![Microsoft Edge の開発者ツールの [ネットワーク] タブ。応答のコンテンツの種類が application/json です。](formatting/_static/json-response.png)

<span data-ttu-id="443b4-120">応答のコンテンツの種類が `application/json` であることに注目してください。ネットワーク要求の一覧と [応答ヘッダー] セクションの両方に表示されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-120">Note that the content type of the response is `application/json`, shown both in the list of network requests and in the Response Headers section.</span></span> <span data-ttu-id="443b4-121">また、ブラウザー (この場合、Microsoft Edge) により [応答ヘッダー] セクションの Accept ヘッダーに表示されるオプションの一覧に注目してください。</span><span class="sxs-lookup"><span data-stu-id="443b4-121">Also note the list of options presented by the browser (in this case, Microsoft Edge) in the Accept header in the Request Headers section.</span></span> <span data-ttu-id="443b4-122">現在の手法ではこのヘッダーが無視されます。無視しない方法について以下で説明します。</span><span class="sxs-lookup"><span data-stu-id="443b4-122">The current technique is ignoring this header; obeying it is discussed below.</span></span>

<span data-ttu-id="443b4-123">プレーンテキストで書式設定されたデータを返すには、`ContentResult` と `Content` ヘルパーを使用します。</span><span class="sxs-lookup"><span data-stu-id="443b4-123">To return plain text formatted data, use `ContentResult` and the `Content` helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

<span data-ttu-id="443b4-124">このアクションからの応答:</span><span class="sxs-lookup"><span data-stu-id="443b4-124">A response from this action:</span></span>

![Microsoft Edge の開発者ツールの [ネットワーク] タブ。応答のコンテンツの種類が text/plain です。](formatting/_static/text-response.png)

<span data-ttu-id="443b4-126">この場合、返される `Content-Type` は `text/plain` です。</span><span class="sxs-lookup"><span data-stu-id="443b4-126">Note in this case the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="443b4-127">応答の型として文字列を使用する方法でもこれと同じ動作が得られます。</span><span class="sxs-lookup"><span data-stu-id="443b4-127">You can also achieve this same behavior using just a string response type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> <span data-ttu-id="443b4-128">戻り値の型やオプションを複数ともなう重要なアクションの場合 (実行された操作の結果に基づき、さまざまな HTTP ステータス コードが生成されるなど)、戻り値の型として `IActionResult` を優先します。</span><span class="sxs-lookup"><span data-stu-id="443b4-128">For non-trivial actions with multiple return types or options (for example, different HTTP status codes based on the result of operations performed), prefer `IActionResult` as the return type.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="443b4-129">コンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="443b4-129">Content Negotiation</span></span>

<span data-ttu-id="443b4-130">クライアントにより [Accept ヘッダー](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)が指定されると、コンテンツ ネゴシエーション (省略形は *conneg*) が発生します。</span><span class="sxs-lookup"><span data-stu-id="443b4-130">Content negotiation (*conneg* for short) occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="443b4-131">ASP.NET Core MVC で使用される既定の書式は JSON です。</span><span class="sxs-lookup"><span data-stu-id="443b4-131">The default format used by ASP.NET Core MVC is JSON.</span></span> <span data-ttu-id="443b4-132">コンテンツ ネゴシエーションは `ObjectResult` によって実装されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-132">Content negotiation is implemented by `ObjectResult`.</span></span> <span data-ttu-id="443b4-133">(すべて `ObjectResult` に基づく) ヘルパー メソッドから返されるステータス コード固有のアクションの結果にも組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="443b4-133">It's also built into the status code specific action results returned from the helper methods (which are all based on `ObjectResult`).</span></span> <span data-ttu-id="443b4-134">モデルの型を返すこともできます (データ転送の型として定義しているクラス)。フレームワークはこれを自動的に `ObjectResult` でラップします。</span><span class="sxs-lookup"><span data-stu-id="443b4-134">You can also return a model type (a class you've defined as your data transfer type) and the framework will automatically wrap it in an `ObjectResult` for you.</span></span>

<span data-ttu-id="443b4-135">次のアクション メソッドでは、ヘルパー メソッドの `Ok` と `NotFound` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-135">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

<span data-ttu-id="443b4-136">別の書式が要求され、その要求された書式をサーバーが返せる場合を除き、JSON で書式設定された応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-136">A JSON-formatted response will be returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="443b4-137">[Fiddler](https://www.telerik.com/fiddler) のようなツールを利用し、Accept ヘッダーを含む要求を作成したり、別の書式を指定したりできます。</span><span class="sxs-lookup"><span data-stu-id="443b4-137">You can use a tool like [Fiddler](https://www.telerik.com/fiddler) to create a request that includes an Accept header and specify another format.</span></span> <span data-ttu-id="443b4-138">その場合、要求された書式で応答を生成できる*フォーマッタ*がサーバーに与えられている場合、クライアントの優先書式で結果が返されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-138">In that case, if the server has a *formatter* that can produce a response in the requested format, the result will be returned in the client-preferred format.</span></span>

![Fiddler コンソールに手動で作成された GET 要求が表示されているのを確認できます。Accept ヘッダー値が application/xml になっています。](formatting/_static/fiddler-composer.png)

<span data-ttu-id="443b4-140">上のスクリーンショットでは、要求の生成に Fiddler Composer が使用されています。`Accept: application/xml` を指定しています。</span><span class="sxs-lookup"><span data-stu-id="443b4-140">In the above screenshot, the Fiddler Composer has been used to generate a request, specifying `Accept: application/xml`.</span></span> <span data-ttu-id="443b4-141">既定では、ASP.NET Core MVC は JSON のみに対応しています。そのため、別の書式が指定されていても、結果は JSON で書式設定されて返されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-141">By default, ASP.NET Core MVC only supports JSON, so even when another format is specified, the result returned is still JSON-formatted.</span></span> <span data-ttu-id="443b4-142">フォーマッタの追加方法は次のセクションで確認します。</span><span class="sxs-lookup"><span data-stu-id="443b4-142">You'll see how to add additional formatters in the next section.</span></span>

<span data-ttu-id="443b4-143">コントローラー アクションでは、POCO (Plain Old CLR Object/単純な従来の CLR オブジェクト) を返すことができます。この場合、ASP.NET Core MVC によって、オブジェクトをラップする `ObjectResult` が自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-143">Controller actions can return POCOs (Plain Old CLR Objects), in which case ASP.NET Core MVC automatically creates an `ObjectResult` for you that wraps the object.</span></span> <span data-ttu-id="443b4-144">書式設定され、シリアル化されたオブジェクトがクライアントに与えられます (JSON 書式が既定ですが、XML やその他の形式を設定できます)。</span><span class="sxs-lookup"><span data-stu-id="443b4-144">The client will get the formatted serialized object (JSON format is the default; you can configure XML or other formats).</span></span> <span data-ttu-id="443b4-145">返されるオブジェクトが `null` の場合、フレームワークは `204 No Content` 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="443b4-145">If the object being returned is `null`, then the framework will return a `204 No Content` response.</span></span>

<span data-ttu-id="443b4-146">オブジェクトの型を返す:</span><span class="sxs-lookup"><span data-stu-id="443b4-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

<span data-ttu-id="443b4-147">このサンプルでは、有効な作成者エイリアスを要求しています。200 OK という応答と作成者のデータが返されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-147">In the sample, a request for a valid author alias will receive a 200 OK response with the author's data.</span></span> <span data-ttu-id="443b4-148">無効なエイリアスを要求すると、204 No Content という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-148">A request for an invalid alias will receive a 204 No Content response.</span></span> <span data-ttu-id="443b4-149">下のスクリーンショットでは、XML 形式と JSON 形式の応答を確認できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-149">Screenshots showing the response in XML and JSON formats are shown below.</span></span>

### <a name="content-negotiation-process"></a><span data-ttu-id="443b4-150">コンテンツ ネゴシエーション プロセス</span><span class="sxs-lookup"><span data-stu-id="443b4-150">Content Negotiation Process</span></span>

<span data-ttu-id="443b4-151">コンテンツ *ネゴシエーション*は、`Accept` ヘッダーが要求に現れるときにのみ発生します。</span><span class="sxs-lookup"><span data-stu-id="443b4-151">Content *negotiation* only takes place if an `Accept` header appears in the request.</span></span> <span data-ttu-id="443b4-152">要求に Accept ヘッダーが含まれると、フレームワークは Accept ヘッダーのメディアの種類を優先順序で列挙し、Accept ヘッダーによって指定される書式の 1 つで応答を生成できるフォーマットを探します。</span><span class="sxs-lookup"><span data-stu-id="443b4-152">When a request contains an accept header, the framework will enumerate the media types in the accept header in preference order and will try to find a formatter that can produce a response in one of the formats specified by the accept header.</span></span> <span data-ttu-id="443b4-153">クライアントの要求を満たせるフォーマッタが見つからない場合、フレームワークは、応答を生成できる最初のフォーマットを見つけようとします (406 Not Acceptable を代わりに返すよう、開発者が `MvcOptions` でオプションを構成していない限り)。</span><span class="sxs-lookup"><span data-stu-id="443b4-153">In case no formatter is found that can satisfy the client's request, the framework will try to find the first formatter that can produce a response (unless the developer has configured the option on `MvcOptions` to return 406 Not Acceptable instead).</span></span> <span data-ttu-id="443b4-154">要求で XML が指定されているが、XML フォーマッタが構成されていない場合、JSON フォーマッタが使用されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-154">If the request specifies XML, but the XML formatter has not been configured, then the JSON formatter will be used.</span></span> <span data-ttu-id="443b4-155">もっと一般的に言うと、要求された書式を提供できるフォーマッタが構成されていない場合、オブジェクトを書式設定できる最初のフォーマッタが使用されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-155">More generally, if no formatter is configured that can provide the requested format, then the first formatter that can format the object is used.</span></span> <span data-ttu-id="443b4-156">ヘッダーが与えられない場合、応答のシリアル化に、返すオブジェクトを処理できる最初のフォーマッタが使用されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-156">If no header is given, the first formatter that can handle the object to be returned will be used to serialize the response.</span></span> <span data-ttu-id="443b4-157">この例では、ネゴシエーションは発生しません。使用する書式はサーバーが決定します。</span><span class="sxs-lookup"><span data-stu-id="443b4-157">In this case, there isn't any negotiation taking place - the server is determining what format it will use.</span></span>

> [!NOTE]
> <span data-ttu-id="443b4-158">Accept ヘッダーに `*/*` が含まれる場合、`RespectBrowserAcceptHeader` が `MvcOptions` で true に設定されていない限り、ヘッダーが無視されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-158">If the Accept header contains `*/*`, the Header will be ignored unless `RespectBrowserAcceptHeader` is set to true on `MvcOptions`.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="443b4-159">ブラウザーとコンテンツ ネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="443b4-159">Browsers and Content Negotiation</span></span>

<span data-ttu-id="443b4-160">一般的な API クライアントとは異なり、Web ブラウザーは、ワイルドカードなど、多大な書式を含む `Accept` ヘッダーを提供することが多々あります。</span><span class="sxs-lookup"><span data-stu-id="443b4-160">Unlike typical API clients, web browsers tend to supply `Accept` headers that include a wide array of formats, including wildcards.</span></span> <span data-ttu-id="443b4-161">既定では、ブラウザーから要求が来ていることをフレームワークが検出すると、フレームワークは `Accept` ヘッダーを無視し、代わりにアプリケーションで構成されている既定の書式でコンテンツを返します (他が設定されていない場合は JSON)。</span><span class="sxs-lookup"><span data-stu-id="443b4-161">By default, when the framework detects that the request is coming from a browser, it will ignore the `Accept` header and instead return the content in the application's configured default format (JSON unless otherwise configured).</span></span> <span data-ttu-id="443b4-162">さまざまなブラウザーで API を利用しても、一貫した操作性が与えられます。</span><span class="sxs-lookup"><span data-stu-id="443b4-162">This provides a more consistent experience when using different browsers to consume APIs.</span></span>

<span data-ttu-id="443b4-163">アプリケーションでブラウザーの Accept ヘッダーを受け付けるようにする場合、MVC の構成の一部として設定できます。*Startup.cs* の `ConfigureServices` メソッドで `RespectBrowserAcceptHeader` を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="443b4-163">If you would prefer your application honor browser accept headers, you can configure this as part of MVC's configuration by setting `RespectBrowserAcceptHeader` to `true` in the `ConfigureServices` method in *Startup.cs*.</span></span>

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a><span data-ttu-id="443b4-164">フォーマッタを構成する</span><span class="sxs-lookup"><span data-stu-id="443b4-164">Configuring Formatters</span></span>

<span data-ttu-id="443b4-165">アプリケーションが既定の JSON 以外の追加書式をサポートしなければならない場合、NuGet パッケージを追加し、サポートするように MVC を構成できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-165">If your application needs to support additional formats beyond the default of JSON, you can add NuGet packages and configure MVC to support them.</span></span> <span data-ttu-id="443b4-166">入力と出力で別々のフォーマッタがあります。</span><span class="sxs-lookup"><span data-stu-id="443b4-166">There are separate formatters for input and output.</span></span> <span data-ttu-id="443b4-167">入力フォーマッタは[モデル バインディング](xref:mvc/models/model-binding)で使用されます。出力フォーマッタは応答の書式設定に使用されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-167">Input formatters are used by [Model Binding](xref:mvc/models/model-binding); output formatters are used to format responses.</span></span> <span data-ttu-id="443b4-168">[カスタム フォーマッタ](xref:web-api/advanced/custom-formatters)を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="443b4-168">You can also configure [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="443b4-169">System.Text.Json ベースのフォーマッタを構成する</span><span class="sxs-lookup"><span data-stu-id="443b4-169">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="443b4-170">`System.Text.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="443b4-170">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="443b4-171">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="443b4-171">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="443b4-172">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="443b4-172">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="443b4-173">Newtonsoft.Json ベースの JSON 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="443b4-173">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="443b4-174">ASP.NET Core 3.0 より前、MVC は既定で `Newtonsoft.Json` パッケージを使用して実装される JSON フォーマッタを使用していました。</span><span class="sxs-lookup"><span data-stu-id="443b4-174">Prior to ASP.NET Core 3.0, MVC defaulted to using JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="443b4-175">ASP.NET Core 3.0 以降、既定の JSON フォーマッタは `System.Text.Json` に基づいています。</span><span class="sxs-lookup"><span data-stu-id="443b4-175">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="443b4-176">`Newtonsoft.Json` ベースのフォーマッタと機能のサポートは、[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet パッケージをインストールして `Startup.ConfigureServices` で構成することで利用できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-176">Support for `Newtonsoft.Json`-based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddControllers()
    .AddNewtonsoftJson();
```

<span data-ttu-id="443b4-177">一部の機能は `System.Text.Json` ベースのフォーマッタでうまく動作せず、ASP.NET Core 3.0 リリースについて `Newtonsoft.Json` ベースのフォーマッタの参照が必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="443b4-177">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters for the ASP.NET Core 3.0 release.</span></span> <span data-ttu-id="443b4-178">お使いの ASP.NET Core 3.0 以降のアプリが次のような場合は、引き続き `Newtonsoft.Json` ベースのフォーマッタを使用してください。</span><span class="sxs-lookup"><span data-stu-id="443b4-178">Continue using the `Newtonsoft.Json`-based formatters if your ASP.NET Core 3.0 or later app:</span></span>

* <span data-ttu-id="443b4-179">`Newtonsoft.Json` 属性 (`[JsonProperty]` や `[JsonIgnore]` など) を使用する、シリアル化設定をカスタマイズする、または `Newtonsoft.Json` で提供される機能に依存している。</span><span class="sxs-lookup"><span data-stu-id="443b4-179">Uses `Newtonsoft.Json` attributes (for example, `[JsonProperty]` or `[JsonIgnore]`), customizes the serialization settings, or relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="443b4-180">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` を構成する。</span><span class="sxs-lookup"><span data-stu-id="443b4-180">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="443b4-181">ASP.NET Core 3.0 より前は、`JsonResult.SerializerSettings`が `Newtonsoft.Json` に固有の `JsonSerializerSettings` のインスタンスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="443b4-181">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="443b4-182">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) ドキュメントを生成する。</span><span class="sxs-lookup"><span data-stu-id="443b4-182">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="443b4-183">`Newtonsoft.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="443b4-183">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefautlContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="443b4-184">出力のシリアル化オプションは、アクションごとに `JsonResult` を使用して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="443b4-184">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="443b4-185">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="443b4-185">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="443b4-186">XML 形式のサポートを追加する</span><span class="sxs-lookup"><span data-stu-id="443b4-186">Add XML format support</span></span>

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="443b4-187">ASP.NET Core 2.2 またはそれ以前で XML の書式設定のサポートを追加するには、[Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet パッケージをインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="443b4-187">To add XML formatting support in ASP.NET Core 2.2 or earlier, install the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

::: moniker-end

<span data-ttu-id="443b4-188">`System.Xml.Serialization.XmlSerializer` を使用して実装された XML フォーマッタは、`Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-188">XML formatters implemented using `System.Xml.Serialization.XmlSerializer` can be configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

<span data-ttu-id="443b4-189">または、`System.Runtime.Serialization.DataContractSerializer` を使用して実装された XML フォーマッタは、`Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出すことで構成できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-189">Alternatively, XML formatters implemented using `System.Runtime.Serialization.DataContractSerializer` can be configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

<span data-ttu-id="443b4-190">XML 書式設定のサポートを追加すると、コントローラー メソッドは、要求の `Accept` ヘッダーに基づき、適切な書式を返すはずです。下の Fiddler の例をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="443b4-190">Once you've added support for XML formatting, your controller methods should return the appropriate format based on the request's `Accept` header, as this Fiddler example demonstrates:</span></span>

![Fiddler コンソール:要求の [Raw] タブで、Accept ヘッダー値が application/xml であることを確認できます。](formatting/_static/xml-response.png)

<span data-ttu-id="443b4-193">[Inspectors] タブで、`Accept: application/xml` ヘッダーが設定された状態で Raw GET 要求が行われたことを確認できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-193">You can see in the Inspectors tab that the Raw GET request was made with an `Accept: application/xml` header set.</span></span> <span data-ttu-id="443b4-194">応答ウィンドウに `Content-Type: application/xml` ヘッダーが表示されます。`Author` オブジェクトが XML にシリアル化されています。</span><span class="sxs-lookup"><span data-stu-id="443b4-194">The response pane shows the `Content-Type: application/xml` header, and the `Author` object has been serialized to XML.</span></span>

<span data-ttu-id="443b4-195">[Composer] タブを使用して要求を変更し、`Accept` ヘッダーに `application/json` を指定します。</span><span class="sxs-lookup"><span data-stu-id="443b4-195">Use the Composer tab to modify the request to specify `application/json` in the `Accept` header.</span></span> <span data-ttu-id="443b4-196">要求を実行します。応答が JSON で書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-196">Execute the request, and the response will be formatted as JSON:</span></span>

![Fiddler コンソール:要求の [Raw] タブで、Accept ヘッダー値が application/json であることを確認できます。](formatting/_static/json-response-fiddler.png)

<span data-ttu-id="443b4-199">このスクリーンショットでは、`Accept: application/json` のヘッダーとして要求セットを確認できます。応答によって、その `Content-Type` と同じものが指定されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-199">In this screenshot, you can see the request sets a header of `Accept: application/json` and the response specifies the same as its `Content-Type`.</span></span> <span data-ttu-id="443b4-200">`Author` オブジェクトは応答の本文に JSON 形式で表示されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-200">The `Author` object is shown in the body of the response, in JSON format.</span></span>

### <a name="forcing-a-particular-format"></a><span data-ttu-id="443b4-201">特定の書式を強制する</span><span class="sxs-lookup"><span data-stu-id="443b4-201">Forcing a Particular Format</span></span>

<span data-ttu-id="443b4-202">特定のアクションの応答書式を制限する場合、`[Produces]` フィルターを適用できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-202">If you would like to restrict the response formats for a specific action you can, you can apply the `[Produces]` filter.</span></span> <span data-ttu-id="443b4-203">`[Produces]` フィルターによって、特定のアクション (またはコントローラー) の応答書式が指定されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-203">The `[Produces]` filter specifies the response formats for a specific action (or controller).</span></span> <span data-ttu-id="443b4-204">ほとんどの[フィルター](xref:mvc/controllers/filters)と同様に、アクション、コントローラー、グローバル スコープで適用できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-204">Like most [Filters](xref:mvc/controllers/filters), this can be applied at the action, controller, or global scope.</span></span>

```csharp
[Produces("application/json")]
public class AuthorsController
```

<span data-ttu-id="443b4-205">アプリケーションに他のフォーマッタが構成されており、クライアントが `Accept` ヘッダーで別の利用できる書式を要求している場合でも、`[Produces]` フィルターは `AuthorsController` 内のすべてのアクションに JSON で書式設定された応答を返すように強制します。</span><span class="sxs-lookup"><span data-stu-id="443b4-205">The `[Produces]` filter will force all actions within the `AuthorsController` to return JSON-formatted responses, even if other formatters were configured for the application and the client provided an `Accept` header requesting a different, available format.</span></span> <span data-ttu-id="443b4-206">フィルターをグローバルで適用する方法など、詳細については「[フィルター](xref:mvc/controllers/filters)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="443b4-206">See [Filters](xref:mvc/controllers/filters) to learn more, including how to apply filters globally.</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="443b4-207">特殊なケースのフォーマッタ</span><span class="sxs-lookup"><span data-stu-id="443b4-207">Special Case Formatters</span></span>

<span data-ttu-id="443b4-208">一部の特殊なケースが組み込みのフォーマッタで実装されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-208">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="443b4-209">既定では、戻り値の型 `string` は *text/plain* として書式設定されます (`Accept` ヘッダー経由で要求された場合は *text/html*)。</span><span class="sxs-lookup"><span data-stu-id="443b4-209">By default, `string` return types will be formatted as *text/plain* (*text/html* if requested via `Accept` header).</span></span> <span data-ttu-id="443b4-210">この動作は `TextOutputFormatter` を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-210">This behavior can be removed by removing the `TextOutputFormatter`.</span></span> <span data-ttu-id="443b4-211">*Startup.cs* の `Configure` メソッドでフォーマッタを削除します (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="443b4-211">You remove formatters in the `Configure` method in *Startup.cs* (shown below).</span></span> <span data-ttu-id="443b4-212">戻り値の型としてモデル オブジェクトをともなうアクションは、`null` を返すとき、204 No Content の応答を返します。</span><span class="sxs-lookup"><span data-stu-id="443b4-212">Actions that have a model object return type will return a 204 No Content response when returning `null`.</span></span> <span data-ttu-id="443b4-213">この動作は `HttpNoContentOutputFormatter` を削除することで削除できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-213">This behavior can be removed by removing the `HttpNoContentOutputFormatter`.</span></span> <span data-ttu-id="443b4-214">次のコードでは、`TextOutputFormatter` と `HttpNoContentOutputFormatter` が削除されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-214">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

<span data-ttu-id="443b4-215">`TextOutputFormatter` がない場合、`string` は戻り値の型として 406 Not Acceptable などを返します。</span><span class="sxs-lookup"><span data-stu-id="443b4-215">Without the `TextOutputFormatter`, `string` return types return 406 Not Acceptable, for example.</span></span> <span data-ttu-id="443b4-216">XML フォーマッタが存在するときは、`TextOutputFormatter` が削除された場合、戻り値の型 `string` が書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-216">Note that if an XML formatter exists, it will format `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="443b4-217">`HttpNoContentOutputFormatter` がない場合、構成されているフォーマッタを利用し、null オブジェクトが書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="443b4-217">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="443b4-218">たとえば、JSON フォーマッタは応答と `null` の本文を返すのに対し、XML フォーマッタは空の XML 要素と属性 `xsi:nil="true"` セットを返します。</span><span class="sxs-lookup"><span data-stu-id="443b4-218">For example, the JSON formatter will simply return a response with a body of `null`, while the XML formatter will return an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="443b4-219">応答形式の URL マッピング</span><span class="sxs-lookup"><span data-stu-id="443b4-219">Response Format URL Mappings</span></span>

<span data-ttu-id="443b4-220">クライアントは URL の一部として特定の書式を要求できます。たとえば、クエリ文字列やパスの一部に含めます。あるいは、.xml や .json など、書式固有のファイル拡張子を利用します。</span><span class="sxs-lookup"><span data-stu-id="443b4-220">Clients can request a particular format as part of the URL, such as in the query string or part of the path, or by using a format-specific file extension such as .xml or .json.</span></span> <span data-ttu-id="443b4-221">要求パスからのマッピングは、API で使用されるルートに指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="443b4-221">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="443b4-222">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="443b4-222">For example:</span></span>

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

<span data-ttu-id="443b4-223">このルートにより、要求された書式をオプションのファイル拡張子として指定できます。</span><span class="sxs-lookup"><span data-stu-id="443b4-223">This route would allow the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="443b4-224">`[FormatFilter]` 属性は `RouteData` に書式値がないか確認し、応答が作成されると、応答形式を適切なフォーマッタにマッピングします。</span><span class="sxs-lookup"><span data-stu-id="443b4-224">The `[FormatFilter]` attribute checks for the existence of the format value in the `RouteData` and will map the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="443b4-225">ルート</span><span class="sxs-lookup"><span data-stu-id="443b4-225">Route</span></span>            |             <span data-ttu-id="443b4-226">フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="443b4-226">Formatter</span></span>              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    <span data-ttu-id="443b4-227">既定の出力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="443b4-227">The default output formatter</span></span>    |
| `/products/GetById/5.json` | <span data-ttu-id="443b4-228">JSON フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="443b4-228">The JSON formatter (if configured)</span></span> |
| `/products/GetById/5.xml`  | <span data-ttu-id="443b4-229">XML フォーマッタ (構成される場合)</span><span class="sxs-lookup"><span data-stu-id="443b4-229">The XML formatter (if configured)</span></span>  |
