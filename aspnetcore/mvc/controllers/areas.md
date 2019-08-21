---
title: ASP.NET Core の区分
author: rick-anderson
description: 区分は ASP.NET MVC の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用する方法を説明します。
ms.author: riande
ms.date: 08/07/2019
uid: mvc/controllers/areas
ms.openlocfilehash: e44c726c47caa3dd0c8c92e3a2502a590bee82d1
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862793"
---
# <a name="areas-in-aspnet-core"></a>ASP.NET Core の区分

作成者: [Dhananjay Kumar](https://twitter.com/debug_mode) および [Rick Anderson](https://twitter.com/RickAndMSFT)

区分は ASP.NET の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用されます。 ルーティングを行うために、区分を使用して、別のルート パラメーター `area` を `controller` と `action` または Razor Page `page` に追加して階層を作成します。

区分は、ASP.NET Core Web アプリをより小さな機能グループにパーティション分割する方法であり、分割した各グループにそれぞれの Razor Pages、コントローラー、ビュー、モデルが与えられます。 区分は、実質的にはアプリ内の構造体となります。 ASP.NET Core Web プロジェクトでは、ページ、モデル、コントローラー、ビューなどの論理コンポーネントが別々のフォルダーに保存されます。 ASP.NET Core ランタイムでは、名前付け規則を使用し、これらのコンポーネント間のリレーションシップを作成します。 大きなアプリでは、アプリを機能の個別の高レベル区分に分割すると便利な場合があります。 チェックアウト、請求、検索などの複数のビジネス ユニットがある eコマース アプリの場合です。 これらのユニットにはそれぞれ、ビュー、コントローラー、Razor Pages、モデルを含める独自の論理区分があります。

次のような場合は、プロジェクトで区分を使用することを検討してください。

* 論理的に大まかに区切れる複数の機能コンポーネントでアプリが構成されている。
* 各機能区分を個別に使用できるようにアプリを分割したい。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。 ダウンロード サンプルからは、区分をテストするための基本的なアプリが与えられます。

Razor Pages を使用している場合は、このドキュメントの「[Razor Pages を使った区分](#areas-with-razor-pages)」をご覧ください。

## <a name="areas-for-controllers-with-views"></a>ビューを伴うコントローラーの区分

区分、コントローラー、ビューを使用する一般的な ASP.NET Core Web アプリに含まれる内容:

* [区分フォルダーの構造](#area-folder-structure)。
* コントローラーと区分を関連付ける目的で [&lbrack;Area&rbrack;](#attribute) 属性で装飾されたコントローラー:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [スタートアップに追加された区分ルート](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>区分フォルダーの構造

あるアプリに *Products* と *Services* という 2 つの論理グループが与えられているとします。 区分を利用すると、フォルダーの構造は次のようになります。

* Project name
  * Areas
    * Products
      * Controllers
        * HomeController.cs
        * ManageController.cs
      * Views
        * Home
          * Index.cshtml
        * Manage
          * Index.cshtml
          * About.cshtml
    * サービス
      * Controllers
        * HomeController.cs
      * Views
        * Home
          * Index.cshtml

区分を使用するとき、前述のレイアウトが一般的ですが、このフォルダー構造を使用するにはビュー ファイルのみが求められます。 ビューの検出では、一致する区分ビュー ファイルを次の順序で検索します。

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

*Controllers* や *Models* など、ビュー以外のフォルダーの場所は問題では**ありません**。 たとえば、*Controllers* や *Models* のフォルダーは不要です。 *Controllers* と *Models* の内容はコードであり、.dll にコンパイルされます。 *Views* の内容は、ビューに対する要求が行われるまでコンパイルされません。

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>コントローラーを区分に関連付ける

区分コントローラーは [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 属性で指名されます。

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>区分ルートを追加する

区分ルートでは通常、属性ルーティングではなく、従来のルーティングが使用されます。 規則ルーティングは順序に依存します。 一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。

URL スペースがすべての区分で統一されている場合、ルート テンプレートでトークンとして `{area:...}` を使用できます。

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

上記のコードでは、ルートは 1 つの区分に一致しなければならないという制約が `exists` によって適用されます。 `{area:...}` の使用は、ルーティングを区分に追加するメカニズムとして最も単純です。

次のコードでは、<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> を使用し、名前の付いた区分ルートが 2 つ作成されます。

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

ASP.NET Core 2.2 で `MapAreaRoute` を使用するときは、[この GitHub 問題](https://github.com/aspnet/AspNetCore/issues/7772)を確認してください。

詳細については、[区分のルーティング](xref:mvc/controllers/routing#areas)に関するページを参照してください。

### <a name="link-generation-with-mvc-areas"></a>MVC 区分を使ったリンクの生成

[サンプル ダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)に含まれる次のコードでは、区分が指定された上でリンクが生成されます。

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。

サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。 部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。 区分が指定されずに生成されたリンクは、同じ区分やコントローラーのページから参照されるときにのみ有効です。

区分またはコントローラーが指定されていないとき、ルーティングは*アンビエント*値に依存します。 現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。 サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。

詳細については、「[コントローラー アクションへのルーティング](xref:mvc/controllers/routing)」を参照してください。

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>_ViewStart.cshtml ファイルを使用した区分の共有レイアウト

アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

標準の場所では、 */Views/_ViewImports.cshtml* は区分に適用されません。 自分の領域で共通の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、`@using`、または `@inject` を使用するには、適切な *_ViewImports.cshtml* ファイルを[区分ビューに確実に適用する](xref:mvc/views/layout#importing-shared-directives)ようにします。 すべてのビューで同じ動作が必要な場合は、 */Views/_ViewImports.cshtml* をアプリケーション ルートに移動します。

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>ビューが保存されている既定の区分フォルダーを変更する

次のコードでは、既定の区分フォルダーが `"Areas"` から `"MyAreas"` に変更されます。

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Razor Pages を使った区分

Razor Pages を使った区分を使うには、アプリのルートに *Areas/&lt;区分名&gt;/Pages* フォルダーが必要です。 [サンプル ダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)では次のフォルダー構造が使われます

* Project name
  * Areas
    * Products
      * Pages
        * _ViewImports
        * バージョン情報
        * インデックス
    * サービス
      * Pages
        * Manage
          * バージョン情報
          * インデックス

### <a name="link-generation-with-razor-pages-and-areas"></a>Razor Pages と区分を使ったリンクの生成

[サンプル ダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)の次のコードでは、区分を指定したリンクの生成を示しています (例: `asp-area="Products"`)。

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。

サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。 部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。 区分が指定されずに生成されたリンクは、同じ区分のページから参照されるときにのみ有効です。

区分が指定されていないとき、ルーティングは "*アンビエント*" 値に依存します。 現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。 サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。 たとえば、次のコードから生成されるリンクを考えてみます。

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

上のコードの場合:

* `<a asp-page="/Manage/About">` から生成されるリンクは、前回の要求が `Services` 区分内のページに向けられていた場合にのみ正しくなります。 たとえば、`/Services/Manage/`、`/Services/Manage/Index`、または `/Services/Manage/About` です。
* `<a asp-page="/About">` から生成されるリンクは、前回の要求が `/Home` 内のページに向けられていた場合にのみ正しくなります。
* コードは、[サンプル ダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)からのものです。

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>_ViewImports ファイルを使って名前空間とタグ ヘルパーをインポートする

*_ViewImports.cshtml* ファイルを各区分の *Pages* フォルダーに追加して、フォルダー内の各 Razor ページに名前空間とタグ ヘルパーをインポートすることができます。

サンプル コードの *Services* 区分について検討します。これには *_ViewImports.cshtml* ファイルが含まれていません。 次のマークアップは */Services/Manage/About* Razor ページを表示します。

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

上のマークアップについて:

* 完全修飾ドメイン名を使ってモデルを指定する必要があります (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。
* [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)は `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` によって有効になります

サンプル ダウンロードでは、Products 区分に次の *_ViewImports.cshtml* ファイルが含まれています。

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

次のマークアップは */Products/About* Razor ページを表示します。

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

上のファイルでは、*Areas/Products/Pages/_ViewImports.cshtml* ファイルによって、名前空間と `@addTagHelper` ディレクティブがファイルにインポートされています。

詳細については、「[タグ ヘルパーのスコープの管理](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)」と「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」をご覧ください。

### <a name="shared-layout-for-razor-pages-areas"></a>Razor Pages 区分の共有レイアウト

アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。

### <a name="publishing-areas"></a>区分の発行

*.csproj ファイルに `<Project Sdk="Microsoft.NET.Sdk.Web">` が含まれているときは、すべての *.cshtml ファイル、および *wwwroot* ディレクトリ内のファイルが出力に発行されます。
