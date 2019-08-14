---
title: ASP.NET Core Razor コンポーネントを作成して使用する
author: guardrex
description: データにバインドする方法、イベントを処理する方法、コンポーネントのライフサイクルを管理する方法など、Razor コンポーネントを作成および使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/components
ms.openlocfilehash: a95c186d30eaf342f10ecbe6f7add242d4679a0f
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993413"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>ASP.NET Core Razor コンポーネントを作成して使用する

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

Blazor アプリは*コンポーネント*を使用して構築されます。 コンポーネントは、ページ、ダイアログ、フォームなどのユーザーインターフェイス (UI) の自己完結型のチャンクです。 コンポーネントには、HTML マークアップと、データを挿入したり UI イベントに応答したりするために必要な処理ロジックが含まれています。 コンポーネントは柔軟で軽量です。 入れ子にして再利用したり、プロジェクト間で共有したりすることができます。

## <a name="component-classes"></a>コンポーネントクラス

コンポーネントは、と HTML マークアップの C#組み合わせを使用して[razor](xref:mvc/views/razor)コンポーネントファイル (razor) に実装されます。 Blazor のコンポーネントは、正式に*Razor コンポーネント*として参照されます。

コンポーネントの名前は、大文字で始まる必要があります。 たとえば、 *MyCoolComponent*は有効で、 *MyCoolComponent*は無効です。

`_RazorComponentInclude` MSBuild プロパティを使用してファイルが Razor コンポーネントファイルとして識別されている限り、ファイル拡張子を使用してコンポーネントを作成できます。 たとえば、 *Pages*フォルダーにあるすべての*Cshtml*ファイルを Razor コンポーネントファイルとして処理するように指定するアプリを次に示します。

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

コンポーネントの UI は、HTML を使用して定義されます。 動的なレンダリング ロジック (たとえばループ、条件、式) が、[Razor](xref:mvc/views/razor) と呼ばれる埋め込みの C# 構文を使って追加されています。 アプリがコンパイルされると、HTML マークアップC#およびレンダリングロジックはコンポーネントクラスに変換されます。 生成されたクラスの名前は、ファイルの名前と一致します。

コンポーネント クラスのメンバーは、`@code` ブロック内で定義されています。 `@code`ブロックでは、コンポーネントの状態 (プロパティ、フィールド) は、イベント処理のメソッドまたはその他のコンポーネントロジックを定義するために指定されます。 複数の `@code` ブロックが許容されます。

> [!NOTE]
> ASP.NET Core 3.0 の以前のプレビューで`@functions`は、Razor コンポーネントのブロックと同じ`@code`目的にブロックが使用されていました。 `@functions`ブロックは`@code` Razor コンポーネントで引き続き機能しますが、ASP.NET Core 3.0 Preview 6 以降ではブロックを使用することをお勧めします。

コンポーネントメンバーは、でC# `@`始まる式を使用して、コンポーネントのレンダリングロジックの一部として使用できます。 たとえば、フィールド名C#にプレフィックス`@`を付けることで、フィールドが表示されます。 次の例では、が評価され、レンダリングされます。

* `_headingFontStyle`の`font-style`CSS プロパティ値。
* `_headingText``<h1>`要素の内容。

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

コンポーネントが最初にレンダリングされた後、コンポーネントはイベントに応答してレンダリングツリーを再生成します。 Blazor は、新しいレンダリングツリーを前のレンダリングツリーと比較し、ブラウザーのドキュメントオブジェクトモデル (DOM) に変更を適用します。

コンポーネントは通常C#のクラスであり、プロジェクト内の任意の場所に配置できます。 Web ページを生成するコンポーネントは、通常、[*ページ*] フォルダーにあります。 ページ以外のコンポーネントは、多くの場合、プロジェクトに追加された*共有*フォルダーまたはカスタムフォルダーに配置されます。 カスタムフォルダーを使用するには、カスタムフォルダーの名前空間を、親コンポーネントまたはアプリのインポートのいずれかの*razor*ファイルに追加します。 たとえば、次の名前空間は、アプリのルート名前空間が`WebApplication`である場合に、コンポーネントフォルダー内のコンポーネントを使用できるようにします。

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>コンポーネントを Razor Pages と MVC アプリに統合する

既存の Razor Pages および MVC アプリでコンポーネントを使用します。 Razor コンポーネントを使用するために既存のページやビューを書き直す必要はありません。 ページまたはビューが表示されると、コンポーネントは同時に prerendered されます。

ページまたはビューからコンポーネントを表示するには、 `RenderComponentAsync<TComponent>` HTML ヘルパーメソッドを使用します。

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。 コンポーネントでは、ビューおよびページ固有のシナリオ (部分ビューやセクションなど) を使用できません。 コンポーネントの部分ビューからロジックを使用するには、部分ビューのロジックをコンポーネントにします。

コンポーネントがどのようにレンダリングされ、コンポーネントの状態が Blazor サーバー側アプリで管理されるか<xref:blazor/hosting-models>の詳細については、「」を参照してください。

## <a name="use-components"></a>コンポーネントを使う

コンポーネントには、HTML 要素構文を使用して宣言することで、他のコンポーネントを含めることができます。 コンポーネントを使うためのマークアップは、そのコンポーネントの種類をタグ名とする HTML タグのようになります。

属性のバインドでは大文字と小文字が区別されます。 たとえば、 `@bind`は有効`@Bind`で、が無効です。

インデックスの次のマークアップは、インスタンス`HeadingComponent`をレンダリングし*ます。*

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Components/HeadingComponent*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

コンポーネント名と一致しない大文字の最初の文字を含む HTML 要素がコンポーネントに含まれている場合は、要素に予期しない名前が付いていることを示す警告が出力されます。 コンポーネントの名前空間にステートメントを追加すると、コンポーネントが使用可能になり、警告が削除されます。`@using`

## <a name="component-parameters"></a>コンポーネントのパラメーター

コンポーネントは、コンポーネントクラス`[Parameter]`のパブリックプロパティを使用して定義されているコンポーネントパラメーターを持つことができます。 マークアップ内でコンポーネントの引数を指定するには、属性を使います。

*Components/ChildComponent。 razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

次の例`ParentComponent`では、はの`Title` `ChildComponent`プロパティの値を設定します。

*Pages/ParentComponent。 razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>子コンテンツ

コンポーネントでは、別のコンポーネントのコンテンツを設定できます。 割り当てコンポーネントは、受信コンポーネントを指定するタグの間にコンテンツを提供します。

次の例`ChildComponent`では、にを`ChildContent`表す`RenderFragment`プロパティがあります。これは、レンダリングする UI のセグメントを表します。 の`ChildContent`値は、コンテンツをレンダリングする必要があるコンポーネントのマークアップに配置されます。 の`ChildContent`値は、親コンポーネントから受信され、ブートストラップパネルの`panel-body`内に表示されます。

*Components/ChildComponent。 razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> コンテンツを`RenderFragment`受け取るプロパティは、規約に`ChildContent`よって名前が付けられている必要があります。

次の`ParentComponent`例では、 `<ChildComponent>`タグ内`ChildComponent`にコンテンツを配置することで、を表示するためのコンテンツを提供できます。

*Pages/ParentComponent。 razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>属性スプラッティングと任意のパラメーター

コンポーネントは、コンポーネントの宣言されたパラメーターに加えて、追加の属性をキャプチャして表示できます。 追加の属性は、ディクショナリ内でキャプチャしてから、 [@attributes](xref:mvc/views/razor#attributes) Razor ディレクティブを使用してコンポーネントがレンダリングされるときに要素に splatted ことができます。 このシナリオは、さまざまなカスタマイズをサポートするマークアップ要素を生成するコンポーネントを定義する場合に便利です。 たとえば、多くのパラメーターをサポートするでは`<input>` 、属性を個別に定義するのは面倒な場合があります。

次の例では、最初`<input>`の要素`id="useIndividualParams"`() は個々のコンポーネントパラメーターを使用`<input>`し、`id="useAttributesDict"`2 番目の要素 () は属性スプラッティングを使用します。

```cshtml
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
            { "required", "true" },
            { "size", "50" }
        };
}
```

パラメーターの型は、文字列キー `IEnumerable<KeyValuePair<string, object>>`を使用して実装する必要があります。 この`IReadOnlyDictionary<string, object>`シナリオでは、を使用することもできます。

両方の`<input>`方法を使用して表示される要素は同じです。

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="true"
       size="50">
```

任意の属性を受け入れるには、プロパティが`[Parameter]` `CaptureUnmatchedValues`に`true`設定された属性を使用して、コンポーネントのパラメーターを定義します。

```cshtml
@code {
    [Parameter(CaptureUnmatchedAttributes = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`CaptureUnmatchedValues` の`[Parameter]`プロパティを使用すると、パラメーターは、他のどのパラメーターとも一致しないすべての属性と一致させることができます。 コンポーネントは、で`CaptureUnmatchedValues`1 つのパラメーターのみを定義できます。 で`CaptureUnmatchedValues`使用されるプロパティ型は、文字列`Dictionary<string, object>`キーを使用してから割り当て可能である必要があります。 `IEnumerable<KeyValuePair<string, object>>`また`IReadOnlyDictionary<string, object>`は、このシナリオのオプションでもあります。

## <a name="data-binding"></a>データ バインディング

コンポーネントと DOM 要素の両方に対するデータバインディングは、 [@bind](xref:mvc/views/razor#bind)属性を使用して実現されます。 次の例では`_italicsCheck` 、フィールドをチェックボックスの checked 状態にバインドします。

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

チェックボックスをオンまたはオフにすると、プロパティの値がそれぞれ`true`と`false`に更新されます。

このチェックボックスは、プロパティの値の変更に対する応答ではなく、コンポーネントがレンダリングされたときにのみ UI で更新されます。 イベントハンドラーのコードを実行した後にコンポーネントがレンダリングされるため、通常、プロパティの更新は UI に直ちに反映されます。

を`@bind` `CurrentValue`プロパティ(`<input @bind="CurrentValue" />`) と共に使用することは、基本的に次のようになります。

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

コンポーネントがレンダリングされると、 `value`入力要素のが`CurrentValue`プロパティから取得されます。 ユーザーがテキストボックス`onchange`に入力すると、イベントが発生`CurrentValue`し、プロパティは変更された値に設定されます。 実際には、では、型変換が実行さ`@bind`れるいくつかのケースが処理されるため、コード生成が少し複雑になります。 原則とし`@bind`て、は、式の現在の値`value`を属性に関連付け、登録されたハンドラーを使用して変更を処理します。

構文を使用し`onchange`た`@bind`イベント[@bind-value](xref:mvc/views/razor#bind)の処理に加えて、 `event`パラメーター ([@bind-value:event](xref:mvc/views/razor#bind)) を持つ属性を指定することで、プロパティまたはフィールドを他のイベントを使用してバインドできます。 次の例では`CurrentValue` 、 `oninput`イベントのプロパティをバインドします。

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

と`onchange`は異なり、要素がフォーカスを失った`oninput`ときに、テキストボックスの値が変更されたときに発生します。

**グローバリゼーション**

`@bind`値は、現在のカルチャの規則を使用して表示および解析するように書式設定されます。

現在のカルチャには、 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>プロパティからアクセスできます。

[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)は、次のフィールドの種類 (`<input type="{TYPE}" />`) に使用されます。

* `date`
* `number`

上記のフィールド型は次のとおりです。

* は、適切なブラウザーベースの書式規則を使用して表示されます。
* 自由形式のテキストを含めることはできません。
* ブラウザーの実装に基づいてユーザーの操作特性を指定します。

次のフィールド型には特定の書式要件があり、Blazor では現在サポートされていません。これは、すべての主要なブラウザーでサポートされていないためです。

* `datetime-local`
* `month`
* `week`

`@bind`値を解析および書式設定<xref:System.Globalization.CultureInfo?displayProperty=fullName>するためのを提供するパラメーターをサポートします。`@bind:culture` フィールド型`date`および`number`フィールド型を使用する場合は、カルチャを指定しないことをお勧めします。 `date`と`number`には、必要なカルチャを提供する組み込みの Blazor サポートが用意されています。

ユーザーのカルチャを設定する方法については、「[ローカリゼーション](#localization)」セクションを参照してください。

**書式指定文字列**

データバインディングは、 <xref:System.DateTime>を使用し[@bind:format](xref:mvc/views/razor#bind)て書式指定文字列で動作します。 通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

前のコードでは、 `<input>`要素のフィールド型 (`type`) は既定`text`でに設定されています。 `@bind:format`は、次の .NET 型のバインドに対してサポートされています。

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName> ですか。
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName> ですか。

属性`@bind:format`は、 `<input>`要素`value`のに適用する日付形式を指定します。 この形式は、 `onchange`イベントが発生したときに値を解析するためにも使用されます。

Blazor には日付を`date`書式設定するためのサポートが組み込まれているため、フィールドの種類の形式を指定することはお勧めしません。

**コンポーネントのパラメーター**

バインディング`@bind-{property}`はコンポーネントパラメーターを認識しますが、はコンポーネント間でプロパティ値をバインドできます。

次の子コンポーネント (`ChildComponent`) には`Year` 、コンポーネントの`YearChanged`パラメーターとコールバックがあります。

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>`については、「 [Eventcallback](#eventcallback) 」セクションを参照してください。

次の親コンポーネントは`ChildComponent`を使用し`ParentYear` 、親のパラメーターを子`Year`コンポーネントのパラメーターにバインドします。

```cshtml
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

を読み込む`ParentComponent`と、次のマークアップが生成されます。

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentYear`のボタン`ParentComponent`を選択`ChildComponent`してプロパティの値を変更すると、のプロパティが更新されます。`Year` の`Year`新しい値は、 `ParentComponent`が再度実行されたときに UI に表示されます。

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

パラメーター `Year`は、 `Year`パラメーターの型と一致する`YearChanged`コンパニオンイベントがあるため、バインド可能です。

慣例により`<ChildComponent @bind-Year="ParentYear" />` 、は基本的には書き込みに相当します。

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

一般に、プロパティは、属性を使用して`@bind-property:event` 、対応するイベントハンドラーにバインドできます。 たとえば、プロパティ`MyProp`は、次の2つ`MyEventHandler`の属性を使用してにバインドできます。

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>イベント処理

Razor コンポーネントは、イベント処理機能を提供します。 という名前`on{event}`の HTML 要素属性 (や`onsubmit`など`onclick` ) にデリゲート型の値を指定すると、Razor コンポーネントはその属性の値をイベントハンドラーとして扱います。 属性の名前は常に[ @on{event}](xref:mvc/views/razor#onevent)に設定されています。

次のコードは、 `UpdateHeading` UI でボタンが選択されたときにメソッドを呼び出します。

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

次のコードは、 `CheckChanged` UI でチェックボックスが変更されたときにメソッドを呼び出します。

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

イベントハンドラーを非同期にして、を<xref:System.Threading.Tasks.Task>返すこともできます。 を手動で呼び出す`StateHasChanged()`必要はありません。 例外は、発生するとログに記録されます。

次の例では`UpdateHeading` 、ボタンが選択されると、が非同期に呼び出されます。

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>イベント引数の型

イベントによっては、イベント引数の型が許可されます。 これらのイベントの種類のいずれかにアクセスする必要がない場合は、メソッドの呼び出しで必要とされません。

サポートされている[Uieventargs](https://github.com/aspnet/AspNetCore/blob/release/3.0-preview8/src/Components/Components/src/UIEventArgs.cs)を次の表に示します。

| イベント | クラス |
| ----- | ----- |
| クリップボードのトピック | `UIClipboardEventArgs` |
| 抗力  | `UIDragEventArgs`はドラッグアンドドロップ操作中にドラッグしたデータを保持するために使用され`UIDataTransferItem`、1つ以上のを保持できます。 &ndash; `DataTransfer` `UIDataTransferItem`1つのドラッグデータ項目を表します。 |
| Error | `UIErrorEventArgs` |
| フォーカス | `UIFocusEventArgs`には、の`relatedTarget`サポートは含まれていません。 &ndash; |
| `<input>` の変更 | `UIChangeEventArgs` |
| キーボード | `UIKeyboardEventArgs` |
| マウス | `UIMouseEventArgs` |
| マウスポインター | `UIPointerEventArgs` |
| マウスホイール | `UIWheelEventArgs` |
| 進行状況 | `UIProgressEventArgs` |
| タッチ | `UITouchEventArgs`&ndash; タッチ感度デバイス上の単一のコンタクト`UITouchPoint`ポイントを表します。 |

前の表に示したイベントのプロパティとイベント処理動作の詳細については、「[参照ソースの EventArgs クラス](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview8/src/Components/Web/src)」を参照してください。

### <a name="lambda-expressions"></a>ラムダ式

ラムダ式を使用することもできます。

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

多くの場合、要素のセットを反復処理するときなど、追加の値を終了すると便利です。 次の例では、3つの`UpdateHeading`ボタンを作成します。各ボタンは、UI で選択したときにイベント引数 (`UIMouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡します。

```cshtml
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

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> ループのループ変数 (`i`) `for`は、ラムダ式内で直接使用しないでください。 それ以外の場合は、すべて`i`のラムダ式で同じ変数が使用されます。これにより、の値がすべてのラムダで同じになります。 常にローカル変数 (`buttonNumber`前の例では) で値をキャプチャし、それを使用します。

### <a name="eventcallback"></a>EventCallback

入れ子になったコンポーネントの一般的なシナリオは、子コンポーネントのイベントが発生&mdash;したときに親コンポーネントのメソッドを実行することです。たとえば、子で`onclick`イベントが発生した場合などです。 コンポーネント間でイベントを公開するに`EventCallback`は、を使用します。 親コンポーネントは、コールバックメソッドを子コンポーネントの`EventCallback`に割り当てることができます。

サンプル`ChildComponent`アプリのは、サンプルの`ParentComponent`から`EventCallback`デリゲートを`onclick`受け取るように、ボタンのハンドラーがどのように設定されているかを示しています。 は`EventCallback` 、周辺機器`UIMouseEventArgs`からの`onclick`イベントに適したを使用して型指定されます。

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

は`ParentComponent` 、子の`EventCallback<T>`をその`ShowMessage`メソッドに設定します。

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

でボタンが選択されると`ChildComponent`、次のようになります。

* `ParentComponent`の`ShowMessage`メソッドが呼び出されます。 `messageText`が更新され、 `ParentComponent`に表示されます。
* コールバックの`StateHasChanged`メソッド (`ShowMessage`) では、の呼び出しは必要ありません。 `StateHasChanged`は、子イベントと同様`ParentComponent`に、子内で実行されるイベントハンドラーでコンポーネントのレンダリングがトリガーされるのと同じように、自動的に呼び出されます。

`EventCallback`非同期`EventCallback<T>`デリゲートを許可します。 `EventCallback<T>`は厳密に型指定され、特定の引数型を必要とします。 `EventCallback`は弱く型指定され、任意の引数型を許可します。

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

`EventCallback`または<xref:System.Threading.Tasks.Task>を使用`InvokeAsync`してを呼び出し、を`EventCallback<T>`待機します。

```csharp
await callback.InvokeAsync(arg);
```

イベント`EventCallback`処理`EventCallback<T>`およびバインドコンポーネントのパラメーターには、およびを使用します。

厳密に型指定`EventCallback<T>` `EventCallback`されたを優先します。 `EventCallback<T>`コンポーネントのユーザーに対して、より適切なエラーフィードバックを提供します。 他の UI イベントハンドラーと同様に、イベントパラメーターの指定は省略可能です。 コール`EventCallback`バックに値が渡されない場合は、を使用します。

## <a name="capture-references-to-components"></a>コンポーネントへの参照をキャプチャする

コンポーネント参照を使用すると、 `Show`や`Reset`などのコマンドをそのインスタンスに発行できるように、コンポーネントインスタンスを参照することができます。 コンポーネント参照をキャプチャするには、 [@ref](xref:mvc/views/razor#ref)子コンポーネントに属性を追加し、子コンポーネントと同じ名前と同じ型のフィールドを定義します。

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

コンポーネントがレンダリング`loginDialog`されると、 `MyLoginDialog`子コンポーネントのインスタンスがフィールドに設定されます。 その後、コンポーネントインスタンスで .NET メソッドを呼び出すことができます。

> [!IMPORTANT]
> 変数は、コンポーネントがレンダリングされた後にのみ設定され`MyLoginDialog` 、その出力には要素が含まれます。 `loginDialog` この時点までは、参照するものはありません。 コンポーネント参照のレンダリングが完了した後にコンポーネント参照を`OnAfterRenderAsync`操作`OnAfterRender`するには、メソッドまたはメソッドを使用します。

コンポーネント参照のキャプチャは、[要素参照のキャプチャ](xref:blazor/javascript-interop#capture-references-to-elements)に似た構文を使用しますが、 [JavaScript 相互運用](xref:blazor/javascript-interop)機能ではありません。 コンポーネント参照は、.net コードで&mdash;のみ使用されているため、JavaScript コードに渡されません。

> [!NOTE]
> 子コンポーネントの状態を変化**さ**せるためにコンポーネント参照を使用しないでください。 代わりに、通常の宣言型パラメーターを使用して、子コンポーネントにデータを渡します。 通常の宣言型パラメーターを使用すると、子コンポーネントが自動的にレンダリングされます。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>キー \@を使用して要素とコンポーネントの保存を制御する

要素またはコンポーネントのリストをレンダリングするときに、要素またはコンポーネントが変更された場合、Blazor の比較アルゴリズムは、前の要素またはコンポーネントを保持できるかどうか、およびモデルオブジェクトをどのようにマップするかを決定する必要があります。 通常、このプロセスは自動的に実行され、無視することができますが、プロセスの制御が必要になる場合があります。

次に例を示します。

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People`コレクションの内容は、挿入、削除、または順序変更されたエントリで変更される可能性があります。 コンポーネントのれ`<DetailsEditor>`時に、コンポーネントが異なる`Details`パラメーター値を受け取るように変更されることがあります。 これにより、予想よりも複雑な再リリースが発生する可能性があります。 場合によっては、要素のフォーカスの喪失など、表示される動作の違いが発生する可能性があります。

マッピングプロセスは、 `@key`ディレクティブ属性を使用して制御できます。 `@key`比較アルゴリズムによって、キーの値に基づいて要素またはコンポーネントの保持が保証されます。

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

コレクションが`People`変更されると、比較アルゴリズムによって`<DetailsEditor>`インスタンスと`person`インスタンスの間の関連付けが保持されます。

* が一覧から削除されると、対応する`<DetailsEditor>`インスタンスだけが UI から削除されます。 `People` `Person` その他のインスタンスは変更されません。
* がリスト内のある位置に挿入されると、その`<DetailsEditor>`位置に1つの新しいインスタンスが挿入されます。 `Person` その他のインスタンスは変更されません。
* エントリが順序変更されると、対応`<DetailsEditor>`するインスタンスが UI に保持され、順序が変更されます。 `Person`

場合によっては、 `@key`を使用すると、回避策の複雑さが最小限に抑えられ、DOM のステートフルな部分 (フォーカス位置など) での潜在的な問題を回避できます。

> [!IMPORTANT]
> キーは、各コンテナー要素またはコンポーネントに対してローカルです。 キーがドキュメント全体でグローバルに比較されることはありません。

### <a name="when-to-use-key"></a>キーを使用\@する場合

通常は、リストがレンダリングさ`@key`れるたびに (たとえば、 `@foreach`ブロックで) 使用し、を定義するための適切な`@key`値が存在することを意味します。

また、を使用`@key`して、オブジェクトが変更されたときに、Blazor が要素またはコンポーネントのサブツリーを保持しないようにすることもできます。

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

属性`@currentPerson`ディレクティブが変更`@key`された場合、Blazor は、 `<div>`とその子孫全体を破棄し、新しい要素とコンポーネントで UI 内のサブツリーを再構築するように強制します。 これは、変更時に`@currentPerson` UI 状態が保持されないことを保証する必要がある場合に役立ちます。

### <a name="when-not-to-use-key"></a>キーを使用\@しない場合

で`@key`比較すると、パフォーマンスコストが発生します。 パフォーマンスコストは大きくありませんが、 `@key`要素またはコンポーネントの保存ルールの制御がアプリにメリットをもたらすかどうかを指定するだけです。

が使用`@key`されていない場合でも、Blazor は子要素とコンポーネントインスタンスを可能な限り保持します。 を使用する唯一の`@key`利点は、マッピングを選択する比較アルゴリズムではなく、保持されているコンポーネントインスタンスにモデルインスタンスをマップする*方法*を制御することです。

### <a name="what-values-to-use-for-key"></a>キーに\@使用する値

一般に、には次のいずれかの値を指定するの`@key`が理にかなっています。

* モデルオブジェクトインスタンス ( `Person`たとえば、前の例のインスタンス)。 これにより、オブジェクト参照の等価性に基づいて保持されます。
* 一意の識別子 (たとえば、 `int` `string`、、または`Guid`型の主キーの値)。

に使用される値`@key`が競合しないようにしてください。 競合するの値が同じ親要素内で検出された場合、Blazor は、古い要素またはコンポーネントを新しい要素またはコンポーネントに確定的にマップできないため、例外をスローします。 個別の値 (オブジェクトインスタンスや主キー値など) のみを使用してください。

## <a name="lifecycle-methods"></a>ライフサイクル メソッド

`OnInitAsync`を`OnInit`実行し、コンポーネントを初期化するコードを実行します。 非同期操作を実行するには`OnInitAsync` 、操作`await`でおよびキーワードを使用します。

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

同期操作の場合は、 `OnInit`次のように使用します。

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync`コンポーネント`OnParametersSet`が親からパラメーターを受け取り、値がプロパティに割り当てられると、とが呼び出されます。 これらのメソッドは、コンポーネントの初期化の後、およびコンポーネントがレンダリングされるたびに実行されます。

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync`と`OnAfterRender`は、コンポーネントのレンダリングが完了した後に呼び出されます。 この時点で、要素参照とコンポーネント参照が設定されます。 レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を実行するには、この段階を使用します。

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>レンダー時の不完全な非同期アクションを処理する

ライフサイクルイベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。 ライフサイクルメソッド`null`の実行中に、オブジェクトがデータと共に読み込まれたり、不完全になったりする可能性があります。 オブジェクトが初期化されていることを確認するためのレンダリングロジックを提供します。 オブジェクトが`null`のときに、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。

Blazor テンプレートの`OnInitAsync` `forecasts`コンポーネントでは、はユーザー receive 予測データ () に上書きされます。 `FetchData` `forecasts` が`null`の場合、読み込みメッセージがユーザーに表示されます。 によっ`Task`て`OnInitAsync`返されたが完了すると、コンポーネントは更新された状態とピアリングされます。

*Pages/FetchData.razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>パラメーターが設定される前にコードを実行する

`SetParameters`をオーバーライドすると、パラメーターを設定する前にコードを実行できます。

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

が`base.SetParameters`呼び出されない場合、カスタムコードは、必要に応じて入力パラメーターの値を解釈できます。 たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。

### <a name="suppress-refreshing-of-the-ui"></a>UI の更新を抑制する

`ShouldRender`UI の更新を抑制するためにオーバーライドできます。 実装からが返さ`true`れた場合は、UI が更新されます。 がオーバーライド`ShouldRender`された場合でも、コンポーネントは常に最初に表示されます。

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>IDisposable によるコンポーネントの破棄

コンポーネントがを実装<xref:System.IDisposable>している場合は、コンポーネントが UI から削除されるときに[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。 次のコンポーネントで`@implements IDisposable`は、 `Dispose`メソッドとメソッドを使用します。

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>ルーティング

Blazor でのルーティングは、アプリ内のアクセス可能な各コンポーネントにルートテンプレートを提供することで実現されます。

`@page`ディレクティブを含む Razor ファイルがコンパイルされると、生成されたクラス<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>にルートテンプレートを指定するが与えられます。 実行時に、ルーターはを`RouteAttribute`使用してコンポーネントクラスを検索し、要求された URL に一致するルートテンプレートを持つコンポーネントをレンダリングします。

コンポーネントには、複数のルートテンプレートを適用できます。 次のコンポーネントは、と`/BlazorRoute` `/DifferentBlazorRoute`に対する要求に応答します。

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>ルートパラメーター

コンポーネントは、 `@page`ディレクティブで指定されたルートテンプレートからルートパラメーターを受け取ることができます。 ルーターは、ルートパラメーターを使用して、対応するコンポーネントパラメーターを設定します。

*ルートパラメーターコンポーネント*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

省略可能なパラメーターはサポートさ`@page`れていないため、上記の例では2つのディレクティブが適用されます。 最初のは、パラメーターを指定せずにコンポーネントへの移動を許可します。 2番`@page`目のディレクティブ`{text}`は route パラメーターを受け取り、その値`Text`をプロパティに割り当てます。

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>"分離コード" エクスペリエンスの基底クラスの継承

コンポーネントファイルは、HTML マークC#アップと処理コードを同じファイルに混在させます。 ディレクティブ`@inherits`を使用すると、コンポーネントマークアップを処理コードから分離する "分離コード" エクスペリエンスを Blazor アプリに提供できます。

この[サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)は、コンポーネントが基本クラス`BlazorRocksBase`を継承して、コンポーネントのプロパティとメソッドを提供する方法を示しています。

*Pages/BlazorRocks*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

基底クラスはから`ComponentBase`派生する必要があります。

## <a name="import-components"></a>コンポーネントのインポート

Razor を使用して作成されたコンポーネントの名前空間は、次のものに基づいています。

* プロジェクトの`RootNamespace`。
* プロジェクトルートからコンポーネントへのパス。 たとえば、 `ComponentsSample/Pages/Index.razor`は名前空間`ComponentsSample.Pages`にあります。 コンポーネントはC# 、名前のバインド規則に従います。 *インデックス*の場合、同じフォルダー、*ページ*、および親フォルダー内のすべてのコンポーネントがスコープに含まれます。

別の名前空間で定義されているコンポーネントは、Razor の[ \@using](xref:mvc/views/razor#using)ディレクティブを使用してスコープに入れることができます。

別のコンポーネント ( `NavMenu.razor`) がフォルダー `ComponentsSample/Shared/`に存在する場合は、次`@using`のステートメント`Index.razor`を使用してでコンポーネントを使用できます。

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

コンポーネントは、完全修飾名を使用して参照することもできます。これにより、 [ \@using](xref:mvc/views/razor#using)ディレクティブは不要になります。

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> この`global::`修飾はサポートされていません。
>
> エイリアス`using`付きステートメント (など) を使用し`@using Foo = Bar`たコンポーネントのインポートはサポートされていません。
>
> 部分修飾名はサポートされていません。 たとえば、を使用`@using ComponentsSample`した`NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>`の追加と参照はサポートされていません。

## <a name="conditional-html-element-attributes"></a>条件付き HTML 要素の属性

HTML 要素の属性は、.NET の値に基づいて条件付きで表示されます。 値がまたは`false` `null`の場合、属性はレンダリングされません。 値が`true`の場合、属性は最小化されて表示されます。

次の例では`IsCompleted` 、が`checked`要素のマークアップに表示されるかどうかを決定します。

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

`IsCompleted` が`true`の場合、このチェックボックスは次のように表示されます。

```html
<input type="checkbox" checked />
```

`IsCompleted` が`false`の場合、このチェックボックスは次のように表示されます。

```html
<input type="checkbox" />
```

詳細については、「 <xref:mvc/views/razor> 」を参照してください。

## <a name="raw-html"></a>未加工の HTML

通常、文字列は DOM テキストノードを使用してレンダリングされます。つまり、含まれているすべてのマークアップは無視され、リテラルテキストとして扱われます。 生の html をレンダリングするには、html コンテンツ`MarkupString`を値にラップします。 値は HTML または SVG として解析され、DOM に挿入されます。

> [!WARNING]
> 信頼できないソースから構築された生の HTML をレンダリングすることは、**セキュリティ上のリスク**があるため、回避する必要があります。

次の例では、 `MarkupString`型を使用して、コンポーネントのレンダリングされた出力に静的 HTML コンテンツのブロックを追加しています。

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

テンプレートコンポーネントは、型または`RenderFragment` `RenderFragment<T>`型の1つ以上のコンポーネントパラメーターを指定することによって定義されます。 レンダーフラグメントは、レンダリングする UI のセグメントを表します。 `RenderFragment<T>`は、render フラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。

`TableTemplate`成分

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

テンプレート化されたコンポーネントを使用する場合、テンプレートパラメーターは、パラメーターの名前 (`TableHeader`および`RowTemplate`次の例では) と一致する子要素を使用して指定できます。

```cshtml
<TableTemplate Items="@pets">
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

要素として`RenderFragment<T>`渡された型のコンポーネント引数に`context`は、という名前の暗黙的なパラメーター `@context.PetId`があります (上記のコードサンプルの例で`Context`は)。ただし、子の属性を使用してパラメーター名を変更できます。element. 次の例では、 `RowTemplate`要素の`Context`属性によっ`pet`てパラメーターが指定されています。

```cshtml
<TableTemplate Items="@pets">
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

または、コンポーネント要素で`Context`属性を指定することもできます。 指定された属性は、指定されたすべてのテンプレートパラメーターに適用されます。`Context` これは、暗黙的な子コンテンツ (ラップする子要素を持たない) のコンテンツパラメーター名を指定する場合に便利です。 次の例`Context`では、属性が`TableTemplate`要素に表示され、すべてのテンプレートパラメーターに適用されます。

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

多くの場合、テンプレート化されたコンポーネントは一般的に型指定されます たとえば、ジェネリック`ListViewTemplate`コンポーネントを使用して値を表示`IEnumerable<T>`できます。 ジェネリックコンポーネントを定義するには、 `@typeparam`ディレクティブを使用して型パラメーターを指定します。

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。 次の例では`TItem="Pet"` 、型を指定します。

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>カスケード値とパラメーター

場合によっては、[コンポーネントパラメーター](#component-parameters)を使用して先祖コンポーネントから子孫コンポーネントにデータをフローするのは不便です。コンポーネントレイヤーが複数ある場合は特にそうです。 カスケード値およびパラメーターは、先祖コンポーネントがすべての子孫コンポーネントに値を提供するのに便利な方法を提供することで、この問題を解決します。 カスケード値とパラメーターは、コンポーネントでコーディネートする方法も提供します。

### <a name="theme-example"></a>テーマの例

次のサンプルアプリの例では、クラス`ThemeInfo`は、コンポーネント階層の下位にあるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。

*UiThemeInfo/* :

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

先祖コンポーネントでは、カスケード値コンポーネントを使用してカスケード値を指定できます。 コンポーネント`CascadingValue`は、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を提供します。

たとえば、サンプルアプリでは、`ThemeInfo` `@Body`プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード型パラメーターとして、アプリのいずれかのレイアウトでテーマ情報 () を指定します。 `ButtonClass`レイアウトコンポーネントにの`btn-success`値が割り当てられています。 すべての子孫コンポーネントは、 `ThemeInfo`カスケードオブジェクトを介してこのプロパティを使用できます。

`CascadingValuesParametersLayout`成分

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
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

カスケード値を使用するために、コンポーネントは、 `[CascadingParameter]`属性を使用するか文字列名の値に基づいて、カスケード型パラメーターを宣言します。

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

同じ型のカスケード値が複数あり、同じサブツリー内で区別する必要がある場合は、文字列名値を使用したバインドが関係します。

カスケード値は、型によってカスケード型パラメーターにバインドされます。

サンプルアプリ`CascadingValuesParametersTheme`では、カスケード値が`ThemeInfo`カスケード型パラメーターにバインドされます。 パラメーターは、コンポーネントによって表示されるボタンの1つに CSS クラスを設定するために使用されます。

`CascadingValuesParametersTheme`成分

```cshtml
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

### <a name="tabset-example"></a>TabSet の例

カスケード型パラメーターを使用すると、コンポーネント階層全体でコンポーネントを連携させることもできます。 たとえば、サンプルアプリで次の*Tabset*の例を考えてみます。

このサンプルアプリには`ITab` 、タブによって実装されるインターフェイスがあります。

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

コンポーネント`CascadingValuesParametersTabSet`は、次`TabSet`のコンポーネントを含む`Tab`コンポーネントを使用します。

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

子`Tab`コンポーネントは、 `TabSet`パラメーターとしてに明示的に渡されません。 代わりに、子`Tab`コンポーネントはの子コンテンツ`TabSet`の一部になります。 ただし、で`TabSet`は、ヘッダーとアクティブな`Tab`タブをレンダリングできるように、各コンポーネントについて認識する必要があります。追加のコードを必要とせずにこの`TabSet`調整を可能にするために、コンポーネントは、子孫`Tab`コンポーネントによって取得される*カスケード値としてそれ自体を提供でき*ます。

`TabSet`成分

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

子孫`Tab`コンポーネントは、を含む`TabSet`をカスケード型パラメーターとして`Tab`キャプチャします。これにより、コンポーネントは、 `TabSet`どのタブがアクティブであるかを座標に追加します。

`Tab`成分

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor テンプレート

レンダリングフラグメントは、Razor テンプレート構文を使用して定義できます。 Razor テンプレートは、UI スニペットを定義する方法であり、次の形式を想定しています。

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

次の例は、と`RenderFragment` `RenderFragment<T>`の値を指定し、テンプレートをコンポーネント内で直接表示する方法を示しています。 レンダーフラグメントは、[テンプレートコンポーネント](#templated-components)に引数として渡すこともできます。

```cshtml
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

`Microsoft.AspNetCore.Components.RenderTree`コンポーネントと要素を操作するためのメソッドを提供しますC# 。これには、コードでのコンポーネントの手動作成も含まれます。

> [!NOTE]
> を使用してコンポーネントを作成することは高度なシナリオです。`RenderTreeBuilder` 形式が正しくないコンポーネント (たとえば、閉じていないマークアップタグなど) は、未定義の動作になる可能性があります。

別のコンポーネント`PetDetails`に手動で組み込むことができる次のコンポーネントについて考えてみます。

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

次の例では、 `CreateComponent`メソッド内のループによって3つ`PetDetails`のコンポーネントが生成されます。 メソッドを`RenderTreeBuilder`呼び出してコンポーネント (`OpenComponent`および`AddAttribute`) を作成する場合、シーケンス番号はソースコードの行番号です。 Blazor の相違アルゴリズムは、個別の呼び出し呼び出しではなく、個別のコード行に対応するシーケンス番号に依存します。 メソッドを使用して`RenderTreeBuilder`コンポーネントを作成する場合は、シーケンス番号の引数をハードコーディングします。 **計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。** 詳細については、「[シーケンス番号はコード行番号に関連し、実行順序を](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)使用しない」セクションを参照してください。

`BuiltContent`成分

```cshtml
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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>シーケンス番号は、実行順序ではなくコード行番号に関連します

Blazor `.razor`ファイルは常にコンパイルされます。 コンパイル手順を使用すると、 `.razor`実行時にアプリのパフォーマンスを向上させる情報を挿入できるため、これはにとって大きな利点となる可能性があります。

これらの機能強化の主要な例には、*シーケンス番号*が含まれます。 シーケンス番号は、コードの特定の行と順序付けられた行の出力元をランタイムに示します。 ランタイムは、この情報を使用して、効率的なツリーの相違を線形時間で生成します。これは、一般的なツリーの相違アルゴリズムでは通常可能です。

次の単純な`.razor`ファイルについて考えてみます。

```cshtml
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

コードを初めて実行するときは、が`someFlag` `true`の場合、ビルダーは次のメッセージを受け取ります。

| シーケンス | 種類      | データ   |
| :------: | --------- | :----: |
| 0        | テキスト ノード | First  |
| 1        | テキスト ノード | Second |

がに`someFlag`なり`false`、マークアップが再び表示されるとします。 この時点で、ビルダーは次のものを受け取ります。

| シーケンス | 種類       | データ   |
| :------: | ---------- | :----: |
| 1        | テキスト ノード  | Second |

ランタイムが diff を実行すると、シーケンス`0`の項目が削除されたことが認識されるため、次のような単純な*編集スクリプト*が生成されます。

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

| シーケンス | 種類      | データ   |
| :------: | --------- | :----: |
| 0        | テキスト ノード | First  |
| 1        | テキスト ノード | Second |

この結果は前のケースと同じであるため、負の問題は存在しません。 `someFlag`2番目のレンダリングで、出力は次のようになります。`false`

| シーケンス | 種類      | データ   |
| :------: | --------- | ------ |
| 0        | テキスト ノード | Second |

今回は、diff アルゴリズムによって*2 つ*の変更が行われたことが確認され、アルゴリズムによって次の編集スクリプトが生成されます。

* 最初のテキストノードの値をに`Second`変更します。
* 2番目のテキストノードを削除します。

シーケンス番号を生成すると、元のコードに分岐と`if/else`ループが存在する場所に関する有用な情報がすべて失われます。 これにより、diff は前の**2 倍**になります。

これは簡単な例です。 複雑で深く入れ子になった構造、特にループを使用したより現実的なケースでは、パフォーマンスコストが高くなります。 どのループブロックまたは分岐が挿入または削除されたかをすぐに識別するのではなく、diff アルゴリズムでは、レンダリングツリーに深く再帰する必要があります相互に関連付けられています。

#### <a name="guidance-and-conclusions"></a>ガイダンスと結論

* シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。
* コンパイル時にキャプチャされない場合、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。
* 手動で実装`RenderTreeBuilder`されたロジックの長いブロックは記述しないでください。 ファイル`.razor`を優先し、コンパイラがシーケンス番号を処理できるようにします。
* シーケンス番号がハードコードされている場合、diff アルゴリズムでは、シーケンス番号の値を大きくする必要があります。 初期値とギャップは関係ありません。 正当な選択肢の1つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1または数百 (または任意の間隔) で増やすことです。 
* Blazor はシーケンス番号を使用しますが、他のツリー比較 UI フレームワークでは使用しません。 シーケンス番号を使用すると、比較ははるかに高速になります。 Blazor には、開発者がファイルを作成`.razor`するときにシーケンス番号を自動的に処理するコンパイル手順の利点があります。

## <a name="localization"></a>ローカリゼーション

Blazor サーバー側アプリはローカライズ[ミドルウェア](xref:fundamentals/localization#localization-middleware)を使用してローカライズされます。 ミドルウェアは、アプリからリソースを要求するユーザーに適切なカルチャを選択します。

カルチャは、次のいずれかの方法を使用して設定できます。

* [クッキー](#cookies)
* [カルチャを選択するための UI を提供する](#provide-ui-to-choose-the-culture)

使用例を含む詳細については、「<xref:fundamentals/localization>」を参照してください。

### <a name="cookies"></a>クッキー

ローカリゼーションカルチャクッキーは、ユーザーのカルチャを保持できます。 Cookie は、アプリのホスト`OnGet`ページ (*Pages/host. cshtml. .cs*) のメソッドによって作成されます。 ローカリゼーションミドルウェアは、後続の要求で cookie を読み取り、ユーザーのカルチャを設定します。 

Cookie を使用すると、WebSocket 接続がカルチャを正しく伝達できるようになります。 ローカライズスキームが URL パスまたはクエリ文字列に基づいている場合は、スキームが Websocket を使用できない可能性があるため、カルチャを永続化できません。 したがって、ローカライズカルチャ cookie を使用することをお勧めします。

カルチャがローカライズ cookie に保存されている場合は、任意の手法を使用してカルチャを割り当てることができます。 アプリにサーバー側 ASP.NET Core 用に確立されたローカライズスキームが既にある場合は、引き続きアプリの既存のローカライズインフラストラクチャを使用し、アプリのスキーム内でローカライズカルチャ cookie を設定します。

次の例では、ローカリゼーションミドルウェアが読み取ることができる cookie で現在のカルチャを設定する方法を示します。 Blazor サーバー側アプリに次の内容を含む*ページ/ホストの cshtml*ファイルを作成します。

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
1. _Host `OnGet`のメソッドは、応答の一部として cookie 内のカルチャを永続化します。
1. ブラウザーは WebSocket 接続を開き、対話型の Blazor サーバー側セッションを作成します。
1. ローカリゼーションミドルウェアは cookie を読み取り、カルチャを割り当てます。
1. Blazor のサーバー側セッションは、正しいカルチャで開始されます。

## <a name="provide-ui-to-choose-the-culture"></a>カルチャを選択するための UI を提供する

ユーザーがカルチャを選択できるように UI を提供するには、*リダイレクトベースのアプローチ*を使用することをお勧めします。 このプロセスは、ユーザーがセキュリティで保護されたリソース&mdash;にアクセスしようとしたときに、ユーザーがサインインページにリダイレクトされ、元のリソースにリダイレクトされるという点に似ています。 

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
> アクションの`LocalRedirect`結果を使用して、開いているリダイレクト攻撃を防止します。 詳細については、「 <xref:security/preventing-open-redirects> 」を参照してください。

次のコンポーネントは、ユーザーがカルチャを選択したときに最初のリダイレクトを実行する方法の例を示しています。

```cshtml
@inject IUriHelper UriHelper

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(UIChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(UriHelper.GetAbsoluteUri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        UriHelper.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>Blazor アプリで .NET ローカライズシナリオを使用する

Blazor アプリ内では、次の .NET ローカリゼーションとグローバリゼーションのシナリオを利用できます。

* .NET のリソースシステム
* カルチャ固有の数値と日付の書式設定

Blazor の`@bind`機能は、ユーザーの現在のカルチャに基づいてグローバリゼーションを実行します。 詳細については、「[データバインディング](#data-binding)」を参照してください。

現在、次のような ASP.NET Core のローカライズシナリオがサポートされています。

* `IStringLocalizer<>`は、Blazor アプリで*サポートされて*います。
* `IHtmlLocalizer<>`、 `IViewLocalizer<>`、およびデータ注釈のローカライズは、Blazor アプリではサポートされて**いない**MVC シナリオ ASP.NET Core ます。

詳細については、「 <xref:fundamentals/localization> 」を参照してください。
