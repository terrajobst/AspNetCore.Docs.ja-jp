---
title: ASP.NET Core Web API における Json パッチ
author: rick-anderson
description: ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: cf1a00c1928652bf5210b2442087209e23b8868e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652952"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="77eb4-103">ASP.NET Core Web API における Json パッチ</span><span class="sxs-lookup"><span data-stu-id="77eb4-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="77eb4-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="77eb4-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77eb4-105">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="77eb4-106">パッケージ インストール</span><span class="sxs-lookup"><span data-stu-id="77eb4-106">Package installation</span></span>

<span data-ttu-id="77eb4-107">JSON パッチのサポートは、`Microsoft.AspNetCore.Mvc.NewtonsoftJson` パッケージを使用して有効にされます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-107">Support for JsonPatch is enabled using the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package.</span></span> <span data-ttu-id="77eb4-108">この機能を有効にするには、アプリで以下を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="77eb4-108">To enable this feature, apps must:</span></span>

* <span data-ttu-id="77eb4-109">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="77eb4-109">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
* <span data-ttu-id="77eb4-110">プロジェクトの `Startup.ConfigureServices` メソッドを更新し、`AddNewtonsoftJson` への呼び出しを含めるようにします。</span><span class="sxs-lookup"><span data-stu-id="77eb4-110">Update the project's `Startup.ConfigureServices` method to include a call to `AddNewtonsoftJson`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

<span data-ttu-id="77eb4-111">`AddNewtonsoftJson` は MVC サービス登録メソッドと互換性があります。</span><span class="sxs-lookup"><span data-stu-id="77eb4-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="77eb4-112">JsonPatch、AddNewtonsoftJson、および System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="77eb4-112">JsonPatch, AddNewtonsoftJson, and System.Text.Json</span></span>
  
<span data-ttu-id="77eb4-113">`AddNewtonsoftJson` では、`System.Text.Json`すべての**JSON コンテンツの書式設定に使用される** ベースの入力と出力フォーマッタが置換されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-113">`AddNewtonsoftJson` replaces the `System.Text.Json` based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="77eb4-114">他のフォーマッタを変更しないまま、`JsonPatch` を使用して `Newtonsoft.Json` のサポートを追加するには、プロジェクトの `Startup.ConfigureServices` を次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-114">To add support for `JsonPatch` using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="77eb4-115">上記のコードでは、[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) への参照と、次の using ステートメントが必要となります。</span><span class="sxs-lookup"><span data-stu-id="77eb4-115">The preceding code requires a reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) and the following using statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="77eb4-116">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="77eb4-116">PATCH HTTP request method</span></span>

<span data-ttu-id="77eb4-117">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="77eb4-118">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="77eb4-119">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="77eb4-119">JSON Patch</span></span>

<span data-ttu-id="77eb4-120">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="77eb4-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="77eb4-121">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="77eb4-122">各操作では、配列要素の追加やプロパティ値の置換など、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-122">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="77eb4-123">たとえば、次の JSON ドキュメントは、1 つのリソースとそのリソースに対応する JSON パッチ ドキュメント、およびパッチ操作を適用した結果を表しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-123">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="77eb4-124">リソースの例</span><span class="sxs-lookup"><span data-stu-id="77eb4-124">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="77eb4-125">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="77eb4-125">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="77eb4-126">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-126">In the preceding JSON:</span></span>

* <span data-ttu-id="77eb4-127">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-127">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="77eb4-128">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-128">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="77eb4-129">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-129">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="77eb4-130">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="77eb4-130">Resource after patch</span></span>

<span data-ttu-id="77eb4-131">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-131">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="77eb4-132">JSON パッチ ドキュメントをリソースに適用することで行われる変更は、アトミックです。つまり、リスト内の任意の操作が失敗した場合は、リスト内のどの操作も適用されません。</span><span class="sxs-lookup"><span data-stu-id="77eb4-132">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="77eb4-133">パス構文</span><span class="sxs-lookup"><span data-stu-id="77eb4-133">Path syntax</span></span>

<span data-ttu-id="77eb4-134">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-134">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="77eb4-135">たとえば、「 `"/address/zipCode"` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-135">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="77eb4-136">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-136">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="77eb4-137">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="77eb4-137">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="77eb4-138">配列の末尾への `add` では、インデックス番号ではなく、`/addresses/-` のようにハイフン (-) を使用します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-138">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="77eb4-139">運用</span><span class="sxs-lookup"><span data-stu-id="77eb4-139">Operations</span></span>

<span data-ttu-id="77eb4-140">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-140">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="77eb4-141">操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-141">Operation</span></span>  | <span data-ttu-id="77eb4-142">説明</span><span class="sxs-lookup"><span data-stu-id="77eb4-142">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="77eb4-143">プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-143">Add a property or array element.</span></span> <span data-ttu-id="77eb4-144">既存のプロパティの場合: 値を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-144">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="77eb4-145">プロパティまたは配列要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-145">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="77eb4-146">`remove` の後に、同じ場所で `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-146">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="77eb4-147">ソースからの `remove` の後に、ソースからの値を使用した宛先への `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-147">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="77eb4-148">ソースからの値を使用した宛先への `add` と同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-148">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="77eb4-149">`path` の値が指定された `value`と一致する場合に、成功の状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-149">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="77eb4-150">ASP.NET Core における JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="77eb4-150">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="77eb4-151">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-151">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="77eb4-152">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="77eb4-152">Action method code</span></span>

<span data-ttu-id="77eb4-153">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="77eb4-153">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="77eb4-154">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-154">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="77eb4-155">通常は `JsonPatchDocument<T>` を利用して、`[FromBody]` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-155">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="77eb4-156">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-156">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="77eb4-157">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-157">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="77eb4-158">サンプル アプリからのこのコードでは、以下の `Customer` モデルを利用します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-158">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="77eb4-159">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="77eb4-159">The sample action method:</span></span>

* <span data-ttu-id="77eb4-160">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-160">Constructs a `Customer`.</span></span>
* <span data-ttu-id="77eb4-161">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="77eb4-161">Applies the patch.</span></span>
* <span data-ttu-id="77eb4-162">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-162">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="77eb4-163">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-163">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="77eb4-164">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="77eb4-164">Model state</span></span>

<span data-ttu-id="77eb4-165">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-165">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="77eb4-166">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-166">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="77eb4-167">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-167">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="77eb4-168">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="77eb4-168">Dynamic objects</span></span>

<span data-ttu-id="77eb4-169">次のアクション メソッドの例では、動的オブジェクトにパッチを適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-169">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="77eb4-170">追加の操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-170">The add operation</span></span>

* <span data-ttu-id="77eb4-171">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-171">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="77eb4-172">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-172">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="77eb4-173">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="77eb4-173">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="77eb4-174">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-174">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="77eb4-175">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-175">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="77eb4-176">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-176">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="77eb4-177">削除の操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-177">The remove operation</span></span>

* <span data-ttu-id="77eb4-178">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-178">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="77eb4-179">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="77eb4-179">If `path` points to a property:</span></span>
  * <span data-ttu-id="77eb4-180">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-180">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="77eb4-181">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="77eb4-181">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="77eb4-182">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-182">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="77eb4-183">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-183">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="77eb4-184">次のサンプル パッチ ドキュメントでは、`CustomerName` に null を設定し、`Orders[0]` を削除します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-184">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="77eb4-185">置換の操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-185">The replace operation</span></span>

<span data-ttu-id="77eb4-186">この操作は、`remove` が後に続く `add` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-186">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="77eb4-187">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Orders[0]` を新しい `Order` オブジェクトに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-187">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="77eb4-188">移動の操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-188">The move operation</span></span>

* <span data-ttu-id="77eb4-189">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`remove` 要素に対して `from` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-189">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="77eb4-190">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`remove` プロパティに対して `from` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-190">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="77eb4-191">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="77eb4-191">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="77eb4-192">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-192">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="77eb4-193">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`remove` プロパティに対して `from` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-193">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="77eb4-194">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="77eb4-194">The following sample patch document:</span></span>

* <span data-ttu-id="77eb4-195">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="77eb4-195">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="77eb4-196">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-196">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="77eb4-197">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-197">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="77eb4-198">コピー操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-198">The copy operation</span></span>

<span data-ttu-id="77eb4-199">この操作は、最後の `move` 手順がない `remove` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-199">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="77eb4-200">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="77eb4-200">The following sample patch document:</span></span>

* <span data-ttu-id="77eb4-201">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="77eb4-201">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="77eb4-202">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-202">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="77eb4-203">テストの操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-203">The test operation</span></span>

<span data-ttu-id="77eb4-204">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-204">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="77eb4-205">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-205">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="77eb4-206">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-206">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="77eb4-207">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="77eb4-207">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="77eb4-208">コードの入手</span><span class="sxs-lookup"><span data-stu-id="77eb4-208">Get the code</span></span>

<span data-ttu-id="77eb4-209">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="77eb4-210">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="77eb4-210">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="77eb4-211">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-211">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="77eb4-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="77eb4-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="77eb4-213">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="77eb4-213">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="77eb4-214">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="77eb4-214">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="77eb4-215">Body: *json プロジェクトフォルダーから json 修正*プログラムドキュメントのサンプルのいずれかをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-215">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77eb4-216">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="77eb4-216">Additional resources</span></span>

* [<span data-ttu-id="77eb4-217">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="77eb4-217">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="77eb4-218">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="77eb4-218">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="77eb4-219">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="77eb4-219">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="77eb4-220">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="77eb4-220">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="77eb4-221">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-221">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="77eb4-222">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="77eb4-222">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77eb4-223">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-223">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="77eb4-224">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="77eb4-224">PATCH HTTP request method</span></span>

<span data-ttu-id="77eb4-225">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-225">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="77eb4-226">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-226">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="77eb4-227">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="77eb4-227">JSON Patch</span></span>

<span data-ttu-id="77eb4-228">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="77eb4-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="77eb4-229">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-229">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="77eb4-230">各操作では、配列要素の追加やプロパティ値の置換など、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-230">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="77eb4-231">たとえば、次の JSON ドキュメントは、1 つのリソースとそのリソースに対応する JSON パッチ ドキュメント、およびパッチ操作を適用した結果を表しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-231">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="77eb4-232">リソースの例</span><span class="sxs-lookup"><span data-stu-id="77eb4-232">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="77eb4-233">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="77eb4-233">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="77eb4-234">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-234">In the preceding JSON:</span></span>

* <span data-ttu-id="77eb4-235">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-235">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="77eb4-236">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-236">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="77eb4-237">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-237">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="77eb4-238">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="77eb4-238">Resource after patch</span></span>

<span data-ttu-id="77eb4-239">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-239">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="77eb4-240">JSON パッチ ドキュメントをリソースに適用することで行われる変更は、アトミックです。つまり、リスト内の任意の操作が失敗した場合は、リスト内のどの操作も適用されません。</span><span class="sxs-lookup"><span data-stu-id="77eb4-240">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="77eb4-241">パス構文</span><span class="sxs-lookup"><span data-stu-id="77eb4-241">Path syntax</span></span>

<span data-ttu-id="77eb4-242">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-242">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="77eb4-243">たとえば、「 `"/address/zipCode"` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-243">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="77eb4-244">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-244">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="77eb4-245">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="77eb4-245">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="77eb4-246">配列の末尾への `add` では、インデックス番号ではなく、`/addresses/-` のようにハイフン (-) を使用します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-246">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="77eb4-247">運用</span><span class="sxs-lookup"><span data-stu-id="77eb4-247">Operations</span></span>

<span data-ttu-id="77eb4-248">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-248">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="77eb4-249">操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-249">Operation</span></span>  | <span data-ttu-id="77eb4-250">説明</span><span class="sxs-lookup"><span data-stu-id="77eb4-250">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="77eb4-251">プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-251">Add a property or array element.</span></span> <span data-ttu-id="77eb4-252">既存のプロパティの場合: 値を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-252">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="77eb4-253">プロパティまたは配列要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-253">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="77eb4-254">`remove` の後に、同じ場所で `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-254">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="77eb4-255">ソースからの `remove` の後に、ソースからの値を使用した宛先への `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-255">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="77eb4-256">ソースからの値を使用した宛先への `add` と同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-256">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="77eb4-257">`path` の値が指定された `value`と一致する場合に、成功の状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-257">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="77eb4-258">ASP.NET Core における JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="77eb4-258">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="77eb4-259">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-259">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="77eb4-260">パッケージは、[Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) メタパッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-260">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="77eb4-261">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="77eb4-261">Action method code</span></span>

<span data-ttu-id="77eb4-262">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="77eb4-262">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="77eb4-263">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-263">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="77eb4-264">通常は `JsonPatchDocument<T>` を利用して、`[FromBody]` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-264">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="77eb4-265">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-265">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="77eb4-266">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-266">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="77eb4-267">サンプル アプリからのこのコードでは、以下の `Customer` モデルを利用します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-267">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="77eb4-268">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="77eb4-268">The sample action method:</span></span>

* <span data-ttu-id="77eb4-269">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-269">Constructs a `Customer`.</span></span>
* <span data-ttu-id="77eb4-270">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="77eb4-270">Applies the patch.</span></span>
* <span data-ttu-id="77eb4-271">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-271">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="77eb4-272">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-272">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="77eb4-273">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="77eb4-273">Model state</span></span>

<span data-ttu-id="77eb4-274">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-274">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="77eb4-275">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-275">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="77eb4-276">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-276">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="77eb4-277">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="77eb4-277">Dynamic objects</span></span>

<span data-ttu-id="77eb4-278">次のアクション メソッドの例では、動的オブジェクトにパッチを適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="77eb4-278">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="77eb4-279">追加の操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-279">The add operation</span></span>

* <span data-ttu-id="77eb4-280">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-280">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="77eb4-281">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-281">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="77eb4-282">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="77eb4-282">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="77eb4-283">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-283">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="77eb4-284">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-284">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="77eb4-285">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-285">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="77eb4-286">削除の操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-286">The remove operation</span></span>

* <span data-ttu-id="77eb4-287">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-287">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="77eb4-288">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="77eb4-288">If `path` points to a property:</span></span>
  * <span data-ttu-id="77eb4-289">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-289">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="77eb4-290">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="77eb4-290">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="77eb4-291">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-291">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="77eb4-292">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-292">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="77eb4-293">次のサンプル パッチ ドキュメントでは、`CustomerName` に null を設定し、`Orders[0]` を削除します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-293">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="77eb4-294">置換の操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-294">The replace operation</span></span>

<span data-ttu-id="77eb4-295">この操作は、`remove` が後に続く `add` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-295">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="77eb4-296">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Orders[0]` を新しい `Order` オブジェクトに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-296">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="77eb4-297">移動の操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-297">The move operation</span></span>

* <span data-ttu-id="77eb4-298">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`remove` 要素に対して `from` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-298">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="77eb4-299">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`remove` プロパティに対して `from` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-299">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="77eb4-300">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="77eb4-300">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="77eb4-301">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-301">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="77eb4-302">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`remove` プロパティに対して `from` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-302">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="77eb4-303">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="77eb4-303">The following sample patch document:</span></span>

* <span data-ttu-id="77eb4-304">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="77eb4-304">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="77eb4-305">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-305">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="77eb4-306">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-306">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="77eb4-307">コピー操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-307">The copy operation</span></span>

<span data-ttu-id="77eb4-308">この操作は、最後の `move` 手順がない `remove` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="77eb4-308">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="77eb4-309">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="77eb4-309">The following sample patch document:</span></span>

* <span data-ttu-id="77eb4-310">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="77eb4-310">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="77eb4-311">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-311">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="77eb4-312">テストの操作</span><span class="sxs-lookup"><span data-stu-id="77eb4-312">The test operation</span></span>

<span data-ttu-id="77eb4-313">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-313">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="77eb4-314">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-314">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="77eb4-315">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-315">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="77eb4-316">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="77eb4-316">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="77eb4-317">コードの入手</span><span class="sxs-lookup"><span data-stu-id="77eb4-317">Get the code</span></span>

<span data-ttu-id="77eb4-318">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="77eb4-319">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="77eb4-319">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="77eb4-320">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="77eb4-320">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="77eb4-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="77eb4-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="77eb4-322">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="77eb4-322">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="77eb4-323">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="77eb4-323">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="77eb4-324">Body: *json プロジェクトフォルダーから json 修正*プログラムドキュメントのサンプルのいずれかをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-324">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77eb4-325">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="77eb4-325">Additional resources</span></span>

* [<span data-ttu-id="77eb4-326">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="77eb4-326">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="77eb4-327">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="77eb4-327">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="77eb4-328">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="77eb4-328">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="77eb4-329">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="77eb4-329">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="77eb4-330">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="77eb4-330">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="77eb4-331">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="77eb4-331">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
