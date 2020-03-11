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
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>ASP.NET Web API から ASP.NET Core への移行

[Scott Addie](https://twitter.com/scott_addie)と[上田 Smith](https://ardalis.com/)

ASP.NET 4.x Web API は、ブラウザーやモバイルデバイスを含む広範なクライアントに到達する HTTP サービスです。 ASP.NET Core は、ASP.NET 4.x の MVC および Web API アプリモデルを、ASP.NET Core MVC と呼ばれるより単純なプログラミングモデルに統合します。 この記事では、ASP.NET 4.x Web API から ASP.NET Core MVC に移行するために必要な手順について説明します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4.x Web API プロジェクトを確認する

開始点として、この記事では、 [ASP.NET Web API 2 ではじめに](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)で作成された製品*アプリ*プロジェクトを使用します。 そのプロジェクトでは、単純な ASP.NET 4.x Web API プロジェクトは次のように構成されます。

*Global.asax.cs*では、`WebApiConfig.Register`に対して呼び出しが行われます。

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig` クラスは*App_Start*フォルダーにあり、静的な `Register` メソッドがあります。

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

このクラスは、実際にはプロジェクトで使用されていませんが、[属性ルーティング](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)を構成します。 また、ASP.NET Web API によって使用されるルーティングテーブルも構成します。 この場合、ASP.NET 4.x Web API は `/api/{controller}/{id}`形式に一致する Url を必要とし、`{id}` は省略可能であると想定しています。

製品*アプリ*プロジェクトには、1つのコントローラーが含まれています。 コントローラーは `ApiController` から継承し、次の2つのアクションを含みます。

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

`ProductsController` によって使用される `Product` モデルは、単純なクラスです。

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

以下のセクションでは、ASP.NET Core MVC に Web API プロジェクトを移行する方法について説明します。

## <a name="create-destination-project"></a>変換先プロジェクトの作成

Visual Studio で次の手順を実行します。

* **Visual Studio ソリューション** > **その他のプロジェクトの種類** > 、[**新しい** > **プロジェクト**] >  **[ファイル]** にアクセスします。 **[空のソリューション]** を選択し、ソリューションに*WebAPIMigration*という名前を指定します。 [OK] ボタンをクリックします。
* 既存の製品*アプリ*プロジェクトをソリューションに追加します。
* 新しい**ASP.NET Core Web アプリケーション**プロジェクトをソリューションに追加します。 ドロップダウンから **.Net Core**ターゲットフレームワークを選択し、[ **API**プロジェクト] テンプレートを選択します。 プロジェクトに*Productscore*という名前を付け、 **[OK]** ボタンをクリックします。

ソリューションに2つのプロジェクトが含まれるようになりました。 次のセクションでは、 *Productscore*プロジェクトの内容を*productscore*プロジェクトに移行する方法について説明します。

## <a name="migrate-configuration"></a>構成の移行

ASP.NET Core では、 *App_Start*フォルダーも*global.asax*ファイルも使用されません *。また、web.config ファイルは*発行時に追加されます。 *Startup.cs*は*global.asax*の後継であり、プロジェクトのルートにあります。 `Startup` クラスは、すべてのアプリのスタートアップタスクを処理します。 詳細については、<xref:fundamentals/startup> を参照してください。

ASP.NET Core MVC では、`Startup.Configure`で <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> を呼び出すと、既定で属性ルーティングが含まれます。 次の `UseMvc` の呼び出しによって、製品*アプリ*プロジェクトの*App_Start*というファイルが置き換えられます。

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>モデルとコントローラーの移行

*Productapp*プロジェクトのコントローラーとそれが使用するモデルをコピーします。 次の手順に従います。

1. 元のプロジェクトから新しいコントローラー */製品コントローラー .cs*をコピーします。
1. 元のプロジェクトの [*モデル*] フォルダー全体を新しいプロジェクトにコピーします。
1. コピーしたファイルの名前空間を新しいプロジェクト名 (*Productscore*) に一致するように変更します。 *ProductsController.cs*の `using ProductsApp.Models;` ステートメントも調整します。

この時点で、アプリをビルドすると、多数のコンパイルエラーが発生します。 このエラーが発生するのは、次のコンポーネントが ASP.NET Core に存在しないためです。

* `ApiController` クラス
* `System.Web.Http` 名前空間
* `IHttpActionResult` インターフェイス

次のようにエラーを修正します。

1. `ApiController` を <xref:Microsoft.AspNetCore.Mvc.ControllerBase> に変更します。 `ControllerBase` 参照を解決する `using Microsoft.AspNetCore.Mvc;` を追加します。
1. `using System.Web.Http;`を削除します。
1. `GetProduct` アクションの戻り値の型を `IHttpActionResult` から `ActionResult<Product>`に変更します。

`GetProduct` アクションの `return` ステートメントを次のように簡略化します。

```csharp
return product;
```

## <a name="configure-routing"></a>ルーティングを構成する

次のようにルーティングを構成します。

1. `ProductsController` クラスを次の属性でマークします。

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    上記の[`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)属性は、コントローラーの属性ルーティングパターンを構成します。 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)属性を使用すると、このコントローラーのすべてのアクションに対して、属性のルーティングが必要になります。

    属性ルーティングでは、`[controller]` や `[action]`などのトークンがサポートされます。 実行時には、各トークンは、属性が適用されたコントローラーまたはアクションの名前にそれぞれ置き換えられます。 トークンにより、プロジェクト内のマジック文字列の数が減少します。 また、トークンは、自動名前変更リファクタリングが適用された場合に、対応するコントローラーとアクションとの同期を維持することも保証します。
1. プロジェクトの互換モードを ASP.NET Core 2.2 に設定します。

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    上記の変更:

    * コントローラーレベルで `[ApiController]` 属性を使用するには、が必要です。
    * ASP.NET Core 2.2 で導入された動作が中断する可能性があります。
1. `ProductController` アクションに対する HTTP Get 要求を有効にします。
    * [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)属性を `GetAllProducts` アクションに適用します。
    * `[HttpGet("{id}")]` 属性を `GetProduct` アクションに適用します。

上記の変更が加えられ、未使用の `using` ステートメントが削除された後、 *ProductsController.cs*ファイルは次のようになります。

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

移行したプロジェクトを実行し、`/api/products`を参照します。 3つの製品の完全な一覧が表示されます。 [https://www.microsoft.com](`/api/products/1`) を参照します。 最初の製品が表示されます。

## <a name="compatibility-shim"></a>互換性 shim

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)ライブラリは、ASP.NET 4.X Web API プロジェクトを ASP.NET Core に移動する互換性 shim を提供します。 互換性 shim は、ASP.NET 4.x Web API 2 のさまざまな規則をサポートするために ASP.NET Core を拡張します。 このドキュメントで以前に移植したサンプルは、互換性 shim が不要であるという基本的なものです。 大規模なプロジェクトでは、互換性 shim を使用すると、ASP.NET Core と ASP.NET 4.x Web API 2 間の API ギャップを一時的にブリッジするのに役立ちます。

Web API 互換性 shim は、大規模な ASP.NET 4.x Web API プロジェクトの ASP.NET Core への移行をサポートするための一時的な手段として使用することを意図しています。 時間の経過と共に、互換性 shim に依存するのではなく、ASP.NET Core パターンを使用するようにプロジェクトを更新する必要があります。

`Microsoft.AspNetCore.Mvc.WebApiCompatShim` に含まれる互換性機能は次のとおりです。

* コントローラーの基本型を更新する必要がないように `ApiController` 型を追加します。
* Web API スタイルモデルのバインドを有効にします。 MVC モデルバインドは、既定では ASP.NET 4.x 5 と同様に機能します。 ASP.NET Core 互換性 shim は、モデルバインディングを ASP.NET 4.x Web API 2 モデルバインディング規則に似たものに変更します。 たとえば、複合型は要求本文から自動的にバインドされます。
* コントローラーアクションが `HttpRequestMessage`型のパラメーターを受け取ることができるように、モデルバインディングを拡張します。
* アクションが型 `HttpResponseMessage`の結果を返すことを可能にするメッセージフォーマッタを追加します。
* Web API 2 のアクションが応答の処理に使用する可能性がある応答メソッドを追加します。
  * `HttpResponseMessage` ジェネレーター:
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * アクションの結果メソッド:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* `IContentNegotiator` のインスタンスをアプリの依存関係挿入 (DI) コンテナーに追加し、 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)のコンテンツネゴシエーションに関連する型を使用できるようにします。 このような型の例としては、`DefaultContentNegotiator` や `MediaTypeFormatter`などがあります。

互換性 shim を使用するには、次のようにします。

1. [AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet パッケージをインストールします。
1. `Startup.ConfigureServices`で `services.AddMvc().AddWebApiConventions()` を呼び出して、互換性 shim のサービスをアプリの DI コンテナーに登録します。
1. アプリの `IApplicationBuilder.UseMvc` 呼び出しの `IRouteBuilder` で `MapWebApiRoute` を使用して、web API 固有のルートを定義します。

## <a name="additional-resources"></a>その他のリソース

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
