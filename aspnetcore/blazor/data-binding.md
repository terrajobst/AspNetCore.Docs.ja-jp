---
title: ASP.NET Core Blazor データ バインディング
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 92377730b9d353a507ffd384710fb979affe7265
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648224"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="8c2cb-103">ASP.NET Core Blazor データ バインディング</span><span class="sxs-lookup"><span data-stu-id="8c2cb-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="8c2cb-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8c2cb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8c2cb-105">コンポーネントと DOM 要素の両方に対するデータ バインディングは、[`@bind`](xref:mvc/views/razor#bind) 属性を使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-105">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="8c2cb-106">次の例では、`CurrentValue` プロパティをテキスト ボックスの値にバインドします。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-106">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8c2cb-107">テキスト ボックスがフォーカスを失うと、プロパティの値が更新されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="8c2cb-108">テキスト ボックスは、プロパティの値の変更に対する応答としてではなく、コンポーネントがレンダリングされたときにのみ UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="8c2cb-109">コンポーネントはイベント ハンドラーのコードが実行された後に自身をレンダリングするため、*通常は*、イベント ハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="8c2cb-110">`CurrentValue` プロパティで `@bind` を使用する (`<input @bind="CurrentValue" />`) ことは、本質的に次の内容と同じです。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8c2cb-111">コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="8c2cb-112">ユーザーがテキスト ボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="8c2cb-113">実際には、`@bind` は型変換が行われるケースを扱うため、コード生成はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="8c2cb-114">原則として、`@bind` は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="8c2cb-115">他のイベントのプロパティまたはフィールドをバインドするには、`event` パラメーターを含めた `@bind:event` 属性を含めます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="8c2cb-116">次の例は、`CurrentValue` プロパティを `oninput` イベントにバインドします。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="8c2cb-117">要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキスト ボックスの値が変更されたときに `oninput` が発生します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="8c2cb-118">`value` 以外の要素の属性をバインドするには、`@bind-{ATTRIBUTE}:event` 構文で `@bind-{ATTRIBUTE}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="8c2cb-119">次の例では、`_paragraphStyle` の値が変更されると段落のスタイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

## <a name="unparsable-values"></a><span data-ttu-id="8c2cb-120">解析不可能値</span><span class="sxs-lookup"><span data-stu-id="8c2cb-120">Unparsable values</span></span>

<span data-ttu-id="8c2cb-121">ユーザーがデータバインド要素に解析できない値を指定すると、バインド イベントがトリガーされたときに、解析できない値は自動的に前の値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-121">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="8c2cb-122">次のシナリオについて検討してください。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-122">Consider the following scenario:</span></span>

* <span data-ttu-id="8c2cb-123">`<input>` 要素は、初期値 `123` を持つ `int` 型にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-123">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="8c2cb-124">ユーザーは、ページで要素の値を `123.45` に更新し、要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-124">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="8c2cb-125">上のシナリオでは、要素の値が `123`に戻されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-125">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="8c2cb-126">値 `123.45` が拒否されて元の値 `123` が設定された場合、ユーザーはその値が受け入れられなかったことを認識します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-126">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="8c2cb-127">既定では、バインドは要素の `onchange` イベントに適用されます (`@bind="{PROPERTY OR FIELD}"`)。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-127">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="8c2cb-128">別のイベントでバインドをトリガーするには、`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-128">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="8c2cb-129">`oninput` イベント (`@bind:event="oninput"`) の場合、解析できない値が入力されたキーストロークの後に元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-129">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="8c2cb-130">`int` にバインドされた型の `oninput` イベントを対象とする場合、ユーザーが `.` 文字を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-130">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="8c2cb-131">`.` 文字はすぐに削除されるので、ユーザーは整数のみが許可されるというフィードバックをすぐに受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-131">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="8c2cb-132">`oninput` イベントで値を元に戻すのが理想的ではないシナリオもあります。たとえば、ユーザーが解析できない `<input>` 値をクリアできる必要がある場合などです。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-132">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="8c2cb-133">代替手段は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-133">Alternatives include:</span></span>

* <span data-ttu-id="8c2cb-134">`oninput` イベントは使用しません。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-134">Don't use the `oninput` event.</span></span> <span data-ttu-id="8c2cb-135">既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"` のみを指定) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-135">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="8c2cb-136">`int?` や `string` などの null 許容型にバインドし、無効なエントリを処理するカスタム ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-136">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="8c2cb-137">`InputNumber` や `InputDate` などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-137">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="8c2cb-138">フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-138">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="8c2cb-139">フォーム検証コンポーネントを使うと、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-139">Form validation components:</span></span>
  * <span data-ttu-id="8c2cb-140">ユーザーの無効な入力を許可し、関連付けられた `EditContext` で検証エラーを受信します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-140">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="8c2cb-141">ユーザーが追加の Web フォーム データを入力することを妨げることなく、UI に検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-141">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="8c2cb-142">書式指定文字列</span><span class="sxs-lookup"><span data-stu-id="8c2cb-142">Format strings</span></span>

<span data-ttu-id="8c2cb-143">データ バインディングは、[`@bind:format`](xref:mvc/views/razor#bind) を使用して <xref:System.DateTime> 書式指定文字列を処理します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-143">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="8c2cb-144">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-144">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="8c2cb-145">上のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-145">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="8c2cb-146">`@bind:format` は、次の .NET 型のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-146">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="8c2cb-147"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="8c2cb-147"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="8c2cb-148"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="8c2cb-148"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="8c2cb-149">`@bind:format` 属性には、`<input>` 要素の `value` に適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-149">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="8c2cb-150">この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-150">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="8c2cb-151">Blazor には日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-151">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="8c2cb-152">この推奨事項には反しますが、`date` フィールド型で形式を指定する場合は、`yyyy-MM-dd` 日付形式を使用してバインドしたのみ正しく動作します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-152">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="8c2cb-153">コンポーネント パラメーターを使用した親から子へのバインディング</span><span class="sxs-lookup"><span data-stu-id="8c2cb-153">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="8c2cb-154">バインディングは、コンポーネントのパラメーターを認識します。`@bind-{PROPERTY}` は、親コンポーネントのプロパティ値を子コンポーネントにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-154">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="8c2cb-155">子から親へのバインドについては、「[チェーン バインドを使用した子から親へのバインディング](#child-to-parent-binding-with-chained-bind)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-155">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="8c2cb-156">次の子コンポーネント (`ChildComponent`) には、`Year` コンポーネント パラメーターと `YearChanged` コールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-156">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="8c2cb-157">`EventCallback<T>` についての説明は、<xref:blazor/event-handling#eventcallback>にあります。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-157">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="8c2cb-158">次の親コンポーネントは、以下を使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-158">The following parent component uses:</span></span>

* <span data-ttu-id="8c2cb-159">`ChildComponent` を使用して親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-159">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="8c2cb-160">`onclick` イベントは、`ChangeTheYear` メソッドをトリガーするために使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-160">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="8c2cb-161">詳細については、「<xref:blazor/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-161">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="8c2cb-162">`ParentComponent` を読み込むと、次のマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-162">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="8c2cb-163">`ParentComponent` のボタンを選択して `ParentYear` プロパティの値を変更すると、`ChildComponent` の `Year` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-163">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="8c2cb-164">`Year` の新しい値は、`ParentComponent` が再度レンダリングされたときに UI に表示されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-164">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="8c2cb-165">`Year` パラメーターには、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-165">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="8c2cb-166">慣例として、`<ChildComponent @bind-Year="ParentYear" />` は基本的に以下の書き込みと同じです。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-166">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="8c2cb-167">一般に、プロパティに `@bind-{PROPRETY}:event` 属性を含めることで、対応するイベント ハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-167">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="8c2cb-168">たとえば、プロパティ `MyProp` は、次の 2 つの属性を使用して `MyEventHandler` にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-168">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="8c2cb-169">チェーン バインドを使用した子から親へのバインディング</span><span class="sxs-lookup"><span data-stu-id="8c2cb-169">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="8c2cb-170">一般的なシナリオでは、データにバインドされたパラメーターをコンポーネントの出力のページ要素に連結します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-170">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="8c2cb-171">このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーン バインド*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-171">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="8c2cb-172">チェーン バインドは、ページの要素で `@bind` 構文を使用して実装することはできません。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-172">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="8c2cb-173">イベント ハンドラーと値は、個別に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-173">The event handler and value must be specified separately.</span></span> <span data-ttu-id="8c2cb-174">ただし、親コンポーネントでは、`@bind` 構文をコンポーネントのパラメーターと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-174">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="8c2cb-175">次の `PasswordField` コンポーネント (*PasswordField.razor*) は、以下のことを行います。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-175">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="8c2cb-176">`Password` プロパティに `<input>` 要素の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-176">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="8c2cb-177">[EventCallback](xref:blazor/event-handling#eventcallback) を使用して、`Password` プロパティの変更を親コンポーネントに公開します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-177">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="8c2cb-178">`onclick` イベントは、`ToggleShowPassword` メソッドをトリガーするために使用します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-178">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="8c2cb-179">詳細については、「<xref:blazor/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-179">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h2>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="8c2cb-180">`PasswordField` コンポーネントは、別のコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-180">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="8c2cb-181">上の例で、パスワードの確認またはエラーの捕捉を行うには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-181">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="8c2cb-182">`Password` のバッキング フィールドを作成します (次のコード例では、`_password`)。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-182">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="8c2cb-183">`Password` セッターで、確認またはエラーの捕捉を行います。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-183">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="8c2cb-184">次の例では、パスワードの値にスペースが使用されている場合、すぐにユーザーにフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-184">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="8c2cb-185">ラジオ ボタン</span><span class="sxs-lookup"><span data-stu-id="8c2cb-185">Radio buttons</span></span>

<span data-ttu-id="8c2cb-186">フォームのラジオ ボタンへのバインドの詳細については、「<xref:blazor/forms-validation#work-with-radio-buttons>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c2cb-186">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
