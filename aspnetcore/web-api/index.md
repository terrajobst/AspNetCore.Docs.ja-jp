---
title: ASP.NET Core を使って Web API を作成する
author: scottaddie
description: ASP.NET Core での Web API の作成の基本について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2019
uid: web-api/index
ms.openlocfilehash: 6e1868690a2c384307a23c89467505d3ed8916db
ms.sourcegitcommit: 805f625d16d74e77f02f5f37326e5aceafcb78e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985458"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="b5df5-103">ASP.NET Core を使って Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="b5df5-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="b5df5-104">作成者: [Scott Addie](https://github.com/scottaddie)、[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b5df5-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b5df5-105">ASP.NET Core では、C# を使った RESTful サービス (別名: Web API) の作成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b5df5-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="b5df5-106">要求を処理するために、Web API ではコントローラーを使用します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="b5df5-107">Web API の "*コントローラー*" は `ControllerBase` から派生するクラスです。</span><span class="sxs-lookup"><span data-stu-id="b5df5-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="b5df5-108">この記事では、コントローラーを使って Web API 要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="b5df5-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="b5df5-110">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="b5df5-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="b5df5-111">ControllerBase クラス</span><span class="sxs-lookup"><span data-stu-id="b5df5-111">ControllerBase class</span></span>

<span data-ttu-id="b5df5-112">Web API は、<xref:Microsoft.AspNetCore.Mvc.ControllerBase> から派生した 1 つ以上のコントローラー クラスで構成されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="b5df5-113">Web API のプロジェクト テンプレートでは、スターター コントローラーが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="b5df5-114"><xref:Microsoft.AspNetCore.Mvc.Controller> クラスから派生させて Web API のコントローラーを作成しないでください。</span><span class="sxs-lookup"><span data-stu-id="b5df5-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="b5df5-115">`ControllerBase` から派生した `Controller` にはビューのサポートが追加されるため、これは Web API 要求ではなく Web ページを処理するためのものです。</span><span class="sxs-lookup"><span data-stu-id="b5df5-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="b5df5-116">このルールには例外があります。ビューと Web API の両方で同じコントローラーを使うことを計画している場合は、`Controller` から派生させます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="b5df5-117">`ControllerBase` クラスには、HTTP 要求の処理に役立つプロパティとメソッドが多数用意されています。</span><span class="sxs-lookup"><span data-stu-id="b5df5-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="b5df5-118">たとえば、`ControllerBase.CreatedAtAction` では状態コード 201 が返されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="b5df5-119">次に、`ControllerBase` に用意されているメソッドの例をさらにいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="b5df5-120">メソッド</span><span class="sxs-lookup"><span data-stu-id="b5df5-120">Method</span></span>   |<span data-ttu-id="b5df5-121">メモ</span><span class="sxs-lookup"><span data-stu-id="b5df5-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="b5df5-122">400 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>|<span data-ttu-id="b5df5-123">404 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="b5df5-124">ファイルを返します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="b5df5-125">[モデル バインド](xref:mvc/models/model-binding)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="b5df5-126">[モデル検証](xref:mvc/models/validation)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="b5df5-127">使用可能なすべてのメソッドとプロパティの一覧については、「<xref:Microsoft.AspNetCore.Mvc.ControllerBase>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5df5-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="b5df5-128">属性</span><span class="sxs-lookup"><span data-stu-id="b5df5-128">Attributes</span></span>

<span data-ttu-id="b5df5-129"><xref:Microsoft.AspNetCore.Mvc> 名前空間には、Web API のコントローラーとアクション メソッドの動作の構成に使用できる属性が用意されています。</span><span class="sxs-lookup"><span data-stu-id="b5df5-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="b5df5-130">次の例では、属性を使って、サポート対象の HTTP アクション動詞と、返却される可能性がある既知の HTTP ステータス コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="b5df5-131">次に、使用できる属性の例をさらにいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="b5df5-132">属性</span><span class="sxs-lookup"><span data-stu-id="b5df5-132">Attribute</span></span>|<span data-ttu-id="b5df5-133">メモ</span><span class="sxs-lookup"><span data-stu-id="b5df5-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="b5df5-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="b5df5-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="b5df5-135">コントローラーまたはアクションの URL パターンを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="b5df5-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="b5df5-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="b5df5-137">モデル バインドのために含めるプレフィックスとプロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="b5df5-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="b5df5-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="b5df5-139">HTTP GET アクション動詞をサポートするアクションを特定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-139">Identifies an action that supports the HTTP GET action verb.</span></span>|
|<span data-ttu-id="b5df5-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="b5df5-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="b5df5-141">アクションが受け取るデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="b5df5-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="b5df5-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="b5df5-143">アクションによって返すデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="b5df5-144">使用可能な属性を含む一覧については、<xref:Microsoft.AspNetCore.Mvc> 名前空間をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b5df5-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="b5df5-145">ApiController 属性</span><span class="sxs-lookup"><span data-stu-id="b5df5-145">ApiController attribute</span></span>

<span data-ttu-id="b5df5-146">[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性をコントローラー クラスに適用して、次の厳格な、API に固有の動作を有効にできます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

* [<span data-ttu-id="b5df5-147">属性ルーティング要件</span><span class="sxs-lookup"><span data-stu-id="b5df5-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="b5df5-148">自動的な HTTP 400 応答</span><span class="sxs-lookup"><span data-stu-id="b5df5-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="b5df5-149">バインディング ソース パラメーター推論</span><span class="sxs-lookup"><span data-stu-id="b5df5-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="b5df5-150">マルチパート/フォーム データ要求の推論</span><span class="sxs-lookup"><span data-stu-id="b5df5-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="b5df5-151">エラー状態コードに関する問題の詳細</span><span class="sxs-lookup"><span data-stu-id="b5df5-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="b5df5-152">これらの機能では[互換性バージョン](xref:mvc/compatibility-version) 2.1 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="b5df5-152">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="b5df5-153">特定のコントローラーにおける属性</span><span class="sxs-lookup"><span data-stu-id="b5df5-153">Attribute on specific controllers</span></span>

<span data-ttu-id="b5df5-154">プロジェクト テンプレートからの次の例のように、`[ApiController]` 属性は特定のコントローラーに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="b5df5-155">複数のコントローラーにおける属性</span><span class="sxs-lookup"><span data-stu-id="b5df5-155">Attribute on multiple controllers</span></span>

<span data-ttu-id="b5df5-156">複数のコントローラーでこの属性を使う方法の 1 つは、`[ApiController]` 属性で注釈を付けたカスタム基本コントローラー クラスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="b5df5-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="b5df5-157">次は、カスタム基底クラスとそこから派生したコントローラーを示す例です。</span><span class="sxs-lookup"><span data-stu-id="b5df5-157">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="b5df5-158">アセンブリにおける属性</span><span class="sxs-lookup"><span data-stu-id="b5df5-158">Attribute on an assembly</span></span>

<span data-ttu-id="b5df5-159">[互換性バージョン](xref:mvc/compatibility-version)が 2.2 以降に設定されている場合は、`[ApiController]` 属性をアセンブリに適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-159">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="b5df5-160">この方法での注釈では、アセンブリ内のすべてのコントローラーに Web API の動作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="b5df5-161">個別のコントローラーを除外する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="b5df5-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="b5df5-162">アセンブリ レベルの属性を `Startup` クラスを囲む名前空間宣言に適用します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-162">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="b5df5-163">属性ルーティング要件</span><span class="sxs-lookup"><span data-stu-id="b5df5-163">Attribute routing requirement</span></span>

<span data-ttu-id="b5df5-164">`[ApiController]` 属性では、属性ルーティング要件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-164">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="b5df5-165">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-165">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="b5df5-166">`Startup.Configure` の `UseEndpoints`、<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>、または <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> で定義された[規則ルート経由](xref:mvc/controllers/routing#conventional-routing)でアクションにアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b5df5-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="b5df5-167">`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> または <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> で定義された[規則ルート](xref:mvc/controllers/routing#conventional-routing)経由でアクションにアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b5df5-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="b5df5-168">自動的な HTTP 400 応答</span><span class="sxs-lookup"><span data-stu-id="b5df5-168">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="b5df5-169">`[ApiController]` 属性により、モデル検証エラーが発生すると HTTP 400 応答が自動的にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-169">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="b5df5-170">その結果、アクション メソッド内の次のコードは不要になります。</span><span class="sxs-lookup"><span data-stu-id="b5df5-170">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="b5df5-171">ASP.NET Core MVC では、<xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> アクション フィルターの使用により前のチェックが行われます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-171">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="b5df5-172">既定の BadRequest 応答</span><span class="sxs-lookup"><span data-stu-id="b5df5-172">Default BadRequest response</span></span> 

<span data-ttu-id="b5df5-173">2\.1 の互換性バージョンを使う場合、HTTP 400 応答に対する既定の応答の種類は <xref:Microsoft.AspNetCore.Mvc.SerializableError> です。</span><span class="sxs-lookup"><span data-stu-id="b5df5-173">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="b5df5-174">次の要求本文は、シリアル化された型の例です。</span><span class="sxs-lookup"><span data-stu-id="b5df5-174">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b5df5-175">2\.2 以降の互換性バージョンを使う場合、HTTP 400 応答に対する既定の応答の種類は <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> です。</span><span class="sxs-lookup"><span data-stu-id="b5df5-175">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="b5df5-176">次の要求本文は、シリアル化された型の例です。</span><span class="sxs-lookup"><span data-stu-id="b5df5-176">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="b5df5-177">`ValidationProblemDetails` 型:</span><span class="sxs-lookup"><span data-stu-id="b5df5-177">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="b5df5-178">Web API 応答でエラーを指定するための、コンピューターが判読できる形式を提供します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-178">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="b5df5-179">[RFC 7807 仕様](https://tools.ietf.org/html/rfc7807)に準拠しています。</span><span class="sxs-lookup"><span data-stu-id="b5df5-179">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

<span data-ttu-id="b5df5-180">既定の応答の種類を `SerializableError` に変更するには、`Startup.ConfigureServices` で強調表示されている変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-180">To change the default response type to `SerializableError`, apply the highlighted changes in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

### <a name="customize-badrequest-response"></a><span data-ttu-id="b5df5-181">BadRequest 応答をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="b5df5-181">Customize BadRequest response</span></span>

<span data-ttu-id="b5df5-182">検証エラーに起因する応答をカスタマイズするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> を使います。</span><span class="sxs-lookup"><span data-stu-id="b5df5-182">To customize the response that results from a validation error, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span></span> <span data-ttu-id="b5df5-183">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-183">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=4-20)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=5-21)]

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="b5df5-184">自動的な 400 応答を記録する</span><span class="sxs-lookup"><span data-stu-id="b5df5-184">Log automatic 400 responses</span></span>

<span data-ttu-id="b5df5-185">[「How to log automatic 400 responses on model validation errors (モデル検証エラー時に自動的な 400 応答を記録する方法)」(aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5df5-185">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="b5df5-186">自動的な 400 応答を無効にする</span><span class="sxs-lookup"><span data-stu-id="b5df5-186">Disable automatic 400 response</span></span>

<span data-ttu-id="b5df5-187">自動的な 400 の動作を無効にするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-187">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="b5df5-188">`Startup.ConfigureServices` で次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-188">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="b5df5-189">バインディング ソース パラメーター推論</span><span class="sxs-lookup"><span data-stu-id="b5df5-189">Binding source parameter inference</span></span>

<span data-ttu-id="b5df5-190">バインディング ソース属性では、アクション パラメーターの値が存在する場所が定義されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-190">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="b5df5-191">次のバインディング ソース属性が存在します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-191">The following binding source attributes exist:</span></span>

|<span data-ttu-id="b5df5-192">属性</span><span class="sxs-lookup"><span data-stu-id="b5df5-192">Attribute</span></span>|<span data-ttu-id="b5df5-193">バインド ソース</span><span class="sxs-lookup"><span data-stu-id="b5df5-193">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="b5df5-194">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="b5df5-194">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="b5df5-195">要求本文</span><span class="sxs-lookup"><span data-stu-id="b5df5-195">Request body</span></span> |
|<span data-ttu-id="b5df5-196">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="b5df5-196">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="b5df5-197">要求本文内のフォーム データ</span><span class="sxs-lookup"><span data-stu-id="b5df5-197">Form data in the request body</span></span> |
|<span data-ttu-id="b5df5-198">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="b5df5-198">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="b5df5-199">要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="b5df5-199">Request header</span></span> |
|<span data-ttu-id="b5df5-200">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="b5df5-200">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="b5df5-201">要求のクエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="b5df5-201">Request query string parameter</span></span> |
|<span data-ttu-id="b5df5-202">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="b5df5-202">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="b5df5-203">現在の要求からのルート データ</span><span class="sxs-lookup"><span data-stu-id="b5df5-203">Route data from the current request</span></span> |
|<span data-ttu-id="b5df5-204">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="b5df5-204">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="b5df5-205">アクション パラメーターとして挿入される要求サービス</span><span class="sxs-lookup"><span data-stu-id="b5df5-205">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="b5df5-206">値に `%2f` (つまり、`/`) が含まれる可能性がある場合は、`[FromRoute]` を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="b5df5-206">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="b5df5-207">`%2f` は `/` にエスケープ解除されません。</span><span class="sxs-lookup"><span data-stu-id="b5df5-207">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="b5df5-208">値に `%2f` が含まれる可能性がある場合は、`[FromQuery]` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="b5df5-208">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="b5df5-209">`[ApiController]` 属性や `[FromQuery]` などのバインディング ソース属性がない場合は、ASP.NET Core ランタイムにより複合オブジェクト モデル バインダーの使用が試行されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-209">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="b5df5-210">複合オブジェクト モデル バインダーでは、値プロバイダーから定義された順序でデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-210">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="b5df5-211">次の例では、`discontinuedOnly` パラメーター値が要求 URL のクエリ文字列に指定されていることが `[FromQuery]` によって示されています。</span><span class="sxs-lookup"><span data-stu-id="b5df5-211">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="b5df5-212">`[ApiController]` 属性により、アクション パラメーターの既定のデータ ソースに対する推論規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-212">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="b5df5-213">これらの規則により、アクション パラメーターに属性を適用することで手動でバインディング ソースを特定する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="b5df5-213">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="b5df5-214">バインディング ソースの推論規則は、次のように動作します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-214">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="b5df5-215">`[FromBody]` は複合型パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-215">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="b5df5-216">`[FromBody]` 推論規則に対する例外は、<xref:Microsoft.AspNetCore.Http.IFormCollection> や <xref:System.Threading.CancellationToken> など、特殊な意味を持つ組み込みの複合型です。</span><span class="sxs-lookup"><span data-stu-id="b5df5-216">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="b5df5-217">バインディング ソース推論コードでは、そのような特殊な型は無視されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-217">The binding source inference code ignores those special types.</span></span> 
* <span data-ttu-id="b5df5-218">`[FromForm]` は <xref:Microsoft.AspNetCore.Http.IFormFile> および <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 型のアクション パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-218">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="b5df5-219">簡易型またはユーザー定義型に対しては推論されません。</span><span class="sxs-lookup"><span data-stu-id="b5df5-219">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="b5df5-220">`[FromRoute]` は、ルート テンプレート内のパラメーターと一致する任意のアクション パラメーター名に対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-220">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="b5df5-221">複数のルートがアクション パラメーターと一致する場合、ルート値はいずれも `[FromRoute]` と見なされます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-221">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="b5df5-222">`[FromQuery]` は他の任意のアクション パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-222">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="b5df5-223">FromBody 推論に関するメモ</span><span class="sxs-lookup"><span data-stu-id="b5df5-223">FromBody inference notes</span></span>

<span data-ttu-id="b5df5-224">`[FromBody]` は、`string` や `int` などの単純型に対しては推論されません。</span><span class="sxs-lookup"><span data-stu-id="b5df5-224">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="b5df5-225">そのため、その機能が必要な場合、単純型に対しては `[FromBody]` 属性を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5df5-225">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="b5df5-226">要求本文からバインドされるパラメーターがアクションに複数ある場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-226">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="b5df5-227">たとえば、次のアクション メソッドのシグネチャはすべて例外の原因となります。</span><span class="sxs-lookup"><span data-stu-id="b5df5-227">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="b5df5-228">複合型であるため、両方に対して `[FromBody]` が推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-228">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="b5df5-229">1 つには `[FromBody]` 属性が使われ、もう 1 つは複合型なので推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-229">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="b5df5-230">両方で `[FromBody]` 属性が使われます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-230">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="b5df5-231">ASP.NET Core 2.1 では、リストや配列などのコレクション型パラメーターが誤って `[FromQuery]` と推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-231">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="b5df5-232">これらのパラメーターを要求本文からバインドする場合は、`[FromBody]` 属性を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5df5-232">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="b5df5-233">この動作は ASP.NET Core 2.2 以降で修正されており、既定でコレクション型パラメーターが本文からバインドされることが推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-233">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="b5df5-234">推論規則を無効にする</span><span class="sxs-lookup"><span data-stu-id="b5df5-234">Disable inference rules</span></span>

<span data-ttu-id="b5df5-235">バインディング ソースの推論を無効にするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-235">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="b5df5-236">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-236">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="b5df5-237">マルチパート/フォーム データ要求の推論</span><span class="sxs-lookup"><span data-stu-id="b5df5-237">Multipart/form-data request inference</span></span>

<span data-ttu-id="b5df5-238">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 属性を使用してアクション パラメーターに注釈を付けた場合、`[ApiController]` 属性が推論規則に適用されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-238">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="b5df5-239">`multipart/form-data` 要求コンテンツ型が推論されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-239">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="b5df5-240">既定の動作を無効にするには、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-240">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="b5df5-241">エラー状態コードに関する問題の詳細</span><span class="sxs-lookup"><span data-stu-id="b5df5-241">Problem details for error status codes</span></span>

<span data-ttu-id="b5df5-242">互換性バージョンが 2.2 以上である場合、MVC によってエラー結果 (状態コードが 400 以上の結果) が <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> を含む結果に変換されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-242">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="b5df5-243">`ProblemDetails` 型は、HTTP 応答でコンピューターが判読できるエラーの詳細を提供するための [RFC 7807 仕様](https://tools.ietf.org/html/rfc7807)に基づきます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-243">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="b5df5-244">コントローラー アクションで次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="b5df5-244">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="b5df5-245">`NotFound` メソッドにより、`ProblemDetails` 本文を使用して HTTP 404 ステータス コードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-245">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="b5df5-246">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-246">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a><span data-ttu-id="b5df5-247">ProblemDetails 応答をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="b5df5-247">Customize ProblemDetails response</span></span>

<span data-ttu-id="b5df5-248">`ProblemDetails` の応答の内容を構成するには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-248">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="b5df5-249">たとえば、次のコードにより、404 応答の `type` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="b5df5-249">For example, the following code updates the `type` property for 404 responses:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

### <a name="disable-problemdetails-response"></a><span data-ttu-id="b5df5-250">ProblemDetails 応答を無効にする</span><span class="sxs-lookup"><span data-stu-id="b5df5-250">Disable ProblemDetails response</span></span>

<span data-ttu-id="b5df5-251"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> プロパティが `true` に設定されている場合、`ProblemDetails` インスタンスの自動作成は無効になります。</span><span class="sxs-lookup"><span data-stu-id="b5df5-251">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> property is set to `true`.</span></span> <span data-ttu-id="b5df5-252">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b5df5-252">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b5df5-253">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b5df5-253">Additional resources</span></span> 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
