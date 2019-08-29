---
title: 最初の Blazor アプリを構築する
author: guardrex
description: Blazor アプリを段階的に構築します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 2f0410eb75a6ef3f3d361c75bce7e79ae6d42a61
ms.sourcegitcommit: 983b31449fe398e6e922eb13e9eb6f4287ec91e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2019
ms.locfileid: "70017502"
---
# <a name="build-your-first-blazor-app"></a>最初の Blazor アプリを構築する

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

このチュートリアルでは、Blazor アプリをビルドして変更する方法を示します。

<xref:blazor/get-started> の記事にあるガイダンスに従って、このチュートリアルの Blazor プロジェクトを作成します。 プロジェクトに *ToDoList* という名前を付けます。

## <a name="build-components"></a>コンポーネントを構築する

1. *Pages* フォルダー内にあるアプリの 3 つのページそれぞれを参照します。ホーム、カウンター、データのフェッチです。 これらのページは Razor コンポーネント ファイル *Index.razor*、*Counter.razor*、および *FetchData.razor* によって実装されています。

1. Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。 Web ページでカウンターをインクリメントする場合、通常は JavaScript を記述することが必要ですが、Blazor には C# を使ったより優れた方法が用意されています。

1. *Counter.razor* ファイルで `Counter` コンポーネントの実装を調べます。

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   `Counter` コンポーネントの UI は、HTML を使って定義されています。 動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。 HTML マークアップと C# のレンダリング ロジックは、ビルド時にコンポーネント クラスに変換されます。 生成される .NET クラスの名前はファイル名と同じです。

   コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。 `@code` ブロック内では、イベント処理や他のコンポーネント ロジックの定義のために、コンポーネントの状態 (プロパティ、フィールド) とメソッドを指定します。 これらのメンバーは、コンポーネントのレンダリング ロジックの一部として、またイベントを処理するために使われます。

   **[クリックしてください]** ボタンを選択すると:

   * `Counter` コンポーネントの登録済みの `onclick` ハンドラーが呼び出されます (`IncrementCount` メソッドです)。
   * `Counter` コンポーネントによりそのレンダリング ツリーが再生成されます。
   * 新しいレンダリング ツリーが以前のものと比較されます。
   * ドキュメント オブジェクト モデル (DOM) に対する変更のみが適用されます。 表示されている数が更新されます。

1. `Counter` コンポーネントの C# のロジックを変更して、カウントが 1 ではなく 2 ずつインクリメントされるようにします。

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. アプリをリビルドして実行し、変更を確認します。 **[クリックしてください]** ボタンを選択します。 カウンターは、2 ずつインクリメントされます。

## <a name="use-components"></a>コンポーネントを使う

HTML 構文を使用して、別のコンポーネント内にコンポーネントを含めます。

1. `Index` コンポーネント (*Index.razor*) に `<Counter />` 要素を追加することで、アプリの `Index` コンポーネントに `Counter` コンポーネントを追加します。

   このエクスペリエンスのためにクライアント側 Blazor を使っている場合、`Index` コンポーネントによって `SurveyPrompt` コンポーネントが使用されます。 `<SurveyPrompt>` 要素を `<Counter />` 要素に置き換えます。 このエクスペリエンスに Blazor サーバー側アプリを使用している場合は、`<Counter />` 要素を `Index` コンポーネントに追加します。

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. アプリケーションをリビルドして実行します。 `Index` コンポーネントには、固有のカウンターがあります。

## <a name="component-parameters"></a>コンポーネントのパラメーター

コンポーネントにパラメーターを持たせることもできます。 コンポーネントのパラメーターは、`[Parameter]` で修飾されたコンポーネント クラス上で、パブリック プロパティを使用して定義されます。 マークアップ内でコンポーネントの引数を指定するには、属性を使います。

1. コンポーネントの `@code` に関する C# コードを更新します。

   * `[Parameter]` 属性で修飾された `IncrementAmount` プロパティを追加します。
   * `currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。

   *Pages/Counter.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. 属性を使って、`Index` コンポーネントの `<Counter>` 要素に `IncrementAmount` パラメーターを指定します。 カウンターを 10 ずつインクリメントするように値を設定します。

   *Pages/Index.razor*:

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. `Index` コンポーネントを再度読み込みます。 カウンターは、 **[クリックしてください]** ボタンを選択するたびに 10 ずつインクリメントされます。 `Counter` コンポーネントにあるカウンターは、継続して 1 ずつインクリメントされます。

## <a name="route-to-components"></a>コンポーネントにルーティングする

*Counter.razor* ファイルの上部にある `@page` ディレクティブは、`Counter` コンポーネントがルーティング エンドポイントであることを指定しています。 `Counter` コンポーネントによって `/counter` に送信された要求が処理されます。 `@page` ディレクティブがない場合、ルーティングされた要求はコンポーネントでは処理されませんが、そのコンポーネントは他のコンポーネントから引き続き使用できます。

## <a name="dependency-injection"></a>依存関係の挿入

アプリのサービス コンテナーに登録されたサービスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) を介してコンポーネントから使用できます。 `@inject` ディレクティブを使ってサービスをコンポーネントに挿入します。

`FetchData` コンポーネントのディレクティブを調べます。

サーバー側 Razor アプリを使用する場合、`WeatherForecastService` サービスは[シングルトン](xref:fundamentals/dependency-injection#service-lifetimes)として登録されているため、サービスの 1 つのインスタンスをアプリ全体で使用できます。 コンポーネントに `WeatherForecastService` サービスのインスタンスを挿入するために、`@inject` ディレクティブが使われています。

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

`FetchData` コンポーネントでは、`ForecastService` のような挿入されたサービスを使って、`WeatherForecast` オブジェクトの配列を取得します。

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

クライアント側 Blazor アプリを使用する場合、*wwwroot/sample-data* フォルダー内の *weather.json* ファイルから天気予報データを取得するために、`HttpClient` が挿入されます。

*Pages/FetchData.razor*:

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

各 forecast インスタンスを気象データのテーブルの行としてレンダリングするために、[\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ループが使われています。

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a>Todo リストを構築する

シンプルな ToDo リストを実装したアプリに新しいコンポーネントを追加します。

1. *Pages* フォルダー内のアプリに、*Todo.razor* という名前の空のファイルを追加します。

1. コンポーネントに最初のマークアップを指定します。

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. ナビゲーション バーに `Todo` コンポーネントを追加します。

   アプリのレイアウトでは、`NavMenu` コンポーネント (*Shared/NavMenu.csthml*) が使用されます。 レイアウトは、アプリ内でのコンテンツの重複を回避するために使うコンポーネントです。

   *Shared/NavMenu.csthml* ファイル内で、既存のリスト アイテムの下に以下のリスト アイテムのマークアップを追加して、`Todo` コンポーネント用の `<NavLink>` 要素を追加します。

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. アプリケーションをリビルドして実行します。 新しい Todo ページに移動して、`Todo` コンポーネントへのリンクが機能することを確認します。

1. Todo アイテムを表すクラスを保持するために、プロジェクトのルートに *TodoItem.cs* ファイルを追加します。 `TodoItem` クラス用に次の C# コードを使います。

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. `Todo` コンポーネントに戻ります (*Pages/Todo.razor*)。

   * Todo 項目用のフィールドを `@code` ブロックに追加します。 `Todo` コンポーネントでは、このフィールドを使って ToDo リストの状態を維持します。
   * 各 Todo アイテムをリスト アイテム (`<li>`) としてレンダリングするために、順序のないリストのマークアップと `foreach` ループを追加します。

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. アプリには、リストに Todo 項目を追加するための UI 要素が必要です。 順序のないリスト (`<ul>...</ul>`) の下に、テキスト入力 (`<input>`) とボタン (`<button>`) を追加します。

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. アプリケーションをリビルドして実行します。 **[Add todo]\(ToDo の追加\)** ボタンを選択しても何も起こりません。ボタンにイベント ハンドラーが関連付けられていないためです。

1. `Todo` コンポーネントに `AddTodo` メソッドを追加し、`@onclick` 属性を使ってこれをボタンの選択用に登録します。 ボタンを選択すると C# のメソッド `AddTodo` が呼び出されます。

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. 新しい Todo アイテムのタイトルを取得するために、`@code` ブロックの上部に文字列フィールド `newTodo` を追加し、`<input>` 要素の `bind` 属性を使ってこれをテキスト入力の値とバインドします。

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. 指定したタイトルを備えた `TodoItem` をリストに追加するように、`AddTodo` メソッドを更新します。 `newTodo` を空の文字列に設定して、テキスト入力の値をクリアします。

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. アプリケーションをリビルドして実行します。 Todo リストに Todo 項目をいくつか追加して、新しいコードをテストします。

1. 各 Todo アイテムのタイトルのテキストは編集可能にすることができます。また、チェック ボックスはユーザーが完了したアイテムを追跡するのに役立ちます。 各 Todo アイテムにチェック ボックス入力を追加し、その値を `IsDone` プロパティにバインドします。 `@todo.Title` を、`@todo.Title` にバインドされた `<input>` 要素に変更します。

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. それらの値がバインドされていることを確認するために、`<h1>` ヘッダーを更新して、完了していない (`IsDone` が `false` の) Todo アイテムの数のカウントを表示するようにします。

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. 完成した `Todo` コンポーネント (*Pages/Todo.razor*):

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. アプリケーションをリビルドして実行します。 Todo アイテムを追加して新しいコードをテストします。

> [!div class="nextstepaction"]
> <xref:blazor/components>
