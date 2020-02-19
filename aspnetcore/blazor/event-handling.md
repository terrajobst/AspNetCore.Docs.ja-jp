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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="bc881-103">ASP.NET Core Blazor のイベント処理</span><span class="sxs-lookup"><span data-stu-id="bc881-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="bc881-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bc881-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="bc881-105">Razor コンポーネントは、イベント処理機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="bc881-105">Razor components provide event handling features.</span></span> <span data-ttu-id="bc881-106">`on{EVENT}` という名前の HTML 要素属性 (`onclick`、`onsubmit`など) とデリゲート型の値がある場合、Razor コンポーネントはその属性の値をイベントハンドラーとして扱います。</span><span class="sxs-lookup"><span data-stu-id="bc881-106">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="bc881-107">属性の名前は常に[`@on{EVENT}`](xref:mvc/views/razor#onevent)書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-107">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="bc881-108">次のコードは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bc881-108">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="bc881-109">次のコードは、UI でチェックボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bc881-109">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="bc881-110">イベントハンドラーを非同期にして、<xref:System.Threading.Tasks.Task>を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="bc881-110">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="bc881-111">[Statehaschanged](xref:blazor/lifecycle#state-changes)を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="bc881-111">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="bc881-112">例外は、発生するとログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-112">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="bc881-113">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-113">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="bc881-114">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="bc881-114">Event argument types</span></span>

<span data-ttu-id="bc881-115">イベントによっては、イベント引数の型が許可されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-115">For some events, event argument types are permitted.</span></span> <span data-ttu-id="bc881-116">メソッド呼び出しでイベントの種類を指定する必要があるのは、イベントの種類がメソッドで使用されている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="bc881-116">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="bc881-117">次の表に、サポートされている `EventArgs` を示します。</span><span class="sxs-lookup"><span data-stu-id="bc881-117">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="bc881-118">Event</span><span class="sxs-lookup"><span data-stu-id="bc881-118">Event</span></span>            | <span data-ttu-id="bc881-119">クラス</span><span class="sxs-lookup"><span data-stu-id="bc881-119">Class</span></span>                | <span data-ttu-id="bc881-120">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="bc881-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="bc881-121">クリップボード</span><span class="sxs-lookup"><span data-stu-id="bc881-121">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="bc881-122">`oncut`、`oncopy`、`onpaste`</span><span class="sxs-lookup"><span data-stu-id="bc881-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="bc881-123">抗力</span><span class="sxs-lookup"><span data-stu-id="bc881-123">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="bc881-124">`ondrag`､`ondragstart`、`ondragenter`、`ondragleave`、`ondragover`、`ondrop`、`ondragend`</span><span class="sxs-lookup"><span data-stu-id="bc881-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="bc881-125">`DataTransfer` および `DataTransferItem` ドラッグした項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="bc881-125">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="bc881-126">エラー</span><span class="sxs-lookup"><span data-stu-id="bc881-126">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="bc881-127">Event</span><span class="sxs-lookup"><span data-stu-id="bc881-127">Event</span></span>            | `EventArgs`          | <span data-ttu-id="bc881-128">*全般*</span><span class="sxs-lookup"><span data-stu-id="bc881-128">*General*</span></span><br><span data-ttu-id="bc881-129">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="bc881-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="bc881-130">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="bc881-130">*Clipboard*</span></span><br><span data-ttu-id="bc881-131">`onbeforecut`、`onbeforecopy`、`onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="bc881-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="bc881-132">*入力*</span><span class="sxs-lookup"><span data-stu-id="bc881-132">*Input*</span></span><br><span data-ttu-id="bc881-133">`oninvalid`、`onreset`、`onselect`、`onselectionchange`、`onselectstart`、`onsubmit`</span><span class="sxs-lookup"><span data-stu-id="bc881-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="bc881-134">*メディア*</span><span class="sxs-lookup"><span data-stu-id="bc881-134">*Media*</span></span><br><span data-ttu-id="bc881-135">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`の `onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、および `onsuspend``ontimeupdate``onvolumechange``onwaiting`</span><span class="sxs-lookup"><span data-stu-id="bc881-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="bc881-136">Focus</span><span class="sxs-lookup"><span data-stu-id="bc881-136">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="bc881-137">`onfocus`、`onblur`、`onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="bc881-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="bc881-138">には `relatedTarget`のサポートは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="bc881-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="bc881-139">入力</span><span class="sxs-lookup"><span data-stu-id="bc881-139">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="bc881-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="bc881-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="bc881-141">[キーボード]</span><span class="sxs-lookup"><span data-stu-id="bc881-141">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="bc881-142">`onkeydown`、`onkeypress`、`onkeyup`</span><span class="sxs-lookup"><span data-stu-id="bc881-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="bc881-143">マウス</span><span class="sxs-lookup"><span data-stu-id="bc881-143">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="bc881-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="bc881-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="bc881-145">マウスポインター</span><span class="sxs-lookup"><span data-stu-id="bc881-145">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="bc881-146">`onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="bc881-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="bc881-147">マウスホイール</span><span class="sxs-lookup"><span data-stu-id="bc881-147">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="bc881-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="bc881-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="bc881-149">進行状況</span><span class="sxs-lookup"><span data-stu-id="bc881-149">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="bc881-150">`onabort`、`onload`、`onloadend`、`onloadstart`、`onprogress`、`ontimeout`</span><span class="sxs-lookup"><span data-stu-id="bc881-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="bc881-151">タッチ</span><span class="sxs-lookup"><span data-stu-id="bc881-151">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="bc881-152">`ontouchstart`、`ontouchend`、`ontouchmove`、`ontouchenter`、`ontouchleave`、`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="bc881-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="bc881-153">`TouchPoint` は、タッチを区別するデバイス上の1つの連絡先ポイントを表します。</span><span class="sxs-lookup"><span data-stu-id="bc881-153">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="bc881-154">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bc881-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="bc881-155">[ASP.NET Core 参照ソースの EventArgs クラス (dotnet/aspnetcore release/3.1 分岐)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="bc881-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="bc881-156">[MDN web ドキュメント: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; には、各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="bc881-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="bc881-157">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="bc881-157">Lambda expressions</span></span>

<span data-ttu-id="bc881-158">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="bc881-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="bc881-159">多くの場合、要素のセットを反復処理するときなど、追加の値を終了すると便利です。</span><span class="sxs-lookup"><span data-stu-id="bc881-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="bc881-160">次の例では、3つのボタンを作成します。各ボタンは、UI で選択されたときに、イベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="bc881-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="bc881-161">ラムダ式では、`for` ループでループ変数 (`i`) を**直接使用しないでください。**</span><span class="sxs-lookup"><span data-stu-id="bc881-161">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="bc881-162">それ以外の場合、すべてのラムダ式で同じ変数が使用され、`i`の値はすべてのラムダで同じになります。</span><span class="sxs-lookup"><span data-stu-id="bc881-162">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="bc881-163">常にローカル変数 (前の例では`buttonNumber`) で値をキャプチャし、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="bc881-163">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="bc881-164">EventCallback</span><span class="sxs-lookup"><span data-stu-id="bc881-164">EventCallback</span></span>

<span data-ttu-id="bc881-165">入れ子になったコンポーネントの一般的なシナリオとして、子コンポーネントのイベント&mdash;が発生したときに親コンポーネントのメソッドを実行することが望まれます。たとえば、子で `onclick` イベントが発生した場合などです。</span><span class="sxs-lookup"><span data-stu-id="bc881-165">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="bc881-166">コンポーネント間でイベントを公開するには、`EventCallback`を使用します。</span><span class="sxs-lookup"><span data-stu-id="bc881-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="bc881-167">親コンポーネントは、コールバックメソッドを子コンポーネントの `EventCallback`に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="bc881-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="bc881-168">サンプルアプリ (*Components/ChildComponent. razor*) の `ChildComponent` は、ボタンの `onclick` ハンドラーが、サンプルの `ParentComponent`から `EventCallback` デリゲートを受け取るように設定されている方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="bc881-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="bc881-169">`EventCallback` は `MouseEventArgs`で入力されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="bc881-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="bc881-170">`ParentComponent` によって、子の `EventCallback<T>` (`OnClickCallback`) が `ShowMessage` メソッドに設定されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="bc881-171">*Pages/ParentComponent。 razor*:</span><span class="sxs-lookup"><span data-stu-id="bc881-171">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="bc881-172">`ChildComponent`でボタンが選択されている場合:</span><span class="sxs-lookup"><span data-stu-id="bc881-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="bc881-173">`ParentComponent`の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="bc881-174">`_messageText` が更新され、`ParentComponent`に表示されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-174">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="bc881-175">このコールバックのメソッド (`ShowMessage`) では、 [Statehaschanged](xref:blazor/lifecycle#state-changes)の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="bc881-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="bc881-176">`StateHasChanged` は、子イベントと同様に、子の内部で実行されるイベントハンドラーでコンポーネントの実行がトリガーされるのと同様に、`ParentComponent`をレンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="bc881-177">`EventCallback` および `EventCallback<T>` 非同期デリゲートを許可します。</span><span class="sxs-lookup"><span data-stu-id="bc881-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="bc881-178">`EventCallback<T>` は厳密に型指定され、特定の引数型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="bc881-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="bc881-179">`EventCallback` は弱く型指定され、任意の引数型を使用できます。</span><span class="sxs-lookup"><span data-stu-id="bc881-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="bc881-180">`InvokeAsync` で `EventCallback` または `EventCallback<T>` を呼び出し、<xref:System.Threading.Tasks.Task>を待機します。</span><span class="sxs-lookup"><span data-stu-id="bc881-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="bc881-181">イベント処理とバインドコンポーネントのパラメーターには、`EventCallback` と `EventCallback<T>` を使用します。</span><span class="sxs-lookup"><span data-stu-id="bc881-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="bc881-182">厳密に型指定された `EventCallback<T>` `EventCallback`を優先します。</span><span class="sxs-lookup"><span data-stu-id="bc881-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="bc881-183">`EventCallback<T>` は、コンポーネントのユーザーに対してより適切なエラーフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="bc881-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="bc881-184">他の UI イベントハンドラーと同様に、イベントパラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="bc881-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="bc881-185">コールバックに値が渡されない場合は、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="bc881-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="bc881-186">既定のアクションを禁止する</span><span class="sxs-lookup"><span data-stu-id="bc881-186">Prevent default actions</span></span>

<span data-ttu-id="bc881-187">イベントの既定のアクションを回避するには、 [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="bc881-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="bc881-188">入力デバイスでキーが選択され、要素のフォーカスがテキストボックスに表示されている場合は、通常、ブラウザーによってテキストボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bc881-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="bc881-189">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作を回避できます。</span><span class="sxs-lookup"><span data-stu-id="bc881-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="bc881-190">カウンターがインクリメントされ、 **+** キーが `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="bc881-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="bc881-191">値を指定せずに `@on{EVENT}:preventDefault` 属性を指定することは `@on{EVENT}:preventDefault="true"`と同じです。</span><span class="sxs-lookup"><span data-stu-id="bc881-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="bc881-192">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="bc881-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="bc881-193">次の例では、`_shouldPreventDefault` は `true` または `false`のいずれかに設定された `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="bc881-193">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="bc881-194">既定のアクションを防ぐために、イベントハンドラーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="bc881-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="bc881-195">イベントハンドラーを使用したり、既定のアクションシナリオを回避したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="bc881-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="bc881-196">イベントの伝達の停止</span><span class="sxs-lookup"><span data-stu-id="bc881-196">Stop event propagation</span></span>

<span data-ttu-id="bc881-197">イベントの伝達を停止するには、 [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="bc881-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="bc881-198">次の例では、チェックボックスをオンにすると、2番目の子 `<div>` からのクリックイベントが親 `<div>`に反映されなくなります。</span><span class="sxs-lookup"><span data-stu-id="bc881-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
