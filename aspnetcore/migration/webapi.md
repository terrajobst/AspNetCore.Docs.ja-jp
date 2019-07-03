---
title: ASP.NET Web API から ASP.NET Core に移行します。
author: ardalis
description: ASP.NET 4.x Web API から ASP.NET Core mvc web API の実装を移行する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/10/2018
uid: migration/webapi
ms.openlocfilehash: 74c7730a667ebc979241489733cdace149cacdf2
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555743"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="7e932-103">ASP.NET Web API から ASP.NET Core に移行します。</span><span class="sxs-lookup"><span data-stu-id="7e932-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="7e932-104">によって[Scott Addie](https://twitter.com/scott_addie)と[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7e932-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7e932-105">ASP.NET 4.x Web API は、クライアント、ブラウザーやモバイル デバイスなどの広範な範囲に到達する HTTP サービスです。</span><span class="sxs-lookup"><span data-stu-id="7e932-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="7e932-106">ASP.NET Core は ASP.NET 4.x の MVC を統一し、Web API アプリを ASP.NET Core MVC と呼ばれる単純なプログラミング モデルにモデル化します。</span><span class="sxs-lookup"><span data-stu-id="7e932-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="7e932-107">この記事では、ASP.NET 4.x Web API から ASP.NET Core MVC への移行に必要な手順を示します。</span><span class="sxs-lookup"><span data-stu-id="7e932-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="7e932-108">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7e932-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e932-109">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7e932-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="7e932-110">ASP.NET 4.x Web API プロジェクトを確認してください。</span><span class="sxs-lookup"><span data-stu-id="7e932-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="7e932-111">この記事では、開始点として、 *ProductsApp*で作成したプロジェクト[ASP.NET Web API 2 の概要](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)します。</span><span class="sxs-lookup"><span data-stu-id="7e932-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="7e932-112">そのプロジェクトでは、単純な ASP.NET 4.x Web API プロジェクトを次のように構成します。</span><span class="sxs-lookup"><span data-stu-id="7e932-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="7e932-113">*Global.asax.cs*、呼び出しに`WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="7e932-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="7e932-114">`WebApiConfig`クラスがある、 *App_Start*フォルダーが静的と`Register`メソッド。</span><span class="sxs-lookup"><span data-stu-id="7e932-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="7e932-115">このクラスを構成します[属性ルーティング](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)、実際には、プロジェクトで使用されています。</span><span class="sxs-lookup"><span data-stu-id="7e932-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="7e932-116">ASP.NET Web API で使用される、ルーティング テーブルも構成します。</span><span class="sxs-lookup"><span data-stu-id="7e932-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="7e932-117">ASP.NET 4.x Web API Url の形式に一致が必要ですがこの場合、`/api/{controller}/{id}`で`{id}`で省略可能します。</span><span class="sxs-lookup"><span data-stu-id="7e932-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="7e932-118">*ProductsApp*プロジェクトには、1 つのコント ローラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7e932-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="7e932-119">コント ローラーが継承`ApiController`2 つのアクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7e932-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="7e932-120">`Product`によって使用されるモデル`ProductsController`は単純なクラスです。</span><span class="sxs-lookup"><span data-stu-id="7e932-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="7e932-121">次のセクションでは、ASP.NET Core mvc Web API プロジェクトの移行について説明します。</span><span class="sxs-lookup"><span data-stu-id="7e932-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="7e932-122">コピー先のプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="7e932-122">Create destination project</span></span>

<span data-ttu-id="7e932-123">Visual Studio で、次の手順を完了するには。</span><span class="sxs-lookup"><span data-stu-id="7e932-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="7e932-124">移動して**ファイル** > **新しい** > **プロジェクト** > **他のプロジェクト タイプ** > **Visual Studio ソリューション**します。</span><span class="sxs-lookup"><span data-stu-id="7e932-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="7e932-125">選択**空のソリューション**、ソリューションの名前と*WebAPIMigration*します。</span><span class="sxs-lookup"><span data-stu-id="7e932-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="7e932-126">をクリックして、 **OK**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7e932-126">Click the **OK** button.</span></span>
* <span data-ttu-id="7e932-127">既存の追加*ProductsApp*プロジェクトがソリューションにします。</span><span class="sxs-lookup"><span data-stu-id="7e932-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="7e932-128">新しい追加**ASP.NET Core Web アプリケーション**プロジェクトがソリューションにします。</span><span class="sxs-lookup"><span data-stu-id="7e932-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="7e932-129">選択、 **.NET Core**ドロップダウン リストからフレームワークをターゲットにして、選択、 **API**プロジェクト テンプレート。</span><span class="sxs-lookup"><span data-stu-id="7e932-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="7e932-130">プロジェクトに名前を*ProductsCore*、 をクリックし、 **OK**ボタン。</span><span class="sxs-lookup"><span data-stu-id="7e932-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="7e932-131">ソリューションには、2 つのプロジェクトが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="7e932-131">The solution now contains two projects.</span></span> <span data-ttu-id="7e932-132">次のセクションでは、移行について説明します、 *ProductsApp*プロジェクトのコンテンツを*ProductsCore*プロジェクト。</span><span class="sxs-lookup"><span data-stu-id="7e932-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="7e932-133">構成を移行します。</span><span class="sxs-lookup"><span data-stu-id="7e932-133">Migrate configuration</span></span>

<span data-ttu-id="7e932-134">ASP.NET Core を使用していない、 *App_Start*フォルダーまたは*Global.asax*ファイル、および*web.config*発行時にファイルが追加します。</span><span class="sxs-lookup"><span data-stu-id="7e932-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="7e932-135">*Startup.cs*の置換は、 *Global.asax*プロジェクトのルートにあるとします。</span><span class="sxs-lookup"><span data-stu-id="7e932-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="7e932-136">`Startup`クラスはすべてのアプリのスタートアップ タスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="7e932-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="7e932-137">詳細については、「 <xref:fundamentals/startup> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7e932-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="7e932-138">ASP.NET Core mvc で属性ルーティングは、既定で含まれてとき<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>で呼び出される`Startup.Configure`します。</span><span class="sxs-lookup"><span data-stu-id="7e932-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="7e932-139">次`UseMvc`置換を呼び出し、 *ProductsApp*プロジェクトの*App_Start/WebApiConfig.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="7e932-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="7e932-140">モデルとコント ローラーを移行します。</span><span class="sxs-lookup"><span data-stu-id="7e932-140">Migrate models and controllers</span></span>

<span data-ttu-id="7e932-141">経由でコピー、 *ProductApp*プロジェクトのコント ローラーと、モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="7e932-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="7e932-142">この場合は、以下の手順に従ってください。</span><span class="sxs-lookup"><span data-stu-id="7e932-142">Follow these steps:</span></span>

1. <span data-ttu-id="7e932-143">コピー *Controllers/ProductsController.cs*から元のプロジェクトに新しいものです。</span><span class="sxs-lookup"><span data-stu-id="7e932-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="7e932-144">全体をコピーします*モデル*フォルダーを元のプロジェクトから新しいものにします。</span><span class="sxs-lookup"><span data-stu-id="7e932-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="7e932-145">新しいプロジェクトの名前に一致するように、コピーしたファイルの名前空間を変更 (*ProductsCore*)。</span><span class="sxs-lookup"><span data-stu-id="7e932-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="7e932-146">調整、`using ProductsApp.Models;`ステートメント*ProductsController.cs*すぎます。</span><span class="sxs-lookup"><span data-stu-id="7e932-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="7e932-147">この時点では、コンパイル エラーの数のアプリの結果を構築します。</span><span class="sxs-lookup"><span data-stu-id="7e932-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="7e932-148">ASP.NET Core で、次のコンポーネントが存在しないため、エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="7e932-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="7e932-149">`ApiController` クラス</span><span class="sxs-lookup"><span data-stu-id="7e932-149">`ApiController` class</span></span>
* <span data-ttu-id="7e932-150">`System.Web.Http` 名前空間</span><span class="sxs-lookup"><span data-stu-id="7e932-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="7e932-151">`IHttpActionResult` インターフェイス</span><span class="sxs-lookup"><span data-stu-id="7e932-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="7e932-152">次のように、エラーを修正します。</span><span class="sxs-lookup"><span data-stu-id="7e932-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="7e932-153">変更`ApiController`に<xref:Microsoft.AspNetCore.Mvc.ControllerBase>します。</span><span class="sxs-lookup"><span data-stu-id="7e932-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="7e932-154">追加`using Microsoft.AspNetCore.Mvc;`解決するのには、`ControllerBase`参照。</span><span class="sxs-lookup"><span data-stu-id="7e932-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="7e932-155">`using System.Web.Http;`を削除します。</span><span class="sxs-lookup"><span data-stu-id="7e932-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="7e932-156">変更、`GetProduct`アクションの戻り値の型から`IHttpActionResult`に`ActionResult<Product>`します。</span><span class="sxs-lookup"><span data-stu-id="7e932-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="7e932-157">簡略化、`GetProduct`アクションの`return`次のステートメント。</span><span class="sxs-lookup"><span data-stu-id="7e932-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="7e932-158">ルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="7e932-158">Configure routing</span></span>

<span data-ttu-id="7e932-159">次のようにルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="7e932-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="7e932-160">装飾、`ProductsController`クラスで、次の属性。</span><span class="sxs-lookup"><span data-stu-id="7e932-160">Decorate the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="7e932-161">上記の[[ルート]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)属性は、コント ローラーの属性のルーティング パターンを構成します。</span><span class="sxs-lookup"><span data-stu-id="7e932-161">The preceding [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="7e932-162">[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)属性は、このコント ローラーで、すべてのアクションの要件をルーティングする属性。</span><span class="sxs-lookup"><span data-stu-id="7e932-162">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="7e932-163">などのトークンをサポートする属性ルーティング`[controller]`と`[action]`します。</span><span class="sxs-lookup"><span data-stu-id="7e932-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="7e932-164">時に、各トークンに置き換えられますコント ローラーまたはアクションの名前、属性が適用されています。</span><span class="sxs-lookup"><span data-stu-id="7e932-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="7e932-165">トークンは、プロジェクト魔法の文字列の数を減らします。</span><span class="sxs-lookup"><span data-stu-id="7e932-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="7e932-166">トークンには、ルートに対応するコント ローラーとの同期を維持し、自動リファクタリングの名前を変更する際のアクションが適用されることも確認してください。</span><span class="sxs-lookup"><span data-stu-id="7e932-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="7e932-167">ASP.NET Core 2.2 には、プロジェクトの互換性モードを設定します。</span><span class="sxs-lookup"><span data-stu-id="7e932-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="7e932-168">上記の変更:</span><span class="sxs-lookup"><span data-stu-id="7e932-168">The preceding change:</span></span>

    * <span data-ttu-id="7e932-169">使用するために必要な`[ApiController]`コント ローラー レベルでの属性。</span><span class="sxs-lookup"><span data-stu-id="7e932-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="7e932-170">可能性のある ASP.NET Core 2.2 で導入された動作を分割することにオプトインします。</span><span class="sxs-lookup"><span data-stu-id="7e932-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="7e932-171">HTTP Get 要求を有効にする、`ProductController`アクション。</span><span class="sxs-lookup"><span data-stu-id="7e932-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="7e932-172">適用、 [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)属性を`GetAllProducts`アクション。</span><span class="sxs-lookup"><span data-stu-id="7e932-172">Apply the [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="7e932-173">適用、`[HttpGet("{id}")]`属性を`GetProduct`アクション。</span><span class="sxs-lookup"><span data-stu-id="7e932-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="7e932-174">上記の変更と未使用の削除後`using`ステートメント、 *ProductsController.cs*ファイルは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7e932-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="7e932-175">移行されたプロジェクトを実行しを参照`/api/products`します。</span><span class="sxs-lookup"><span data-stu-id="7e932-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="7e932-176">3 つの製品の完全な一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e932-176">A full list of three products appears.</span></span> <span data-ttu-id="7e932-177">`/api/products/1` を参照します。</span><span class="sxs-lookup"><span data-stu-id="7e932-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="7e932-178">最初の製品が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e932-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="7e932-179">互換性 shim</span><span class="sxs-lookup"><span data-stu-id="7e932-179">Compatibility shim</span></span>

<span data-ttu-id="7e932-180">[Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)ライブラリは ASP.NET 4.x Web API プロジェクトを ASP.NET Core に移動する互換性 shim を提供します。</span><span class="sxs-lookup"><span data-stu-id="7e932-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="7e932-181">互換性 shim は、多くの ASP.NET 4.x Web API 2 の規則をサポートするために ASP.NET Core を拡張します。</span><span class="sxs-lookup"><span data-stu-id="7e932-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="7e932-182">このドキュメントで既に移植サンプルは、基本互換性 shim は必要なかったことです。</span><span class="sxs-lookup"><span data-stu-id="7e932-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="7e932-183">大規模なプロジェクトは、互換性 shim を使用してできる一時的に ASP.NET Core と ASP.NET 4.x Web API 2 API のギャップを埋めるに便利です。</span><span class="sxs-lookup"><span data-stu-id="7e932-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="7e932-184">Web API の互換性 shim は、移行する大規模な ASP.NET 4.x Web API プロジェクトを ASP.NET Core をサポートするために、一時的な措置として使用します。</span><span class="sxs-lookup"><span data-stu-id="7e932-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="7e932-185">時間の経過と共に ASP.NET Core のパターンを使用して、互換性 shim を使用する代わりにプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e932-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="7e932-186">含まれる互換性機能`Microsoft.AspNetCore.Mvc.WebApiCompatShim`が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7e932-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="7e932-187">追加、`ApiController`コント ローラーの基本型を更新する必要があるように入力します。</span><span class="sxs-lookup"><span data-stu-id="7e932-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="7e932-188">Web API スタイルのモデル バインドを有効にします。</span><span class="sxs-lookup"><span data-stu-id="7e932-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="7e932-189">ASP.NET Core MVC のモデル バインドの関数と同様に ASP.NET の 4.x MVC 5 の場合は、既定では。</span><span class="sxs-lookup"><span data-stu-id="7e932-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="7e932-190">互換性 shim の変更はモデル バインドを ASP.NET 4.x Web API 2 のモデル バインドの規則に似ています。</span><span class="sxs-lookup"><span data-stu-id="7e932-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="7e932-191">たとえば、複合型は、要求本文から自動的にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="7e932-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="7e932-192">コント ローラー アクションは、型のパラメーターを実行できるように、モデル バインドを拡張`HttpRequestMessage`します。</span><span class="sxs-lookup"><span data-stu-id="7e932-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="7e932-193">型の結果を返すアクションを許可するメッセージ フォーマッタを追加します。`HttpResponseMessage`します。</span><span class="sxs-lookup"><span data-stu-id="7e932-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="7e932-194">Web API 2 の操作が応答を処理するために使用する追加の応答メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="7e932-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="7e932-195">`HttpResponseMessage` ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="7e932-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="7e932-196">結果のアクション メソッド:</span><span class="sxs-lookup"><span data-stu-id="7e932-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="7e932-197">インスタンスを追加`IContentNegotiator`アプリへの依存関係の挿入 (DI) コンテナーと、コンテンツ ネゴシエーションに関連する型から使用できるように[Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)します。</span><span class="sxs-lookup"><span data-stu-id="7e932-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="7e932-198">このような種類の例として、`DefaultContentNegotiator`と`MediaTypeFormatter`します。</span><span class="sxs-lookup"><span data-stu-id="7e932-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="7e932-199">互換性 shim を使用するには。</span><span class="sxs-lookup"><span data-stu-id="7e932-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="7e932-200">インストール、 [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="7e932-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="7e932-201">互換性 shim のサービスを呼び出すことによって、アプリの DI コンテナーを登録`services.AddMvc().AddWebApiConventions()`で`Startup.ConfigureServices`します。</span><span class="sxs-lookup"><span data-stu-id="7e932-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="7e932-202">Web API に固有のルーティングを使用して定義`MapWebApiRoute`上、`IRouteBuilder`アプリの`IApplicationBuilder.UseMvc`呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7e932-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e932-203">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7e932-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
