---
title: ASP.NET Core でのコントローラー アクションへのルーティング
author: rick-anderson
description: ASP.NET Core MVC でルーティング ミドルウェアを使って、受信した要求の URL を照合し、アクションにマップする方法について説明します。
ms.author: riande
ms.date: 01/24/2019
uid: mvc/controllers/routing
ms.openlocfilehash: a0dbfbe60c151990581b494f81e500fe0b315f55
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589855"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core でのコントローラー アクションへのルーティング

作成者: [Ryan Nowak](https://github.com/rynowak)、[Rick Anderson](https://twitter.com/RickAndMSFT)

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

既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。 ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](../../fundamentals/routing.md#route-template-reference)」をご覧ください。

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

これは、"*規則ルーティング*" の例です。 このスタイルを "*規則ルーティング*" と呼ぶのは、それが URL パスの "*規則*" を作成するためです。

* 最初のパス セグメントは、コントローラー名にマップします

* 2 つ目は、アクション名にマップします。

* 3 番目のセグメントは、モデル エンティティへのマップに使われる省略可能な `id` に使われます

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
> "*専用の規則ルート*" は、多くの場合、`{*article}` のようなキャッチオール ルート パラメーターを使って、URL パスの残りの部分をキャプチャします。 このようにすると、ルートの "一致範囲が広くなりすぎて"、他のルートと一致させるつもりであった URL まで一致する可能性があります。 この問題を解決するには、"一致範囲の広い" ルートはルート テーブルの後の方に配置します。

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

`ProductsApi.GetProduct(int)` アクションは、`/products/3` のような URL パスに対しては実行されますが、`/products` のような URL パスに対しては実行されません。 ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](../../fundamentals/routing.md)」をご覧ください。

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

次の例では、URL パス `/products` は `ProductsApi.ListProducts` と一致でき、URL パス `/products/5` は `ProductsApi.GetProduct(int)` と一致できます。 どちらのアクションも、`HttpGetAttribute` で修飾されているため、HTTP `GET` だけと一致します。

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

定義されている順序で実行される規則ルートとは異なり、属性ルーティングはツリーを作成して、すべてのルートと同時に一致します。 これは、ルート エントリが理想的な順序で配置されているかのように動作します。一般的なルートより前に、最も固有のルートが実行する機会があります。

たとえば、`blog/search/{topic}` のようなルートは、`blog/{*article}` のようなルートより固有です。 論理的には、`blog/search/{topic}` ルートが唯一の意味のある順序なので、既定では、これが最初に "実行" されます。 規則ルーティングを使うときは、開発者が目的の順序でルートを配置する必要があります。

属性ルートでは、フレームワークが提供するすべてのルート属性の `Order` プロパティを使って、順序を構成できます。 ルートは、`Order` プロパティの昇順に従って処理されます。 既定の順序は `0` です。 `Order = -1` を使ってルートを設定すると、順序が設定されていないルートの前に実行されます。 `Order = 1` を使ってルートを設定すると、既定のルート順序の後で実行されます。

> [!TIP]
> `Order` には依存しないでください。 URL 空間で正しくルーティングするために明示的な順序値が必要な場合、クライアントの混乱を招く可能性があります。 一般に、属性ルーティングは URL 照合で正しいルートを選びます。 URL の生成に使われる既定の順序がうまくいかない場合は、通常、オーバーライドとしてルート名を使う方が、`Order` プロパティを適用するより簡単です。

Razor Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。 Razor Pages でのルート順序に関する情報は、[Razor Pages のルートとアプリの規則: ルート順序](xref:razor-pages/razor-pages-conventions#route-order)に関する記事を参照してください。

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>ルート テンプレートでのトークンの置換 ([controller], [action], [area])

利便性のため、属性ルートではトークンを角かっこ (`[`、`]`) で囲むことによる "*トークンの置換*" がサポートされています。 トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、区分名、コントローラー名の値に置き換えられます。 次の例では、アクションはコメントで説明されているように URL パスと一致します。

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

属性ルート作成の最後のステップで、トークンの置換が発生します。 上の例は、次のコードと同じように動作します。

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

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

::: moniker range=">= aspnetcore-2.2"

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

ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](../../fundamentals/routing.md#route-template-reference)」をご覧ください。

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

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>混合ルーティング:属性ルーティングと規則ルーティング

MVC アプリケーションでは、規則ルーティングと属性ルーティングを併用できます。 一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。

アクションは、規則的にルーティングされるか、または属性でルーティングされます。 コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。 属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。 コントローラーの**任意**のルート属性により、コントローラー属性のすべてのアクションがルーティングされます。

> [!NOTE]
> 2 種類のルーティング システムを区別しているのは、URL がルート テンプレートと一致した後に適用されるプロセスです。 規則ルーティングでは、一致のルート値を使って、規則ルーティングされるすべてのアクションの参照テーブルから、アクションとコントローラーが選ばれます。 属性ルーティングでは、各テンプレートはアクションと既に関連付けられており、それ以上参照する必要はありません。

## <a name="complex-segments"></a>複雑なセグメント

複雑なセグメント (たとえば、`[Route("/dog{token}cat")]`) は、右から左にリテラルを最短の方法で照合することによって処理されます。 説明については、[ソース コード](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)をご覧ください。 詳しくは、[こちらの懸案事項](https://github.com/aspnet/AspNetCore.Docs/issues/8197)をご覧ください。

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL の生成

MVC アプリケーションでは、ルーティングの URL 生成機能を使って、アクションへの URL リンクを生成できます。 URL を生成すると URL をハードコーディングする必要がなくなり、コードの堅牢性と保守性が向上します。 このセクションでは、MVC によって提供される URL 生成機能について説明します。URL 生成のしくみに関する基本だけを取り上げます。 URL の生成について詳しくは、「[ルーティング](../../fundamentals/routing.md)」をご覧ください。

`IUrlHelper` インターフェイスは、MVC と URL 生成のルーティングの間にあるインフラストラクチャの基盤部分です。 使用可能な `IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビュー コンポーネントの `Url` プロパティを使って探します。

この例の `IUrlHelper` インターフェイスは、別のアクションへの URL を生成するために `Controller.Url` プロパティを介して使われています。

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

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

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC はすべての属性ルーティングされるアクションの参照テーブルを作成し、`controller` と `action` の値で照合して、URL 生成に使うルート テンプレートを選びます。 上の例では、`custom/url/to/destination` が生成されます。

### <a name="generating-urls-by-action-name"></a>アクション名による URL の生成

`Url.Action` (`IUrlHelper`. `Action`) およびすべての関連するオーバーロードは、コントローラー名とアクション名を指定することによってリンク先を指定するアイデアに基づきます。

> [!NOTE]
> `Url.Action` を使うと、`controller` および `action` の現在のルート値が自動的に指定されます。`controller` および `action` の値は、"*アンビエント値*" **と** "*値*" 両方の一部です。 `Url.Action` メソッドは、常に `action` と `controller` の現在の値は使い、現在のアクションにルーティングする URL パスを生成します。

ルーティングは、ユーザーが URL 生成時に指定しなかった情報を、アンビエント値を使って埋めようとします。 `{a}/{b}/{c}/{d}` やアンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }` のようなルートを使うと、すべてのルート パラメーターが値を持っているため、ルーティングには URL を生成するための十分な情報があり、追加の値は必要ありません。 値 `{ d = Donovan }` を追加した場合は、値 `{ d = David }` は無視されて、生成される URL パスは `Alice/Bob/Carol/Donovan` になります。

> [!WARNING]
> URL パスは階層的です。 上の例で、値 `{ c = Cheryl }` を追加した場合は、両方の値 `{ c = Carol, d = David }` が無視されます。 この場合、`d` には値がなくなり、URL の生成は失敗します。 `c` および `d` の目的の値を指定する必要があります。  既定のルート (`{controller}/{action}/{id?}`) でもこの問題が発生すると思われるかもしれませんが、`Url.Action` が常に `controller` と `action` の値を明示的に指定するので、実際には、このような動作が発生することはほとんどありません。

`Url.Action` の長いオーバーロードも、追加の "*ルート値*" オブジェクトを受け取って、`controller` および `action` 以外のルート パラメーターの値を提供します。 これを最もよく目にするのは、`Url.Action("Buy", "Products", new { id = 17 })` のように `id` で使われる場合です。 慣例では、"*ルート値*" オブジェクトは通常は匿名型のオブジェクトですが、`IDictionary<>` または "*単純な従来の .NET オブジェクト*" を使うこともできます。 ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> 絶対 URL を作成するには、`protocol` を受け取るオーバーロードを使います。`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>ルートによる URL の生成

上記のコードでは、コントローラーとアクション名を渡すことによる URL の生成を示しました。 `IUrlHelper` では、`Url.RouteUrl` ファミリ メソッドも提供されています。 これらのメソッドは、`Url.Action` と似ていますが、`action` および `controller` の現在の値をルート値にコピーしません。 最も一般的な使用方法は、ルート名を指定して特定のルートを使って URL を生成する場合です。通常、コントローラーまたはアクション名は指定 "*しません*"。

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

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

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

`/Manage/Users/AddUser` のような URL パスを照合すると、最初のルートではルート値 `{ area = Blog, controller = Users, action = AddUser }` が生成されます。 `area` ルート値は、`area` の既定値によって生成されます。実際には、`MapAreaRoute` によって作成されるルートは以下と同等です。

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。 既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。

> [!TIP]
> 規則ルーティングは順序に依存します。 一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。

上の例を使うと、ルート値は次のアクションと一致します。

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` はコントローラーが区分の一部であることを示すものであり、このコントローラーは `Blog` 区分に含まれます。 `[Area]` 属性を持たないコントローラーはどの区域のメンバーでもなく、`area` ルート値がルーティングによって提供されたときは一致**しません**。 次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> ここでは完全を期すために各コントローラーの名前空間を示してあります。そうしないと、コントローラーの名前が競合し、コンパイラ エラーが発生します。 クラスの名前空間は、MVC のルーティングに対して影響を与えません。

最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。 3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。

> [!NOTE]
> "*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。

区分の内部でアクションを実行するとき、`area` のルート値は、URL の生成に使うルーティングの "*アンビエント値*" として利用できます。 つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

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

次の例の制約は、ルート データの "*国コード*" に基づいてアクションを選びます。 [GitHub の完全なサンプルはこちら](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)をご覧ください。

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
