---
title: ASP.NET Web API から ASP.NET Core への移行
author: ardalis
description: Web API の実装を ASP.NET 4.x Web API から ASP.NET Core MVC に移行する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: 7f61b78c589fc9d01061b50554e5a639e372c3d8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653006"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="98c97-103">ASP.NET Web API から ASP.NET Core への移行</span><span class="sxs-lookup"><span data-stu-id="98c97-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="98c97-104">[Scott Addie](https://twitter.com/scott_addie)と[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="98c97-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="98c97-105">ASP.NET 4.x Web API は、ブラウザーやモバイルデバイスを含む広範なクライアントに到達する HTTP サービスです。</span><span class="sxs-lookup"><span data-stu-id="98c97-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="98c97-106">ASP.NET Core は、ASP.NET 4.x の MVC および Web API アプリモデルを、ASP.NET Core MVC と呼ばれるより単純なプログラミングモデルに統合します。</span><span class="sxs-lookup"><span data-stu-id="98c97-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="98c97-107">この記事では、ASP.NET 4.x Web API から ASP.NET Core MVC に移行するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="98c97-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="98c97-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98c97-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="98c97-109">前提条件</span><span class="sxs-lookup"><span data-stu-id="98c97-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="98c97-110">ASP.NET 4.x Web API プロジェクトを確認する</span><span class="sxs-lookup"><span data-stu-id="98c97-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="98c97-111">開始点として、この記事では、 [ASP.NET Web API 2 ではじめに](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)で作成された製品*アプリ*プロジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="98c97-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="98c97-112">そのプロジェクトでは、単純な ASP.NET 4.x Web API プロジェクトは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="98c97-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="98c97-113">*Global.asax.cs*では、`WebApiConfig.Register`に対して呼び出しが行われます。</span><span class="sxs-lookup"><span data-stu-id="98c97-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="98c97-114">`WebApiConfig` クラスは*App_Start*フォルダーにあり、静的な `Register` メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="98c97-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="98c97-115">このクラスは、実際にはプロジェクトで使用されていませんが、[属性ルーティング](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)を構成します。</span><span class="sxs-lookup"><span data-stu-id="98c97-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="98c97-116">また、ASP.NET Web API によって使用されるルーティングテーブルも構成します。</span><span class="sxs-lookup"><span data-stu-id="98c97-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="98c97-117">この場合、ASP.NET 4.x Web API は `/api/{controller}/{id}`形式に一致する Url を必要とし、`{id}` は省略可能であると想定しています。</span><span class="sxs-lookup"><span data-stu-id="98c97-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="98c97-118">製品*アプリ*プロジェクトには、1つのコントローラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="98c97-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="98c97-119">コントローラーは `ApiController` から継承し、次の2つのアクションを含みます。</span><span class="sxs-lookup"><span data-stu-id="98c97-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="98c97-120">`ProductsController` によって使用される `Product` モデルは、単純なクラスです。</span><span class="sxs-lookup"><span data-stu-id="98c97-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="98c97-121">以下のセクションでは、ASP.NET Core MVC に Web API プロジェクトを移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="98c97-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="98c97-122">変換先プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="98c97-122">Create destination project</span></span>

<span data-ttu-id="98c97-123">Visual Studio で次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="98c97-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="98c97-124">**Visual Studio ソリューション** > **その他のプロジェクトの種類** > 、[**新しい** > **プロジェクト**] >  **[ファイル]** にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="98c97-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="98c97-125">**[空のソリューション]** を選択し、ソリューションに*WebAPIMigration*という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="98c97-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="98c97-126">[OK] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="98c97-126">Click the **OK** button.</span></span>
* <span data-ttu-id="98c97-127">既存の製品*アプリ*プロジェクトをソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="98c97-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="98c97-128">新しい**ASP.NET Core Web アプリケーション**プロジェクトをソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="98c97-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="98c97-129">ドロップダウンから **.Net Core**ターゲットフレームワークを選択し、[ **API**プロジェクト] テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="98c97-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="98c97-130">プロジェクトに*Productscore*という名前を付け、 **[OK]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="98c97-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="98c97-131">ソリューションに2つのプロジェクトが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="98c97-131">The solution now contains two projects.</span></span> <span data-ttu-id="98c97-132">次のセクションでは、 *Productscore*プロジェクトの内容を*productscore*プロジェクトに移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="98c97-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="98c97-133">構成の移行</span><span class="sxs-lookup"><span data-stu-id="98c97-133">Migrate configuration</span></span>

<span data-ttu-id="98c97-134">ASP.NET Core では、 *App_Start*フォルダーも*global.asax*ファイルも使用されません *。また、web.config ファイルは*発行時に追加されます。</span><span class="sxs-lookup"><span data-stu-id="98c97-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="98c97-135">*Startup.cs*は*global.asax*の後継であり、プロジェクトのルートにあります。</span><span class="sxs-lookup"><span data-stu-id="98c97-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="98c97-136">`Startup` クラスは、すべてのアプリのスタートアップタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="98c97-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="98c97-137">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="98c97-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="98c97-138">ASP.NET Core MVC では、`Startup.Configure`で <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> を呼び出すと、既定で属性ルーティングが含まれます。</span><span class="sxs-lookup"><span data-stu-id="98c97-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="98c97-139">次の `UseMvc` の呼び出しによって、製品*アプリ*プロジェクトの*App_Start*というファイルが置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="98c97-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="98c97-140">モデルとコントローラーの移行</span><span class="sxs-lookup"><span data-stu-id="98c97-140">Migrate models and controllers</span></span>

<span data-ttu-id="98c97-141">*Productapp*プロジェクトのコントローラーとそれが使用するモデルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="98c97-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="98c97-142">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="98c97-142">Follow these steps:</span></span>

1. <span data-ttu-id="98c97-143">元のプロジェクトから新しいコントローラー */製品コントローラー .cs*をコピーします。</span><span class="sxs-lookup"><span data-stu-id="98c97-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="98c97-144">元のプロジェクトの [*モデル*] フォルダー全体を新しいプロジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="98c97-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="98c97-145">コピーしたファイルの名前空間を新しいプロジェクト名 (*Productscore*) に一致するように変更します。</span><span class="sxs-lookup"><span data-stu-id="98c97-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="98c97-146">*ProductsController.cs*の `using ProductsApp.Models;` ステートメントも調整します。</span><span class="sxs-lookup"><span data-stu-id="98c97-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="98c97-147">この時点で、アプリをビルドすると、多数のコンパイルエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="98c97-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="98c97-148">このエラーが発生するのは、次のコンポーネントが ASP.NET Core に存在しないためです。</span><span class="sxs-lookup"><span data-stu-id="98c97-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="98c97-149">`ApiController` クラス</span><span class="sxs-lookup"><span data-stu-id="98c97-149">`ApiController` class</span></span>
* <span data-ttu-id="98c97-150">`System.Web.Http` 名前空間</span><span class="sxs-lookup"><span data-stu-id="98c97-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="98c97-151">`IHttpActionResult` インターフェイス</span><span class="sxs-lookup"><span data-stu-id="98c97-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="98c97-152">次のようにエラーを修正します。</span><span class="sxs-lookup"><span data-stu-id="98c97-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="98c97-153">`ApiController` を <xref:Microsoft.AspNetCore.Mvc.ControllerBase> に変更します。</span><span class="sxs-lookup"><span data-stu-id="98c97-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="98c97-154">`ControllerBase` 参照を解決する `using Microsoft.AspNetCore.Mvc;` を追加します。</span><span class="sxs-lookup"><span data-stu-id="98c97-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="98c97-155">`using System.Web.Http;`を削除します。</span><span class="sxs-lookup"><span data-stu-id="98c97-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="98c97-156">`GetProduct` アクションの戻り値の型を `IHttpActionResult` から `ActionResult<Product>`に変更します。</span><span class="sxs-lookup"><span data-stu-id="98c97-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="98c97-157">`GetProduct` アクションの `return` ステートメントを次のように簡略化します。</span><span class="sxs-lookup"><span data-stu-id="98c97-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="98c97-158">ルーティングを構成する</span><span class="sxs-lookup"><span data-stu-id="98c97-158">Configure routing</span></span>

<span data-ttu-id="98c97-159">次のようにルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="98c97-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="98c97-160">`ProductsController` クラスを次の属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="98c97-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="98c97-161">上記の[`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)属性は、コントローラーの属性ルーティングパターンを構成します。</span><span class="sxs-lookup"><span data-stu-id="98c97-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="98c97-162">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)属性を使用すると、このコントローラーのすべてのアクションに対して、属性のルーティングが必要になります。</span><span class="sxs-lookup"><span data-stu-id="98c97-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="98c97-163">属性ルーティングでは、`[controller]` や `[action]`などのトークンがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="98c97-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="98c97-164">実行時には、各トークンは、属性が適用されたコントローラーまたはアクションの名前にそれぞれ置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="98c97-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="98c97-165">トークンにより、プロジェクト内のマジック文字列の数が減少します。</span><span class="sxs-lookup"><span data-stu-id="98c97-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="98c97-166">また、トークンは、自動名前変更リファクタリングが適用された場合に、対応するコントローラーとアクションとの同期を維持することも保証します。</span><span class="sxs-lookup"><span data-stu-id="98c97-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="98c97-167">プロジェクトの互換モードを ASP.NET Core 2.2 に設定します。</span><span class="sxs-lookup"><span data-stu-id="98c97-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="98c97-168">上記の変更:</span><span class="sxs-lookup"><span data-stu-id="98c97-168">The preceding change:</span></span>

    * <span data-ttu-id="98c97-169">コントローラーレベルで `[ApiController]` 属性を使用するには、が必要です。</span><span class="sxs-lookup"><span data-stu-id="98c97-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="98c97-170">ASP.NET Core 2.2 で導入された動作が中断する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="98c97-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="98c97-171">`ProductController` アクションに対する HTTP Get 要求を有効にします。</span><span class="sxs-lookup"><span data-stu-id="98c97-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="98c97-172">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)属性を `GetAllProducts` アクションに適用します。</span><span class="sxs-lookup"><span data-stu-id="98c97-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="98c97-173">`[HttpGet("{id}")]` 属性を `GetProduct` アクションに適用します。</span><span class="sxs-lookup"><span data-stu-id="98c97-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="98c97-174">上記の変更が加えられ、未使用の `using` ステートメントが削除された後、 *ProductsController.cs*ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="98c97-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="98c97-175">移行したプロジェクトを実行し、`/api/products`を参照します。</span><span class="sxs-lookup"><span data-stu-id="98c97-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="98c97-176">3つの製品の完全な一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="98c97-176">A full list of three products appears.</span></span> <span data-ttu-id="98c97-177">[https://www.microsoft.com](`/api/products/1`) を参照します。</span><span class="sxs-lookup"><span data-stu-id="98c97-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="98c97-178">最初の製品が表示されます。</span><span class="sxs-lookup"><span data-stu-id="98c97-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="98c97-179">互換性 shim</span><span class="sxs-lookup"><span data-stu-id="98c97-179">Compatibility shim</span></span>

<span data-ttu-id="98c97-180">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)ライブラリは、ASP.NET 4.X Web API プロジェクトを ASP.NET Core に移動する互換性 shim を提供します。</span><span class="sxs-lookup"><span data-stu-id="98c97-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="98c97-181">互換性 shim は、ASP.NET 4.x Web API 2 のさまざまな規則をサポートするために ASP.NET Core を拡張します。</span><span class="sxs-lookup"><span data-stu-id="98c97-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="98c97-182">このドキュメントで以前に移植したサンプルは、互換性 shim が不要であるという基本的なものです。</span><span class="sxs-lookup"><span data-stu-id="98c97-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="98c97-183">大規模なプロジェクトでは、互換性 shim を使用すると、ASP.NET Core と ASP.NET 4.x Web API 2 間の API ギャップを一時的にブリッジするのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="98c97-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="98c97-184">Web API 互換性 shim は、大規模な ASP.NET 4.x Web API プロジェクトの ASP.NET Core への移行をサポートするための一時的な手段として使用することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="98c97-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="98c97-185">時間の経過と共に、互換性 shim に依存するのではなく、ASP.NET Core パターンを使用するようにプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="98c97-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="98c97-186">`Microsoft.AspNetCore.Mvc.WebApiCompatShim` に含まれる互換性機能は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="98c97-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="98c97-187">コントローラーの基本型を更新する必要がないように `ApiController` 型を追加します。</span><span class="sxs-lookup"><span data-stu-id="98c97-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="98c97-188">Web API スタイルモデルのバインドを有効にします。</span><span class="sxs-lookup"><span data-stu-id="98c97-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="98c97-189">MVC モデルバインドは、既定では ASP.NET 4.x 5 と同様に機能します。 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98c97-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="98c97-190">互換性 shim は、モデルバインディングを ASP.NET 4.x Web API 2 モデルバインディング規則に似たものに変更します。</span><span class="sxs-lookup"><span data-stu-id="98c97-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="98c97-191">たとえば、複合型は要求本文から自動的にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="98c97-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="98c97-192">コントローラーアクションが `HttpRequestMessage`型のパラメーターを受け取ることができるように、モデルバインディングを拡張します。</span><span class="sxs-lookup"><span data-stu-id="98c97-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="98c97-193">アクションが型 `HttpResponseMessage`の結果を返すことを可能にするメッセージフォーマッタを追加します。</span><span class="sxs-lookup"><span data-stu-id="98c97-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="98c97-194">Web API 2 のアクションが応答の処理に使用する可能性がある応答メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="98c97-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="98c97-195">`HttpResponseMessage` ジェネレーター:</span><span class="sxs-lookup"><span data-stu-id="98c97-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="98c97-196">アクションの結果メソッド:</span><span class="sxs-lookup"><span data-stu-id="98c97-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="98c97-197">`IContentNegotiator` のインスタンスをアプリの依存関係挿入 (DI) コンテナーに追加し、 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)のコンテンツネゴシエーションに関連する型を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="98c97-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="98c97-198">このような型の例としては、`DefaultContentNegotiator` や `MediaTypeFormatter`などがあります。</span><span class="sxs-lookup"><span data-stu-id="98c97-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="98c97-199">互換性 shim を使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="98c97-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="98c97-200">[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="98c97-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="98c97-201">`Startup.ConfigureServices`で `services.AddMvc().AddWebApiConventions()` を呼び出して、互換性 shim のサービスをアプリの DI コンテナーに登録します。</span><span class="sxs-lookup"><span data-stu-id="98c97-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="98c97-202">アプリの `IApplicationBuilder.UseMvc` 呼び出しの `IRouteBuilder` で `MapWebApiRoute` を使用して、web API 固有のルートを定義します。</span><span class="sxs-lookup"><span data-stu-id="98c97-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98c97-203">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="98c97-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
