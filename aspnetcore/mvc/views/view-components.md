---
title: ASP.NET Core のビュー コンポーネント
author: rick-anderson
description: ASP.NET Core でのビュー コンポーネントの使用方法とそれらをアプリに追加する方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
uid: mvc/views/view-components
ms.openlocfilehash: 910fffbf360ed0f62f7fe20bc8bfdf5be8198876
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652316"
---
# <a name="view-components-in-aspnet-core"></a>ASP.NET Core のビュー コンポーネント

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="view-components"></a>ビュー コンポーネント

ビュー コンポーネントは部分ビューと似ていますが、はるかに強力なものです。 ビュー コンポーネントでは、モデル バインドを使用せず、呼び出すときに指定されたデータのみに依存します。 この記事はコントローラーとビューを使用して作成されましたが、ビュー コンポーネントは Razor Pages でも利用できます。

ビュー コンポーネントの特徴は次のとおりです。

* 応答全体ではなく、チャンクをレンダリングします。
* コントローラーとビューの間にあるのと同じ関心の分離とテストの容易性の利点があります。
* パラメーターとビジネス ロジックを含めることができます。
* 通常、レイアウト ページから呼び出されます。

ビュー コンポーネントは、次のような部分ビューには複雑すぎる、再利用可能なレンダリング ロジックをどこでも使用できるようにするためのものです。

* 動的なナビゲーション メニュー
* タグ クラウド (データベースをクエリする場所)
* ログイン パネル
* ショッピング カート
* 新着情報の記事
* 一般的なブログのサイドバーのコンテンツ
* すべてのページでレンダリングされ、ユーザーの状態のログに応じて、ログアウトまたはログインのいずれかのリンクを示すログイン パネル

ビュー コンポーネントは、次の 2 つのパーツで構成されます。クラス (通常、[ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent) から派生) と、クラスで返される結果 (通常はビュー) です。 コントローラーと同様に、ビュー コンポーネントは POCO の場合がありますが、ほとんどの開発者は `ViewComponent` から派生させて、利用できるメソッドとプロパティを活用する必要があります。

ビュー コンポーネントが、アプリの仕様を満たしているかどうかを検討する場合は、代わりに Razor コンポーネントを使用することを検討してください。 Razor コンポーネントもまた、C# コードとマークアップを組み合わせて、再利用可能な UI ユニットを生成します。 Razor コンポーネントは、クライアント側の UI ロジックとコンポジションを提供する場合の開発者の生産性のために設計されています。 詳細については、<xref:blazor/components> を参照してください。

## <a name="creating-a-view-component"></a>ビューのコンポーネントを作成する

このセクションには、ビュー コンポーネントを作成するための高レベルの要件が含まれています。 記事の後半で、各ステップの詳細を検証し、ビュー コンポーネントを作成します。

### <a name="the-view-component-class"></a>ビュー コンポーネント クラス

ビュー コンポーネント クラスは、次のいずれかによって作成できます。

* *ViewComponent* から派生させる
* `[ViewComponent]` 属性でクラスを装飾するか、`[ViewComponent]` 属性でクラスから派生させる
* 名前がサフィックス *ViewComponent* で終わるクラスを作成する

コントローラーと同様に、ビュー コンポーネントは、パブリック クラス、入れ子にされていないクラス、および非抽象クラスである必要があります。 ビュー コンポーネント名は、"ViewComponent" サフィックスを除いたクラス名です。 また、これは `ViewComponentAttribute.Name` プロパティを使用して、明示的に指定することもできます。

ビュー コンポーネント クラスの特徴は次のとおりです。

* コンストラクターの[依存性の注入](../../fundamentals/dependency-injection.md)を完全にサポートします

* コントローラーのライフサイクルに関わりません。つまり、ビュー コンポーネントで[フィルター](../controllers/filters.md)を使用できないということです

### <a name="view-component-methods"></a>ビュー コンポーネント メソッド

ビュー コンポーネントでは、`InvokeAsync` を返す `Task<IViewComponentResult>` メソッドまたは `Invoke` を返す同期 `IViewComponentResult` メソッドでロジックを定義します。 パラメーターは、モデル バインドではなく、ビュー コンポーネントから直接取得します。 ビュー コンポーネントが要求を直接処理することはありません。 通常、ビュー コンポーネントは、モデルを初期化し、`View` メソッドを呼び出してビューに渡します。 要約すると、ビュー コンポーネント メソッドの特徴は次のとおりです。

* `InvokeAsync` を返す `Task<IViewComponentResult>` メソッドまたは `Invoke` を返す同期 `IViewComponentResult` メソッドを定義します。
* 通常、モデルを初期化し、`ViewComponent` `View` メソッドを呼び出してビューに渡します。
* パラメーターは HTTP ではなく、呼び出し元のメソッドから取得されます。 モデル バインドはありません。
* HTTP エンドポイントとして直接到達することはできません。 (通常はビュー内で) コードから呼び出されます。 ビュー コンポーネントでは要求が処理されません。
* 現在の HTTP 要求からの詳細ではなく、シグネチャでオーバーロードされます。

### <a name="view-search-path"></a>ビューの検索パス

ランタイムでは、次のパスでビューを検索します。

* /Views/{コントローラー名}/Components/{ビュー コンポーネント名}/{ビュー名}
* /Views/Shared/Components/{ビュー コンポーネント名}/{ビュー名}
* /Pages/Shared/Components/{ビュー コンポーネント名}/{ビュー名}

検索パスは、コントローラー + ビューおよび Razor Pages を使うプロジェクトに適用されます。

ビュー コンポーネントの既定のビュー名は、*Default* です。つまり、通常、ビュー ファイルは *Default.cshtml* という名前になるということです。 ビュー コンポーネントの結果を作成したり、`View` メソッドを呼び出したりするときに、別のビュー名を指定することができます。

ビュー ファイルに *Default.cshtml* という名前を付けて、"*Views/Shared/Components/{ビュー コンポーネント名}/{ビュー名}* " というパスを使用することをお勧めします。 このサンプルで使用される `PriorityList` ビュー コンポーネントは、ビュー コンポーネント ビューに *Views/Shared/Components/PriorityList/Default.cshtml* を使用します。

### <a name="customize-the-view-search-path"></a>ビューの検索パスをカスタマイズする

ビューの検索パスをカスタマイズするには、Razor の <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> コレクションを変更します。 たとえば、"/Components/{ビュー コンポーネント名}/{ビュー名}" というパス内のビューを検索するには、次のように新しい項目をコレクションに追加します。

[!code-cs[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

上記のコードでは、プレースホルダー "{0}" はパス "/Components/{ビュー コンポーネント名}/{ビュー名}" を表しています。

## <a name="invoking-a-view-component"></a>ビュー コンポーネントを呼び出す

ビュー コンポーネントを使用するには、ビュー内で以下を呼び出します。

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

パラメーターは、`InvokeAsync` メソッドに渡されます。 この記事で開発した `PriorityList` ビュー コンポーネントは、*Views/ToDo/Index.cshtml* ビュー ファイルから呼び出されます。 以下では、`InvokeAsync` メソッドは、2 つのパラメーターで呼び出されます。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a>タグ ヘルパーとしてビュー コンポーネントを呼び出す

ASP.NET Core 1.1 以降の場合は、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)としてビュー コンポーネントを呼び出すことができます。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

タグ ヘルパーのパスカル ケースのクラスとメソッドのパラメーターは、それぞれ[ケバブ ケース](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)に変換されます。 ビュー コンポーネントを呼び出すタグ ヘルパーでは、`<vc></vc>` 要素を使用します。 ビュー コンポーネントは、次のように指定されます。

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

ビュー コンポーネントをタグ ヘルパーとして使用するには、`@addTagHelper` ディレクティブを使用して、ビュー コンポーネントを含むアセンブリを登録します。 ビュー コンポーネントが `MyWebApp` と呼ばれるアセンブリにある場合は、次のディレクティブを *_ViewImports.cshtml* ファイルに追加します。

```cshtml
@addTagHelper *, MyWebApp
```

ビュー コンポーネントを参照する任意のファイルへのタグ ヘルパーとして、ビュー コンポーネントを登録できます。 タグ ヘルパーを登録する方法の詳細については、「[Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)」 (タグ ヘルパーのスコープの管理) を参照してください。

このチュートリアルで使用される `InvokeAsync` メソッドは、次のとおりです。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

タグ ヘルパーのマークアップでは、次のようになります。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

上記のサンプルでは、`PriorityList` ビュー コンポーネントは `priority-list` になります。 ビュー コンポーネントに対するパラメーターは、ケバブ ケースの属性として渡されます。

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a>ビュー コンポーネントをコントローラーから直接呼び出す

通常、ビュー コンポーネントはビューから呼び出されますが、コントローラー メソッドから直接呼び出すことができます。 ビュー コンポーネントでコントローラーなどのエンドポイントを定義しないときに、`ViewComponentResult` のコンテンツを返すコントローラー アクションを簡単に実装できます。

この例では、ビュー コンポーネントは、コントローラーから直接呼び出されます。

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>チュートリアル: 単純なビュー コンポーネントの作成

スタート コードを[ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)、ビルド、およびテストします。 これは、`ToDo`[ToDo]*項目のリストを表示する* コントローラーを備えた、単純なプロジェクトです。

![[ToDo] のリスト](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>ViewComponent クラスの追加

*ViewComponents* フォルダーを作成して、次の `PriorityListViewComponent` クラスを追加します。

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

コードに関する注意事項

* ビュー コンポーネント クラスは、プロジェクト内の**任意**のフォルダーに含めることができます。
* クラス名 PriorityList**ViewComponent** は、サフィックス **ViewComponent** で終わるため、ビューからクラス コンポーネントを参照するときに、ランタイムでは文字列 "PriorityList" を使用します。 これについては、後で詳しく説明します。
* `[ViewComponent]` 属性では、ビュー コンポーネントを参照するために使用する名前を変更できます。 たとえば、クラスに `XYZ` という名前を付けて、`ViewComponent` 属性を適用することができます。

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* 上述の `[ViewComponent]` 属性は、ビュー コンポーネント セレクターに、コンポーネントに関連付けられたビューを探す場合は `PriorityList` という名前を使用し、ビューからクラス コンポーネントを参照する場合は文字列 "PriorityList" を使用するように指示します。 これについては、後で詳しく説明します。
* コンポーネントでは、[依存性の注入](../../fundamentals/dependency-injection.md)を使用して、データ コンテキストを利用できるようにします。
* `InvokeAsync` ではビューから呼び出すことができるメソッドを表示し、任意の数の引数を取得できます。
* `InvokeAsync` メソッドでは、`ToDo` と `isDone` パラメーターを満たす `maxPriority` 項目のセットを返します。

### <a name="create-the-view-component-razor-view"></a>ビュー コンポーネントの Razor ビューの作成

* *Views/Shared/Components* フォルダーを作成します。 このフォルダーは、**Components** という名前にする*必要があります*。

* *Views/Shared/Components/PriorityList* フォルダーを作成します。 このフォルダー名は、ビュー コンポーネント クラスの名前、または (規則に従い、クラス名に *ViewComponent* サフィックスを使用した場合は) サフィックスを差し引いたクラスの名前に一致する必要があります。 `ViewComponent` 属性を使用した場合は、クラス名は属性の指定に一致する必要があります。

* *Views/Shared/Components/PriorityList/Default.cshtml* Razor ビューを作成します。


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   Razor ビューでは、`TodoItem` のリストを取得して表示します。 ビュー コンポーネントの `InvokeAsync` メソッドで (サンプルのように) ビューの名前を渡さない場合、*Default* が規則によってビュー名に使用されます。 このチュートリアルの後半で、ビューの名前を渡す方法について示します。 特定のコントローラーの既定のスタイルをオーバーライドするには、コントローラーに固有のビュー フォルダー (例: *Views/ToDo/Components/PriorityList/Default.cshtml)* にビューを追加します。

    ビュー コンポーネントがコントローラーに固有の場合、それをコントローラーに固有のフォルダー (*Views/ToDo/Components/PriorityList/Default.cshtml*) に追加できます。

* 優先順位リストのコンポーネントの呼び出しを含む `div` を *Views/ToDo/index.cshtml* ファイルの下部に追加します。

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

`@await Component.InvokeAsync` マークアップは、ビュー コンポーネントを呼び出すための構文を示します。 最初の引数は、呼び出す必要があるコンポーネントの名前です。 後続のパラメーターは、そのコンポーネントに渡されます。 `InvokeAsync` では、任意の数の引数を取得できます。

アプリをテストします。 次の画像は、[ToDo] リストと優先順位の項目を示します。

![[ToDo] リストと優先順位の項目](view-components/_static/pi.png)

また、コントローラーから直接ビュー コンポーネントを呼び出すこともできます。

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC アクションからの優先順位の項目](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>ビュー名を指定する

複雑なビュー コンポーネントでは、いくつかの条件下で、既定以外のビューを指定する必要がある場合があります。 次のコードでは、`InvokeAsync` メソッドから "PVC" ビューを指定する方法を示しています。 `InvokeAsync` クラスで `PriorityListViewComponent` メソッドを更新します。

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

*Views/Shared/Components/PriorityList/Default.cshtml* ファイルを *Views/Shared/Components/PriorityList/PVC.cshtml* という名前のビューにコピーします。 PVC ビューが使用されていることを示すために、見出しを追加します。

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

*Views/ToDo/Index.cshtml* を更新します。

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

アプリを実行して、PVC ビューを確認します。

![優先順位のビュー コンポーネント](view-components/_static/pvc.png)

PVC ビューがレンダリングされない場合は、4 以上の優先順位でビュー コンポーネントを呼び出していることを確認します。

### <a name="examine-the-view-path"></a>ビューのパスを調べる

* 優先順位ビューが返されないように、優先順位パラメーターを 3 以下に変更します。
* 一時的に *Views/ToDo/Components/PriorityList/Default.cshtml* の名前を *1Default.cshtml* に変更します。
* アプリをテストすると、次のエラーが表示されます。

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* *Views/ToDo/Components/PriorityList/1Default.cshtml* を *Views/Shared/Components/PriorityList/Default.cshtml* にコピーします。
* *[Shared]* の [ToDo] ビュー コンポーネントのビューにマークアップを追加して、そのビューが *[Shared]* フォルダーからのものであることを示します。
* **[Shared]** コンポーネント ビューをテストします。

![[Shared] コンポーネント ビューを含む [ToDo] 出力](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a>ハードコーディングされた文字列の回避

コンパイル時間の安全性を確保する必要がある場合は、ハードコーディングされたビュー コンポーネント名をクラス名に置き換えることができます。 "ViewComponent" サフィックスのないビュー コンポーネントを作成します。

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

`using` ステートメントを Razor ビュー ファイルに追加して、`nameof` 演算子を使用します。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a>同期作業を実行する

非同期作業を実行する必要がない場合は、フレームワークで同期 `Invoke` メソッドの呼び出しが処理されます。 次のメソッドでは同期 `Invoke` ビュー コンポーネントを作成します。

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

ビュー コンポーネントの Razor ファイルに、`Invoke` メソッドに渡された文字列が一覧表示されます (*Views/Home/Components/PriorityList/Default.cshtml*)。

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

次のいずれかの方法を使用して、Razor ファイルでビュー コンポーネントが呼び出されます (たとえば、*Views/Home/Index.cshtml*)。

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)

<xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> の方法を使用するには、`Component.InvokeAsync` を呼び出します。

::: moniker-end

::: moniker range="< aspnetcore-1.1"

*を使用して、Razor ファイルでビュー コンポーネントが呼び出されます (たとえば、* Views/Home/Index.cshtml<xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>)。

`Component.InvokeAsync` を呼び出します。

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

タグ ヘルパーを使用するには、`@addTagHelper` ディレクティブを使用して、ビュー コンポーネントを含むアセンブリを登録します (ビュー コンポーネントは、`MyWebApp` と呼ばれるアセンブリ内にあります)。

```cshtml
@addTagHelper *, MyWebApp
```

Razor マークアップ ファイルでビュー コンポーネントのタグ ヘルパーを使用します。

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

`PriorityList.Invoke` のメソッド署名は同期的ですが、Razor ではマークアップ ファイルで `Component.InvokeAsync` を使用してメソッドを見つけて呼び出します。

## <a name="all-view-component-parameters-are-required"></a>ビュー コンポーネントのすべてのパラメーターが必要

ビュー コンポーネントの各パラメーターは、必須の属性です。 [こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5011)を参照してください。 パラメーターを省略した場合は、次のようになります。

* `InvokeAsync` メソッドのシグネチャが一致しないため、メソッドが実行されません。
* ViewComponent がマークアップをレンダリングしません。
* エラーがスローされません。

## <a name="additional-resources"></a>その他のリソース

* [ビューへの依存性の注入](xref:mvc/views/dependency-injection)
