---
title: ASP.NET Core でのモデル バインド
author: tdykstra
description: ASP.NET Core でのモデル バインドのしくみと、その動作のカスタマイズ方法について説明します。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 10a9f8327bf16d11ec1e04ac3888d701f1ab1778
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724533"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="d524f-103">ASP.NET Core でのモデル バインド</span><span class="sxs-lookup"><span data-stu-id="d524f-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="d524f-104">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="d524f-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="d524f-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d524f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="d524f-106">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="d524f-106">What is Model binding</span></span>

<span data-ttu-id="d524f-107">コントローラーおよび Razor Pages では、HTTP 要求からのデータが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="d524f-108">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="d524f-109">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="d524f-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="d524f-110">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="d524f-110">Model binding automates this process.</span></span> <span data-ttu-id="d524f-111">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="d524f-111">The model binding system:</span></span>

* <span data-ttu-id="d524f-112">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="d524f-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="d524f-113">メソッド パラメーターとパブリック プロパティでコントローラーと Razor Pages にデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="d524f-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="d524f-114">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="d524f-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="d524f-115">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="d524f-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="d524f-116">例</span><span class="sxs-lookup"><span data-stu-id="d524f-116">Example</span></span>

<span data-ttu-id="d524f-117">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="d524f-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="d524f-118">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="d524f-119">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-119">Model binding goes though the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="d524f-120">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="d524f-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="d524f-121">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="d524f-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="d524f-122">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="d524f-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="d524f-123">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="d524f-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="d524f-124">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="d524f-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="d524f-125">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="d524f-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="d524f-126">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="d524f-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="d524f-127">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="d524f-128">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="d524f-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="d524f-129">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="d524f-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="d524f-130">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="d524f-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="d524f-131">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="d524f-132">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="d524f-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="d524f-133">ターゲット</span><span class="sxs-lookup"><span data-stu-id="d524f-133">Targets</span></span>

<span data-ttu-id="d524f-134">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="d524f-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="d524f-135">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="d524f-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="d524f-136">要求のルーティング先である Razor Pages ハンドラー メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="d524f-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="d524f-137">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="d524f-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="d524f-138">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="d524f-138">[BindProperty] attribute</span></span>

<span data-ttu-id="d524f-139">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="d524f-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="d524f-140">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="d524f-140">[BindProperties] attribute</span></span>

<span data-ttu-id="d524f-141">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d524f-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="d524f-142">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="d524f-143">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="d524f-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="d524f-144">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="d524f-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="d524f-145">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="d524f-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="d524f-146">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="d524f-147">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d524f-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="d524f-148">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d524f-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="d524f-149">ソース</span><span class="sxs-lookup"><span data-stu-id="d524f-149">Sources</span></span>

<span data-ttu-id="d524f-150">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="d524f-151">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="d524f-151">Form fields</span></span> 
1. <span data-ttu-id="d524f-152">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="d524f-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="d524f-153">ルート データ</span><span class="sxs-lookup"><span data-stu-id="d524f-153">Route data</span></span>
1. <span data-ttu-id="d524f-154">クエリ文字列のパラメーター</span><span class="sxs-lookup"><span data-stu-id="d524f-154">Query string parameters</span></span>
1. <span data-ttu-id="d524f-155">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="d524f-155">Uploaded files</span></span> 

<span data-ttu-id="d524f-156">ターゲット パラメーターまたはプロパティごとに、この一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="d524f-156">For each target parameter or property, the sources are scanned in the order indicated in this list.</span></span> <span data-ttu-id="d524f-157">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="d524f-157">There are a few exceptions:</span></span>

* <span data-ttu-id="d524f-158">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="d524f-159">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d524f-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="d524f-160">既定の動作によって正しい結果が得られない場合は、次のいずれかの属性を使用して、特定のターゲットに使用するソースを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d524f-160">If the default behavior doesn't give the right results, you can use one of the following attributes to specify the source to use for any given target.</span></span> 

* <span data-ttu-id="d524f-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="d524f-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="d524f-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - ルート データから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="d524f-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="d524f-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - ポストされたフォーム フィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="d524f-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="d524f-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - 要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="d524f-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="d524f-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="d524f-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="d524f-166">これらの属性:</span><span class="sxs-lookup"><span data-stu-id="d524f-166">These attributes:</span></span>

* <span data-ttu-id="d524f-167">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="d524f-168">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d524f-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="d524f-169">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="d524f-170">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d524f-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="d524f-171">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="d524f-171">[FromBody] attribute</span></span>

<span data-ttu-id="d524f-172">要求本文のデータは、要求のコンテンツの種類に固有の入力フォーマッタを使用して解析されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-172">The request body data is parsed by using input formatters specific to the content type of the request.</span></span> <span data-ttu-id="d524f-173">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="d524f-173">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="d524f-174">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d524f-174">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="d524f-175">ASP.NET Core ランタイムでは、要求ストリームを読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-175">The ASP.NET Core runtime delegates the responsibility of reading the request stream to the input formatter.</span></span> <span data-ttu-id="d524f-176">要求ストリームがいったん読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="d524f-176">Once the request stream is read, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="d524f-177">その他のソース</span><span class="sxs-lookup"><span data-stu-id="d524f-177">Additional sources</span></span>

<span data-ttu-id="d524f-178">ソース データは、"*値プロバイダー*" によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-178">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="d524f-179">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="d524f-179">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="d524f-180">たとえば、cookie またはセッション状態からのデータが必要だとします。</span><span class="sxs-lookup"><span data-stu-id="d524f-180">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="d524f-181">新しいソースからデータを取得するには:</span><span class="sxs-lookup"><span data-stu-id="d524f-181">To get data from a new source:</span></span>

* <span data-ttu-id="d524f-182">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="d524f-182">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="d524f-183">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="d524f-183">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="d524f-184">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="d524f-184">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="d524f-185">サンプル アプリには、cookie から値を取得する[値プロバイダー](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs)と[ファクトリ](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs)の例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d524f-185">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="d524f-186">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="d524f-186">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="d524f-187">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="d524f-187">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="d524f-188">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new CookieValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d524f-188">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="d524f-189">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="d524f-189">No source for a model property</span></span>

<span data-ttu-id="d524f-190">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="d524f-190">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="d524f-191">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-191">The property is set to null or a default value:</span></span>

* <span data-ttu-id="d524f-192">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-192">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="d524f-193">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-193">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="d524f-194">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-194">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="d524f-195">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-195">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="d524f-196">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-196">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="d524f-197">モデル プロパティ用のフォーム フィールド内で何も見つからないときモデル状態を無効にする必要がある場合は、[[BindRequired] 属性](#bindrequired-attribute)を使用します。</span><span class="sxs-lookup"><span data-stu-id="d524f-197">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span></span>

<span data-ttu-id="d524f-198">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d524f-198">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="d524f-199">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-199">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="d524f-200">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="d524f-200">Type conversion errors</span></span>

<span data-ttu-id="d524f-201">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="d524f-201">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="d524f-202">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-202">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="d524f-203">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-203">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="d524f-204">Razor ページでは、エラー メッセージを含むページが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-204">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="d524f-205">クライアント側の検証では、それを行わないなら Razor Pages フォームに送信されてしまう不適切なデータのほとんどがキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="d524f-205">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="d524f-206">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="d524f-206">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="d524f-207">サンプル アプリには、 **[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、 **[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-207">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="d524f-208">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="d524f-208">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="d524f-209">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="d524f-209">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="d524f-210">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="d524f-210">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="d524f-211">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-211">The invalid input does appear in an error message.</span></span> <span data-ttu-id="d524f-212">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d524f-212">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="d524f-213">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d524f-213">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="d524f-214">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="d524f-214">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="d524f-215">単純型</span><span class="sxs-lookup"><span data-stu-id="d524f-215">Simple types</span></span>

<span data-ttu-id="d524f-216">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="d524f-216">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="d524f-217">Boolean</span><span class="sxs-lookup"><span data-stu-id="d524f-217">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="d524f-218">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="d524f-218">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="d524f-219">Char</span><span class="sxs-lookup"><span data-stu-id="d524f-219">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="d524f-220">DateTime</span><span class="sxs-lookup"><span data-stu-id="d524f-220">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="d524f-221">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="d524f-221">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="d524f-222">Decimal</span><span class="sxs-lookup"><span data-stu-id="d524f-222">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="d524f-223">Double</span><span class="sxs-lookup"><span data-stu-id="d524f-223">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="d524f-224">Enum</span><span class="sxs-lookup"><span data-stu-id="d524f-224">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="d524f-225">Guid</span><span class="sxs-lookup"><span data-stu-id="d524f-225">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="d524f-226">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="d524f-226">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="d524f-227">Single</span><span class="sxs-lookup"><span data-stu-id="d524f-227">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="d524f-228">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="d524f-228">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="d524f-229">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="d524f-229">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="d524f-230">Uri</span><span class="sxs-lookup"><span data-stu-id="d524f-230">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="d524f-231">Version</span><span class="sxs-lookup"><span data-stu-id="d524f-231">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="d524f-232">複合型</span><span class="sxs-lookup"><span data-stu-id="d524f-232">Complex types</span></span>

<span data-ttu-id="d524f-233">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="d524f-233">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="d524f-234">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-234">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="d524f-235">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="d524f-235">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="d524f-236">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-236">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="d524f-237">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="d524f-237">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="d524f-238">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="d524f-238">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="d524f-239">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d524f-239">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="d524f-240">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="d524f-240">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="d524f-241">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="d524f-241">Prefix = parameter name</span></span>

<span data-ttu-id="d524f-242">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合:</span><span class="sxs-lookup"><span data-stu-id="d524f-242">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="d524f-243">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="d524f-243">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="d524f-244">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-244">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="d524f-245">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="d524f-245">Prefix = property name</span></span>

<span data-ttu-id="d524f-246">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合:</span><span class="sxs-lookup"><span data-stu-id="d524f-246">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="d524f-247">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="d524f-247">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="d524f-248">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-248">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="d524f-249">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="d524f-249">Custom prefix</span></span>

<span data-ttu-id="d524f-250">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合:</span><span class="sxs-lookup"><span data-stu-id="d524f-250">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="d524f-251">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="d524f-251">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="d524f-252">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="d524f-253">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="d524f-253">Attributes for complex type targets</span></span>

<span data-ttu-id="d524f-254">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d524f-254">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="d524f-255">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="d524f-255">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="d524f-256">ポストされた JSON および XML 要求本文を処理する入力フォーマッタには影響しません。</span><span class="sxs-lookup"><span data-stu-id="d524f-256">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="d524f-257">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="d524f-257">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="d524f-258">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="d524f-258">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="d524f-259">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="d524f-259">[BindRequired] attribute</span></span>

<span data-ttu-id="d524f-260">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="d524f-260">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="d524f-261">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="d524f-261">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="d524f-262">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d524f-262">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="d524f-263">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="d524f-263">[BindNever] attribute</span></span>

<span data-ttu-id="d524f-264">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="d524f-264">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="d524f-265">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="d524f-265">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="d524f-266">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d524f-266">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="d524f-267">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="d524f-267">[Bind] attribute</span></span>

<span data-ttu-id="d524f-268">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="d524f-268">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="d524f-269">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="d524f-269">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="d524f-270">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d524f-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="d524f-271">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d524f-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="d524f-272">`[Bind]` 属性を使用すれば、"*作成*" シナリオにおいて過剰ポスティングから保護することができます。</span><span class="sxs-lookup"><span data-stu-id="d524f-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="d524f-273">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="d524f-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="d524f-274">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d524f-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="d524f-275">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d524f-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="d524f-276">コレクション</span><span class="sxs-lookup"><span data-stu-id="d524f-276">Collections</span></span>

<span data-ttu-id="d524f-277">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-277">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="d524f-278">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-278">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="d524f-279">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d524f-279">For example:</span></span>

* <span data-ttu-id="d524f-280">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合:</span><span class="sxs-lookup"><span data-stu-id="d524f-280">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="d524f-281">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="d524f-281">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="d524f-282">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d524f-282">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="d524f-283">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-283">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="d524f-284">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="d524f-284">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="d524f-285">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="d524f-285">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="d524f-286">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d524f-286">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="d524f-287">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-287">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="d524f-288">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-288">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="d524f-289">ディクショナリ</span><span class="sxs-lookup"><span data-stu-id="d524f-289">Dictionaries</span></span>

<span data-ttu-id="d524f-290">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-290">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="d524f-291">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-291">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="d524f-292">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d524f-292">For example:</span></span>

* <span data-ttu-id="d524f-293">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします:</span><span class="sxs-lookup"><span data-stu-id="d524f-293">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="d524f-294">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="d524f-294">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="d524f-295">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-295">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="d524f-296">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="d524f-296">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="d524f-297">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="d524f-297">selectedCourses["2000"]="Economics"</span></span>

## <a name="special-data-types"></a><span data-ttu-id="d524f-298">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="d524f-298">Special data types</span></span>

<span data-ttu-id="d524f-299">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d524f-299">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="d524f-300">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="d524f-300">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="d524f-301">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="d524f-301">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="d524f-302">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d524f-302">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="d524f-303">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="d524f-303">CancellationToken</span></span>

<span data-ttu-id="d524f-304">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-304">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="d524f-305">FormCollection</span><span class="sxs-lookup"><span data-stu-id="d524f-305">FormCollection</span></span>

<span data-ttu-id="d524f-306">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="d524f-306">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="d524f-307">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="d524f-307">Input formatters</span></span>

<span data-ttu-id="d524f-308">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d524f-308">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="d524f-309">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された "*入力フォーマッタ*" が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-309">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="d524f-310">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d524f-310">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="d524f-311">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="d524f-311">You can add other formatters for other content types.</span></span>

<span data-ttu-id="d524f-312">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-312">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="d524f-313">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-313">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="d524f-314">組み込みの XML 入力フォーマッタを使用するには:</span><span class="sxs-lookup"><span data-stu-id="d524f-314">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="d524f-315">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d524f-315">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="d524f-316">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d524f-316">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="d524f-317">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="d524f-317">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="d524f-318">詳細については、「[XML シリアル化の概要](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d524f-318">For more information, see [Introducing XML Serialization](https://docs.microsoft.com/en-us/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="d524f-319">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="d524f-319">Exclude specified types from model binding</span></span>

<span data-ttu-id="d524f-320">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-320">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="d524f-321">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="d524f-321">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="d524f-322">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="d524f-322">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="d524f-323">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="d524f-323">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d524f-324">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="d524f-324">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="d524f-325">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="d524f-325">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d524f-326">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="d524f-326">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="d524f-327">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="d524f-327">Custom model binders</span></span>

<span data-ttu-id="d524f-328">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="d524f-328">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="d524f-329">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d524f-329">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="d524f-330">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="d524f-330">Manual model binding</span></span>

<span data-ttu-id="d524f-331">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="d524f-331">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="d524f-332">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="d524f-332">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="d524f-333">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d524f-333">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="d524f-334">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-334">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="d524f-335">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d524f-335">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="d524f-336">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="d524f-336">[FromServices] attribute</span></span>

<span data-ttu-id="d524f-337">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="d524f-337">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="d524f-338">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="d524f-338">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="d524f-339">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="d524f-339">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d524f-340">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="d524f-340">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d524f-341">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d524f-341">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
