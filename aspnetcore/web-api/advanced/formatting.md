---
title: ASP.NET Core Web API の応答データの書式設定
author: ardalis
description: ASP.NET Core Web API で応答データを書式設定する方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: b050011aa38743353fb2a7d133abcdca0b8c6d33
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814813"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>ASP.NET Core Web API の応答データの書式設定

作成者: [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC には、応答データを書式設定するためのサポートが組み込まれています。固定の書式を利用するか、クライアントの仕様に合わせます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="format-specific-action-results"></a>書式固有アクションの結果

アクションの結果には、`JsonResult` や `ContentResult` のように、特定の形式に固有となる型があります。 アクションは、常に特定の方法で書式設定された結果を返すことができます。 たとえば、`JsonResult` を返すとき、クライアントの設定に関係なく、JSON で書式設定されたデータが返されます。 同様に、`ContentResult` を返すとき、プレーンテキストで書式設定された文字列データが返されます (単純に文字列が返されます)。

> [!NOTE]
> 特定の型を返すためにアクションは必要ありません。MVC ではあらゆるオブジェクト戻り値を利用できます。 アクションが `IActionResult` 実装を返し、コントローラーが `Controller` から継承する場合、開発者は選択肢の多くに対応するさまざまなヘルパー メソッドを利用できます。 型が `IActionResult` ではないオブジェクトを返すアクションからの結果は、適切な `IOutputFormatter` 実装を利用してシリアル化されます。

`Controller` 基底クラスから継承するコントローラーから特定の書式でデータを返すには、組み込みのヘルパー メソッド `Json` を使用して JSON を返し、`Content` を使用してプレーンテキストを返します。 アクション メソッドでは、特定の結果の型を返す必要があります (`JsonResult` や `IActionResult` など)。

JSON で書式設定されたデータを返す:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

このアクションからのサンプル応答:

![Microsoft Edge の開発者ツールの [ネットワーク] タブ。応答のコンテンツの種類が application/json です。](formatting/_static/json-response.png)

応答のコンテンツの種類が `application/json` であることに注目してください。ネットワーク要求の一覧と [応答ヘッダー] セクションの両方に表示されます。 また、ブラウザー (この場合、Microsoft Edge) により [応答ヘッダー] セクションの Accept ヘッダーに表示されるオプションの一覧に注目してください。 現在の手法ではこのヘッダーが無視されます。無視しない方法について以下で説明します。

プレーンテキストで書式設定されたデータを返すには、`ContentResult` と `Content` ヘルパーを使用します。

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

このアクションからの応答:

![Microsoft Edge の開発者ツールの [ネットワーク] タブ。応答のコンテンツの種類が text/plain です。](formatting/_static/text-response.png)

この場合、返される `Content-Type` は `text/plain` です。 応答の型として文字列を使用する方法でもこれと同じ動作が得られます。

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> 戻り値の型やオプションを複数ともなう重要なアクションの場合 (実行された操作の結果に基づき、さまざまな HTTP ステータス コードが生成されるなど)、戻り値の型として `IActionResult` を優先します。

## <a name="content-negotiation"></a>コンテンツ ネゴシエーション

クライアントにより [Accept ヘッダー](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)が指定されると、コンテンツ ネゴシエーション (省略形は *conneg*) が発生します。 ASP.NET Core MVC で使用される既定の書式は JSON です。 コンテンツ ネゴシエーションは `ObjectResult` によって実装されます。 (すべて `ObjectResult` に基づく) ヘルパー メソッドから返されるステータス コード固有のアクションの結果にも組み込まれます。 モデルの型を返すこともできます (データ転送の型として定義しているクラス)。フレームワークはこれを自動的に `ObjectResult` でラップします。

次のアクション メソッドでは、ヘルパー メソッドの `Ok` と `NotFound` が使用されます。

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

別の書式が要求され、その要求された書式をサーバーが返せる場合を除き、JSON で書式設定された応答が返されます。 [Fiddler](https://www.telerik.com/fiddler) のようなツールを利用し、Accept ヘッダーを含む要求を作成したり、別の書式を指定したりできます。 その場合、要求された書式で応答を生成できる*フォーマッタ*がサーバーに与えられている場合、クライアントの優先書式で結果が返されます。

![Fiddler コンソールに手動で作成された GET 要求が表示されているのを確認できます。Accept ヘッダー値が application/xml になっています。](formatting/_static/fiddler-composer.png)

上のスクリーンショットでは、要求の生成に Fiddler Composer が使用されています。`Accept: application/xml` を指定しています。 既定では、ASP.NET Core MVC は JSON のみに対応しています。そのため、別の書式が指定されていても、結果は JSON で書式設定されて返されます。 フォーマッタの追加方法は次のセクションで確認します。

コントローラー アクションでは、POCO (Plain Old CLR Object/単純な従来の CLR オブジェクト) を返すことができます。この場合、ASP.NET Core MVC によって、オブジェクトをラップする `ObjectResult` が自動的に作成されます。 書式設定され、シリアル化されたオブジェクトがクライアントに与えられます (JSON 書式が既定ですが、XML やその他の形式を設定できます)。 返されるオブジェクトが `null` の場合、フレームワークは `204 No Content` 応答を返します。

オブジェクトの型を返す:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

このサンプルでは、有効な作成者エイリアスを要求しています。200 OK という応答と作成者のデータが返されます。 無効なエイリアスを要求すると、204 No Content という応答が返されます。 下のスクリーンショットでは、XML 形式と JSON 形式の応答を確認できます。

### <a name="content-negotiation-process"></a>コンテンツ ネゴシエーション プロセス

コンテンツ *ネゴシエーション*は、`Accept` ヘッダーが要求に現れるときにのみ発生します。 要求に Accept ヘッダーが含まれると、フレームワークは Accept ヘッダーのメディアの種類を優先順序で列挙し、Accept ヘッダーによって指定される書式の 1 つで応答を生成できるフォーマットを探します。 クライアントの要求を満たせるフォーマッタが見つからない場合、フレームワークは、応答を生成できる最初のフォーマットを見つけようとします (406 Not Acceptable を代わりに返すよう、開発者が `MvcOptions` でオプションを構成していない限り)。 要求で XML が指定されているが、XML フォーマッタが構成されていない場合、JSON フォーマッタが使用されます。 もっと一般的に言うと、要求された書式を提供できるフォーマッタが構成されていない場合、オブジェクトを書式設定できる最初のフォーマッタが使用されます。 ヘッダーが与えられない場合、応答のシリアル化に、返すオブジェクトを処理できる最初のフォーマッタが使用されます。 この例では、ネゴシエーションは発生しません。使用する書式はサーバーが決定します。

> [!NOTE]
> Accept ヘッダーに `*/*` が含まれる場合、`RespectBrowserAcceptHeader` が `MvcOptions` で true に設定されていない限り、ヘッダーが無視されます。

### <a name="browsers-and-content-negotiation"></a>ブラウザーとコンテンツ ネゴシエーション

一般的な API クライアントとは異なり、Web ブラウザーは、ワイルドカードなど、多大な書式を含む `Accept` ヘッダーを提供することが多々あります。 既定では、ブラウザーから要求が来ていることをフレームワークが検出すると、フレームワークは `Accept` ヘッダーを無視し、代わりにアプリケーションで構成されている既定の書式でコンテンツを返します (他が設定されていない場合は JSON)。 さまざまなブラウザーで API を利用しても、一貫した操作性が与えられます。

アプリケーションでブラウザーの Accept ヘッダーを受け付けるようにする場合、MVC の構成の一部として設定できます。*Startup.cs* の `ConfigureServices` メソッドで `RespectBrowserAcceptHeader` を `true` に設定します。

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a>フォーマッタを構成する

アプリケーションが既定の JSON 以外の追加書式をサポートしなければならない場合、NuGet パッケージを追加し、サポートするように MVC を構成できます。 入力と出力で別々のフォーマッタがあります。 入力フォーマッタは[モデル バインディング](xref:mvc/models/model-binding)で使用されます。出力フォーマッタは応答の書式設定に使用されます。 [カスタム フォーマッタ](xref:web-api/advanced/custom-formatters)を構成することもできます。

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a>System.Text.Json ベースのフォーマッタを構成する

`System.Text.Json` ベースのフォーマッタの機能は、`Microsoft.AspNetCore.Mvc.MvcOptions.SerializerOptions` を使用して構成することができます。

```csharp
services.AddMvc(options =>
{
    options.SerializerOptions.WriterSettings.Indented = true;
});
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Newtonsoft.Json ベースの JSON 形式のサポートを追加する

ASP.NET Core 3.0 より前、MVC は既定で `Newtonsoft.Json` パッケージを使用して実装される JSON フォーマッタを使用していました。 ASP.NET Core 3.0 以降、既定の JSON フォーマッタは `System.Text.Json` に基づいています。 `Newtonsoft.Json` ベースのフォーマッタと機能のサポートは、[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet パッケージをインストールして `Startup.ConfigureServices` で構成することで利用できます。

```csharp
services.AddMvc()
    .AddNewtonsoftJson();
```

一部の機能は `System.Text.Json` ベースのフォーマッタでうまく動作せず、ASP.NET Core 3.0 リリースについて `Newtonsoft.Json` ベースのフォーマッタの参照が必要となる場合があります。 お使いの ASP.NET Core 3.0 以降のアプリが次のような場合は、引き続き `Newtonsoft.Json` ベースのフォーマッタを使用してください。

* `Newtonsoft.Json` 属性 (`[JsonProperty]` や `[JsonIgnore]` など) を使用する、シリアル化設定をカスタマイズする、または `Newtonsoft.Json` で提供される機能に依存している。
* `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` を構成する。 ASP.NET Core 3.0 より前は、`JsonResult.SerializerSettings`が `Newtonsoft.Json` に固有の `JsonSerializerSettings` のインスタンスを受け入れます。
* [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) ドキュメントを生成する。

::: moniker-end

### <a name="add-xml-format-support"></a>XML 形式のサポートを追加する

::: moniker range="<= aspnetcore-2.2"

ASP.NET Core 2.2 またはそれ以前で XML の書式設定のサポートを追加するには、[Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet パッケージをインストールしてください。

::: moniker-end

`System.Xml.Serialization.XmlSerializer` を使用して実装された XML フォーマッタは、`Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> を呼び出すことで構成できます。

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

または、`System.Runtime.Serialization.DataContractSerializer` を使用して実装された XML フォーマッタは、`Startup.ConfigureServices` の <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> を呼び出すことで構成できます。

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

XML 書式設定のサポートを追加すると、コントローラー メソッドは、要求の `Accept` ヘッダーに基づき、適切な書式を返すはずです。下の Fiddler の例をご覧ください。

![Fiddler コンソール:要求の [Raw] タブで、Accept ヘッダー値が application/xml であることを確認できます。 応答の [Raw] タブで、Content-Type ヘッダー値が application/xml であることを確認できます。](formatting/_static/xml-response.png)

[Inspectors] タブで、`Accept: application/xml` ヘッダーが設定された状態で Raw GET 要求が行われたことを確認できます。 応答ウィンドウに `Content-Type: application/xml` ヘッダーが表示されます。`Author` オブジェクトが XML にシリアル化されています。

[Composer] タブを使用して要求を変更し、`Accept` ヘッダーに `application/json` を指定します。 要求を実行します。応答が JSON で書式設定されます。

![Fiddler コンソール:要求の [Raw] タブで、Accept ヘッダー値が application/json であることを確認できます。 応答の [Raw] タブで、Content-Type ヘッダー値が application/json であることを確認できます。](formatting/_static/json-response-fiddler.png)

このスクリーンショットでは、`Accept: application/json` のヘッダーとして要求セットを確認できます。応答によって、その `Content-Type` と同じものが指定されます。 `Author` オブジェクトは応答の本文に JSON 形式で表示されます。

### <a name="forcing-a-particular-format"></a>特定の書式を強制する

特定のアクションの応答書式を制限する場合、`[Produces]` フィルターを適用できます。 `[Produces]` フィルターによって、特定のアクション (またはコントローラー) の応答書式が指定されます。 ほとんどの[フィルター](xref:mvc/controllers/filters)と同様に、アクション、コントローラー、グローバル スコープで適用できます。

```csharp
[Produces("application/json")]
public class AuthorsController
```

アプリケーションに他のフォーマッタが構成されており、クライアントが `Accept` ヘッダーで別の利用できる書式を要求している場合でも、`[Produces]` フィルターは `AuthorsController` 内のすべてのアクションに JSON で書式設定された応答を返すように強制します。 フィルターをグローバルで適用する方法など、詳細については「[フィルター](xref:mvc/controllers/filters)」を参照してください。

### <a name="special-case-formatters"></a>特殊なケースのフォーマッタ

一部の特殊なケースが組み込みのフォーマッタで実装されます。 既定では、戻り値の型 `string` は *text/plain* として書式設定されます (`Accept` ヘッダー経由で要求された場合は *text/html*)。 この動作は `TextOutputFormatter` を削除することで削除できます。 *Startup.cs* の `Configure` メソッドでフォーマッタを削除します (下記参照)。 戻り値の型としてモデル オブジェクトをともなうアクションは、`null` を返すとき、204 No Content の応答を返します。 この動作は `HttpNoContentOutputFormatter` を削除することで削除できます。 次のコードでは、`TextOutputFormatter` と `HttpNoContentOutputFormatter` が削除されます。

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

`TextOutputFormatter` がない場合、`string` は戻り値の型として 406 Not Acceptable などを返します。 XML フォーマッタが存在するときは、`TextOutputFormatter` が削除された場合、戻り値の型 `string` が書式設定されます。

`HttpNoContentOutputFormatter` がない場合、構成されているフォーマッタを利用し、null オブジェクトが書式設定されます。 たとえば、JSON フォーマッタは応答と `null` の本文を返すのに対し、XML フォーマッタは空の XML 要素と属性 `xsi:nil="true"` セットを返します。

## <a name="response-format-url-mappings"></a>応答形式の URL マッピング

クライアントは URL の一部として特定の書式を要求できます。たとえば、クエリ文字列やパスの一部に含めます。あるいは、.xml や .json など、書式固有のファイル拡張子を利用します。 要求パスからのマッピングは、API で使用されるルートに指定する必要があります。 次に例を示します。

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

このルートにより、要求された書式をオプションのファイル拡張子として指定できます。 `[FormatFilter]` 属性は `RouteData` に書式値がないか確認し、応答が作成されると、応答形式を適切なフォーマッタにマッピングします。

|           ルート            |             フォーマッタ              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    既定の出力フォーマッタ    |
| `/products/GetById/5.json` | JSON フォーマッタ (構成される場合) |
| `/products/GetById/5.xml`  | XML フォーマッタ (構成される場合)  |
