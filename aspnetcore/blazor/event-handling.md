---
title: ASP.NET Core Blazor のイベント処理
author: guardrex
description: イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511367"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core Blazor のイベント処理

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)

Razor コンポーネントでは、イベント処理機能が提供されます。 [`@on{EVENT}`](xref:mvc/views/razor#onevent) という名前の HTML 要素属性 (`@onclick` など) でデリゲート型の値がある場合、Razor コンポーネントでは、属性の値がイベント ハンドラーとして扱われます。

次のコードでは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。

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

次のコードでは、UI でチェック ボックスが変更されたときに `CheckChanged` メソッドを呼び出します。

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

イベント ハンドラーは、非同期にして <xref:System.Threading.Tasks.Task> を返すこともできます。 [StateHasChanged](xref:blazor/lifecycle#state-changes) を手動で呼び出す必要はありません。 例外は、発生時にログに記録されます。

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

イベントによっては、イベント引数の型を選択できます。 メソッド呼び出しでイベント型を指定する必要があるのは、そのイベント型がメソッド内で使用されている場合のみです。

サポートされている `EventArgs` を次の表に示します。

| event            | クラス                | DOM のイベントとメモ |
| ---------------- | -------------------- | -------------------- |
| クリップボードのトピック        | `ClipboardEventArgs` | `oncut`、`oncopy`、`onpaste` |
| ドラッグ             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` および `DataTransferItem` では、ドラッグされた項目データを保持します。 |
| Error            | `ErrorEventArgs`     | `onerror` |
| event            | `EventArgs`          | *全般*<br>`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`<br><br>*クリップボード*<br>`onbeforecut`、`onbeforecopy`、`onbeforepaste`<br><br>*入力*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*メディア*<br>`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting` |
| フォーカス            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>`relatedTarget` のサポートは含まれません。 |
| 入力            | `ChangeEventArgs`    | `onchange`、`oninput` |
| キーボード         | `KeyboardEventArgs`  | `onkeydown`、`onkeypress`、`onkeyup` |
| マウス            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| マウス ポインター    | `PointerEventArgs`   | `onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture` |
| マウス ホイール      | `WheelEventArgs`     | `onwheel`、`onmousewheel` |
| 進行状況         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| タッチ            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` は、タッチを検知するデバイス上の単一接触点を表します。 |

詳細については、次のリソースを参照してください。

* [ASP.NET Core 参照ソースの EventArgs クラス (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。
* [MDN Web ドキュメント:GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。

## <a name="lambda-expressions"></a>ラムダ式

[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)も使用できます。

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

要素のセットを反復処理するときなど、追加の値に集中すると便利な場合がよくあります。 次の例では 3 つのボタンを作成しています。各ボタンは、UI で選択されたときにイベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。

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
> ラムダ式では、`for` ループ内で直接、ループ変数 (`i`) を使用**しない**でください。 そうしないと、すべてのラムダ式で同じ変数が使用され、`i` の値はすべてのラムダで同じになります。 値は常にローカル変数 (前の例では`buttonNumber`) でキャプチャしてから、それを使用します。

## <a name="eventcallback"></a>EventCallback

入れ子になったコンポーネントがある一般的なシナリオでは、子で `onclick` イベントが発生したときなど、子コンポーネントのイベント&mdash;が発生したときに親コンポーネントのメソッドを実行する必要があります。 コンポーネント間にわたってイベントを公開するには、`EventCallback` を使用します。 親コンポーネントでは、コールバック メソッドを子コンポーネントの `EventCallback` に割り当てることができます。

サンプル アプリの `ChildComponent` (*Components/ChildComponent.razor*) は、ボタンの `onclick` ハンドラーがどのように、サンプルの `ParentComponent` から `EventCallback` デリゲートを受け取るように設定されているかを示しています。 `EventCallback` は `MouseEventArgs` によって型指定されます。これは、周辺機器の `onclick` イベントに適しています。

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` では、子の `EventCallback<T>` (`OnClickCallback`) を `ShowMessage` メソッドに設定しています。

*Pages/ParentComponent.razor*:

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

`ChildComponent` でボタンが選択されると:

* `ParentComponent` の `ShowMessage` メソッドが呼び出されます。 `_messageText` が更新されて、`ParentComponent` に表示されます。
* コールバックのメソッド (`ShowMessage`) 内に、[StateHasChanged](xref:blazor/lifecycle#state-changes) の呼び出しは必要ありません。 `StateHasChanged` は、子イベントが子の中で実行されるイベント ハンドラーでコンポーネントのレンダリングをトリガーするのと同様に、`ParentComponent` を再レンダリングするために自動的に呼び出されます。

`EventCallback` と `EventCallback<T>` では非同期デリゲートを使用できます。 `EventCallback<T>` は厳密に型指定され、特定の引数型を必要とします。 `EventCallback` は弱く型指定され、どの引数型でも許されます。

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

`InvokeAsync` を使用して `EventCallback` または `EventCallback<T>` を呼び出して、<xref:System.Threading.Tasks.Task> を待機します。

```csharp
await callback.InvokeAsync(arg);
```

イベント処理とバインド コンポーネントのパラメーターには、`EventCallback` と `EventCallback<T>` を使用します。

厳密に型指定された `EventCallback<T>` を `EventCallback` よりも優先します。 `EventCallback<T>` では、コンポーネントのユーザーに、より適切なエラー フィードバックが提供されます。 他の UI イベント ハンドラーと同様に、このイベント パラメーターの指定は省略可能です。 コールバックに渡される値がない場合は、`EventCallback` を使用します。

## <a name="prevent-default-actions"></a>既定のアクションを止める

イベントに対する既定のアクションを止めるには、[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) ディレクティブ属性を使用します。

入力デバイスでキーが選択され、要素のフォーカスがテキスト ボックス上にあるときは、通常、ブラウザーによってテキスト ボックスにキーの文字が表示されます。 次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作が止められています。 カウンターはインクリメントされて、 **+** キーは `<input>` 要素の値にキャプチャされません。

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

値なしで `@on{EVENT}:preventDefault` 属性を指定することは、`@on{EVENT}:preventDefault="true"` と同じことになります。

属性の値は、式にすることもできます。 次の例では、`_shouldPreventDefault` は `true` または `false` のいずれかに設定される `bool` フィールドです。

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

既定のアクションを止めるためにイベント ハンドラーは必要ありません。 イベント ハンドラーと、既定のアクションを止めるシナリオは、独立して使用できます。

## <a name="stop-event-propagation"></a>イベント伝達を停止する

イベント伝達を停止するには、[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) ディレクティブ属性を使用します。

次の例では、チェック ボックスをオンにすると、2 番目の子 `<div>` からのクリック イベントが親の `<div>` に伝達されなくなります。

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
