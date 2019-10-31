---
title: ASP.NET Core フィルター
author: ardalis
description: フィルターのしくみと ASP.NET Core でそれを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: mvc/controllers/filters
ms.openlocfilehash: 6a83b8e85b68a9b8796aeed2fd39108dbeed3266
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190534"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="dcb0a-103">ASP.NET Core フィルター</span><span class="sxs-lookup"><span data-stu-id="dcb0a-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="dcb0a-104">作成者: [Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dcb0a-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dcb0a-105">ASP.NET Core で*フィルター*を使用すると、要求処理パイプラインの特定のステージの前または後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="dcb0a-106">組み込みのフィルターでは次のようなタスクが処理されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="dcb0a-107">許可 (ユーザーに許可が与えられていないリソースの場合、アクセスを禁止する)。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="dcb0a-108">応答キャッシュ (要求パイプラインを迂回し、キャッシュされている応答を返す)。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="dcb0a-109">横断的な問題を処理するカスタム フィルターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="dcb0a-110">横断的な問題の例には、エラー処理、キャッシュ、構成、認証、ログなどがあります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="dcb0a-111">フィルターにより、コードの重複が回避されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="dcb0a-112">たとえば、エラー処理例外フィルターではエラー処理を統合できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="dcb0a-113">このドキュメントは、Razor Pages、API コントローラー、ビューのあるコントローラーに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="dcb0a-114">[サンプルを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="dcb0a-115">フィルターのしくみ</span><span class="sxs-lookup"><span data-stu-id="dcb0a-115">How filters work</span></span>

<span data-ttu-id="dcb0a-116">*ASP.NET Core のアクション呼び出しパイプライン*内で実行されるフィルターは、*フィルター パイプライン*と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="dcb0a-117">フィルター パイプラインは、ASP.NET Core が実行するアクションを選択した後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求は、他のミドルウェア、ルーティング ミドルウェア、アクション選択、ASP.NET Core のアクション呼び出しパイプラインを通じて処理されます。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="dcb0a-120">フィルターの種類</span><span class="sxs-lookup"><span data-stu-id="dcb0a-120">Filter types</span></span>

<span data-ttu-id="dcb0a-121">フィルターの種類はそれぞれ、フィルター パイプラインの異なるステージで実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="dcb0a-122">[承認フィルター](#authorization-filters)は、最初に実行され、ユーザーが要求に対して承認されているかどうかを判断するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="dcb0a-123">承認フィルターでは、要求が承認されていない場合、パイプラインがショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-123">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="dcb0a-124">[リソース フィルター](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="dcb0a-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="dcb0a-125">承認後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-125">Run after authorization.</span></span>  
  * <span data-ttu-id="dcb0a-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> では、残りのフィルター パイプラインの前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="dcb0a-127">たとえば、`OnResourceExecuting` では、モデル バインディングの前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-127">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="dcb0a-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> では、残りのパイプラインの完了後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="dcb0a-129">[アクション フィルター](#action-filters)は、個々のアクション メソッドが呼び出される直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-129">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="dcb0a-130">アクションに渡される引数とアクションから返される結果を操作するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-130">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="dcb0a-131">アクション フィルターは Razor Pages ではサポート**されていません**。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-131">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="dcb0a-132">[例外フィルター](#exception-filters)は、応答本文に何かが書き込まれる前に発生する未処理の例外にグローバル ポリシーを適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="dcb0a-133">[結果フィルター](#result-filters)は、個々のアクション結果の実行の直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="dcb0a-134">アクション メソッドが正常に実行された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="dcb0a-135">ビューまたはフォーマッタ実行を取り囲む必要があるロジックに便利です。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="dcb0a-136">フィルターの種類がフィルター パイプラインでどのように連携しているかを、次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-136">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求は、承認フィルター、リソース フィルター、モデル バインド、アクション フィルター、アクションの実行とアクション結果の変換、例外フィルター、結果フィルター、結果の実行を介して処理されます。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="dcb0a-139">実装</span><span class="sxs-lookup"><span data-stu-id="dcb0a-139">Implementation</span></span>

<span data-ttu-id="dcb0a-140">フィルターは、異なるインターフェイス定義を介して、同期と非同期の実装をサポートします。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="dcb0a-141">同期フィルターでは、パイプライン ステージの前 (`On-Stage-Executing`) と後 (`On-Stage-Executed`) にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-141">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="dcb0a-142">たとえば、`OnActionExecuting` はアクション メソッドの呼び出し前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-142">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="dcb0a-143">`OnActionExecuted` は、アクション メソッドが戻った後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-143">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="dcb0a-144">非同期フィルターにより `On-Stage-ExecutionAsync` メソッドが定義されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-144">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="dcb0a-145">上記のコードでは、`SampleAsyncActionFilter` にアクション メソッドを実行する <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) があります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-145">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="dcb0a-146">各 `On-Stage-ExecutionAsync` メソッドは、フィルターのパイプライン ステージを実行する `FilterType-ExecutionDelegate` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-146">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="dcb0a-147">複数のフィルター ステージ</span><span class="sxs-lookup"><span data-stu-id="dcb0a-147">Multiple filter stages</span></span>

<span data-ttu-id="dcb0a-148">複数のフィルター ステージのためのインターフェイスを 1 つのクラスで実装できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-148">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="dcb0a-149">たとえば、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> クラスは、`IActionFilter`、`IResultFilter`、およびそれらの非同期バージョンを実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-149">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="dcb0a-150">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-150">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="dcb0a-151">ランタイムは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-151">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="dcb0a-152">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-152">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="dcb0a-153">非同期インターフェイスと同期インターフェイスの両方が 1 つのクラスで実装される場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-153">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="dcb0a-154"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> などの抽象クラスを使用する場合は、同期メソッドのみをオーバーライドするか、フィルターの種類ごとに非同期メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-154">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="dcb0a-155">組み込みのフィルター属性</span><span class="sxs-lookup"><span data-stu-id="dcb0a-155">Built-in filter attributes</span></span>

<span data-ttu-id="dcb0a-156">ASP.NET Core には、サブクラスを作成したり、カスタマイズしたりできる組み込みの属性ベースのフィルターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-156">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="dcb0a-157">たとえば、次の結果フィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-157">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="dcb0a-158">上記の例のように、属性によってフィルターは引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-158">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="dcb0a-159">`AddHeaderAttribute` をコントローラーまたはアクション メソッドに適用し、HTTP ヘッダーの名前と値を指定します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-159">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="dcb0a-160">フィルター インターフェイスのいくつかには対応する属性があり、カスタムの実装に基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-160">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="dcb0a-161">フィルター属性:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-161">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="dcb0a-162">フィルターのスコープと実行の順序</span><span class="sxs-lookup"><span data-stu-id="dcb0a-162">Filter scopes and order of execution</span></span>

<span data-ttu-id="dcb0a-163">フィルターは、3 つの*スコープ*のいずれかでパイプラインに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-163">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="dcb0a-164">アクションで属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-164">Using an attribute on an action.</span></span>
* <span data-ttu-id="dcb0a-165">コントローラーで属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-165">Using an attribute on a controller.</span></span>
* <span data-ttu-id="dcb0a-166">次のコードのように、すべてのコントローラーとアクションに対してグローバルに:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-166">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="dcb0a-167">上記のコードでは、[MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) コレクションを利用し、3 つのフィルターがグローバルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-167">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="dcb0a-168">実行の既定の順序</span><span class="sxs-lookup"><span data-stu-id="dcb0a-168">Default order of execution</span></span>

<span data-ttu-id="dcb0a-169">パイプラインの特定のステージに対して複数のフィルターがある場合に、スコープがフィルターの実行の既定の順序を決定します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-169">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="dcb0a-170">グローバル フィルターがクラス フィルターを囲み、クラス フィルターがメソッド フィルターを囲みます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-170">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="dcb0a-171">フィルターの入れ子の結果として、フィルターの *after* コードが *before* コードと逆の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-171">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="dcb0a-172">フィルター シーケンス:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-172">The filter sequence:</span></span>

* <span data-ttu-id="dcb0a-173">グローバル フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-173">The *before* code of global filters.</span></span>
  * <span data-ttu-id="dcb0a-174">コントローラー フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-174">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="dcb0a-175">アクション メソッド フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-175">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="dcb0a-176">アクション メソッド フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-176">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="dcb0a-177">コントローラー フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-177">The *after* code of controller filters.</span></span>
* <span data-ttu-id="dcb0a-178">グローバル フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-178">The *after* code of global filters.</span></span>
  
<span data-ttu-id="dcb0a-179">次の例は、同期アクション フィルターに対してフィルター メソッドが呼び出される順序を示しています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-179">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="dcb0a-180">シーケンス</span><span class="sxs-lookup"><span data-stu-id="dcb0a-180">Sequence</span></span> | <span data-ttu-id="dcb0a-181">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="dcb0a-181">Filter scope</span></span> | <span data-ttu-id="dcb0a-182">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="dcb0a-182">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="dcb0a-183">1</span><span class="sxs-lookup"><span data-stu-id="dcb0a-183">1</span></span> | <span data-ttu-id="dcb0a-184">Global</span><span class="sxs-lookup"><span data-stu-id="dcb0a-184">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dcb0a-185">2</span><span class="sxs-lookup"><span data-stu-id="dcb0a-185">2</span></span> | <span data-ttu-id="dcb0a-186">コントローラー</span><span class="sxs-lookup"><span data-stu-id="dcb0a-186">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dcb0a-187">3</span><span class="sxs-lookup"><span data-stu-id="dcb0a-187">3</span></span> | <span data-ttu-id="dcb0a-188">メソッド</span><span class="sxs-lookup"><span data-stu-id="dcb0a-188">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dcb0a-189">4</span><span class="sxs-lookup"><span data-stu-id="dcb0a-189">4</span></span> | <span data-ttu-id="dcb0a-190">メソッド</span><span class="sxs-lookup"><span data-stu-id="dcb0a-190">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="dcb0a-191">5</span><span class="sxs-lookup"><span data-stu-id="dcb0a-191">5</span></span> | <span data-ttu-id="dcb0a-192">コントローラー</span><span class="sxs-lookup"><span data-stu-id="dcb0a-192">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="dcb0a-193">6</span><span class="sxs-lookup"><span data-stu-id="dcb0a-193">6</span></span> | <span data-ttu-id="dcb0a-194">Global</span><span class="sxs-lookup"><span data-stu-id="dcb0a-194">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="dcb0a-195">このシーケンスが示すもの:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-195">This sequence shows:</span></span>

* <span data-ttu-id="dcb0a-196">メソッド フィルターは、コントローラー フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-196">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="dcb0a-197">コントローラー フィルターは、グローバル フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-197">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="dcb0a-198">コントローラーと Razor Page のレベル フィルター</span><span class="sxs-lookup"><span data-stu-id="dcb0a-198">Controller and Razor Page level filters</span></span>

<span data-ttu-id="dcb0a-199"><xref:Microsoft.AspNetCore.Mvc.Controller> 基底クラスから継承するすべてのコントローラーに、[Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)、[Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-199">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="dcb0a-200">これらのメソッド:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-200">These methods:</span></span>

* <span data-ttu-id="dcb0a-201">特定のアクションに対して実行されるフィルターをラップします。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-201">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="dcb0a-202">`OnActionExecuting` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-202">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="dcb0a-203">`OnActionExecuted` は、あらゆるアクション フィルターの後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-203">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="dcb0a-204">`OnActionExecutionAsync` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-204">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="dcb0a-205">`next` の後のフィルターのコードは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-205">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="dcb0a-206">たとえば、ダウンロード サンプルで、`MySampleActionFilter` は起動中、グローバルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-206">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="dcb0a-207">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-207">The `TestController`:</span></span>

* <span data-ttu-id="dcb0a-208">`SampleActionFilterAttribute` (`[SampleActionFilter]`) が `FilterTest2` アクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-208">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="dcb0a-209">`OnActionExecuting` と `OnActionExecuted` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-209">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="dcb0a-210">`https://localhost:5001/Test/FilterTest2` に移動すると、次のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-210">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="dcb0a-211">Razor Pages の場合、「[フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-211">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="dcb0a-212">既定の順序のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="dcb0a-212">Overriding the default order</span></span>

<span data-ttu-id="dcb0a-213"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> を実装することで、実行の既定の順序をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-213">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="dcb0a-214">`IOrderedFilter` により、実行の順序を決定するために、スコープよりも優先される <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-214">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="dcb0a-215">より低い `Order` 値を持つフィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-215">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="dcb0a-216">より高い `Order` の値を持つフィルターのそれよりも前に *before* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-216">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="dcb0a-217">より高い `Order` の値を持つフィルターのそれよりも後に *after* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-217">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="dcb0a-218">`Order` プロパティはコンストラクター パラメーターで設定できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-218">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="dcb0a-219">上記の例にある同じ 3 つのアクション フィルターを検討してください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-219">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="dcb0a-220">コントローラーとグローバル フィルターの `Order` プロパティが 1 と 2 にそれぞれ設定される場合、実行順序が逆になります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-220">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="dcb0a-221">シーケンス</span><span class="sxs-lookup"><span data-stu-id="dcb0a-221">Sequence</span></span> | <span data-ttu-id="dcb0a-222">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="dcb0a-222">Filter scope</span></span> | <span data-ttu-id="dcb0a-223">`Order` プロパティ</span><span class="sxs-lookup"><span data-stu-id="dcb0a-223">`Order` property</span></span> | <span data-ttu-id="dcb0a-224">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="dcb0a-224">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="dcb0a-225">1</span><span class="sxs-lookup"><span data-stu-id="dcb0a-225">1</span></span> | <span data-ttu-id="dcb0a-226">メソッド</span><span class="sxs-lookup"><span data-stu-id="dcb0a-226">Method</span></span> | <span data-ttu-id="dcb0a-227">0</span><span class="sxs-lookup"><span data-stu-id="dcb0a-227">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dcb0a-228">2</span><span class="sxs-lookup"><span data-stu-id="dcb0a-228">2</span></span> | <span data-ttu-id="dcb0a-229">コントローラー</span><span class="sxs-lookup"><span data-stu-id="dcb0a-229">Controller</span></span> | <span data-ttu-id="dcb0a-230">1</span><span class="sxs-lookup"><span data-stu-id="dcb0a-230">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="dcb0a-231">3</span><span class="sxs-lookup"><span data-stu-id="dcb0a-231">3</span></span> | <span data-ttu-id="dcb0a-232">Global</span><span class="sxs-lookup"><span data-stu-id="dcb0a-232">Global</span></span> | <span data-ttu-id="dcb0a-233">2</span><span class="sxs-lookup"><span data-stu-id="dcb0a-233">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="dcb0a-234">4</span><span class="sxs-lookup"><span data-stu-id="dcb0a-234">4</span></span> | <span data-ttu-id="dcb0a-235">Global</span><span class="sxs-lookup"><span data-stu-id="dcb0a-235">Global</span></span> | <span data-ttu-id="dcb0a-236">2</span><span class="sxs-lookup"><span data-stu-id="dcb0a-236">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="dcb0a-237">5</span><span class="sxs-lookup"><span data-stu-id="dcb0a-237">5</span></span> | <span data-ttu-id="dcb0a-238">コントローラー</span><span class="sxs-lookup"><span data-stu-id="dcb0a-238">Controller</span></span> | <span data-ttu-id="dcb0a-239">1</span><span class="sxs-lookup"><span data-stu-id="dcb0a-239">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="dcb0a-240">6</span><span class="sxs-lookup"><span data-stu-id="dcb0a-240">6</span></span> | <span data-ttu-id="dcb0a-241">メソッド</span><span class="sxs-lookup"><span data-stu-id="dcb0a-241">Method</span></span> | <span data-ttu-id="dcb0a-242">0</span><span class="sxs-lookup"><span data-stu-id="dcb0a-242">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="dcb0a-243">フィルターの実行順序を決定するときに、`Order` プロパティによりスコープがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="dcb0a-244">最初に順序でフィルターが並べ替えられ、次に同じ順位の優先度を決めるためにスコープが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="dcb0a-245">組み込みのフィルターはすべて `IOrderedFilter` を実装し、既定の `Order` 値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="dcb0a-246">組み込みのフィルターの場合、`Order` をゼロ以外の値に設定しない限り、スコープによって順序が決定されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-246">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="dcb0a-247">キャンセルとショートサーキット</span><span class="sxs-lookup"><span data-stu-id="dcb0a-247">Cancellation and short-circuiting</span></span>

<span data-ttu-id="dcb0a-248">フィルター メソッドに提供される <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> パラメーターで <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> プロパティを設定することで、フィルター パイプラインをショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-248">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="dcb0a-249">たとえば、次のリソース フィルターは、パイプラインの残りの部分が実行されるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-249">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="dcb0a-250">次のコードでは、`ShortCircuitingResourceFilter` と `AddHeader` の両方のフィルターが `SomeResource` アクション メソッドをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-250">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="dcb0a-251">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-251">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="dcb0a-252">これはリソース フィルターであり、`AddHeader` はアクション フィルターであるため、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-252">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="dcb0a-253">パイプラインの残りの部分は迂回されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-253">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="dcb0a-254">そのため、`SomeResource` アクションの場合、`AddHeader` フィルターが実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-254">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="dcb0a-255">`ShortCircuitingResourceFilter` が最初に実行された場合は、両方のフィルターがアクション メソッド レベルで適用されると、この動作が同じになります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-255">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="dcb0a-256">そのフィルターの種類が原因で、あるいは `Order` プロパティの明示的な使用により、`ShortCircuitingResourceFilter` が最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-256">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="dcb0a-257">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="dcb0a-257">Dependency injection</span></span>

<span data-ttu-id="dcb0a-258">フィルターは種類ごとまたはインスタンスごとに追加できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-258">Filters can be added by type or by instance.</span></span> <span data-ttu-id="dcb0a-259">インスタンスが追加される場合、そのインスタンスはすべての要求に対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-259">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="dcb0a-260">種類が追加される場合、それは種類でアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-260">If a type is added, it's type-activated.</span></span> <span data-ttu-id="dcb0a-261">種類でアクティブ化されるフィルターの意味:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-261">A type-activated filter means:</span></span>

* <span data-ttu-id="dcb0a-262">インスタンスは要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-262">An instance is created for each request.</span></span>
* <span data-ttu-id="dcb0a-263">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-263">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="dcb0a-264">属性として実装され、コントローラー クラスまたはアクション メソッドに直接追加されるフィルターは、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって提供されるコンストラクターの依存関係を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-264">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="dcb0a-265">コンストラクターの依存関係は、次の理由から DI によって与えられません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-265">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="dcb0a-266">属性には、適用される場所で提供される独自のコンストラクター パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-266">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="dcb0a-267">これは、属性のしくみの制限です。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-267">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="dcb0a-268">次のフィルターでは、DI から提供されるコンストラクターの依存関係がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-268">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="dcb0a-269">属性に実装された <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="dcb0a-270">上記のフィルターは、コントローラーまたはアクション メソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-270">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="dcb0a-271">ロガーは DI から利用できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-271">Loggers are available from DI.</span></span> <span data-ttu-id="dcb0a-272">ただし、ログ目的でのみフィルターを作成し、使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-272">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="dcb0a-273">[組み込みフレームワークのログ機能](xref:fundamentals/logging/index)で通常、ログ記録に必要なものが与えられます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-273">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="dcb0a-274">フィルターに追加されるログ記録:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-274">Logging added to filters:</span></span>

* <span data-ttu-id="dcb0a-275">ビジネス ドメインの懸念事項やフィルターに固有の動作に焦点を合わせます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-275">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="dcb0a-276">アクションやその他のフレームワーク イベントはログに記録**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-276">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="dcb0a-277">組み込みフィルターでは、アクションとフレームワーク イベントが記録されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-277">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="dcb0a-278">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="dcb0a-278">ServiceFilterAttribute</span></span>

<span data-ttu-id="dcb0a-279">サービス フィルターの実装の種類は `ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-279">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="dcb0a-280"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は DI からフィルターのインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="dcb0a-281">このコードでは、`AddHeaderResultServiceFilter` が示されています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-281">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="dcb0a-282">次のコードでは、`AddHeaderResultServiceFilter` が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-282">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="dcb0a-283">次のコードでは、`ServiceFilter` 属性により、DI から `AddHeaderResultServiceFilter` フィルターのインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-283">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="dcb0a-284">`ServiceFilterAttribute` を使用するときに、[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-284">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="dcb0a-285">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-285">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="dcb0a-286">ASP.NET Core ランタイムで保証されないこと:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-286">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="dcb0a-287">フィルターのインスタンスが 1 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-287">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="dcb0a-288">フィルターが後の時点で、DI コンテナーから再要求されることはありません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-288">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="dcb0a-289">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-289">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="dcb0a-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="dcb0a-291">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-291">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="dcb0a-292">`CreateInstance` により、DI から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-292">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="dcb0a-293">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="dcb0a-293">TypeFilterAttribute</span></span>

<span data-ttu-id="dcb0a-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は<xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> と似ていますが、その型は DI コンテナーから直接解決されません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="dcb0a-295"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> を使って型をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-295">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="dcb0a-296">`TypeFilterAttribute` 型は DI コンテナーから直接解決されないためです。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-296">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="dcb0a-297">`TypeFilterAttribute` を利用して参照される型は、DI コンテナーに登録する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-297">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="dcb0a-298">DI コンテナーによって依存関係が満たされています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-298">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="dcb0a-299">`TypeFilterAttribute` は必要に応じて、型のコンストラクター引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-299">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="dcb0a-300">`TypeFilterAttribute` を使用するときに、[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-300">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="dcb0a-301">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-301">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="dcb0a-302">ASP.NET Core ランタイムでは、フィルターの 1 インスタンスが作成されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-302">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="dcb0a-303">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-303">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="dcb0a-304">次の例は、`TypeFilterAttribute` を使用して、型に引数を渡す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-304">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="dcb0a-305">承認フィルター</span><span class="sxs-lookup"><span data-stu-id="dcb0a-305">Authorization filters</span></span>

<span data-ttu-id="dcb0a-306">承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-306">Authorization filters:</span></span>

* <span data-ttu-id="dcb0a-307">フィルター パイプライン内で実行される最初のフィルターです。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-307">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="dcb0a-308">アクション メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-308">Control access to action methods.</span></span>
* <span data-ttu-id="dcb0a-309">before メソッドが与えられ、after メソッドは与えられません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-309">Have a before method, but no after method.</span></span>

<span data-ttu-id="dcb0a-310">カスタム承認フィルターには、カスタム承認フレームワークが必要です。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-310">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="dcb0a-311">カスタム フィルターを記述するよりも、独自の承認ポリシーを構成するか、カスタム承認ポリシーを記述することを選びます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-311">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="dcb0a-312">組み込み承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-312">The built-in authorization filter:</span></span>

* <span data-ttu-id="dcb0a-313">承認システムを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-313">Calls the authorization system.</span></span>
* <span data-ttu-id="dcb0a-314">要求は承認されません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-314">Does not authorize requests.</span></span>

<span data-ttu-id="dcb0a-315">承認フィルター内で例外をスロー**しません**。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-315">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="dcb0a-316">例外は処理されません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-316">The exception will not be handled.</span></span>
* <span data-ttu-id="dcb0a-317">例外フィルターで例外が処理されません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-317">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="dcb0a-318">承認フィルターで例外が発生した場合、チャレンジ発行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-318">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="dcb0a-319">承認の詳細については、[こちら](xref:security/authorization/introduction)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-319">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="dcb0a-320">リソース フィルター</span><span class="sxs-lookup"><span data-stu-id="dcb0a-320">Resource filters</span></span>

<span data-ttu-id="dcb0a-321">リソース フィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-321">Resource filters:</span></span>

* <span data-ttu-id="dcb0a-322"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-322">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="dcb0a-323">実行により、ほとんどのフィルター パイプラインがラップされます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-323">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="dcb0a-324">[承認フィルター](#authorization-filters)のみ、リソース フィルターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-324">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="dcb0a-325">リソース フィルターは、ほとんどのパイプラインをショートサーキットする目的で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-325">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="dcb0a-326">たとえば、キャッシュ フィルターにより、キャッシュ ヒットがあるとき、残りのパイプラインを回避できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-326">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="dcb0a-327">リソース フィルターの例:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-327">Resource filter examples:</span></span>

* <span data-ttu-id="dcb0a-328">前に示した[ショートサーキットするリソース フィルター](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-328">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="dcb0a-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="dcb0a-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="dcb0a-330">モデル バインドがフォーム データにアクセスすることを禁止します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-330">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="dcb0a-331">メモリにフォーム データが読み込まれないようにする目的で大きなファイルのアップロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-331">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="dcb0a-332">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="dcb0a-332">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dcb0a-333">アクション フィルターは Razor Pages に適用**されません**。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-333">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="dcb0a-334">Razor Pages では <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-334">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="dcb0a-335">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-335">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="dcb0a-336">アクション フィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-336">Action filters:</span></span>

* <span data-ttu-id="dcb0a-337"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-337">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="dcb0a-338">この実行はアクション メソッドの実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-338">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="dcb0a-339">次のコードは、サンプル アクション フィルターを示しています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-339">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="dcb0a-340"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> では次のプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-340">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="dcb0a-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - アクション メソッドへの入力を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="dcb0a-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - コントローラー インスタンスを操作できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="dcb0a-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` を設定すると、アクション メソッドと後続のアクション フィルターの実行がショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="dcb0a-344">アクション メソッドで例外をスローする:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-344">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="dcb0a-345">後続のフィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-345">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="dcb0a-346">`Result` の設定とは異なり、結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-346">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="dcb0a-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> は、`Controller` と `Result` に加え、次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="dcb0a-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 別のフィルターによってアクションの実行がショートサーキットされた場合は、true になります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="dcb0a-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - アクションまたは前に実行されたアクション フィルターが例外をスローした場合は、null 以外になります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="dcb0a-350">このプロパティを null に設定する:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-350">Setting this property to null:</span></span>

  * <span data-ttu-id="dcb0a-351">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-351">Effectively handles the exception.</span></span>
  * <span data-ttu-id="dcb0a-352">`Result` は、アクション メソッドから返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-352">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="dcb0a-353">`IAsyncActionFilter` の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> の呼び出しによって:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-353">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="dcb0a-354">後続のすべてのアクション フィルターとアクション メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-354">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="dcb0a-355">`ActionExecutedContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-355">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="dcb0a-356">ショートサーキットするには、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> を結果インスタンスに割り当てます。`next` (`ActionExecutionDelegate`) は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-356">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="dcb0a-357">このフレームワークからは、サブクラス化できる抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-357">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="dcb0a-358">`OnActionExecuting` アクション フィルターを次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-358">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="dcb0a-359">モデルの状態を検証します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-359">Validate model state.</span></span>
* <span data-ttu-id="dcb0a-360">状態が有効でない場合は、エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-360">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="dcb0a-361">`OnActionExecuted` メソッドは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-361">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="dcb0a-362">また、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> プロパティを介してアクションの結果を表示したり、操作したりできます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-362">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="dcb0a-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> は、アクションの実行が別のフィルターによってショートサーキットされた場合、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="dcb0a-364">アクションまたは後続のアクション フィルターが例外をスローした場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="dcb0a-365">`Exception` を null に設定すると:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-365">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="dcb0a-366">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-366">Effectively handles an exception.</span></span>
  * <span data-ttu-id="dcb0a-367">`ActionExecutedContext.Result` は、アクション メソッドから通常どおり返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-367">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="dcb0a-368">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="dcb0a-368">Exception filters</span></span>

<span data-ttu-id="dcb0a-369">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-369">Exception filters:</span></span>

* <span data-ttu-id="dcb0a-370"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> を実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-370">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="dcb0a-371">共通のエラー処理ポリシーを実装する目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-371">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="dcb0a-372">次の例外フィルターのサンプルでは、カスタムのエラー ビューを使用して、アプリの開発中に発生する例外に関する詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-372">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="dcb0a-373">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-373">Exception filters:</span></span>

* <span data-ttu-id="dcb0a-374">before イベントと after イベントが与えられません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-374">Don't have before and after events.</span></span>
* <span data-ttu-id="dcb0a-375"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> を実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-375">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="dcb0a-376">Razor Page またはコントローラーの作成、[モデル バインド](xref:mvc/models/model-binding)、アクション フィルター、またはアクション メソッドで発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-376">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="dcb0a-377">リソース フィルター、結果フィルター、または MVC 結果の実行で発生した例外はキャッチ**しません**。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-377">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="dcb0a-378">例外を処理するには、<xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> プロパティを `true` に設定するか、応答を記述します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-378">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="dcb0a-379">これにより、例外の伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-379">This stops propagation of the exception.</span></span> <span data-ttu-id="dcb0a-380">例外フィルターで例外を "成功" に変えることはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-380">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="dcb0a-381">それができるのは、アクション フィルターだけです。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-381">Only an action filter can do that.</span></span>

<span data-ttu-id="dcb0a-382">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-382">Exception filters:</span></span>

* <span data-ttu-id="dcb0a-383">アクション内で発生する例外のトラップに適しています。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-383">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="dcb0a-384">エラー処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-384">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="dcb0a-385">例外処理にはミドルウェアを選択してください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-385">Prefer middleware for exception handling.</span></span> <span data-ttu-id="dcb0a-386">呼び出されたアクション メソッドに基づいてエラー処理が*異なる*状況でのみ例外フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-386">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="dcb0a-387">たとえば、アプリには、API エンドポイントとビュー/HTML の両方に対するアクション メソッドがある場合があります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-387">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="dcb0a-388">API エンドポイントは、JSON としてのエラー情報を返す可能性がある一方で、ビュー ベースのアクションがエラー ページを HTML として返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-388">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="dcb0a-389">結果フィルター</span><span class="sxs-lookup"><span data-stu-id="dcb0a-389">Result filters</span></span>

<span data-ttu-id="dcb0a-390">結果フィルター:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-390">Result filters:</span></span>

* <span data-ttu-id="dcb0a-391">インターフェイスを実装します:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-391">Implement an interface:</span></span>
  * <span data-ttu-id="dcb0a-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="dcb0a-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="dcb0a-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="dcb0a-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="dcb0a-394">この実行はアクション結果の実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-394">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="dcb0a-395">IResultFilter および IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="dcb0a-395">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="dcb0a-396">次は、HTTP ヘッダーを追加する結果フィルターのコードです。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-396">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="dcb0a-397">実行されている結果の種類は、アクションに依存します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-397">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="dcb0a-398">ビューを返すアクションには、実行されている <xref:Microsoft.AspNetCore.Mvc.ViewResult> の一部として、すべての razor 処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-398">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="dcb0a-399">API メソッドは、結果の実行の一部としていくつかのシリアル化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-399">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="dcb0a-400">[アクション結果](xref:mvc/controllers/actions)に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-400">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="dcb0a-401">結果フィルターは、アクションまたはアクション フィルターによってアクション結果を生成される場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-401">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="dcb0a-402">結果フィルターは、次の場合には実行されません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-402">Result filters are not executed when:</span></span>

* <span data-ttu-id="dcb0a-403">承認フィルターまたはリソース フィルターによって、パイプラインがショートサーキットされる。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-403">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="dcb0a-404">アクションの結果を生成することで、例外フィルターによって例外が処理されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-404">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="dcb0a-405"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> メソッドは、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> を `true` に設定することで、アクションの結果と後続の結果フィルターの実行をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-405">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="dcb0a-406">ショートサーキットする場合は、空の応答が生成されないように応答オブジェクトに記述します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-406">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="dcb0a-407">`IResultFilter.OnResultExecuting` で例外をスローすることで:</span><span class="sxs-lookup"><span data-stu-id="dcb0a-407">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="dcb0a-408">アクション結果と後続フィルターの実行が回避されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-408">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="dcb0a-409">結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-409">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="dcb0a-410"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> メソッドが実行されたとき、応答が既にクライアントに送信されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-410">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="dcb0a-411">応答が既にクライアントに送信されていた場合は、それ以上変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-411">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="dcb0a-412">別のフィルターによってアクションの結果の実行がショートサーキットされた場合、`ResultExecutedContext.Canceled` は `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-412">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="dcb0a-413">アクションの結果または後続の結果フィルターが例外をスローした場合、`ResultExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-413">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="dcb0a-414">`Exception` を null に設定すると、例外を効果的に処理でき、パイプラインの後方で ASP.NET Core によって例外が再スローされることを防げます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-414">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="dcb0a-415">結果フィルターの例外を処理するとき、応答にデータを書き込む目的で信頼できる方法はありません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-415">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="dcb0a-416">アクションの結果により例外がスローされるとき、ヘッダーがクライアントにフラッシュされている場合、エラー コードを送信する目的で信頼できるメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-416">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="dcb0a-417"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> で `await next` を呼び出すと、後続のすべての結果フィルターとアクションの結果が実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-417">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="dcb0a-418">ショートサーキットするには、[ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) を `true` に設定し、`ResultExecutionDelegate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-418">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="dcb0a-419">このフレームワークからは、サブクラス化できる抽象 `ResultFilterAttribute` が与えられます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-419">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="dcb0a-420">前に示した [AddHeaderAttribute](#add-header-attribute) クラスは、結果フィルター属性の一例です。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-420">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="dcb0a-421">IAlwaysRunResultFilter および IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="dcb0a-421">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="dcb0a-422"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> および <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> インターフェイスでは、すべてのアクションの結果に対して実行される <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> の実装が宣言されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-422">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="dcb0a-423">これには、以下によって生成されるアクションの結果が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-423">This includes action results produced by:</span></span>

* <span data-ttu-id="dcb0a-424">ショートサーキットが行われる承認フィルターとリソース フィルター。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-424">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="dcb0a-425">例外フィルター。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-425">Exception filters.</span></span>

<span data-ttu-id="dcb0a-426">たとえば、次のフィルターは常に実行され、コンテンツ ネゴシエーションが失敗した場合に "*422 処理不可エンティティ*" 状態コードを使ってアクションの結果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) を設定します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-426">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="dcb0a-427">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="dcb0a-427">IFilterFactory</span></span>

<span data-ttu-id="dcb0a-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> を実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="dcb0a-429">そのため、`IFilterFactory` インスタンスはフィルター パイプライン内の任意の場所で `IFilterMetadata` インスタンスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-429">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="dcb0a-430">ランタイムでは、フィルターを呼び出す準備をする際、`IFilterFactory` へのキャストが試行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-430">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="dcb0a-431">そのキャストが成功した場合、呼び出される `IFilterMetadata` インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-431">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="dcb0a-432">これにより、アプリの起動時に正確なフィルター パイプラインを明示的に設定する必要がないため、柔軟なデザインが可能になります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-432">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="dcb0a-433">フィルターを作成するための別の方法として、カスタムの属性の実装で `IFilterFactory` を実装できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-433">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="dcb0a-434">[ダウンロード サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)を実行することで、前のコードをテストできます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-434">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="dcb0a-435">F12 開発者ツールを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-435">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="dcb0a-436">`https://localhost:5001/Sample/HeaderWithFactory` に移動します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-436">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="dcb0a-437">F12 開発者ツールでは、サンプル コードによって追加された次の応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-437">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="dcb0a-438">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="dcb0a-438">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="dcb0a-439">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="dcb0a-439">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="dcb0a-440">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="dcb0a-440">**internal:** `My header`</span></span>

<span data-ttu-id="dcb0a-441">上記のコードでは、**internal:** `My header` という応答ヘッダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-441">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="dcb0a-442">属性に実装された IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="dcb0a-442">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="dcb0a-443">`IFilterFactory` を実装するフィルターは次のようなフィルターに便利です。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-443">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="dcb0a-444">パラメーターの引き渡しを必要としません。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-444">Don't require passing parameters.</span></span>
* <span data-ttu-id="dcb0a-445">DI で満たす必要があるコンストラクター依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-445">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="dcb0a-446"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-446"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="dcb0a-447">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-447">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="dcb0a-448">`CreateInstance` により、サービス コンテナー (DI) から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-448">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="dcb0a-449">次のコードでは、`[SampleActionFilter]` を適用する 3 つの手法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-449">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="dcb0a-450">上記のコードでは、`SampleActionFilter` を適用する方法としては、`[SampleActionFilter]` でメソッドを装飾する方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-450">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="dcb0a-451">フィルター パイプラインでのミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="dcb0a-451">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="dcb0a-452">リソース フィルターは、パイプラインの後方で登場するすべての実行を囲む点で、[ミドルウェア](xref:fundamentals/middleware/index)のように機能します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-452">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="dcb0a-453">ただし、フィルターは ASP.NET Core ランタイムの一部である点がミドルウェアとは異なります。つまり、フィルターには ASP.NET Core コンテキストとコンストラクトへのアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-453">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="dcb0a-454">ミドルウェアをフィルターとして使用するには、フィルター パイプラインに挿入するミドルウェアを指定する `Configure` メソッドを使用して型を作成します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-454">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="dcb0a-455">ローカリゼーション ミドルウェアを使用して要求の現在のカルチャを確立する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-455">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="dcb0a-456"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> を使用し、ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-456">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="dcb0a-457">ミドルウェア フィルターは、フィルター パイプラインのリソース フィルターと同じステージ (モデル バインドの前、残りのパイプラインの後) で実行されます。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-457">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="dcb0a-458">次の操作</span><span class="sxs-lookup"><span data-stu-id="dcb0a-458">Next actions</span></span>

* <span data-ttu-id="dcb0a-459">[Razor Pages のフィルター メソッド](xref:razor-pages/filter)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-459">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="dcb0a-460">フィルターを試すには、[GitHub のサンプルをダウンロードして、テストおよび変更を行います](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="dcb0a-460">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
