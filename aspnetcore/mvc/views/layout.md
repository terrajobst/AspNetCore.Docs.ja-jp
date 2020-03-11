---
title: ASP.NET Core でのレイアウト
author: ardalis
description: 共通レイアウトの使用方法、ディレクティブの共有方法、および ASP.NET Core アプリでビューをレンダリングする前に共通コードを実行する方法について説明します。
ms.author: riande
ms.date: 07/30/2019
uid: mvc/views/layout
ms.openlocfilehash: db8c6c30397593c1a8375ebc800c1c0e34d241cb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655046"
---
# <a name="layout-in-aspnet-core"></a>ASP.NET Core でのレイアウト

作成者: [Steve Smith](https://ardalis.com/)、[Dave Brock](https://twitter.com/daveabrock)

ページやビューは、多くの場合、ビジュアルおよびプログラムの要素を共有します。 この記事では、次の方法について説明します。

* 共通のレイアウトを使用する。
* ディレクティブを共有する。
* ページまたはビューを表示する前に、共通のコードを実行する。

このドキュメントでは、ASP.NET Core MVC に対するアプローチとして、Razor Pages とビューを含むコントローラーの 2 種類のレイアウトについて説明します。 このトピックでは、違いは最小限です。

* Razor Pages は、*Pages* フォルダーにあります。
* ビューを含むコントローラーでは、*Views* フォルダーをビューに使用します。

## <a name="what-is-a-layout"></a>レイアウトとは

ほとんどの Web アプリには、ユーザーがページ間を移動する際に一貫性のあるエクスペリエンスを提供する共通レイアウトがあります。 通常、このレイアウトには、アプリのヘッダー、ナビゲーションまたはメニュー要素、フッターなどの共通のユーザー インターフェイス要素が含まれます。

![ページ レイアウトの例](layout/_static/page-layout.png)

スクリプトやスタイルシートなどの共通の HTML 構造体も、アプリ内の多くのページでよく使用されます。 これらの共有要素をすべて *layout* ファイルで定義することで、アプリ内で使用する任意のビューで参照できるようになります。 レイアウトにより、ビュー内の重複コードが減ります。

規則により、ASP.NET Core アプリの既定のレイアウトには *_Layout.cshtml* という名前が付けられます。 テンプレートを使用すると、次のような新しい ASP.NET Core プロジェクトのレイアウト ファイルが作成されます。

* Razor Pages: *Pages/Shared/_Layout.cshtml*

  ![ソリューション エクスプローラーの Pages フォルダー](layout/_static/rp-web-project-views.png)

* ビューを含むコントローラー: *Views/Shared/_Layout.cshtml*

  ![ソリューション エクスプローラーの Views フォルダー](layout/_static/mvc-web-project-views.png)

レイアウトでは、アプリのビューの最上位のテンプレートが定義されています。 アプリでは、レイアウトは必要ありません。 アプリでは、それぞれ異なるレイアウトを指定するさまざまなビューを使用して、複数のレイアウトを定義できます。

次のコードでは、コントローラーとビューを含むテンプレートで作成されたプロジェクトのレイアウト ファイルを示します。

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>レイアウトの指定

Razor ビューには `Layout` プロパティがあります。 個々のビューは、このプロパティを設定することでレイアウトを指定します。

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

指定されるレイアウトでは、完全なパス (例: */Pages/Shared/_Layout.cshtml*、 */Views/Shared/_Layout.cshtml*) または部分パス (例: `_Layout`) を使用できます。 部分的な名前を指定すると、Razor ビュー エンジンが標準の検出プロセスを使用して、レイアウト ファイルを検索します。 ハンドラー メソッド (またはコントローラー) が存在するフォルダーが最初に検索され、その後で *Shared* フォルダーが検索されます。 この検出プロセスは、[部分ビュー](xref:mvc/views/partial#partial-view-discovery)の検出に使用されるのと同じプロセスです。

既定では、すべてのレイアウトで `RenderBody` を呼び出す必要があります。 `RenderBody` への呼び出しが配置されると、ビューのコンテンツがレンダリングされます。

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a>セクション

レイアウトは、必要に応じて  *を呼び出すことで、1 つ以上の*セクション`RenderSection`を参照することができます。 セクションは、特定のページ要素の配置場所を整理する方法を提供します。 `RenderSection` の呼び出しごとに、そのセクションを必須またはオプションにするかどうかを指定できます。

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

必須のセクションが見つからない場合、例外がスローされます。 個々のビューは、`@section` Razor 構文を使用して、セクション内にレンダリングされるコンテンツを指定します。 ページまたはビューでセクションを定義する場合は、レンダリングされる必要があります (そうしないと、エラーが発生します)。

Razor Pages ビューでの `@section` 定義の例:

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

上記のコードでは、*scripts/main.js* がページまたはビューの `scripts` セクションに追加されています。 同じアプリの他のページまたはビューではこのスクリプトは必要なく、スクリプト セクションは定義されていません。

次のマークアップでは、[部分タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)を使用して *_ValidationScriptsPartial.cshtml* を表示しています。

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

上記のマークアップは、[スキャフォールディング ID](xref:security/authentication/scaffold-identity) によって生成されました。

ページまたはビューで定義されたセクションは、そのイミディエイト レイアウト ページでのみ使用できます。 これらは、部分、ビュー コンポーネント、またはビュー システムの他の部分からは参照できません。

### <a name="ignoring-sections"></a>セクションの無視

既定では、コンテンツ ページの本文とすべてのセクションがレイアウト ページですべてレンダリングされる必要があります。 Razor ビュー エンジンは、本文と各セクションがレンダリングされているかどうかを追跡することによってこれを実行します。

本文またはセクションを無視するようにビュー エンジンに指示するには、`IgnoreBody` メソッドと `IgnoreSection` メソッドを呼び出します。

Razor ページ内の本文とすべてのセクションは、レンダリングされるか無視される必要があります。

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>共有ディレクティブのインポート

ビューおよびページでは、Razor ディレクティブを使用して名前空間をインポートし、[依存関係の挿入](dependency-injection.md)を使用できます。 多くのビューで共有されるディレクティブは、共通の *_ViewImports.cshtml* ファイルで指定できます。 `_ViewImports` ファイルは、次のディレクティブをサポートします。

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

このファイルは、関数やセクションの定義などの Razor 機能をサポートしていません。

`_ViewImports.cshtml` ファイルのサンプル:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

ASP.NET Core MVC アプリの *_ViewImports.cshtml* ファイルは、通常、*Pages* (または *Views*) フォルダーに配置されます。 *_ViewImports.cshtml* ファイルは、任意のフォルダー内に配置できますが、その場合は、そのフォルダーとそのサブフォルダー内にあるパージまたはビューにのみ適用されます。 `_ViewImports` ファイルの処理はルート レベルで開始されてから、フォルダーごとに、ページまたはビュー自体の場所に至るまで行われます。 ルート レベルで指定された `_ViewImports` の設定は、フォルダー レベルでオーバーライドされる可能性があります。

たとえば、次のように想定します。

* ルート レベルの *_ViewImports.cshtml* ファイルには、`@model MyModel1` と `@addTagHelper *, MyTagHelper1` が含まれます。
* サブフォルダーの *_ViewImports.cshtml* ファイルには、`@model MyModel2` と `@addTagHelper *, MyTagHelper2` が含まれます。

サブフォルダー内のページおよびビューは、タグ ヘルパーと `MyModel2` モデルの両方にアクセスできます。

複数の *_ViewImports.cshtml* ファイルがファイル階層内にある場合、ディレクティブの結合された動作は次のようになります。

* `@addTagHelper`、`@removeTagHelper`: 順番どおりにすべて実行
* `@tagHelperPrefix`: ビューに最も近いものが、他のものをすべてをオーバーライドする
* `@model`: ビューに最も近いものが、他のものをすべてをオーバーライドする
* `@inherits`: ビューに最も近いものが、他のものをすべてをオーバーライドする
* `@using`: すべてが含まれ、重複は無視される
* `@inject`: プロパティごとに、ビューに最も近いものが、同じプロパティ名を持つ他のものすべてをオーバーライドする

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>各ビューの前にコードを実行する

各ビューまたはページの前に実行する必要があるコードは、 *_ViewStart.cshtml* ファイルに配置する必要があります。 慣例により、 *_ViewStart.cshtml* ファイルは *Pages* (または *Views*) フォルダーに配置されます。 *_ViewStart.cshtml* に列記されているステートメントは、すべての (レイアウトでもなく、部分ビューでもない) 完全なビューより前に実行されます。 [ViewImports.cshtml](xref:mvc/views/layout#viewimports) と同様に、 *_ViewStart.cshtml* は階層構造です。 *_ViewStart.cshtml* ファイルがビューまたはページ フォルダーで定義されている場合、*Pages* (または *Views*) フォルダーのルートで定義されているファイル (ある場合) の後に実行されます。

*_ViewStart.cshtml* ファイルのサンプル:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

上記のファイルは、すべてのビューで *_Layout.cshtml* レイアウトを使用することを指定します。

通常、 *_ViewStart.cshtml* および *_ViewImports.cshtml* は、 **/Pages/Shared** (または */Views/Shared*) フォルダーには配置*されません*。 これらのファイルのアプリ レベルのバージョンは、 */Pages* (または */Views*) フォルダーに直接配置する必要があります。
