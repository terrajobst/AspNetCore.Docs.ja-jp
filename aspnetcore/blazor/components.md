---
title: ASP.NET Core Razor コンポーネントを作成して使用する
author: guardrex
description: データにバインドする方法、イベントを処理する方法、コンポーネントのライフサイクルを管理する方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/components
ms.openlocfilehash: a79202565f45b4d26e280427892ea16b33f3f853
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943863"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor コンポーネントを作成して使用する

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

Blazor アプリは*コンポーネント*を使用して構築されます。 コンポーネントは、ページ、ダイアログ、フォームなどのユーザーインターフェイス (UI) の自己完結型のチャンクです。 コンポーネントには、HTML マークアップと、データを挿入したり UI イベントに応答したりするために必要な処理ロジックが含まれています。 コンポーネントは柔軟で軽量です。 入れ子にして再利用したり、プロジェクト間で共有したりすることができます。

## <a name="component-classes"></a>コンポーネントクラス

コンポーネントは、と HTML マークアップの C#組み合わせを使用して[razor](xref:mvc/views/razor)コンポーネントファイル (razor) に実装されます。 Blazor のコンポーネントは、正式に*Razor コンポーネント*として参照されます。

コンポーネントの名前は、大文字で始まる必要があります。 たとえば、 *MyCoolComponent*は有効で、 *MyCoolComponent*は無効です。

コンポーネントの UI は、HTML を使用して定義されます。 動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。 アプリがコンパイルされると、HTML マークアップC#およびレンダリングロジックはコンポーネントクラスに変換されます。 生成されたクラスの名前は、ファイルの名前と一致します。

コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。 `@code` ブロックでは、コンポーネントの状態 (プロパティ、フィールド) は、イベント処理のメソッド、またはその他のコンポーネントロジックを定義するために指定されます。 複数の `@code` ブロックが許容されます。

> [!NOTE]
> ASP.NET Core 3.0 の以前のプレビューでは、`@functions` ブロックは Razor コンポーネントの `@code` ブロックと同じ目的で使用されていました。 `@functions` ブロックは Razor コンポーネントで引き続き機能しますが、ASP.NET Core 3.0 Preview 6 以降では `@code` ブロックを使用することをお勧めします。

コンポーネントメンバーは、`@`で始まる式を使用してC# 、コンポーネントのレンダリングロジックの一部として使用できます。 たとえば、 C#フィールド名をプレフィックス `@` によって表示されます。 次の例では、が評価され、レンダリングされます。

* `font-style`の CSS プロパティ値に `_headingFontStyle` します。
* `<h1>` 要素の内容に `_headingText` します。

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

コンポーネントが最初にレンダリングされた後、コンポーネントはイベントに応答してレンダリングツリーを再生成します。 Blazor は、新しいレンダリングツリーを前のレンダリングツリーと比較し、ブラウザーのドキュメントオブジェクトモデル (DOM) に変更を適用します。

コンポーネントは通常C#のクラスであり、プロジェクト内の任意の場所に配置できます。 Web ページを生成するコンポーネントは、通常、[*ページ*] フォルダーにあります。 ページ以外のコンポーネントは、多くの場合、プロジェクトに追加された*共有*フォルダーまたはカスタムフォルダーに配置されます。 カスタムフォルダーを使用するには、カスタムフォルダーの名前空間を親コンポーネントまたはアプリの *_Imports razor*ファイルに追加します。 たとえば、次の名前空間は、アプリのルート名前空間が `WebApplication`場合*に、コンポーネントフォルダー内*のコンポーネントを使用可能にします。

```razor
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>コンポーネントを Razor Pages と MVC アプリに統合する

既存の Razor Pages および MVC アプリでコンポーネントを使用します。 Razor コンポーネントを使用するために既存のページやビューを書き直す必要はありません。 ページまたはビューが表示されると、コンポーネントは同時に prerendered されます。

::: moniker range=">= aspnetcore-3.1"

ページまたはビューからコンポーネントを表示するには、`Component` タグヘルパーを使用します。

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

パラメーターの引き渡し (たとえば、前の例では `IncrementAmount`) はサポートされています。

コンポーネントの `RenderMode` を構成します。

* ページに prerendered ます。
* は、ページに静的な HTML として表示されるか、またはユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Server`            | Blazor サーバーアプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれていません。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 |

ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。 コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。 コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。

静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。

コンポーネントのレンダリング方法、コンポーネントの状態、および `Component` タグヘルパーの詳細については、「<xref:blazor/hosting-models>」を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-3.1"

ページまたはビューからコンポーネントを表示するには、`RenderComponentAsync<TComponent>` HTML ヘルパーメソッドを使用します。

```cshtml
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
```

コンポーネントの `RenderMode` を構成します。

* ページに prerendered ます。
* は、ページに静的な HTML として表示されるか、またはユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 パラメーターはサポートされていません。 |
| `Server`            | Blazor サーバーアプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれていません。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 パラメーターはサポートされていません。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 パラメーターがサポートされています。 |

ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。 コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。 コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。

静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。

コンポーネントのレンダリング方法、コンポーネントの状態、および `RenderComponentAsync` HTML ヘルパーの詳細については、「<xref:blazor/hosting-models>」を参照してください。

::: moniker-end

## <a name="use-components"></a>コンポーネントを使う

コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。 コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。

属性のバインドでは大文字と小文字が区別されます。 たとえば、`@bind` が有効で、`@Bind` が無効です。

*Index. razor*の次のマークアップは、`HeadingComponent` インスタンスをレンダリングします。

```razor
<HeadingComponent />
```

*Components/HeadingComponent*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

コンポーネント名と一致しない大文字の最初の文字を含む HTML 要素がコンポーネントに含まれている場合は、要素に予期しない名前が付いていることを示す警告が出力されます。 コンポーネントの名前空間に `@using` ステートメントを追加すると、コンポーネントが使用可能になり、警告が削除されます。

## <a name="component-parameters"></a>コンポーネントのパラメーター

コンポーネントには、コンポーネントクラスのパブリックプロパティを使用して定義されるコンポーネント*パラメーター*を含めることができます。これは、`[Parameter]` 属性を使用して構成します。 マークアップ内でコンポーネントの引数を指定するには、属性を使います。

*Components/ChildComponent。 razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

サンプルアプリの次の例では、`ParentComponent` によって `ChildComponent`の `Title` プロパティの値が設定されます。

*Pages/ParentComponent。 razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a>子コンテンツ

コンポーネントでは、別のコンポーネントのコンテンツを設定できます。 割り当てコンポーネントは、受信コンポーネントを指定するタグの間にコンテンツを提供します。

次の例では、`ChildComponent` に、レンダリングする UI のセグメントを表す `RenderFragment`を表す `ChildContent` プロパティがあります。 `ChildContent` の値は、コンテンツをレンダリングする必要があるコンポーネントのマークアップに配置されます。 `ChildContent` の値は、親コンポーネントから受信され、ブートストラップパネルの `panel-body`内に表示されます。

*Components/ChildComponent。 razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> `RenderFragment` コンテンツを受け取るプロパティには、規約によって `ChildContent` 名前を付ける必要があります。

サンプルアプリの `ParentComponent` では、コンテンツを `<ChildComponent>` タグ内に配置することによって、`ChildComponent` を表示するためのコンテンツを提供できます。

*Pages/ParentComponent。 razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>属性スプラッティングと任意のパラメーター

コンポーネントは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャして表示できます。 Splatted Razor ディレクティブ[`@attributes`](xref:mvc/views/razor#attributes)を使用してコンポーネントがレンダリングされるときに、ディクショナリ内で追加の属性をキャプチャし、要素にすることができます。 このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。 たとえば、多くのパラメーターをサポートする `<input>` に対して、属性を個別に定義するのは面倒な場合があります。

次の例では、最初の `<input>` 要素 (`id="useIndividualParams"`) は個々のコンポーネントパラメーターを使用し、2番目の `<input>` 要素 (`id="useAttributesDict"`) は属性スプラッティングを使用します。

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

パラメーターの型は、文字列キーを使用して `IEnumerable<KeyValuePair<string, object>>` を実装する必要があります。 このシナリオでは `IReadOnlyDictionary<string, object>` を使用することもできます。

両方の方法を使用して表示される `<input>` 要素は同じです。

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

任意の属性を受け入れるには、`CaptureUnmatchedValues` プロパティを `true`に設定して、`[Parameter]` 属性を使用してコンポーネントパラメーターを定義します。

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`[Parameter]` の `CaptureUnmatchedValues` プロパティを使用すると、パラメーターを他のパラメーターと一致しないすべての属性と一致させることができます。 コンポーネントは、`CaptureUnmatchedValues`で1つのパラメーターのみを定義できます。 `CaptureUnmatchedValues` で使用されるプロパティの型は、文字列キーを使用して `Dictionary<string, object>` から割り当て可能である必要があります。 このシナリオでは、`IEnumerable<KeyValuePair<string, object>>` または `IReadOnlyDictionary<string, object>` もオプションです。

要素属性の位置を基準とした `@attributes` の位置が重要です。 要素に対して `@attributes` が splatted されると、属性は右から左 (最後から順) に処理されます。 `Child` コンポーネントを使用するコンポーネントの次の例を考えてみます。

*Parentcomponent。 razor*:

```razor
<ChildComponent extra="10" />
```

*Childcomponent。 razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` コンポーネントの `extra` 属性が `@attributes`の右側に設定されます。 属性が右から左に処理されるため、`Parent` コンポーネントのレンダリングされる `<div>` には `extra="5"` が含まれます。

```html
<div extra="5" />
```

次の例では、`Child` コンポーネントの `<div>`で `extra` と `@attributes` の順序が逆になっています。

*Parentcomponent。 razor*:

```razor
<ChildComponent extra="10" />
```

*Childcomponent。 razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Parent` コンポーネントに表示される `<div>` には、追加の属性を通じて渡された `extra="10"` が含まれます。

```html
<div extra="10" />
```

## <a name="data-binding"></a>データ バインディング

コンポーネントと DOM 要素の両方に対するデータバインディングは、 [`@bind`](xref:mvc/views/razor#bind)属性を使用して行われます。 次の例では、`CurrentValue` プロパティをテキストボックスの値にバインドします。

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

テキストボックスがフォーカスを失うと、プロパティの値が更新されます。

テキストボックスは、プロパティの値の変更に対する応答ではなく、コンポーネントがレンダリングされたときにのみ UI で更新されます。 イベントハンドラーのコードを実行した後にコンポーネントがレンダリングされるため、*通常*、イベントハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。

`CurrentValue` プロパティ (`<input @bind="CurrentValue" />`) で `@bind` を使用することは、基本的に次のようなものです。

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。 ユーザーがテキストボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。 実際には、型変換が実行されるケースが `@bind` によって処理されるため、コード生成はより複雑になります。 原則として、`@bind` は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。

`@bind` 構文を使用した `onchange` イベントの処理に加えて、`event` パラメーター ([`@bind-value:event`](xref:mvc/views/razor#bind)) を使用して[`@bind-value`](xref:mvc/views/razor#bind)属性を指定することで、プロパティまたはフィールドを他のイベントを使用してバインドできます。 次の例では、`oninput` イベントの `CurrentValue` プロパティをバインドします。

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキストボックスの値が変更されたときに `oninput` が発生します。

**解析不可能値**

データバインド要素に解析できない値を指定すると、バインドイベントがトリガーされたときに、解析されていない値が自動的に前の値に戻されます。

このような場合は、まず、次のことを確認してください。

* `<input>` 要素は、初期値 `123`を持つ `int` 型にバインドされます。

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* ユーザーは、要素の値をページの `123.45` に更新し、要素のフォーカスを変更します。

前のシナリオでは、要素の値が `123`に戻されます。 `123`の元の値を優先して `123.45` 値が拒否された場合、ユーザーはその値が受け入れられていないことを認識します。

既定では、バインドは要素の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) に適用されます。 別のイベントを設定するには、`@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` を使用します。 `oninput` イベント (`@bind-value:event="oninput"`) の場合、解析できない値を導入するキーストロークの後に再設定が発生します。 `int`バインドされた型の `oninput` イベントを対象とする場合、ユーザーは `.` 文字を入力できません。 `.` 文字はすぐに削除されるので、ユーザーは、整数のみが許可されるというフィードバックをすぐに受け取ることができます。 `oninput` イベントの値を元に戻すことは、ユーザーが解析できない `<input>` 値のクリアを許可する必要がある場合など、理想的ではありません。 代替手段は次のとおりです。

* `oninput` イベントは使用しないでください。 既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。
* `int?` や `string`などの null 許容型にバインドし、無効なエントリを処理するカスタムロジックを提供します。
* `InputNumber` や `InputDate`などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。 フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。 フォーム検証コンポーネント:
  * ユーザーが無効な入力を提供し、関連付けられた `EditContext`で検証エラーを受信することを許可します。
  * 追加の web フォームデータを入力するユーザーに干渉することなく、UI に検証エラーを表示します。

**グローバリゼーション**

`@bind` 値は、現在のカルチャの規則を使用して表示および解析するように書式設定されます。

現在のカルチャには、<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> プロパティからアクセスできます。

[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)は、次のフィールドの種類 (`<input type="{TYPE}" />`) に使用されます。

* `date`
* `number`

上記のフィールド型は次のとおりです。

* は、適切なブラウザーベースの書式規則を使用して表示されます。
* 自由形式のテキストを含めることはできません。
* ブラウザーの実装に基づいてユーザーの操作特性を指定します。

次のフィールド型には特定の書式設定要件がありますが、Blazor で現在サポートされていないのは、すべての主要なブラウザーでサポートされていないためです。

* `datetime-local`
* `month`
* `week`

`@bind` では、`@bind:culture` パラメーターを使用して、値の解析および書式設定のための <xref:System.Globalization.CultureInfo?displayProperty=fullName> を提供します。 `date` および `number` のフィールドの種類を使用する場合は、カルチャを指定しないことをお勧めします。 `date` と `number` には、必要なカルチャを提供する Blazor サポートが組み込まれています。

ユーザーのカルチャを設定する方法については、「[ローカリゼーション](#localization)」セクションを参照してください。

**書式指定文字列**

データバインディングは、 [`@bind:format`](xref:mvc/views/razor#bind)を使用して <xref:System.DateTime> 書式指定文字列で動作します。 通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

前のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text`に設定されています。 `@bind:format` は、次の .NET 型のバインドに対してサポートされています。

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName> ですか。
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName> ですか。

`@bind:format` 属性は、`<input>` 要素の `value` に適用する日付形式を指定します。 この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。

Blazor に日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。 推奨事項では、`date` フィールドの種類で形式が指定されている場合、バインドの `yyyy-MM-dd` 日付形式のみを使用することをお勧めします。

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

**コンポーネントのパラメーター**

バインディングはコンポーネントパラメーターを認識しますが、`@bind-{property}` はコンポーネント間でプロパティ値をバインドできます。

次の子コンポーネント (`ChildComponent`) には、`Year` コンポーネントパラメーターと `YearChanged` コールバックがあります。

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>` については、「 [Eventcallback](#eventcallback) 」セクションを参照してください。

次の親コンポーネントは `ChildComponent` を使用し、親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

`ParentComponent` を読み込むと、次のマークアップが生成されます。

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentComponent`のボタンを選択して `ParentYear` プロパティの値が変更された場合は、`ChildComponent` の `Year` プロパティが更新されます。 `Year` の新しい値は、`ParentComponent` が再度実行されたときに UI に表示されます。

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` パラメーターは、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。

慣例により、`<ChildComponent @bind-Year="ParentYear" />` は基本的には書き込みと同じです。

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

一般に、プロパティは、`@bind-property:event` 属性を使用して、対応するイベントハンドラーにバインドできます。 たとえば、プロパティ `MyProp` は、次の2つの属性を使用して `MyEventHandler` にバインドできます。

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>イベント処理

Razor コンポーネントは、イベント処理機能を提供します。 `on{EVENT}` という名前の HTML 要素属性 (`onclick`、`onsubmit`など) とデリゲート型の値がある場合、Razor コンポーネントはその属性の値をイベントハンドラーとして扱います。 属性の名前は常に[`@on{EVENT}`](xref:mvc/views/razor#onevent)書式設定されます。

次のコードは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

次のコードは、UI でチェックボックスが変更されたときに `CheckChanged` メソッドを呼び出します。

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

イベントハンドラーを非同期にして、<xref:System.Threading.Tasks.Task>を返すこともできます。 [Statehaschanged](xref:blazor/lifecycle#state-changes)を手動で呼び出す必要はありません。 例外は、発生するとログに記録されます。

次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>イベント引数の型

イベントによっては、イベント引数の型が許可されます。 これらのイベントの種類のいずれかにアクセスする必要がない場合は、メソッドの呼び出しで必要とされません。

次の表に、サポートされている `EventArgs` を示します。

| Event            | &lt;クラス&gt; のすべてのオブジェクト                | DOM のイベントとメモ |
| ---------------- | -------------------- | -------------------- |
| クリップボード        | `ClipboardEventArgs` | `oncut`では、 `oncopy`では、 `onpaste` |
| ドラッグ             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` および `DataTransferItem` ドラッグした項目データを保持します。 |
| エラー            | `ErrorEventArgs`     | `onerror` |
| Event            | `EventArgs`          | *全般*<br>`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`<br><br>*クリップボード*<br>`onbeforecut`では、 `onbeforecopy`では、 `onbeforepaste`<br><br>*入力*<br>`oninvalid`、 `onreset`、 `onselect`、 `onselectionchange`、 `onselectstart`、 `onsubmit`<br><br>*メディア*<br>`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`の `onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、および `onsuspend` |
| フォーカス            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>には `relatedTarget`のサポートは含まれていません。 |
| [入力]            | `ChangeEventArgs`    | `onchange`、 `oninput` |
| キーボード         | `KeyboardEventArgs`  | `onkeydown`では、 `onkeypress`では、 `onkeyup` |
| マウス            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| マウス ポインター    | `PointerEventArgs`   | `onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture` |
| マウス ホイール      | `WheelEventArgs`     | `onwheel`、 `onmousewheel` |
| 中         | `ProgressEventArgs`  | `onabort`、 `onload`、 `onloadend`、 `onloadstart`、 `onprogress`、 `ontimeout` |
| タッチ            | `TouchEventArgs`     | `ontouchstart`、 `ontouchend`、 `ontouchmove`、 `ontouchenter`、 `ontouchleave`、 `ontouchcancel`<br><br>`TouchPoint` は、タッチを区別するデバイス上の1つの連絡先ポイントを表します。 |

前の表に示したイベントのプロパティとイベント処理動作の詳細については、「[参照ソースの EventArgs クラス (aspnet/AspNetCore release/3.0 分岐)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web)」を参照してください。

### <a name="lambda-expressions"></a>ラムダ式

ラムダ式を使用することもできます。

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

多くの場合、要素のセットを反復処理するときなど、追加の値を終了すると便利です。 次の例では、3つのボタンを作成します。各ボタンは、UI で選択されたときに、イベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> ラムダ式では、`for` ループでループ変数 (`i`) を**直接使用しないでください。** それ以外の場合、すべてのラムダ式で同じ変数が使用され、`i`の値はすべてのラムダで同じになります。 常にローカル変数 (前の例では`buttonNumber`) で値をキャプチャし、それを使用します。

### <a name="eventcallback"></a>EventCallback

入れ子になったコンポーネントの一般的なシナリオとして、子コンポーネントのイベント&mdash;が発生したときに親コンポーネントのメソッドを実行することが望まれます。たとえば、子で `onclick` イベントが発生した場合などです。 コンポーネント間でイベントを公開するには、`EventCallback`を使用します。 親コンポーネントは、コールバックメソッドを子コンポーネントの `EventCallback`に割り当てることができます。

サンプルアプリ (*Components/ChildComponent. razor*) の `ChildComponent` は、ボタンの `onclick` ハンドラーが、サンプルの `ParentComponent`から `EventCallback` デリゲートを受け取るように設定されている方法を示しています。 `EventCallback` は `MouseEventArgs`で入力されます。これは、周辺機器の `onclick` イベントに適しています。

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` は、子の `EventCallback<T>` を `ShowMessage` メソッドに設定します。

*Pages/ParentComponent。 razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

`ChildComponent`でボタンが選択されている場合:

* `ParentComponent`の `ShowMessage` メソッドが呼び出されます。 `messageText` が更新され、`ParentComponent`に表示されます。
* このコールバックのメソッド (`ShowMessage`) では、 [Statehaschanged](xref:blazor/lifecycle#state-changes)の呼び出しは必要ありません。 `StateHasChanged` は、子イベントと同様に、子の内部で実行されるイベントハンドラーでコンポーネントの実行がトリガーされるのと同様に、`ParentComponent`をレンダリングするために自動的に呼び出されます。

`EventCallback` および `EventCallback<T>` 非同期デリゲートを許可します。 `EventCallback<T>` は厳密に型指定され、特定の引数型を必要とします。 `EventCallback` は弱く型指定され、任意の引数型を使用できます。

```razor
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

`InvokeAsync` で `EventCallback` または `EventCallback<T>` を呼び出し、<xref:System.Threading.Tasks.Task>を待機します。

```csharp
await callback.InvokeAsync(arg);
```

イベント処理とバインドコンポーネントのパラメーターには、`EventCallback` と `EventCallback<T>` を使用します。

厳密に型指定された `EventCallback<T>` `EventCallback`を優先します。 `EventCallback<T>` は、コンポーネントのユーザーに対してより適切なエラーフィードバックを提供します。 他の UI イベントハンドラーと同様に、イベントパラメーターの指定は省略可能です。 コールバックに値が渡されない場合は、`EventCallback` を使用します。

::: moniker range=">= aspnetcore-3.1"

### <a name="prevent-default-actions"></a>既定のアクションを禁止する

イベントの既定のアクションを回避するには、 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)ディレクティブ属性を使用します。

入力デバイスでキーが選択され、要素のフォーカスがテキストボックスに表示されている場合は、通常、ブラウザーによってテキストボックスにキーの文字が表示されます。 次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作を回避できます。 カウンターがインクリメントされ、 **+** キーが `<input>` 要素の値にキャプチャされません。

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

値を指定せずに `@on{EVENT}:preventDefault` 属性を指定することは `@on{EVENT}:preventDefault="true"`と同じです。

属性の値は、式にすることもできます。 次の例では、`_shouldPreventDefault` は `true` または `false`のいずれかに設定された `bool` フィールドです。

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

既定のアクションを防ぐために、イベントハンドラーは必要ありません。 イベントハンドラーを使用したり、既定のアクションシナリオを回避したりすることができます。

### <a name="stop-event-propagation"></a>イベントの伝達の停止

イベントの伝達を停止するには、 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)ディレクティブ属性を使用します。

次の例では、チェックボックスをオンにすると、2番目の子 `<div>` からのクリックイベントが親 `<div>`に反映されなくなります。

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker-end

## <a name="chained-bind"></a>チェーンバインド

一般的なシナリオでは、データバインドパラメーターをコンポーネントの出力のページ要素に連結します。 このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーンバインド*と呼ばれます。

チェーンバインドは、ページの要素で `@bind` 構文を使用して実装することはできません。 イベントハンドラーと値は、個別に指定する必要があります。 ただし、親コンポーネントでは、`@bind` 構文をコンポーネントのパラメーターと共に使用できます。

次の `PasswordField` コンポーネント (*Passwordfield*):

* `Password` プロパティに `<input>` 要素の値を設定します。
* [Eventcallback](#eventcallback)を使用して、`Password` プロパティの変更を親コンポーネントに公開します。

```razor
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

`PasswordField` コンポーネントが別のコンポーネントで使用されています。

```razor
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

前の例で、パスワードの確認またはトラップエラーを実行するには、次の手順を実行します。

* `Password` のバッキングフィールドを作成します (次のコード例では`password`)。
* `Password` setter で、チェックまたはトラップのエラーを実行します。

次の例では、パスワードの値にスペースが使用されている場合に、ユーザーにすぐにフィードバックを提供します。

```razor
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a>コンポーネントへの参照をキャプチャする

コンポーネント参照を使用すると、`Show` や `Reset`などのコマンドをそのインスタンスに発行できるように、コンポーネントインスタンスを参照することができます。 コンポーネント参照をキャプチャするには、次のようにします。

* 子コンポーネントに[`@ref`](xref:mvc/views/razor#ref)属性を追加します。
* 子コンポーネントと同じ型のフィールドを定義します。

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

コンポーネントがレンダリングされると、[`loginDialog`] フィールドに `MyLoginDialog` 子コンポーネントインスタンスが設定されます。 その後、コンポーネントインスタンスで .NET メソッドを呼び出すことができます。

> [!IMPORTANT]
> `loginDialog` 変数は、コンポーネントがレンダリングされた後にのみ設定され、その出力には `MyLoginDialog` 要素が含まれます。 この時点までは、参照するものはありません。 コンポーネント参照のレンダリングが完了した後にコンポーネント参照を操作するに[は、OnAfterRenderAsync メソッドまたは OnAfterRender メソッド](xref:blazor/lifecycle#after-component-render)を使用します。

コンポーネント参照のキャプチャは、[要素参照のキャプチャ](xref:blazor/javascript-interop#capture-references-to-elements)に似た構文を使用しますが、 [JavaScript 相互運用](xref:blazor/javascript-interop)機能ではありません。 コンポーネント参照は、.NET コードでのみ使用される&mdash;JavaScript コードに渡されません。

> [!NOTE]
> 子コンポーネントの状態を変化**さ**せるためにコンポーネント参照を使用しないでください。 代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。 通常の宣言型パラメーターを使用すると、子コンポーネントが自動的にレンダリングされます。

## <a name="invoke-component-methods-externally-to-update-state"></a>状態を更新するためにコンポーネントメソッドを外部に呼び出す

Blazor は、`SynchronizationContext` を使用して、1つの論理スレッドの実行を強制します。 Blazor によって発生するコンポーネントの[ライフサイクルメソッド](xref:blazor/lifecycle)とイベントコールバックは、この `SynchronizationContext`で実行されます。 タイマーやその他の通知など、外部のイベントに基づいてコンポーネントを更新する必要がある場合は、`InvokeAsync` メソッドを使用します。これにより、Blazorの `SynchronizationContext`にディスパッチされます。

たとえば、更新された状態のすべてのリッスンコンポーネントに通知できる通知*サービス*を考えてみます。

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

コンポーネントを更新するための `NotifierService` の使用法:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

前の例では、`NotifierService` は Blazorの `SynchronizationContext`の外部でコンポーネントの `OnNotify` メソッドを呼び出します。 `InvokeAsync` は、正しいコンテキストに切り替え、レンダリングをキューに移動するために使用されます。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>\@キーを使用して要素とコンポーネントの保存を制御する

要素またはコンポーネントのリストをレンダリングするときに、要素またはコンポーネントが変更された場合、Blazorの比較アルゴリズムは、前の要素またはコンポーネントを保持できるかどうか、およびモデルオブジェクトをどのようにマップするかを決定する必要があります。 通常、このプロセスは自動的に実行され、無視することができますが、プロセスの制御が必要になる場合があります。

次に例を示します。

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` コレクションの内容は、挿入、削除、または順序変更されたエントリで変更される可能性があります。 コンポーネントのれ時に、`<DetailsEditor>` コンポーネントが異なる `Details` パラメーター値を受け取るように変更されることがあります。 これにより、予想よりも複雑な再リリースが発生する可能性があります。 場合によっては、要素のフォーカスの喪失など、表示される動作の違いが発生する可能性があります。

マッピングプロセスは、`@key` ディレクティブ属性を使用して制御できます。 `@key` によって、キーの値に基づいて要素またはコンポーネントの保持が比較アルゴリズムによって保証されます。

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` コレクションが変更されると、比較アルゴリズムによって、`<DetailsEditor>` インスタンスと `person` インスタンスの間の関連付けが保持されます。

* `Person` が `People` の一覧から削除された場合は、対応する `<DetailsEditor>` インスタンスだけが UI から削除されます。 その他のインスタンスは変更されません。
* リスト内のある位置に `Person` が挿入されると、その対応する位置に1つの新しい `<DetailsEditor>` インスタンスが挿入されます。 その他のインスタンスは変更されません。
* `Person` エントリが再順序付けされている場合は、対応する `<DetailsEditor>` インスタンスが UI に保持され、並べ替えられます。

場合によっては、`@key` を使用すると、回避の複雑さが最小限に抑えられるため、フォーカス位置など、DOM のステートフルな部分に対する潜在的な問題を回避できます。

> [!IMPORTANT]
> キーは、各コンテナー要素またはコンポーネントに対してローカルです。 キーがドキュメント全体でグローバルに比較されることはありません。

### <a name="when-to-use-key"></a>\@キーを使用する場合

通常は、リストがレンダリングされるたびに (たとえば、`@foreach` ブロックで) `@key` を使用し、`@key`を定義するための適切な値が存在することを意味します。

また、`@key` を使用すると、オブジェクトが変更されたときに Blazor によって要素またはコンポーネントのサブツリーが保持されないようにすることもできます。

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

`@currentPerson` が変更された場合、`@key` attribute ディレクティブは、`<div>` とその子孫全体を破棄し、新しい要素とコンポーネントで UI 内のサブツリーを再構築する Blazor を強制的に実行します。 これは、`@currentPerson` 変更時に UI の状態が保持されないことを保証する必要がある場合に役立ちます。

### <a name="when-not-to-use-key"></a>\@キーを使用しない場合

`@key`を使用すると、パフォーマンスが低下します。 パフォーマンスコストは大きくありませんが、要素またはコンポーネントの保存規則を制御することによってアプリにメリットがある場合にのみ `@key` を指定します。

`@key` が使用されていない場合でも Blazor、子要素とコンポーネントインスタンスは可能な限り保持されます。 `@key` を使用する唯一の利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネントインスタンスにモデルインスタンスをマップする*方法*を制御することです。

### <a name="what-values-to-use-for-key"></a>\@キーに使用する値

一般に、`@key`には、次のいずれかの値を指定するのが理にかなっています。

* モデルオブジェクトインスタンス (たとえば、前の例のように、`Person` インスタンス)。 これにより、オブジェクト参照の等価性に基づいて保持されます。
* 一意の識別子 (たとえば、`int`型、`string`型、`Guid`型の主キー値)。

`@key` に使用される値が競合しないようにしてください。 競合するの値が同じ親要素内で検出された場合、Blazor は、古い要素またはコンポーネントを新しい要素またはコンポーネントに確定的にマップできないため、例外をスローします。 個別の値 (オブジェクトインスタンスや主キー値など) のみを使用してください。

## <a name="routing"></a>ルーティング

Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントにルートテンプレートを提供することで実現されます。

`@page` ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラスにルートテンプレートを指定する <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> が与えられます。 実行時に、ルーターは `RouteAttribute` を持つコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを持つコンポーネントをレンダリングします。

コンポーネントには、複数のルートテンプレートを適用できます。 次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute`に対する要求に応答します。

*Pages/BlazorRoute*:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a>ルートパラメーター

コンポーネントは、`@page` ディレクティブで指定されたルートテンプレートからルートパラメーターを受け取ることができます。 ルーターは、ルートパラメーターを使用して、対応するコンポーネントパラメーターを設定します。

*Pages/RouteParameter。 razor*:

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

省略可能なパラメーターはサポートされていないため、上記の例では2つの `@page` ディレクティブが適用されます。 最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。 2番目の `@page` ディレクティブは、`{text}` route パラメーターを受け取り、その値を `Text` プロパティに割り当てます。

複数のフォルダー境界をまたいでパスをキャプチャする*キャッチオール*パラメーター構文 (`*`/`**`) は、razor コンポーネント (*razor*) ではサポートされて**いません**。

::: moniker range=">= aspnetcore-3.1"

## <a name="partial-class-support"></a>部分クラスのサポート

Razor コンポーネントは部分クラスとして生成されます。 Razor コンポーネントは、次のいずれかの方法を使用して作成されます。

* C#コードは、1つのファイル内に HTML マークアップと Razor コードを含む[`@code`](xref:mvc/views/razor#code)ブロックで定義されます。 Blazor テンプレートでは、この方法を使用して Razor コンポーネントを定義します。
* C#コードは、部分クラスとして定義されている分離コードファイルに配置されます。

次の例は、Blazor テンプレートから生成されたアプリで `@code` ブロックを持つ既定の `Counter` コンポーネントを示しています。 HTML マークアップ、Razor コード、 C#およびコードは、同じファイル内にあります。

*Counter。 razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

`Counter` コンポーネントは、部分クラスの分離コードファイルを使用して作成することもできます。

*Counter。 razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

## <a name="specify-a-component-base-class"></a>コンポーネントの基本クラスを指定する

`@inherits` ディレクティブは、コンポーネントの基底クラスを指定するために使用できます。

この[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)は、コンポーネントが基底クラス `BlazorRocksBase`を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。

*Pages/BlazorRocks*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

基底クラスは `ComponentBase`から派生する必要があります。

::: moniker-end

## <a name="import-components"></a>コンポーネントのインポート

Razor で作成されるコンポーネントの名前空間は、(優先順位に従って) に基づきます。

* Razor ファイル (*razor*) マークアップ (`@namespace BlazorSample.MyNamespace`) の[`@namespace`](xref:mvc/views/razor#namespace)指定。
* プロジェクトファイル内のプロジェクトの `RootNamespace` (`<RootNamespace>BlazorSample</RootNamespace>`)。
* プロジェクトファイルのファイル名 ( *.csproj*) から取得されたプロジェクト名、およびプロジェクトルートからコンポーネントへのパス。 たとえば、フレームワークは *{PROJECT ROOT}/* *BlazorSample (* ) を名前空間 `BlazorSample.Pages`に解決します。 コンポーネントはC# 、名前のバインド規則に従います。 この例の `Index` コンポーネントの場合、スコープ内のコンポーネントはすべてコンポーネントです。
  * 同じフォルダー内の*ページ*。
  * 別の名前空間を明示的に指定していない、プロジェクトのルート内のコンポーネント。

別の名前空間で定義されているコンポーネントは、Razor の[`@using`](xref:mvc/views/razor#using)ディレクティブを使用してスコープ内に置かれます。

*BlazorSample/Shared/* フォルダーに別のコンポーネント (`NavMenu.razor`) が存在する場合は、次の `@using` ステートメントを使用して `Index.razor` でコンポーネントを使用できます。

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

コンポーネントは、完全修飾名を使用して参照することもできます。この場合、 [`@using`](xref:mvc/views/razor#using)ディレクティブは必要ありません。

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` の修飾はサポートされていません。
>
> エイリアス付き `using` ステートメント (`@using Foo = Bar`など) を含むコンポーネントのインポートはサポートされていません。
>
> 部分修飾名はサポートされていません。 たとえば、`<Shared.NavMenu></Shared.NavMenu>` での `@using BlazorSample` の追加と `NavMenu.razor` の参照はサポートされていません。

## <a name="conditional-html-element-attributes"></a>条件付き HTML 要素の属性

HTML 要素の属性は、.NET の値に基づいて条件付きで表示されます。 値が `false` または `null`の場合、属性はレンダリングされません。 値が `true`場合は、属性が最小化されて表示されます。

次の例では、`IsCompleted` によって `checked` が要素のマークアップに表示されるかどうかが決まります。

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted` が `true`場合、このチェックボックスは次のように表示されます。

```html
<input type="checkbox" checked />
```

`IsCompleted` が `false`場合、このチェックボックスは次のように表示されます。

```html
<input type="checkbox" />
```

詳細については、「<xref:mvc/views/razor>」を参照してください。

> [!WARNING]
> [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)などの一部の HTML 属性は、.net 型が `bool`の場合、正しく機能しません。 そのような場合は、`bool`ではなく `string` の型を使用します。

## <a name="raw-html"></a>未加工の HTML

通常、文字列は DOM テキストノードを使用してレンダリングされます。つまり、含まれているすべてのマークアップは無視され、リテラルテキストとして扱われます。 生の HTML をレンダリングするには、`MarkupString` 値で HTML コンテンツをラップします。 値は HTML または SVG として解析され、DOM に挿入されます。

> [!WARNING]
> 信頼できないソースから構築された生の HTML をレンダリングすることは、**セキュリティ上のリスク**があるため、回避する必要があります。

次の例では、`MarkupString` 型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加します。

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>テンプレートコンポーネント

テンプレートコンポーネントは、1つまたは複数の UI テンプレートをパラメーターとして受け取り、コンポーネントのレンダリングロジックの一部として使用できるコンポーネントです。 テンプレート化されたコンポーネントを使用すると、通常のコンポーネントよりも再利用しやすい上位レベルのコンポーネントを作成できます。 いくつかの例を次に示します。

* テーブルのヘッダー、行、およびフッターのテンプレートをユーザーが指定できるようにするテーブルコンポーネント。
* リスト内の項目を表示するためのテンプレートをユーザーが指定できるようにするリストコンポーネント。

### <a name="template-parameters"></a>テンプレート パラメーター

`RenderFragment` または `RenderFragment<T>`型の1つ以上のコンポーネントパラメーターを指定して、テンプレート化されたコンポーネントを定義します。 レンダーフラグメントは、レンダリングする UI のセグメントを表します。 `RenderFragment<T>` は、レンダリングフラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。

`TableTemplate` コンポーネント:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

テンプレート化されたコンポーネントを使用する場合、テンプレートパラメーターは、パラメーターの名前と一致する子要素を使用して指定できます (`TableHeader` と、次の例の `RowTemplate`)。

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a>テンプレートコンテキストパラメーター

要素として渡される型 `RenderFragment<T>` のコンポーネント引数には、`context` という名前の暗黙的なパラメーターがあります (たとえば、上記のコードサンプルでは `@context.PetId`)。ただし、子要素の `Context` 属性を使用してパラメーター名を変更することもできます。 次の例では、`RowTemplate` 要素の `Context` 属性で `pet` パラメーターを指定しています。

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

または、コンポーネント要素の `Context` 属性を指定することもできます。 指定された `Context` 属性は、指定されたすべてのテンプレートパラメーターに適用されます。 これは、暗黙的な子コンテンツ (ラップする子要素を持たない) のコンテンツパラメーター名を指定する場合に便利です。 次の例では、`Context` 属性が `TableTemplate` 要素に表示され、すべてのテンプレートパラメーターに適用されます。

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a>汎用型のコンポーネント

多くの場合、テンプレート化されたコンポーネントは一般的に型指定されます たとえば、汎用 `ListViewTemplate` コンポーネントを使用して `IEnumerable<T>` 値を表示できます。 ジェネリックコンポーネントを定義するには、 [`@typeparam`](xref:mvc/views/razor#typeparam)ディレクティブを使用して型パラメーターを指定します。

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。 次の例では、`TItem="Pet"` 型を指定しています。

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>カスケード値とパラメーター

場合によっては、[コンポーネントパラメーター](#component-parameters)を使用して先祖コンポーネントから子孫コンポーネントにデータをフローするのは不便です。コンポーネントレイヤーが複数ある場合は特にそうです。 カスケード値およびパラメーターは、先祖コンポーネントがすべての子孫コンポーネントに値を提供するのに便利な方法を提供することで、この問題を解決します。 カスケード値とパラメーターは、コンポーネントでコーディネートする方法も提供します。

### <a name="theme-example"></a>テーマの例

次のサンプルアプリの例では、`ThemeInfo` クラスが、コンポーネント階層の下位にあるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。

*UiThemeInfo/* :

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

先祖コンポーネントでは、カスケード値コンポーネントを使用してカスケード値を指定できます。 `CascadingValue` コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供します。

たとえば、サンプルアプリでは、アプリのいずれかのレイアウトのテーマ情報 (`ThemeInfo`) を、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード型パラメーターとして指定します。 `ButtonClass` には、レイアウトコンポーネントで `btn-success` の値が割り当てられます。 すべての子孫コンポーネントは、`ThemeInfo` カスケードオブジェクトを介してこのプロパティを使用できます。

`CascadingValuesParametersLayout` コンポーネント:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

カスケード値を使用するために、コンポーネントは `[CascadingParameter]` 属性を使用してカスケード型パラメーターを宣言します。 カスケード値は、型によってカスケード型パラメーターにバインドされます。

サンプルアプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値がカスケード型パラメーターにバインドされます。 パラメーターは、コンポーネントによって表示されるボタンの1つに CSS クラスを設定するために使用されます。

`CascadingValuesParametersTheme` コンポーネント:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

同じサブツリー内で同じ型の複数の値を連鎖させるには、各 `CascadingValue` コンポーネントとそれに対応する `CascadingParameter`に一意の `Name` 文字列を指定します。 次の例では、2つの `CascadingValue` コンポーネントが名前で `MyCascadingType` の異なるインスタンスをカスケードしています。

```razor
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

子孫コンポーネントでは、カスケードされたパラメーターは、先祖コンポーネントの対応するカスケード値から名前で値を受け取ります。

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet の例

カスケード型パラメーターを使用すると、コンポーネント階層全体でコンポーネントを連携させることもできます。 たとえば、サンプルアプリで次の*Tabset*の例を考えてみます。

このサンプルアプリには、タブに実装されている `ITab` インターフェイスがあります。

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` コンポーネントは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

子 `Tab` コンポーネントは、パラメーターとして `TabSet`に明示的に渡されません。 代わりに、子 `Tab` コンポーネントは、`TabSet`の子コンテンツの一部になります。 ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントは、*それ自体をカスケード値として提供*し、その後、子孫 `Tab` コンポーネントによって取得されます。

`TabSet` コンポーネント:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

子孫の `Tab` コンポーネントは、含まれている `TabSet` をカスケード型パラメーターとしてキャプチャするので、`Tab` のコンポーネントは、どのタブがアクティブであるかを `TabSet` および座標に追加します。

`Tab` コンポーネント:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor テンプレート

レンダリングフラグメントは、Razor テンプレート構文を使用して定義できます。 Razor テンプレートは、UI スニペットを定義する方法であり、次の形式を想定しています。

```razor
@<{HTML tag}>...</{HTML tag}>
```

次の例は、`RenderFragment` と `RenderFragment<T>` の値を指定し、テンプレートをコンポーネント内で直接表示する方法を示しています。 レンダーフラグメントは、[テンプレートコンポーネント](#templated-components)に引数として渡すこともできます。

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

前のコードの出力をレンダリングします。

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>手動の RenderTreeBuilder ロジック

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` には、コンポーネントと要素を操作するためのメソッドがC#用意されています

> [!NOTE]
> コンポーネントの作成に `RenderTreeBuilder` を使用することは、高度なシナリオです。 形式が正しくないコンポーネント (たとえば、閉じていないマークアップタグなど) は、未定義の動作になる可能性があります。

次の `PetDetails` コンポーネントを検討してください。これは手動で別のコンポーネントに組み込むことができます。

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

次の例では、`CreateComponent` メソッドのループによって、3つの `PetDetails` コンポーネントが生成されます。 `RenderTreeBuilder` メソッドを呼び出してコンポーネント (`OpenComponent` と `AddAttribute`) を作成する場合、シーケンス番号はソースコードの行番号です。 Blazor の相違アルゴリズムは、個別の呼び出し呼び出しではなく、個別のコード行に対応するシーケンス番号に依存します。 `RenderTreeBuilder` メソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコーディングします。 **計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。** 詳細については、「[シーケンス番号はコード行番号に関連し、実行順序を](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)使用しない」セクションを参照してください。

`BuiltContent` コンポーネント:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> !要する`Microsoft.AspNetCore.Components.RenderTree` の型により、レンダリング操作の*結果*を処理できます。 これらは、Blazor framework 実装の内部的な詳細です。 これらの型は*不安定*であると見なされ、今後のリリースで変更される可能性があります。

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>シーケンス番号は、実行順序ではなくコード行番号に関連します

Blazor `.razor` ファイルは常にコンパイルされます。 コンパイル手順を使用すると、実行時にアプリのパフォーマンスを向上させる情報を挿入できるため、`.razor` にとって大きな利点があります。

これらの機能強化の主要な例には、*シーケンス番号*が含まれます。 シーケンス番号は、コードの特定の行と順序付けられた行の出力元をランタイムに示します。 ランタイムは、この情報を使用して、効率的なツリーの相違を線形時間で生成します。これは、一般的なツリーの相違アルゴリズムでは通常可能です。

次の Razor コンポーネント (*razor*) ファイルについて考えてみます。

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

上記のコードは、次のようにコンパイルされます。

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

コードを初めて実行するときに、`someFlag` が `true`場合、ビルダーは次のメッセージを受け取ります。

| シーケンス | の型      | Data   |
| :------: | --------- | :----: |
| 0        | テキスト ノード | First  |
| 1        | テキスト ノード | Second |

`someFlag` が `false`になり、マークアップが再び表示されるとします。 この時点で、ビルダーは次のものを受け取ります。

| シーケンス | の型       | Data   |
| :------: | ---------- | :----: |
| 1        | テキスト ノード  | Second |

ランタイムが diff を実行すると、シーケンス `0` の項目が削除されたことが認識されるため、次のような単純な*編集スクリプト*が生成されます。

* 最初のテキストノードを削除します。

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>プログラムによってシーケンス番号を生成した場合の問題

代わりに、次のレンダーツリービルダーのロジックを記述したとします。

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

これで、最初の出力は次のようになります。

| シーケンス | の型      | Data   |
| :------: | --------- | :----: |
| 0        | テキスト ノード | First  |
| 1        | テキスト ノード | Second |

この結果は前のケースと同じであるため、負の問題は存在しません。 2番目のレンダリングでは `someFlag` が `false`、出力は次のようになります。

| シーケンス | の型      | Data   |
| :------: | --------- | ------ |
| 0        | テキスト ノード | Second |

今回は、diff アルゴリズムによって*2 つ*の変更が行われたことが確認され、アルゴリズムによって次の編集スクリプトが生成されます。

* 最初のテキストノードの値を `Second`に変更します。
* 2番目のテキストノードを削除します。

シーケンス番号を生成すると、`if/else` 分岐とループが元のコードに存在する場所に関する有用な情報がすべて失われます。 これにより、diff は前の**2 倍**になります。

これは簡単な例です。 複雑で深く入れ子になった構造、特にループを使用したより現実的なケースでは、パフォーマンスコストが高くなります。 どのループブロックまたは分岐が挿入または削除されたかをすぐに識別するのではなく、diff アルゴリズムでは、レンダリングツリーに深く再帰する必要があります相互に関連付けられています。

#### <a name="guidance-and-conclusions"></a>ガイダンスと結論

* シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。
* コンパイル時にキャプチャされない場合、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。
* 手動で実装された `RenderTreeBuilder` ロジックの長いブロックは記述しないでください。 `.razor` ファイルを優先し、コンパイラがシーケンス番号を処理できるようにします。 手動の `RenderTreeBuilder` ロジックを回避できない場合は、長いブロックのコードを `OpenRegion`/`CloseRegion` 呼び出しでラップされた小さな部分に分割します。 各リージョンにはシーケンス番号の個別のスペースがあるため、各リージョン内のゼロ (または任意の任意の数) から再起動できます。
* シーケンス番号がハードコードされている場合、diff アルゴリズムでは、シーケンス番号の値を大きくする必要があります。 初期値とギャップは関係ありません。 正当な選択肢の1つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1または数百 (または任意の間隔) で増やすことです。 
* Blazor はシーケンス番号を使用しますが、他のツリー比較 UI フレームワークでは使用しません。 シーケンス番号を使用すると比較ははるかに高速になり*ます。* また、Blazor には、開発者が razor ファイルを作成するときにシーケンス番号を自動的に処理するコンパイル手順の利点があります。

## <a name="localization"></a>ローカリゼーション

Blazor サーバーアプリはローカライズ[ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。 ミドルウェアは、アプリからリソースを要求するユーザーに適切なカルチャを選択します。

カルチャは、次のいずれかの方法を使用して設定できます。

* [クッキー](#cookies)
* [カルチャを選択するための UI を提供する](#provide-ui-to-choose-the-culture)

使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a>国際化のためのリンカーの構成 (Blazor WebAssembly

既定では、Blazor WebAssembly に対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報は除去されます。 リンカーの動作を制御する方法の詳細とガイダンスについては、「<xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>」を参照してください。

### <a name="cookies"></a>Cookies

ローカリゼーションカルチャクッキーは、ユーザーのカルチャを保持できます。 Cookie は、アプリのホストページ (*Pages/host. cshtml. .cs*) の `OnGet` メソッドによって作成されます。 ローカリゼーションミドルウェアは、後続の要求で cookie を読み取り、ユーザーのカルチャを設定します。 

Cookie を使用すると、WebSocket 接続がカルチャを正しく伝達できるようになります。 ローカライズスキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが Websocket を使用できない可能性があるため、カルチャを永続化できません。 したがって、ローカライズカルチャ cookie を使用することをお勧めします。

カルチャがローカライズ cookie に保存されている場合は、任意の手法を使用してカルチャを割り当てることができます。 アプリにサーバー側 ASP.NET Core 用に確立されたローカライズスキームが既にある場合は、引き続きアプリの既存のローカライズインフラストラクチャを使用し、アプリのスキーム内でローカライズカルチャ cookie を設定します。

次の例では、ローカリゼーションミドルウェアが読み取ることができる cookie で現在のカルチャを設定する方法を示します。 Blazor Server アプリで次の内容を含む*ページ/ホストの cshtml*ファイルを作成します。

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

ローカライズはアプリで処理されます。

1. ブラウザーは、アプリに最初の HTTP 要求を送信します。
1. カルチャは、ローカリゼーションミドルウェアによって割り当てられます。
1. *_Host*の `OnGet` メソッドは、応答の一部として cookie 内のカルチャを永続化します。
1. ブラウザーは、WebSocket 接続を開き、対話型の Blazor サーバーセッションを作成します。
1. ローカリゼーションミドルウェアは cookie を読み取り、カルチャを割り当てます。
1. Blazor サーバーセッションは、正しいカルチャで開始されます。

### <a name="provide-ui-to-choose-the-culture"></a>カルチャを選択するための UI を提供する

ユーザーがカルチャを選択できるように UI を提供するには、*リダイレクトベースのアプローチ*を使用することをお勧めします。 このプロセスは、ユーザーがセキュリティで保護されたリソースにアクセスしようとしたときに、ユーザーがサインインページにリダイレクトされ、元のリソースにリダイレクトされる&mdash;、web アプリで発生する処理に似ています。 

アプリは、コントローラーへのリダイレクトによって、ユーザーが選択したカルチャを永続化します。 コントローラーは、ユーザーが選択したカルチャを cookie に設定し、ユーザーを元の URI にリダイレクトします。

Cookie でユーザーが選択したカルチャを設定し、元の URI へのリダイレクトを実行するために、サーバー上に HTTP エンドポイントを確立します。

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> `LocalRedirect` アクションの結果を使用して、開いているリダイレクト攻撃を防止します。 詳細については、「<xref:security/preventing-open-redirects>」を参照してください。

次のコンポーネントは、ユーザーがカルチャを選択したときに最初のリダイレクトを実行する方法の例を示しています。

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a>Blazor アプリで .NET ローカライズシナリオを使用する

Blazor アプリ内では、次の .NET ローカリゼーションとグローバリゼーションのシナリオを利用できます。

* .NET のリソースシステム
* カルチャ固有の数値と日付の書式設定

Blazorの `@bind` 機能は、ユーザーの現在のカルチャに基づいてグローバリゼーションを実行します。 詳細については、「[データバインディング](#data-binding)」を参照してください。

現在、次のような ASP.NET Core のローカライズシナリオがサポートされています。

* `IStringLocalizer<>` は Blazor アプリで*サポートされて*います。
* `IHtmlLocalizer<>`、`IViewLocalizer<>`、データ注釈のローカライズは MVC シナリオ ASP.NET Core、Blazor アプリではサポートされて**いません**。

詳細については、「<xref:fundamentals/localization>」を参照してください。

## <a name="scalable-vector-graphics-svg-images"></a>スケーラブルベクターグラフィックス (SVG) イメージ

Blazor は HTML をレンダリングするため、スケーラブルベクターグラフィックス (svg) イメージ (*svg*) を含むブラウザーでサポートされているイメージは、`<img>` タグを介してサポートされます。

```html
<img alt="Example image" src="some-image.svg" />
```

同様に、SVG イメージは、スタイルシートファイル ( *.css*) の css 規則でサポートされています。

```css
.my-element {
    background-image: url("some-image.svg");
}
```

ただし、インライン SVG マークアップは、すべてのシナリオでサポートされているわけではありません。 コンポーネントファイル (*razor*) に `<svg>` タグを直接配置した場合、基本的な画像レンダリングはサポートされますが、多くの高度なシナリオはサポートされていません。 たとえば、`<use>` タグは現在尊重されていないため、`@bind` をいくつかの SVG タグと共に使用することはできません。 今後のリリースでは、これらの制限に対処する予定です。

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/blazor/server> &ndash; には、リソース枯渇に対処する必要がある Blazor サーバーアプリを構築するためのガイダンスが含まれています。
