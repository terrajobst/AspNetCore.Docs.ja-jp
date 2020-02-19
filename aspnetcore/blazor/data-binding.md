---
title: ASP.NET Core Blazor データバインディング
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータバインディングのシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: c38e6095d4e93d3eead10fa8bb0356b2113bb220
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453158"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="dcccb-103">ASP.NET Core Blazor データバインディング</span><span class="sxs-lookup"><span data-stu-id="dcccb-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="dcccb-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="dcccb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="dcccb-105">コンポーネントと DOM 要素の両方に対するデータバインディングは、 [`@bind`](xref:mvc/views/razor#bind)属性を使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-105">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="dcccb-106">次の例では、`CurrentValue` プロパティをテキストボックスの値にバインドします。</span><span class="sxs-lookup"><span data-stu-id="dcccb-106">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="dcccb-107">テキストボックスがフォーカスを失うと、プロパティの値が更新されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="dcccb-108">テキストボックスは、プロパティの値の変更に対する応答ではなく、コンポーネントがレンダリングされたときにのみ UI で更新されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="dcccb-109">イベントハンドラーのコードを実行した後にコンポーネントがレンダリングされるため、*通常*、イベントハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="dcccb-110">`CurrentValue` プロパティ (`<input @bind="CurrentValue" />`) で `@bind` を使用することは、基本的に次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="dcccb-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="dcccb-111">コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="dcccb-112">ユーザーがテキストボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="dcccb-113">実際には、型変換が実行されるケースが `@bind` によって処理されるため、コード生成はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="dcccb-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="dcccb-114">原則として、`@bind` は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="dcccb-115">`@bind` 構文を使用した `onchange` イベントの処理に加えて、`event` パラメーター ([`@bind-value:event`](xref:mvc/views/razor#bind)) を使用して[`@bind-value`](xref:mvc/views/razor#bind)属性を指定することで、プロパティまたはフィールドを他のイベントを使用してバインドできます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-115">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="dcccb-116">次の例では、`oninput` イベントの `CurrentValue` プロパティをバインドします。</span><span class="sxs-lookup"><span data-stu-id="dcccb-116">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="dcccb-117">要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキストボックスの値が変更されたときに `oninput` が発生します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="dcccb-118">前の例の `@bind-value` は、次のようにバインドします。</span><span class="sxs-lookup"><span data-stu-id="dcccb-118">`@bind-value` in the preceding example binds:</span></span>

* <span data-ttu-id="dcccb-119">要素の `value` 属性に対して指定された式 (`CurrentValue`)。</span><span class="sxs-lookup"><span data-stu-id="dcccb-119">The specified expression (`CurrentValue`) to the element's `value` attribute.</span></span>
* <span data-ttu-id="dcccb-120">`@bind-value:event`によって指定されたイベントへの変更イベントデリゲート。</span><span class="sxs-lookup"><span data-stu-id="dcccb-120">A change event delegate to the event specified by `@bind-value:event`.</span></span>

### <a name="unparsable-values"></a><span data-ttu-id="dcccb-121">解析不可能値</span><span class="sxs-lookup"><span data-stu-id="dcccb-121">Unparsable values</span></span>

<span data-ttu-id="dcccb-122">データバインド要素に解析できない値を指定すると、バインドイベントがトリガーされたときに、解析されていない値が自動的に前の値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="dcccb-123">以下のシナリオについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-123">Consider the following scenario:</span></span>

* <span data-ttu-id="dcccb-124">`<input>` 要素は、初期値 `123`を持つ `int` 型にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="dcccb-125">ユーザーは、要素の値をページの `123.45` に更新し、要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="dcccb-126">前のシナリオでは、要素の値が `123`に戻されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="dcccb-127">`123`の元の値を優先して `123.45` 値が拒否された場合、ユーザーはその値が受け入れられていないことを認識します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="dcccb-128">既定では、バインドは要素の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) に適用されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="dcccb-129">別のイベントを設定するには、`@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-129">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="dcccb-130">`oninput` イベント (`@bind-value:event="oninput"`) の場合、解析できない値を導入するキーストロークの後に再設定が発生します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-130">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="dcccb-131">`int`バインドされた型の `oninput` イベントを対象とする場合、ユーザーは `.` 文字を入力できません。</span><span class="sxs-lookup"><span data-stu-id="dcccb-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="dcccb-132">`.` 文字はすぐに削除されるので、ユーザーは、整数のみが許可されるというフィードバックをすぐに受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="dcccb-133">`oninput` イベントの値を元に戻すことは、ユーザーが解析できない `<input>` 値のクリアを許可する必要がある場合など、理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="dcccb-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="dcccb-134">代替手段は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dcccb-134">Alternatives include:</span></span>

* <span data-ttu-id="dcccb-135">`oninput` イベントは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="dcccb-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="dcccb-136">既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。</span><span class="sxs-lookup"><span data-stu-id="dcccb-136">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="dcccb-137">`int?` や `string`などの null 許容型にバインドし、無効なエントリを処理するカスタムロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-137">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="dcccb-138">`InputNumber` や `InputDate`などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-138">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="dcccb-139">フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="dcccb-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="dcccb-140">フォーム検証コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="dcccb-140">Form validation components:</span></span>
  * <span data-ttu-id="dcccb-141">ユーザーが無効な入力を提供し、関連付けられた `EditContext`で検証エラーを受信することを許可します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-141">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="dcccb-142">追加の web フォームデータを入力するユーザーに干渉することなく、UI に検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-142">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

### <a name="format-strings"></a><span data-ttu-id="dcccb-143">書式指定文字列</span><span class="sxs-lookup"><span data-stu-id="dcccb-143">Format strings</span></span>

<span data-ttu-id="dcccb-144">データバインディングは、 [`@bind:format`](xref:mvc/views/razor#bind)を使用して <xref:System.DateTime> 書式指定文字列で動作します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-144">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="dcccb-145">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="dcccb-145">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="dcccb-146">前のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text`に設定されています。</span><span class="sxs-lookup"><span data-stu-id="dcccb-146">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="dcccb-147">`@bind:format` は、次の .NET 型のバインドに対してサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dcccb-147">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="dcccb-148"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="dcccb-148"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="dcccb-149"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="dcccb-149"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="dcccb-150">`@bind:format` 属性は、`<input>` 要素の `value` に適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-150">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="dcccb-151">この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-151">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="dcccb-152">Blazor に日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="dcccb-152">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="dcccb-153">推奨事項では、`date` フィールドの種類で形式が指定されている場合、バインドの `yyyy-MM-dd` 日付形式のみを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dcccb-153">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="dcccb-154">コンポーネントパラメーターを使用した親子バインド</span><span class="sxs-lookup"><span data-stu-id="dcccb-154">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="dcccb-155">バインディングは、コンポーネントのパラメーターを認識します。 `@bind-{property}` は、親コンポーネントのプロパティ値を子コンポーネントにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-155">Binding recognizes component parameters, where `@bind-{property}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="dcccb-156">子から親へのバインドについては、「[チェーンバインドを使用した子から親へのバインド](#child-to-parent-binding-with-chained-bind)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcccb-156">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="dcccb-157">次の子コンポーネント (`ChildComponent`) には、`Year` コンポーネントパラメーターと `YearChanged` コールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="dcccb-157">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="dcccb-158">`EventCallback<T>` については <xref:blazor/event-handling#eventcallback>をご説明します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-158">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="dcccb-159">次の親コンポーネントはを使用します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-159">The following parent component uses:</span></span>

* <span data-ttu-id="dcccb-160">を `ChildComponent` し、親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。</span><span class="sxs-lookup"><span data-stu-id="dcccb-160">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="dcccb-161">`onclick` イベントは、`ChangeTheYear` メソッドをトリガーするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-161">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="dcccb-162">詳細については、<xref:blazor/event-handling> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcccb-162">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="dcccb-163">`ParentComponent` を読み込むと、次のマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-163">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="dcccb-164">`ParentComponent`のボタンを選択して `ParentYear` プロパティの値が変更された場合は、`ChildComponent` の `Year` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-164">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="dcccb-165">`Year` の新しい値は、`ParentComponent` が再度実行されたときに UI に表示されます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-165">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="dcccb-166">`Year` パラメーターは、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="dcccb-166">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="dcccb-167">慣例により、`<ChildComponent @bind-Year="ParentYear" />` は基本的には書き込みと同じです。</span><span class="sxs-lookup"><span data-stu-id="dcccb-167">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="dcccb-168">一般に、プロパティは、`@bind-property:event` 属性を使用して、対応するイベントハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-168">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="dcccb-169">たとえば、プロパティ `MyProp` は、次の2つの属性を使用して `MyEventHandler` にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-169">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="dcccb-170">チェーンバインドを使用した親子バインド</span><span class="sxs-lookup"><span data-stu-id="dcccb-170">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="dcccb-171">一般的なシナリオでは、データバインドパラメーターをコンポーネントの出力のページ要素に連結します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-171">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="dcccb-172">このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーンバインド*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-172">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="dcccb-173">チェーンバインドは、ページの要素で `@bind` 構文を使用して実装することはできません。</span><span class="sxs-lookup"><span data-stu-id="dcccb-173">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="dcccb-174">イベントハンドラーと値は、個別に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dcccb-174">The event handler and value must be specified separately.</span></span> <span data-ttu-id="dcccb-175">ただし、親コンポーネントでは、`@bind` 構文をコンポーネントのパラメーターと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="dcccb-175">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="dcccb-176">次の `PasswordField` コンポーネント (*Passwordfield*):</span><span class="sxs-lookup"><span data-stu-id="dcccb-176">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="dcccb-177">`Password` プロパティに `<input>` 要素の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-177">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="dcccb-178">[Eventcallback](xref:blazor/event-handling#eventcallback)を使用して、`Password` プロパティの変更を親コンポーネントに公開します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-178">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="dcccb-179">`onclick` イベントを使用して、`ToggleShowPassword` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="dcccb-179">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="dcccb-180">詳細については、<xref:blazor/event-handling> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcccb-180">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="dcccb-181">`PasswordField` コンポーネントが別のコンポーネントで使用されています。</span><span class="sxs-lookup"><span data-stu-id="dcccb-181">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="dcccb-182">前の例で、パスワードの確認またはトラップエラーを実行するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-182">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="dcccb-183">`Password` のバッキングフィールドを作成します (次のコード例では`_password`)。</span><span class="sxs-lookup"><span data-stu-id="dcccb-183">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="dcccb-184">`Password` setter で、チェックまたはトラップのエラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-184">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="dcccb-185">次の例では、パスワードの値にスペースが使用されている場合に、ユーザーにすぐにフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="dcccb-185">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

### <a name="radio-buttons"></a><span data-ttu-id="dcccb-186">オプション ボタン</span><span class="sxs-lookup"><span data-stu-id="dcccb-186">Radio buttons</span></span>

<span data-ttu-id="dcccb-187">フォームのオプションボタンへのバインドの詳細については、「<xref:blazor/forms-validation#work-with-radio-buttons>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dcccb-187">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
