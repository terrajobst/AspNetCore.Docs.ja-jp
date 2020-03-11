---
title: ASP.NET Core フィルター
author: Rick-Anderson
description: フィルターのしくみと ASP.NET Core でそれを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 03335811766ea3a1455901199863c6da0e35f7e4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653342"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="4cd37-103">ASP.NET Core フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4cd37-104">作成者: [Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4cd37-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4cd37-105">ASP.NET Core で*フィルター*を使用すると、要求処理パイプラインの特定のステージの前または後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="4cd37-106">組み込みのフィルターでは次のようなタスクが処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="4cd37-107">許可 (ユーザーに許可が与えられていないリソースの場合、アクセスを禁止する)。</span><span class="sxs-lookup"><span data-stu-id="4cd37-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="4cd37-108">応答キャッシュ (要求パイプラインを迂回し、キャッシュされている応答を返す)。</span><span class="sxs-lookup"><span data-stu-id="4cd37-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="4cd37-109">横断的な問題を処理するカスタム フィルターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="4cd37-110">横断的な問題の例には、エラー処理、キャッシュ、構成、認証、ログなどがあります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="4cd37-111">フィルターにより、コードの重複が回避されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="4cd37-112">たとえば、エラー処理例外フィルターではエラー処理を統合できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="4cd37-113">このドキュメントは、Razor Pages、API コントローラー、ビューのあるコントローラーに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="4cd37-114">フィルターは、[Razor コンポーネント](xref:blazor/components)では直接機能しません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="4cd37-115">次の場合、フィルターは間接的にコンポーネントに影響するのみです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="4cd37-116">コンポーネントがページまたはビューに埋め込まれている。</span><span class="sxs-lookup"><span data-stu-id="4cd37-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="4cd37-117">ページまたはコントローラー/ビューでフィルターが使用されている。</span><span class="sxs-lookup"><span data-stu-id="4cd37-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="4cd37-118">[サンプルを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4cd37-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="4cd37-119">フィルターのしくみ</span><span class="sxs-lookup"><span data-stu-id="4cd37-119">How filters work</span></span>

<span data-ttu-id="4cd37-120">*ASP.NET Core のアクション呼び出しパイプライン*内で実行されるフィルターは、*フィルター パイプライン*と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="4cd37-121">フィルター パイプラインは、ASP.NET Core が実行するアクションを選択した後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求は、他のミドルウェア、ルーティング ミドルウェア、アクション選択、およびアクション呼び出しパイプラインを通じて処理されます。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="4cd37-124">フィルターの種類</span><span class="sxs-lookup"><span data-stu-id="4cd37-124">Filter types</span></span>

<span data-ttu-id="4cd37-125">フィルターの種類はそれぞれ、フィルター パイプラインの異なるステージで実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="4cd37-126">[承認フィルター](#authorization-filters)は、最初に実行され、ユーザーが要求に対して承認されているかどうかを判断するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="4cd37-127">承認フィルターでは、要求が承認されていない場合、パイプラインがショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="4cd37-128">[リソース フィルター](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="4cd37-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="4cd37-129">承認後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-129">Run after authorization.</span></span>  
  * <span data-ttu-id="4cd37-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> では、残りのフィルター パイプラインの前にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="4cd37-131">たとえば、`OnResourceExecuting` では、モデル バインディングの前にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="4cd37-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> では、残りのパイプラインの完了後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="4cd37-133">[アクション フィルター](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="4cd37-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="4cd37-134">アクション メソッドが呼び出される直前と直後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="4cd37-135">アクションに渡される引数を変更できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="4cd37-136">アクションから返された結果を変更できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="4cd37-137">Razor Pages ではサポート**されていません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="4cd37-138">[例外フィルター](#exception-filters)では、応答本文への書き込みが行われる前に発生する未処理の例外にグローバル ポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="4cd37-139">[結果フィルター](#result-filters)では、アクション結果の実行の直前と直後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="4cd37-140">アクション メソッドが正常に実行された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="4cd37-141">ビューまたはフォーマッタ実行を取り囲む必要があるロジックに便利です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="4cd37-142">フィルターの種類がフィルター パイプラインでどのように連携しているかを、次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求は、承認フィルター、リソース フィルター、モデル バインド、アクション フィルター、アクションの実行とアクション結果の変換、例外フィルター、結果フィルター、結果の実行を介して処理されます。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="4cd37-145">実装</span><span class="sxs-lookup"><span data-stu-id="4cd37-145">Implementation</span></span>

<span data-ttu-id="4cd37-146">フィルターは、異なるインターフェイス定義を介して、同期と非同期の実装をサポートします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="4cd37-147">同期フィルターでは、そのパイプライン ステージの前と後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="4cd37-148">たとえば、<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> はアクション メソッドの呼び出し前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="4cd37-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> は、アクション メソッドが戻った後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4cd37-150">非同期フィルターでは、`On-Stage-ExecutionAsync` メソッドが定義されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="4cd37-151">例: <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*></span><span class="sxs-lookup"><span data-stu-id="4cd37-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="4cd37-152">上記のコードでは、`SampleAsyncActionFilter` にアクション メソッドを実行する <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="4cd37-153">複数のフィルター ステージ</span><span class="sxs-lookup"><span data-stu-id="4cd37-153">Multiple filter stages</span></span>

<span data-ttu-id="4cd37-154">複数のフィルター ステージのためのインターフェイスを 1 つのクラスで実装できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="4cd37-155">たとえば、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> クラスでは次のものが実装されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="4cd37-156">同期: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="4cd37-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="4cd37-157">非同期: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4cd37-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="4cd37-158">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="4cd37-159">ランタイムは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="4cd37-160">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="4cd37-161">非同期インターフェイスと同期インターフェイスの両方が 1 つのクラスで実装される場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="4cd37-162"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> などの抽象クラスを使用する場合は、同期メソッドのみをオーバーライドするか、フィルターの種類ごとに非同期メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="4cd37-163">組み込みのフィルター属性</span><span class="sxs-lookup"><span data-stu-id="4cd37-163">Built-in filter attributes</span></span>

<span data-ttu-id="4cd37-164">ASP.NET Core には、サブクラスを作成したり、カスタマイズしたりできる組み込みの属性ベースのフィルターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="4cd37-165">たとえば、次の結果フィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="4cd37-166">上記の例のように、属性によってフィルターは引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="4cd37-167">`AddHeaderAttribute` をコントローラーまたはアクション メソッドに適用し、HTTP ヘッダーの名前と値を指定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="4cd37-168">[ブラウザー開発者ツール](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) などのツールを使用して、ヘッダーを調べます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="4cd37-169">**[応答ヘッダー]** の下に `author: Rick Anderson` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="4cd37-170">次のコードでは、次のことを行う `ActionFilterAttribute` が実装されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="4cd37-171">構成システムからタイトルと名前を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="4cd37-172">前のサンプルとは異なり、次のコードでは、フィルター パラメーターをコードに追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="4cd37-173">応答ヘッダーにタイトルと名前を追加します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4cd37-174">構成オプションは、[構成システム](xref:fundamentals/configuration/index)から[オプション パターン](xref:fundamentals/configuration/options)を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="4cd37-175">たとえば、*appsettings.json* ファイルから、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="4cd37-176">`StartUp.ConfigureServices`で、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="4cd37-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="4cd37-177">`PositionOptions` 構成領域を使用して `"Position"` クラスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="4cd37-178">`MyActionFilterAttribute` をサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="4cd37-179">次のコードは `PositionOptions` クラスを示しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="4cd37-180">次のコードでは、`MyActionFilterAttribute` メソッドに `Index2` が適用されています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="4cd37-181">**エンドポイントが呼び出されると、** [応答ヘッダー]`author: Rick Anderson` の下に `Editor: Joe Smith` および `Sample/Index2` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="4cd37-182">次のコードでは、`MyActionFilterAttribute` と `AddHeaderAttribute` が Razor ページに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4cd37-183">Razor ページ ハンドラー メソッドにフィルターを適用することはできません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="4cd37-184">これらは、Razor ページ モデルに適用するか、グローバルに適用することが可能です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="4cd37-185">フィルター インターフェイスのいくつかには対応する属性があり、カスタムの実装に基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="4cd37-186">フィルター属性:</span><span class="sxs-lookup"><span data-stu-id="4cd37-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="4cd37-187">フィルターのスコープと実行の順序</span><span class="sxs-lookup"><span data-stu-id="4cd37-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="4cd37-188">フィルターは、3 つの*スコープ*のいずれかでパイプラインに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="4cd37-189">コントローラー アクションでの属性の使用。</span><span class="sxs-lookup"><span data-stu-id="4cd37-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="4cd37-190">フィルター属性を Razor Pages ハンドラー メソッドに適用することはできません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="4cd37-191">コントローラーまたは Razor ページでの属性の使用。</span><span class="sxs-lookup"><span data-stu-id="4cd37-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="4cd37-192">次のコードのように、すべてのコントローラー、アクション、および Razor Pages に対してグローバルに:</span><span class="sxs-lookup"><span data-stu-id="4cd37-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="4cd37-193">実行の既定の順序</span><span class="sxs-lookup"><span data-stu-id="4cd37-193">Default order of execution</span></span>

<span data-ttu-id="4cd37-194">パイプラインの特定のステージに対して複数のフィルターがある場合に、スコープがフィルターの実行の既定の順序を決定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="4cd37-195">グローバル フィルターがクラス フィルターを囲み、クラス フィルターがメソッド フィルターを囲みます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="4cd37-196">フィルターの入れ子の結果として、フィルターの *after* コードが *before* コードと逆の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="4cd37-197">フィルター シーケンス:</span><span class="sxs-lookup"><span data-stu-id="4cd37-197">The filter sequence:</span></span>

* <span data-ttu-id="4cd37-198">グローバル フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="4cd37-199">コントローラーおよび Razor ページ フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="4cd37-200">アクション メソッド フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="4cd37-201">アクション メソッド フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="4cd37-202">コントローラーおよび Razor ページ フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="4cd37-203">グローバル フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="4cd37-204">次の例は、同期アクション フィルターに対してフィルター メソッドが呼び出される順序を示しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="4cd37-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="4cd37-205">Sequence</span></span> | <span data-ttu-id="4cd37-206">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="4cd37-206">Filter scope</span></span> | <span data-ttu-id="4cd37-207">Filter メソッド</span><span class="sxs-lookup"><span data-stu-id="4cd37-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="4cd37-208">1</span><span class="sxs-lookup"><span data-stu-id="4cd37-208">1</span></span> | <span data-ttu-id="4cd37-209">グローバル</span><span class="sxs-lookup"><span data-stu-id="4cd37-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4cd37-210">2</span><span class="sxs-lookup"><span data-stu-id="4cd37-210">2</span></span> | <span data-ttu-id="4cd37-211">コントローラーまたは Razor ページ</span><span class="sxs-lookup"><span data-stu-id="4cd37-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="4cd37-212">3</span><span class="sxs-lookup"><span data-stu-id="4cd37-212">3</span></span> | <span data-ttu-id="4cd37-213">方法</span><span class="sxs-lookup"><span data-stu-id="4cd37-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4cd37-214">4</span><span class="sxs-lookup"><span data-stu-id="4cd37-214">4</span></span> | <span data-ttu-id="4cd37-215">方法</span><span class="sxs-lookup"><span data-stu-id="4cd37-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4cd37-216">5</span><span class="sxs-lookup"><span data-stu-id="4cd37-216">5</span></span> | <span data-ttu-id="4cd37-217">コントローラーまたは Razor ページ</span><span class="sxs-lookup"><span data-stu-id="4cd37-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4cd37-218">6</span><span class="sxs-lookup"><span data-stu-id="4cd37-218">6</span></span> | <span data-ttu-id="4cd37-219">グローバル</span><span class="sxs-lookup"><span data-stu-id="4cd37-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="4cd37-220">コントローラー レベルのフィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-220">Controller level filters</span></span>

<span data-ttu-id="4cd37-221"><xref:Microsoft.AspNetCore.Mvc.Controller> 基底クラスから継承するすべてのコントローラーに、[Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)、[Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="4cd37-222">これらのメソッド:</span><span class="sxs-lookup"><span data-stu-id="4cd37-222">These methods:</span></span>

* <span data-ttu-id="4cd37-223">特定のアクションに対して実行されるフィルターをラップします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="4cd37-224">`OnActionExecuting` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="4cd37-225">`OnActionExecuted` は、あらゆるアクション フィルターの後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="4cd37-226">`OnActionExecutionAsync` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="4cd37-227">`next` の後のフィルターのコードは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="4cd37-228">たとえば、ダウンロード サンプルで、`MySampleActionFilter` は起動中、グローバルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="4cd37-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="4cd37-229">The `TestController`:</span></span>

* <span data-ttu-id="4cd37-230">`SampleActionFilterAttribute` (`[SampleActionFilter]`) が `FilterTest2` アクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="4cd37-231">`OnActionExecuting` と `OnActionExecuted` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="4cd37-232">`https://localhost:5001/Test2/FilterTest2` に移動すると、次のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="4cd37-233">コントローラー レベルのフィルターでは、[Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) プロパティが `int.MinValue` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="4cd37-234">コントローラー レベルのフィルターは、メソッドにフィルターが適用された後に実行されるように設定することは**できません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="4cd37-235">順序については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-235">Order is explained in the next section.</span></span>

<span data-ttu-id="4cd37-236">Razor Pages の場合、「[フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="4cd37-237">既定の順序のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="4cd37-237">Overriding the default order</span></span>

<span data-ttu-id="4cd37-238"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> を実装することで、実行の既定の順序をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="4cd37-239">`IOrderedFilter` により、実行の順序を決定するために、スコープよりも優先される <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="4cd37-240">より低い `Order` 値を持つフィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="4cd37-241">より高い *の値を持つフィルターのそれよりも前に*before`Order` コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="4cd37-242">より高い *の値を持つフィルターのそれよりも後に*after`Order` コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="4cd37-243">`Order` プロパティは、次のコンストラクター パラメーターで設定できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="4cd37-244">次のコントローラーの 2 つのアクションフィルターについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="4cd37-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="4cd37-245">グローバル フィルターが次のように `StartUp.ConfigureServices` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="4cd37-246">3 つのフィルターは、次の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="4cd37-247">フィルターの実行順序を決定するときに、`Order` プロパティによりスコープがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="4cd37-248">最初に順序でフィルターが並べ替えられ、次に同じ順位の優先度を決めるためにスコープが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="4cd37-249">組み込みのフィルターはすべて `IOrderedFilter` を実装し、既定の `Order` 値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="4cd37-250">既に説明したように、コントローラー レベルのフィルターでは、[Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) プロパティが `int.MinValue` に設定されます。組み込みのフィルターの場合は、`Order` が 0 以外の値に設定されている場合を除き、スコープによって順序が決定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="4cd37-251">上記のコードにおいて、`MySampleActionFilter` にはグローバル スコープがあるため、コントローラー スコープを持つ `MyAction2FilterAttribute` の前で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="4cd37-252">`MyAction2FilterAttribute` を最初に実行するようにするには、`int.MinValue` に対して順序を設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="4cd37-253">グローバル フィルター `MySampleActionFilter` を最初に実行するようにするには、次のように `Order` を `int.MinValue` に設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="4cd37-254">キャンセルとショートサーキット</span><span class="sxs-lookup"><span data-stu-id="4cd37-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="4cd37-255">フィルター メソッドに提供される <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> パラメーターで <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> プロパティを設定することで、フィルター パイプラインをショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="4cd37-256">たとえば、次のリソース フィルターは、パイプラインの残りの部分が実行されるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4cd37-257">次のコードでは、`ShortCircuitingResourceFilter` と `AddHeader` の両方のフィルターが `SomeResource` アクション メソッドをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="4cd37-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="4cd37-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="4cd37-259">これはリソース フィルターであり、`AddHeader` はアクション フィルターであるため、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="4cd37-260">パイプラインの残りの部分は迂回されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="4cd37-261">そのため、`AddHeader` アクションの場合、`SomeResource` フィルターが実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="4cd37-262">`ShortCircuitingResourceFilter` が最初に実行された場合は、両方のフィルターがアクション メソッド レベルで適用されると、この動作が同じになります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="4cd37-263">そのフィルターの種類が原因で、あるいは `ShortCircuitingResourceFilter` プロパティの明示的な使用により、`Order` が最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="4cd37-264">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="4cd37-264">Dependency injection</span></span>

<span data-ttu-id="4cd37-265">フィルターは種類ごとまたはインスタンスごとに追加できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="4cd37-266">インスタンスが追加される場合、そのインスタンスはすべての要求に対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="4cd37-267">種類が追加される場合、それは種類でアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="4cd37-268">種類でアクティブ化されるフィルターの意味:</span><span class="sxs-lookup"><span data-stu-id="4cd37-268">A type-activated filter means:</span></span>

* <span data-ttu-id="4cd37-269">インスタンスは要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-269">An instance is created for each request.</span></span>
* <span data-ttu-id="4cd37-270">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="4cd37-271">属性として実装され、コントローラー クラスまたはアクション メソッドに直接追加されるフィルターは、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって提供されるコンストラクターの依存関係を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="4cd37-272">コンストラクターの依存関係は、次の理由から DI によって与えられません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="4cd37-273">属性には、適用される場所で提供される独自のコンストラクター パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="4cd37-274">これは、属性のしくみの制限です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="4cd37-275">次のフィルターでは、DI から提供されるコンストラクターの依存関係がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="4cd37-276">属性に実装された <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="4cd37-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="4cd37-277">上記のフィルターは、コントローラーまたはアクション メソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="4cd37-278">ロガーは DI から利用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-278">Loggers are available from DI.</span></span> <span data-ttu-id="4cd37-279">ただし、ログ目的でのみフィルターを作成し、使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="4cd37-280">[組み込みフレームワークのログ機能](xref:fundamentals/logging/index)で通常、ログ記録に必要なものが与えられます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="4cd37-281">フィルターに追加されるログ記録:</span><span class="sxs-lookup"><span data-stu-id="4cd37-281">Logging added to filters:</span></span>

* <span data-ttu-id="4cd37-282">ビジネス ドメインの懸念事項やフィルターに固有の動作に焦点を合わせます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="4cd37-283">アクションやその他のフレームワーク イベントはログに記録**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="4cd37-284">組み込みのフィルターでは、アクションとフレームワーク イベントが記録されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="4cd37-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4cd37-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="4cd37-286">サービス フィルターの実装の種類は `ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="4cd37-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は DI からフィルターのインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="4cd37-288">このコードでは、`AddHeaderResultServiceFilter` が示されています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4cd37-289">次のコードでは、`AddHeaderResultServiceFilter` が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="4cd37-290">次のコードでは、`ServiceFilter` 属性により、DI から `AddHeaderResultServiceFilter` フィルターのインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="4cd37-291">`ServiceFilterAttribute` を使用するときに、[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="4cd37-292">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4cd37-293">ASP.NET Core ランタイムで保証されないこと:</span><span class="sxs-lookup"><span data-stu-id="4cd37-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="4cd37-294">フィルターのインスタンスが 1 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="4cd37-295">フィルターが後の時点で、DI コンテナーから再要求されることはありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="4cd37-296">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="4cd37-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4cd37-298">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4cd37-299">`CreateInstance` により、DI から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="4cd37-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4cd37-300">TypeFilterAttribute</span></span>

<span data-ttu-id="4cd37-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は<xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> と似ていますが、その型は DI コンテナーから直接解決されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="4cd37-302"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> を使って型をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="4cd37-303">`TypeFilterAttribute` 型は DI コンテナーから直接解決されないためです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="4cd37-304">`TypeFilterAttribute` を利用して参照される型は、DI コンテナーに登録する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="4cd37-305">DI コンテナーによって依存関係が満たされています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="4cd37-306">`TypeFilterAttribute` は必要に応じて、型のコンストラクター引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="4cd37-307">`TypeFilterAttribute` を使用するときに、[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="4cd37-308">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4cd37-309">ASP.NET Core ランタイムでは、フィルターの 1 インスタンスが作成されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="4cd37-310">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="4cd37-311">次の例は、`TypeFilterAttribute` を使用して、型に引数を渡す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="4cd37-312">承認フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-312">Authorization filters</span></span>

<span data-ttu-id="4cd37-313">承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-313">Authorization filters:</span></span>

* <span data-ttu-id="4cd37-314">フィルター パイプライン内で実行される最初のフィルターです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="4cd37-315">アクション メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-315">Control access to action methods.</span></span>
* <span data-ttu-id="4cd37-316">before メソッドが与えられ、after メソッドは与えられません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="4cd37-317">カスタム承認フィルターには、カスタム承認フレームワークが必要です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="4cd37-318">カスタム フィルターを記述するよりも、独自の承認ポリシーを構成するか、カスタム承認ポリシーを記述することを選びます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="4cd37-319">組み込み承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="4cd37-320">承認システムを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-320">Calls the authorization system.</span></span>
* <span data-ttu-id="4cd37-321">要求は承認されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-321">Does not authorize requests.</span></span>

<span data-ttu-id="4cd37-322">承認フィルター内で例外をスロー**しません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="4cd37-323">例外は処理されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-323">The exception will not be handled.</span></span>
* <span data-ttu-id="4cd37-324">例外フィルターで例外が処理されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="4cd37-325">承認フィルターで例外が発生した場合、チャレンジ発行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="4cd37-326">承認の詳細については、[こちら](xref:security/authorization/introduction)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="4cd37-327">リソース フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-327">Resource filters</span></span>

<span data-ttu-id="4cd37-328">リソース フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-328">Resource filters:</span></span>

* <span data-ttu-id="4cd37-329"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="4cd37-330">実行により、ほとんどのフィルター パイプラインがラップされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="4cd37-331">[承認フィルター](#authorization-filters)のみ、リソース フィルターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="4cd37-332">リソース フィルターは、ほとんどのパイプラインをショートサーキットする目的で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="4cd37-333">たとえば、キャッシュ フィルターにより、キャッシュ ヒットがあるとき、残りのパイプラインを回避できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="4cd37-334">リソース フィルターの例:</span><span class="sxs-lookup"><span data-stu-id="4cd37-334">Resource filter examples:</span></span>

* <span data-ttu-id="4cd37-335">前に示した[ショートサーキットするリソース フィルター](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="4cd37-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="4cd37-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="4cd37-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="4cd37-337">モデル バインドがフォーム データにアクセスすることを禁止します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="4cd37-338">メモリにフォーム データが読み込まれないようにする目的で大きなファイルのアップロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="4cd37-339">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-339">Action filters</span></span>

<span data-ttu-id="4cd37-340">アクション フィルターは Razor Pages に適用**されません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="4cd37-341">Razor Pages では <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="4cd37-342">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="4cd37-343">アクション フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-343">Action filters:</span></span>

* <span data-ttu-id="4cd37-344"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="4cd37-345">この実行はアクション メソッドの実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="4cd37-346">次のコードは、サンプル アクション フィルターを示しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4cd37-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> では次のプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="4cd37-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - アクション メソッドへの入力の読み取りを有効にします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="4cd37-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - コントローラー インスタンスを操作できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="4cd37-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` を設定すると、アクション メソッドと後続のアクション フィルターの実行がショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="4cd37-351">アクション メソッドで例外をスローする:</span><span class="sxs-lookup"><span data-stu-id="4cd37-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="4cd37-352">後続のフィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="4cd37-353">`Result` の設定とは異なり、結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4cd37-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> は、`Controller` と `Result` に加え、次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="4cd37-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 別のフィルターによってアクションの実行がショートサーキットされた場合は、true になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4cd37-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - アクションまたは前に実行されたアクション フィルターが例外をスローした場合は、null 以外になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="4cd37-357">このプロパティを null に設定する:</span><span class="sxs-lookup"><span data-stu-id="4cd37-357">Setting this property to null:</span></span>

  * <span data-ttu-id="4cd37-358">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="4cd37-359">`Result` は、アクション メソッドから返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="4cd37-360">`IAsyncActionFilter` の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> の呼び出しによって:</span><span class="sxs-lookup"><span data-stu-id="4cd37-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="4cd37-361">後続のすべてのアクション フィルターとアクション メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="4cd37-362">`ActionExecutedContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="4cd37-363">ショートサーキットするには、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> を結果インスタンスに割り当てます。`next` (`ActionExecutionDelegate`) は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="4cd37-364">このフレームワークからは、サブクラス化できる抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="4cd37-365">`OnActionExecuting` アクション フィルターを次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="4cd37-366">モデルの状態を検証します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-366">Validate model state.</span></span>
* <span data-ttu-id="4cd37-367">状態が有効でない場合は、エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="4cd37-368">`OnActionExecuted` メソッドは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="4cd37-369">また、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> プロパティを介してアクションの結果を表示したり、操作したりできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="4cd37-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> は、アクションの実行が別のフィルターによってショートサーキットされた場合、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4cd37-371">アクションまたは後続のアクション フィルターが例外をスローした場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="4cd37-372">`Exception` を null に設定すると:</span><span class="sxs-lookup"><span data-stu-id="4cd37-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="4cd37-373">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="4cd37-374">`ActionExecutedContext.Result` は、アクション メソッドから通常どおり返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="4cd37-375">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-375">Exception filters</span></span>

<span data-ttu-id="4cd37-376">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-376">Exception filters:</span></span>

* <span data-ttu-id="4cd37-377"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="4cd37-378">共通のエラー処理ポリシーを実装する目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="4cd37-379">次の例外フィルターのサンプルでは、カスタムのエラー ビューを使用して、アプリの開発中に発生する例外に関する詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="4cd37-380">次のコードでは、例外フィルターをテストします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="4cd37-381">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-381">Exception filters:</span></span>

* <span data-ttu-id="4cd37-382">before イベントと after イベントが与えられません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-382">Don't have before and after events.</span></span>
* <span data-ttu-id="4cd37-383"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="4cd37-384">Razor Page またはコントローラーの作成、[モデル バインド](xref:mvc/models/model-binding)、アクション フィルター、またはアクション メソッドで発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="4cd37-385">リソース フィルター、結果フィルター、または MVC 結果の実行で発生した例外はキャッチ**しません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="4cd37-386">例外を処理するには、<xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> プロパティを `true` に設定するか、応答を記述します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="4cd37-387">これにより、例外の伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-387">This stops propagation of the exception.</span></span> <span data-ttu-id="4cd37-388">例外フィルターで例外を "成功" に変えることはできません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="4cd37-389">それができるのは、アクション フィルターだけです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-389">Only an action filter can do that.</span></span>

<span data-ttu-id="4cd37-390">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-390">Exception filters:</span></span>

* <span data-ttu-id="4cd37-391">アクション内で発生する例外のトラップに適しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="4cd37-392">エラー処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="4cd37-393">例外処理にはミドルウェアを選択してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="4cd37-394">呼び出されたアクション メソッドに基づいてエラー処理が*異なる*状況でのみ例外フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="4cd37-395">たとえば、アプリには、API エンドポイントとビュー/HTML の両方に対するアクション メソッドがある場合があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="4cd37-396">API エンドポイントは、JSON としてのエラー情報を返す可能性がある一方で、ビュー ベースのアクションがエラー ページを HTML として返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="4cd37-397">結果フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-397">Result filters</span></span>

<span data-ttu-id="4cd37-398">結果フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-398">Result filters:</span></span>

* <span data-ttu-id="4cd37-399">インターフェイスを実装します:</span><span class="sxs-lookup"><span data-stu-id="4cd37-399">Implement an interface:</span></span>
  * <span data-ttu-id="4cd37-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4cd37-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="4cd37-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="4cd37-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="4cd37-402">この実行はアクション結果の実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="4cd37-403">IResultFilter および IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="4cd37-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="4cd37-404">次は、HTTP ヘッダーを追加する結果フィルターのコードです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4cd37-405">実行されている結果の種類は、アクションに依存します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="4cd37-406">ビューを返すアクションには、実行されている <xref:Microsoft.AspNetCore.Mvc.ViewResult> の一部として、すべての razor 処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="4cd37-407">API メソッドは、結果の実行の一部としていくつかのシリアル化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="4cd37-408">[アクション結果](xref:mvc/controllers/actions)に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="4cd37-409">結果フィルターは、アクションまたはアクション フィルターによってアクション結果を生成される場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="4cd37-410">結果フィルターは、次の場合には実行されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="4cd37-411">承認フィルターまたはリソース フィルターによって、パイプラインがショートサーキットされる。</span><span class="sxs-lookup"><span data-stu-id="4cd37-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="4cd37-412">アクションの結果を生成することで、例外フィルターによって例外が処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="4cd37-413"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> メソッドは、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> を `true` に設定することで、アクションの結果と後続の結果フィルターの実行をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="4cd37-414">ショートサーキットする場合は、空の応答が生成されないように応答オブジェクトに記述します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="4cd37-415">`IResultFilter.OnResultExecuting` での例外のスロー:</span><span class="sxs-lookup"><span data-stu-id="4cd37-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="4cd37-416">アクション結果と後続フィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="4cd37-417">結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4cd37-418"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> メソッドが実行されたとき、おそらく応答は既にクライアントに送信されています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="4cd37-419">応答が既にクライアントに送信されていた場合は、それを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="4cd37-420">別のフィルターによってアクションの結果の実行がショートサーキットされた場合、`ResultExecutedContext.Canceled` は `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="4cd37-421">アクションの結果または後続の結果フィルターが例外をスローした場合、`ResultExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="4cd37-422">`Exception` を null に設定すると、例外を効果的に処理し、パイプラインの後方で例外が再スローされるのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="4cd37-423">結果フィルターの例外を処理するとき、応答にデータを書き込む目的で信頼できる方法はありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="4cd37-424">アクションの結果により例外がスローされるとき、ヘッダーがクライアントにフラッシュされている場合、エラー コードを送信する目的で信頼できるメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="4cd37-425"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> の場合、`await next` で <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> を呼び出すと、後続のすべての結果フィルターとアクションの結果が実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="4cd37-426">ショートサーキットするには、[ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) を `true` に設定し、`ResultExecutionDelegate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="4cd37-427">このフレームワークからは、サブクラス化できる抽象 `ResultFilterAttribute` が与えられます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="4cd37-428">前に示した [AddHeaderAttribute](#add-header-attribute) クラスは、結果フィルター属性の一例です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="4cd37-429">IAlwaysRunResultFilter および IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="4cd37-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="4cd37-430"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> および <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> インターフェイスでは、すべてのアクションの結果に対して実行される <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> の実装が宣言されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="4cd37-431">これには、以下によって生成されるアクションの結果が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-431">This includes action results produced by:</span></span>

* <span data-ttu-id="4cd37-432">ショートサーキットが行われる承認フィルターとリソース フィルター。</span><span class="sxs-lookup"><span data-stu-id="4cd37-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="4cd37-433">例外フィルター。</span><span class="sxs-lookup"><span data-stu-id="4cd37-433">Exception filters.</span></span>

<span data-ttu-id="4cd37-434">たとえば、次のフィルターは常に実行され、コンテンツ ネゴシエーションが失敗した場合に "<xref:Microsoft.AspNetCore.Mvc.ObjectResult>422 処理不可エンティティ *" 状態コードを使ってアクションの結果 (* ) を設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="4cd37-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="4cd37-435">IFilterFactory</span></span>

<span data-ttu-id="4cd37-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="4cd37-437">そのため、`IFilterFactory` インスタンスはフィルター パイプライン内の任意の場所で `IFilterMetadata` インスタンスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="4cd37-438">ランタイムでは、フィルターを呼び出す準備をする際、`IFilterFactory` へのキャストが試行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="4cd37-439">そのキャストが成功した場合、呼び出される <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> インスタンスを作成するために `IFilterMetadata` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="4cd37-440">これにより、アプリの起動時に正確なフィルター パイプラインを明示的に設定する必要がないため、柔軟なデザインが可能になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="4cd37-441">フィルターを作成するための別の方法として、カスタムの属性の実装で `IFilterFactory` を実装できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="4cd37-442">フィルターは次のコードで適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="4cd37-443">[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)を実行することで、上記のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="4cd37-444">F12 開発者ツールを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="4cd37-445">`https://localhost:5001/Sample/HeaderWithFactory` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="4cd37-446">F12 開発者ツールでは、サンプル コードによって追加された次の応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="4cd37-447">**author:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="4cd37-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="4cd37-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="4cd37-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="4cd37-449">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="4cd37-449">**internal:** `My header`</span></span>

<span data-ttu-id="4cd37-450">上記のコードでは、**internal:** `My header` という応答ヘッダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="4cd37-451">属性に実装された IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="4cd37-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="4cd37-452">`IFilterFactory` を実装するフィルターは次のようなフィルターに便利です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="4cd37-453">パラメーターの引き渡しを必要としません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="4cd37-454">DI で満たす必要があるコンストラクター依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="4cd37-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4cd37-456">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4cd37-457">`CreateInstance` により、サービス コンテナー (DI) から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="4cd37-458">次のコードでは、`[SampleActionFilter]` を適用する 3 つの手法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="4cd37-459">上記のコードでは、`[SampleActionFilter]` を適用する方法としては、`SampleActionFilter` でメソッドを装飾する方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="4cd37-460">フィルター パイプラインでのミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="4cd37-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="4cd37-461">リソース フィルターは、パイプラインの後方で登場するすべての実行を囲む点で、[ミドルウェア](xref:fundamentals/middleware/index)のように機能します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="4cd37-462">ただし、フィルターはランタイムの一部である点がミドルウェアとは異なります。つまり、それらはコンテキストとコンストラクトにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="4cd37-463">ミドルウェアをフィルターとして使用するには、フィルター パイプラインに挿入するミドルウェアを指定する `Configure` メソッドを使用して型を作成します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="4cd37-464">ローカリゼーション ミドルウェアを使用して要求の現在のカルチャを確立する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="4cd37-465"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> を使用し、ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="4cd37-466">ミドルウェア フィルターは、フィルター パイプラインのリソース フィルターと同じステージ (モデル バインドの前、残りのパイプラインの後) で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="4cd37-467">次の操作</span><span class="sxs-lookup"><span data-stu-id="4cd37-467">Next actions</span></span>

* <span data-ttu-id="4cd37-468">[Razor Pages のフィルター メソッド](xref:razor-pages/filter)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="4cd37-469">フィルターを試すには、[GitHub のサンプルをダウンロードして、テストおよび変更を行います](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)。</span><span class="sxs-lookup"><span data-stu-id="4cd37-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4cd37-470">作成者: [Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4cd37-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4cd37-471">ASP.NET Core で*フィルター*を使用すると、要求処理パイプラインの特定のステージの前または後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="4cd37-472">組み込みのフィルターでは次のようなタスクが処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="4cd37-473">許可 (ユーザーに許可が与えられていないリソースの場合、アクセスを禁止する)。</span><span class="sxs-lookup"><span data-stu-id="4cd37-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="4cd37-474">応答キャッシュ (要求パイプラインを迂回し、キャッシュされている応答を返す)。</span><span class="sxs-lookup"><span data-stu-id="4cd37-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="4cd37-475">横断的な問題を処理するカスタム フィルターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="4cd37-476">横断的な問題の例には、エラー処理、キャッシュ、構成、認証、ログなどがあります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="4cd37-477">フィルターにより、コードの重複が回避されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="4cd37-478">たとえば、エラー処理例外フィルターではエラー処理を統合できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="4cd37-479">このドキュメントは、Razor Pages、API コントローラー、ビューのあるコントローラーに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="4cd37-480">[サンプルを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4cd37-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="4cd37-481">フィルターのしくみ</span><span class="sxs-lookup"><span data-stu-id="4cd37-481">How filters work</span></span>

<span data-ttu-id="4cd37-482">*ASP.NET Core のアクション呼び出しパイプライン*内で実行されるフィルターは、*フィルター パイプライン*と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="4cd37-483">フィルター パイプラインは、ASP.NET Core が実行するアクションを選択した後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求は、他のミドルウェア、ルーティング ミドルウェア、アクション選択、ASP.NET Core のアクション呼び出しパイプラインを通じて処理されます。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="4cd37-486">フィルターの種類</span><span class="sxs-lookup"><span data-stu-id="4cd37-486">Filter types</span></span>

<span data-ttu-id="4cd37-487">フィルターの種類はそれぞれ、フィルター パイプラインの異なるステージで実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="4cd37-488">[承認フィルター](#authorization-filters)は、最初に実行され、ユーザーが要求に対して承認されているかどうかを判断するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="4cd37-489">承認フィルターでは、要求が承認されていない場合、パイプラインがショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="4cd37-490">[リソース フィルター](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="4cd37-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="4cd37-491">承認後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-491">Run after authorization.</span></span>  
  * <span data-ttu-id="4cd37-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> では、残りのフィルター パイプラインの前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="4cd37-493">たとえば、`OnResourceExecuting` では、モデル バインディングの前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="4cd37-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> では、残りのパイプラインの完了後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="4cd37-495">[アクション フィルター](#action-filters)は、個々のアクション メソッドが呼び出される直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="4cd37-496">アクションに渡される引数とアクションから返される結果を操作するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="4cd37-497">アクション フィルターは Razor Pages ではサポート**されていません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="4cd37-498">[例外フィルター](#exception-filters)は、応答本文に何かが書き込まれる前に発生する未処理の例外にグローバル ポリシーを適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="4cd37-499">[結果フィルター](#result-filters)は、個々のアクション結果の実行の直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="4cd37-500">アクション メソッドが正常に実行された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="4cd37-501">ビューまたはフォーマッタ実行を取り囲む必要があるロジックに便利です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="4cd37-502">フィルターの種類がフィルター パイプラインでどのように連携しているかを、次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求は、承認フィルター、リソース フィルター、モデル バインド、アクション フィルター、アクションの実行とアクション結果の変換、例外フィルター、結果フィルター、結果の実行を介して処理されます。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="4cd37-505">実装</span><span class="sxs-lookup"><span data-stu-id="4cd37-505">Implementation</span></span>

<span data-ttu-id="4cd37-506">フィルターは、異なるインターフェイス定義を介して、同期と非同期の実装をサポートします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="4cd37-507">同期フィルターでは、パイプライン ステージの前 (`On-Stage-Executing`) と後 (`On-Stage-Executed`) にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="4cd37-508">たとえば、`OnActionExecuting` はアクション メソッドの呼び出し前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="4cd37-509">`OnActionExecuted` は、アクション メソッドが戻った後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4cd37-510">非同期フィルターにより `On-Stage-ExecutionAsync` メソッドが定義されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="4cd37-511">上記のコードでは、`SampleAsyncActionFilter` にアクション メソッドを実行する <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="4cd37-512">各 `On-Stage-ExecutionAsync` メソッドは、フィルターのパイプライン ステージを実行する `FilterType-ExecutionDelegate` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="4cd37-513">複数のフィルター ステージ</span><span class="sxs-lookup"><span data-stu-id="4cd37-513">Multiple filter stages</span></span>

<span data-ttu-id="4cd37-514">複数のフィルター ステージのためのインターフェイスを 1 つのクラスで実装できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="4cd37-515">たとえば、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> クラスは、`IActionFilter`、`IResultFilter`、およびそれらの非同期バージョンを実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="4cd37-516">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="4cd37-517">ランタイムは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="4cd37-518">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="4cd37-519">非同期インターフェイスと同期インターフェイスの両方が 1 つのクラスで実装される場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="4cd37-520"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> などの抽象クラスを使用する場合は、同期メソッドのみをオーバーライドするか、フィルターの種類ごとに非同期メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="4cd37-521">組み込みのフィルター属性</span><span class="sxs-lookup"><span data-stu-id="4cd37-521">Built-in filter attributes</span></span>

<span data-ttu-id="4cd37-522">ASP.NET Core には、サブクラスを作成したり、カスタマイズしたりできる組み込みの属性ベースのフィルターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="4cd37-523">たとえば、次の結果フィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="4cd37-524">上記の例のように、属性によってフィルターは引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="4cd37-525">`AddHeaderAttribute` をコントローラーまたはアクション メソッドに適用し、HTTP ヘッダーの名前と値を指定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="4cd37-526">フィルター インターフェイスのいくつかには対応する属性があり、カスタムの実装に基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="4cd37-527">フィルター属性:</span><span class="sxs-lookup"><span data-stu-id="4cd37-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="4cd37-528">フィルターのスコープと実行の順序</span><span class="sxs-lookup"><span data-stu-id="4cd37-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="4cd37-529">フィルターは、3 つの*スコープ*のいずれかでパイプラインに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="4cd37-530">アクションで属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="4cd37-531">コントローラーで属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="4cd37-532">次のコードのように、すべてのコントローラーとアクションに対してグローバルに:</span><span class="sxs-lookup"><span data-stu-id="4cd37-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="4cd37-533">上記のコードでは、[MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) コレクションを利用し、3 つのフィルターがグローバルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="4cd37-534">実行の既定の順序</span><span class="sxs-lookup"><span data-stu-id="4cd37-534">Default order of execution</span></span>

<span data-ttu-id="4cd37-535">"*同じ種類のフィルター*" が複数存在する場合は、スコープによって、フィルターの実行の既定の順序が決まります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="4cd37-536">グローバル フィルターによってクラス フィルターが囲まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-536">Global filters surround class filters.</span></span> <span data-ttu-id="4cd37-537">クラス フィルターによってメソッド フィルターが囲まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-537">Class filters surround method filters.</span></span>

<span data-ttu-id="4cd37-538">フィルターの入れ子の結果として、フィルターの *after* コードが *before* コードと逆の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="4cd37-539">フィルター シーケンス:</span><span class="sxs-lookup"><span data-stu-id="4cd37-539">The filter sequence:</span></span>

* <span data-ttu-id="4cd37-540">グローバル フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="4cd37-541">コントローラー フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="4cd37-542">アクション メソッド フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="4cd37-543">アクション メソッド フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="4cd37-544">コントローラー フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="4cd37-545">グローバル フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="4cd37-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="4cd37-546">次の例は、同期アクション フィルターに対してフィルター メソッドが呼び出される順序を示しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="4cd37-547">Sequence</span><span class="sxs-lookup"><span data-stu-id="4cd37-547">Sequence</span></span> | <span data-ttu-id="4cd37-548">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="4cd37-548">Filter scope</span></span> | <span data-ttu-id="4cd37-549">Filter メソッド</span><span class="sxs-lookup"><span data-stu-id="4cd37-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="4cd37-550">1</span><span class="sxs-lookup"><span data-stu-id="4cd37-550">1</span></span> | <span data-ttu-id="4cd37-551">グローバル</span><span class="sxs-lookup"><span data-stu-id="4cd37-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4cd37-552">2</span><span class="sxs-lookup"><span data-stu-id="4cd37-552">2</span></span> | <span data-ttu-id="4cd37-553">コントローラー</span><span class="sxs-lookup"><span data-stu-id="4cd37-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4cd37-554">3</span><span class="sxs-lookup"><span data-stu-id="4cd37-554">3</span></span> | <span data-ttu-id="4cd37-555">方法</span><span class="sxs-lookup"><span data-stu-id="4cd37-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4cd37-556">4</span><span class="sxs-lookup"><span data-stu-id="4cd37-556">4</span></span> | <span data-ttu-id="4cd37-557">方法</span><span class="sxs-lookup"><span data-stu-id="4cd37-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4cd37-558">5</span><span class="sxs-lookup"><span data-stu-id="4cd37-558">5</span></span> | <span data-ttu-id="4cd37-559">コントローラー</span><span class="sxs-lookup"><span data-stu-id="4cd37-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="4cd37-560">6</span><span class="sxs-lookup"><span data-stu-id="4cd37-560">6</span></span> | <span data-ttu-id="4cd37-561">グローバル</span><span class="sxs-lookup"><span data-stu-id="4cd37-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="4cd37-562">このシーケンスが示すもの:</span><span class="sxs-lookup"><span data-stu-id="4cd37-562">This sequence shows:</span></span>

* <span data-ttu-id="4cd37-563">メソッド フィルターは、コントローラー フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="4cd37-564">コントローラー フィルターは、グローバル フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="4cd37-565">コントローラーと Razor Page のレベル フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="4cd37-566"><xref:Microsoft.AspNetCore.Mvc.Controller> 基底クラスから継承するすべてのコントローラーに、[Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)、[Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="4cd37-567">これらのメソッド:</span><span class="sxs-lookup"><span data-stu-id="4cd37-567">These methods:</span></span>

* <span data-ttu-id="4cd37-568">特定のアクションに対して実行されるフィルターをラップします。</span><span class="sxs-lookup"><span data-stu-id="4cd37-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="4cd37-569">`OnActionExecuting` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="4cd37-570">`OnActionExecuted` は、あらゆるアクション フィルターの後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="4cd37-571">`OnActionExecutionAsync` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="4cd37-572">`next` の後のフィルターのコードは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="4cd37-573">たとえば、ダウンロード サンプルで、`MySampleActionFilter` は起動中、グローバルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="4cd37-574">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="4cd37-574">The `TestController`:</span></span>

* <span data-ttu-id="4cd37-575">`SampleActionFilterAttribute` (`[SampleActionFilter]`) が `FilterTest2` アクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="4cd37-576">`OnActionExecuting` と `OnActionExecuted` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="4cd37-577">`https://localhost:5001/Test/FilterTest2` に移動すると、次のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="4cd37-578">Razor Pages の場合、「[フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="4cd37-579">既定の順序のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="4cd37-579">Overriding the default order</span></span>

<span data-ttu-id="4cd37-580"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> を実装することで、実行の既定の順序をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="4cd37-581">`IOrderedFilter` により、実行の順序を決定するために、スコープよりも優先される <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="4cd37-582">より低い `Order` 値を持つフィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="4cd37-583">より高い *の値を持つフィルターのそれよりも前に*before`Order` コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="4cd37-584">より高い *の値を持つフィルターのそれよりも後に*after`Order` コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="4cd37-585">`Order` プロパティはコンストラクター パラメーターで設定できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="4cd37-586">上記の例にある同じ 3 つのアクション フィルターを検討してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="4cd37-587">コントローラーとグローバル フィルターの `Order` プロパティが 1 と 2 にそれぞれ設定される場合、実行順序が逆になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="4cd37-588">Sequence</span><span class="sxs-lookup"><span data-stu-id="4cd37-588">Sequence</span></span> | <span data-ttu-id="4cd37-589">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="4cd37-589">Filter scope</span></span> | <span data-ttu-id="4cd37-590">`Order` プロパティ</span><span class="sxs-lookup"><span data-stu-id="4cd37-590">`Order` property</span></span> | <span data-ttu-id="4cd37-591">Filter メソッド</span><span class="sxs-lookup"><span data-stu-id="4cd37-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="4cd37-592">1</span><span class="sxs-lookup"><span data-stu-id="4cd37-592">1</span></span> | <span data-ttu-id="4cd37-593">方法</span><span class="sxs-lookup"><span data-stu-id="4cd37-593">Method</span></span> | <span data-ttu-id="4cd37-594">0</span><span class="sxs-lookup"><span data-stu-id="4cd37-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="4cd37-595">2</span><span class="sxs-lookup"><span data-stu-id="4cd37-595">2</span></span> | <span data-ttu-id="4cd37-596">コントローラー</span><span class="sxs-lookup"><span data-stu-id="4cd37-596">Controller</span></span> | <span data-ttu-id="4cd37-597">1</span><span class="sxs-lookup"><span data-stu-id="4cd37-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="4cd37-598">3</span><span class="sxs-lookup"><span data-stu-id="4cd37-598">3</span></span> | <span data-ttu-id="4cd37-599">グローバル</span><span class="sxs-lookup"><span data-stu-id="4cd37-599">Global</span></span> | <span data-ttu-id="4cd37-600">2</span><span class="sxs-lookup"><span data-stu-id="4cd37-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="4cd37-601">4</span><span class="sxs-lookup"><span data-stu-id="4cd37-601">4</span></span> | <span data-ttu-id="4cd37-602">グローバル</span><span class="sxs-lookup"><span data-stu-id="4cd37-602">Global</span></span> | <span data-ttu-id="4cd37-603">2</span><span class="sxs-lookup"><span data-stu-id="4cd37-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="4cd37-604">5</span><span class="sxs-lookup"><span data-stu-id="4cd37-604">5</span></span> | <span data-ttu-id="4cd37-605">コントローラー</span><span class="sxs-lookup"><span data-stu-id="4cd37-605">Controller</span></span> | <span data-ttu-id="4cd37-606">1</span><span class="sxs-lookup"><span data-stu-id="4cd37-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="4cd37-607">6</span><span class="sxs-lookup"><span data-stu-id="4cd37-607">6</span></span> | <span data-ttu-id="4cd37-608">方法</span><span class="sxs-lookup"><span data-stu-id="4cd37-608">Method</span></span> | <span data-ttu-id="4cd37-609">0</span><span class="sxs-lookup"><span data-stu-id="4cd37-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="4cd37-610">フィルターの実行順序を決定するときに、`Order` プロパティによりスコープがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="4cd37-611">最初に順序でフィルターが並べ替えられ、次に同じ順位の優先度を決めるためにスコープが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="4cd37-612">組み込みのフィルターはすべて `IOrderedFilter` を実装し、既定の `Order` 値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="4cd37-613">組み込みのフィルターの場合、`Order` をゼロ以外の値に設定しない限り、スコープによって順序が決定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="4cd37-614">キャンセルとショートサーキット</span><span class="sxs-lookup"><span data-stu-id="4cd37-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="4cd37-615">フィルター メソッドに提供される <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> パラメーターで <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> プロパティを設定することで、フィルター パイプラインをショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="4cd37-616">たとえば、次のリソース フィルターは、パイプラインの残りの部分が実行されるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="4cd37-617">次のコードでは、`ShortCircuitingResourceFilter` と `AddHeader` の両方のフィルターが `SomeResource` アクション メソッドをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="4cd37-618">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="4cd37-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="4cd37-619">これはリソース フィルターであり、`AddHeader` はアクション フィルターであるため、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="4cd37-620">パイプラインの残りの部分は迂回されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="4cd37-621">そのため、`AddHeader` アクションの場合、`SomeResource` フィルターが実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="4cd37-622">`ShortCircuitingResourceFilter` が最初に実行された場合は、両方のフィルターがアクション メソッド レベルで適用されると、この動作が同じになります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="4cd37-623">そのフィルターの種類が原因で、あるいは `ShortCircuitingResourceFilter` プロパティの明示的な使用により、`Order` が最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="4cd37-624">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="4cd37-624">Dependency injection</span></span>

<span data-ttu-id="4cd37-625">フィルターは種類ごとまたはインスタンスごとに追加できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="4cd37-626">インスタンスが追加される場合、そのインスタンスはすべての要求に対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="4cd37-627">種類が追加される場合、それは種類でアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="4cd37-628">種類でアクティブ化されるフィルターの意味:</span><span class="sxs-lookup"><span data-stu-id="4cd37-628">A type-activated filter means:</span></span>

* <span data-ttu-id="4cd37-629">インスタンスは要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-629">An instance is created for each request.</span></span>
* <span data-ttu-id="4cd37-630">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="4cd37-631">属性として実装され、コントローラー クラスまたはアクション メソッドに直接追加されるフィルターは、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって提供されるコンストラクターの依存関係を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="4cd37-632">コンストラクターの依存関係は、次の理由から DI によって与えられません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="4cd37-633">属性には、適用される場所で提供される独自のコンストラクター パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="4cd37-634">これは、属性のしくみの制限です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="4cd37-635">次のフィルターでは、DI から提供されるコンストラクターの依存関係がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="4cd37-636">属性に実装された <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="4cd37-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="4cd37-637">上記のフィルターは、コントローラーまたはアクション メソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="4cd37-638">ロガーは DI から利用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-638">Loggers are available from DI.</span></span> <span data-ttu-id="4cd37-639">ただし、ログ目的でのみフィルターを作成し、使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="4cd37-640">[組み込みフレームワークのログ機能](xref:fundamentals/logging/index)で通常、ログ記録に必要なものが与えられます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="4cd37-641">フィルターに追加されるログ記録:</span><span class="sxs-lookup"><span data-stu-id="4cd37-641">Logging added to filters:</span></span>

* <span data-ttu-id="4cd37-642">ビジネス ドメインの懸念事項やフィルターに固有の動作に焦点を合わせます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="4cd37-643">アクションやその他のフレームワーク イベントはログに記録**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="4cd37-644">組み込みフィルターでは、アクションとフレームワーク イベントが記録されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="4cd37-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4cd37-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="4cd37-646">サービス フィルターの実装の種類は `ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="4cd37-647"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は DI からフィルターのインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="4cd37-648">このコードでは、`AddHeaderResultServiceFilter` が示されています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4cd37-649">次のコードでは、`AddHeaderResultServiceFilter` が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="4cd37-650">次のコードでは、`ServiceFilter` 属性により、DI から `AddHeaderResultServiceFilter` フィルターのインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="4cd37-651">`ServiceFilterAttribute` を使用するときに、[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="4cd37-652">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4cd37-653">ASP.NET Core ランタイムで保証されないこと:</span><span class="sxs-lookup"><span data-stu-id="4cd37-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="4cd37-654">フィルターのインスタンスが 1 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="4cd37-655">フィルターが後の時点で、DI コンテナーから再要求されることはありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="4cd37-656">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="4cd37-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4cd37-658">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4cd37-659">`CreateInstance` により、DI から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="4cd37-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="4cd37-660">TypeFilterAttribute</span></span>

<span data-ttu-id="4cd37-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は<xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> と似ていますが、その型は DI コンテナーから直接解決されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="4cd37-662"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> を使って型をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="4cd37-663">`TypeFilterAttribute` 型は DI コンテナーから直接解決されないためです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="4cd37-664">`TypeFilterAttribute` を利用して参照される型は、DI コンテナーに登録する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="4cd37-665">DI コンテナーによって依存関係が満たされています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="4cd37-666">`TypeFilterAttribute` は必要に応じて、型のコンストラクター引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="4cd37-667">`TypeFilterAttribute` を使用するときに、[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="4cd37-668">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="4cd37-669">ASP.NET Core ランタイムでは、フィルターの 1 インスタンスが作成されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="4cd37-670">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="4cd37-671">次の例は、`TypeFilterAttribute` を使用して、型に引数を渡す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="4cd37-672">承認フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-672">Authorization filters</span></span>

<span data-ttu-id="4cd37-673">承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-673">Authorization filters:</span></span>

* <span data-ttu-id="4cd37-674">フィルター パイプライン内で実行される最初のフィルターです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="4cd37-675">アクション メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-675">Control access to action methods.</span></span>
* <span data-ttu-id="4cd37-676">before メソッドが与えられ、after メソッドは与えられません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="4cd37-677">カスタム承認フィルターには、カスタム承認フレームワークが必要です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="4cd37-678">カスタム フィルターを記述するよりも、独自の承認ポリシーを構成するか、カスタム承認ポリシーを記述することを選びます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="4cd37-679">組み込み承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="4cd37-680">承認システムを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-680">Calls the authorization system.</span></span>
* <span data-ttu-id="4cd37-681">要求は承認されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-681">Does not authorize requests.</span></span>

<span data-ttu-id="4cd37-682">承認フィルター内で例外をスロー**しません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="4cd37-683">例外は処理されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-683">The exception will not be handled.</span></span>
* <span data-ttu-id="4cd37-684">例外フィルターで例外が処理されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="4cd37-685">承認フィルターで例外が発生した場合、チャレンジ発行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="4cd37-686">承認の詳細については、[こちら](xref:security/authorization/introduction)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="4cd37-687">リソース フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-687">Resource filters</span></span>

<span data-ttu-id="4cd37-688">リソース フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-688">Resource filters:</span></span>

* <span data-ttu-id="4cd37-689"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="4cd37-690">実行により、ほとんどのフィルター パイプラインがラップされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="4cd37-691">[承認フィルター](#authorization-filters)のみ、リソース フィルターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="4cd37-692">リソース フィルターは、ほとんどのパイプラインをショートサーキットする目的で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="4cd37-693">たとえば、キャッシュ フィルターにより、キャッシュ ヒットがあるとき、残りのパイプラインを回避できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="4cd37-694">リソース フィルターの例:</span><span class="sxs-lookup"><span data-stu-id="4cd37-694">Resource filter examples:</span></span>

* <span data-ttu-id="4cd37-695">前に示した[ショートサーキットするリソース フィルター](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="4cd37-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="4cd37-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="4cd37-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="4cd37-697">モデル バインドがフォーム データにアクセスすることを禁止します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="4cd37-698">メモリにフォーム データが読み込まれないようにする目的で大きなファイルのアップロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="4cd37-699">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4cd37-700">アクション フィルターは Razor Pages に適用**されません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-700">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="4cd37-701">Razor Pages では <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-701">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="4cd37-702">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="4cd37-703">アクション フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-703">Action filters:</span></span>

* <span data-ttu-id="4cd37-704"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="4cd37-705">この実行はアクション メソッドの実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="4cd37-706">次のコードは、サンプル アクション フィルターを示しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="4cd37-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> では次のプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="4cd37-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - アクション メソッドへの入力を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="4cd37-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - コントローラー インスタンスを操作できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="4cd37-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` を設定すると、アクション メソッドと後続のアクション フィルターの実行がショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="4cd37-711">アクション メソッドで例外をスローする:</span><span class="sxs-lookup"><span data-stu-id="4cd37-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="4cd37-712">後続のフィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="4cd37-713">`Result` の設定とは異なり、結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4cd37-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> は、`Controller` と `Result` に加え、次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="4cd37-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 別のフィルターによってアクションの実行がショートサーキットされた場合は、true になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4cd37-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - アクションまたは前に実行されたアクション フィルターが例外をスローした場合は、null 以外になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="4cd37-717">このプロパティを null に設定する:</span><span class="sxs-lookup"><span data-stu-id="4cd37-717">Setting this property to null:</span></span>

  * <span data-ttu-id="4cd37-718">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="4cd37-719">`Result` は、アクション メソッドから返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="4cd37-720">`IAsyncActionFilter` の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> の呼び出しによって:</span><span class="sxs-lookup"><span data-stu-id="4cd37-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="4cd37-721">後続のすべてのアクション フィルターとアクション メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="4cd37-722">`ActionExecutedContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="4cd37-723">ショートサーキットするには、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> を結果インスタンスに割り当てます。`next` (`ActionExecutionDelegate`) は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="4cd37-724">このフレームワークからは、サブクラス化できる抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="4cd37-725">`OnActionExecuting` アクション フィルターを次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="4cd37-726">モデルの状態を検証します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-726">Validate model state.</span></span>
* <span data-ttu-id="4cd37-727">状態が有効でない場合は、エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="4cd37-728">`OnActionExecuted` メソッドは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="4cd37-729">また、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> プロパティを介してアクションの結果を表示したり、操作したりできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="4cd37-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> は、アクションの実行が別のフィルターによってショートサーキットされた場合、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="4cd37-731">アクションまたは後続のアクション フィルターが例外をスローした場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="4cd37-732">`Exception` を null に設定すると:</span><span class="sxs-lookup"><span data-stu-id="4cd37-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="4cd37-733">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="4cd37-734">`ActionExecutedContext.Result` は、アクション メソッドから通常どおり返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="4cd37-735">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-735">Exception filters</span></span>

<span data-ttu-id="4cd37-736">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-736">Exception filters:</span></span>

* <span data-ttu-id="4cd37-737"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="4cd37-738">共通のエラー処理ポリシーを実装する目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="4cd37-739">次の例外フィルターのサンプルでは、カスタムのエラー ビューを使用して、アプリの開発中に発生する例外に関する詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="4cd37-740">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-740">Exception filters:</span></span>

* <span data-ttu-id="4cd37-741">before イベントと after イベントが与えられません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-741">Don't have before and after events.</span></span>
* <span data-ttu-id="4cd37-742"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="4cd37-743">Razor Page またはコントローラーの作成、[モデル バインド](xref:mvc/models/model-binding)、アクション フィルター、またはアクション メソッドで発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="4cd37-744">リソース フィルター、結果フィルター、または MVC 結果の実行で発生した例外はキャッチ**しません**。</span><span class="sxs-lookup"><span data-stu-id="4cd37-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="4cd37-745">例外を処理するには、<xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> プロパティを `true` に設定するか、応答を記述します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="4cd37-746">これにより、例外の伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-746">This stops propagation of the exception.</span></span> <span data-ttu-id="4cd37-747">例外フィルターで例外を "成功" に変えることはできません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="4cd37-748">それができるのは、アクション フィルターだけです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-748">Only an action filter can do that.</span></span>

<span data-ttu-id="4cd37-749">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-749">Exception filters:</span></span>

* <span data-ttu-id="4cd37-750">アクション内で発生する例外のトラップに適しています。</span><span class="sxs-lookup"><span data-stu-id="4cd37-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="4cd37-751">エラー処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="4cd37-752">例外処理にはミドルウェアを選択してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="4cd37-753">呼び出されたアクション メソッドに基づいてエラー処理が*異なる*状況でのみ例外フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="4cd37-754">たとえば、アプリには、API エンドポイントとビュー/HTML の両方に対するアクション メソッドがある場合があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="4cd37-755">API エンドポイントは、JSON としてのエラー情報を返す可能性がある一方で、ビュー ベースのアクションがエラー ページを HTML として返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="4cd37-756">結果フィルター</span><span class="sxs-lookup"><span data-stu-id="4cd37-756">Result filters</span></span>

<span data-ttu-id="4cd37-757">結果フィルター:</span><span class="sxs-lookup"><span data-stu-id="4cd37-757">Result filters:</span></span>

* <span data-ttu-id="4cd37-758">インターフェイスを実装します:</span><span class="sxs-lookup"><span data-stu-id="4cd37-758">Implement an interface:</span></span>
  * <span data-ttu-id="4cd37-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="4cd37-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="4cd37-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="4cd37-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="4cd37-761">この実行はアクション結果の実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="4cd37-762">IResultFilter および IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="4cd37-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="4cd37-763">次は、HTTP ヘッダーを追加する結果フィルターのコードです。</span><span class="sxs-lookup"><span data-stu-id="4cd37-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="4cd37-764">実行されている結果の種類は、アクションに依存します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="4cd37-765">ビューを返すアクションには、実行されている <xref:Microsoft.AspNetCore.Mvc.ViewResult> の一部として、すべての razor 処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="4cd37-766">API メソッドは、結果の実行の一部としていくつかのシリアル化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="4cd37-767">[アクション結果](xref:mvc/controllers/actions)に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="4cd37-768">結果フィルターは、アクションまたはアクション フィルターによってアクション結果を生成される場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="4cd37-769">結果フィルターは、次の場合には実行されません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="4cd37-770">承認フィルターまたはリソース フィルターによって、パイプラインがショートサーキットされる。</span><span class="sxs-lookup"><span data-stu-id="4cd37-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="4cd37-771">アクションの結果を生成することで、例外フィルターによって例外が処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="4cd37-772"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> メソッドは、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> を `true` に設定することで、アクションの結果と後続の結果フィルターの実行をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="4cd37-773">ショートサーキットする場合は、空の応答が生成されないように応答オブジェクトに記述します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="4cd37-774">`IResultFilter.OnResultExecuting` で例外をスローすることで:</span><span class="sxs-lookup"><span data-stu-id="4cd37-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="4cd37-775">アクション結果と後続フィルターの実行が回避されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="4cd37-776">結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="4cd37-777"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> メソッドが実行されたとき、応答が既にクライアントに送信されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="4cd37-778">応答が既にクライアントに送信されていた場合は、それ以上変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="4cd37-779">別のフィルターによってアクションの結果の実行がショートサーキットされた場合、`ResultExecutedContext.Canceled` は `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="4cd37-780">アクションの結果または後続の結果フィルターが例外をスローした場合、`ResultExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="4cd37-781">`Exception` を null に設定すると、例外を効果的に処理でき、パイプラインの後方で ASP.NET Core によって例外が再スローされることを防げます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="4cd37-782">結果フィルターの例外を処理するとき、応答にデータを書き込む目的で信頼できる方法はありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="4cd37-783">アクションの結果により例外がスローされるとき、ヘッダーがクライアントにフラッシュされている場合、エラー コードを送信する目的で信頼できるメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="4cd37-784"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> の場合、`await next` で <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> を呼び出すと、後続のすべての結果フィルターとアクションの結果が実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="4cd37-785">ショートサーキットするには、[ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) を `true` に設定し、`ResultExecutionDelegate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="4cd37-786">このフレームワークからは、サブクラス化できる抽象 `ResultFilterAttribute` が与えられます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="4cd37-787">前に示した [AddHeaderAttribute](#add-header-attribute) クラスは、結果フィルター属性の一例です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="4cd37-788">IAlwaysRunResultFilter および IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="4cd37-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="4cd37-789"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> および <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> インターフェイスでは、すべてのアクションの結果に対して実行される <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> の実装が宣言されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="4cd37-790">これには、以下によって生成されるアクションの結果が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-790">This includes action results produced by:</span></span>

* <span data-ttu-id="4cd37-791">ショートサーキットが行われる承認フィルターとリソース フィルター。</span><span class="sxs-lookup"><span data-stu-id="4cd37-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="4cd37-792">例外フィルター。</span><span class="sxs-lookup"><span data-stu-id="4cd37-792">Exception filters.</span></span>

<span data-ttu-id="4cd37-793">たとえば、次のフィルターは常に実行され、コンテンツ ネゴシエーションが失敗した場合に "<xref:Microsoft.AspNetCore.Mvc.ObjectResult>422 処理不可エンティティ *" 状態コードを使ってアクションの結果 (* ) を設定します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="4cd37-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="4cd37-794">IFilterFactory</span></span>

<span data-ttu-id="4cd37-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="4cd37-796">そのため、`IFilterFactory` インスタンスはフィルター パイプライン内の任意の場所で `IFilterMetadata` インスタンスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="4cd37-797">ランタイムでは、フィルターを呼び出す準備をする際、`IFilterFactory` へのキャストが試行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="4cd37-798">そのキャストが成功した場合、呼び出される <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> インスタンスを作成するために `IFilterMetadata` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="4cd37-799">これにより、アプリの起動時に正確なフィルター パイプラインを明示的に設定する必要がないため、柔軟なデザインが可能になります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="4cd37-800">フィルターを作成するための別の方法として、カスタムの属性の実装で `IFilterFactory` を実装できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="4cd37-801">[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)を実行することで、前のコードをテストできます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="4cd37-802">F12 開発者ツールを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="4cd37-803">`https://localhost:5001/Sample/HeaderWithFactory` に移動します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="4cd37-804">F12 開発者ツールでは、サンプル コードによって追加された次の応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="4cd37-805">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="4cd37-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="4cd37-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="4cd37-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="4cd37-807">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="4cd37-807">**internal:** `My header`</span></span>

<span data-ttu-id="4cd37-808">上記のコードでは、**internal:** `My header` という応答ヘッダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="4cd37-809">属性に実装された IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="4cd37-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="4cd37-810">`IFilterFactory` を実装するフィルターは次のようなフィルターに便利です。</span><span class="sxs-lookup"><span data-stu-id="4cd37-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="4cd37-811">パラメーターの引き渡しを必要としません。</span><span class="sxs-lookup"><span data-stu-id="4cd37-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="4cd37-812">DI で満たす必要があるコンストラクター依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="4cd37-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="4cd37-814">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="4cd37-815">`CreateInstance` により、サービス コンテナー (DI) から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="4cd37-816">次のコードでは、`[SampleActionFilter]` を適用する 3 つの手法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="4cd37-817">上記のコードでは、`[SampleActionFilter]` を適用する方法としては、`SampleActionFilter` でメソッドを装飾する方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="4cd37-818">フィルター パイプラインでのミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="4cd37-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="4cd37-819">リソース フィルターは、パイプラインの後方で登場するすべての実行を囲む点で、[ミドルウェア](xref:fundamentals/middleware/index)のように機能します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="4cd37-820">ただし、フィルターは ASP.NET Core ランタイムの一部である点がミドルウェアとは異なります。つまり、フィルターには ASP.NET Core コンテキストとコンストラクトへのアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="4cd37-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="4cd37-821">ミドルウェアをフィルターとして使用するには、フィルター パイプラインに挿入するミドルウェアを指定する `Configure` メソッドを使用して型を作成します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="4cd37-822">ローカリゼーション ミドルウェアを使用して要求の現在のカルチャを確立する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="4cd37-823"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> を使用し、ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="4cd37-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="4cd37-824">ミドルウェア フィルターは、フィルター パイプラインのリソース フィルターと同じステージ (モデル バインドの前、残りのパイプラインの後) で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4cd37-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="4cd37-825">次の操作</span><span class="sxs-lookup"><span data-stu-id="4cd37-825">Next actions</span></span>

* <span data-ttu-id="4cd37-826">[Razor Pages のフィルター メソッド](xref:razor-pages/filter)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4cd37-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="4cd37-827">フィルターを試すには、[GitHub のサンプルをダウンロードして、テストおよび変更を行います](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="4cd37-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
