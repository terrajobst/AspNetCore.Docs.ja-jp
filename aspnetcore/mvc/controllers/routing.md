---
title: ASP.NET Core でのコントローラー アクションへのルーティング
author: rick-anderson
description: ASP.NET Core MVC でルーティング ミドルウェアを使って、受信した要求の URL を照合し、アクションにマップする方法について説明します。
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c1c0d978714718af1de0f627e50a54f66ed391ed
ms.sourcegitcommit: 4b166b49ec557a03f99f872dd069ca5e56faa524
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80362645"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core でのコントローラー アクションへのルーティング

[ライアン Nowak](https://github.com/rynowak)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core コントローラーは、ルーティング[ミドルウェア](xref:fundamentals/middleware/index)を使用して受信要求の url を照合し、[アクション](#action)にマップします。  ルートテンプレート:

* はスタートアップコードまたは属性で定義されます。
* URL パスと[アクション](#action)の照合方法を記述します。
* リンクの Url を生成するために使用されます。 生成されたリンクは通常、応答で返されます。

アクションは、[慣例的にルーティング](#cr)されるか、または[属性ルーティング](#ar)されます。 コントローラーまたは[アクション](#action)にルートを配置すると、ルートが属性ルーティングされます。 詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。

このドキュメント:

* MVC とルーティングの間の相互作用について説明します。
  * 一般的な MVC アプリでルーティング機能を利用する方法。
  * 次の両方について説明します。
    * 通常、[従来のルーティング](#cr)では、コントローラーとビューが使用されます。
    * REST Api で使用される*属性ルーティング*。 REST Api のルーティングに主に関心がある場合は、「 [Rest api の属性ルーティング](#ar)」セクションに進んでください。
  * 詳細については、「[ルーティング](xref:fundamentals/routing)の詳細」を参照してください。
* ASP.NET Core 3.0 で追加された既定のルーティングシステムを指します (エンドポイントルーティングと呼ばれます)。 以前のバージョンのルーティングでは、互換性のためにコントローラーを使用することができます。 手順については、 [2.2-3.0 移行ガイド](xref:migration/22-to-30)を参照してください。 レガシルーティングシステムのリファレンス資料については、[このドキュメントの2.2 バージョン](xref:mvc/controllers/routing?view=aspnetcore-2.2)を参照してください。

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>従来のルートの設定

通常、`Startup.Configure` は、通常の[ルーティング](#crd)を使用するときに、次のようなコードを使用します。

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>の呼び出しの内部では、<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> を使用して1つのルートを作成します。 1つのルートには `default` route という名前が付けられます。 コントローラーとビューを使用するほとんどのアプリでは、`default` ルートと同様のルートテンプレートが使用されます。 REST Api では、[属性ルーティング](#ar)を使用する必要があります。

ルートテンプレート `"{controller=Home}/{action=Index}/{id?}"`:

* 次のような URL パスと一致し `/Products/Details/5`
* パスをトークン化して `{ controller = Products, action = Details, id = 5 }` ルート値を抽出します。 アプリに `ProductsController` という名前のコントローラーと `Details` アクションがある場合、ルート値を抽出した結果が一致します。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

 [Mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。

  * `/Products/Details/5` モデルは、`id = 5` の値をバインドして、`id` パラメーターを `5`に設定します。 詳細については、「[モデルバインド](xref:mvc/models/model-binding)」を参照してください。
* `{controller=Home}` は、`Home` を既定の `controller`として定義します。
* `{action=Index}` は、`Index` を既定の `action`として定義します。
*  `{id?}` の `?` 文字は、`id` を省略可能として定義します。
  * 既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。 ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。
* URL パス `/`と一致します。
* `{ controller = Home, action = Index }`ルート値を生成します。
* [Mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。

`controller` と `action` の値によって、既定値が使用されます。 URL パスに対応するセグメントがないため、`id` は値を生成しません。 `/` は、`HomeController` と `Index` アクションが存在する場合にのみ一致します。

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

前のコントローラー定義とルートテンプレートを使用して、次の URL パスに対して `HomeController.Index` アクションが実行されます。

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

`/` URL パスでは、ルートテンプレートの既定の `Home` コントローラーと `Index` アクションを使用します。 ルートテンプレートの既定 `Index` アクションを使用 `/Home` URL パス。

便利なメソッド <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

もの

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

ルーティングは、<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> と <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ミドルウェアを使用して構成されます。 コントローラーを使用するには:

* [属性ルーティング](#ar)コントローラーをマップするには `UseEndpoints` 内で <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> を呼び出します。
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> または <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>を呼び出して、[従来のルーティング](#cr)コントローラーをマップします。

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>規則ルーティング

従来のルーティングは、コントローラーとビューで使用されます。 次は `default` ルートです。

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

これは、"*規則ルーティング*" の例です。 これは、URL パスの*規則*を確立するため、*従来のルーティング*と呼ばれます。

* 最初のパスセグメントである `{controller=Home}`は、コントローラー名にマップされます。
* 2番目のセグメントである `{action=Index}`は、[アクション](#action)名にマップされます。
* 3番目のセグメント `{id?}` は、オプションの `id`に使用されます。 `{id?}` の `?` によってオプションが作成されます。 `id` は、モデルエンティティにマップするために使用されます。

この `default` ルートを使用して、URL パスを次のようにします。

* `/Products/List` `ProductsController.List` アクションにマップされます。
* `/Blog/Article/17` は `BlogController.Article` にマップされ、通常、モデルは `id` パラメーターを17にバインドします。

このマッピングは次のとおりです。

* は、コントローラー名と[アクション](#action)名**のみ**に基づいています。
* は、名前空間、ソースファイルの場所、またはメソッドのパラメーターに基づいていません。

既定のルートで従来のルーティングを使用すると、各アクションの新しい URL パターンを使用しなくてもアプリを作成できます。 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)スタイルのアクションを使用するアプリの場合、コントローラー間で url の一貫性を確保します。

* コードを簡略化するのに役立ちます。
* UI の予測可能性を向上させます。

> [!WARNING]
> 前のコードの `id` は、ルートテンプレートによってオプションとして定義されます。 アクションは、URL の一部として指定された省略可能な ID なしで実行できます。 一般に、URL から`id` を省略すると、次のようになります。
>
> * `id` は、モデルバインドによって `0` するように設定されています。
> * `id == 0`一致するエンティティがデータベースに見つかりません。
>
> [属性ルーティング](#ar)を使用すると、他のアクションではなく、一部のアクションに必要な ID を作成できます。 慣例により、ドキュメントには、正しい使用状況で表示される可能性がある `id` のような省略可能なパラメーターが含まれています。

ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。 既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:

* 基本的でわかりやすいルーティング スキームをサポートしています。
* UI ベースのアプリの便利な開始点となります。
* は、多くの web UI アプリに必要な唯一のルートテンプレートです。 大規模な web UI アプリの場合は、必要な場合は、[領域](#areas)を使用する別のルートがあります。

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> と <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>:

* 呼び出された順序に基づいて、エンドポイントに**注文**値を自動的に割り当てます。

ASP.NET Core 3.0 以降でのエンドポイントのルーティング:

* ルートの概念はありません。
* は、拡張性の実行に対する順序の保証を提供しません。すべてのエンドポイントが一度に処理されます。

[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。

[属性ルーティング](#ar)については、このドキュメントで後ほど説明します。

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>複数の従来のルート

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> と <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>に対する呼び出しをさらに追加することで、複数の[従来のルート](#cr)を `UseEndpoints` 内部に追加できます。 これにより、複数の規則を定義したり、次のような特定の[アクション](#action)専用の通常のルートを追加したりすることができます。

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

前のコードの `blog` ルートは、**専用の従来のルート**です。 これは、次の理由で専用の従来のルートと呼ばれます。

* 従来の[ルーティング](#cr)を使用します。
* これは特定の[アクション](#action)専用です。

`controller` と `action` がパラメーターとしてルートテンプレート `"blog/{*article}"` に表示されないため、次のようになります。

* `{ controller = "Blog", action = "Article" }`既定値のみを持つことができます。
* このルートは、常にアクション `BlogController.Article`にマップされます。

`/Blog`、`/Blog/Article`、および `/Blog/{any-string}` は、ブログルートに一致する唯一の URL パスです。

前の例を次に示します。

* `blog` ルートは最初に追加されるため、`default` ルートよりも優先順位が高くなります。
* とは、URL の一部としてアーティクル名を持つ一般的な[スラグ](https://developer.mozilla.org/docs/Glossary/Slug)スタイルのルーティングの例です。

> [!WARNING]
> ASP.NET Core 3.0 以降では、ルーティングは次のようになります。
> * *ルート*と呼ばれる概念を定義します。 `UseRouting` は、ミドルウェアパイプラインにルート一致を追加します。 `UseRouting` ミドルウェアは、アプリで定義されているエンドポイントのセットを調べ、要求に基づいて最適なエンドポイント一致を選択します。
> * <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> や <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>などの機能拡張の実行順序についての保証を提供します。
>
>ルーティングのリファレンス資料については、「[ルーティング](xref:fundamentals/routing)」を参照してください。

<a name="cro"></a>

### <a name="conventional-routing-order"></a>従来のルーティング順序

従来のルーティングは、アプリによって定義されたアクションとコントローラーの組み合わせにのみ一致します。 これは、通常のルートが重複するケースを簡略化することを目的としています。
<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>、<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>、および <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> を使用してルートを追加すると、呼び出された順序に基づいて、そのエンドポイントに注文の値が自動的に割り当てられます。 以前に表示されたルートからの一致は、優先順位が高くなります。 規則ルーティングは順序に依存します。 一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。 `{*article}` のようなすべてのルートパラメーターをキャッチした[専用](#dcr)のルートでは、ルートの[最長](xref:fundamentals/routing#greedy)長を指定できます。つまり、他のルートと一致するように意図した url と一致します。 最短一致の一致を防ぐために、ルートテーブルの中で最長一致のルートを指定します。

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>あいまいなアクションの解決

ルーティングを通じて2つのエンドポイントが一致する場合、ルーティングでは次のいずれかを実行する必要があります。

* 最適な候補を選択します。
* 例外をスローします。

例 :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

前のコントローラーは、次の2つのアクションに一致するアクションを定義します。

* URL パス `/Products33/Edit/17`
* データ `{ controller = Products33, action = Edit, id = 17 }`をルーティングします。

これは、MVC コントローラーの一般的なパターンです。

* `Edit(int)` には、製品を編集するためのフォームが表示されます。
* `Edit(int, Product)` は、ポストされたフォームを処理します。

正しいルートを解決するには、次のようにします。

* 要求が HTTP `POST`の場合は `Edit(int, Product)` が選択されます。
* [HTTP 動詞](#verb)が他のものである場合は `Edit(int)` が選択されます。 `Edit(int)` は、一般に `GET`経由で呼び出されます。

<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、`[HttpPost]`は、要求の HTTP メソッドに基づいて選択できるように、ルーティングのために用意されています。 `HttpPostAttribute` によって、`Edit(int)`よりも `Edit(int, Product)` の方が適しています。

`HttpPostAttribute`のような属性の役割を理解しておくことが重要です。 同様の属性は、他の[HTTP 動詞](#verb)に対して定義されます。 [従来のルーティング](#cr)では、表示フォーム、送信フォームワークフローの一部である場合、アクションは同じアクション名を使用するのが一般的です。 例については、「 [2 つの編集アクションメソッドの確認](xref:tutorials/first-mvc-app/controller-methods-views#get-post)」を参照してください。

ルーティングで最適な候補を選択できない場合は、一致する複数のエンドポイントを一覧表示する <xref:System.Reflection.AmbiguousMatchException> がスローされます。

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>従来のルート名

次の例の文字列 `"blog"` と `"default"` は、通常のルート名です。

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

ルート名によって、ルートに論理名が指定されます。 名前付きルートは、URL の生成に使用できます。 ルートの順序によって URL の生成が複雑になる可能性がある場合、名前付きルートを使用すると、URL の作成が簡単になります。 ルート名は、アプリケーション全体で一意である必要があります。

ルート名:

* URL の照合や要求の処理には影響しません。
* は、URL の生成にのみ使用されます。

ルート名の概念は、ルーティングで[IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)として表されます。 **ルート名**と**エンドポイント名**の用語:

* 交換可能です。
* ドキュメントとコードで使用されるものは、記述されている API によって異なります。

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>REST Api の属性ルーティング

REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が[HTTP 動詞](#verb)によって表されるようにする必要があります。

属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。 次の `StartUp.Configure` コードは、REST API の一般的なもので、次のサンプルで使用します。

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

前のコードでは、属性ルーティングコントローラーをマップするために、`UseEndpoints` 内部で <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> が呼び出されます。

たとえば、行が次のように表示されているとします。

* 上記の `Configure` メソッドが使用されます。
* `HomeController` は、既定の従来のルート `{controller=Home}/{action=Index}/{id?}` 一致と同様の Url のセットと一致します。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

`HomeController.Index` アクションは、URL パス `/`、`/Home`、`/Home/Index`、または `/Home/Index/3`に対して実行されます。

この例では、属性ルーティングと[従来のルーティング](#cr)の主なプログラミングの違いについて取り上げます。 属性ルーティングでは、ルートを指定するためにより多くの入力が必要です。 従来の既定のルートは、ルートをより簡潔に処理します。 ただし、属性ルーティングでは、各[アクション](#action)に適用されるルートテンプレートを正確に制御する必要があります。

属性ルーティングでは、コントローラー名とアクション名は、アクションが一致する役割を果たし**ません**。 次の例は、前の例と同じ Url に一致します。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

次のコードでは、`action` と `controller`にトークン置換を使用しています。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

次のコードは、コントローラーに `[Route("[controller]/[action]")]` を適用します。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

前のコードでは、`Index` メソッドテンプレートをルートテンプレートに `/` または `~/` する必要があります。 `/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。

ルートテンプレートの選択の詳細については、[ルートテンプレートの優先順位](xref:fundamentals/routing#rtp)に関するセクションを参照してください。

## <a name="reserved-routing-names"></a>ルーティングの予約名

コントローラーまたは Razor Pages を使用する場合は、次のキーワードが予約されたルートパラメーター名です。

* `action`
* `area`
* `controller`
* `handler`
* `page`

属性ルーティングを使用してルートパラメーターとして `page` を使用することは、一般的なエラーです。 その結果、URL の生成によって一貫性のない動作がわかりにくくなります。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

特別なパラメーター名は、url 生成操作が Razor ページとコントローラーのどちらを参照するかを決定するために、URL 生成によって使用されます。

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP 動詞テンプレート

ASP.NET Core には、次の HTTP 動詞テンプレートがあります。

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>ルート テンプレート

ASP.NET Core には、次のルートテンプレートがあります。

* すべての[HTTP 動詞テンプレート](#verb)はルートテンプレートです。
* [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Http 動詞属性を使用した属性ルーティング

次のコントローラーを考えてみましょう。

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

上のコードでは以下の操作が行われます。

* 各アクションには `[HttpGet]` 属性が含まれています。これにより、一致する HTTP GET 要求のみが制限されます。
* `GetProduct` アクションには `"{id}"` テンプレートが含まれているため、`id` はコントローラーの `"api/[controller]"` テンプレートに追加されます。 メソッドテンプレートは `"api/[controller]/"{id}""`。 したがって、このアクションでは、`/api/test2/xyz`、`/api/test2/123`、`/api/test2/{any string}`などの形式の GET 要求のみが一致します。
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* `GetIntProduct` アクションには、`"int/{id:int}")` テンプレートが含まれています。 テンプレートの `:int` の部分は、`id` ルート値を整数に変換できる文字列に制限します。 `/api/test2/int/abc`する GET 要求:
  * このアクションと一致しません。
  * [404 が見つからないこと](https://developer.mozilla.org/docs/Web/HTTP/Status/404)を示すエラーを返します。
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product` アクションには、テンプレート内の `{id}` が含まれていますが、整数に変換できる値に `id` を制限していません。 `/api/test2/int2/abc`する GET 要求:
  * このルートと一致します。
  * モデルバインドが `abc` を整数に変換できません。 メソッドの `id` パラメーターは integer です。
  * モデルバインドが`abc` を整数に変換できなかったため、 [400 の無効な要求](https://developer.mozilla.org/docs/Web/HTTP/Status/400)が返されます。
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

属性ルーティングでは、<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、<xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>、<xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>などの <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> 属性を使用できます。 すべての[HTTP 動詞](#verb)属性は、ルートテンプレートを受け入れます。 次の例は、同じルートテンプレートに一致する2つのアクションを示しています。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

`/products3`URL パスを使用します。

* `MyProductsController.ListProducts` アクションは、 [HTTP 動詞](#verb)が `GET`ときに実行されます。
* `MyProductsController.CreateProduct` アクションは、 [HTTP 動詞](#verb)が `POST`ときに実行されます。

REST API をビルドする場合、アクションはすべての HTTP メソッドを受け入れるため、アクションメソッドで `[Route(...)]` を使用する必要があります。 API がサポートする内容については、より具体的な[HTTP 動詞属性](#verb)を使用することをお勧めします。 REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。

REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が HTTP 動詞によって表されるようにする必要があります。 これは、同じ論理リソースに対する GET や POST などの多くの操作で同じ URL が使用されることを意味します。 属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。

属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。 次の例では、URL パスの一部として `id` が必要です。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

`Products2ApiController.GetProduct(int)` アクション:

* は、のような URL パスを使用して実行され `/products2/3`
* は、URL パス `/products2`では実行されません。

[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性を使用すると、アクションでサポートされる要求のコンテンツの種類を制限できます。 詳細については、「[利用属性を使用したサポートされる要求のコンテンツの種類の定義](xref:web-api/index#consumes)」を参照してください。

 ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。

`[ApiController]`の詳細については、「 [ApiController 属性](xref:web-api/index##apicontroller-attribute)」を参照してください。

## <a name="route-name"></a>ルート名

次のコードでは、`Products_List`のルート名を定義しています。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

ルート名を使うと、特定のルートに基づいて URL を生成できます。 ルート名:

* ルーティングの URL 照合動作には影響しません。
* は、URL の生成にのみ使用されます。

ルート名は、アプリケーション全体で一意である必要があります。

上記のコードと従来の既定のルートを比較します。これにより、`id` のパラメーターが省略可能な (`{id?}`) として定義されます。 Api を正確に指定する機能には、`/products` と `/products/5` を異なるアクションにディスパッチできるようにするなどの利点があります。

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>属性ルートの結合

属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。 コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。 ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

前の例の場合:

* `/products` URL パスを一致させることができ `ProductsApi.ListProducts`
* URL パス `/products/5` は `ProductsApi.GetProduct(int)`と一致できます。

これらのアクションはどちらも、`[HttpGet]` 属性でマークされているので、HTTP `GET` にのみ一致します。

`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。 次の例は、既定のルートに似た URL パスのセットに一致します。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

次の表では、上記のコードの `[Route]` の属性について説明します。

| 属性               | `[Route("Home")]` と結合します | ルートテンプレートを定義します |
| ----------------- | ------------ | --------- |
| `[Route("")]` | はい | `"Home"` |
| `[Route("Index")]` | はい | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | はい | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>属性のルートの順序

ルーティングによってツリーが構築され、すべてのエンドポイントが同時に一致します。

* ルートエントリは、最適な順序で配置されているかのように動作します。
* 最も具体的なルートは、より一般的なルートの前に実行される可能性があります。

たとえば、`blog/search/{topic}` のような属性ルートは、`blog/{*article}`のような属性ルートよりも具体的です。 既定では、`blog/search/{topic}` ルートの方が優先順位が高くなります。 開発者は、[従来のルーティング](#cr)を使用して、ルートを目的の順序で配置します。

属性ルートでは、<xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> プロパティを使用して注文を構成できます。 フレームワークに用意されているすべての[ルート属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)には、`Order` が含まれます。 ルートは、`Order` プロパティの昇順に従って処理されます。 既定の順序は `0` です。 `Order = -1` を使用してルートを設定すると、順序を設定していないルートの前に実行されます。 `Order = 1` を使用したルートの設定は、既定のルートの順序の後に実行されます。

`Order`によっては**避けるよう**にしてください。 アプリの URL 空間で、明示的な順序の値を正しくルーティングする必要がある場合は、クライアントにも混乱を招く可能性があります。 一般に、属性ルーティングでは、URL が一致する正しいルートが選択されます。 URL の生成に使用される既定の順序が機能していない場合、通常は、`Order` プロパティを適用するよりも、ルート名をオーバーライドとして使用する方が簡単です。

次の2つのコントローラーを考えてみます。 `/home`に一致するルートが定義されています。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

前のコードで `/home` を要求すると、次のような例外がスローされます。

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

ルート属性のいずれかに `Order` を追加すると、あいまいさが解消されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

上記のコードでは、`/home` が `HomeController.Index` エンドポイントを実行します。 `MyDemoController.MyIndex`にアクセスするには、`/home/MyIndex`を要求します。 **注**:

* 上記のコードは、ルーティング設計の一例または不十分です。 これは、`Order` プロパティを示すために使用されていました。
* `Order` プロパティはあいまいさを解決するだけで、そのテンプレートは一致しません。 `[Route("Home")]` テンプレートを削除することをお勧めします。

Razor Pages を使用したルートの順序については[、Razor Pages ルートとアプリの規則](xref:razor-pages/razor-pages-conventions#route-order)に関する説明を参照してください。

場合によっては、あいまいなルートで HTTP 500 エラーが返されます。 [ログ](xref:fundamentals/logging/index)を使用して、`AmbiguousMatchException`の原因となったエンドポイントを確認します。

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>ルートテンプレートでのトークンの置換 [controller]、[action]、[area]

便宜上、属性ルートは、次のいずれかのトークンを囲むことによって、予約ルートパラメーターのトークン置換をサポートしています。

* 角かっこ: `[]`
* 中かっこ: `{}`

トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、領域名、およびコントローラー名の値に置き換えられます。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

上のコードでは以下の操作が行われます。

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * 一致する `/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * 一致する `/Products0/Edit/{id}`

属性ルート作成の最後のステップで、トークンの置換が発生します。 前の例では、次のコードと同じ動作が実行されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

属性ルートを継承と組み合わせることもできます。 これは、トークンの置換と組み合わせた強力な機能です。 トークンの置換は、属性ルートで定義されているルート名にも適用されます。
`[Route("[controller]/[action]", Name="[controller]_[action]")]`によって、各アクションの一意のルート名が生成されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

トークンの置換は、属性ルートで定義されているルート名にも適用されます。
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
各アクションの一意のルート名を生成します。

リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする

トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。 パラメーター トランスフォーマーは <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> を実装し、パラメーターの値を変換します。 たとえば、カスタムの `SlugifyParameterTransformer` パラメータートランスフォーマーは、`SubscriptionManagement` ルート値を `subscription-management`に変更します。

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> は、次のようなアプリケーション モデルの規則です。

* パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。
* 置き換えられる属性ルートのトークン値をカスタマイズします。

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

前の `ListAll` メソッドは `/subscription-management/list-all`と一致します。

`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

スラグの定義については、[スラグの MDN web ドキュメント](https://developer.mozilla.org/docs/Glossary/Slug)を参照してください。

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>複数の属性ルート

属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。 この最も一般的な使用方法は、次の例に示すように、既定のルートの動作を模倣することです。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

コントローラーに複数のルート属性を配置することは、それぞれがアクションメソッドの各ルート属性と結合することを意味します。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

すべての[HTTP 動詞](#verb)ルート制約は、`IActionConstraint`を実装します。

<xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> を実装する複数のルート属性がアクションに配置されている場合:

* 各アクション制約は、コントローラーに適用されたルートテンプレートと結合されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

アクションで複数のルートを使用すると便利で強力な場合があります。アプリの URL 空間の基本を明確に定義しておくことをお勧めします。 既存のクライアントをサポートするために必要な場合に**のみ**、アクションで複数のルートを使用します。

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>属性ルートの省略可能なパラメーター、既定値、制約を指定する

属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

上記のコードでは、`[HttpPost("product/{id:int}")]` はルート制約を適用します。 `ProductsController.ShowProduct` アクションは、`/product/3`のような URL パスによってのみ照合されます。 ルートテンプレートの部分は、そのセグメントを整数のみに制限 `{id:int}` ます。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>IRouteTemplateProvider を使用したカスタムルート属性

すべての[ルート属性](#rt)は <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>を実装します。 ASP.NET Core ランタイム:

* アプリの起動時に、コントローラークラスとアクションメソッドの属性を検索します。
* `IRouteTemplateProvider` を実装する属性を使用して、ルートの初期セットを構築します。

`IRouteTemplateProvider` を実装して、カスタムルート属性を定義します。 各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

前の `Get` メソッドは `Order = 2, Template = api/MyTestApi`を返します。

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>アプリケーションモデルを使用して属性ルートをカスタマイズする

アプリケーションモデル:

* は、起動時に作成されるオブジェクトモデルです。
* アプリでアクションをルーティングおよび実行するために ASP.NET Core によって使用されるすべてのメタデータが含まれます。

アプリケーションモデルには、ルート属性から収集されたすべてのデータが含まれます。 ルート属性からのデータは、`IRouteTemplateProvider` の実装によって提供されます。 規定

* アプリケーションモデルを変更して、ルーティングの動作をカスタマイズするために、を記述できます。
* アプリの起動時に読み込まれます。

このセクションでは、アプリケーションモデルを使用してルーティングをカスタマイズする基本的な例を示します。 次のコードでは、ルートがプロジェクトのフォルダー構造とほぼ同じになります。

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

次のコードは、属性がルーティングされているコントローラーに `namespace` 規則が適用されないようにします。

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

たとえば、次のコントローラーは `NamespaceRoutingConvention`を使用しません。

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply` メソッド:

* コントローラーが属性ルーティングされている場合は、何も実行しません。
* `namespace`に基づいてコントローラーテンプレートを設定し、ベース `namespace` 削除します。

`NamespaceRoutingConvention` は `Startup.ConfigureServices`で適用できます。

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

たとえば、次のコントローラーを考えてみます。

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

上のコードでは以下の操作が行われます。

* 基本 `namespace` が `My.Application`。
* 前のコントローラーの完全な名前は `My.Application.Admin.Controllers.UsersController`ます。
* `NamespaceRoutingConvention` は、コントローラーテンプレートを `Admin/Controllers/Users/[action]/{id?`に設定します。

`NamespaceRoutingConvention` は、コントローラーの属性として適用することもできます。

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>混合ルーティング: 属性ルーティングと規則ルーティング

ASP.NET Core アプリでは、従来のルーティングと属性ルーティングの使用を混在させることができます。 一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。

アクションは、規則的にルーティングされるか、または属性でルーティングされます。 コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。 属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。 コントローラー**のルート属性では、コントローラー**属性の**すべて**のアクションがルーティングされます。

属性ルーティングと従来のルーティングでは、同じルーティングエンジンが使用されます。

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL 生成とアンビエント値

アプリでは、ルーティング URL 生成機能を使用して、アクションへの URL リンクを生成できます。 Url を生成することで、Url をハードコーディングすることがなくなり、コードの堅牢性と保守性が向上します。 このセクションでは、MVC によって提供される URL 生成機能について説明し、URL の生成のしくみの基本についてのみ説明します。 URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。

<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> インターフェイスは、MVC と URL 生成のルーティングの間のインフラストラクチャの基盤となる要素です。 `IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビューコンポーネントの `Url` プロパティを使用して取得できます。

次の例では、`Controller.Url` プロパティを介して `IUrlHelper` インターフェイスを使用して、別のアクションへの URL を生成しています。

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

アプリが既定のルートを使用している場合、`url` 変数の値は `/UrlGeneration/Destination`URL パス文字列になります。 この URL パスは、次の組み合わせによってルーティングによって作成されます。

* 現在の要求からのルート値 (**アンビエント値**と呼ばれます)。
* `Url.Action` に渡され、それらの値をルートテンプレートに置き換える値。

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。 値を持たないルートパラメーターには、次のようなものがあります。

* 既定値がある場合は、それを使用します。
* 省略可能な場合はスキップします。 たとえば、ルートテンプレートの `id` `{controller}/{action}/{id?}`です。

必要なルートパラメーターに対応する値がない場合、URL の生成は失敗します。 ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。

上記の `Url.Action` の例では、[従来のルーティング](#cr)が想定されています。 URL の生成は、[属性ルーティング](#ar)と同様に機能しますが、概念は異なります。 従来のルーティングの場合:

* ルート値は、テンプレートを展開するために使用されます。
* `controller` と `action` のルート値は、通常、そのテンプレートに表示されます。 これは、ルーティングによって一致した Url が規則に準拠しているために機能します。

次の例では、属性ルーティングを使用します。

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

前のコードの `Source` アクションでは `custom/url/to/destination`が生成されます。

`IUrlHelper`の代替手段として ASP.NET Core 3.0 に <xref:Microsoft.AspNetCore.Routing.LinkGenerator> が追加されました。 `LinkGenerator` には、同様に柔軟な機能が用意されています。 `IUrlHelper` のメソッドには、`LinkGenerator` に対応するメソッドのファミリもあります。

### <a name="generating-urls-by-action-name"></a>アクション名による URL の生成

[Url. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)、 [Linkgenerator、GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)、およびすべての関連するすべてのオーバーロードは、コントローラー名とアクション名を指定してターゲットエンドポイントを生成するように設計されています。

`Url.Action`を使用する場合、`controller` と `action` の現在のルート値は、ランタイムによって提供されます。

* `controller` と `action` の値は、[アンビエント値](#ambient)と値の両方に含まれます。 メソッド `Url.Action` は常に `action` および `controller` の現在の値を使用し、現在のアクションにルーティングする URL パスを生成します。

ルーティングでは、アンビエント値の値を使用して、URL の生成時に提供されなかった情報を入力しようとします。 アンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }`の `{a}/{b}/{c}/{d}` のようなルートを考えてみます。

* ルーティングには、URL を生成するための十分な情報が含まれており、追加の値は必要ありません。
* すべてのルートパラメーターに値があるため、ルーティングに十分な情報が得られます。

`{ d = Donovan }` 値が追加された場合は、次のようになります。

* `{ d = David }` 値は無視されます。
* 生成された URL パスは `Alice/Bob/Carol/Donovan`。

**警告**: URL パスは階層化されています。 前の例では、`{ c = Cheryl }` 値が追加された場合、次のようになります。

* `{ c = Carol, d = David }` 値は両方とも無視されます。
* `d` の値はなく、URL の生成は失敗します。
* URL を生成するには、`c` と `d` の必要な値を指定する必要があります。  

既定のルート `{controller}/{action}/{id?}`では、この問題が発生する可能性があります。 `Url.Action` は常に `controller` と `action` 値を明示的に指定するため、この問題はほとんどありません。

Url のいくつかのオーバーロードでは、ルート値オブジェクトを使用して、`controller` と `action`以外のルートパラメーターの値を指定し[ます。](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) ルート値オブジェクトは、`id`と共によく使用されます。 たとえば、`Url.Action("Buy", "Products", new { id = 17 })` のようにします。 ルート値オブジェクト:

* 慣例により、通常は匿名型のオブジェクトです。
* には、`IDictionary<>` または[POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)) を指定できます。

ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

上記のコードでは `/Products/Buy/17?color=red`が生成されます。

次のコードでは、絶対 URL が生成されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

絶対 URL を作成するには、次のいずれかを使用します。

* `protocol`を受け入れるオーバーロード。 たとえば、上記のコードを使用します。
* [Linkgenerator. GetUriByAction。](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)既定で絶対 uri を生成します。

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>ルートによる Url の生成

前のコードでは、コントローラーとアクション名を渡すことによって URL を生成することを示していました。 `IUrlHelper` は、メソッドの[Url routeurl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)ファミリも提供します。 これらのメソッドは、 [Url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)に似ていますが、`action` の現在の値と `controller` はルート値にコピーされません。 `Url.RouteUrl`の最も一般的な使用方法は次のとおりです。

* URL を生成するルート名を指定します。
* 通常、コントローラーまたはアクション名は指定しません。

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

次の Razor ファイルでは、`Destination_Route`への HTML リンクが生成されます。

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>HTML および Razor での Url の生成

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> では、`<form>` と `<a>` 要素をそれぞれ生成するために、<xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> メソッド[html.actionlink と html](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) . を[提供して](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*)います。 これらのメソッドは、Url を生成するために[url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)メソッドを使用し、同様の引数を受け取ります。 `Url.RouteUrl` の `HtmlHelper` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。

TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。 これらはどちらも、実装に `IUrlHelper` を使います。 詳細については、「[フォームのタグヘルパー](xref:mvc/views/working-with-forms) 」を参照してください。

ビューの内部では、`IUrlHelper` プロパティを使って任意のアドホック URL 生成に `Url` を使うことができます (上の記事では説明されていません)。

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>アクションの結果での URL の生成

前の例では、コントローラーで `IUrlHelper` を使用する方法を示しました。 コントローラーで最も一般的に使用されるのは、アクションの結果の一部として URL を生成することです。

基底クラス <xref:Microsoft.AspNetCore.Mvc.ControllerBase> および <xref:Microsoft.AspNetCore.Mvc.Controller> では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。 一般的な使用方法の1つは、ユーザー入力を受け入れた後にリダイレクトすることです。

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> や <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> などのアクションの結果ファクトリメソッドは、`IUrlHelper`のメソッドと同様のパターンに従います。

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>専用規則ルートの特殊なケース

[従来のルーティング](#cr)では、[専用の従来のルート](#dcr)と呼ばれる特別な種類のルート定義を使用できます。 次の例では、`blog` という名前のルートが専用の従来のルートになっています。

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

上記のルート定義を使用すると、`Url.Action("Index", "Home")` は `default` ルートを使用して `/` URL パスを生成しますが、その理由は何でしょうか。 ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。

[専用の従来のルート](#dcr)では、ルートパラメーターが指定されていない既定値の特殊な動作に依存しています。これにより、ルートが URL 生成に[最長一致](xref:fundamentals/routing#greedy)するのを防ぐことができます。 この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。 ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。 `{ controller = Home, action = Index }` の値が `{ controller = Blog, action = Article }`と一致しないため、`blog` を使用した URL 生成は失敗します。 そして、ルーティングはフォールバックして `default` を試み、これは成功します。

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>領域

[区分](xref:mvc/controllers/areas)は、関連する機能を別のグループとしてグループにまとめるために使用される MVC 機能です。

* コントローラーアクションのルーティング名前空間。
* ビューのフォルダー構造。

区分を使用すると、異なる領域がある限り、同じ名前の複数のコントローラーをアプリに割り当てることができます。 区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。 ここでは、ルーティングと領域の相互作用について説明します。 領域をビューで使用する方法の詳細については、「[区分](xref:mvc/controllers/areas)」を参照してください。

次の例では、既定の従来のルートと、`Blog`という名前の `area` の `area` ルートを使用するように MVC を構成します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

前のコードでは、`"blog_route"`を作成するために <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> が呼び出されます。 2番目のパラメーター `"Blog"`は、領域名です。

`/Manage/Users/AddUser`のような URL パスと一致すると、`"blog_route"` ルートによって `{ area = Blog, controller = Users, action = AddUser }`ルート値が生成されます。 `area` ルート値は、`area`の既定値によって生成されます。 `MapAreaControllerRoute` によって作成されたルートは、次のようになります。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` は、指定された区分名 (この場合は`area`) を使い、既定値と、`Blog` に対する制約の両方からルートを作成します。 既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。

規則ルーティングは順序に依存します。 一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。

前の例を使用すると、ルート値 `{ area = Blog, controller = Users, action = AddUser }` 次のアクションと一致します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[[区分]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性は、1つの領域の一部としてコントローラーを表します。 このコントローラーは `Blog` 領域にあります。 `[Area]` 属性のないコントローラーは、どの領域のメンバーでもなく、`area` ルート値がルーティングによって指定されている場合は一致しませ**ん**。 次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

各コントローラーの名前空間は、完全を期すためにここに示されています。 前のコントローラーで同じ名前空間が使用されている場合は、コンパイラエラーが生成されます。 クラスの名前空間は、MVC のルーティングに対して影響を与えません。

最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。 3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。

<a name="aa"></a>

"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。

領域内でアクションを実行する場合、`area` のルート値は、URL の生成に使用するルーティングの[アンビエント値](#ambient)として使用できます。 つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

次のコードでは `/Zebra/Users/AddUser`の URL が生成されます。

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>アクションの定義

[非 action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute)属性を持つパブリックメソッドは、アクションです。

## <a name="sample-code"></a>サンプル コード

 * [Mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。
* [サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC は、ルーティング [ミドルウェア](xref:fundamentals/middleware/index)を使って、受信した要求の URL を照合し、アクションにマップします。 ルートは、スタートアップ コードまたは属性で定義されています。 ルートでは、URL パスとアクションの照合方法が記述されています。 また、ルートは、応答で送信される (リンク用) の URL の生成にも使われます。

アクションは、規則的にルーティングされるか、または属性でルーティングされます。 コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。 詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。

このドキュメントでは、MVC とルーティングの間の相互作用、および標準的な MVC アプリでのルーティング機能の利用方法を説明します。 高度なルーティングについて詳しくは、「[ASP.NET Core のルーティング](xref:fundamentals/routing)」をご覧ください。

## <a name="setting-up-routing-middleware"></a>ルーティング ミドルウェアの設定

*Configure* メソッドには、次のようなコードが含まれることがあります。

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` の呼び出しの内部で、`MapRoute` は単一のルートの作成に使われます。これは、`default` ルートと呼ばれます。 ほとんどの MVC アプリは、`default` ルートのようなルートをテンプレートで使います。

ルート テンプレート `"{controller=Home}/{action=Index}/{id?}"` は、`/Products/Details/5` のような URL パスと一致し、パスをトークン化することによってルート値 `{ controller = Products, action = Details, id = 5 }` を抽出します。 MVC は、`ProductsController` という名前のコントローラーを探して、アクション `Details` の実行を試みます。

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

この例では、このアクションを呼び出すときに、モデル バインドが `id = 5` という値を使って、`id` パラメーターを `5` に設定することに注意してください。 詳しくは、「[モデル バインド](../models/model-binding.md)」をご覧ください。

`default` ルートの使用:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

ルート テンプレート:

* `{controller=Home}` は、`Home` を既定の `controller` として定義します

* `{action=Index}` は、`Index` を既定の `action` として定義します

* `{id?}` は、`id` を省略可能として定義します

既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。 ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。

`"{controller=Home}/{action=Index}/{id?}"` は URL パス `/` と一致でき、ルート値 `{ controller = Home, action = Index }` を生成します。 `controller` と `action` の値は既定値を使い、`id` は URL パスに対応するセグメントが存在しないため値を生成しません。 MVC はこれらのルート値を使って、`HomeController` および `Index` アクションを選びます。

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

このコントローラー定義とルート テンプレートを使うと、`HomeController.Index` アクションは次のいずれかの URL パスに対して実行されます。

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

便利なメソッド `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

これは、次の代わりに使うことができます。

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` および `UseMvcWithDefaultRoute` は、`RouterMiddleware` のインスタンスをミドルウェア パイプラインに追加します。 MVC は、ミドルウェアと直接相互作用せず、ルーティングを使って要求を処理します。 MVC は、`MvcRouteHandler` のインスタンスによってルートに接続されます。 `UseMvc` の内部のコードは次のようになります。

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` は、ルートを直接定義することはなく、`attribute` ルートのルート コレクションにプレースホルダーを追加します。 オーバーロード `UseMvc(Action<IRouteBuilder>)` は、独自ルートの追加を可能にし、属性ルーティングをサポートします。  `UseMvc` とそのすべてのバリエーションは、属性ルートのプレースホルダーを追加します。属性ルーティングは、`UseMvc` の構成方法に関係なく常に利用できます。 `UseMvcWithDefaultRoute` は既定のルートを定義し、属性ルーティングをサポートします。 「[属性ルーティング](#attribute-routing-ref-label)」セクションでは、属性ルーティングについてさらに詳しく説明します。

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>規則ルーティング

次は `default` ルートです。

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

前のコードは、従来のルーティングの例です。 このスタイルは、URL パスの*規則*を確立するため、従来のルーティングと呼ばれます。

* 最初のパスセグメントは、コントローラー名にマップされます。
* 2つ目は、アクション名にマップされます。
* 3番目のセグメントは、オプションの `id`に使用されます。 `id` は、モデルエンティティにマップされます。

この `default` ルートを使うと、URL パス `/Products/List` は `ProductsController.List` アクションにマップし、`/Blog/Article/17` は `BlogController.Article` にマップします。 このマッピングは、コントローラーとアクションの名前に**のみ**基づき、名前空間、ソース ファイルの場所、またはメソッドのパラメーターには基づきません。

> [!TIP]
> 既定のルートで規則ルーティングを使うと、定義するアクションごとに新しい URL パターンを考える必要がなく、アプリケーションをすばやく構築できます。 CRUD スタイルのアクションを使うアプリケーションでは、コントローラー間で URL に一貫性を持たせると、コードが容易になり、UI の予測可能性が向上します。

> [!WARNING]
> `id` はルート テンプレートで省略可能と定義されており、これは URL の一部として ID を提供されなくてもアクションを実行できることを意味します。 通常、`id` が URL から省略されると、ID はモデル バインドによって `0` に設定され、結果として、`id == 0` と一致するエンティティはデータベースで検索されません。 属性ルーティングを使うと、ID が必須のアクションと必須ではないアクションをきめ細かく制御できます。 慣例に従って、ドキュメントでは `id` などの省略可能なパラメーターが正しい使用法で使われる可能性がある場合はパラメーターを記載してあります。

## <a name="multiple-routes"></a>複数のルート

`UseMvc` の呼び出しをさらに追加することで、`MapRoute` の内部に複数のルートを追加できます。 これにより、次のように、複数の規則を定義すること、または特定のアクションに専用の規則ルートを追加することができます。

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`blog` ルートは "*専用の規則ルート*" です。これは、このルートは規則ルーティング システムを使いますが、特定のアクション専用であることを意味します。 `controller` と `action` はパラメーターとしてルート テンプレートに表示されないので、既定値を持つことだけができ、したがってこのルートは常に `BlogController.Article` アクションにマップされます。

ルート コレクション内のルートには順序があり、追加された順序で処理されます。 そのため、この例では、`blog` ルートが `default` ルートより先に試されます。

> [!NOTE]
> *専用の従来のルート*では、多くの場合、`{*article}` のような**キャッチオール**ルートパラメーターを使用して、URL パスの残りの部分をキャプチャします。 このようにすると、ルートの "一致範囲が広くなりすぎて"、他のルートと一致させるつもりであった URL まで一致する可能性があります。 この問題を解決するには、"一致範囲の広い" ルートはルート テーブルの後の方に配置します。

### <a name="fallback"></a>フォールバック

要求処理の一環として、MVC はルートの値を使ってアプリケーション内のコントローラーとアクションを検索できることを確認します。 ルートの値がアクションと一致しない場合、そのルートは一致と見なされず、次のルートが試されます。 これは "*フォールバック*" と呼ばれ、規則ルートがオーバーラップしている場合の簡略化を意図したものです。

### <a name="disambiguating-actions"></a>アクションの明確化

2 つのアクションがルーティングで一致する場合、MVC はあいまいさを解消して "最善の" 候補を選ぶか、または例外をスローする必要があります。 例 :

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

このコントローラーでは、URL パス `/Products/Edit/17` およびルート データ `{ controller = Products, action = Edit, id = 17 }` と一致する 2 つのアクションが定義されています。 これは、`Edit(int)` で製品を編集するためのフォームを表示し、`Edit(int, Product)` で送信されたフォームを処理する MVC コントローラーの一般的なパターンです。 これを MVC で可能にするには、要求が HTTP `Edit(int, Product)` の場合は `POST` を選び、それ以外の HTTP 動詞の場合は `Edit(int)` を選ぶ必要があります。

`HttpPostAttribute` (`[HttpPost]`) は、HTTP 動詞が `IActionConstraint` の場合にのみそのアクションの選択を許可する `POST` の実装です。 `IActionConstraint` が存在すると、`Edit(int, Product)` の方が `Edit(int)` より "良い" 一致になるので、`Edit(int, Product)` が最初に試されます。

カスタム `IActionConstraint` を作成する必要があるのは特殊なシナリオだけですが、`HttpPostAttribute` のような属性の役割を理解しておくことが重要です。似た属性が他の HTTP 動詞に対しても定義されています。 規則ルーティングでは、アクションが `show form -> submit form` ワークフローの一部になっている場合、複数のアクションが同じアクション名を使うのはよくあることです。 「[IActionConstraint について](#understanding-iactionconstraint)」セクションを読むと、このパターンの便利さがいっそうよくわかります。

複数のルートが一致し、MVC が "最善の " ルートを発見できない場合、MVC は `AmbiguousActionException` をスローします。

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>ルート名

次の例の文字列 `"blog"` と `"default"` は、ルート名です。

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

ルート名は、URL の生成に名前付きのルートを使うことができるよう、ルートに論理名を提供します。 これは、ルートの順序指定によって URL の生成が複雑になる場合に、URL の作成を大幅に合理化します。 ルート名は、アプリケーション全体で一意である必要があります。

ルート名が URL の照合や要求の処理に影響を与えることはありません。ルート名は URL の生成のみに使われます。 MVC 固有のヘルパーでの URL の生成など、URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>属性ルーティング

属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。 次の例では、`app.UseMvc();` が `Configure` メソッド内で使われており、ルートは渡されていません。 `HomeController` は、既定のルート `{controller=Home}/{action=Index}/{id?}` が一致するのと同じように、URL のセットと一致します。

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

`HomeController.Index()` アクションは、URL パス `/`、`/Home`、または `/Home/Index` のいずれかに対して実行されます。

> [!NOTE]
> この例では、属性ルーティングと規則ルーティングでのプログラミングの大きな違いが強調して示されています。 属性ルーティングの方が、ルートを指定するために多くの入力を必要とします。既定の規則ルートの方が簡潔にルートを処理します。 ただし、属性ルーティングでは、各アクションに適用するルート テンプレートを正確に制御できます (そして制御する必要があります)。

属性ルーティングでは、コントローラー名とアクション名はアクションの選択において役割を**持ちません**。 この例では、前の例と同じ URL を照合します。

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> 上のルート テンプレートでは、`action`、`area`、`controller` のルート パラメーターは定義されていません。 実際、これらのルート パラメーターは属性ルートでは許可されていません。 ルート テンプレートはアクションと既に関連付けられているため、URL からアクション名を解析しても意味はありません。

## <a name="attribute-routing-with-httpverb-attributes"></a>Http[Verb] 属性を使用する属性ルーティング

属性ルーティングでは、`Http[Verb]` などの `HttpPostAttribute` 属性も使うことができます。 これらの属性はすべて、ルート テンプレートを受け入れることができます。 次の例では、同じルート テンプレートと一致する 2 つのアクションが示されています。

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

`/products` のような URL パスの場合、HTTP 動詞が `ProductsApi.ListProducts` のときは `GET` アクションが実行され、HTTP 動詞が `ProductsApi.CreateProduct` のときは `POST` が実行されます。 属性ルーティングでは、最初に、ルート属性で定義されているルート テンプレートのセットに対して URL が照合されます。 ルート テンプレートが一致すると、`IActionConstraint` 制約が適用されて、実行できるアクションが決定されます。

> [!TIP]
> REST API を構築する場合、アクションではすべての HTTP メソッドが受け入れられるので、アクション メソッドに対して `[Route(...)]` を使用することはまれです。 より固有の `Http*Verb*Attributes` を使って、API がサポートするものを正確に指定するのがよい方法です。 REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。

属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。 次の例では、`id` は URL パスの一部として必須です。

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)` アクションは、`/products/3` のような URL パスに対しては実行されますが、`/products` のような URL パスに対しては実行されません。 ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。

## <a name="route-name"></a>ルート名

次のコードでは、 *の "* ルート名`Products_List`" を定義しています。

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

ルート名を使うと、特定のルートに基づいて URL を生成できます。 ルート名は、ルーティングの URL 照合動作には影響を与えず、URL 生成に対してのみ使われます。 ルート名は、アプリケーション全体で一意である必要があります。

> [!NOTE]
> これに対し、規則 "*既定ルート*" では、`id` パラメーターは省略可能 (`{id?}`) として定義されています。 API を厳密に指定するこの機能には、`/products` と `/products/5` を異なるアクションに対してディスパッチできるといった利点があります。

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>ルートの結合

属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。 コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。 ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

次の例では、URL パス `/products` は `ProductsApi.ListProducts` と一致でき、URL パス `/products/5` は `ProductsApi.GetProduct(int)` と一致できます。 どちらのアクションも、`GET` でマークされているため、HTTP `HttpGetAttribute` だけと一致します。

`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。 次の例は、"*既定ルート*" と同様の URL パスのセットと一致します。

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>属性ルートの順序の指定

定義された順序で実行される従来のルートとは対照的に、属性ルーティングはツリーを構築し、すべてのルートを同時に照合します。 これは、ルート エントリが理想的な順序で配置されているかのように動作します。一般的なルートより前に、最も固有のルートが実行する機会があります。

たとえば、`blog/search/{topic}` のようなルートは、`blog/{*article}` のようなルートより固有です。 論理的には、`blog/search/{topic}` ルートが唯一の意味のある順序なので、既定では、これが最初に "実行" されます。 規則ルーティングを使うときは、開発者が目的の順序でルートを配置する必要があります。

属性ルートでは、フレームワークが提供するすべてのルート属性の `Order` プロパティを使って、順序を構成できます。 ルートは、`Order` プロパティの昇順に従って処理されます。 既定の順序は `0` です。 `Order = -1` を使ってルートを設定すると、順序が設定されていないルートの前に実行されます。 `Order = 1` を使ってルートを設定すると、既定のルート順序の後で実行されます。

> [!TIP]
> `Order` には依存しないでください。 URL 空間で正しくルーティングするために明示的な順序値が必要な場合、クライアントの混乱を招く可能性があります。 一般に、属性ルーティングは URL 照合で正しいルートを選びます。 URL の生成に使われる既定の順序がうまくいかない場合は、通常、オーバーライドとしてルート名を使う方が、`Order` プロパティを適用するより簡単です。

Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。 Razor Pages でのルート順序に関する情報は、[Razor Pages のルートとアプリの規則:ルート順序](xref:razor-pages/razor-pages-conventions#route-order)に関する記事を参照してください。

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>ルート テンプレートでのトークンの置換 ([controller], [action], [area])

利便性のため、属性ルートではトークンを角かっこ ( *、* ) で囲むことによる "`[`トークンの置換`]`" がサポートされています。 トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、区分名、コントローラー名の値に置き換えられます。 次の例では、アクションはコメントで説明されているように URL パスと一致します。

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

属性ルート作成の最後のステップで、トークンの置換が発生します。 上の例は、次のコードと同じように動作します。

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

属性ルートを継承と組み合わせることもできます。 これをトークンの置換と組み合わせると特に強力です。

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

トークンの置換は、属性ルートで定義されているルート名にも適用されます。 `[Route("[controller]/[action]", Name="[controller]_[action]")]` では、アクションごとに一意のルート名が生成されます。

リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする

トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。 パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。 たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。

`RouteTokenTransformerConvention` は、次のようなアプリケーション モデルの規則です。

* パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。
* 置き換えられる属性ルートのトークン値をカスタマイズします。

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>複数のルート

属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。 これの最も一般的な使用方法は、次の例で示すように、"*既定の規則ルート*" の動作を模倣することです。

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

コントローラーに複数のルート属性を配置すると、それぞれが、アクション メソッドの各ルート属性と結合します。

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

アクションに (`IActionConstraint` を実装する) 複数のルート属性を配置すると、各アクション制約がそれを定義した属性のルート テンプレートと結合します。

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> アクションで複数のルートを使うと強力に見えますが、アプリケーションの URL 空間は簡潔で明確に定義された状態に保つことをお勧めします。 アクションで複数のルートを使うのは、必要な場合 (既存のクライアントをサポートする、など) だけにしてください。

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>属性ルートの省略可能なパラメーター、既定値、制約を指定する

属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>`IRouteTemplateProvider` を使用するカスタム ルート属性

フレームワークで提供されるすべてのルート属性 (`[Route(...)]`、`[HttpGet(...)]` など) は、`IRouteTemplateProvider` インターフェイスを実装しています。 アプリが起動し、`IRouteTemplateProvider` を実装している属性を使ってルートの初期セットを作成すると、MVC はコントローラー クラスとアクション メソッドで属性を検索します。

`IRouteTemplateProvider` を実装して、独自のルート属性を定義できます。 各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

上の例の属性は、`Template` が適用されると、自動的に `"api/[controller]"` を `[MyApiController]` に設定します。

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>アプリケーション モデルを使用した属性ルートのカスタマイズ

"*アプリケーション モデル*" は、アクションをルーティングおよび実行するために MVC によって使われるすべてのメタデータで起動時に作成されるオブジェクト モデルです。 "*アプリケーション モデル*" には、(`IRouteTemplateProvider` によって) ルート属性から収集されるすべてのデータが含まれます。 起動時にアプリケーション モデルを変更してルーティングの動作方法をカスタマイズする "*規則*" を作成できます。 このセクションでは、アプリケーション モデルを使ってルーティングをカスタマイズする簡単な例を示します。

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>混合ルーティング: 属性ルーティングと規則ルーティング

MVC アプリケーションでは、規則ルーティングと属性ルーティングを併用できます。 一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。

アクションは、規則的にルーティングされるか、または属性でルーティングされます。 コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。 属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。 コントローラーの**任意**のルート属性により、コントローラー属性のすべてのアクションがルーティングされます。

> [!NOTE]
> 2 種類のルーティング システムを区別しているのは、URL がルート テンプレートと一致した後に適用されるプロセスです。 規則ルーティングでは、一致のルート値を使って、規則ルーティングされるすべてのアクションの参照テーブルから、アクションとコントローラーが選ばれます。 属性ルーティングでは、各テンプレートはアクションと既に関連付けられており、それ以上参照する必要はありません。

## <a name="complex-segments"></a>複雑なセグメント

複雑なセグメント (たとえば、`[Route("/dog{token}cat")]`) は、右から左にリテラルを最短の方法で照合することによって処理されます。 説明については、[ソース コード](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)をご覧ください。 詳しくは、[こちらの懸案事項](https://github.com/dotnet/AspNetCore.Docs/issues/8197)をご覧ください。

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL の生成

MVC アプリケーションでは、ルーティングの URL 生成機能を使って、アクションへの URL リンクを生成できます。 URL を生成すると URL をハードコーディングする必要がなくなり、コードの堅牢性と保守性が向上します。 このセクションでは、MVC によって提供される URL 生成機能について説明します。URL 生成のしくみに関する基本だけを取り上げます。 URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。

`IUrlHelper` インターフェイスは、MVC と URL 生成のルーティングの間にあるインフラストラクチャの基盤部分です。 使用可能な `IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビュー コンポーネントの `Url` プロパティを使って探します。

この例の `IUrlHelper` インターフェイスは、別のアクションへの URL を生成するために `Controller.Url` プロパティを介して使われています。

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

アプリケーションが既定の規則ルートを使っている場合、`url` 変数の値は URL パス文字列 `/UrlGeneration/Destination` になります。 この URL パスは、ルーティングにより、現在の要求からのルート値 (アンビエント値) と、`Url.Action` に渡された値を結合し、これらの値でルート テンプレートを置換することによって作成されます。

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。 値を持たないルート パラメーターは、既定値がある場合はそれを使うことができ、省略可能な場合はスキップできます (この例の `id` の場合と同様)。 必須のルート パラメーターに対応する値がない場合、URL の生成は失敗します。 ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。

上の `Url.Action` の例では規則ルーティングを想定しています。URL 生成は属性ルーティングでも同じように動作しますが、概念は異なります。 規則ルーティングでは、ルート値を使ってテンプレートが展開され、通常、`controller` と `action` のルートがそのテンプレートに表示されます。これは、ルーティングによって一致した URL が "*規則*" に従うために動作します。 属性ルーティングでは、`controller` と `action` のルート値はテンプレートに表示できません。代わりに、使うテンプレートの検索に使われます。

この例では、属性ルーティングを使います。

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC はすべての属性ルーティングされるアクションの参照テーブルを作成し、`controller` と `action` の値で照合して、URL 生成に使うルート テンプレートを選びます。 上の例では、`custom/url/to/destination` が生成されます。

### <a name="generating-urls-by-action-name"></a>アクション名による URL の生成

`Url.Action` (`IUrlHelper`. `Action`) およびすべての関連するオーバーロードは、コントローラー名とアクション名を指定することによってリンク先を指定するアイデアに基づきます。

> [!NOTE]
> `Url.Action` を使うと、`controller` および `action` の現在のルート値が自動的に指定されます。`controller` および `action` の値は、"*アンビエント値*" **と** "*値*" 両方の一部です。 `Url.Action` メソッドは、常に `action` と `controller` の現在の値は使い、現在のアクションにルーティングする URL パスを生成します。

ルーティングは、ユーザーが URL 生成時に指定しなかった情報を、アンビエント値を使って埋めようとします。 `{a}/{b}/{c}/{d}` やアンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }` のようなルートを使うと、すべてのルート パラメーターが値を持っているため、ルーティングには URL を生成するための十分な情報があり、追加の値は必要ありません。 値 `{ d = Donovan }` を追加した場合は、値 `{ d = David }` は無視されて、生成される URL パスは `Alice/Bob/Carol/Donovan` になります。

> [!WARNING]
> URL パスは階層的です。 上の例で、値 `{ c = Cheryl }` を追加した場合は、両方の値 `{ c = Carol, d = David }` が無視されます。 この場合、`d` には値がなくなり、URL の生成は失敗します。 `c` および `d` の目的の値を指定する必要があります。  既定のルート (`{controller}/{action}/{id?}`) でもこの問題が発生すると思われるかもしれませんが、`Url.Action` が常に `controller` と `action` の値を明示的に指定するので、実際には、このような動作が発生することはほとんどありません。

`Url.Action` の長いオーバーロードも、追加の "*ルート値*" オブジェクトを受け取って、`controller` および `action` 以外のルート パラメーターの値を提供します。 これを最もよく目にするのは、`id` のように `Url.Action("Buy", "Products", new { id = 17 })` で使われる場合です。 慣例では、"*ルート値*" オブジェクトは通常は匿名型のオブジェクトですが、`IDictionary<>` または "*単純な従来の .NET オブジェクト*" を使うこともできます。 ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> 絶対 URL を作成するには、`protocol` を受け取るオーバーロードを使います。`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>ルートによる URL の生成

上記のコードでは、コントローラーとアクション名を渡すことによる URL の生成を示しました。 `IUrlHelper` では、`Url.RouteUrl` ファミリ メソッドも提供されています。 これらのメソッドは、`Url.Action` と似ていますが、`action` および `controller` の現在の値をルート値にコピーしません。 最も一般的な使用方法は、ルート名を指定して特定のルートを使って URL を生成する場合です。通常、コントローラーまたはアクション名は指定 "*しません*"。

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>HTML での URL の生成

`IHtmlHelper` で提供されている `HtmlHelper` メソッドの `Html.BeginForm` および `Html.ActionLink` は、それぞれ `<form>` 要素と `<a>` 要素を生成します。 これらのメソッドは `Url.Action` メソッドを使って URL を生成するので、同じような引数を受け取ります。 `Url.RouteUrl` の `HtmlHelper` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。

TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。 これらはどちらも、実装に `IUrlHelper` を使います。 詳しくは、「[フォームの操作](../views/working-with-forms.md)」をご覧ください。

ビューの内部では、`IUrlHelper` プロパティを使って任意のアドホック URL 生成に `Url` を使うことができます (上の記事では説明されていません)。

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>アクションの結果での URL の生成

上の例ではコントローラーでの `IUrlHelper` の使用が示されていますが、コントローラーでの最も一般的な使用方法はアクションの結果の一部として URL を生成することです。

基底クラス `ControllerBase` および `Controller` では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。 一般的な使用法の 1 つは、ユーザー入力を受け付けた後でリダイレクトする場合です。

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

アクション結果ファクトリ メソッドは、`IUrlHelper` のメソッドに似たパターンに従います。

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>専用規則ルートの特殊なケース

規則ルーティングでは、"*専用規則ルート*" と呼ばれる特殊なルート定義を使うことができます。 次の例の `blog` という名前のルートが専用規則ルートです。

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

これらのルート定義を使うと、`Url.Action("Index", "Home")` は `/` ルートで URL パス `default` を生成します。なぜでしょうか。 ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。

専用規則ルートは、URL の生成でルートの "一致範囲が広くなりすぎる" のを防ぐために対応するルート パラメーターを持たない既定値の特別な動作に依存します。 この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。 ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。 値 `blog` は `{ controller = Home, action = Index }` と一致しないため、`{ controller = Blog, action = Article }` を使った URL の生成は失敗します。 そして、ルーティングはフォールバックして `default` を試み、これは成功します。

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>領域

[区分](areas.md)は MVC の機能であり、関連する機能を独立したルーティング名前空間 (コントローラー アクションの場合) およびフォルダー構造 (ビューの場合) としてグループ化するために使われます。 区分を使うと、アプリケーションは同じ名前の複数のコントローラーを持つことができます (ただし、"*区分*" が異なる場合)。 区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。 このセクションでは、ルーティングと区分がどのように相互作用するのかについて説明します。ビューでの区分の使い方について詳しくは、「[区分](areas.md)」をご覧ください。

次の例では、既定の規則ルートと、 *という名前の区分に対する "* 区分ルート`Blog`" を使うように、MVC を構成しています。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

`/Manage/Users/AddUser` のような URL パスを照合すると、最初のルートではルート値 `{ area = Blog, controller = Users, action = AddUser }` が生成されます。 `area` ルート値は、`area` の既定値によって生成されます。実際には、`MapAreaRoute` によって作成されるルートは以下と同等です。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` は、指定された区分名 (この場合は`area`) を使い、既定値と、`Blog` に対する制約の両方からルートを作成します。 既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。

> [!TIP]
> 規則ルーティングは順序に依存します。 一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。

上の例を使うと、ルート値は次のアクションと一致します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` はコントローラーが区分の一部であることを示すものであり、このコントローラーは `Blog` 区分に含まれます。 `[Area]` 属性を持たないコントローラーはどの区域のメンバーでもなく、 **ルート値がルーティングによって提供されたときは一致**しません`area`。 次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> ここでは完全を期すために各コントローラーの名前空間を示してあります。そうしないと、コントローラーの名前が競合し、コンパイラ エラーが発生します。 クラスの名前空間は、MVC のルーティングに対して影響を与えません。

最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。 3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。

> [!NOTE]
> "*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。

区分の内部でアクションを実行するとき、`area` のルート値は、URL の生成に使うルーティングの "*アンビエント値*" として利用できます。 つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>IActionConstraint について

> [!NOTE]
> このセクションでは、フレームワークの内部構造と MVC が実行するアクションを選ぶ方法について詳しく説明します。 一般的なアプリケーションでは、カスタム `IActionConstraint` は必要ありません。

インターフェイスにあまり詳しくない場合でも、既に `IActionConstraint` を使ったことがあるはずです。 `[HttpGet]` 属性およびそれに似た `[Http-VERB]` 属性は、アクション メソッドの実行を制限するために `IActionConstraint` を実装しています。

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

既定の規則ルートでは、URL パス `/Products/Edit` は値 `{ controller = Products, action = Edit }` を生成し、ここで示す**両方**のアクションと一致するものとします。 `IActionConstraint` ではこれを、両方のアクションが候補と見なされる、といいます。どちらもルート データと一致するからです。

`HttpGetAttribute` は実行すると、*Edit()* は *GET* に対して一致し、他の HTTP 動詞には一致しないことを示します。 `Edit(...)` アクションには制約が定義されていないので、すべての HTTP 動詞と一致します。 したがって、`POST` は `Edit(...)` のみと一致します。 一方、`GET` の場合は両方のアクションが一致します。しかし、`IActionConstraint` を指定されているアクションの方が常に、指定されていないアクション "*より適切*" であると見なされます。 したがって、`Edit()` には `[HttpGet]` があるので、より具体的と見なされ、両方のアクションが一致する場合はこちらが選ばれます。

概念的には、`IActionConstraint` は "*オーバーロード*" の形式ですが、同じ名前のメソッドをオーバーロードするのではなく、同じ URL に一致するアクション間でオーバーロードします。 属性ルーティングも `IActionConstraint` を使い、異なるコントローラーのアクションがどちらも候補と見なされる結果になることがあります。

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>IActionConstraint の実装

`IActionConstraint` を実装する最も簡単な方法は、`System.Attribute` の派生クラスを作成し、アクションとコントローラーに配置する方法です。 MVC は、属性として適用された `IActionConstraint` を自動的に検出します。 アプリケーション モデルを使って制約を適用することができ、適用方法をメタプログラムできるので、おそらくこれが最も柔軟なアプローチです。

次の例では、制約は、ルートデータから*国コード*に基づいてアクションを選択します。 [GitHub の完全なサンプルはこちら](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)をご覧ください。

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

ユーザーは、`Accept` メソッドを実装し、実行する制約の "順序" を選ぶ必要があります。 この例では、`Accept` ルート値が一致すると、`true` メソッドは `country` を返してアクションが一致することを示します。 この動作は、非属性アクションにフォールバックできる `RouteValueAttribute` とは異なります。 サンプルでは、`en-US` アクションを定義すると、`fr-FR` のような国コードは `[CountrySpecific(...)]` が適用されていない、より一般的なコントローラーにフォールバックすることが示されています。

`Order` プロパティは、制約が含まれる "*ステージ*" を決定します。 アクションの制約は、`Order` に基づいてグループで実行されます。 たとえば、フレームワークで提供されるすべての HTTP メソッド属性は、同じステージで実行されるように、同じ `Order` 値を使います。 目的のポリシーを実装するため、必要なだけいくつでもステージを使うことができます。

> [!TIP]
> `Order` の値を決めるときは、HTTP メソッドより前に制約を適用する必要があるかどうかを考えます。 小さい値が先に実行されます。

::: moniker-end
