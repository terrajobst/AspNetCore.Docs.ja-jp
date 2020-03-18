---
title: ASP.NET Core Blazor のイベント処理
author: guardrex
description: イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理シナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648332"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="82fc9-103">ASP.NET Core Blazor のイベント処理</span><span class="sxs-lookup"><span data-stu-id="82fc9-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="82fc9-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="82fc9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="82fc9-105">Razor コンポーネントでは、イベント処理機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-105">Razor components provide event handling features.</span></span> <span data-ttu-id="82fc9-106">`on{EVENT}` という名前の HTML 要素属性 (`onclick`、`onsubmit` など) でデリゲート型の値がある場合、Razor コンポーネントでは、属性の値がイベント ハンドラーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-106">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="82fc9-107">属性の名前は常に [`@on{EVENT}`](xref:mvc/views/razor#onevent) という形式になります。</span><span class="sxs-lookup"><span data-stu-id="82fc9-107">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="82fc9-108">次のコードでは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-108">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="82fc9-109">次のコードでは、UI でチェック ボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-109">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="82fc9-110">イベント ハンドラーは、非同期にして <xref:System.Threading.Tasks.Task> を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-110">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="82fc9-111">[StateHasChanged](xref:blazor/lifecycle#state-changes) を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="82fc9-111">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="82fc9-112">例外は、発生時にログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-112">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="82fc9-113">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-113">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="82fc9-114">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="82fc9-114">Event argument types</span></span>

<span data-ttu-id="82fc9-115">イベントによっては、イベント引数の型を選択できます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-115">For some events, event argument types are permitted.</span></span> <span data-ttu-id="82fc9-116">メソッド呼び出しでイベント型を指定する必要があるのは、そのイベント型がメソッド内で使用されている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="82fc9-116">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="82fc9-117">サポートされている `EventArgs` を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-117">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="82fc9-118">event</span><span class="sxs-lookup"><span data-stu-id="82fc9-118">Event</span></span>            | <span data-ttu-id="82fc9-119">クラス</span><span class="sxs-lookup"><span data-stu-id="82fc9-119">Class</span></span>                | <span data-ttu-id="82fc9-120">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="82fc9-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="82fc9-121">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="82fc9-121">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="82fc9-122">`oncut`、`oncopy`、`onpaste`</span><span class="sxs-lookup"><span data-stu-id="82fc9-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="82fc9-123">ドラッグ</span><span class="sxs-lookup"><span data-stu-id="82fc9-123">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="82fc9-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="82fc9-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="82fc9-125">`DataTransfer` および `DataTransferItem` では、ドラッグされた項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-125">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="82fc9-126">Error</span><span class="sxs-lookup"><span data-stu-id="82fc9-126">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="82fc9-127">event</span><span class="sxs-lookup"><span data-stu-id="82fc9-127">Event</span></span>            | `EventArgs`          | <span data-ttu-id="82fc9-128">*全般*</span><span class="sxs-lookup"><span data-stu-id="82fc9-128">*General*</span></span><br><span data-ttu-id="82fc9-129">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="82fc9-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="82fc9-130">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="82fc9-130">*Clipboard*</span></span><br><span data-ttu-id="82fc9-131">`onbeforecut`、`onbeforecopy`、`onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="82fc9-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="82fc9-132">*入力*</span><span class="sxs-lookup"><span data-stu-id="82fc9-132">*Input*</span></span><br><span data-ttu-id="82fc9-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="82fc9-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="82fc9-134">*メディア*</span><span class="sxs-lookup"><span data-stu-id="82fc9-134">*Media*</span></span><br><span data-ttu-id="82fc9-135">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting`</span><span class="sxs-lookup"><span data-stu-id="82fc9-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="82fc9-136">フォーカス</span><span class="sxs-lookup"><span data-stu-id="82fc9-136">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="82fc9-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="82fc9-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="82fc9-138">`relatedTarget` のサポートは含まれません。</span><span class="sxs-lookup"><span data-stu-id="82fc9-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="82fc9-139">入力</span><span class="sxs-lookup"><span data-stu-id="82fc9-139">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="82fc9-140">`onchange`、`oninput`</span><span class="sxs-lookup"><span data-stu-id="82fc9-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="82fc9-141">キーボード</span><span class="sxs-lookup"><span data-stu-id="82fc9-141">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="82fc9-142">`onkeydown`、`onkeypress`、`onkeyup`</span><span class="sxs-lookup"><span data-stu-id="82fc9-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="82fc9-143">マウス</span><span class="sxs-lookup"><span data-stu-id="82fc9-143">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="82fc9-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="82fc9-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="82fc9-145">マウス ポインター</span><span class="sxs-lookup"><span data-stu-id="82fc9-145">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="82fc9-146">`onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="82fc9-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="82fc9-147">マウス ホイール</span><span class="sxs-lookup"><span data-stu-id="82fc9-147">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="82fc9-148">`onwheel`、`onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="82fc9-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="82fc9-149">進行状況</span><span class="sxs-lookup"><span data-stu-id="82fc9-149">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="82fc9-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="82fc9-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="82fc9-151">タッチ</span><span class="sxs-lookup"><span data-stu-id="82fc9-151">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="82fc9-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="82fc9-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="82fc9-153">`TouchPoint` は、タッチを検知するデバイス上の単一接触点を表します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-153">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="82fc9-154">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="82fc9-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="82fc9-155">[ASP.NET Core 参照ソースの EventArgs クラス (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="82fc9-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="82fc9-156">[MDN Web ドキュメント:GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="82fc9-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="82fc9-157">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="82fc9-157">Lambda expressions</span></span>

<span data-ttu-id="82fc9-158">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)も使用できます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="82fc9-159">要素のセットを反復処理するときなど、追加の値に集中すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="82fc9-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="82fc9-160">次の例では 3 つのボタンを作成しています。各ボタンは、UI で選択されたときにイベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="82fc9-161">ラムダ式では、`for` ループ内で直接、ループ変数 (`i`) を使用**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="82fc9-161">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="82fc9-162">そうしないと、すべてのラムダ式で同じ変数が使用され、`i` の値はすべてのラムダで同じになります。</span><span class="sxs-lookup"><span data-stu-id="82fc9-162">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="82fc9-163">値は常にローカル変数 (前の例では`buttonNumber`) でキャプチャしてから、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-163">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="82fc9-164">EventCallback</span><span class="sxs-lookup"><span data-stu-id="82fc9-164">EventCallback</span></span>

<span data-ttu-id="82fc9-165">入れ子になったコンポーネントがある一般的なシナリオでは、子で `onclick` イベントが発生したときなど、子コンポーネントのイベント&mdash;が発生したときに親コンポーネントのメソッドを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="82fc9-165">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="82fc9-166">コンポーネント間にわたってイベントを公開するには、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="82fc9-167">親コンポーネントでは、コールバック メソッドを子コンポーネントの `EventCallback` に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="82fc9-168">サンプル アプリの `ChildComponent` (*Components/ChildComponent.razor*) は、ボタンの `onclick` ハンドラーがどのように、サンプルの `ParentComponent` から `EventCallback` デリゲートを受け取るように設定されているかを示しています。</span><span class="sxs-lookup"><span data-stu-id="82fc9-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="82fc9-169">`EventCallback` は `MouseEventArgs` によって型指定されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="82fc9-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="82fc9-170">`ParentComponent` では、子の `EventCallback<T>` (`OnClickCallback`) を `ShowMessage` メソッドに設定しています。</span><span class="sxs-lookup"><span data-stu-id="82fc9-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="82fc9-171">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="82fc9-171">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="82fc9-172">`ChildComponent` でボタンが選択されると:</span><span class="sxs-lookup"><span data-stu-id="82fc9-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="82fc9-173">`ParentComponent` の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="82fc9-174">`_messageText` が更新されて、`ParentComponent` に表示されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-174">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="82fc9-175">コールバックのメソッド (`ShowMessage`) 内に、[StateHasChanged](xref:blazor/lifecycle#state-changes) の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="82fc9-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="82fc9-176">`StateHasChanged` は、子イベントが子の中で実行されるイベント ハンドラーでコンポーネントのレンダリングをトリガーするのと同様に、`ParentComponent` を再レンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="82fc9-177">`EventCallback` と `EventCallback<T>` では非同期デリゲートを使用できます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="82fc9-178">`EventCallback<T>` は厳密に型指定され、特定の引数型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="82fc9-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="82fc9-179">`EventCallback` は弱く型指定され、どの引数型でも許されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="82fc9-180">`InvokeAsync` を使用して `EventCallback` または `EventCallback<T>` を呼び出して、<xref:System.Threading.Tasks.Task> を待機します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="82fc9-181">イベント処理とバインド コンポーネントのパラメーターには、`EventCallback` と `EventCallback<T>` を使用します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="82fc9-182">厳密に型指定された `EventCallback<T>` を `EventCallback` よりも優先します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="82fc9-183">`EventCallback<T>` では、コンポーネントのユーザーに、より適切なエラー フィードバックが提供されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="82fc9-184">他の UI イベント ハンドラーと同様に、このイベント パラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="82fc9-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="82fc9-185">コールバックに渡される値がない場合は、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="82fc9-186">既定のアクションを止める</span><span class="sxs-lookup"><span data-stu-id="82fc9-186">Prevent default actions</span></span>

<span data-ttu-id="82fc9-187">イベントに対する既定のアクションを止めるには、[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="82fc9-188">入力デバイスでキーが選択され、要素のフォーカスがテキスト ボックス上にあるときは、通常、ブラウザーによってテキスト ボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="82fc9-189">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作が止められています。</span><span class="sxs-lookup"><span data-stu-id="82fc9-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="82fc9-190">カウンターはインクリメントされて、 **+** キーは `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="82fc9-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="82fc9-191">値なしで `@on{EVENT}:preventDefault` 属性を指定することは、`@on{EVENT}:preventDefault="true"` と同じことになります。</span><span class="sxs-lookup"><span data-stu-id="82fc9-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="82fc9-192">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="82fc9-193">次の例では、`_shouldPreventDefault` は `true` または `false` のいずれかに設定される `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="82fc9-193">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="82fc9-194">既定のアクションを止めるためにイベント ハンドラーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="82fc9-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="82fc9-195">イベント ハンドラーと、既定のアクションを止めるシナリオは、独立して使用できます。</span><span class="sxs-lookup"><span data-stu-id="82fc9-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="82fc9-196">イベント伝達を停止する</span><span class="sxs-lookup"><span data-stu-id="82fc9-196">Stop event propagation</span></span>

<span data-ttu-id="82fc9-197">イベント伝達を停止するには、[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="82fc9-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="82fc9-198">次の例では、チェック ボックスをオンにすると、2 番目の子 `<div>` からのクリック イベントが親の `<div>` に伝達されなくなります。</span><span class="sxs-lookup"><span data-stu-id="82fc9-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
