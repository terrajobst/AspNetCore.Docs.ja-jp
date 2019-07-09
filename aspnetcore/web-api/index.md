---
title: ASP.NET Core を使って Web API を作成する
author: scottaddie
description: ASP.NET Core での Web API の作成の基本について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/08/2019
uid: web-api/index
ms.openlocfilehash: 4f9c334f74dd2a8b7c31c7a42703fa361ccf9139
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622796"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="d5209-103">ASP.NET Core を使って Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="d5209-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="d5209-104">作成者: [Scott Addie](https://github.com/scottaddie)、[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d5209-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="d5209-105">ASP.NET Core では、C# を使った RESTful サービス (別名: Web API) の作成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d5209-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="d5209-106">要求を処理するために、Web API ではコントローラーを使用します。</span><span class="sxs-lookup"><span data-stu-id="d5209-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="d5209-107">Web API の "*コントローラー*" は `ControllerBase` から派生するクラスです。</span><span class="sxs-lookup"><span data-stu-id="d5209-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="d5209-108">この記事では、コントローラーを使って API 要求を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d5209-108">This article shows how to use controllers for handling API requests.</span></span>

<span data-ttu-id="d5209-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)します。</span><span class="sxs-lookup"><span data-stu-id="d5209-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="d5209-110">([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d5209-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="d5209-111">ControllerBase クラス</span><span class="sxs-lookup"><span data-stu-id="d5209-111">ControllerBase class</span></span>

<span data-ttu-id="d5209-112">Web API には、<xref:Microsoft.AspNetCore.Mvc.ControllerBase> から派生したコントローラー クラスが 1 つ以上含まれます。</span><span class="sxs-lookup"><span data-stu-id="d5209-112">A web API has one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="d5209-113">たとえば、Web API のプロジェクト テンプレートでは Values コントローラーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d5209-113">For example, the web API project template creates a Values controller:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

<span data-ttu-id="d5209-114"><xref:Microsoft.AspNetCore.Mvc.Controller> クラスから派生させて Web API のコントローラーを作成しないでください。</span><span class="sxs-lookup"><span data-stu-id="d5209-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="d5209-115">`ControllerBase` から派生した `Controller` にはビューのサポートが追加されるため、これは Web API 要求ではなく Web ページを処理するためのものです。</span><span class="sxs-lookup"><span data-stu-id="d5209-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span>  <span data-ttu-id="d5209-116">このルールには例外があります。ビューと API の両方で同じコントローラーを使うことを計画している場合は、`Controller` から派生させます。</span><span class="sxs-lookup"><span data-stu-id="d5209-116">There's an exception to this rule: if you plan to use the same controller for both views and APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="d5209-117">`ControllerBase` クラスには、HTTP 要求の処理に役立つプロパティとメソッドが多数用意されています。</span><span class="sxs-lookup"><span data-stu-id="d5209-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="d5209-118">たとえば、`ControllerBase.CreatedAtAction` では状態コード 201 が返されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 <span data-ttu-id="d5209-119">次に、`ControllerBase` に用意されているメソッドの例をさらにいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="d5209-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="d5209-120">メソッド</span><span class="sxs-lookup"><span data-stu-id="d5209-120">Method</span></span>  |<span data-ttu-id="d5209-121">メモ</span><span class="sxs-lookup"><span data-stu-id="d5209-121">Notes</span></span>  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="d5209-122">400 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="d5209-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |<span data-ttu-id="d5209-123">404 状態コードを返します。</span><span class="sxs-lookup"><span data-stu-id="d5209-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="d5209-124">ファイルを返します。</span><span class="sxs-lookup"><span data-stu-id="d5209-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="d5209-125">[モデル バインド](xref:mvc/models/model-binding)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d5209-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="d5209-126">[モデル検証](xref:mvc/models/validation)を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d5209-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="d5209-127">使用可能なすべてのメソッドとプロパティの一覧については、「<xref:Microsoft.AspNetCore.Mvc.ControllerBase>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d5209-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="d5209-128">属性</span><span class="sxs-lookup"><span data-stu-id="d5209-128">Attributes</span></span>

<span data-ttu-id="d5209-129"><xref:Microsoft.AspNetCore.Mvc> 名前空間には、Web API のコントローラーとアクション メソッドの動作の構成に使用できる属性が用意されています。</span><span class="sxs-lookup"><span data-stu-id="d5209-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="d5209-130">次の例では、属性を使って、受け取る HTTP メソッドと返す状態コードを指定しています。</span><span class="sxs-lookup"><span data-stu-id="d5209-130">The following example uses attributes to specify the HTTP method accepted and the status codes returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="d5209-131">次に、使用できる属性の例をさらにいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="d5209-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="d5209-132">属性</span><span class="sxs-lookup"><span data-stu-id="d5209-132">Attribute</span></span>|<span data-ttu-id="d5209-133">メモ</span><span class="sxs-lookup"><span data-stu-id="d5209-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="d5209-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="d5209-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="d5209-135">コントローラーまたはアクションの URL パターンを指定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="d5209-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="d5209-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="d5209-137">モデル バインドのために含めるプレフィックスとプロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="d5209-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="d5209-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="d5209-139">HTTP GET メソッドをサポートするアクションを特定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-139">Identifies an action that supports the HTTP GET method.</span></span>|
|<span data-ttu-id="d5209-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="d5209-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="d5209-141">アクションが受け取るデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="d5209-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="d5209-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="d5209-143">アクションによって返すデータ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="d5209-144">使用可能な属性を含む一覧については、<xref:Microsoft.AspNetCore.Mvc> 名前空間をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d5209-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="d5209-145">ApiController 属性</span><span class="sxs-lookup"><span data-stu-id="d5209-145">ApiController attribute</span></span>

<span data-ttu-id="d5209-146">[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性をコントローラー クラスに適用して、API に固有の動作を有効にできます。</span><span class="sxs-lookup"><span data-stu-id="d5209-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable API-specific behaviors:</span></span>

* [<span data-ttu-id="d5209-147">属性ルーティング要件</span><span class="sxs-lookup"><span data-stu-id="d5209-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="d5209-148">自動的な HTTP 400 応答</span><span class="sxs-lookup"><span data-stu-id="d5209-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="d5209-149">バインディング ソース パラメーター推論</span><span class="sxs-lookup"><span data-stu-id="d5209-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="d5209-150">マルチパート/フォーム データ要求の推論</span><span class="sxs-lookup"><span data-stu-id="d5209-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="d5209-151">エラー状態コードに関する問題の詳細</span><span class="sxs-lookup"><span data-stu-id="d5209-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="d5209-152">これらの機能では[互換性バージョン](<xref:mvc/compatibility-version>) 2.1 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="d5209-152">These features require a [compatibility version](<xref:mvc/compatibility-version>) of 2.1 or later.</span></span>

### <a name="apicontroller-on-specific-controllers"></a><span data-ttu-id="d5209-153">特定のコントローラーでの ApiController</span><span class="sxs-lookup"><span data-stu-id="d5209-153">ApiController on specific controllers</span></span>

<span data-ttu-id="d5209-154">プロジェクト テンプレートからの次の例のように、`[ApiController]` 属性は特定のコントローラーに適用できます。</span><span class="sxs-lookup"><span data-stu-id="d5209-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a><span data-ttu-id="d5209-155">複数のコントローラーでの ApiController</span><span class="sxs-lookup"><span data-stu-id="d5209-155">ApiController on multiple controllers</span></span>

<span data-ttu-id="d5209-156">複数のコントローラーでこの属性を使う方法の 1 つは、`[ApiController]` 属性で注釈を付けたカスタム基本コントローラー クラスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="d5209-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="d5209-157">次は、カスタム基底クラスとそこから派生したコントローラーを示す例です。</span><span class="sxs-lookup"><span data-stu-id="d5209-157">Here's an example showing a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a><span data-ttu-id="d5209-158">アセンブリでの ApiController</span><span class="sxs-lookup"><span data-stu-id="d5209-158">ApiController on an assembly</span></span>

<span data-ttu-id="d5209-159">[互換性バージョン](<xref:mvc/compatibility-version>)が 2.2 以降に設定されている場合は、`[ApiController]` 属性をアセンブリに適用できます。</span><span class="sxs-lookup"><span data-stu-id="d5209-159">If [compatibility version](<xref:mvc/compatibility-version>) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="d5209-160">この方法での注釈では、アセンブリ内のすべてのコントローラーに Web API の動作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="d5209-161">個別のコントローラーを除外する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="d5209-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="d5209-162">次の例に示すように、アセンブリ レベルの属性を `Startup` クラスに適用します。</span><span class="sxs-lookup"><span data-stu-id="d5209-162">Apply the assembly-level attribute to the `Startup` class as shown in the following example:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="d5209-163">属性ルーティング要件</span><span class="sxs-lookup"><span data-stu-id="d5209-163">Attribute routing requirement</span></span>

<span data-ttu-id="d5209-164">`ApiController` 属性では、属性ルーティング要件が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-164">The `ApiController` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="d5209-165">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d5209-165">For example:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

<span data-ttu-id="d5209-166">`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> または <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> で定義された[規則ルート](xref:mvc/controllers/routing#conventional-routing)経由でアクションにアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="d5209-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

## <a name="automatic-http-400-responses"></a><span data-ttu-id="d5209-167">自動的な HTTP 400 応答</span><span class="sxs-lookup"><span data-stu-id="d5209-167">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="d5209-168">`ApiController` 属性により、モデル検証エラーが発生すると HTTP 400 応答が自動的にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="d5209-168">The `ApiController` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="d5209-169">その結果、アクション メソッド内の次のコードは不要になります。</span><span class="sxs-lookup"><span data-stu-id="d5209-169">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a><span data-ttu-id="d5209-170">既定の BadRequest 応答</span><span class="sxs-lookup"><span data-stu-id="d5209-170">Default BadRequest response</span></span> 

<span data-ttu-id="d5209-171">2\.2 以降の互換性バージョンを使う場合、HTTP 400 応答に対する既定の応答の種類は <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> です。</span><span class="sxs-lookup"><span data-stu-id="d5209-171">With a compatibility version of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="d5209-172">`ValidationProblemDetails` 型は [RFC 7807 仕様](https://tools.ietf.org/html/rfc7807)に準拠しています。</span><span class="sxs-lookup"><span data-stu-id="d5209-172">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

<span data-ttu-id="d5209-173"><xref:Microsoft.AspNetCore.Mvc.SerializableError> に対する既定の応答を変更するには、次の例のように、`Startup.ConfigureServices` で `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-173">To change the default response to <xref:Microsoft.AspNetCore.Mvc.SerializableError>, set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a><span data-ttu-id="d5209-174">BadRequest 応答をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d5209-174">Customize BadRequest response</span></span>

<span data-ttu-id="d5209-175">検証エラーに起因する応答をカスタマイズするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> を使います。</span><span class="sxs-lookup"><span data-stu-id="d5209-175">To customize the response that results from a validation error, use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>.</span></span> <span data-ttu-id="d5209-176">`services.AddMvc().SetCompatibilityVersion` の後に、次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d5209-176">Add the following highlighted code after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="log-automatic-400-responses"></a><span data-ttu-id="d5209-177">自動的な 400 応答を記録する</span><span class="sxs-lookup"><span data-stu-id="d5209-177">Log automatic 400 responses</span></span>

<span data-ttu-id="d5209-178">[「How to log automatic 400 responses on model validation errors (モデル検証エラー時に自動的な 400 応答を記録する方法)」(aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d5209-178">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400"></a><span data-ttu-id="d5209-179">自動的な 400 を無効にする</span><span class="sxs-lookup"><span data-stu-id="d5209-179">Disable automatic 400</span></span>

<span data-ttu-id="d5209-180">自動的な 400 の動作を無効にするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> プロパティを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-180">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="d5209-181">次の強調表示されたコードを、`Startup.ConfigureServices` の `services.AddMvc().SetCompatibilityVersion` の後に追加します。</span><span class="sxs-lookup"><span data-stu-id="d5209-181">Add the following highlighted code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="d5209-182">バインディング ソース パラメーター推論</span><span class="sxs-lookup"><span data-stu-id="d5209-182">Binding source parameter inference</span></span>

<span data-ttu-id="d5209-183">バインディング ソース属性では、アクション パラメーターの値が存在する場所が定義されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-183">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="d5209-184">次のバインディング ソース属性が存在します。</span><span class="sxs-lookup"><span data-stu-id="d5209-184">The following binding source attributes exist:</span></span>

|<span data-ttu-id="d5209-185">属性</span><span class="sxs-lookup"><span data-stu-id="d5209-185">Attribute</span></span>|<span data-ttu-id="d5209-186">バインド ソース</span><span class="sxs-lookup"><span data-stu-id="d5209-186">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="d5209-187">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="d5209-187">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="d5209-188">要求本文</span><span class="sxs-lookup"><span data-stu-id="d5209-188">Request body</span></span> |
|<span data-ttu-id="d5209-189">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="d5209-189">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="d5209-190">要求本文内のフォーム データ</span><span class="sxs-lookup"><span data-stu-id="d5209-190">Form data in the request body</span></span> |
|<span data-ttu-id="d5209-191">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="d5209-191">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="d5209-192">要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="d5209-192">Request header</span></span> |
|<span data-ttu-id="d5209-193">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="d5209-193">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="d5209-194">要求のクエリ文字列パラメーター</span><span class="sxs-lookup"><span data-stu-id="d5209-194">Request query string parameter</span></span> |
|<span data-ttu-id="d5209-195">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="d5209-195">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="d5209-196">現在の要求からのルート データ</span><span class="sxs-lookup"><span data-stu-id="d5209-196">Route data from the current request</span></span> |
|<span data-ttu-id="d5209-197">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="d5209-197">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="d5209-198">アクション パラメーターとして挿入される要求サービス</span><span class="sxs-lookup"><span data-stu-id="d5209-198">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="d5209-199">値に `%2f` (つまり、`/`) が含まれる可能性がある場合は、`[FromRoute]` を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d5209-199">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="d5209-200">`%2f` は `/` にエスケープ解除されません。</span><span class="sxs-lookup"><span data-stu-id="d5209-200">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="d5209-201">値に `%2f` が含まれる可能性がある場合は、`[FromQuery]` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="d5209-201">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="d5209-202">`[ApiController]` 属性や `[FromQuery]` などのバインディング ソース属性がない場合は、ASP.NET Core ランタイムにより複合オブジェクト モデル バインダーの使用が試行されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-202">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="d5209-203">複合オブジェクト モデル バインダーでは、値プロバイダーから定義された順序でデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="d5209-203">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="d5209-204">次の例では、`discontinuedOnly` パラメーター値が要求 URL のクエリ文字列に指定されていることが `[FromQuery]` によって示されています。</span><span class="sxs-lookup"><span data-stu-id="d5209-204">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="d5209-205">`[ApiController]` 属性により、アクション パラメーターの既定のデータ ソースに対する推論規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-205">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="d5209-206">これらの規則により、アクション パラメーターに属性を適用することで手動でバインディング ソースを特定する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="d5209-206">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="d5209-207">バインディング ソースの推論規則は、次のように動作します。</span><span class="sxs-lookup"><span data-stu-id="d5209-207">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="d5209-208">`[FromBody]` は複合型パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-208">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="d5209-209">`[FromBody]` 推論規則に対する例外は、<xref:Microsoft.AspNetCore.Http.IFormCollection> や <xref:System.Threading.CancellationToken> など、特殊な意味を持つ組み込みの複合型です。</span><span class="sxs-lookup"><span data-stu-id="d5209-209">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="d5209-210">バインディング ソース推論コードでは、そのような特殊な型は無視されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-210">The binding source inference code ignores those special types.</span></span> 
* <span data-ttu-id="d5209-211">`[FromForm]` は <xref:Microsoft.AspNetCore.Http.IFormFile> および <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 型のアクション パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-211">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="d5209-212">簡易型またはユーザー定義型に対しては推論されません。</span><span class="sxs-lookup"><span data-stu-id="d5209-212">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="d5209-213">`[FromRoute]` は、ルート テンプレート内のパラメーターと一致する任意のアクション パラメーター名に対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-213">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="d5209-214">複数のルートがアクション パラメーターと一致する場合、ルート値はいずれも `[FromRoute]` と見なされます。</span><span class="sxs-lookup"><span data-stu-id="d5209-214">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="d5209-215">`[FromQuery]` は他の任意のアクション パラメーターに対して推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-215">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="d5209-216">FromBody 推論に関するメモ</span><span class="sxs-lookup"><span data-stu-id="d5209-216">FromBody inference notes</span></span>

<span data-ttu-id="d5209-217">`[FromBody]` は、`string` や `int` などの単純型に対しては推論されません。</span><span class="sxs-lookup"><span data-stu-id="d5209-217">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="d5209-218">そのため、その機能が必要な場合、単純型に対しては `[FromBody]` 属性を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5209-218">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="d5209-219">要求本文からバインドされるパラメーターがアクションに複数ある場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d5209-219">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="d5209-220">たとえば、次のアクション メソッドのシグネチャはすべて例外の原因となります。</span><span class="sxs-lookup"><span data-stu-id="d5209-220">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="d5209-221">複合型であるため、両方に対して `[FromBody]` が推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-221">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="d5209-222">1 つには `[FromBody]` 属性が使われ、もう 1 つは複合型なので推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-222">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="d5209-223">両方で `[FromBody]` 属性が使われます。</span><span class="sxs-lookup"><span data-stu-id="d5209-223">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> <span data-ttu-id="d5209-224">ASP.NET Core 2.1 では、リストや配列などのコレクション型パラメーターが誤って `[FromQuery]` と推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-224">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="d5209-225">これらのパラメーターを要求本文からバインドする場合は、`[FromBody]` 属性を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d5209-225">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="d5209-226">この動作は ASP.NET Core 2.2 以降で修正されており、既定でコレクション型パラメーターが本文からバインドされることが推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-226">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

### <a name="disable-inference-rules"></a><span data-ttu-id="d5209-227">推論規則を無効にする</span><span class="sxs-lookup"><span data-stu-id="d5209-227">Disable inference rules</span></span>

<span data-ttu-id="d5209-228">バインディング ソースの推論を無効にするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-228">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="d5209-229">`Startup.ConfigureServices` の `services.AddMvc().SetCompatibilityVersion` の後ろに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d5209-229">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="d5209-230">マルチパート/フォーム データ要求の推論</span><span class="sxs-lookup"><span data-stu-id="d5209-230">Multipart/form-data request inference</span></span>

<span data-ttu-id="d5209-231">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 属性を使用してアクション パラメーターに注釈を付けた場合、`[ApiController]` 属性が推論規則に適用されます。つまり、`multipart/form-data` 要求コンテンツ型が推論されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-231">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute: the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="d5209-232">既定の動作を無効にするには、次の例のように、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d5209-232">To disable the default behavior, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>  to `true` in `Startup.ConfigureServices`, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="d5209-233">エラー状態コードに関する問題の詳細</span><span class="sxs-lookup"><span data-stu-id="d5209-233">Problem details for error status codes</span></span>

<span data-ttu-id="d5209-234">互換性バージョンが 2.2 以上である場合、MVC によってエラー結果 (状態コードが 400 以上の結果) が <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> を含む結果に変換されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-234">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="d5209-235">`ProblemDetails` 型は、HTTP 応答でコンピューターが判読できるエラーの詳細を提供するための [RFC 7807 仕様](https://tools.ietf.org/html/rfc7807)に基づきます。</span><span class="sxs-lookup"><span data-stu-id="d5209-235">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="d5209-236">コントローラー アクションで次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="d5209-236">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="d5209-237">`NotFound` の HTTP 応答には 404 状態コードと `ProblemDetails` の本文が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d5209-237">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="d5209-238">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d5209-238">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a><span data-ttu-id="d5209-239">ProblemDetails 応答をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="d5209-239">Customize ProblemDetails response</span></span>

<span data-ttu-id="d5209-240">`ProblemDetails` の応答の内容を構成するには、`ClientErrorMapping` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="d5209-240">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="d5209-241">たとえば、次のコードにより、404 応答の `type` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="d5209-241">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a><span data-ttu-id="d5209-242">ProblemDetails 応答を無効にする</span><span class="sxs-lookup"><span data-stu-id="d5209-242">Disable ProblemDetails response</span></span>

<span data-ttu-id="d5209-243">`SuppressMapClientErrors` プロパティが `true` に設定されている場合、`ProblemDetails` の自動作成は無効になります。</span><span class="sxs-lookup"><span data-stu-id="d5209-243">The automatic creation of `ProblemDetails` is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="d5209-244">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d5209-244">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a><span data-ttu-id="d5209-245">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d5209-245">Additional resources</span></span> 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
