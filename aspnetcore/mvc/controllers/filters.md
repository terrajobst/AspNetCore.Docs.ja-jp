---
title: ASP.NET Core フィルター
author: Rick-Anderson
description: フィルターのしくみと ASP.NET Core でそれを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 1/1/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 2300b14a6a89191d3d8c673311880fc144183da9
ms.sourcegitcommit: e7d4fe6727d423f905faaeaa312f6c25ef844047
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2020
ms.locfileid: "75608127"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="0b916-103">ASP.NET Core フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0b916-104">作成者: [Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0b916-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0b916-105">ASP.NET Core で*フィルター*を使用すると、要求処理パイプラインの特定のステージの前または後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="0b916-106">組み込みのフィルターでは次のようなタスクが処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="0b916-107">許可 (ユーザーに許可が与えられていないリソースの場合、アクセスを禁止する)。</span><span class="sxs-lookup"><span data-stu-id="0b916-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="0b916-108">応答キャッシュ (要求パイプラインを迂回し、キャッシュされている応答を返す)。</span><span class="sxs-lookup"><span data-stu-id="0b916-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="0b916-109">横断的な問題を処理するカスタム フィルターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="0b916-110">横断的な問題の例には、エラー処理、キャッシュ、構成、認証、ログなどがあります。</span><span class="sxs-lookup"><span data-stu-id="0b916-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="0b916-111">フィルターにより、コードの重複が回避されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="0b916-112">たとえば、エラー処理例外フィルターではエラー処理を統合できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="0b916-113">このドキュメントは、Razor Pages、API コントローラー、ビューのあるコントローラーに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="0b916-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="0b916-114">[サンプルを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0b916-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="0b916-115">フィルターのしくみ</span><span class="sxs-lookup"><span data-stu-id="0b916-115">How filters work</span></span>

<span data-ttu-id="0b916-116">*ASP.NET Core のアクション呼び出しパイプライン*内で実行されるフィルターは、*フィルター パイプライン*と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="0b916-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="0b916-117">フィルター パイプラインは、ASP.NET Core が実行するアクションを選択した後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求は、他のミドルウェア、ルーティング ミドルウェア、アクション選択、およびアクション呼び出しパイプラインを通じて処理されます。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="0b916-120">フィルターの種類</span><span class="sxs-lookup"><span data-stu-id="0b916-120">Filter types</span></span>

<span data-ttu-id="0b916-121">フィルターの種類はそれぞれ、フィルター パイプラインの異なるステージで実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="0b916-122">[承認フィルター](#authorization-filters)は、最初に実行され、ユーザーが要求に対して承認されているかどうかを判断するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="0b916-123">承認フィルターでは、要求が承認されていない場合、パイプラインがショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-123">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="0b916-124">[リソース フィルター](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="0b916-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="0b916-125">承認後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-125">Run after authorization.</span></span>  
  * <span data-ttu-id="0b916-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> では、残りのフィルター パイプラインの前にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="0b916-127">たとえば、`OnResourceExecuting` では、モデル バインディングの前にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-127">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="0b916-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> では、残りのパイプラインの完了後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="0b916-129">[アクション フィルター](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="0b916-129">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="0b916-130">アクション メソッドが呼び出される直前と直後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="0b916-130">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="0b916-131">アクションに渡される引数を変更できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-131">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="0b916-132">アクションから返された結果を変更できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-132">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="0b916-133">Razor Pages ではサポート**されていません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-133">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="0b916-134">[例外フィルター](#exception-filters)では、応答本文への書き込みが行われる前に発生する未処理の例外にグローバル ポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-134">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="0b916-135">[結果フィルター](#result-filters)では、アクション結果の実行の直前と直後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-135">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="0b916-136">アクション メソッドが正常に実行された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-136">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="0b916-137">ビューまたはフォーマッタ実行を取り囲む必要があるロジックに便利です。</span><span class="sxs-lookup"><span data-stu-id="0b916-137">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="0b916-138">フィルターの種類がフィルター パイプラインでどのように連携しているかを、次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="0b916-138">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求は、承認フィルター、リソース フィルター、モデル バインド、アクション フィルター、アクションの実行とアクション結果の変換、例外フィルター、結果フィルター、結果の実行を介して処理されます。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="0b916-141">実装</span><span class="sxs-lookup"><span data-stu-id="0b916-141">Implementation</span></span>

<span data-ttu-id="0b916-142">フィルターは、異なるインターフェイス定義を介して、同期と非同期の実装をサポートします。</span><span class="sxs-lookup"><span data-stu-id="0b916-142">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="0b916-143">同期フィルターでは、そのパイプライン ステージの前と後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-143">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="0b916-144">たとえば、<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> はアクション メソッドの呼び出し前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-144">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="0b916-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> は、アクション メソッドが戻った後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="0b916-146">非同期フィルターでは、`On-Stage-ExecutionAsync` メソッドが定義されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-146">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="0b916-147"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0b916-147">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="0b916-148">上記のコードでは、`SampleAsyncActionFilter` にアクション メソッドを実行する <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-148">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="0b916-149">複数のフィルター ステージ</span><span class="sxs-lookup"><span data-stu-id="0b916-149">Multiple filter stages</span></span>

<span data-ttu-id="0b916-150">複数のフィルター ステージのためのインターフェイスを 1 つのクラスで実装できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-150">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="0b916-151">たとえば、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> クラスでは次のものが実装されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-151">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="0b916-152">同期: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b916-152">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="0b916-153">非同期: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b916-153">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="0b916-154">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-154">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="0b916-155">ランタイムは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b916-155">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="0b916-156">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b916-156">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="0b916-157">非同期インターフェイスと同期インターフェイスの両方が 1 つのクラスで実装される場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-157">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="0b916-158"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> などの抽象クラスを使用する場合は、同期メソッドのみをオーバーライドするか、フィルターの種類ごとに非同期メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="0b916-158">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="0b916-159">組み込みのフィルター属性</span><span class="sxs-lookup"><span data-stu-id="0b916-159">Built-in filter attributes</span></span>

<span data-ttu-id="0b916-160">ASP.NET Core には、サブクラスを作成したり、カスタマイズしたりできる組み込みの属性ベースのフィルターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-160">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="0b916-161">たとえば、次の結果フィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="0b916-161">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="0b916-162">上記の例のように、属性によってフィルターは引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-162">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="0b916-163">`AddHeaderAttribute` をコントローラーまたはアクション メソッドに適用し、HTTP ヘッダーの名前と値を指定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-163">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="0b916-164">[ブラウザー開発者ツール](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) などのツールを使用して、ヘッダーを調べます。</span><span class="sxs-lookup"><span data-stu-id="0b916-164">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="0b916-165">**[応答ヘッダー]** の下に `author: Rick Anderson` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-165">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="0b916-166">次のコードでは、次のことを行う `ActionFilterAttribute` が実装されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-166">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="0b916-167">構成システムからタイトルと名前を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="0b916-167">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="0b916-168">前のサンプルとは異なり、次のコードでは、フィルター パラメーターをコードに追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-168">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="0b916-169">応答ヘッダーにタイトルと名前を追加します。</span><span class="sxs-lookup"><span data-stu-id="0b916-169">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="0b916-170">構成オプションは、[構成システム](xref:fundamentals/configuration/index)から[オプション パターン](xref:fundamentals/configuration/options)を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-170">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0b916-171">たとえば、*appsettings.json* ファイルから、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="0b916-171">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="0b916-172">`StartUp.ConfigureServices` では、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="0b916-172">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="0b916-173">`"Position"` 構成領域を使用して `PositionOptions` クラスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="0b916-173">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="0b916-174">`MyActionFilterAttribute` をサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="0b916-174">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="0b916-175">次のコードは `PositionOptions` クラスを示しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-175">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="0b916-176">次のコードでは、`Index2` メソッドに `MyActionFilterAttribute` が適用されています。</span><span class="sxs-lookup"><span data-stu-id="0b916-176">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="0b916-177">`Sample/Index2` エンドポイントが呼び出されると、 **[応答ヘッダー]** の下に `author: Rick Anderson` および `Editor: Joe Smith` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-177">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="0b916-178">次のコードでは、`MyActionFilterAttribute` と `AddHeaderAttribute` が Razor ページに適用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-178">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0b916-179">Razor ページ ハンドラー メソッドにフィルターを適用することはできません。</span><span class="sxs-lookup"><span data-stu-id="0b916-179">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="0b916-180">これらは、Razor ページ モデルに適用するか、グローバルに適用することが可能です。</span><span class="sxs-lookup"><span data-stu-id="0b916-180">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="0b916-181">フィルター インターフェイスのいくつかには対応する属性があり、カスタムの実装に基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-181">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="0b916-182">フィルター属性:</span><span class="sxs-lookup"><span data-stu-id="0b916-182">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="0b916-183">フィルターのスコープと実行の順序</span><span class="sxs-lookup"><span data-stu-id="0b916-183">Filter scopes and order of execution</span></span>

<span data-ttu-id="0b916-184">フィルターは、3 つの*スコープ*のいずれかでパイプラインに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-184">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="0b916-185">コントローラー アクションでの属性の使用。</span><span class="sxs-lookup"><span data-stu-id="0b916-185">Using an attribute on a controller action.</span></span> <span data-ttu-id="0b916-186">フィルター属性を Razor Pages ハンドラー メソッドに適用することはできません。</span><span class="sxs-lookup"><span data-stu-id="0b916-186">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="0b916-187">コントローラーまたは Razor ページでの属性の使用。</span><span class="sxs-lookup"><span data-stu-id="0b916-187">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="0b916-188">次のコードのように、すべてのコントローラー、アクション、および Razor Pages に対してグローバルに:</span><span class="sxs-lookup"><span data-stu-id="0b916-188">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="0b916-189">実行の既定の順序</span><span class="sxs-lookup"><span data-stu-id="0b916-189">Default order of execution</span></span>

<span data-ttu-id="0b916-190">パイプラインの特定のステージに対して複数のフィルターがある場合に、スコープがフィルターの実行の既定の順序を決定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-190">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="0b916-191">グローバル フィルターがクラス フィルターを囲み、クラス フィルターがメソッド フィルターを囲みます。</span><span class="sxs-lookup"><span data-stu-id="0b916-191">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="0b916-192">フィルターの入れ子の結果として、フィルターの *after* コードが *before* コードと逆の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-192">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="0b916-193">フィルター シーケンス:</span><span class="sxs-lookup"><span data-stu-id="0b916-193">The filter sequence:</span></span>

* <span data-ttu-id="0b916-194">グローバル フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-194">The *before* code of global filters.</span></span>
  * <span data-ttu-id="0b916-195">コントローラーおよび Razor ページ フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-195">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="0b916-196">アクション メソッド フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-196">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="0b916-197">アクション メソッド フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-197">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="0b916-198">コントローラーおよび Razor ページ フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-198">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="0b916-199">グローバル フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-199">The *after* code of global filters.</span></span>
  
<span data-ttu-id="0b916-200">次の例は、同期アクション フィルターに対してフィルター メソッドが呼び出される順序を示しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-200">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="0b916-201">シーケンス</span><span class="sxs-lookup"><span data-stu-id="0b916-201">Sequence</span></span> | <span data-ttu-id="0b916-202">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="0b916-202">Filter scope</span></span> | <span data-ttu-id="0b916-203">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-203">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="0b916-204">1</span><span class="sxs-lookup"><span data-stu-id="0b916-204">1</span></span> | <span data-ttu-id="0b916-205">Global</span><span class="sxs-lookup"><span data-stu-id="0b916-205">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b916-206">2</span><span class="sxs-lookup"><span data-stu-id="0b916-206">2</span></span> | <span data-ttu-id="0b916-207">コントローラーまたは Razor ページ</span><span class="sxs-lookup"><span data-stu-id="0b916-207">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="0b916-208">3</span><span class="sxs-lookup"><span data-stu-id="0b916-208">3</span></span> | <span data-ttu-id="0b916-209">メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-209">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b916-210">4</span><span class="sxs-lookup"><span data-stu-id="0b916-210">4</span></span> | <span data-ttu-id="0b916-211">メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-211">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="0b916-212">5</span><span class="sxs-lookup"><span data-stu-id="0b916-212">5</span></span> | <span data-ttu-id="0b916-213">コントローラーまたは Razor ページ</span><span class="sxs-lookup"><span data-stu-id="0b916-213">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="0b916-214">6</span><span class="sxs-lookup"><span data-stu-id="0b916-214">6</span></span> | <span data-ttu-id="0b916-215">Global</span><span class="sxs-lookup"><span data-stu-id="0b916-215">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="0b916-216">コントローラー レベルのフィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-216">Controller level filters</span></span>

<span data-ttu-id="0b916-217"><xref:Microsoft.AspNetCore.Mvc.Controller> 基底クラスから継承するすべてのコントローラーに、[Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)、[Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0b916-217">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="0b916-218">これらのメソッド:</span><span class="sxs-lookup"><span data-stu-id="0b916-218">These methods:</span></span>

* <span data-ttu-id="0b916-219">特定のアクションに対して実行されるフィルターをラップします。</span><span class="sxs-lookup"><span data-stu-id="0b916-219">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="0b916-220">`OnActionExecuting` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-220">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="0b916-221">`OnActionExecuted` は、あらゆるアクション フィルターの後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-221">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="0b916-222">`OnActionExecutionAsync` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-222">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="0b916-223">`next` の後のフィルターのコードは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-223">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="0b916-224">たとえば、ダウンロード サンプルで、`MySampleActionFilter` は起動中、グローバルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-224">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="0b916-225">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="0b916-225">The `TestController`:</span></span>

* <span data-ttu-id="0b916-226">`SampleActionFilterAttribute` (`[SampleActionFilter]`) が `FilterTest2` アクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-226">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="0b916-227">`OnActionExecuting` と `OnActionExecuted` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-227">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="0b916-228">`https://localhost:5001/Test2/FilterTest2` に移動すると、次のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-228">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="0b916-229">コントローラー レベルのフィルターでは、[Order](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) プロパティが `int.MinValue` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-229">Controller level filters set the [Order](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="0b916-230">コントローラー レベルのフィルターは、メソッドにフィルターが適用された後に実行されるように設定することは**できません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-230">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="0b916-231">順序については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="0b916-231">Order is explained in the next section.</span></span>

<span data-ttu-id="0b916-232">Razor Pages の場合、「[フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-232">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="0b916-233">既定の順序のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="0b916-233">Overriding the default order</span></span>

<span data-ttu-id="0b916-234"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> を実装することで、実行の既定の順序をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-234">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="0b916-235">`IOrderedFilter` により、実行の順序を決定するために、スコープよりも優先される <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-235">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="0b916-236">より低い `Order` 値を持つフィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-236">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="0b916-237">より高い `Order` の値を持つフィルターのそれよりも前に *before* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-237">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="0b916-238">より高い `Order` の値を持つフィルターのそれよりも後に *after* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-238">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="0b916-239">`Order` プロパティは、次のコンストラクター パラメーターで設定できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-239">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="0b916-240">次のコントローラーの 2 つのアクションフィルターについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="0b916-240">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="0b916-241">グローバル フィルターが次のように `StartUp.ConfigureServices` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-241">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="0b916-242">3 つのフィルターは、次の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-242">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="0b916-243">フィルターの実行順序を決定するときに、`Order` プロパティによりスコープがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="0b916-244">最初に順序でフィルターが並べ替えられ、次に同じ順位の優先度を決めるためにスコープが使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="0b916-245">組み込みのフィルターはすべて `IOrderedFilter` を実装し、既定の `Order` 値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="0b916-246">既に説明したように、コントローラー レベルのフィルターでは、[Order](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) プロパティが `int.MinValue` に設定されます。組み込みのフィルターの場合は、`Order` が 0 以外の値に設定されている場合を除き、スコープによって順序が決定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-246">As mentioned previously, controller level filters set the [Order](https://github.com/aspnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="0b916-247">上記のコードにおいて、`MySampleActionFilter` にはグローバル スコープがあるため、コントローラー スコープを持つ `MyAction2FilterAttribute` の前で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-247">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="0b916-248">`MyAction2FilterAttribute` を最初に実行するようにするには、`int.MinValue` に対して順序を設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-248">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="0b916-249">グローバル フィルター `MySampleActionFilter` を最初に実行するようにするには、次のように `Order` を `int.MinValue` に設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-249">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="0b916-250">キャンセルとショートサーキット</span><span class="sxs-lookup"><span data-stu-id="0b916-250">Cancellation and short-circuiting</span></span>

<span data-ttu-id="0b916-251">フィルター メソッドに提供される <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> パラメーターで <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> プロパティを設定することで、フィルター パイプラインをショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-251">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="0b916-252">たとえば、次のリソース フィルターは、パイプラインの残りの部分が実行されるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="0b916-252">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="0b916-253">次のコードでは、`ShortCircuitingResourceFilter` と `AddHeader` の両方のフィルターが `SomeResource` アクション メソッドをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="0b916-253">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="0b916-254">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="0b916-254">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="0b916-255">これはリソース フィルターであり、`AddHeader` はアクション フィルターであるため、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-255">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="0b916-256">パイプラインの残りの部分は迂回されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-256">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="0b916-257">そのため、`SomeResource` アクションの場合、`AddHeader` フィルターが実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-257">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="0b916-258">`ShortCircuitingResourceFilter` が最初に実行された場合は、両方のフィルターがアクション メソッド レベルで適用されると、この動作が同じになります。</span><span class="sxs-lookup"><span data-stu-id="0b916-258">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="0b916-259">そのフィルターの種類が原因で、あるいは `Order` プロパティの明示的な使用により、`ShortCircuitingResourceFilter` が最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-259">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="0b916-260">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="0b916-260">Dependency injection</span></span>

<span data-ttu-id="0b916-261">フィルターは種類ごとまたはインスタンスごとに追加できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-261">Filters can be added by type or by instance.</span></span> <span data-ttu-id="0b916-262">インスタンスが追加される場合、そのインスタンスはすべての要求に対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-262">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="0b916-263">種類が追加される場合、それは種類でアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-263">If a type is added, it's type-activated.</span></span> <span data-ttu-id="0b916-264">種類でアクティブ化されるフィルターの意味:</span><span class="sxs-lookup"><span data-stu-id="0b916-264">A type-activated filter means:</span></span>

* <span data-ttu-id="0b916-265">インスタンスは要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-265">An instance is created for each request.</span></span>
* <span data-ttu-id="0b916-266">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-266">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="0b916-267">属性として実装され、コントローラー クラスまたはアクション メソッドに直接追加されるフィルターは、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって提供されるコンストラクターの依存関係を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="0b916-267">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="0b916-268">コンストラクターの依存関係は、次の理由から DI によって与えられません。</span><span class="sxs-lookup"><span data-stu-id="0b916-268">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="0b916-269">属性には、適用される場所で提供される独自のコンストラクター パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="0b916-269">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="0b916-270">これは、属性のしくみの制限です。</span><span class="sxs-lookup"><span data-stu-id="0b916-270">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="0b916-271">次のフィルターでは、DI から提供されるコンストラクターの依存関係がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-271">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="0b916-272">属性に実装された <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="0b916-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="0b916-273">上記のフィルターは、コントローラーまたはアクション メソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-273">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="0b916-274">ロガーは DI から利用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-274">Loggers are available from DI.</span></span> <span data-ttu-id="0b916-275">ただし、ログ目的でのみフィルターを作成し、使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="0b916-275">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="0b916-276">[組み込みフレームワークのログ機能](xref:fundamentals/logging/index)で通常、ログ記録に必要なものが与えられます。</span><span class="sxs-lookup"><span data-stu-id="0b916-276">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="0b916-277">フィルターに追加されるログ記録:</span><span class="sxs-lookup"><span data-stu-id="0b916-277">Logging added to filters:</span></span>

* <span data-ttu-id="0b916-278">ビジネス ドメインの懸念事項やフィルターに固有の動作に焦点を合わせます。</span><span class="sxs-lookup"><span data-stu-id="0b916-278">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="0b916-279">アクションやその他のフレームワーク イベントはログに記録**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="0b916-279">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="0b916-280">組み込みのフィルターでは、アクションとフレームワーク イベントが記録されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-280">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="0b916-281">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="0b916-281">ServiceFilterAttribute</span></span>

<span data-ttu-id="0b916-282">サービス フィルターの実装の種類は `ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="0b916-282">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="0b916-283"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は DI からフィルターのインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="0b916-283">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="0b916-284">このコードでは、`AddHeaderResultServiceFilter` が示されています。</span><span class="sxs-lookup"><span data-stu-id="0b916-284">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="0b916-285">次のコードでは、`AddHeaderResultServiceFilter` が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-285">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="0b916-286">次のコードでは、`ServiceFilter` 属性により、DI から `AddHeaderResultServiceFilter` フィルターのインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-286">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="0b916-287">`ServiceFilterAttribute` を使用するときに、[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-287">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="0b916-288">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="0b916-288">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="0b916-289">ASP.NET Core ランタイムで保証されないこと:</span><span class="sxs-lookup"><span data-stu-id="0b916-289">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="0b916-290">フィルターのインスタンスが 1 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-290">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="0b916-291">フィルターが後の時点で、DI コンテナーから再要求されることはありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-291">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="0b916-292">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="0b916-292">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="0b916-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="0b916-294">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="0b916-294">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="0b916-295">`CreateInstance` により、DI から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-295">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="0b916-296">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="0b916-296">TypeFilterAttribute</span></span>

<span data-ttu-id="0b916-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は<xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> と似ていますが、その型は DI コンテナーから直接解決されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="0b916-298"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> を使って型をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="0b916-298">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="0b916-299">`TypeFilterAttribute` 型は DI コンテナーから直接解決されないためです。</span><span class="sxs-lookup"><span data-stu-id="0b916-299">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="0b916-300">`TypeFilterAttribute` を利用して参照される型は、DI コンテナーに登録する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-300">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="0b916-301">DI コンテナーによって依存関係が満たされています。</span><span class="sxs-lookup"><span data-stu-id="0b916-301">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="0b916-302">`TypeFilterAttribute` は必要に応じて、型のコンストラクター引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-302">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="0b916-303">`TypeFilterAttribute` を使用するときに、[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-303">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="0b916-304">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="0b916-304">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="0b916-305">ASP.NET Core ランタイムでは、フィルターの 1 インスタンスが作成されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-305">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="0b916-306">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="0b916-306">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="0b916-307">次の例は、`TypeFilterAttribute` を使用して、型に引数を渡す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-307">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="0b916-308">承認フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-308">Authorization filters</span></span>

<span data-ttu-id="0b916-309">承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-309">Authorization filters:</span></span>

* <span data-ttu-id="0b916-310">フィルター パイプライン内で実行される最初のフィルターです。</span><span class="sxs-lookup"><span data-stu-id="0b916-310">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="0b916-311">アクション メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="0b916-311">Control access to action methods.</span></span>
* <span data-ttu-id="0b916-312">before メソッドが与えられ、after メソッドは与えられません。</span><span class="sxs-lookup"><span data-stu-id="0b916-312">Have a before method, but no after method.</span></span>

<span data-ttu-id="0b916-313">カスタム承認フィルターには、カスタム承認フレームワークが必要です。</span><span class="sxs-lookup"><span data-stu-id="0b916-313">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="0b916-314">カスタム フィルターを記述するよりも、独自の承認ポリシーを構成するか、カスタム承認ポリシーを記述することを選びます。</span><span class="sxs-lookup"><span data-stu-id="0b916-314">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="0b916-315">組み込み承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-315">The built-in authorization filter:</span></span>

* <span data-ttu-id="0b916-316">承認システムを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b916-316">Calls the authorization system.</span></span>
* <span data-ttu-id="0b916-317">要求は承認されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-317">Does not authorize requests.</span></span>

<span data-ttu-id="0b916-318">承認フィルター内で例外をスロー**しません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-318">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="0b916-319">例外は処理されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-319">The exception will not be handled.</span></span>
* <span data-ttu-id="0b916-320">例外フィルターで例外が処理されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-320">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="0b916-321">承認フィルターで例外が発生した場合、チャレンジ発行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-321">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="0b916-322">承認の詳細については、[こちら](xref:security/authorization/introduction)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-322">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="0b916-323">リソース フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-323">Resource filters</span></span>

<span data-ttu-id="0b916-324">リソース フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-324">Resource filters:</span></span>

* <span data-ttu-id="0b916-325"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-325">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="0b916-326">実行により、ほとんどのフィルター パイプラインがラップされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-326">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="0b916-327">[承認フィルター](#authorization-filters)のみ、リソース フィルターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-327">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="0b916-328">リソース フィルターは、ほとんどのパイプラインをショートサーキットする目的で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="0b916-328">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="0b916-329">たとえば、キャッシュ フィルターにより、キャッシュ ヒットがあるとき、残りのパイプラインを回避できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-329">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="0b916-330">リソース フィルターの例:</span><span class="sxs-lookup"><span data-stu-id="0b916-330">Resource filter examples:</span></span>

* <span data-ttu-id="0b916-331">前に示した[ショートサーキットするリソース フィルター](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="0b916-331">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="0b916-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="0b916-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="0b916-333">モデル バインドがフォーム データにアクセスすることを禁止します。</span><span class="sxs-lookup"><span data-stu-id="0b916-333">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="0b916-334">メモリにフォーム データが読み込まれないようにする目的で大きなファイルのアップロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-334">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="0b916-335">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-335">Action filters</span></span>

<span data-ttu-id="0b916-336">アクション フィルターは Razor Pages に適用**されません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-336">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="0b916-337">Razor Pages では <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="0b916-337">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="0b916-338">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-338">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="0b916-339">アクション フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-339">Action filters:</span></span>

* <span data-ttu-id="0b916-340"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-340">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="0b916-341">この実行はアクション メソッドの実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="0b916-341">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="0b916-342">次のコードは、サンプル アクション フィルターを示しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-342">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="0b916-343"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> では次のプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-343">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="0b916-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - アクション メソッドへの入力の読み取りを有効にします。</span><span class="sxs-lookup"><span data-stu-id="0b916-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="0b916-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - コントローラー インスタンスを操作できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="0b916-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` を設定すると、アクション メソッドと後続のアクション フィルターの実行がショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="0b916-347">アクション メソッドで例外をスローする:</span><span class="sxs-lookup"><span data-stu-id="0b916-347">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="0b916-348">後続のフィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="0b916-348">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="0b916-349">`Result` の設定とは異なり、結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-349">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="0b916-350"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> は、`Controller` と `Result` に加え、次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="0b916-350">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="0b916-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 別のフィルターによってアクションの実行がショートサーキットされた場合は、true になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="0b916-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - アクションまたは前に実行されたアクション フィルターが例外をスローした場合は、null 以外になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="0b916-353">このプロパティを null に設定する:</span><span class="sxs-lookup"><span data-stu-id="0b916-353">Setting this property to null:</span></span>

  * <span data-ttu-id="0b916-354">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-354">Effectively handles the exception.</span></span>
  * <span data-ttu-id="0b916-355">`Result` は、アクション メソッドから返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-355">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="0b916-356">`IAsyncActionFilter` の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> の呼び出しによって:</span><span class="sxs-lookup"><span data-stu-id="0b916-356">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="0b916-357">後続のすべてのアクション フィルターとアクション メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-357">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="0b916-358">`ActionExecutedContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="0b916-358">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="0b916-359">ショートサーキットするには、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> を結果インスタンスに割り当てます。`next` (`ActionExecutionDelegate`) は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="0b916-359">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="0b916-360">このフレームワークからは、サブクラス化できる抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="0b916-360">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="0b916-361">`OnActionExecuting` アクション フィルターを次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-361">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="0b916-362">モデルの状態を検証します。</span><span class="sxs-lookup"><span data-stu-id="0b916-362">Validate model state.</span></span>
* <span data-ttu-id="0b916-363">状態が有効でない場合は、エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="0b916-363">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="0b916-364">`OnActionExecuted` メソッドは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-364">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="0b916-365">また、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> プロパティを介してアクションの結果を表示したり、操作したりできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-365">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="0b916-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> は、アクションの実行が別のフィルターによってショートサーキットされた場合、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="0b916-367">アクションまたは後続のアクション フィルターが例外をスローした場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="0b916-368">`Exception` を null に設定すると:</span><span class="sxs-lookup"><span data-stu-id="0b916-368">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="0b916-369">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-369">Effectively handles an exception.</span></span>
  * <span data-ttu-id="0b916-370">`ActionExecutedContext.Result` は、アクション メソッドから通常どおり返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-370">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="0b916-371">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-371">Exception filters</span></span>

<span data-ttu-id="0b916-372">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-372">Exception filters:</span></span>

* <span data-ttu-id="0b916-373"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-373">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="0b916-374">共通のエラー処理ポリシーを実装する目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-374">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="0b916-375">次の例外フィルターのサンプルでは、カスタムのエラー ビューを使用して、アプリの開発中に発生する例外に関する詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="0b916-375">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="0b916-376">次のコードでは、例外フィルターをテストします。</span><span class="sxs-lookup"><span data-stu-id="0b916-376">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="0b916-377">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-377">Exception filters:</span></span>

* <span data-ttu-id="0b916-378">before イベントと after イベントが与えられません。</span><span class="sxs-lookup"><span data-stu-id="0b916-378">Don't have before and after events.</span></span>
* <span data-ttu-id="0b916-379"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="0b916-380">Razor Page またはコントローラーの作成、[モデル バインド](xref:mvc/models/model-binding)、アクション フィルター、またはアクション メソッドで発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="0b916-380">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="0b916-381">リソース フィルター、結果フィルター、または MVC 結果の実行で発生した例外はキャッチ**しません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-381">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="0b916-382">例外を処理するには、<xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> プロパティを `true` に設定するか、応答を記述します。</span><span class="sxs-lookup"><span data-stu-id="0b916-382">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="0b916-383">これにより、例外の伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="0b916-383">This stops propagation of the exception.</span></span> <span data-ttu-id="0b916-384">例外フィルターで例外を "成功" に変えることはできません。</span><span class="sxs-lookup"><span data-stu-id="0b916-384">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="0b916-385">それができるのは、アクション フィルターだけです。</span><span class="sxs-lookup"><span data-stu-id="0b916-385">Only an action filter can do that.</span></span>

<span data-ttu-id="0b916-386">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-386">Exception filters:</span></span>

* <span data-ttu-id="0b916-387">アクション内で発生する例外のトラップに適しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-387">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="0b916-388">エラー処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-388">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="0b916-389">例外処理にはミドルウェアを選択してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-389">Prefer middleware for exception handling.</span></span> <span data-ttu-id="0b916-390">呼び出されたアクション メソッドに基づいてエラー処理が*異なる*状況でのみ例外フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="0b916-390">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="0b916-391">たとえば、アプリには、API エンドポイントとビュー/HTML の両方に対するアクション メソッドがある場合があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-391">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="0b916-392">API エンドポイントは、JSON としてのエラー情報を返す可能性がある一方で、ビュー ベースのアクションがエラー ページを HTML として返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-392">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="0b916-393">結果フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-393">Result filters</span></span>

<span data-ttu-id="0b916-394">結果フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-394">Result filters:</span></span>

* <span data-ttu-id="0b916-395">インターフェイスを実装します:</span><span class="sxs-lookup"><span data-stu-id="0b916-395">Implement an interface:</span></span>
  * <span data-ttu-id="0b916-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b916-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="0b916-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b916-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="0b916-398">この実行はアクション結果の実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="0b916-398">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="0b916-399">IResultFilter および IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="0b916-399">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="0b916-400">次は、HTTP ヘッダーを追加する結果フィルターのコードです。</span><span class="sxs-lookup"><span data-stu-id="0b916-400">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="0b916-401">実行されている結果の種類は、アクションに依存します。</span><span class="sxs-lookup"><span data-stu-id="0b916-401">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="0b916-402">ビューを返すアクションには、実行されている <xref:Microsoft.AspNetCore.Mvc.ViewResult> の一部として、すべての razor 処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-402">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="0b916-403">API メソッドは、結果の実行の一部としていくつかのシリアル化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-403">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="0b916-404">[アクション結果](xref:mvc/controllers/actions)に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-404">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="0b916-405">結果フィルターは、アクションまたはアクション フィルターによってアクション結果を生成される場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-405">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="0b916-406">結果フィルターは、次の場合には実行されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-406">Result filters are not executed when:</span></span>

* <span data-ttu-id="0b916-407">承認フィルターまたはリソース フィルターによって、パイプラインがショートサーキットされる。</span><span class="sxs-lookup"><span data-stu-id="0b916-407">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="0b916-408">アクションの結果を生成することで、例外フィルターによって例外が処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-408">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="0b916-409"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> メソッドは、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> を `true` に設定することで、アクションの結果と後続の結果フィルターの実行をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-409">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="0b916-410">ショートサーキットする場合は、空の応答が生成されないように応答オブジェクトに記述します。</span><span class="sxs-lookup"><span data-stu-id="0b916-410">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="0b916-411">`IResultFilter.OnResultExecuting` での例外のスロー:</span><span class="sxs-lookup"><span data-stu-id="0b916-411">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="0b916-412">アクション結果と後続フィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="0b916-412">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="0b916-413">結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-413">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="0b916-414"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> メソッドが実行されたとき、おそらく応答は既にクライアントに送信されています。</span><span class="sxs-lookup"><span data-stu-id="0b916-414">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="0b916-415">応答が既にクライアントに送信されていた場合は、それを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="0b916-415">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="0b916-416">別のフィルターによってアクションの結果の実行がショートサーキットされた場合、`ResultExecutedContext.Canceled` は `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-416">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="0b916-417">アクションの結果または後続の結果フィルターが例外をスローした場合、`ResultExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-417">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="0b916-418">`Exception` を null に設定すると、例外を効果的に処理し、パイプラインの後方で例外が再スローされるのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-418">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="0b916-419">結果フィルターの例外を処理するとき、応答にデータを書き込む目的で信頼できる方法はありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-419">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="0b916-420">アクションの結果により例外がスローされるとき、ヘッダーがクライアントにフラッシュされている場合、エラー コードを送信する目的で信頼できるメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-420">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="0b916-421"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> で `await next` を呼び出すと、後続のすべての結果フィルターとアクションの結果が実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-421">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="0b916-422">ショートサーキットするには、[ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) を `true` に設定し、`ResultExecutionDelegate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="0b916-422">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="0b916-423">このフレームワークからは、サブクラス化できる抽象 `ResultFilterAttribute` が与えられます。</span><span class="sxs-lookup"><span data-stu-id="0b916-423">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="0b916-424">前に示した [AddHeaderAttribute](#add-header-attribute) クラスは、結果フィルター属性の一例です。</span><span class="sxs-lookup"><span data-stu-id="0b916-424">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="0b916-425">IAlwaysRunResultFilter および IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="0b916-425">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="0b916-426"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> および <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> インターフェイスでは、すべてのアクションの結果に対して実行される <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> の実装が宣言されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-426">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="0b916-427">これには、以下によって生成されるアクションの結果が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-427">This includes action results produced by:</span></span>

* <span data-ttu-id="0b916-428">ショートサーキットが行われる承認フィルターとリソース フィルター。</span><span class="sxs-lookup"><span data-stu-id="0b916-428">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="0b916-429">例外フィルター。</span><span class="sxs-lookup"><span data-stu-id="0b916-429">Exception filters.</span></span>

<span data-ttu-id="0b916-430">たとえば、次のフィルターは常に実行され、コンテンツ ネゴシエーションが失敗した場合に "*422 処理不可エンティティ*" 状態コードを使ってアクションの結果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) を設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-430">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="0b916-431">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="0b916-431">IFilterFactory</span></span>

<span data-ttu-id="0b916-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="0b916-433">そのため、`IFilterFactory` インスタンスはフィルター パイプライン内の任意の場所で `IFilterMetadata` インスタンスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-433">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="0b916-434">ランタイムでは、フィルターを呼び出す準備をする際、`IFilterFactory` へのキャストが試行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-434">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="0b916-435">そのキャストが成功した場合、呼び出される `IFilterMetadata` インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-435">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="0b916-436">これにより、アプリの起動時に正確なフィルター パイプラインを明示的に設定する必要がないため、柔軟なデザインが可能になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-436">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="0b916-437">フィルターを作成するための別の方法として、カスタムの属性の実装で `IFilterFactory` を実装できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-437">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="0b916-438">フィルターは次のコードで適用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-438">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="0b916-439">[ダウンロード サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)を実行することで、上記のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="0b916-439">Test the preceding code by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="0b916-440">F12 開発者ツールを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b916-440">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="0b916-441">`https://localhost:5001/Sample/HeaderWithFactory` に移動します。</span><span class="sxs-lookup"><span data-stu-id="0b916-441">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="0b916-442">F12 開発者ツールでは、サンプル コードによって追加された次の応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-442">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="0b916-443">**author:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="0b916-443">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="0b916-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="0b916-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="0b916-445">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="0b916-445">**internal:** `My header`</span></span>

<span data-ttu-id="0b916-446">上記のコードでは、**internal:** `My header` という応答ヘッダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-446">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="0b916-447">属性に実装された IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="0b916-447">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="0b916-448">`IFilterFactory` を実装するフィルターは次のようなフィルターに便利です。</span><span class="sxs-lookup"><span data-stu-id="0b916-448">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="0b916-449">パラメーターの引き渡しを必要としません。</span><span class="sxs-lookup"><span data-stu-id="0b916-449">Don't require passing parameters.</span></span>
* <span data-ttu-id="0b916-450">DI で満たす必要があるコンストラクター依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-450">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="0b916-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="0b916-452">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="0b916-452">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="0b916-453">`CreateInstance` により、サービス コンテナー (DI) から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-453">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="0b916-454">次のコードでは、`[SampleActionFilter]` を適用する 3 つの手法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-454">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="0b916-455">上記のコードでは、`SampleActionFilter` を適用する方法としては、`[SampleActionFilter]` でメソッドを装飾する方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-455">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="0b916-456">フィルター パイプラインでのミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="0b916-456">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="0b916-457">リソース フィルターは、パイプラインの後方で登場するすべての実行を囲む点で、[ミドルウェア](xref:fundamentals/middleware/index)のように機能します。</span><span class="sxs-lookup"><span data-stu-id="0b916-457">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="0b916-458">ただし、フィルターはランタイムの一部である点がミドルウェアとは異なります。つまり、それらはコンテキストとコンストラクトにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-458">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="0b916-459">ミドルウェアをフィルターとして使用するには、フィルター パイプラインに挿入するミドルウェアを指定する `Configure` メソッドを使用して型を作成します。</span><span class="sxs-lookup"><span data-stu-id="0b916-459">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="0b916-460">ローカリゼーション ミドルウェアを使用して要求の現在のカルチャを確立する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0b916-460">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="0b916-461"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> を使用し、ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="0b916-461">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="0b916-462">ミドルウェア フィルターは、フィルター パイプラインのリソース フィルターと同じステージ (モデル バインドの前、残りのパイプラインの後) で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-462">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="0b916-463">次の操作</span><span class="sxs-lookup"><span data-stu-id="0b916-463">Next actions</span></span>

* <span data-ttu-id="0b916-464">[Razor Pages のフィルター メソッド](xref:razor-pages/filter)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0b916-464">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="0b916-465">フィルターを試すには、[GitHub のサンプルをダウンロードして、テストおよび変更を行います](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)。</span><span class="sxs-lookup"><span data-stu-id="0b916-465">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b916-466">作成者: [Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="0b916-466">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0b916-467">ASP.NET Core で*フィルター*を使用すると、要求処理パイプラインの特定のステージの前または後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-467">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="0b916-468">組み込みのフィルターでは次のようなタスクが処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-468">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="0b916-469">許可 (ユーザーに許可が与えられていないリソースの場合、アクセスを禁止する)。</span><span class="sxs-lookup"><span data-stu-id="0b916-469">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="0b916-470">応答キャッシュ (要求パイプラインを迂回し、キャッシュされている応答を返す)。</span><span class="sxs-lookup"><span data-stu-id="0b916-470">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="0b916-471">横断的な問題を処理するカスタム フィルターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-471">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="0b916-472">横断的な問題の例には、エラー処理、キャッシュ、構成、認証、ログなどがあります。</span><span class="sxs-lookup"><span data-stu-id="0b916-472">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="0b916-473">フィルターにより、コードの重複が回避されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-473">Filters avoid duplicating code.</span></span> <span data-ttu-id="0b916-474">たとえば、エラー処理例外フィルターではエラー処理を統合できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-474">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="0b916-475">このドキュメントは、Razor Pages、API コントローラー、ビューのあるコントローラーに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="0b916-475">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="0b916-476">[サンプルを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0b916-476">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="0b916-477">フィルターのしくみ</span><span class="sxs-lookup"><span data-stu-id="0b916-477">How filters work</span></span>

<span data-ttu-id="0b916-478">*ASP.NET Core のアクション呼び出しパイプライン*内で実行されるフィルターは、*フィルター パイプライン*と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="0b916-478">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="0b916-479">フィルター パイプラインは、ASP.NET Core が実行するアクションを選択した後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-479">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求は、他のミドルウェア、ルーティング ミドルウェア、アクション選択、ASP.NET Core のアクション呼び出しパイプラインを通じて処理されます。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="0b916-482">フィルターの種類</span><span class="sxs-lookup"><span data-stu-id="0b916-482">Filter types</span></span>

<span data-ttu-id="0b916-483">フィルターの種類はそれぞれ、フィルター パイプラインの異なるステージで実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-483">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="0b916-484">[承認フィルター](#authorization-filters)は、最初に実行され、ユーザーが要求に対して承認されているかどうかを判断するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-484">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="0b916-485">承認フィルターでは、要求が承認されていない場合、パイプラインがショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-485">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="0b916-486">[リソース フィルター](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="0b916-486">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="0b916-487">承認後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-487">Run after authorization.</span></span>  
  * <span data-ttu-id="0b916-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> では、残りのフィルター パイプラインの前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="0b916-489">たとえば、`OnResourceExecuting` では、モデル バインディングの前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-489">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="0b916-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> では、残りのパイプラインの完了後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="0b916-491">[アクション フィルター](#action-filters)は、個々のアクション メソッドが呼び出される直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-491">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="0b916-492">アクションに渡される引数とアクションから返される結果を操作するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-492">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="0b916-493">アクション フィルターは Razor Pages ではサポート**されていません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-493">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="0b916-494">[例外フィルター](#exception-filters)は、応答本文に何かが書き込まれる前に発生する未処理の例外にグローバル ポリシーを適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-494">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="0b916-495">[結果フィルター](#result-filters)は、個々のアクション結果の実行の直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-495">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="0b916-496">アクション メソッドが正常に実行された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-496">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="0b916-497">ビューまたはフォーマッタ実行を取り囲む必要があるロジックに便利です。</span><span class="sxs-lookup"><span data-stu-id="0b916-497">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="0b916-498">フィルターの種類がフィルター パイプラインでどのように連携しているかを、次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="0b916-498">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求は、承認フィルター、リソース フィルター、モデル バインド、アクション フィルター、アクションの実行とアクション結果の変換、例外フィルター、結果フィルター、結果の実行を介して処理されます。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="0b916-501">実装</span><span class="sxs-lookup"><span data-stu-id="0b916-501">Implementation</span></span>

<span data-ttu-id="0b916-502">フィルターは、異なるインターフェイス定義を介して、同期と非同期の実装をサポートします。</span><span class="sxs-lookup"><span data-stu-id="0b916-502">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="0b916-503">同期フィルターでは、パイプライン ステージの前 (`On-Stage-Executing`) と後 (`On-Stage-Executed`) にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-503">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="0b916-504">たとえば、`OnActionExecuting` はアクション メソッドの呼び出し前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-504">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="0b916-505">`OnActionExecuted` は、アクション メソッドが戻った後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-505">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="0b916-506">非同期フィルターにより `On-Stage-ExecutionAsync` メソッドが定義されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-506">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="0b916-507">上記のコードでは、`SampleAsyncActionFilter` にアクション メソッドを実行する <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-507">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="0b916-508">各 `On-Stage-ExecutionAsync` メソッドは、フィルターのパイプライン ステージを実行する `FilterType-ExecutionDelegate` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="0b916-508">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="0b916-509">複数のフィルター ステージ</span><span class="sxs-lookup"><span data-stu-id="0b916-509">Multiple filter stages</span></span>

<span data-ttu-id="0b916-510">複数のフィルター ステージのためのインターフェイスを 1 つのクラスで実装できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-510">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="0b916-511">たとえば、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> クラスは、`IActionFilter`、`IResultFilter`、およびそれらの非同期バージョンを実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-511">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="0b916-512">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-512">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="0b916-513">ランタイムは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b916-513">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="0b916-514">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b916-514">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="0b916-515">非同期インターフェイスと同期インターフェイスの両方が 1 つのクラスで実装される場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-515">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="0b916-516"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> などの抽象クラスを使用する場合は、同期メソッドのみをオーバーライドするか、フィルターの種類ごとに非同期メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="0b916-516">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="0b916-517">組み込みのフィルター属性</span><span class="sxs-lookup"><span data-stu-id="0b916-517">Built-in filter attributes</span></span>

<span data-ttu-id="0b916-518">ASP.NET Core には、サブクラスを作成したり、カスタマイズしたりできる組み込みの属性ベースのフィルターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-518">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="0b916-519">たとえば、次の結果フィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="0b916-519">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="0b916-520">上記の例のように、属性によってフィルターは引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-520">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="0b916-521">`AddHeaderAttribute` をコントローラーまたはアクション メソッドに適用し、HTTP ヘッダーの名前と値を指定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-521">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="0b916-522">フィルター インターフェイスのいくつかには対応する属性があり、カスタムの実装に基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-522">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="0b916-523">フィルター属性:</span><span class="sxs-lookup"><span data-stu-id="0b916-523">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="0b916-524">フィルターのスコープと実行の順序</span><span class="sxs-lookup"><span data-stu-id="0b916-524">Filter scopes and order of execution</span></span>

<span data-ttu-id="0b916-525">フィルターは、3 つの*スコープ*のいずれかでパイプラインに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-525">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="0b916-526">アクションで属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="0b916-526">Using an attribute on an action.</span></span>
* <span data-ttu-id="0b916-527">コントローラーで属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="0b916-527">Using an attribute on a controller.</span></span>
* <span data-ttu-id="0b916-528">次のコードのように、すべてのコントローラーとアクションに対してグローバルに:</span><span class="sxs-lookup"><span data-stu-id="0b916-528">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0b916-529">上記のコードでは、[MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) コレクションを利用し、3 つのフィルターがグローバルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-529">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="0b916-530">実行の既定の順序</span><span class="sxs-lookup"><span data-stu-id="0b916-530">Default order of execution</span></span>

<span data-ttu-id="0b916-531">"*同じ種類のフィルター*" が複数存在する場合は、スコープによって、フィルターの実行の既定の順序が決まります。</span><span class="sxs-lookup"><span data-stu-id="0b916-531">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="0b916-532">グローバル フィルターによってクラス フィルターが囲まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-532">Global filters surround class filters.</span></span> <span data-ttu-id="0b916-533">クラス フィルターによってメソッド フィルターが囲まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-533">Class filters surround method filters.</span></span>

<span data-ttu-id="0b916-534">フィルターの入れ子の結果として、フィルターの *after* コードが *before* コードと逆の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-534">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="0b916-535">フィルター シーケンス:</span><span class="sxs-lookup"><span data-stu-id="0b916-535">The filter sequence:</span></span>

* <span data-ttu-id="0b916-536">グローバル フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-536">The *before* code of global filters.</span></span>
  * <span data-ttu-id="0b916-537">コントローラー フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-537">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="0b916-538">アクション メソッド フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-538">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="0b916-539">アクション メソッド フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-539">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="0b916-540">コントローラー フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-540">The *after* code of controller filters.</span></span>
* <span data-ttu-id="0b916-541">グローバル フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="0b916-541">The *after* code of global filters.</span></span>
  
<span data-ttu-id="0b916-542">次の例は、同期アクション フィルターに対してフィルター メソッドが呼び出される順序を示しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-542">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="0b916-543">シーケンス</span><span class="sxs-lookup"><span data-stu-id="0b916-543">Sequence</span></span> | <span data-ttu-id="0b916-544">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="0b916-544">Filter scope</span></span> | <span data-ttu-id="0b916-545">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-545">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="0b916-546">1</span><span class="sxs-lookup"><span data-stu-id="0b916-546">1</span></span> | <span data-ttu-id="0b916-547">Global</span><span class="sxs-lookup"><span data-stu-id="0b916-547">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b916-548">2</span><span class="sxs-lookup"><span data-stu-id="0b916-548">2</span></span> | <span data-ttu-id="0b916-549">コントローラー</span><span class="sxs-lookup"><span data-stu-id="0b916-549">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b916-550">3</span><span class="sxs-lookup"><span data-stu-id="0b916-550">3</span></span> | <span data-ttu-id="0b916-551">メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-551">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b916-552">4</span><span class="sxs-lookup"><span data-stu-id="0b916-552">4</span></span> | <span data-ttu-id="0b916-553">メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-553">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="0b916-554">5</span><span class="sxs-lookup"><span data-stu-id="0b916-554">5</span></span> | <span data-ttu-id="0b916-555">コントローラー</span><span class="sxs-lookup"><span data-stu-id="0b916-555">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="0b916-556">6</span><span class="sxs-lookup"><span data-stu-id="0b916-556">6</span></span> | <span data-ttu-id="0b916-557">Global</span><span class="sxs-lookup"><span data-stu-id="0b916-557">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="0b916-558">このシーケンスが示すもの:</span><span class="sxs-lookup"><span data-stu-id="0b916-558">This sequence shows:</span></span>

* <span data-ttu-id="0b916-559">メソッド フィルターは、コントローラー フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-559">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="0b916-560">コントローラー フィルターは、グローバル フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-560">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="0b916-561">コントローラーと Razor Page のレベル フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-561">Controller and Razor Page level filters</span></span>

<span data-ttu-id="0b916-562"><xref:Microsoft.AspNetCore.Mvc.Controller> 基底クラスから継承するすべてのコントローラーに、[Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)、[Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0b916-562">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="0b916-563">これらのメソッド:</span><span class="sxs-lookup"><span data-stu-id="0b916-563">These methods:</span></span>

* <span data-ttu-id="0b916-564">特定のアクションに対して実行されるフィルターをラップします。</span><span class="sxs-lookup"><span data-stu-id="0b916-564">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="0b916-565">`OnActionExecuting` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-565">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="0b916-566">`OnActionExecuted` は、あらゆるアクション フィルターの後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-566">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="0b916-567">`OnActionExecutionAsync` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-567">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="0b916-568">`next` の後のフィルターのコードは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-568">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="0b916-569">たとえば、ダウンロード サンプルで、`MySampleActionFilter` は起動中、グローバルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-569">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="0b916-570">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="0b916-570">The `TestController`:</span></span>

* <span data-ttu-id="0b916-571">`SampleActionFilterAttribute` (`[SampleActionFilter]`) が `FilterTest2` アクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-571">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="0b916-572">`OnActionExecuting` と `OnActionExecuted` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-572">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="0b916-573">`https://localhost:5001/Test/FilterTest2` に移動すると、次のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-573">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="0b916-574">Razor Pages の場合、「[フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-574">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="0b916-575">既定の順序のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="0b916-575">Overriding the default order</span></span>

<span data-ttu-id="0b916-576"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> を実装することで、実行の既定の順序をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-576">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="0b916-577">`IOrderedFilter` により、実行の順序を決定するために、スコープよりも優先される <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-577">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="0b916-578">より低い `Order` 値を持つフィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-578">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="0b916-579">より高い `Order` の値を持つフィルターのそれよりも前に *before* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-579">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="0b916-580">より高い `Order` の値を持つフィルターのそれよりも後に *after* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-580">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="0b916-581">`Order` プロパティはコンストラクター パラメーターで設定できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-581">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="0b916-582">上記の例にある同じ 3 つのアクション フィルターを検討してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-582">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="0b916-583">コントローラーとグローバル フィルターの `Order` プロパティが 1 と 2 にそれぞれ設定される場合、実行順序が逆になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-583">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="0b916-584">シーケンス</span><span class="sxs-lookup"><span data-stu-id="0b916-584">Sequence</span></span> | <span data-ttu-id="0b916-585">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="0b916-585">Filter scope</span></span> | <span data-ttu-id="0b916-586">`Order` プロパティ</span><span class="sxs-lookup"><span data-stu-id="0b916-586">`Order` property</span></span> | <span data-ttu-id="0b916-587">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-587">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="0b916-588">1</span><span class="sxs-lookup"><span data-stu-id="0b916-588">1</span></span> | <span data-ttu-id="0b916-589">メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-589">Method</span></span> | <span data-ttu-id="0b916-590">0</span><span class="sxs-lookup"><span data-stu-id="0b916-590">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="0b916-591">2</span><span class="sxs-lookup"><span data-stu-id="0b916-591">2</span></span> | <span data-ttu-id="0b916-592">コントローラー</span><span class="sxs-lookup"><span data-stu-id="0b916-592">Controller</span></span> | <span data-ttu-id="0b916-593">1</span><span class="sxs-lookup"><span data-stu-id="0b916-593">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="0b916-594">3</span><span class="sxs-lookup"><span data-stu-id="0b916-594">3</span></span> | <span data-ttu-id="0b916-595">Global</span><span class="sxs-lookup"><span data-stu-id="0b916-595">Global</span></span> | <span data-ttu-id="0b916-596">2</span><span class="sxs-lookup"><span data-stu-id="0b916-596">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="0b916-597">4</span><span class="sxs-lookup"><span data-stu-id="0b916-597">4</span></span> | <span data-ttu-id="0b916-598">Global</span><span class="sxs-lookup"><span data-stu-id="0b916-598">Global</span></span> | <span data-ttu-id="0b916-599">2</span><span class="sxs-lookup"><span data-stu-id="0b916-599">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="0b916-600">5</span><span class="sxs-lookup"><span data-stu-id="0b916-600">5</span></span> | <span data-ttu-id="0b916-601">コントローラー</span><span class="sxs-lookup"><span data-stu-id="0b916-601">Controller</span></span> | <span data-ttu-id="0b916-602">1</span><span class="sxs-lookup"><span data-stu-id="0b916-602">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="0b916-603">6</span><span class="sxs-lookup"><span data-stu-id="0b916-603">6</span></span> | <span data-ttu-id="0b916-604">メソッド</span><span class="sxs-lookup"><span data-stu-id="0b916-604">Method</span></span> | <span data-ttu-id="0b916-605">0</span><span class="sxs-lookup"><span data-stu-id="0b916-605">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="0b916-606">フィルターの実行順序を決定するときに、`Order` プロパティによりスコープがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-606">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="0b916-607">最初に順序でフィルターが並べ替えられ、次に同じ順位の優先度を決めるためにスコープが使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-607">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="0b916-608">組み込みのフィルターはすべて `IOrderedFilter` を実装し、既定の `Order` 値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-608">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="0b916-609">組み込みのフィルターの場合、`Order` をゼロ以外の値に設定しない限り、スコープによって順序が決定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-609">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="0b916-610">キャンセルとショートサーキット</span><span class="sxs-lookup"><span data-stu-id="0b916-610">Cancellation and short-circuiting</span></span>

<span data-ttu-id="0b916-611">フィルター メソッドに提供される <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> パラメーターで <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> プロパティを設定することで、フィルター パイプラインをショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-611">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="0b916-612">たとえば、次のリソース フィルターは、パイプラインの残りの部分が実行されるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="0b916-612">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="0b916-613">次のコードでは、`ShortCircuitingResourceFilter` と `AddHeader` の両方のフィルターが `SomeResource` アクション メソッドをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="0b916-613">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="0b916-614">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="0b916-614">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="0b916-615">これはリソース フィルターであり、`AddHeader` はアクション フィルターであるため、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-615">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="0b916-616">パイプラインの残りの部分は迂回されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-616">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="0b916-617">そのため、`SomeResource` アクションの場合、`AddHeader` フィルターが実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-617">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="0b916-618">`ShortCircuitingResourceFilter` が最初に実行された場合は、両方のフィルターがアクション メソッド レベルで適用されると、この動作が同じになります。</span><span class="sxs-lookup"><span data-stu-id="0b916-618">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="0b916-619">そのフィルターの種類が原因で、あるいは `Order` プロパティの明示的な使用により、`ShortCircuitingResourceFilter` が最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-619">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="0b916-620">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="0b916-620">Dependency injection</span></span>

<span data-ttu-id="0b916-621">フィルターは種類ごとまたはインスタンスごとに追加できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-621">Filters can be added by type or by instance.</span></span> <span data-ttu-id="0b916-622">インスタンスが追加される場合、そのインスタンスはすべての要求に対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-622">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="0b916-623">種類が追加される場合、それは種類でアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-623">If a type is added, it's type-activated.</span></span> <span data-ttu-id="0b916-624">種類でアクティブ化されるフィルターの意味:</span><span class="sxs-lookup"><span data-stu-id="0b916-624">A type-activated filter means:</span></span>

* <span data-ttu-id="0b916-625">インスタンスは要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-625">An instance is created for each request.</span></span>
* <span data-ttu-id="0b916-626">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-626">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="0b916-627">属性として実装され、コントローラー クラスまたはアクション メソッドに直接追加されるフィルターは、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって提供されるコンストラクターの依存関係を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="0b916-627">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="0b916-628">コンストラクターの依存関係は、次の理由から DI によって与えられません。</span><span class="sxs-lookup"><span data-stu-id="0b916-628">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="0b916-629">属性には、適用される場所で提供される独自のコンストラクター パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="0b916-629">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="0b916-630">これは、属性のしくみの制限です。</span><span class="sxs-lookup"><span data-stu-id="0b916-630">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="0b916-631">次のフィルターでは、DI から提供されるコンストラクターの依存関係がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-631">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="0b916-632">属性に実装された <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="0b916-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="0b916-633">上記のフィルターは、コントローラーまたはアクション メソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-633">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="0b916-634">ロガーは DI から利用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-634">Loggers are available from DI.</span></span> <span data-ttu-id="0b916-635">ただし、ログ目的でのみフィルターを作成し、使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="0b916-635">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="0b916-636">[組み込みフレームワークのログ機能](xref:fundamentals/logging/index)で通常、ログ記録に必要なものが与えられます。</span><span class="sxs-lookup"><span data-stu-id="0b916-636">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="0b916-637">フィルターに追加されるログ記録:</span><span class="sxs-lookup"><span data-stu-id="0b916-637">Logging added to filters:</span></span>

* <span data-ttu-id="0b916-638">ビジネス ドメインの懸念事項やフィルターに固有の動作に焦点を合わせます。</span><span class="sxs-lookup"><span data-stu-id="0b916-638">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="0b916-639">アクションやその他のフレームワーク イベントはログに記録**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="0b916-639">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="0b916-640">組み込みフィルターでは、アクションとフレームワーク イベントが記録されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-640">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="0b916-641">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="0b916-641">ServiceFilterAttribute</span></span>

<span data-ttu-id="0b916-642">サービス フィルターの実装の種類は `ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="0b916-642">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="0b916-643"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は DI からフィルターのインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="0b916-643">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="0b916-644">このコードでは、`AddHeaderResultServiceFilter` が示されています。</span><span class="sxs-lookup"><span data-stu-id="0b916-644">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="0b916-645">次のコードでは、`AddHeaderResultServiceFilter` が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-645">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="0b916-646">次のコードでは、`ServiceFilter` 属性により、DI から `AddHeaderResultServiceFilter` フィルターのインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-646">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="0b916-647">`ServiceFilterAttribute` を使用するときに、[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-647">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="0b916-648">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="0b916-648">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="0b916-649">ASP.NET Core ランタイムで保証されないこと:</span><span class="sxs-lookup"><span data-stu-id="0b916-649">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="0b916-650">フィルターのインスタンスが 1 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-650">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="0b916-651">フィルターが後の時点で、DI コンテナーから再要求されることはありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-651">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="0b916-652">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="0b916-652">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="0b916-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="0b916-654">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="0b916-654">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="0b916-655">`CreateInstance` により、DI から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-655">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="0b916-656">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="0b916-656">TypeFilterAttribute</span></span>

<span data-ttu-id="0b916-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は<xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> と似ていますが、その型は DI コンテナーから直接解決されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="0b916-658"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> を使って型をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="0b916-658">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="0b916-659">`TypeFilterAttribute` 型は DI コンテナーから直接解決されないためです。</span><span class="sxs-lookup"><span data-stu-id="0b916-659">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="0b916-660">`TypeFilterAttribute` を利用して参照される型は、DI コンテナーに登録する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-660">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="0b916-661">DI コンテナーによって依存関係が満たされています。</span><span class="sxs-lookup"><span data-stu-id="0b916-661">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="0b916-662">`TypeFilterAttribute` は必要に応じて、型のコンストラクター引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-662">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="0b916-663">`TypeFilterAttribute` を使用するときに、[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-663">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="0b916-664">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="0b916-664">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="0b916-665">ASP.NET Core ランタイムでは、フィルターの 1 インスタンスが作成されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-665">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="0b916-666">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="0b916-666">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="0b916-667">次の例は、`TypeFilterAttribute` を使用して、型に引数を渡す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-667">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="0b916-668">承認フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-668">Authorization filters</span></span>

<span data-ttu-id="0b916-669">承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-669">Authorization filters:</span></span>

* <span data-ttu-id="0b916-670">フィルター パイプライン内で実行される最初のフィルターです。</span><span class="sxs-lookup"><span data-stu-id="0b916-670">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="0b916-671">アクション メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="0b916-671">Control access to action methods.</span></span>
* <span data-ttu-id="0b916-672">before メソッドが与えられ、after メソッドは与えられません。</span><span class="sxs-lookup"><span data-stu-id="0b916-672">Have a before method, but no after method.</span></span>

<span data-ttu-id="0b916-673">カスタム承認フィルターには、カスタム承認フレームワークが必要です。</span><span class="sxs-lookup"><span data-stu-id="0b916-673">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="0b916-674">カスタム フィルターを記述するよりも、独自の承認ポリシーを構成するか、カスタム承認ポリシーを記述することを選びます。</span><span class="sxs-lookup"><span data-stu-id="0b916-674">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="0b916-675">組み込み承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-675">The built-in authorization filter:</span></span>

* <span data-ttu-id="0b916-676">承認システムを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b916-676">Calls the authorization system.</span></span>
* <span data-ttu-id="0b916-677">要求は承認されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-677">Does not authorize requests.</span></span>

<span data-ttu-id="0b916-678">承認フィルター内で例外をスロー**しません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-678">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="0b916-679">例外は処理されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-679">The exception will not be handled.</span></span>
* <span data-ttu-id="0b916-680">例外フィルターで例外が処理されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-680">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="0b916-681">承認フィルターで例外が発生した場合、チャレンジ発行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-681">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="0b916-682">承認の詳細については、[こちら](xref:security/authorization/introduction)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-682">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="0b916-683">リソース フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-683">Resource filters</span></span>

<span data-ttu-id="0b916-684">リソース フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-684">Resource filters:</span></span>

* <span data-ttu-id="0b916-685"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-685">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="0b916-686">実行により、ほとんどのフィルター パイプラインがラップされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-686">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="0b916-687">[承認フィルター](#authorization-filters)のみ、リソース フィルターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-687">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="0b916-688">リソース フィルターは、ほとんどのパイプラインをショートサーキットする目的で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="0b916-688">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="0b916-689">たとえば、キャッシュ フィルターにより、キャッシュ ヒットがあるとき、残りのパイプラインを回避できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-689">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="0b916-690">リソース フィルターの例:</span><span class="sxs-lookup"><span data-stu-id="0b916-690">Resource filter examples:</span></span>

* <span data-ttu-id="0b916-691">前に示した[ショートサーキットするリソース フィルター](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="0b916-691">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="0b916-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="0b916-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="0b916-693">モデル バインドがフォーム データにアクセスすることを禁止します。</span><span class="sxs-lookup"><span data-stu-id="0b916-693">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="0b916-694">メモリにフォーム データが読み込まれないようにする目的で大きなファイルのアップロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-694">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="0b916-695">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-695">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0b916-696">アクション フィルターは Razor Pages に適用**されません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-696">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="0b916-697">Razor Pages では <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="0b916-697">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="0b916-698">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-698">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="0b916-699">アクション フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-699">Action filters:</span></span>

* <span data-ttu-id="0b916-700"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="0b916-701">この実行はアクション メソッドの実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="0b916-701">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="0b916-702">次のコードは、サンプル アクション フィルターを示しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-702">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="0b916-703"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> では次のプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-703">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="0b916-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - アクション メソッドへの入力を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="0b916-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="0b916-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - コントローラー インスタンスを操作できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="0b916-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` を設定すると、アクション メソッドと後続のアクション フィルターの実行がショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="0b916-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="0b916-707">アクション メソッドで例外をスローする:</span><span class="sxs-lookup"><span data-stu-id="0b916-707">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="0b916-708">後続のフィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="0b916-708">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="0b916-709">`Result` の設定とは異なり、結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-709">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="0b916-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> は、`Controller` と `Result` に加え、次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="0b916-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="0b916-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 別のフィルターによってアクションの実行がショートサーキットされた場合は、true になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="0b916-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - アクションまたは前に実行されたアクション フィルターが例外をスローした場合は、null 以外になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="0b916-713">このプロパティを null に設定する:</span><span class="sxs-lookup"><span data-stu-id="0b916-713">Setting this property to null:</span></span>

  * <span data-ttu-id="0b916-714">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-714">Effectively handles the exception.</span></span>
  * <span data-ttu-id="0b916-715">`Result` は、アクション メソッドから返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-715">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="0b916-716">`IAsyncActionFilter` の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> の呼び出しによって:</span><span class="sxs-lookup"><span data-stu-id="0b916-716">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="0b916-717">後続のすべてのアクション フィルターとアクション メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-717">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="0b916-718">`ActionExecutedContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="0b916-718">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="0b916-719">ショートサーキットするには、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> を結果インスタンスに割り当てます。`next` (`ActionExecutionDelegate`) は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="0b916-719">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="0b916-720">このフレームワークからは、サブクラス化できる抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="0b916-720">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="0b916-721">`OnActionExecuting` アクション フィルターを次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-721">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="0b916-722">モデルの状態を検証します。</span><span class="sxs-lookup"><span data-stu-id="0b916-722">Validate model state.</span></span>
* <span data-ttu-id="0b916-723">状態が有効でない場合は、エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="0b916-723">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="0b916-724">`OnActionExecuted` メソッドは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-724">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="0b916-725">また、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> プロパティを介してアクションの結果を表示したり、操作したりできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-725">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="0b916-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> は、アクションの実行が別のフィルターによってショートサーキットされた場合、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="0b916-727">アクションまたは後続のアクション フィルターが例外をスローした場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="0b916-728">`Exception` を null に設定すると:</span><span class="sxs-lookup"><span data-stu-id="0b916-728">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="0b916-729">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-729">Effectively handles an exception.</span></span>
  * <span data-ttu-id="0b916-730">`ActionExecutedContext.Result` は、アクション メソッドから通常どおり返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-730">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="0b916-731">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-731">Exception filters</span></span>

<span data-ttu-id="0b916-732">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-732">Exception filters:</span></span>

* <span data-ttu-id="0b916-733"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-733">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="0b916-734">共通のエラー処理ポリシーを実装する目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-734">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="0b916-735">次の例外フィルターのサンプルでは、カスタムのエラー ビューを使用して、アプリの開発中に発生する例外に関する詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="0b916-735">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="0b916-736">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-736">Exception filters:</span></span>

* <span data-ttu-id="0b916-737">before イベントと after イベントが与えられません。</span><span class="sxs-lookup"><span data-stu-id="0b916-737">Don't have before and after events.</span></span>
* <span data-ttu-id="0b916-738"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-738">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="0b916-739">Razor Page またはコントローラーの作成、[モデル バインド](xref:mvc/models/model-binding)、アクション フィルター、またはアクション メソッドで発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="0b916-739">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="0b916-740">リソース フィルター、結果フィルター、または MVC 結果の実行で発生した例外はキャッチ**しません**。</span><span class="sxs-lookup"><span data-stu-id="0b916-740">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="0b916-741">例外を処理するには、<xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> プロパティを `true` に設定するか、応答を記述します。</span><span class="sxs-lookup"><span data-stu-id="0b916-741">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="0b916-742">これにより、例外の伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="0b916-742">This stops propagation of the exception.</span></span> <span data-ttu-id="0b916-743">例外フィルターで例外を "成功" に変えることはできません。</span><span class="sxs-lookup"><span data-stu-id="0b916-743">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="0b916-744">それができるのは、アクション フィルターだけです。</span><span class="sxs-lookup"><span data-stu-id="0b916-744">Only an action filter can do that.</span></span>

<span data-ttu-id="0b916-745">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-745">Exception filters:</span></span>

* <span data-ttu-id="0b916-746">アクション内で発生する例外のトラップに適しています。</span><span class="sxs-lookup"><span data-stu-id="0b916-746">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="0b916-747">エラー処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-747">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="0b916-748">例外処理にはミドルウェアを選択してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-748">Prefer middleware for exception handling.</span></span> <span data-ttu-id="0b916-749">呼び出されたアクション メソッドに基づいてエラー処理が*異なる*状況でのみ例外フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="0b916-749">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="0b916-750">たとえば、アプリには、API エンドポイントとビュー/HTML の両方に対するアクション メソッドがある場合があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-750">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="0b916-751">API エンドポイントは、JSON としてのエラー情報を返す可能性がある一方で、ビュー ベースのアクションがエラー ページを HTML として返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-751">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="0b916-752">結果フィルター</span><span class="sxs-lookup"><span data-stu-id="0b916-752">Result filters</span></span>

<span data-ttu-id="0b916-753">結果フィルター:</span><span class="sxs-lookup"><span data-stu-id="0b916-753">Result filters:</span></span>

* <span data-ttu-id="0b916-754">インターフェイスを実装します:</span><span class="sxs-lookup"><span data-stu-id="0b916-754">Implement an interface:</span></span>
  * <span data-ttu-id="0b916-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b916-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="0b916-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="0b916-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="0b916-757">この実行はアクション結果の実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="0b916-757">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="0b916-758">IResultFilter および IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="0b916-758">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="0b916-759">次は、HTTP ヘッダーを追加する結果フィルターのコードです。</span><span class="sxs-lookup"><span data-stu-id="0b916-759">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="0b916-760">実行されている結果の種類は、アクションに依存します。</span><span class="sxs-lookup"><span data-stu-id="0b916-760">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="0b916-761">ビューを返すアクションには、実行されている <xref:Microsoft.AspNetCore.Mvc.ViewResult> の一部として、すべての razor 処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-761">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="0b916-762">API メソッドは、結果の実行の一部としていくつかのシリアル化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-762">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="0b916-763">[アクション結果](xref:mvc/controllers/actions)に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0b916-763">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="0b916-764">結果フィルターは、アクションまたはアクション フィルターによってアクション結果を生成される場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-764">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="0b916-765">結果フィルターは、次の場合には実行されません。</span><span class="sxs-lookup"><span data-stu-id="0b916-765">Result filters are not executed when:</span></span>

* <span data-ttu-id="0b916-766">承認フィルターまたはリソース フィルターによって、パイプラインがショートサーキットされる。</span><span class="sxs-lookup"><span data-stu-id="0b916-766">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="0b916-767">アクションの結果を生成することで、例外フィルターによって例外が処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-767">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="0b916-768"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> メソッドは、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> を `true` に設定することで、アクションの結果と後続の結果フィルターの実行をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-768">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="0b916-769">ショートサーキットする場合は、空の応答が生成されないように応答オブジェクトに記述します。</span><span class="sxs-lookup"><span data-stu-id="0b916-769">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="0b916-770">`IResultFilter.OnResultExecuting` で例外をスローすることで:</span><span class="sxs-lookup"><span data-stu-id="0b916-770">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="0b916-771">アクション結果と後続フィルターの実行が回避されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-771">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="0b916-772">結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-772">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="0b916-773"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> メソッドが実行されたとき、応答が既にクライアントに送信されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-773">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="0b916-774">応答が既にクライアントに送信されていた場合は、それ以上変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="0b916-774">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="0b916-775">別のフィルターによってアクションの結果の実行がショートサーキットされた場合、`ResultExecutedContext.Canceled` は `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-775">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="0b916-776">アクションの結果または後続の結果フィルターが例外をスローした場合、`ResultExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-776">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="0b916-777">`Exception` を null に設定すると、例外を効果的に処理でき、パイプラインの後方で ASP.NET Core によって例外が再スローされることを防げます。</span><span class="sxs-lookup"><span data-stu-id="0b916-777">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="0b916-778">結果フィルターの例外を処理するとき、応答にデータを書き込む目的で信頼できる方法はありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-778">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="0b916-779">アクションの結果により例外がスローされるとき、ヘッダーがクライアントにフラッシュされている場合、エラー コードを送信する目的で信頼できるメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="0b916-779">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="0b916-780"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> で `await next` を呼び出すと、後続のすべての結果フィルターとアクションの結果が実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-780">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="0b916-781">ショートサーキットするには、[ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) を `true` に設定し、`ResultExecutionDelegate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="0b916-781">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="0b916-782">このフレームワークからは、サブクラス化できる抽象 `ResultFilterAttribute` が与えられます。</span><span class="sxs-lookup"><span data-stu-id="0b916-782">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="0b916-783">前に示した [AddHeaderAttribute](#add-header-attribute) クラスは、結果フィルター属性の一例です。</span><span class="sxs-lookup"><span data-stu-id="0b916-783">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="0b916-784">IAlwaysRunResultFilter および IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="0b916-784">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="0b916-785"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> および <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> インターフェイスでは、すべてのアクションの結果に対して実行される <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> の実装が宣言されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-785">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="0b916-786">これには、以下によって生成されるアクションの結果が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-786">This includes action results produced by:</span></span>

* <span data-ttu-id="0b916-787">ショートサーキットが行われる承認フィルターとリソース フィルター。</span><span class="sxs-lookup"><span data-stu-id="0b916-787">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="0b916-788">例外フィルター。</span><span class="sxs-lookup"><span data-stu-id="0b916-788">Exception filters.</span></span>

<span data-ttu-id="0b916-789">たとえば、次のフィルターは常に実行され、コンテンツ ネゴシエーションが失敗した場合に "*422 処理不可エンティティ*" 状態コードを使ってアクションの結果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) を設定します。</span><span class="sxs-lookup"><span data-stu-id="0b916-789">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="0b916-790">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="0b916-790">IFilterFactory</span></span>

<span data-ttu-id="0b916-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="0b916-792">そのため、`IFilterFactory` インスタンスはフィルター パイプライン内の任意の場所で `IFilterMetadata` インスタンスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-792">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="0b916-793">ランタイムでは、フィルターを呼び出す準備をする際、`IFilterFactory` へのキャストが試行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-793">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="0b916-794">そのキャストが成功した場合、呼び出される `IFilterMetadata` インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-794">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="0b916-795">これにより、アプリの起動時に正確なフィルター パイプラインを明示的に設定する必要がないため、柔軟なデザインが可能になります。</span><span class="sxs-lookup"><span data-stu-id="0b916-795">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="0b916-796">フィルターを作成するための別の方法として、カスタムの属性の実装で `IFilterFactory` を実装できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-796">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="0b916-797">[ダウンロード サンプル](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)を実行することで、前のコードをテストできます。</span><span class="sxs-lookup"><span data-stu-id="0b916-797">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="0b916-798">F12 開発者ツールを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0b916-798">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="0b916-799">`https://localhost:5001/Sample/HeaderWithFactory` に移動します。</span><span class="sxs-lookup"><span data-stu-id="0b916-799">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="0b916-800">F12 開発者ツールでは、サンプル コードによって追加された次の応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-800">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="0b916-801">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="0b916-801">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="0b916-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="0b916-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="0b916-803">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="0b916-803">**internal:** `My header`</span></span>

<span data-ttu-id="0b916-804">上記のコードでは、**internal:** `My header` という応答ヘッダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-804">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="0b916-805">属性に実装された IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="0b916-805">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="0b916-806">`IFilterFactory` を実装するフィルターは次のようなフィルターに便利です。</span><span class="sxs-lookup"><span data-stu-id="0b916-806">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="0b916-807">パラメーターの引き渡しを必要としません。</span><span class="sxs-lookup"><span data-stu-id="0b916-807">Don't require passing parameters.</span></span>
* <span data-ttu-id="0b916-808">DI で満たす必要があるコンストラクター依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="0b916-808">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="0b916-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="0b916-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="0b916-810">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="0b916-810">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="0b916-811">`CreateInstance` により、サービス コンテナー (DI) から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0b916-811">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="0b916-812">次のコードでは、`[SampleActionFilter]` を適用する 3 つの手法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="0b916-812">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="0b916-813">上記のコードでは、`SampleActionFilter` を適用する方法としては、`[SampleActionFilter]` でメソッドを装飾する方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-813">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="0b916-814">フィルター パイプラインでのミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="0b916-814">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="0b916-815">リソース フィルターは、パイプラインの後方で登場するすべての実行を囲む点で、[ミドルウェア](xref:fundamentals/middleware/index)のように機能します。</span><span class="sxs-lookup"><span data-stu-id="0b916-815">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="0b916-816">ただし、フィルターは ASP.NET Core ランタイムの一部である点がミドルウェアとは異なります。つまり、フィルターには ASP.NET Core コンテキストとコンストラクトへのアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="0b916-816">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="0b916-817">ミドルウェアをフィルターとして使用するには、フィルター パイプラインに挿入するミドルウェアを指定する `Configure` メソッドを使用して型を作成します。</span><span class="sxs-lookup"><span data-stu-id="0b916-817">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="0b916-818">ローカリゼーション ミドルウェアを使用して要求の現在のカルチャを確立する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0b916-818">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="0b916-819"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> を使用し、ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="0b916-819">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="0b916-820">ミドルウェア フィルターは、フィルター パイプラインのリソース フィルターと同じステージ (モデル バインドの前、残りのパイプラインの後) で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0b916-820">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="0b916-821">次の操作</span><span class="sxs-lookup"><span data-stu-id="0b916-821">Next actions</span></span>

* <span data-ttu-id="0b916-822">[Razor Pages のフィルター メソッド](xref:razor-pages/filter)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0b916-822">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="0b916-823">フィルターを試すには、[GitHub のサンプルをダウンロードして、テストおよび変更を行います](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="0b916-823">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
