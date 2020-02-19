---
title: ASP.NET Core Blazor のイベント処理
author: guardrex
description: イベント引数の型、イベントのコールバック、既定のブラウザーイベントの管理など、Blazorのイベント処理シナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453164"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor のイベント処理

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

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

## <a name="event-argument-types"></a>イベント引数の型

イベントによっては、イベント引数の型が許可されます。 メソッド呼び出しでイベントの種類を指定する必要があるのは、イベントの種類がメソッドで使用されている場合のみです。

次の表に、サポートされている `EventArgs` を示します。

| Event            | クラス                | DOM のイベントとメモ |
| ---------------- | -------------------- | -------------------- |
| クリップボード        | `ClipboardEventArgs` | `oncut`、`oncopy`、`onpaste` |
| 抗力             | `DragEventArgs`      | `ondrag`､`ondragstart`、`ondragenter`、`ondragleave`、`ondragover`、`ondrop`、`ondragend`<br><br>`DataTransfer` および `DataTransferItem` ドラッグした項目データを保持します。 |
| エラー            | `ErrorEventArgs`     | `onerror` |
| Event            | `EventArgs`          | *全般*<br>`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`<br><br>*クリップボード*<br>`onbeforecut`、`onbeforecopy`、`onbeforepaste`<br><br>*入力*<br>`oninvalid`、`onreset`、`onselect`、`onselectionchange`、`onselectstart`、`onsubmit`<br><br>*メディア*<br>`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`の `onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、および `onsuspend``ontimeupdate``onvolumechange``onwaiting` |
| Focus            | `FocusEventArgs`     | `onfocus`、`onblur`、`onfocusin`, `onfocusout`<br><br>には `relatedTarget`のサポートは含まれていません。 |
| 入力            | `ChangeEventArgs`    | `onchange`, `oninput` |
| [キーボード]         | `KeyboardEventArgs`  | `onkeydown`、`onkeypress`、`onkeyup` |
| マウス            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| マウスポインター    | `PointerEventArgs`   | `onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture` |
| マウスホイール      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| 進行状況         | `ProgressEventArgs`  | `onabort`、`onload`、`onloadend`、`onloadstart`、`onprogress`、`ontimeout` |
| タッチ            | `TouchEventArgs`     | `ontouchstart`、`ontouchend`、`ontouchmove`、`ontouchenter`、`ontouchleave`、`ontouchcancel`<br><br>`TouchPoint` は、タッチを区別するデバイス上の1つの連絡先ポイントを表します。 |

詳細については、次のリソースを参照してください。

* [ASP.NET Core 参照ソースの EventArgs クラス (dotnet/aspnetcore release/3.1 分岐)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。
* [MDN web ドキュメント: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; には、各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。

## <a name="lambda-expressions"></a>ラムダ式

[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用することもできます。

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

多くの場合、要素のセットを反復処理するときなど、追加の値を終了すると便利です。 次の例では、3つのボタンを作成します。各ボタンは、UI で選択されたときに、イベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> ラムダ式では、`for` ループでループ変数 (`i`) を**直接使用しないでください。** それ以外の場合、すべてのラムダ式で同じ変数が使用され、`i`の値はすべてのラムダで同じになります。 常にローカル変数 (前の例では`buttonNumber`) で値をキャプチャし、それを使用します。

## <a name="eventcallback"></a>EventCallback

入れ子になったコンポーネントの一般的なシナリオとして、子コンポーネントのイベント&mdash;が発生したときに親コンポーネントのメソッドを実行することが望まれます。たとえば、子で `onclick` イベントが発生した場合などです。 コンポーネント間でイベントを公開するには、`EventCallback`を使用します。 親コンポーネントは、コールバックメソッドを子コンポーネントの `EventCallback`に割り当てることができます。

サンプルアプリ (*Components/ChildComponent. razor*) の `ChildComponent` は、ボタンの `onclick` ハンドラーが、サンプルの `ParentComponent`から `EventCallback` デリゲートを受け取るように設定されている方法を示しています。 `EventCallback` は `MouseEventArgs`で入力されます。これは、周辺機器の `onclick` イベントに適しています。

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` によって、子の `EventCallback<T>` (`OnClickCallback`) が `ShowMessage` メソッドに設定されます。

*Pages/ParentComponent。 razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

`ChildComponent`でボタンが選択されている場合:

* `ParentComponent`の `ShowMessage` メソッドが呼び出されます。 `_messageText` が更新され、`ParentComponent`に表示されます。
* このコールバックのメソッド (`ShowMessage`) では、 [Statehaschanged](xref:blazor/lifecycle#state-changes)の呼び出しは必要ありません。 `StateHasChanged` は、子イベントと同様に、子の内部で実行されるイベントハンドラーでコンポーネントの実行がトリガーされるのと同様に、`ParentComponent`をレンダリングするために自動的に呼び出されます。

`EventCallback` および `EventCallback<T>` 非同期デリゲートを許可します。 `EventCallback<T>` は厳密に型指定され、特定の引数型を必要とします。 `EventCallback` は弱く型指定され、任意の引数型を使用できます。

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

`InvokeAsync` で `EventCallback` または `EventCallback<T>` を呼び出し、<xref:System.Threading.Tasks.Task>を待機します。

```csharp
await callback.InvokeAsync(arg);
```

イベント処理とバインドコンポーネントのパラメーターには、`EventCallback` と `EventCallback<T>` を使用します。

厳密に型指定された `EventCallback<T>` `EventCallback`を優先します。 `EventCallback<T>` は、コンポーネントのユーザーに対してより適切なエラーフィードバックを提供します。 他の UI イベントハンドラーと同様に、イベントパラメーターの指定は省略可能です。 コールバックに値が渡されない場合は、`EventCallback` を使用します。

## <a name="prevent-default-actions"></a>既定のアクションを禁止する

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

## <a name="stop-event-propagation"></a>イベントの伝達の停止

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
