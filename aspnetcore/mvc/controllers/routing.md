---
title: ASP.NET Core でのコントローラー アクションへのルーティング
author: rick-anderson
description: ASP.NET Core MVC でルーティング ミドルウェアを使って、受信した要求の URL を照合し、アクションにマップする方法について説明します。
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c63313ec060c5be368fcbd20edf5f0d557046d2e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977219"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core でのコントローラー アクションへのルーティング

[ライアン・ノワク](https://github.com/rynowak)、[カーク・ラーキン](https://twitter.com/serpent5)、[リック・アンダーソン](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NETコア コントローラは、ルーティング[ミドルウェア](xref:fundamentals/middleware/index)を使用して着信要求の URL を照合し、それらをアクション にマップ[します](#action)。  ルート テンプレート:

* スタートアップ コードまたは属性で定義されます。
* URL パスと[アクション](#action)の照合方法を説明する :
* リンクの URL を生成するために使用されます。 生成されたリンクは、通常、応答として返されます。

アクションは、[従来のルーティング](#cr)または[属性](#ar)ルーティングのいずれかです。 コントローラまたは[アクション](#action)にルートを配置すると、そのルートは属性ルーティングになります。 詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。

このドキュメント:

* MVC とルーティングの相互作用について説明します。
  * 標準的な MVC アプリがルーティング機能をどのように利用するかを示します。
  * 両方をカバーします。
    * [従来のルーティングは](#cr)、通常、コントローラとビューで使用されます。
    * REST API で使用される*属性ルーティング*。 主に REST API のルーティングに関心がある場合は、「REST API[の属性ルーティング](#ar)」セクションに移動します。
  * 詳細[なルーティング](xref:fundamentals/routing)の詳細については、「ルーティング」を参照してください。
* エンドポイント ルーティングと呼ばれる、ASP.NET Core 3.0 で追加された既定のルーティング システムを指します。 互換性を保つために、以前のバージョンのルーティングでコントローラーを使用することが可能です。 手順については[、2.2-3.0 の移行ガイド](xref:migration/22-to-30)を参照してください。 従来のルーティング システムの参考資料については[、このドキュメントの 2.2 バージョン](xref:mvc/controllers/routing?view=aspnetcore-2.2)を参照してください。

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>従来のルートを設定する

`Startup.Configure`通常、[従来のルーティング](#crd)を使用する場合は、次のようなコードがあります。

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

の<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>呼び出し<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>の内部では、単一のルートを作成するために使用されます。 単一のルートは`default`ルートという名前です。 コントローラーとビューを持つほとんどのアプリでは、ルートに似た`default`ルート テンプレートを使用します。 REST API は[属性ルーティング](#ar)を使用する必要があります。

ルート テンプレート`"{controller=Home}/{action=Index}/{id?}"`:

* 次のような URL パスに一致します。`/Products/Details/5`
* パスをトークン化して`{ controller = Products, action = Details, id = 5 }`ルート値を抽出します。 ルート値の抽出は、アプリにコントローラー名と`ProductsController`アクションがある場合に一致します`Details`。

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`model は、 の`id = 5`値をバインド`id`して、`5`パラメーターを に設定します。 詳細については、[モデル バインド](xref:mvc/models/model-binding)を参照してください。
* `{controller=Home}`は`Home`、既定`controller`の として定義されます。
* `{action=Index}`は`Index`、既定`action`の として定義されます。
*  の`?`文字は`{id?}`オプション`id`として定義されます。
  * 既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。 ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。
* URL パス`/`に一致します。
* ルート値`{ controller = Home, action = Index }`を生成します。

既定値`controller`の値と`action`、既定値を使用する値。 `id`URL パスに対応するセグメントがないため、値は生成されません。 `/`アクション`HomeController``Index`が存在する場合にのみ一致します。

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

上記のコントローラ定義とルート テンプレートを使用して`HomeController.Index`、アクションは次の URL パスに対して実行されます。

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

URL パス`/`は、ルート テンプレート`Home`の既定の`Index`コントローラとアクションを使用します。 URL パス`/Home`は、ルート テンプレート`Index`の既定のアクションを使用します。

便利なメソッド <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

置き換えます：

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

ルーティングは、 および<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>ミドルウェアを使用して構成されます。 コントローラを使用するには:

* 内部<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*>`UseEndpoints`を呼び出して[、属性ルーティング コントローラ](#ar)をマップします。
* または<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>を呼び出して、[従来のルーティングされたコントローラを](#cr)マップします。

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>規則ルーティング

従来のルーティングは、コントローラとビューで使用されます。 次は `default` ルートです。

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

これは、"*規則ルーティング*" の例です。 これは、URL パスの*規則*を確立するため、*従来型ルーティング*と呼ばれます。

* 最初のパス セグメント`{controller=Home}`は、コントローラ名にマップされます。
* 2 番目の`{action=Index}`セグメント は、[アクション](#action)名にマップされます。
* 3 番目の`{id?}`セグメントは、オプション`id`の . in`?``{id?}`はオプションになります。 `id`は、モデル エンティティにマップするために使用されます。

この`default`ルートを使用して、URL パス:

* `/Products/List`アクションにマップ`ProductsController.List`されます。
* `/Blog/Article/17`にマップ`BlogController.Article`し、通常はモデルは`id`パラメータを 17 にバインドします。

このマッピング:

* は、コントローラ名と[アクション](#action)名**のみに**基づいています。
* 名前空間、ソース ファイルの場所、メソッド パラメーターに基づいていません。

既定のルートで従来のルーティングを使用すると、各アクションの新しい URL パターンを考え出すことなく、アプリを作成できます。 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)スタイルアクションを持つアプリの場合、コントローラー間で URL の整合性を保つ:

* コードを簡略化します。
* UI の予測を容易にします。

> [!WARNING]
> `id`上記のコードは、ルート テンプレートによってオプションとして定義されます。 アクションは、URL の一部として提供されるオプションの ID なしで実行できます。 一般に`id`、URL から省略すると次のようになります。
>
> * `id`は、モデル`0`バインドによって設定されます。
> * データベースに一致する エンティティが`id == 0`見つかりません。
>
> [属性ルーティング](#ar)は、一部のアクションに必要な ID を作成し、他のアクションに対しては必要としない詳細な制御を提供します。 規則により、ドキュメントには、正しい使用方法`id`で表示される可能性が高い場合などのオプションのパラメーターが含まれています。

ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。 既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:

* 基本的でわかりやすいルーティング スキームをサポートしています。
* UI ベースのアプリの便利な開始点となります。
* 多くの Web UI アプリに必要なルート テンプレートは、のみです。 大規模な Web UI アプリの場合、必要な場合は、多くの場合[、領域](#areas)を使用して別のルート。

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>および<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>:

* 呼び出された順序に基づいて、**エンドポイントに注文**値を自動的に割り当てます。

ASP.NET Core 3.0 以降のエンドポイント ルーティング:

* ルートの概念を持っていません。
* 拡張性の実行に対する順序の保証は提供されませんが、すべてのエンドポイントが一度に処理されます。

[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。

[属性ルーティング](#ar)については、このドキュメントで後述します。

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>複数の従来のルート

にコールを追加することで、複数[の従来のルート](#cr)を`UseEndpoints`内部<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>に<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>追加できます。 これにより、複数の規則を定義したり、次のような特定の[アクション](#action)専用の従来のルートを追加することができます。

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

上記`blog`のコードのルートは **、専用の従来のルート**です。 これは、専用の従来のルートと呼ばれています。

* [これは、従来のルーティングを](#cr)使用します。
* これは、特定の[アクション](#action)に専用です。

`action`ルート テンプレート`"blog/{*article}"`にパラメーターとして表示されないの`controller`で、

* 既定値のみを持つことができます`{ controller = "Blog", action = "Article" }`。
* このルートは常に アクション`BlogController.Article`にマップされます。

`/Blog`、、`/Blog/Article`および`/Blog/{any-string}`はブログルートに一致する唯一の URL パスです。

前の例は次のとおりです。

* `blog`ルートは最初に追加されるため、一致`default`の優先順位はルートよりも高くなります。
* これは、URL の一部として記事名を持つことを一般的な[Slug](https://developer.mozilla.org/docs/Glossary/Slug)スタイル ルーティングの例です。

> [!WARNING]
> ASP.NET Core 3.0 以降では、ルーティングは次の処理を行いません。
> * *ルート*と呼ばれる概念を定義します。 `UseRouting`は、ミドルウェア パイプラインにルートマッチングを追加します。 ミドル`UseRouting`ウェアは、アプリで定義されているエンドポイントのセットを調べ、要求に基づいて最適なエンドポイント一致を選択します。
> * または のような<xref:Microsoft.AspNetCore.Routing.IRouteConstraint>拡張の実行順序に関する保証を<xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>提供します。
>
>[ルーティング](xref:fundamentals/routing)の参考資料については、「ルーティング」を参照してください。

<a name="cro"></a>

### <a name="conventional-routing-order"></a>従来のルーティング順序

従来のルーティングは、アプリによって定義されたアクションとコントローラーの組み合わせにのみ一致します。 これは、従来のルートが重複する場合を単純化することを目的としています。
を<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>使用してルートを追加し<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>、呼び出された順序に基づいて自動的にエンドポイントに注文値を割り当てます。 以前に表示されたルートからの一致は、優先順位が高くなります。 規則ルーティングは順序に依存します。 一般に、エリアを含むルートは、エリアのないルートよりも具体的であるため、早めに配置する必要があります。 キャッチされたすべての`{*article}`ルートパラメータを持つ[専用の従来のルート](#dcr)は、ルートを[貪欲](xref:fundamentals/routing#greedy)にしすぎて、他のルートと一致させるURLと一致することを意味します。 欲張り一致を防ぐために、後でルート テーブルに欲張りルートを配置します。

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>あいまいなアクションの解決

ルーティングを通じて 2 つのエンドポイントが一致する場合、ルーティングは次のいずれかを行う必要があります。

* 最適な候補を選択します。
* 例外をスローします。

次に例を示します。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

上記のコントローラは、一致する 2 つのアクションを定義します。

* URL パス`/Products33/Edit/17`
* ルート`{ controller = Products33, action = Edit, id = 17 }`データ :

これは MVC コントローラーの典型的なパターンです。

* `Edit(int)`は、製品を編集するためのフォームを表示します。
* `Edit(int, Product)`を使用して、転記されたフォームを処理します。

正しいルートを解決するには、次の手順に従います。

* `Edit(int, Product)`は、要求が HTTP`POST`の場合に選択されます。
* `Edit(int)`[は、HTTP 動詞](#verb)が他の何かである場合に選択されます。 `Edit(int)`通常は を`GET`介して 呼び出されます。

は<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、`[HttpPost]`要求の HTTP メソッドに基づいて選択できるようにルーティングに提供されます。 より`HttpPostAttribute`良`Edit(int, Product)`い一致を`Edit(int)`作る.

などの`HttpPostAttribute`属性の役割を理解することが重要です。 他の[HTTP 動詞](#verb)にも同様の属性が定義されています。 [従来のルーティング](#cr)では、アクションがショー フォームの一部である場合、同じアクション名を使用してフォーム ワークフローを送信するのが一般的です。 たとえば[、2 つの Edit アクション メソッドを調べる](xref:tutorials/first-mvc-app/controller-methods-views#get-post)を参照してください。

最適な候補を選択できない場合、複数の<xref:System.Reflection.AmbiguousMatchException>一致したエンドポイントが一覧表示されます。

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>従来のルート名

文字列`"blog"`と次`"default"`の例では、従来のルート名です。

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

ルート名はルートに論理名を付けます。 名前付きルートは、URL 生成に使用できます。 名前付きルートを使用すると、ルートの順序付けによって URL の生成が複雑になる可能性がある場合に、URL の作成が簡単になります。 ルート名は、アプリケーション全体で一意である必要があります。

ルート名:

* URL の一致や要求の処理には影響しません。
* URL 生成にのみ使用されます。

ルート名の概念は、ルーティングで[IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)として表されます。 **ルート名**と**エンドポイント名**という用語:

* 交換可能です。
* ドキュメントとコードで使用される API は、記述されている API によって異なります。

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>REST API の属性ルーティング

REST API は、属性ルーティングを使用して、操作が HTTP 動詞 で表されるリソースのセットとして、アプリの機能[をモデル化する](#verb)必要があります。

属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。 次`StartUp.Configure`のコードは、REST API の一般的なコードで、次のサンプルで使用されます。

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

上記のコードでは、内部<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*>`UseEndpoints`で呼び出され、属性ルーティング コントローラをマップします。

次の例では

* 上記の`Configure`メソッドが使用されます。
* `HomeController`は、既定の従来のルート`{controller=Home}/{action=Index}/{id?}`が一致するものと同様の一連の URL に一致します。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

この`HomeController.Index`アクションは、任意の URL パス`/` `/Home`、 `/Home/Index`、、または`/Home/Index/3`に対して実行されます。

この例では、属性ルーティングと従来のルーティングとの間のプログラミングの主な違いを強調[しています](#cr)。 属性ルーティングでは、ルートを指定するためにより多くの入力が必要です。 従来のデフォルトルートは、ルートをより簡潔に処理します。 ただし、属性ルーティングでは、各[アクション](#action)に適用するルート テンプレートを正確に制御できる必要があります。

属性ルーティングでは、コントローラー名とアクション名は、アクションが一致する役割を果**たしません**。 次の例は、前の例と同じ URL に一致します。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

次のコードでは、 と`action``controller`のトークン置換を使用しています。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

コントローラには、次`[Route("[controller]/[action]")]`のコードが適用されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

上記のコードでは、メソッド`Index`テンプレートの前に、`/`または`~/`ルート テンプレートを追加する必要があります。 `/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。

ルート テンプレートの選択については、「[ルート テンプレートの優先順位](xref:fundamentals/routing#rtp)」を参照してください。

## <a name="reserved-routing-names"></a>ルーティングの予約名

コントローラーまたは Razor ページを使用する場合、次のキーワードは予約されたルート パラメーター名です。

* `action`
* `area`
* `controller`
* `handler`
* `page`

属性`page`ルーティングでルート パラメータとして使用すると、一般的なエラーになります。 そうすることで、URL 生成との一貫性がなく、混乱を招く動作が発生します。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

特別なパラメーター名は、URL 生成操作が Razor ページまたはコントローラーを参照するかどうかを決定するために、URL 生成によって使用されます。

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP 動詞テンプレート

ASP.NETコアには、次の HTTP 動詞テンプレートがあります。

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[Httpパッチ]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>工順テンプレート

ASP.NET Core には、次のルート テンプレートがあります。

* HTTP[動詞テンプレート](#verb)はすべてルート テンプレートです。
* [[ルート]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Http 動詞属性を使用した属性ルーティング

次のコントローラを考えてみます。

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

上のコードでは以下の操作が行われます。

* 各アクションには属性`[HttpGet]`が含まれており、HTTP GET 要求のみに一致するように制約されます。
* アクション`GetProduct`にはテンプレートが`"{id}"`含まれるため`id`、コントローラの`"api/[controller]"`テンプレートに追加されます。 メソッド テンプレートは`"api/[controller]/"{id}""`です。 したがって、このアクションは、フォーム`/api/test2/xyz`、、`/api/test2/123`など`/api/test2/{any string}`に対する GET 要求にのみ一致します。
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* アクション`GetIntProduct`にはテンプレートが`"int/{id:int}")`含まれます。 テンプレート`:int`の一部は、`id`整数に変換できる文字列にルート値を制約します。 GET 要求`/api/test2/int/abc`:
  * このアクションと一致しません。
  * [404 Not Found エラーを返](https://developer.mozilla.org/docs/Web/HTTP/Status/404)します。
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* アクション`GetInt2Product`はテンプレート`{id}`に含まれますが、整数に変換できる値`id`に制約はありません。 GET 要求`/api/test2/int2/abc`:
  * このルートに一致します。
  * モデル バインドは整数`abc`への変換に失敗します。 メソッド`id`のパラメーターは整数です。
  * モデル バインドが整数への変換`abc`に失敗したため[、400 不正な要求](https://developer.mozilla.org/docs/Web/HTTP/Status/400)を返します。
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

属性<xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute>ルーティングでは<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、 、 、<xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>などの<xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>属性を使用できます。 すべての[HTTP 動詞](#verb)属性は、ルート テンプレートを受け入れます。 次の例は、同じルート テンプレートに一致する 2 つのアクションを示しています。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

URL パス`/products3`の使用 :

* アクション`MyProductsController.ListProducts`は HTTP[動詞](#verb)が`GET`の場合に実行されます。
* アクション`MyProductsController.CreateProduct`は HTTP[動詞](#verb)が`POST`の場合に実行されます。

REST API を構築する場合、アクションはすべての HTTP メソッドを受`[Route(...)]`け入れるため、アクションメソッドで使用する必要が生じることはまれです。 API がサポートする内容を正確に示すために、より具体的な[HTTP 動詞属性](#verb)を使用することをおしいます。 REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。

REST API は、属性ルーティングを使用して、操作が HTTP 動詞によって表される一連のリソースとしてアプリの機能をモデル化する必要があります。 つまり、同じ論理リソース上の GET や POST などの多くの操作で同じ URL が使用されます。 属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。

属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。 次の例では、URL`id`パスの一部として必要です。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

アクション`Products2ApiController.GetProduct(int)`:

* 次のような URL パスで実行されます。`/products2/3`
* URL パス`/products2`で実行されません。

[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性を使用すると、アクションでサポートされる要求のコンテンツの種類を制限できます。 詳細については、「 [[使用] 属性を使用してサポートされる要求コンテンツ タイプを定義する](xref:web-api/index#consumes)」を参照してください。

 ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。

`[ApiController]`の詳細については、「 [ApiController 属性](xref:web-api/index##apicontroller-attribute)」を参照してください。

## <a name="route-name"></a>ルート名

次のコードでは、 の "`Products_List`ルート名" を定義しています。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

ルート名を使うと、特定のルートに基づいて URL を生成できます。 ルート名:

* ルーティングの URL マッチング動作には影響しません。
* URL 生成にのみ使用されます。

ルート名は、アプリケーション全体で一意である必要があります。

前のコードと、パラメータをオプション ( )`id``{id?}`として定義する従来の既定のルートと対比します。 API を正確に指定する機能には、さまざまなアクションへのアクセス`/products`許可`/products/5`やディスパッチなどの利点があります。

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>属性ルートの結合

属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。 コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。 ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

前の例の場合:

* URL パス`/products`は一致できます`ProductsApi.ListProducts`
* URL パス`/products/5`は、`ProductsApi.GetProduct(int)`と一致します。

これらのアクションはどちらも属性でマーク`GET`されているため、HTTP にのみ一致`[HttpGet]`します。

`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。 次の例では、既定のルートと同様の URL パスのセットに一致します。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

次の表に、`[Route]`上記のコードの属性を示します。

| 属性               | と組み合わせる`[Route("Home")]` | 工順テンプレートを定義します |
| ----------------- | ------------ | --------- |
| `[Route("")]` | はい | `"Home"` |
| `[Route("Index")]` | はい | `"Home/Index"` |
| `[Route("/")]` | **いいえ** | `""` |
| `[Route("About")]` | はい | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>属性の工順順序

ルーティングはツリーを構築し、すべてのエンドポイントに同時に一致します。

* ルート エントリは、理想的な順序で配置された場合と同様に動作します。
* 最も具体的なルートは、より一般的なルートの前に実行する機会があります。

たとえば、属性ルートのような`blog/search/{topic}`属性ルートは、 のような`blog/{*article}`属性ルートよりも具体的です。 ルート`blog/search/{topic}`の優先順位は、より具体的であるため、既定ではより高くなります。 [従来のルーティング](#cr)を使用して、開発者は、希望の順序でルートを配置する責任があります。

属性ルートは、プロパティを使用して注文<xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>を設定できます。 フレームワークで提供されるすべての[ルート属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)には、`Order`が含まれます。 ルートは、`Order` プロパティの昇順に従って処理されます。 既定の順序は `0` です。 注文を設定しない`Order = -1`ルートの前に、ランを使用してルートを設定する。 デフォルトのルート順序`Order = 1`付け後に実行を使用してルートを設定する。

**Avoid**に依存しないように`Order`します。 アプリの URL 領域が正しくルーティングするために明示的な順序値を必要とする場合、クライアントにも混乱を招く可能性があります。 一般に、属性ルーティングは URL 照合を使用して正しいルートを選択します。 URL 生成に使用される既定の順序が機能しない場合、通常は`Order`、オーバーライドとしてルート名を使用する方が、プロパティを適用するよりも簡単です。

両方ともルートマッチング`/home`を定義する次の2つのコントローラを考えてみましょう。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

上記`/home`のコードを要求すると、次のような例外がスローされます。

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

ルート`Order`属性の 1 つに追加すると、あいまいさが解決されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

上記のコードを使用して`/home`、エンドポイント`HomeController.Index`を実行します。 に行くために、`MyDemoController.MyIndex`要求`/home/MyIndex`. **注**: 

* 上記のコードは、例または不適切なルーティング設計です。 これは、`Order`プロパティを説明するために使用されました。
* この`Order`プロパティは、テンプレートを照合できないあいまいさを解決するだけです。 テンプレートを削除することをお`[Route("Home")]`しだい。

[Razor ページのルート順序については、「Razor ページのルートとアプリの規則: ルートの順序](xref:razor-pages/razor-pages-conventions#route-order)」を参照してください。

場合によっては、あいまいなルートを伴う HTTP 500 エラーが返されます。 [ログを](xref:fundamentals/logging/index)使用して、どのエンドポイントが原因`AmbiguousMatchException`となっているかを確認します。

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>ルートテンプレート[コントローラ]でのトークン置換、[アクション]、[エリア]

便宜上、属性ルートは、次のいずれかにトークンを囲むことで予約ルート パラメータのトークンの置換をサポートします。

* 四角いブレース:`[]`
* 中かっこ:`{}`

トークン`[action]`、、`[area]`および`[controller]`は、ルートが定義されているアクションのアクション名、エリア名、およびコントローラー名の値に置き換えられます。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

上のコードでは以下の操作が行われます。

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * 一致`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * 一致`/Products0/Edit/{id}`

属性ルート作成の最後のステップで、トークンの置換が発生します。 前の例は、次のコードと同じように動作します。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

属性ルートを継承と組み合わせることもできます。 これは、トークンの置換と組み合わせた強力な機能です。 トークンの置換は、属性ルートで定義されているルート名にも適用されます。
`[Route("[controller]/[action]", Name="[controller]_[action]")]`では、アクションごとに一意のルート名が生成されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

トークンの置換は、属性ルートで定義されているルート名にも適用されます。
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 では、アクションごとに一意のルート名が生成されます。

リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする

トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。 パラメーター トランスフォーマーは <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> を実装し、パラメーターの値を変換します。 たとえば、カスタム`SlugifyParameterTransformer`パラメータ トランスフォーマは`SubscriptionManagement`ルート値を`subscription-management`に変更します。

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> は、次のようなアプリケーション モデルの規則です。

* パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。
* 置き換えられる属性ルートのトークン値をカスタマイズします。

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

上記の`ListAll`メソッドは、`/subscription-management/list-all`と一致します。

`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

スラグの定義については[、Slug の MDN Web ドキュメント](https://developer.mozilla.org/docs/Glossary/Slug)を参照してください。

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>複数の属性ルート

属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。 これの最も一般的な使用方法は、次の例で示すように、"既定の規則ルート" の動作を模倣することです。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

コントローラに複数のルート属性を配置すると、それぞれがアクションメソッドの各ルート属性と組み合わされます。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

すべての[HTTP 動詞](#verb)ルート制約`IActionConstraint`が実装されます。

実装<xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>する複数のルート属性がアクションに配置されている場合:

* 各アクション制約は、コントローラに適用されたルート テンプレートと組み合わされます。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

アクションで複数のルートを使用すると便利で強力に思えるかもしれませんが、アプリの URL 空間を基本的かつ適切に定義しておく方が適しています。 既存のクライアントをサポートするために、必要な場合**にのみ**、複数のルートを使用します。

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>属性ルートの省略可能なパラメーター、既定値、制約を指定する

属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

上記のコードでは、`[HttpPost("product/{id:int}")]`ルート制約を適用します。 アクション`ProductsController.ShowProduct`は、 のような`/product/3`URL パスによってのみ照合されます。 ルート テンプレート部分`{id:int}`は、そのセグメントを整数のみに制約します。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>カスタム ルート属性を使用して、プロバイダー

すべての[ルート属性](#rt)が実装<xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>されます。 ASP.NETコアランタイム:

* アプリの起動時にコントローラー クラスとアクション メソッドの属性を検索します。
* 実装`IRouteTemplateProvider`する属性を使用して、ルートの初期セットを構築します。

カスタム`IRouteTemplateProvider`ルート属性を定義するために実装します。 各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

上記の`Get`メソッドは、`Order = 2, Template = api/MyTestApi`を返します。

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>アプリケーション モデルを使用して属性ルートをカスタマイズする

アプリケーション モデル:

* 起動時に作成されるオブジェクト モデルです。
* アプリでアクションをルーティングして実行するために ASP.NET Core が使用するすべてのメタデータが含まれます。

アプリケーション モデルには、ルート属性から収集されたすべてのデータが含まれます。 ルート属性からのデータは、実装によって提供`IRouteTemplateProvider`されます。 規則：

* アプリケーション モデルを変更してルーティングの動作をカスタマイズするために記述できます。
* アプリの起動時に読み取られます。

このセクションでは、アプリケーション モデルを使用したルーティングのカスタマイズの基本的な例を示します。 次のコードでは、ルートがプロジェクトのフォルダ構造とほぼ同じになります。

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

次のコードは、属性`namespace`ルーティングされているコントローラに規則が適用されないようにします。

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

たとえば、次のコントローラはを使用`NamespaceRoutingConvention`しません。

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply` メソッドは、以下の操作を行います。

* コントローラが属性ルーティングの場合は何もしません。
* ベース`namespace``namespace`を削除した後に、 に基づいてコントローラ テンプレートを設定します。

は`NamespaceRoutingConvention`で適用できます`Startup.ConfigureServices`。

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

たとえば、次のコントローラを考えます。

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

上のコードでは以下の操作が行われます。

* ベース`namespace`は`My.Application`です。
* 上記のコントローラの完全名は です`My.Application.Admin.Controllers.UsersController`。
* コントローラ`NamespaceRoutingConvention`テンプレートを に`Admin/Controllers/Users/[action]/{id?`設定します。

コントローラ`NamespaceRoutingConvention`の属性として適用することもできます。

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>混合ルーティング: 属性ルーティングと規則ルーティング

ASP.NET Core アプリは、従来のルーティングと属性ルーティングの使用を混在させることができます。 一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。

アクションは、規則的にルーティングされるか、または属性でルーティングされます。 コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。 属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。 コントローラ上**の任意**のルート属性は、コントローラ属性**のすべての**アクションをルーティングします。

属性ルーティングと従来のルーティングでは、同じルーティング エンジンを使用します。

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL 生成とアンビエント値

アプリは、ルーティング URL 生成機能を使用して、アクションへの URL リンクを生成できます。 URL を生成すると、URL のハードコーディングが不要になり、コードの堅牢性と保守性が向上します。 このセクションでは、MVC で提供される URL 生成機能に焦点を当て、URL 生成の仕組みの基本のみを取り上げる。 URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。

インターフェイス<xref:Microsoft.AspNetCore.Mvc.IUrlHelper>は、MVC と URL 生成のためのルーティングの間のインフラストラクチャの基になる要素です。 インスタンス`IUrlHelper`は、コントローラ、ビュー、`Url`およびビュー コンポーネントのプロパティを通じて使用できます。

次の例では、インターフェイス`IUrlHelper`をプロパティを通じて`Controller.Url`使用して、別のアクションへの URL を生成します。

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

アプリが既定の従来のルートを使用している場合、変数の`url`値は URL パス`/UrlGeneration/Destination`文字列 です。 この URL パスは、次の組み合わせによってルーティングによって作成されます。

* 現在の要求からのルート値 (**アンビエント値**と呼ばれます ) 。
* 渡`Url.Action`された値とこれらの値をルート テンプレートに代入します。

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。 値を持たないルート パラメータは、次のことができます。

* 既定値がある場合は、既定値を使用します。
* 省略可能な場合はスキップします。 たとえば、`id`からルート テンプレート`{controller}/{action}/{id?}`を使用します。

必要なルート パラメータに対応する値がない場合、URL 生成は失敗します。 ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。

前の例では、`Url.Action`[従来のルーティング](#cr)を想定しています。 URL の生成は[属性ルーティング](#ar)と同様に機能しますが、概念は異なります。 従来のルーティングでは、

* 工順の値は、テンプレートを展開するために使用されます。
* ルート値は`controller`、`action`通常、そのテンプレートに表示されます。 これは、ルーティングによって一致する URL が規則に従っているためです。

次の例では、属性ルーティングを使用します。

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

上記`Source`のコードのアクションによって、 が`custom/url/to/destination`生成されます。

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>の代替として ASP.NET Core 3.0`IUrlHelper`で追加されました。 `LinkGenerator`は、類似するが、より柔軟な機能を提供します。 各`IUrlHelper`メソッドには、対応するメソッド ファミリ`LinkGenerator`も含まれています。

### <a name="generating-urls-by-action-name"></a>アクション名による URL の生成

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) [、LinkGenerator.GetPathByAction、](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)および関連するすべてのオーバーロードは、コントローラ名とアクション名を指定してターゲットエンドポイントを生成するように設計されています。

を使用`Url.Action`する場合、現在のルート`controller`値`action`は、ランタイムによって提供されます。

* `controller`の値と`action`は[、アンビエント値と値](#ambient)の両方の一部です。 メソッド`Url.Action`は常に現在の値を`action`使用`controller`し、現在のアクションにルーティングする URL パスを生成します。

ルーティングは、アンビエント値の値を使用して、URL の生成時に提供されなかった情報を入力しようとします。 アンビエント値`{a}/{b}/{c}/{d}``{ a = Alice, b = Bob, c = Carol, d = David }`のようなルートを考えてみましょう:

* ルーティングには、追加の値を含まない URL を生成するのに十分な情報があります。
* すべてのルート パラメータに値があるため、ルーティングには十分な情報があります。

値`{ d = Donovan }`が追加された場合:

* 値`{ d = David }`は無視されます。
* 生成される URL`Alice/Bob/Carol/Donovan`パスは です。

**警告**: URL パスは階層的です。 前の例では、値`{ c = Cheryl }`が追加された場合は次のようになります。

* 両方の値`{ c = Carol, d = David }`は無視されます。
* 値がなくなり`d`、URL 生成が失敗します。
* URL を`c`生成するために`d`必要な値と の値を指定する必要があります。  

既定のルート`{controller}/{action}/{id?}`でこの問題が発生すると予想される場合があります。 この問題は、常に明示的`Url.Action`に値`controller`を`action`指定するため、実際にはまれです。

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)のいくつかのオーバーロードは、ルート値オブジェクトを受け取り、`controller``action`および 以外のルート パラメータの値を提供します。 ルート値オブジェクトは、 でよく`id`使用されます。 たとえば、「 `Url.Action("Buy", "Products", new { id = 17 })` 」のように入力します。 ルート値オブジェクト:

* 通常、慣例では匿名型のオブジェクトです。
* `IDictionary<>`または[POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)を指定できます。

ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

上記のコードは、`/Products/Buy/17?color=red`を生成します。

次のコードでは、絶対 URL が生成されます。

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

絶対 URL を作成するには、次のいずれかを使用します。

* を受け入れるオーバーロード`protocol`。 たとえば、上記のコードを使用します。
* 既定で絶対 URI[を生成](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)する。

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>ルート別の URL の生成

上記のコードでは、コントローラとアクション名を渡して URL を生成する方法を示しました。 `IUrlHelper`また、メソッドの[Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)ファミリも提供します。 これらのメソッドは[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)に似ていますが、ルート値と`action``controller`ルート値に現在の値はコピーしません。 の最も一般的`Url.RouteUrl`な使用法:

* URL を生成するルート名を指定します。
* 通常、コントローラー名やアクション名は指定しません。

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

次の Razor ファイルは、 への`Destination_Route`HTML リンクを生成します。

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>HTML とカミソリで URL を生成する

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>には、<xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>それぞれ生成するメソッドを提供`<form>`する Html.BeginForm`<a>`と[Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*)が用意されています。 [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) これらのメソッドは[、Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)メソッドを使用して URL を生成し、同様の引数を受け取ります。 `HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。

TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。 これらはどちらも、実装に `IUrlHelper` を使います。 詳細については、「[フォームのタグ ヘルパー](xref:mvc/views/working-with-forms) 」を参照してください。

ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>アクション結果の URL 生成

上記の例は、コントローラ`IUrlHelper`での使用を示しました。 コントローラで最もよく使用される用途は、アクション結果の一部として URL を生成することです。

基底クラス <xref:Microsoft.AspNetCore.Mvc.ControllerBase> および <xref:Microsoft.AspNetCore.Mvc.Controller> では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。 一般的な使用法の 1 つは、ユーザー入力を受け入れた後にリダイレクトすることです。

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

アクションは、ファクトリ メソッドなどの<xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*>メソッド<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>を生成し、そのメソッドと同様`IUrlHelper`のパターンに従います。

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>専用規則ルートの特殊なケース

[従来のルーティング](#cr)は専用の[従来](#dcr)ルートと呼ばれる特殊なルート定義を使用できます。 次の例では、名前の付`blog`いたルートは専用の従来のルートです。

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

上記のルート定義を使用して`Url.Action("Index", "Home")`、ルートを使用して`/`URL`default`パスを生成しますが、なぜですか? ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。

[専用の従来のルートは、](#dcr)ルートが URL 生成に[強欲](xref:fundamentals/routing#greedy)すぎることを防ぐ対応するルート パラメーターを持たない既定値の特別な動作に依存します。 この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。 ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。 URL の`blog`生成は、値`{ controller = Home, action = Index }`が一致`{ controller = Blog, action = Article }`しないため失敗します。 そして、ルーティングはフォールバックして `default` を試み、これは成功します。

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Areas

[領域](xref:mvc/controllers/areas)は、関連する機能を別のグループに整理するために使用される MVC 機能です。

* コントローラーアクションのルーティング名前空間。
* ビューのフォルダ構造。

エリアを使用すると、アプリは、異なる領域を持つ限り、同じ名前の複数のコントローラーを持つことができます。 区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。 ここでは、ルーティングがエリアとどのように相互作用するかを説明します。 [エリア](xref:mvc/controllers/areas)がビューで使用される方法の詳細については、「エリア」を参照してください。

次の例では、既定の従来のルートと名前の`area``area``Blog`ルートを使用するように MVC を構成します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

上記のコードでは、<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>が呼び出され`"blog_route"`、 が作成されます。 2 番目の`"Blog"`パラメータはエリア名です。

のような`/Manage/Users/AddUser`URL パスを一致`"blog_route"`させると、ルートは`{ area = Blog, controller = Users, action = AddUser }`ルート値 を生成します。 ルート`area`値は、 の`area`既定値で生成されます。 作成される`MapAreaControllerRoute`ルートは、次のルートと同じです。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。 既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。

規則ルーティングは順序に依存します。 一般に、エリアを含むルートは、エリアのないルートよりも具体的であるため、早めに配置する必要があります。

上記の例を使用すると、ルート値`{ area = Blog, controller = Users, action = AddUser }`は次のアクションと一致します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[[エリア]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性は、エリアの一部としてコントローラを表します。 このコントローラはエリア内`Blog`にあります。 属性のない`[Area]`コントローラは、どのエリアのメンバでもなく、`area`ルーティングによってルート値が提供される場合は一致**しません**。 次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

完全な処理のために、各コントローラの名前空間をここに示します。 上記のコントローラが同じ名前空間を使用している場合、コンパイラ エラーが生成されます。 クラスの名前空間は、MVC のルーティングに対して影響を与えません。

最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。 3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。

<a name="aa"></a>

"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。

エリア内でアクションを実行する場合、ルート値`area`は URL 生成に使用するルーティングの[アンビエント値](#ambient)として使用できます。 つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

次のコードは、 への`/Zebra/Users/AddUser`URL を生成します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>アクション定義

[NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute)属性を持つメソッドを除く、コントローラー上のパブリック メソッドはアクションです。

## <a name="sample-code"></a>サンプル コード

 * [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。
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

上記のコードは、従来のルーティングの例です。 このスタイルは、URL パスの*規則*を確立するため、従来型ルーティングと呼ばれます。

* 最初のパス セグメントは、コントローラ名にマップされます。
* 2 つ目はアクション名にマップされます。
* 3 番目のセグメントは、省略`id`可能な . `id`はモデル エンティティにマップされます。

この `default` ルートを使うと、URL パス `/Products/List` は `ProductsController.List` アクションにマップし、`/Blog/Article/17` は `BlogController.Article` にマップします。 このマッピングは、コントローラーとアクションの名前に**のみ**基づき、名前空間、ソース ファイルの場所、またはメソッドのパラメーターには基づきません。

> [!TIP]
> 既定のルートで規則ルーティングを使うと、定義するアクションごとに新しい URL パターンを考える必要がなく、アプリケーションをすばやく構築できます。 CRUD スタイルのアクションを使うアプリケーションでは、コントローラー間で URL に一貫性を持たせると、コードが容易になり、UI の予測可能性が向上します。

> [!WARNING]
> `id` はルート テンプレートで省略可能と定義されており、これは URL の一部として ID を提供されなくてもアクションを実行できることを意味します。 通常、`id` が URL から省略されると、ID はモデル バインドによって `0` に設定され、結果として、`id == 0` と一致するエンティティはデータベースで検索されません。 属性ルーティングを使うと、ID が必須のアクションと必須ではないアクションをきめ細かく制御できます。 慣例に従って、ドキュメントでは `id` などの省略可能なパラメーターが正しい使用法で使われる可能性がある場合はパラメーターを記載してあります。

## <a name="multiple-routes"></a>複数のルート

`MapRoute` の呼び出しをさらに追加することで、`UseMvc` の内部に複数のルートを追加できます。 これにより、次のように、複数の規則を定義すること、または特定のアクションに専用の規則ルートを追加することができます。

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
> *専用の従来のルートでは、URL*パスの残`{*article}`りの部分をキャプチャするなどの**キャッチオール**ルート パラメータを使用することがよくあります。 このようにすると、ルートの "一致範囲が広くなりすぎて"、他のルートと一致させるつもりであった URL まで一致する可能性があります。 この問題を解決するには、"一致範囲の広い" ルートはルート テーブルの後の方に配置します。

### <a name="fallback"></a>フォールバック

要求処理の一環として、MVC はルートの値を使ってアプリケーション内のコントローラーとアクションを検索できることを確認します。 ルートの値がアクションと一致しない場合、そのルートは一致と見なされず、次のルートが試されます。 これは "*フォールバック*" と呼ばれ、規則ルートがオーバーラップしている場合の簡略化を意図したものです。

### <a name="disambiguating-actions"></a>アクションの明確化

2 つのアクションがルーティングで一致する場合、MVC はあいまいさを解消して "最善の" 候補を選ぶか、または例外をスローする必要があります。 次に例を示します。

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

このコントローラーでは、URL パス `/Products/Edit/17` およびルート データ `{ controller = Products, action = Edit, id = 17 }` と一致する 2 つのアクションが定義されています。 これは、`Edit(int)` で製品を編集するためのフォームを表示し、`Edit(int, Product)` で送信されたフォームを処理する MVC コントローラーの一般的なパターンです。 これを MVC で可能にするには、要求が HTTP `POST` の場合は `Edit(int, Product)` を選び、それ以外の HTTP 動詞の場合は `Edit(int)` を選ぶ必要があります。

`HttpPostAttribute` (`[HttpPost]`) は、HTTP 動詞が `POST` の場合にのみそのアクションの選択を許可する `IActionConstraint` の実装です。 `IActionConstraint` が存在すると、`Edit(int, Product)` の方が `Edit(int)` より "良い" 一致になるので、`Edit(int, Product)` が最初に試されます。

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

属性ルーティングでは、`HttpPostAttribute` などの `Http[Verb]` 属性も使うことができます。 これらの属性はすべて、ルート テンプレートを受け入れることができます。 次の例では、同じルート テンプレートと一致する 2 つのアクションが示されています。

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

`/products` のような URL パスの場合、HTTP 動詞が `GET` のときは `ProductsApi.ListProducts` アクションが実行され、HTTP 動詞が `POST` のときは `ProductsApi.CreateProduct` が実行されます。 属性ルーティングでは、最初に、ルート属性で定義されているルート テンプレートのセットに対して URL が照合されます。 ルート テンプレートが一致すると、`IActionConstraint` 制約が適用されて、実行できるアクションが決定されます。

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

次のコードでは、`Products_List` の "*ルート名*" を定義しています。

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

次の例では、URL パス `/products` は `ProductsApi.ListProducts` と一致でき、URL パス `/products/5` は `ProductsApi.GetProduct(int)` と一致できます。 どちらのアクションも、`HttpGetAttribute` でマークされているため、HTTP `GET` だけと一致します。

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

便宜上、属性ルートは *、トークン*を角かっこ (`[`, )`]`で囲むことでトークンの置換をサポートします。 トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、区分名、コントローラー名の値に置き換えられます。 次の例では、アクションはコメントで説明されているように URL パスと一致します。

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

上の例の属性は、`[MyApiController]` が適用されると、自動的に `Template` を `"api/[controller]"` に設定します。

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

`Url.Action` (`IUrlHelper` . `Action`) およびすべての関連するオーバーロードは、コントローラー名とアクション名を指定することによってリンク先を指定するアイデアに基づきます。

> [!NOTE]
> `Url.Action` を使うと、`controller` および `action` の現在のルート値が自動的に指定されます。`controller` および `action` の値は、"*アンビエント値" * **と "** *値*" 両方の一部です。 `Url.Action` メソッドは、常に `action` と `controller` の現在の値は使い、現在のアクションにルーティングする URL パスを生成します。

ルーティングは、ユーザーが URL 生成時に指定しなかった情報を、アンビエント値を使って埋めようとします。 `{a}/{b}/{c}/{d}` やアンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }` のようなルートを使うと、すべてのルート パラメーターが値を持っているため、ルーティングには URL を生成するための十分な情報があり、追加の値は必要ありません。 値 `{ d = Donovan }` を追加した場合は、値 `{ d = David }` は無視されて、生成される URL パスは `Alice/Bob/Carol/Donovan` になります。

> [!WARNING]
> URL パスは階層的です。 上の例で、値 `{ c = Cheryl }` を追加した場合は、両方の値 `{ c = Carol, d = David }` が無視されます。 この場合、`d` には値がなくなり、URL の生成は失敗します。 `c` および `d` の目的の値を指定する必要があります。  既定のルート (`{controller}/{action}/{id?}`) でもこの問題が発生すると思われるかもしれませんが、`Url.Action` が常に `controller` と `action` の値を明示的に指定するので、実際には、このような動作が発生することはほとんどありません。

`Url.Action` の長いオーバーロードも、追加の "*ルート値*" オブジェクトを受け取って、`controller` および `action` 以外のルート パラメーターの値を提供します。 これを最もよく目にするのは、`Url.Action("Buy", "Products", new { id = 17 })` のように `id` で使われる場合です。 慣例では、"*ルート値*" オブジェクトは通常は匿名型のオブジェクトですが、`IDictionary<>` または "*単純な従来の .NET オブジェクト*" を使うこともできます。 ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> 絶対 URL を作成するには、`protocol` を受け取るオーバーロードを使います。`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>ルートによる URL の生成

上記のコードでは、コントローラーとアクション名を渡すことによる URL の生成を示しました。 `IUrlHelper` では、`Url.RouteUrl` ファミリ メソッドも提供されています。 これらのメソッドは、`Url.Action` と似ていますが、`action` および `controller` の現在の値をルート値にコピーしません。 最も一般的な使用方法は、ルート名を指定して特定のルートを使って URL を生成する場合です。通常、コントローラーまたはアクション名は指定 "*しません*"。

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>HTML での URL の生成

`IHtmlHelper` で提供されている `HtmlHelper` メソッドの `Html.BeginForm` および `Html.ActionLink` は、それぞれ `<form>` 要素と `<a>` 要素を生成します。 これらのメソッドは `Url.Action` メソッドを使って URL を生成するので、同じような引数を受け取ります。 `HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。

TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。 これらはどちらも、実装に `IUrlHelper` を使います。 詳しくは、「[フォームの操作](../views/working-with-forms.md)」をご覧ください。

ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。

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

これらのルート定義を使うと、`Url.Action("Index", "Home")` は `default` ルートで URL パス `/` を生成します。なぜでしょうか。 ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。

専用規則ルートは、URL の生成でルートの "一致範囲が広くなりすぎる" のを防ぐために対応するルート パラメーターを持たない既定値の特別な動作に依存します。 この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。 ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。 値 `{ controller = Home, action = Index }` は `{ controller = Blog, action = Article }` と一致しないため、`blog` を使った URL の生成は失敗します。 そして、ルーティングはフォールバックして `default` を試み、これは成功します。

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Areas

[区分](areas.md)は MVC の機能であり、関連する機能を独立したルーティング名前空間 (コントローラー アクションの場合) およびフォルダー構造 (ビューの場合) としてグループ化するために使われます。 区分を使うと、アプリケーションは同じ名前の複数のコントローラーを持つことができます (ただし、"*区分*" が異なる場合)。 区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。 このセクションでは、ルーティングと区分がどのように相互作用するのかについて説明します。ビューでの区分の使い方について詳しくは、「[区分](areas.md)」をご覧ください。

次の例では、既定の規則ルートと、`Blog` という名前の区分に対する "*区分ルート*" を使うように、MVC を構成しています。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

`/Manage/Users/AddUser` のような URL パスを照合すると、最初のルートではルート値 `{ area = Blog, controller = Users, action = AddUser }` が生成されます。 `area` ルート値は、`area` の既定値によって生成されます。実際には、`MapAreaRoute` によって作成されるルートは以下と同等です。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。 既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。

> [!TIP]
> 規則ルーティングは順序に依存します。 一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。

上の例を使うと、ルート値は次のアクションと一致します。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` はコントローラーが区分の一部であることを示すものであり、このコントローラーは `Blog` 区分に含まれます。 `[Area]` 属性を持たないコントローラーはどの区域のメンバーでもなく、`area` ルート値がルーティングによって提供されたときは一致**しません**。 次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。

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

次の例では、制約は、ルート データから*国コード*に基づいてアクションを選択します。 [GitHub の完全なサンプルはこちら](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)をご覧ください。

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

ユーザーは、`Accept` メソッドを実装し、実行する制約の "順序" を選ぶ必要があります。 この例では、`country` ルート値が一致すると、`Accept` メソッドは `true` を返してアクションが一致することを示します。 この動作は、非属性アクションにフォールバックできる `RouteValueAttribute` とは異なります。 サンプルでは、`en-US` アクションを定義すると、`fr-FR` のような国コードは `[CountrySpecific(...)]` が適用されていない、より一般的なコントローラーにフォールバックすることが示されています。

`Order` プロパティは、制約が含まれる "*ステージ*" を決定します。 アクションの制約は、`Order` に基づいてグループで実行されます。 たとえば、フレームワークで提供されるすべての HTTP メソッド属性は、同じステージで実行されるように、同じ `Order` 値を使います。 目的のポリシーを実装するため、必要なだけいくつでもステージを使うことができます。

> [!TIP]
> `Order` の値を決めるときは、HTTP メソッドより前に制約を適用する必要があるかどうかを考えます。 小さい値が先に実行されます。

::: moniker-end
