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
# <a name="create-web-apis-with-aspnet-core"></a>ASP.NET Core を使って Web API を作成する

作成者: [Scott Addie](https://github.com/scottaddie)、[Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core では、C# を使った RESTful サービス (別名: Web API) の作成がサポートされています。 要求を処理するために、Web API ではコントローラーを使用します。 Web API の "*コントローラー*" は `ControllerBase` から派生するクラスです。 この記事では、コントローラーを使って API 要求を処理する方法について説明します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)します。 ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="controllerbase-class"></a>ControllerBase クラス

Web API には、<xref:Microsoft.AspNetCore.Mvc.ControllerBase> から派生したコントローラー クラスが 1 つ以上含まれます。 たとえば、Web API のプロジェクト テンプレートでは Values コントローラーを作成します。

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

<xref:Microsoft.AspNetCore.Mvc.Controller> クラスから派生させて Web API のコントローラーを作成しないでください。 `ControllerBase` から派生した `Controller` にはビューのサポートが追加されるため、これは Web API 要求ではなく Web ページを処理するためのものです。  このルールには例外があります。ビューと API の両方で同じコントローラーを使うことを計画している場合は、`Controller` から派生させます。

`ControllerBase` クラスには、HTTP 要求の処理に役立つプロパティとメソッドが多数用意されています。 たとえば、`ControllerBase.CreatedAtAction` では状態コード 201 が返されます。

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 次に、`ControllerBase` に用意されているメソッドの例をさらにいくつか示します。

|メソッド  |メモ  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| 400 状態コードを返します。|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |404 状態コードを返します。|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|ファイルを返します。|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|[モデル バインド](xref:mvc/models/model-binding)を呼び出します。|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|[モデル検証](xref:mvc/models/validation)を呼び出します。|

使用可能なすべてのメソッドとプロパティの一覧については、「<xref:Microsoft.AspNetCore.Mvc.ControllerBase>」を参照してください。

## <a name="attributes"></a>属性

<xref:Microsoft.AspNetCore.Mvc> 名前空間には、Web API のコントローラーとアクション メソッドの動作の構成に使用できる属性が用意されています。 次の例では、属性を使って、受け取る HTTP メソッドと返す状態コードを指定しています。

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

次に、使用できる属性の例をさらにいくつか示します。

|属性|メモ|
|---------|-----|
|[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |コントローラーまたはアクションの URL パターンを指定します。|
|[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |モデル バインドのために含めるプレフィックスとプロパティを指定します。|
|[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |HTTP GET メソッドをサポートするアクションを特定します。|
|[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|アクションが受け取るデータ型を指定します。|
|[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|アクションによって返すデータ型を指定します。|

使用可能な属性を含む一覧については、<xref:Microsoft.AspNetCore.Mvc> 名前空間をご覧ください。

## <a name="apicontroller-attribute"></a>ApiController 属性

[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性をコントローラー クラスに適用して、API に固有の動作を有効にできます。

* [属性ルーティング要件](#attribute-routing-requirement)
* [自動的な HTTP 400 応答](#automatic-http-400-responses)
* [バインディング ソース パラメーター推論](#binding-source-parameter-inference)
* [マルチパート/フォーム データ要求の推論](#multipartform-data-request-inference)
* [エラー状態コードに関する問題の詳細](#problem-details-for-error-status-codes)

これらの機能では[互換性バージョン](<xref:mvc/compatibility-version>) 2.1 以降が必要です。

### <a name="apicontroller-on-specific-controllers"></a>特定のコントローラーでの ApiController

プロジェクト テンプレートからの次の例のように、`[ApiController]` 属性は特定のコントローラーに適用できます。

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a>複数のコントローラーでの ApiController

複数のコントローラーでこの属性を使う方法の 1 つは、`[ApiController]` 属性で注釈を付けたカスタム基本コントローラー クラスを作成することです。 次は、カスタム基底クラスとそこから派生したコントローラーを示す例です。

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a>アセンブリでの ApiController

[互換性バージョン](<xref:mvc/compatibility-version>)が 2.2 以降に設定されている場合は、`[ApiController]` 属性をアセンブリに適用できます。 この方法での注釈では、アセンブリ内のすべてのコントローラーに Web API の動作が適用されます。 個別のコントローラーを除外する方法はありません。 次の例に示すように、アセンブリ レベルの属性を `Startup` クラスに適用します。

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

## <a name="attribute-routing-requirement"></a>属性ルーティング要件

`ApiController` 属性では、属性ルーティング要件が作成されます。 次に例を示します。

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

`Startup.Configure` の <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> または <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> で定義された[規則ルート](xref:mvc/controllers/routing#conventional-routing)経由でアクションにアクセスすることはできません。

## <a name="automatic-http-400-responses"></a>自動的な HTTP 400 応答

`ApiController` 属性により、モデル検証エラーが発生すると HTTP 400 応答が自動的にトリガーされます。 その結果、アクション メソッド内の次のコードは不要になります。

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a>既定の BadRequest 応答 

2\.2 以降の互換性バージョンを使う場合、HTTP 400 応答に対する既定の応答の種類は <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> です。 `ValidationProblemDetails` 型は [RFC 7807 仕様](https://tools.ietf.org/html/rfc7807)に準拠しています。

<xref:Microsoft.AspNetCore.Mvc.SerializableError> に対する既定の応答を変更するには、次の例のように、`Startup.ConfigureServices` で `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` プロパティを `true` に設定します。

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a>BadRequest 応答をカスタマイズする

検証エラーに起因する応答をカスタマイズするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> を使います。 `services.AddMvc().SetCompatibilityVersion` の後に、次の強調表示されたコードを追加します。

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="log-automatic-400-responses"></a>自動的な 400 応答を記録する

[「How to log automatic 400 responses on model validation errors (モデル検証エラー時に自動的な 400 応答を記録する方法)」(aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157) を参照してください。

### <a name="disable-automatic-400"></a>自動的な 400 を無効にする

自動的な 400 の動作を無効にするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> プロパティを `true` に設定します。 次の強調表示されたコードを、`Startup.ConfigureServices` の `services.AddMvc().SetCompatibilityVersion` の後に追加します。

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a>バインディング ソース パラメーター推論

バインディング ソース属性では、アクション パラメーターの値が存在する場所が定義されます。 次のバインディング ソース属性が存在します。

|属性|バインド ソース |
|---------|---------|
|[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | 要求本文 |
|[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | 要求本文内のフォーム データ |
|[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | 要求ヘッダー |
|[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | 要求のクエリ文字列パラメーター |
|[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | 現在の要求からのルート データ |
|[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | アクション パラメーターとして挿入される要求サービス |

> [!WARNING]
> 値に `%2f` (つまり、`/`) が含まれる可能性がある場合は、`[FromRoute]` を使用しないでください。 `%2f` は `/` にエスケープ解除されません。 値に `%2f` が含まれる可能性がある場合は、`[FromQuery]` を使用してください。

`[ApiController]` 属性や `[FromQuery]` などのバインディング ソース属性がない場合は、ASP.NET Core ランタイムにより複合オブジェクト モデル バインダーの使用が試行されます。 複合オブジェクト モデル バインダーでは、値プロバイダーから定義された順序でデータを取得します。

次の例では、`discontinuedOnly` パラメーター値が要求 URL のクエリ文字列に指定されていることが `[FromQuery]` によって示されています。

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

`[ApiController]` 属性により、アクション パラメーターの既定のデータ ソースに対する推論規則が適用されます。 これらの規則により、アクション パラメーターに属性を適用することで手動でバインディング ソースを特定する必要がなくなります。 バインディング ソースの推論規則は、次のように動作します。

* `[FromBody]` は複合型パラメーターに対して推論されます。 `[FromBody]` 推論規則に対する例外は、<xref:Microsoft.AspNetCore.Http.IFormCollection> や <xref:System.Threading.CancellationToken> など、特殊な意味を持つ組み込みの複合型です。 バインディング ソース推論コードでは、そのような特殊な型は無視されます。 
* `[FromForm]` は <xref:Microsoft.AspNetCore.Http.IFormFile> および <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 型のアクション パラメーターに対して推論されます。 簡易型またはユーザー定義型に対しては推論されません。
* `[FromRoute]` は、ルート テンプレート内のパラメーターと一致する任意のアクション パラメーター名に対して推論されます。 複数のルートがアクション パラメーターと一致する場合、ルート値はいずれも `[FromRoute]` と見なされます。
* `[FromQuery]` は他の任意のアクション パラメーターに対して推論されます。

### <a name="frombody-inference-notes"></a>FromBody 推論に関するメモ

`[FromBody]` は、`string` や `int` などの単純型に対しては推論されません。 そのため、その機能が必要な場合、単純型に対しては `[FromBody]` 属性を使用する必要があります。

要求本文からバインドされるパラメーターがアクションに複数ある場合は、例外がスローされます。 たとえば、次のアクション メソッドのシグネチャはすべて例外の原因となります。

* 複合型であるため、両方に対して `[FromBody]` が推論されます。

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* 1 つには `[FromBody]` 属性が使われ、もう 1 つは複合型なので推論されます。

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* 両方で `[FromBody]` 属性が使われます。

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> ASP.NET Core 2.1 では、リストや配列などのコレクション型パラメーターが誤って `[FromQuery]` と推論されます。 これらのパラメーターを要求本文からバインドする場合は、`[FromBody]` 属性を使用する必要があります。 この動作は ASP.NET Core 2.2 以降で修正されており、既定でコレクション型パラメーターが本文からバインドされることが推論されます。

### <a name="disable-inference-rules"></a>推論規則を無効にする

バインディング ソースの推論を無効にするには、<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> を `true` に設定します。 `Startup.ConfigureServices` の `services.AddMvc().SetCompatibilityVersion` の後ろに次のコードを追加します。

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a>マルチパート/フォーム データ要求の推論

[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 属性を使用してアクション パラメーターに注釈を付けた場合、`[ApiController]` 属性が推論規則に適用されます。つまり、`multipart/form-data` 要求コンテンツ型が推論されます。

既定の動作を無効にするには、次の例のように、`Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> を `true` に設定します。

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a>エラー状態コードに関する問題の詳細

互換性バージョンが 2.2 以上である場合、MVC によってエラー結果 (状態コードが 400 以上の結果) が <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> を含む結果に変換されます。 `ProblemDetails` 型は、HTTP 応答でコンピューターが判読できるエラーの詳細を提供するための [RFC 7807 仕様](https://tools.ietf.org/html/rfc7807)に基づきます。

コントローラー アクションで次のコードがあるとします。

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

`NotFound` の HTTP 応答には 404 状態コードと `ProblemDetails` の本文が含まれています。 次に例を示します。

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a>ProblemDetails 応答をカスタマイズする

`ProblemDetails` の応答の内容を構成するには、`ClientErrorMapping` プロパティを使用します。 たとえば、次のコードにより、404 応答の `type` プロパティが更新されます。

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a>ProblemDetails 応答を無効にする

`SuppressMapClientErrors` プロパティが `true` に設定されている場合、`ProblemDetails` の自動作成は無効になります。 `Startup.ConfigureServices` に次のコードを追加します。

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a>その他の技術情報 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
