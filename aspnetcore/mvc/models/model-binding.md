---
title: ASP.NET Core でのモデル バインド
author: rick-anderson
description: ASP.NET Core でのモデル バインドのしくみと、その動作のカスタマイズ方法について説明します。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 11/21/2019
uid: mvc/models/model-binding
ms.openlocfilehash: da6cc25e0bbb1b2301529b34eab4c91f9ccb46eb
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944296"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="c42d1-103">ASP.NET Core でのモデル バインド</span><span class="sxs-lookup"><span data-stu-id="c42d1-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c42d1-104">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="c42d1-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c42d1-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="c42d1-106">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="c42d1-106">What is Model binding</span></span>

<span data-ttu-id="c42d1-107">コントローラーおよび Razor Pages では、HTTP 要求からのデータが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="c42d1-108">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="c42d1-109">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="c42d1-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="c42d1-110">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-110">Model binding automates this process.</span></span> <span data-ttu-id="c42d1-111">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-111">The model binding system:</span></span>

* <span data-ttu-id="c42d1-112">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="c42d1-113">メソッド パラメーターとパブリック プロパティでコントローラーと Razor Pages にデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="c42d1-114">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="c42d1-115">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="c42d1-116">例</span><span class="sxs-lookup"><span data-stu-id="c42d1-116">Example</span></span>

<span data-ttu-id="c42d1-117">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="c42d1-118">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="c42d1-119">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="c42d1-120">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="c42d1-121">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="c42d1-122">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="c42d1-123">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="c42d1-124">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="c42d1-125">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="c42d1-126">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="c42d1-127">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="c42d1-128">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="c42d1-129">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="c42d1-130">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="c42d1-131">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="c42d1-132">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="c42d1-133">ターゲット</span><span class="sxs-lookup"><span data-stu-id="c42d1-133">Targets</span></span>

<span data-ttu-id="c42d1-134">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="c42d1-135">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="c42d1-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="c42d1-136">要求のルーティング先である Razor Pages ハンドラー メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="c42d1-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="c42d1-137">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="c42d1-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="c42d1-138">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-138">[BindProperty] attribute</span></span>

<span data-ttu-id="c42d1-139">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="c42d1-140">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-140">[BindProperties] attribute</span></span>

<span data-ttu-id="c42d1-141">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="c42d1-142">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="c42d1-143">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="c42d1-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="c42d1-144">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="c42d1-145">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="c42d1-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="c42d1-146">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="c42d1-147">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="c42d1-148">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="c42d1-149">ソース</span><span class="sxs-lookup"><span data-stu-id="c42d1-149">Sources</span></span>

<span data-ttu-id="c42d1-150">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="c42d1-151">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="c42d1-151">Form fields</span></span>
1. <span data-ttu-id="c42d1-152">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="c42d1-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="c42d1-153">ルート データ</span><span class="sxs-lookup"><span data-stu-id="c42d1-153">Route data</span></span>
1. <span data-ttu-id="c42d1-154">クエリ文字列のパラメーター</span><span class="sxs-lookup"><span data-stu-id="c42d1-154">Query string parameters</span></span>
1. <span data-ttu-id="c42d1-155">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="c42d1-155">Uploaded files</span></span>

<span data-ttu-id="c42d1-156">ターゲット パラメーターまたはプロパティごとに、前述の一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="c42d1-157">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-157">There are a few exceptions:</span></span>

* <span data-ttu-id="c42d1-158">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="c42d1-159">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="c42d1-160">既定のソースが正しくない場合は、次のいずれかの属性を使用してソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="c42d1-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="c42d1-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - ルート データから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="c42d1-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - ポストされたフォーム フィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="c42d1-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - 要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="c42d1-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="c42d1-166">これらの属性:</span><span class="sxs-lookup"><span data-stu-id="c42d1-166">These attributes:</span></span>

* <span data-ttu-id="c42d1-167">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="c42d1-168">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="c42d1-169">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="c42d1-170">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="c42d1-171">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-171">[FromBody] attribute</span></span>

<span data-ttu-id="c42d1-172">`[FromBody]` 属性をパラメーターに適用すると、HTTP 要求の本文からそのプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="c42d1-173">ASP.NET Core ランタイムでは、本文を読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="c42d1-174">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="c42d1-175">`[FromBody]` を複合型パラメーターに適用すると、そのプロパティに適用されているバインディング ソース属性はいずれも無視されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="c42d1-176">たとえば、次の `Create` アクションでは、その `pet` パラメーターを本文から設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="c42d1-177">`Pet` クラスでは、`Breed` プロパティをクエリ文字列パラメーターから設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="c42d1-178">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-178">In the preceding example:</span></span>

* <span data-ttu-id="c42d1-179">`[FromQuery]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="c42d1-180">`Breed` プロパティは、クエリ文字列パラメーターから設定されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="c42d1-181">入力フォーマッタでは本文のみが読み取られ、バインディング ソース属性は認識されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="c42d1-182">本文内で適切な値が見つかった場合は、その値を使用して `Breed` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="c42d1-183">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="c42d1-184">入力フォーマッタによって要求ストリームが読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="c42d1-185">その他のソース</span><span class="sxs-lookup"><span data-stu-id="c42d1-185">Additional sources</span></span>

<span data-ttu-id="c42d1-186">ソース データは、"*値プロバイダー*" によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="c42d1-187">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="c42d1-188">たとえば、cookie またはセッション状態からのデータが必要だとします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="c42d1-189">新しいソースからデータを取得するには:</span><span class="sxs-lookup"><span data-stu-id="c42d1-189">To get data from a new source:</span></span>

* <span data-ttu-id="c42d1-190">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="c42d1-191">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="c42d1-192">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="c42d1-193">サンプル アプリには、cookie から値を取得する[値プロバイダー](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs)と[ファクトリ](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs)の例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-193">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="c42d1-194">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="c42d1-195">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="c42d1-196">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new CookieValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="c42d1-197">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="c42d1-197">No source for a model property</span></span>

<span data-ttu-id="c42d1-198">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="c42d1-199">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="c42d1-200">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="c42d1-201">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="c42d1-202">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="c42d1-203">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="c42d1-204">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="c42d1-205">モデル プロパティ用のフォーム フィールド内で何も見つからないときモデル状態を無効にする必要がある場合は、[`[BindRequired]`](#bindrequired-attribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="c42d1-206">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="c42d1-207">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="c42d1-208">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="c42d1-208">Type conversion errors</span></span>

<span data-ttu-id="c42d1-209">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="c42d1-210">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="c42d1-211">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="c42d1-212">Razor ページでは、エラー メッセージを含むページが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="c42d1-213">クライアント側の検証では、それを行わないなら Razor Pages フォームに送信されてしまう不適切なデータのほとんどがキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="c42d1-214">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="c42d1-215">サンプル アプリには、 **[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、 **[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="c42d1-216">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="c42d1-217">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="c42d1-218">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="c42d1-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="c42d1-219">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="c42d1-220">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="c42d1-221">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="c42d1-222">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="c42d1-223">単純型</span><span class="sxs-lookup"><span data-stu-id="c42d1-223">Simple types</span></span>

<span data-ttu-id="c42d1-224">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="c42d1-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="c42d1-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="c42d1-226">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="c42d1-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="c42d1-227">Char</span><span class="sxs-lookup"><span data-stu-id="c42d1-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="c42d1-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="c42d1-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="c42d1-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="c42d1-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="c42d1-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="c42d1-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="c42d1-231">Double</span><span class="sxs-lookup"><span data-stu-id="c42d1-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="c42d1-232">Enum</span><span class="sxs-lookup"><span data-stu-id="c42d1-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="c42d1-233">Guid</span><span class="sxs-lookup"><span data-stu-id="c42d1-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="c42d1-234">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="c42d1-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="c42d1-235">Single</span><span class="sxs-lookup"><span data-stu-id="c42d1-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="c42d1-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="c42d1-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="c42d1-237">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="c42d1-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="c42d1-238">Uri</span><span class="sxs-lookup"><span data-stu-id="c42d1-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="c42d1-239">Version</span><span class="sxs-lookup"><span data-stu-id="c42d1-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="c42d1-240">複合型</span><span class="sxs-lookup"><span data-stu-id="c42d1-240">Complex types</span></span>

<span data-ttu-id="c42d1-241">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="c42d1-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="c42d1-242">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="c42d1-243">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="c42d1-244">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="c42d1-245">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="c42d1-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="c42d1-246">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="c42d1-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="c42d1-247">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="c42d1-248">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="c42d1-249">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="c42d1-249">Prefix = parameter name</span></span>

<span data-ttu-id="c42d1-250">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="c42d1-251">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="c42d1-252">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="c42d1-253">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="c42d1-253">Prefix = property name</span></span>

<span data-ttu-id="c42d1-254">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="c42d1-255">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="c42d1-256">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="c42d1-257">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="c42d1-257">Custom prefix</span></span>

<span data-ttu-id="c42d1-258">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="c42d1-259">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="c42d1-260">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="c42d1-261">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-261">Attributes for complex type targets</span></span>

<span data-ttu-id="c42d1-262">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="c42d1-263">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="c42d1-264">ポストされた JSON および XML 要求本文を処理する入力フォーマッタには影響しません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="c42d1-265">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="c42d1-266">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="c42d1-267">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-267">[BindRequired] attribute</span></span>

<span data-ttu-id="c42d1-268">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="c42d1-269">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="c42d1-270">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="c42d1-271">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-271">[BindNever] attribute</span></span>

<span data-ttu-id="c42d1-272">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="c42d1-273">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="c42d1-274">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="c42d1-275">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-275">[Bind] attribute</span></span>

<span data-ttu-id="c42d1-276">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="c42d1-277">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="c42d1-278">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="c42d1-279">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="c42d1-280">`[Bind]` 属性を使用すれば、"*作成*" シナリオにおいて過剰ポスティングから保護することができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="c42d1-281">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="c42d1-282">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="c42d1-283">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="c42d1-284">コレクション</span><span class="sxs-lookup"><span data-stu-id="c42d1-284">Collections</span></span>

<span data-ttu-id="c42d1-285">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="c42d1-286">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="c42d1-287">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-287">For example:</span></span>

* <span data-ttu-id="c42d1-288">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="c42d1-289">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="c42d1-290">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="c42d1-291">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="c42d1-292">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="c42d1-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="c42d1-293">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="c42d1-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="c42d1-294">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="c42d1-295">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="c42d1-296">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="c42d1-297">ディクショナリ</span><span class="sxs-lookup"><span data-stu-id="c42d1-297">Dictionaries</span></span>

<span data-ttu-id="c42d1-298">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="c42d1-299">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="c42d1-300">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-300">For example:</span></span>

* <span data-ttu-id="c42d1-301">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします:</span><span class="sxs-lookup"><span data-stu-id="c42d1-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="c42d1-302">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="c42d1-303">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="c42d1-304">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="c42d1-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="c42d1-305">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="c42d1-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="c42d1-306">モデル バインド ルート データとクエリ文字列のグローバリゼーション動作</span><span class="sxs-lookup"><span data-stu-id="c42d1-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="c42d1-307">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="c42d1-308">値をインバリアント カルチャとして扱います。</span><span class="sxs-lookup"><span data-stu-id="c42d1-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="c42d1-309">URL はカルチャに依存しないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="c42d1-310">これに対し、フォーム データからの値は、カルチャに依存した変換にかけられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="c42d1-311">URL がロケール間で共有可能なように、設計上そのようになっています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="c42d1-312">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでカルチャ依存の変換が行われるようにするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="c42d1-313"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory> から継承します</span><span class="sxs-lookup"><span data-stu-id="c42d1-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="c42d1-314">[QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) または [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) からコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="c42d1-315">値プロバイダー コンストラクターに渡された[カルチャ値](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)を [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-315">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="c42d1-316">MVC オプション内の既定値プロバイダー ファクトリを、新しいものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="c42d1-317">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="c42d1-317">Special data types</span></span>

<span data-ttu-id="c42d1-318">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="c42d1-319">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="c42d1-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="c42d1-320">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="c42d1-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="c42d1-321">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="c42d1-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="c42d1-322">CancellationToken</span></span>

<span data-ttu-id="c42d1-323">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="c42d1-324">FormCollection</span><span class="sxs-lookup"><span data-stu-id="c42d1-324">FormCollection</span></span>

<span data-ttu-id="c42d1-325">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="c42d1-326">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="c42d1-326">Input formatters</span></span>

<span data-ttu-id="c42d1-327">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="c42d1-328">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された "*入力フォーマッタ*" が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="c42d1-329">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="c42d1-330">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="c42d1-331">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="c42d1-332">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="c42d1-333">組み込みの XML 入力フォーマッタを使用するには:</span><span class="sxs-lookup"><span data-stu-id="c42d1-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="c42d1-334">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="c42d1-335">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="c42d1-336">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="c42d1-337">詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="c42d1-338">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="c42d1-338">Exclude specified types from model binding</span></span>

<span data-ttu-id="c42d1-339">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-339">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="c42d1-340">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-340">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="c42d1-341">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-341">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="c42d1-342">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-342">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c42d1-343">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-343">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="c42d1-344">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-344">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c42d1-345">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-345">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="c42d1-346">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="c42d1-346">Custom model binders</span></span>

<span data-ttu-id="c42d1-347">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-347">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="c42d1-348">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-348">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="c42d1-349">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="c42d1-349">Manual model binding</span></span>

<span data-ttu-id="c42d1-350">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-350">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="c42d1-351">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-351">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="c42d1-352">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-352">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="c42d1-353">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-353">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="c42d1-354">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-354">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="c42d1-355">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-355">[FromServices] attribute</span></span>

<span data-ttu-id="c42d1-356">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="c42d1-356">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="c42d1-357">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-357">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="c42d1-358">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-358">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c42d1-359">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-359">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c42d1-360">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c42d1-360">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c42d1-361">この記事では、モデル バインドとは何か、そのしくみ、その動作のカスタマイズ方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-361">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="c42d1-362">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c42d1-362">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="c42d1-363">モデル バインドとは何か</span><span class="sxs-lookup"><span data-stu-id="c42d1-363">What is Model binding</span></span>

<span data-ttu-id="c42d1-364">コントローラーおよび Razor Pages では、HTTP 要求からのデータが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-364">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="c42d1-365">たとえば、ルート データからはレコード キーが提供され、ポストされたフォーム フィールドからはモデルのプロパティ用の値が提供されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-365">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="c42d1-366">これらの各値を取得してそれらを文字列から .NET 型に変換するためのコードを記述するのは、面倒で間違いも起こりやすいでしょう。</span><span class="sxs-lookup"><span data-stu-id="c42d1-366">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="c42d1-367">モデル バインドを使用すれば、このプロセスを自動化できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-367">Model binding automates this process.</span></span> <span data-ttu-id="c42d1-368">モデル バインド システムでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-368">The model binding system:</span></span>

* <span data-ttu-id="c42d1-369">ルート データ、フォーム フィールド、クエリ文字列などのさまざまなソースからデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-369">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="c42d1-370">メソッド パラメーターとパブリック プロパティでコントローラーと Razor Pages にデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-370">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="c42d1-371">文字列データを .NET 型に変換します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-371">Converts string data to .NET types.</span></span>
* <span data-ttu-id="c42d1-372">複合型のプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-372">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="c42d1-373">例</span><span class="sxs-lookup"><span data-stu-id="c42d1-373">Example</span></span>

<span data-ttu-id="c42d1-374">次のアクション メソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-374">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="c42d1-375">さらにアプリでは、この URL を使用して要求が受信されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-375">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="c42d1-376">ルーティング システムでアクション メソッドが選択されたら、モデル バインドでは次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-376">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="c42d1-377">`GetByID` の最初のパラメーター (`id` という名前の整数型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-377">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="c42d1-378">HTTP 要求内で利用可能なソースを調べ、ルート データ内で `id` = "2" を検索します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-378">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="c42d1-379">文字列 "2" を整数 2 に変換します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-379">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="c42d1-380">`GetByID` の次のパラメーター (`dogsOnly` という名前のブール型) を検索します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-380">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="c42d1-381">該当するソース内を調べ、クエリ文字列内で "DogsOnly=true" を検索します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-381">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="c42d1-382">名前の照合では大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-382">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="c42d1-383">文字列 "true" をブール型の `true` に変換します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-383">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="c42d1-384">次にフレームワークによって `GetById` メソッドが呼び出され、`id` パラメーターには 2 が、`dogsOnly` パラメーターには `true` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-384">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="c42d1-385">上記の例で、モデル バインディング ターゲットは単純型のメソッド パラメーターになっています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-385">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="c42d1-386">ターゲットは複合型のプロパティになる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-386">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="c42d1-387">各プロパティが正常にバインドされたら、そのプロパティに対して[モデル検証](xref:mvc/models/validation)が行われます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-387">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="c42d1-388">どのようなデータがモデルにバインドされているかを示す記録、バインド エラー、または検証のエラーは、[ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) または [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-388">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="c42d1-389">このプロセスが正常終了したかどうかを確認するために、アプリでは [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) フラグが調べられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-389">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="c42d1-390">ターゲット</span><span class="sxs-lookup"><span data-stu-id="c42d1-390">Targets</span></span>

<span data-ttu-id="c42d1-391">モデル バインドでは、次の種類のターゲットの値について検索が試みられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-391">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="c42d1-392">要求のルーティング先であるコントローラー アクション メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="c42d1-392">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="c42d1-393">要求のルーティング先である Razor Pages ハンドラー メソッドのパラメーター。</span><span class="sxs-lookup"><span data-stu-id="c42d1-393">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="c42d1-394">属性によって指定されている場合は、コントローラーまたは `PageModel` クラスのパブリック プロパティ。</span><span class="sxs-lookup"><span data-stu-id="c42d1-394">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="c42d1-395">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-395">[BindProperty] attribute</span></span>

<span data-ttu-id="c42d1-396">コントローラーまたは `PageModel` クラスのパブリック プロパティに適用できます。これによってモデル バインドはそのプロパティをターゲットとするようになります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-396">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="c42d1-397">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-397">[BindProperties] attribute</span></span>

<span data-ttu-id="c42d1-398">ASP.NET Core 2.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-398">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="c42d1-399">コントローラーまたは `PageModel` クラスに適用できます。これによってモデル バインドはクラスのすべてのパブリック プロパティをターゲットとするように指示されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-399">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="c42d1-400">HTTP GET 要求のモデル バインド</span><span class="sxs-lookup"><span data-stu-id="c42d1-400">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="c42d1-401">既定では、プロパティは HTTP GET 要求にバインドされません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-401">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="c42d1-402">通常、GET 要求に必要なのはレコード ID パラメーターのみです。</span><span class="sxs-lookup"><span data-stu-id="c42d1-402">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="c42d1-403">レコード ID は、データベース内の項目の検索に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-403">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="c42d1-404">そのため、モデルのインスタンスを保持するプロパティをバインドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-404">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="c42d1-405">GET 要求からのデータにプロパティがバインドされるようにするシナリオでは、`SupportsGet` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-405">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="c42d1-406">ソース</span><span class="sxs-lookup"><span data-stu-id="c42d1-406">Sources</span></span>

<span data-ttu-id="c42d1-407">既定では、モデル バインドでは HTTP 要求内の次のソースからキーと値のペアの形式でデータが取得されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-407">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="c42d1-408">フォーム フィールド</span><span class="sxs-lookup"><span data-stu-id="c42d1-408">Form fields</span></span>
1. <span data-ttu-id="c42d1-409">要求本文 ([[ApiController] 属性を持つコントローラー](xref:web-api/index#binding-source-parameter-inference) の場合)。</span><span class="sxs-lookup"><span data-stu-id="c42d1-409">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="c42d1-410">ルート データ</span><span class="sxs-lookup"><span data-stu-id="c42d1-410">Route data</span></span>
1. <span data-ttu-id="c42d1-411">クエリ文字列のパラメーター</span><span class="sxs-lookup"><span data-stu-id="c42d1-411">Query string parameters</span></span>
1. <span data-ttu-id="c42d1-412">アップロード済みのファイル</span><span class="sxs-lookup"><span data-stu-id="c42d1-412">Uploaded files</span></span>

<span data-ttu-id="c42d1-413">ターゲット パラメーターまたはプロパティごとに、前述の一覧に示されている順序でソースがスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-413">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="c42d1-414">次のようにいくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-414">There are a few exceptions:</span></span>

* <span data-ttu-id="c42d1-415">ルート データとクエリ文字列の値は単純型にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-415">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="c42d1-416">アップロード済みのファイルは、`IFormFile` または `IEnumerable<IFormFile>` を実装するターゲットの種類にのみバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-416">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="c42d1-417">既定のソースが正しくない場合は、次のいずれかの属性を使用してソースを指定します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-417">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="c42d1-418">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - クエリ文字列から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-418">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="c42d1-419">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - ルート データから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-419">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="c42d1-420">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - ポストされたフォーム フィールドから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-420">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="c42d1-421">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - 要求本文から値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-421">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="c42d1-422">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - HTTP ヘッダーから値を取得します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-422">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="c42d1-423">これらの属性:</span><span class="sxs-lookup"><span data-stu-id="c42d1-423">These attributes:</span></span>

* <span data-ttu-id="c42d1-424">次の例のように、(モデル クラスにではなく) モデル プロパティに個別に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-424">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="c42d1-425">必要に応じて、コンストラクター内のモデル名の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-425">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="c42d1-426">このオプションは、プロパティ名と要求内の値とが一致しない場合に指定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-426">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="c42d1-427">たとえば、要求内の値は、次の例のようにその名前にハイフンが含まれている場合、ヘッダーである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-427">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="c42d1-428">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-428">[FromBody] attribute</span></span>

<span data-ttu-id="c42d1-429">`[FromBody]` 属性をパラメーターに適用すると、HTTP 要求の本文からそのプロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-429">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="c42d1-430">ASP.NET Core ランタイムでは、本文を読み取る責任が入力フォーマッタに委任されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-430">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="c42d1-431">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-431">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="c42d1-432">`[FromBody]` を複合型パラメーターに適用すると、そのプロパティに適用されているバインディング ソース属性はいずれも無視されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-432">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="c42d1-433">たとえば、次の `Create` アクションでは、その `pet` パラメーターを本文から設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-433">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="c42d1-434">`Pet` クラスでは、`Breed` プロパティをクエリ文字列パラメーターから設定するように指定されています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-434">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="c42d1-435">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-435">In the preceding example:</span></span>

* <span data-ttu-id="c42d1-436">`[FromQuery]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-436">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="c42d1-437">`Breed` プロパティは、クエリ文字列パラメーターから設定されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-437">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="c42d1-438">入力フォーマッタでは本文のみが読み取られ、バインディング ソース属性は認識されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-438">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="c42d1-439">本文内で適切な値が見つかった場合は、その値を使用して `Breed` プロパティが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-439">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="c42d1-440">アクション メソッドごとに `[FromBody]` を複数のパラメーターに適用しないでください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-440">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="c42d1-441">入力フォーマッタによって要求ストリームが読み取られると、他の `[FromBody]` パラメーターをバインドするためにそれを再度読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-441">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="c42d1-442">その他のソース</span><span class="sxs-lookup"><span data-stu-id="c42d1-442">Additional sources</span></span>

<span data-ttu-id="c42d1-443">ソース データは、"*値プロバイダー*" によってモデル バインド システムに提供されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-443">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="c42d1-444">モデル バインド用に、他のソースからデータを取得するカスタムの値プロバイダーを作成して登録することができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-444">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="c42d1-445">たとえば、cookie またはセッション状態からのデータが必要だとします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-445">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="c42d1-446">新しいソースからデータを取得するには:</span><span class="sxs-lookup"><span data-stu-id="c42d1-446">To get data from a new source:</span></span>

* <span data-ttu-id="c42d1-447">`IValueProvider` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-447">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="c42d1-448">`IValueProviderFactory` を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-448">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="c42d1-449">`Startup.ConfigureServices` 内のファクトリ クラスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-449">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="c42d1-450">サンプル アプリには、cookie から値を取得する[値プロバイダー](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs)と[ファクトリ](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs)の例が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-450">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="c42d1-451">`Startup.ConfigureServices` 内の登録コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-451">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="c42d1-452">表示したコードでは、すべての組み込み値プロバイダーの後にカスタムの値プロバイダーが配置されています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-452">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="c42d1-453">それをリストの最初に持ってくるには、`Add` ではなく `Insert(0, new CookieValueProviderFactory())` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-453">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="c42d1-454">モデル プロパティ用のソースがない</span><span class="sxs-lookup"><span data-stu-id="c42d1-454">No source for a model property</span></span>

<span data-ttu-id="c42d1-455">既定では、モデル プロパティ用の値が見つからない場合、モデル状態エラーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-455">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="c42d1-456">プロパティは次のように null 値または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-456">The property is set to null or a default value:</span></span>

* <span data-ttu-id="c42d1-457">null 許容単純型は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-457">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="c42d1-458">null 非許容値型は `default(T)` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-458">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="c42d1-459">たとえば、パラメーター `int id` は 0 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-459">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="c42d1-460">複合型の場合、モデル バインドでは、プロパティを設定せずに既定のコンストラクターを使用して、インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-460">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="c42d1-461">配列は `Array.Empty<T>()` に設定されます。例外として、`byte[]` 配列は `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-461">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="c42d1-462">モデル プロパティ用のフォーム フィールド内で何も見つからないときモデル状態を無効にする必要がある場合は、[`[BindRequired]`](#bindrequired-attribute) 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-462">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="c42d1-463">この `[BindRequired]` 動作は、要求本文内の JSON または XML データに対してではなく、ポストされたフォーム データからのモデル バインドに適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-463">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="c42d1-464">要求本文データは、[入力フォーマッタ](#input-formatters)によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-464">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="c42d1-465">型変換エラー</span><span class="sxs-lookup"><span data-stu-id="c42d1-465">Type conversion errors</span></span>

<span data-ttu-id="c42d1-466">ソースは見つかってもそれをターゲットの種類に変換できない場合、無効であることを示すフラグがモデル状態に付けられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-466">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="c42d1-467">前のセクションで説明したように、ターゲットのパラメーターまたはプロパティは null または既定値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-467">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="c42d1-468">`[ApiController]` 属性を持つ API コントローラーでは、モデル状態が無効であると、HTTP 400 の自動応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-468">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="c42d1-469">Razor ページでは、エラー メッセージを含むページが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-469">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="c42d1-470">クライアント側の検証では、それを行わないなら Razor Pages フォームに送信されてしまう不適切なデータのほとんどがキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-470">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="c42d1-471">この検証により、前の強調表示されたコードをトリガーするのが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-471">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="c42d1-472">サンプル アプリには、 **[Submit with Invalid Date]** ボタンが含まれており、これを使用すると、 **[Hire Date]** フィールドに不適切なデータが入力され、そのフォームが送信されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-472">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="c42d1-473">このボタンを使用すると、データ変換エラーが発生したときにページを再表示するためのコードがどのように機能するかを表示できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-473">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="c42d1-474">上のコードでページが再表示されると、無効な入力はフォーム フィールドに表示されません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-474">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="c42d1-475">これは、モデル プロパティが null または既定値に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="c42d1-475">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="c42d1-476">無効な入力はエラー メッセージに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-476">The invalid input does appear in an error message.</span></span> <span data-ttu-id="c42d1-477">しかし、フォーム フィールドに不適切なデータを再表示したい場合は、モデル プロパティを文字列にしてデータ変換を手動で行うことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-477">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="c42d1-478">型変換エラーが結果的にモデル状態エラーになることを望まない場合も同じ方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-478">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="c42d1-479">その場合は、モデル プロパティを文字列にします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-479">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="c42d1-480">単純型</span><span class="sxs-lookup"><span data-stu-id="c42d1-480">Simple types</span></span>

<span data-ttu-id="c42d1-481">モデル バインダーでソース文字列の変換先とすることができる単純型には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-481">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="c42d1-482">Boolean</span><span class="sxs-lookup"><span data-stu-id="c42d1-482">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="c42d1-483">[Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="c42d1-483">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="c42d1-484">Char</span><span class="sxs-lookup"><span data-stu-id="c42d1-484">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="c42d1-485">DateTime</span><span class="sxs-lookup"><span data-stu-id="c42d1-485">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="c42d1-486">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="c42d1-486">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="c42d1-487">Decimal</span><span class="sxs-lookup"><span data-stu-id="c42d1-487">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="c42d1-488">Double</span><span class="sxs-lookup"><span data-stu-id="c42d1-488">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="c42d1-489">Enum</span><span class="sxs-lookup"><span data-stu-id="c42d1-489">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="c42d1-490">Guid</span><span class="sxs-lookup"><span data-stu-id="c42d1-490">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="c42d1-491">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="c42d1-491">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="c42d1-492">Single</span><span class="sxs-lookup"><span data-stu-id="c42d1-492">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="c42d1-493">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="c42d1-493">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="c42d1-494">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="c42d1-494">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="c42d1-495">Uri</span><span class="sxs-lookup"><span data-stu-id="c42d1-495">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="c42d1-496">Version</span><span class="sxs-lookup"><span data-stu-id="c42d1-496">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="c42d1-497">複合型</span><span class="sxs-lookup"><span data-stu-id="c42d1-497">Complex types</span></span>

<span data-ttu-id="c42d1-498">複合型には、バインドする既定のパブリック コンストラクターと書き込み可能なパブリック プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="c42d1-498">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="c42d1-499">モデル バインドが行われると、クラスは既定のパブリック コンストラクターを使用してインスタンス化されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-499">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="c42d1-500">複合型のプロパティごとに、モデル バインドでは名前パターン *prefix.property_name* がないかソースが調べられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-500">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="c42d1-501">何も見つからない場合は、プレフィックスなしで *property_name* だけが探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-501">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="c42d1-502">パラメーターにバインドする場合、プレフィックスはパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="c42d1-502">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="c42d1-503">`PageModel` パブリック プロパティにバインドする場合、プレフィックスはパブリック プロパティ名です。</span><span class="sxs-lookup"><span data-stu-id="c42d1-503">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="c42d1-504">一部の属性には、パラメーター名またはプロパティ名の既定の使用をオーバーライドするための `Prefix` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-504">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="c42d1-505">たとえば、複合型が次の `Instructor` クラスであるとします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-505">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="c42d1-506">プレフィックス = パラメーター名</span><span class="sxs-lookup"><span data-stu-id="c42d1-506">Prefix = parameter name</span></span>

<span data-ttu-id="c42d1-507">バインドされるモデルが `instructorToUpdate` という名前のパラメーターである場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-507">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="c42d1-508">モデル バインドでは、キー `instructorToUpdate.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-508">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="c42d1-509">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-509">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="c42d1-510">プレフィックス = プロパティ名</span><span class="sxs-lookup"><span data-stu-id="c42d1-510">Prefix = property name</span></span>

<span data-ttu-id="c42d1-511">バインドされるモデルがコントローラーの `Instructor` という名前のプロパティか、または `PageModel` クラスである場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-511">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="c42d1-512">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-512">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="c42d1-513">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-513">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="c42d1-514">カスタム プレフィックス</span><span class="sxs-lookup"><span data-stu-id="c42d1-514">Custom prefix</span></span>

<span data-ttu-id="c42d1-515">バインドされるモデルが `instructorToUpdate` という名前のパラメーターであり、かつ `Bind` 属性でプレフィックスとして `Instructor` が指定されている場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-515">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="c42d1-516">モデル バインドでは、キー `Instructor.ID` がないかソースを調べることから始まります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-516">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="c42d1-517">見つからない場合は、プレフィックスなしで `ID` が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-517">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="c42d1-518">複合型のターゲットの属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-518">Attributes for complex type targets</span></span>

<span data-ttu-id="c42d1-519">複合型のモデル バインドを制御するために利用できる組み込みの属性がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-519">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="c42d1-520">ポストされたフォーム データが値のソースである場合、これらの属性はモデル バインドに影響します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-520">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="c42d1-521">ポストされた JSON および XML 要求本文を処理する入力フォーマッタには影響しません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-521">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="c42d1-522">入力フォーマッタについては、[この記事で後ほど](#input-formatters)説明します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-522">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="c42d1-523">[モデル検証](xref:mvc/models/validation#required-attribute)に関するページにある `[Required]` 属性の説明も参照してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-523">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="c42d1-524">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-524">[BindRequired] attribute</span></span>

<span data-ttu-id="c42d1-525">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-525">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="c42d1-526">モデルのプロパティに対してバインドを実行できない場合に、モデル バインドがモデル状態エラーを追加できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-526">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="c42d1-527">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-527">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="c42d1-528">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-528">[BindNever] attribute</span></span>

<span data-ttu-id="c42d1-529">モデルのプロパティにのみに適用でき、メソッドのパラメーターには適用できません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-529">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="c42d1-530">モデル バインドがモデルのプロパティを設定できないようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-530">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="c42d1-531">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-531">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="c42d1-532">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-532">[Bind] attribute</span></span>

<span data-ttu-id="c42d1-533">クラスまたはメソッド パラメーターに適用できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-533">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="c42d1-534">モデルのどのプロパティをモデル バインドに含めるかを指定します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-534">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="c42d1-535">次の例では、任意のハンドラーまたはアクション メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-535">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="c42d1-536">次の例では、`OnPost` メソッドが呼び出されると、`Instructor` モデルの指定されたプロパティのみがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-536">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="c42d1-537">`[Bind]` 属性を使用すれば、"*作成*" シナリオにおいて過剰ポスティングから保護することができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-537">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="c42d1-538">除外されたプロパティはそのままにしておくのではなく null または既定値に設定されるので、この属性は編集シナリオではうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-538">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="c42d1-539">過剰ポスティングを防ぐ場合は、`[Bind]` 属性ではなくビュー モデルをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-539">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="c42d1-540">詳細については、「[過剰ポスティングに関するセキュリティの注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-540">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="c42d1-541">コレクション</span><span class="sxs-lookup"><span data-stu-id="c42d1-541">Collections</span></span>

<span data-ttu-id="c42d1-542">ターゲットが単純型のコレクションである場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-542">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="c42d1-543">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-543">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="c42d1-544">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-544">For example:</span></span>

* <span data-ttu-id="c42d1-545">バインドされるパラメーターが `selectedCourses` という名前の配列であるとした場合:</span><span class="sxs-lookup"><span data-stu-id="c42d1-545">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="c42d1-546">フォームまたはクエリ文字列データは、次のいずれかの形式とすることができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-546">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="c42d1-547">次の形式は、フォーム データでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-547">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="c42d1-548">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る配列が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-548">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="c42d1-549">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="c42d1-549">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="c42d1-550">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="c42d1-550">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="c42d1-551">添え字番号 (... [0] ... [1] ...) を使用するデータ フォーマットでは、確実にそれらがゼロから始まる連続した番号になるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-551">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="c42d1-552">添え字の番号付けで欠落している番号がある場合、欠落している番号の後の項目はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-552">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="c42d1-553">たとえば、添え字が 0、1 の並びではなく、0、2 の並びで振られている場合、2 番目の項目は無視されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-553">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="c42d1-554">ディクショナリ</span><span class="sxs-lookup"><span data-stu-id="c42d1-554">Dictionaries</span></span>

<span data-ttu-id="c42d1-555">`Dictionary` ターゲットの場合、モデル バインドでは *parameter_name* または *property_name* との一致が探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-555">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="c42d1-556">一致が見つからない場合は、サポートされているいずれかの形式がプレフィックスなしで探索されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-556">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="c42d1-557">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-557">For example:</span></span>

* <span data-ttu-id="c42d1-558">ターゲット パラメーターが `selectedCourses` という名前の `Dictionary<int, string>` であるとします:</span><span class="sxs-lookup"><span data-stu-id="c42d1-558">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="c42d1-559">ポストされたフォームまたはクエリ文字列データは、次のいずれかの例のようになります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-559">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="c42d1-560">上記のすべてのフォーマット例において、モデル バインドでは 2 つの項目から成る辞書が `selectedCourses` パラメーターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-560">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="c42d1-561">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="c42d1-561">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="c42d1-562">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="c42d1-562">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="c42d1-563">モデル バインド ルート データとクエリ文字列のグローバリゼーション動作</span><span class="sxs-lookup"><span data-stu-id="c42d1-563">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="c42d1-564">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-564">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="c42d1-565">値をインバリアント カルチャとして扱います。</span><span class="sxs-lookup"><span data-stu-id="c42d1-565">Treat values as invariant culture.</span></span>
* <span data-ttu-id="c42d1-566">URL はカルチャに依存しないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-566">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="c42d1-567">これに対し、フォーム データからの値は、カルチャに依存した変換にかけられます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-567">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="c42d1-568">URL がロケール間で共有可能なように、設計上そのようになっています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-568">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="c42d1-569">ASP.NET Core ルート値プロバイダーとクエリ文字列値プロバイダーでカルチャ依存の変換が行われるようにするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-569">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="c42d1-570"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory> から継承します</span><span class="sxs-lookup"><span data-stu-id="c42d1-570">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="c42d1-571">[QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) または [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) からコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-571">Copy the code from [QueryStringValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="c42d1-572">値プロバイダー コンストラクターに渡された[カルチャ値](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)を [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-572">Replace the [culture value](https://github.com/aspnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="c42d1-573">MVC オプション内の既定値プロバイダー ファクトリを、新しいものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-573">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="c42d1-574">特別なデータ型</span><span class="sxs-lookup"><span data-stu-id="c42d1-574">Special data types</span></span>

<span data-ttu-id="c42d1-575">モデル バインドで処理できる特殊なデータ型がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-575">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="c42d1-576">IFormFile と IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="c42d1-576">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="c42d1-577">HTTP 要求に含まれたアップロード済みファイル。</span><span class="sxs-lookup"><span data-stu-id="c42d1-577">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="c42d1-578">また、複数のファイルに対して `IEnumerable<IFormFile>` もサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-578">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="c42d1-579">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="c42d1-579">CancellationToken</span></span>

<span data-ttu-id="c42d1-580">非同期コントローラーでアクティビティをキャンセルするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-580">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="c42d1-581">FormCollection</span><span class="sxs-lookup"><span data-stu-id="c42d1-581">FormCollection</span></span>

<span data-ttu-id="c42d1-582">ポストされたフォーム データからすべての値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-582">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="c42d1-583">入力フォーマッタ</span><span class="sxs-lookup"><span data-stu-id="c42d1-583">Input formatters</span></span>

<span data-ttu-id="c42d1-584">要求本文内のデータは、JSON、XML、またはその他のいくつかの形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-584">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="c42d1-585">このデータを解析するために、モデル バインドでは、特定のコンテンツの種類を処理するように構成された "*入力フォーマッタ*" が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-585">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="c42d1-586">既定では、ASP.NET Core には JSON データ処理用の JSON ベースの入力フォーマッタが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-586">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="c42d1-587">他のコンテンツの種類については対応する他のフォーマッタを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-587">You can add other formatters for other content types.</span></span>

<span data-ttu-id="c42d1-588">ASP.NET Core では、[Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性に基づいて入力フォーマッタが選択されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-588">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="c42d1-589">属性が存在しない場合は、[Content-Type ヘッダー](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-589">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="c42d1-590">組み込みの XML 入力フォーマッタを使用するには:</span><span class="sxs-lookup"><span data-stu-id="c42d1-590">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="c42d1-591">`Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-591">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="c42d1-592">`Startup.ConfigureServices` で、<xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> または <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-592">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="c42d1-593">要求本文で XML を必要とするコントローラー クラスまたはアクション メソッドに `Consumes` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-593">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="c42d1-594">詳細については、「[XML シリアル化の概要](/dotnet/standard/serialization/introducing-xml-serialization)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-594">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="c42d1-595">指定された型をモデル バインドから除外する</span><span class="sxs-lookup"><span data-stu-id="c42d1-595">Exclude specified types from model binding</span></span>

<span data-ttu-id="c42d1-596">モデル バインドおよび検証システムの動作は、[ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) によって駆動されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-596">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="c42d1-597">`ModelMetadata` については、詳細プロバイダーを [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) に追加してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-597">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="c42d1-598">組み込みの詳細プロバイダーは、指定された型に対してモデル バインドまたは検証を無効にする場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-598">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="c42d1-599">指定された型のすべてのモデルに対してモデル バインドを無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-599">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c42d1-600">たとえば、`System.Version` 型のすべてのモデルに対してモデル バインドを無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-600">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="c42d1-601">指定された型のプロパティに対して検証を無効にするには、`Startup.ConfigureServices` に <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-601">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c42d1-602">たとえば、`System.Guid` 型のプロパティに対して検証を無効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c42d1-602">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="c42d1-603">カスタム モデル バインダー</span><span class="sxs-lookup"><span data-stu-id="c42d1-603">Custom model binders</span></span>

<span data-ttu-id="c42d1-604">モデル バインドを拡張するには、カスタム モデル バインダーを記述し、`[ModelBinder]` 属性を使用してそれを特定のターゲット向けに選択します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-604">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="c42d1-605">詳細については、「[custom model binding](xref:mvc/advanced/custom-model-binding)」 (カスタム モデル バインド) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c42d1-605">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="c42d1-606">手動によるモデル バインド</span><span class="sxs-lookup"><span data-stu-id="c42d1-606">Manual model binding</span></span>

<span data-ttu-id="c42d1-607">モデル バインドは、<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> メソッドを使用して手動で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-607">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="c42d1-608">このメソッドは `ControllerBase` クラスと `PageModel` クラスの両方で定義されています。</span><span class="sxs-lookup"><span data-stu-id="c42d1-608">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="c42d1-609">メソッドのオーバーロードにより、使用するプレフィックスと値プロバイダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-609">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="c42d1-610">モデル バインドが失敗した場合は、メソッドから `false` が返されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-610">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="c42d1-611">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c42d1-611">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="c42d1-612">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="c42d1-612">[FromServices] attribute</span></span>

<span data-ttu-id="c42d1-613">この属性の名前は、データ ソースを指定するモデル バインド属性のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="c42d1-613">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="c42d1-614">ただし、それは、値プロバイダーからのデータ バインドを説明するものではありません。</span><span class="sxs-lookup"><span data-stu-id="c42d1-614">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="c42d1-615">[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーから型のインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="c42d1-615">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c42d1-616">その目的は、特定のメソッドが呼び出された場合にのみサービスを必要するときにコンストラクターの挿入の代替手段を提供することにあります。</span><span class="sxs-lookup"><span data-stu-id="c42d1-616">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c42d1-617">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c42d1-617">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
