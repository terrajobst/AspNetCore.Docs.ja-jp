---
title: ASP.NET Core MVC のビュー
author: ardalis
description: ビューがアプリのデータ表示と、ASP.NET Core MVC でのユーザー操作を処理する方法について説明します。
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: f636908ee36d0af6e92875876240cb8712dd2ccc
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881030"
---
# <a name="views-in-aspnet-core-mvc"></a>ASP.NET Core MVC のビュー

作成者: [Steve Smith](https://ardalis.com/)、[Luke Latham](https://github.com/guardrex)

このドキュメントでは、ASP.NET Core MVC アプリケーションで使用されるビューについて説明します。 Razor ページについては、「[ASP.NET Core での Razor ページの概要](xref:razor-pages/index)」を参照してください。

Model-View-Controller (MVC) パターンでは、*ビュー*がアプリのデータ表示とユーザー操作を処理します。 ビューは、[Razor マークアップ](xref:mvc/views/razor)が埋め込まれた HTML テンプレートてです。 Razor マークアップは、クライアントに送信する Web ページを生成するために HTML マークアップと対話するコードです。

ASP.NET Core MVC では、ビューは、Razor マークアップで [C# プログラミング言語](/dotnet/csharp/)を使用する *.cshtml* ファイルです。 通常、ビュー ファイルは、各アプリの[コントローラー](xref:mvc/controllers/actions)の名前が付いたフォルダーにグループ化されます。 これらのフォルダーは、アプリのルートにある *Views* フォルダーに格納されます。

![Visual Studio のソリューション エクスプローラーで Views フォルダー、Home フォルダーが開かれ、About.cshtml、Contact.cshtml、および Index.cshtml ファイルが示されています。](overview/_static/views_solution_explorer.png)

*ホーム* コントローラーは、*Views* フォルダー内の *Home* フォルダーによって表されます。 *Home* フォルダーには、*About*、*Contact*、*Index* (ホームページ) の Web ページのビューが含まれています。 ユーザーがこれら 3 つの Web ページのうちの 1 つを要求すると、*ホーム* コントローラー内のコントローラー アクションが 3 つのビューからビルドに使用するものを決定して、ユーザーに Web ページを返します。

[レイアウト](xref:mvc/views/layout)を使用して、一貫性のある Web ページ セクションを提供し、コードの繰り返しを削減します。 多くの場合、レイアウトには、ヘッダー、ナビゲーションとメニュー要素、フッターが含まれています。 ヘッダーとフッターには通常、多くのメタデータ要素とスクリプトおよびスタイル アセットへのリンクの定型マークアップが含まれます。 レイアウトは、ビューでこの定型マークアップを回避するのに役立ちます。

[部分ビュー](xref:mvc/views/partial)は、再利用可能なビューの部分を管理することで、コードの重複を削減します。 たとえば、ブログ Web サイトで複数のビューに表示される作成者の略歴に、部分ビューが役立ちます。 作成者の略歴は、通常のビュー コンテンツで、Web ページのコンテンツを生成するためにコードを実行する必要はありません。 作成者の略歴コンテンツは、モデル バインディングだけでビューで使用することができるため、この種類のコンテンツには部分ビューを使用するのが最適です。

[ビュー コンポーネント](xref:mvc/views/view-components)は、コードの繰り返しを削減できる点は部分ビューと似ていますが、Web ページをレンダリングするために、コードをサーバーで実行する必要があるビュー コンテンツに適しています。 ビュー コンポーネントは、レンダリングされたコンテンツで、Web サイトのショッピング カートのように、データベースとの対話を必要とする場合に便利です。 ビュー コンポーネントは、Web ページの出力を生成するためのモデル バインディングに限定されるものではありません。

## <a name="benefits-of-using-views"></a>ビューを使用するメリット

ビューは、ユーザー インターフェイス マークアップをアプリの他の部分から分離して、MVC アプリ内で[懸念事項の分離](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)を確立するのに役立ちます。 SoC 設計に従うことで、アプリをモジュール化することができます。これにより次のような複数のメリットがもたらされます。

* より効率的に整理されるため、アプリの維持が容易になります。 ビューは通常、アプリの機能によってグループ化されます。 これにより、機能を使用する際に、関連するビューが見つけやすくなります。
* アプリの部分は弱く結合されています。 ビジネス ロジックとデータ アクセス コンポーネントと切り離して、アプリのビューをビルドおよび更新できます。 アプリの他の部分を更新しなくても、アプリのビューを変更できます。
* ビューは個別の単位であるため、アプリのユーザー インターフェイス部分をより簡単にテストできます。
* より効率的に整理されるため、ユーザー インターフェイスのセクションを誤って繰り返す可能性が低くなります。

## <a name="creating-a-view"></a>ビューの作成

コントローラーに固有のビューは、*Views/[ControllerName]* フォルダー内に作成されます。 コントローラー間で共有されるビューは、*Views/Shared* フォルダーに配置されます。 ビューを作成するには、新しいファイルを追加し、このファイルに、関連付けられたコントローラー アクションと同じ名前にファイル拡張子 *.cshtml* を付けた名前を付けます。 *ホーム* コントローラーで、*About* アクションに対応するビューを作成するには、*Views/Home* フォルダー内に *About.cshtml* ファイルを作成します。

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* マークアップは、`@` 記号で始まります。 中かっこ (`{ ... }`) で始まる [Razor コード ブロック](xref:mvc/views/razor#razor-code-blocks)内に C# コードを配置して、C# ステートメントを実行します。 例として、上に示されている `ViewData["Title"]` への "About" の割り当てを参照してください。 `@` 記号を使用して値を参照するだけで、HTML 内に値を表示することができます。 上記の `<h2>` 要素と `<h3>` 要素のコンテンツを参照してください。

上記のビュー コンテンツは、ユーザーにレンダリングされる Web ページ全体の一部のみを示しています。 残りのページのレイアウトとビューのその他の共通する側面は、他のビュー ファイルで指定されます。 詳細については、「[Layout](xref:mvc/views/layout)」 (レイアウト) のトピックを参照してください。

## <a name="how-controllers-specify-views"></a>コントローラーがビューを指定する方法

ビューは通常、[ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) の型である [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult) としてアクションから返されます。 アクション メソッドで `ViewResult` を作成して直接返すことはできますが、一般的には行われていません。 ほとんどのコントローラーは [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) から継承されるため、`View` ヘルパー メソッドを使って `ViewResult` を返します。

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

このアクションが返されると、最後のセクションに表示されている *About.cshtml* ビューが、次の Web ページとしてレンダリングされます。

![Microsoft Edge ブラウザーでレンダリングされた About ページ](overview/_static/about-page.png)

`View` ヘルパー メソッドには複数のオーバーロードがあります。 必要に応じて以下を指定できます。

* 返す明示的なビュー:

  ```csharp
  return View("Orders");
  ```

* ビューに渡す[モデル](xref:mvc/models/model-binding):

  ```csharp
  return View(Orders);
  ```

* ビューとモデルの両方:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>ビューの検出

アクションがビューを返すときに、*ビューの検出*と呼ばれるプロセスが行われます。 このプロセスでは、ビュー名に基づいて、使用するビュー ファイルを決定します。 

`View` メソッド (`return View();`) の既定の動作は、呼び出し元のアクション メソッドと同じ名前を持つビューを返すことです。 たとえば、コントローラーの *About* `ActionResult` メソッド名は、*About.cshtml* という名前のビュー ファイルの検索に使用されます。 ランタイムは最初に、ビューの *Views/[ControllerName]* フォルダーを調べます。 そこで一致するビューが見つからない場合は、ビューの *Shared* フォルダーを検索します。

`return View();` を使用して `ViewResult` を暗黙的に返すか、`return View("<ViewName>");` を使用してビュー名を `View` メソッドに明示的に渡すかは関係ありません。 どちらの場合も、ビューの検出は、次の順序で一致するビュー ファイルを検索します。

   1. *Views/\[ControllerName]/\[ViewName].cshtml*
   1. *Views/Shared/\[ViewName].cshtml*

ビュー名の代わりに、ビュー ファイル パスを指定できます。 アプリのルートから始まる (必要に応じて"/" または "~/" で始まる) 絶対パスを使用する場合は、 *.cshtml* 拡張子を指定する必要があります。

```csharp
return View("Views/Home/About.cshtml");
```

*.cshtml* 拡張子なしで、相対パスを使用して異なるディレクトリ内にあるビューを指定することもできます。 `HomeController` の内部で、相対パスを使用して、*Manage* ビューの *Index* ビューを返すことができます。

```csharp
return View("../Manage/Index");
```

同様に、プレフィックス "./" を使用して、現在のコントローラーに固有のディレクトリを指定することができます。

```csharp
return View("./About");
```

[部分ビュー](xref:mvc/views/partial)と[ビュー コンポーネント](xref:mvc/views/view-components)は、まったく同じではありませんが、同様の検出メカニズムを使用します。

カスタムの [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) を使用して、ビューをアプリ内に配置する方法の既定の規則をカスタマイズすることができます。

ビューの検出は、ファイル名によるビュー ファイルの検出に依存しています。 基になるファイル システムが大文字と小文字を区別する場合は、ビューの名前も大文字と小文字を区別する可能性があります。 オペレーティング システム間の互換性のため、コントローラー、アクション名、関連するビュー フォルダー、ファイル名の間で、大文字と小文字の区別を一致させます。 大文字と小文字を区別するファイル システムを使用していて、ビュー ファイルが見つからないというエラーが発生する場合は、要求されたビュー ファイルと実際のビュー ファイルの名前の大文字と小文字が一致していることを確認します。

保守性を高め、わかりやすくするため、ビューのファイル構造を整理するためのベスト プラクティスに従って、コントローラー、アクション、およびビューのリレーションシップを反映します。

## <a name="passing-data-to-views"></a>ビューにデータを渡す

ビューにデータを渡すには、いくつかの方法があります。

* 厳密に型指定されたデータ: viewmodel
* 弱く型指定されたデータ
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>厳密に型指定されたデータ (viewmodel)

最も確実な方法は、ビューで[モデル](xref:mvc/models/model-binding)の型を指定することです。 このモデルは、一般的に *viewmodel* と呼ばれます。 viewmodel 型のインスタンスをアクションからビューに渡します。

viewmodel を使用してデータをビューに渡すことで、ビューで*厳密な*型チェックを利用できるようになります。 *厳密な型指定* (または*厳密に型指定された*) は、すべての変数および定数に明示的に定義された型 (`string`、`int`、`DateTime` など) があることを意味します。 ビューで使用される型の妥当性は、コンパイル時にチェックされます。

[Visual Studio](https://visualstudio.microsoft.com) と [Visual Studio Code](https://code.visualstudio.com/) では、[IntelliSense](/visualstudio/ide/using-intellisense) と呼ばれる機能を使用して、厳密に型指定されたクラス メンバーを一覧表示します。 viewmodel のプロパティを表示する場合は、viewmodel の変数名に続けてピリオド (`.`) を入力します。 これにより、エラーの少ないコードをより早く記述できます。

`@model` ディレクティブを使用してモデルを指定します。 `@Model` を使用してモデルを使用します。

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

モデルをビューに提供するため、コントローラーはモデルをパラメーターとして渡します。

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

ビューに提供できるモデルの型に制限はありません。 Plain Old CLR Object (POCO) viewmodel を、動作 (メソッド) をほとんど定義せずに使用することをお勧めします。 通常、viewmodel クラスは、*Models* フォルダーまたはアプリのルートにある個別の *ViewModels* フォルダーのいずれかに格納されます。 上記の例で使用されている *Address* viewmodel は、*Address.cs* という名前のファイルに格納されている POCO viewmodel です。

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

viewmodel 型とビジネス モデル型の両方に同じクラスを使用することを妨げるものはありません。 しかし、別のモデルを使用することで、ビジネス ロジックとアプリのデータ アクセス部分からは独立して、ビューを変えることができます。 モデルと viewmodel を分離することで、モデルが[モデル バインディング](xref:mvc/models/model-binding)と[検証](xref:mvc/models/validation)を使用する際に、ユーザーによってアプリに送信されるデータに対してセキュリティ上の利点ももたらされます。

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>弱く型指定されたデータ (ViewData、ViewData 属性、ViewBag)

`ViewBag`  *は Razor ページでは使用できません。*

厳密に型指定されたビューに加え、ビューはデータの*弱く型指定された* (*緩く型指定された*ともいう) コレクションにもアクセスできます。 厳密な型とは異なり、*弱い型* (または*緩い型*) は、使用するデータの型を明示的に宣言しないことを意味します。 弱く型指定されたデータのコレクションを使用して、少量のデータをコントローラーとビュー間でやり取りすることができます。

| データをやり取りする相手                        | 例                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| コントローラーとビュー                             | ドロップダウン リストにデータを読み込む。                                          |
| ビューと[レイアウト ビュー](xref:mvc/views/layout)   | ビュー ファイルからレイアウト ビューの **\<title>** 要素の内容を設定する。  |
| [部分ビュー](xref:mvc/views/partial)とビュー | ユーザーが要求した Web ページに基づいてデータを表示するウィジェット。      |

このコレクションは、コントローラーおよびビューで `ViewData` または `ViewBag` のいずれかのプロパティを通じて参照できます。 `ViewData` プロパティは、弱く型指定されたオブジェクトのディクショナリです。 `ViewBag` プロパティは、基になる `ViewData` コレクションに動的プロパティを提供する `ViewData` をラップするラッパーです。 メモ:キー参照は、`ViewData` と `ViewBag` のどちらについても、大文字と小文字の区別はありません。

`ViewData` および `ViewBag` は実行時に動的に解決されます。 これらはコンパイル時の型チェックを提供していないため、どちらも viewmodel を使用する場合よりも一般的にエラーが発生しやすくなります。 そのため、開発者の中には、`ViewData` および `ViewBag` の使用を最小限に抑えるか、まったく使用しない人もいます。

<a name="VD"></a>

**ViewData**

`ViewData` は `string` キーを介してアクセスされる [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) オブジェクトです。 文字列データは、格納してキャストなしで直接使用できますが、特定の型を抽出するときには、他の `ViewData` オブジェクトの値をこれらの型にキャストする必要があります。 `ViewData` を使用して、データをコントローラーからビューとビュー内部 ([部分ビュー](xref:mvc/views/partial)および[レイアウト](xref:mvc/views/layout)を含む) に渡すことができます。

次の例では、1 つのアクションで `ViewData` を使用して、あいさつ文とアドレスに値を設定します。

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

ビューでデータを使用します。

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**ViewData 属性**

[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) を使用するもう 1 つの方法は [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) です。 コントローラーや Razor ページのモデルのプロパティが `[ViewData]` 属性でマークされている場合、その値をディクショナリに格納してそこから読み込むことができます。

次の例では、Home コントローラーには `[ViewData]` でマークされた `Title` プロパティが含まれています。 `About` メソッドは、About ビューのタイトルを設定します。

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

About ビューでは、モデル プロパティとして `Title` プロパティにアクセスします。

```cshtml
<h1>@Model.Title</h1>
```

レイアウトでは、タイトルは ViewData ディクショナリから読み込まれます。

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**ViewBag**

`ViewBag`  *は Razor ページでは使用できません。*

`ViewBag` は、`ViewData` に格納されているオブジェクトへの動的アクセスを提供する [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) オブジェクトです。 `ViewBag` はキャストを必要としないため、より簡単に使用できます。 次の例は、上記の `ViewData` を使用した時と同じ結果になるように、`ViewBag` を使用する方法を示しています。

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**ViewBag、ViewData を同時に使用する**

`ViewBag`  *は Razor ページでは使用できません。*

`ViewData` と `ViewBag` は基になる同じ `ViewData` コレクションを参照しているため、値を読み書きするときに、`ViewData` と `ViewBag` の両方を使用して、それらを組み合わせることができます。

*About.cshtml* ビューの一番上で、`ViewBag` を使用してタイトルを設定し、`ViewData` を使用して説明を設定します。

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

プロパティを読み取りますが、`ViewData` と `ViewBag` の使用を反転します。 *_Layout.cshtml*ファイルで、`ViewData` を使用してタイトルを取得し、`ViewBag` を使用して説明を取得します。

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

文字列には `ViewData` のキャストを必要としないことに注意してください。 キャストせずに `@ViewData["Title"]` を使用できます。

`ViewData` と `ViewBag` の両方を同時に使用することは、プロパティの読み取りと書き込みを組み合わせることと同様に可能です。 次のマークアップがレンダリングされます。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**ViewData と ViewBag の相違点の概要**

 `ViewBag` は Razor ページでは使用できません。

* `ViewData`
  * [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) から派生するため、`ContainsKey`、`Add`、`Remove`、`Clear` などの役に立つディクショナリ プロパティがあります。
  * ディクショナリ内のキーは文字列なので、空白が許可されます。 例 : `ViewData["Some Key With Whitespace"]`
  * `ViewData` を使用するには、ビューで `string` 以外のすべての型をキャストする必要があります。
* `ViewBag`
  * [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) から派生するため、ドット表記 (`@ViewBag.SomeKey = <value or object>`) を使用して動的プロパティを作成できます。キャストは必要ありません。 `ViewBag` の構文は、コントローラーとビューへの追加を高速化します。
  * null 値のチェックを簡素化します。 例 : `@ViewBag.Person?.Name`

**ViewData または ViewBag を使用する場合**

コントローラーとビュー間で少量のデータを渡すには、`ViewData` と `ViewBag` はどちらも等しく有効な方法です。 どちらを使用するかは、任意で選択できます。 `ViewData` オブジェクトと `ViewBag` オブジェクトを組み合わせることはできますが、1 つの方法を一貫して使用した方が、コードが読みやすくなり、維持も容易になります。 どちらの方法も実行時に動的に解決されるため、実行時エラーが発生しやすくなります。 開発チームの中には、これらを避けているところもあります。

### <a name="dynamic-views"></a>動的ビュー

`@model` を使用してモデル型は宣言しないが、渡されるモデル インスタンス (`return View(Address);` など) があるビューは、インスタンスのプロパティを動的に参照できます。

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

この機能は柔軟性を提供しますが、コンパイルの保護や IntelliSense は提供していません。 プロパティが存在しない場合は、実行時に Web ページの生成が失敗します。

## <a name="more-view-features"></a>ビューのその他の機能

[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)は、既存の HTML タグへのサーバー側の動作の追加を容易にします。 タグ ヘルパーを使用すると、ビュー内でカスタム コードやヘルパーを記述する必要がなくなります。 タグ ヘルパーは、HTML 要素に属性として適用され、タグ ヘルパーを処理できないエディターでは無視されます。 これにより、さまざまなツールでビューのマークアップの編集およびレンダリングが可能になります。

カスタムの HTML マークアップの生成は、さまざまな組み込み HTML ヘルパーを使って行えます。 より複雑なユーザー インターフェイス ロジックは、[ビュー コンポーネント](xref:mvc/views/view-components)で処理できます。 ビュー コンポーネントは、コントローラーとビューが提供しているのと同じ SoC を提供します。 これにより、共通のユーザー インターフェイス要素で使用されるデータを扱うアクションおよびビューの必要がなくなります。

ASP.NET Core の他の多くの側面と同様に、ビューは、サービスを[ビューに挿入する](xref:mvc/views/dependency-injection)ことを許可する、[依存性の注入](xref:fundamentals/dependency-injection)をサポートしています。
