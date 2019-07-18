---
title: ASP.NET Core Web API における Json パッチ
author: tdykstra
description: ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 97264903d85dbb397e85fdbf7b070e2aaae74bc8
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815549"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="fba83-103">ASP.NET Core Web API における Json パッチ</span><span class="sxs-lookup"><span data-stu-id="fba83-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="fba83-104">著者: [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="fba83-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="fba83-105">この記事では、ASP.NET Core Web API において JSON パッチ要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="fba83-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="fba83-106">PATCH HTTP 要求メソッド</span><span class="sxs-lookup"><span data-stu-id="fba83-106">PATCH HTTP request method</span></span>

<span data-ttu-id="fba83-107">PUT および [PATCH](https://tools.ietf.org/html/rfc5789) メソッドは、既存のリソースを更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fba83-107">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="fba83-108">両者の違いは、PUT ではリソース全体を置き換えるのに対して、PATCH では変更箇所だけを指定することです。</span><span class="sxs-lookup"><span data-stu-id="fba83-108">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="fba83-109">JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="fba83-109">JSON Patch</span></span>

<span data-ttu-id="fba83-110">[JSON パッチ](https://tools.ietf.org/html/rfc6902)は、リソースに適用される更新を指定するための形式です。</span><span class="sxs-lookup"><span data-stu-id="fba83-110">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="fba83-111">JSON パッチ ドキュメントには、"*操作*" の配列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="fba83-111">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="fba83-112">各操作では、配列要素の追加やプロパティ値の置換など、特定の種類の変更を識別します。</span><span class="sxs-lookup"><span data-stu-id="fba83-112">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="fba83-113">たとえば、次の JSON ドキュメントは、1 つのリソースとそのリソースに対応する JSON パッチ ドキュメント、およびパッチ操作を適用した結果を表しています。</span><span class="sxs-lookup"><span data-stu-id="fba83-113">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="fba83-114">リソースの例</span><span class="sxs-lookup"><span data-stu-id="fba83-114">Resource example</span></span>

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="fba83-115">JSON パッチの例</span><span class="sxs-lookup"><span data-stu-id="fba83-115">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="fba83-116">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="fba83-116">In the preceding JSON:</span></span>

* <span data-ttu-id="fba83-117">`op` プロパティでは、操作の種類を指示します。</span><span class="sxs-lookup"><span data-stu-id="fba83-117">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="fba83-118">`path` プロパティでは、更新する要素を指示します。</span><span class="sxs-lookup"><span data-stu-id="fba83-118">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="fba83-119">`value` プロパティでは、新しい値を指定します。</span><span class="sxs-lookup"><span data-stu-id="fba83-119">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="fba83-120">パッチ後のリソース</span><span class="sxs-lookup"><span data-stu-id="fba83-120">Resource after patch</span></span>

<span data-ttu-id="fba83-121">上記の JSON パッチ ドキュメントを適用した後のリソースを、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="fba83-121">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="fba83-122">JSON パッチ ドキュメントをリソースに適用することで行われる変更は、アトミックです。つまり、リスト内の任意の操作が失敗した場合は、リスト内のどの操作も適用されません。</span><span class="sxs-lookup"><span data-stu-id="fba83-122">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="fba83-123">パス構文</span><span class="sxs-lookup"><span data-stu-id="fba83-123">Path syntax</span></span>

<span data-ttu-id="fba83-124">操作オブジェクトの [path](https://tools.ietf.org/html/rfc6901) プロパティでは、レベル間にスラッシュを保持します。</span><span class="sxs-lookup"><span data-stu-id="fba83-124">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="fba83-125">たとえば、`"/address/zipCode"` のようにします。</span><span class="sxs-lookup"><span data-stu-id="fba83-125">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="fba83-126">0 から始まるインデックスは、配列の要素を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fba83-126">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="fba83-127">`addresses` 配列の最初の要素は、`/addresses/0` にあります。</span><span class="sxs-lookup"><span data-stu-id="fba83-127">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="fba83-128">配列の末尾への `add` では、インデックス番号ではなく、`/addresses/-` のようにハイフン (-) を使用します。</span><span class="sxs-lookup"><span data-stu-id="fba83-128">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="fba83-129">オペレーション</span><span class="sxs-lookup"><span data-stu-id="fba83-129">Operations</span></span>

<span data-ttu-id="fba83-130">次の表は、[JSON パッチの仕様](https://tools.ietf.org/html/rfc6902)に定義されている、サポートされる操作を示しています。</span><span class="sxs-lookup"><span data-stu-id="fba83-130">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="fba83-131">操作</span><span class="sxs-lookup"><span data-stu-id="fba83-131">Operation</span></span>  | <span data-ttu-id="fba83-132">メモ</span><span class="sxs-lookup"><span data-stu-id="fba83-132">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="fba83-133">プロパティまたは配列要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="fba83-133">Add a property or array element.</span></span> <span data-ttu-id="fba83-134">既存のプロパティの場合: 値を設定します。</span><span class="sxs-lookup"><span data-stu-id="fba83-134">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="fba83-135">プロパティまたは配列要素を削除します。</span><span class="sxs-lookup"><span data-stu-id="fba83-135">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="fba83-136">`remove` の後に、同じ場所で `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="fba83-136">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="fba83-137">ソースからの `remove` の後に、ソースからの値を使用した宛先への `add` が続く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="fba83-137">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="fba83-138">ソースからの値を使用した宛先への `add` と同じです。</span><span class="sxs-lookup"><span data-stu-id="fba83-138">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="fba83-139">`path` の値が指定された `value`と一致する場合に、成功の状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="fba83-139">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="fba83-140">ASP.NET Core における JSON パッチ</span><span class="sxs-lookup"><span data-stu-id="fba83-140">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="fba83-141">JSON パッチの ASP.NET Core 実装は、[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet パッケージ内に提供されています。</span><span class="sxs-lookup"><span data-stu-id="fba83-141">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="fba83-142">パッケージは、[ Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) メタパッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="fba83-142">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="fba83-143">アクション メソッド コード</span><span class="sxs-lookup"><span data-stu-id="fba83-143">Action method code</span></span>

<span data-ttu-id="fba83-144">API コントローラーにおける JSON パッチ用のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="fba83-144">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="fba83-145">`HttpPatch` 属性によって注釈されます。</span><span class="sxs-lookup"><span data-stu-id="fba83-145">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="fba83-146">通常は [FromBody] を利用して、`JsonPatchDocument<T>` を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="fba83-146">Accepts a `JsonPatchDocument<T>`, typically with [FromBody].</span></span>
* <span data-ttu-id="fba83-147">パッチ ドキュメント上の `ApplyTo` を呼び出して、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="fba83-147">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="fba83-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fba83-148">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="fba83-149">サンプル アプリからのこのコードでは、以下の `Customer` モデルを利用します。</span><span class="sxs-lookup"><span data-stu-id="fba83-149">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="fba83-150">同じアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="fba83-150">The sample action method:</span></span>

* <span data-ttu-id="fba83-151">`Customer` を構築します。</span><span class="sxs-lookup"><span data-stu-id="fba83-151">Constructs a `Customer`.</span></span>
* <span data-ttu-id="fba83-152">パッチを適用します</span><span class="sxs-lookup"><span data-stu-id="fba83-152">Applies the patch.</span></span>
* <span data-ttu-id="fba83-153">応答の本文内で結果を返します。</span><span class="sxs-lookup"><span data-stu-id="fba83-153">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="fba83-154">実際のアプリでは、コードはデータベースなどの保存場所からデータを取得し、パッチを適用した後にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="fba83-154">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="fba83-155">モデルの状態</span><span class="sxs-lookup"><span data-stu-id="fba83-155">Model state</span></span>

<span data-ttu-id="fba83-156">前のアクション メソッドの例では、パラメーターの 1 つとしてモデルの状態を取得する `ApplyTo` のオーバーロードを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="fba83-156">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="fba83-157">このオプションを利用すると、応答内にエラー メッセージを取得できます。</span><span class="sxs-lookup"><span data-stu-id="fba83-157">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="fba83-158">次の例では、`test` 操作に対する 400 Bad Request 応答の本文を示しています。</span><span class="sxs-lookup"><span data-stu-id="fba83-158">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="fba83-159">動的オブジェクト</span><span class="sxs-lookup"><span data-stu-id="fba83-159">Dynamic objects</span></span>

<span data-ttu-id="fba83-160">次のアクション メソッドの例では、動的オブジェクトにパッチを適用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="fba83-160">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="fba83-161">追加の操作</span><span class="sxs-lookup"><span data-stu-id="fba83-161">The add operation</span></span>

* <span data-ttu-id="fba83-162">`path` が配列要素を参照する場合: `path` によって指定された要素の前に新しい要素を挿入します。</span><span class="sxs-lookup"><span data-stu-id="fba83-162">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="fba83-163">`path` がプロパティを参照する場合: プロパティ値を設定します。</span><span class="sxs-lookup"><span data-stu-id="fba83-163">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="fba83-164">`path` が存在しない場所を参照する場合:</span><span class="sxs-lookup"><span data-stu-id="fba83-164">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="fba83-165">パッチへのリソースが動的オブジェクトの場合: プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="fba83-165">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="fba83-166">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="fba83-166">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="fba83-167">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Order` オブジェクトを `Orders` 配列の末尾に追加します。</span><span class="sxs-lookup"><span data-stu-id="fba83-167">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="fba83-168">削除の操作</span><span class="sxs-lookup"><span data-stu-id="fba83-168">The remove operation</span></span>

* <span data-ttu-id="fba83-169">`path` が配列要素を参照する場合: 配列を削除します。</span><span class="sxs-lookup"><span data-stu-id="fba83-169">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="fba83-170">`path` がプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="fba83-170">If `path` points to a property:</span></span>
  * <span data-ttu-id="fba83-171">パッチへのリソースが動的オブジェクトの場合: プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="fba83-171">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="fba83-172">パッチへのリソースが静的オブジェクトの場合:</span><span class="sxs-lookup"><span data-stu-id="fba83-172">If resource to patch is a static object:</span></span> 
    * <span data-ttu-id="fba83-173">プロパティが null 値を許容する場合: null を設定します。</span><span class="sxs-lookup"><span data-stu-id="fba83-173">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="fba83-174">プロパティが null 値を許容しない場合: `default<T>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="fba83-174">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="fba83-175">次のサンプル パッチ ドキュメントでは、`CustomerName` に null を設定し、`Orders[0]` を削除します。</span><span class="sxs-lookup"><span data-stu-id="fba83-175">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="fba83-176">置換の操作</span><span class="sxs-lookup"><span data-stu-id="fba83-176">The replace operation</span></span>

<span data-ttu-id="fba83-177">この操作は、`add` が後に続く `remove` と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="fba83-177">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="fba83-178">次のサンプル パッチ ドキュメントでは、`CustomerName` の値を設定して、`Orders[0]` を新しい `Order` オブジェクトに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="fba83-178">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="fba83-179">移動の操作</span><span class="sxs-lookup"><span data-stu-id="fba83-179">The move operation</span></span>

* <span data-ttu-id="fba83-180">`path` が配列要素を参照する場合: `from` 要素を `path` 要素の場所にコピーしてから、`from` 要素に対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="fba83-180">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="fba83-181">`path` がプロパティを参照する場合: `from` プロパティの値を `path` プロパティにコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="fba83-181">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="fba83-182">`path` が存在しないプロパティを参照する場合:</span><span class="sxs-lookup"><span data-stu-id="fba83-182">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="fba83-183">パッチへのリソースが静的オブジェクトの場合: 要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="fba83-183">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="fba83-184">パッチへのリソースが動的オブジェクトの場合: `from` プロパティを `path`によって指示された場所にコピーしてから、`from` プロパティに対して `remove` 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="fba83-184">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="fba83-185">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="fba83-185">The following sample patch document:</span></span>

* <span data-ttu-id="fba83-186">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="fba83-186">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="fba83-187">`Orders[0].OrderName` に null を設定します。</span><span class="sxs-lookup"><span data-stu-id="fba83-187">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="fba83-188">`Orders[1]` を `Orders[0]` の前に移動します。</span><span class="sxs-lookup"><span data-stu-id="fba83-188">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="fba83-189">コピー操作</span><span class="sxs-lookup"><span data-stu-id="fba83-189">The copy operation</span></span>

<span data-ttu-id="fba83-190">この操作は、最後の `remove` 手順がない `move` 操作と機能的に同じです。</span><span class="sxs-lookup"><span data-stu-id="fba83-190">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="fba83-191">次のサンプル パッチ ドキュメントでは、以下の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="fba83-191">The following sample patch document:</span></span>

* <span data-ttu-id="fba83-192">`Orders[0].OrderName` の値を `CustomerName` にコピーします。</span><span class="sxs-lookup"><span data-stu-id="fba83-192">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="fba83-193">`Orders[1]` のコピーを `Orders[0]` の前に挿入します。</span><span class="sxs-lookup"><span data-stu-id="fba83-193">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="fba83-194">テストの操作</span><span class="sxs-lookup"><span data-stu-id="fba83-194">The test operation</span></span>

<span data-ttu-id="fba83-195">`path` によって指示された場所にある値が `value` に指定されている値と異なる場合、要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="fba83-195">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="fba83-196">その場合は、パッチ ドキュメントにあるその他すべての操作が成功したとしても、PATCH 要求全体が失敗します。</span><span class="sxs-lookup"><span data-stu-id="fba83-196">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="fba83-197">`test` 操作は、同時実行の競合がある場合に、一般的に更新を防ぐために使用されます。</span><span class="sxs-lookup"><span data-stu-id="fba83-197">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="fba83-198">`CustomerName` の初期値が "John" の場合、テストは失敗するため、次のサンプル パッチ ドキュメントは無効です。</span><span class="sxs-lookup"><span data-stu-id="fba83-198">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="fba83-199">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="fba83-199">Get the code</span></span>

<span data-ttu-id="fba83-200">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)します。</span><span class="sxs-lookup"><span data-stu-id="fba83-200">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="fba83-201">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fba83-201">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fba83-202">サンプルをテストするには、アプリを実行して、次の設定を使って HTTP 要求を送信します。</span><span class="sxs-lookup"><span data-stu-id="fba83-202">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="fba83-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="fba83-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="fba83-204">HTTP メソッド: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="fba83-204">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="fba83-205">ヘッダー: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="fba83-205">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="fba83-206">本文:*JSON* プロジェクト フォルダーから JSON パッチ ドキュメント サンプルの 1 つをコピーして貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="fba83-206">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fba83-207">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fba83-207">Additional resources</span></span>

* [<span data-ttu-id="fba83-208">IETF RFC 5789 PATCH メソッドの仕様</span><span class="sxs-lookup"><span data-stu-id="fba83-208">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="fba83-209">IETF RFC 6902 JSON パッチの仕様</span><span class="sxs-lookup"><span data-stu-id="fba83-209">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="fba83-210">IETF RFC 6901 JSON パッチのパス形式の仕様</span><span class="sxs-lookup"><span data-stu-id="fba83-210">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="fba83-211">[JSON パッチ ドキュメント](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="fba83-211">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="fba83-212">JSON パッチ ドキュメントを作成するためのリソースへのリンクを含みます。</span><span class="sxs-lookup"><span data-stu-id="fba83-212">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="fba83-213">ASP.NET Core JSON パッチのソース コード</span><span class="sxs-lookup"><span data-stu-id="fba83-213">ASP.NET Core JSON Patch source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)
