---
title: フォームと検証の Blazor の ASP.NET Core
author: guardrex
description: Blazorでフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 2758bcbbc76c8a59716fe224dd2deb4ca8c06929
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726884"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="1336c-103">フォームと検証の [!OP.NO-LOC(Blazor)] の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1336c-103">ASP.NET Core [!OP.NO-LOC(Blazor)] forms and validation</span></span>

<span data-ttu-id="1336c-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1336c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1336c-105">[データ注釈](xref:mvc/models/validation)を使用した [!OP.NO-LOC(Blazor)] では、フォームおよび検証がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1336c-105">Forms and validation are supported in [!OP.NO-LOC(Blazor)] using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="1336c-106">次の `ExampleModel` 型は、データ注釈を使用して検証ロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="1336c-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="1336c-107">フォームは、`EditForm` コンポーネントを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="1336c-108">次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。</span><span class="sxs-lookup"><span data-stu-id="1336c-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="1336c-109">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="1336c-109">In the preceding example:</span></span>

* <span data-ttu-id="1336c-110">フォームは、`ExampleModel` の種類で定義されている検証を使用して、`name` フィールドのユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="1336c-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="1336c-111">モデルはコンポーネントの `@code` ブロック内に作成され、プライベートフィールド (`_exampleModel`) に保持されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="1336c-112">フィールドは、`<EditForm>` 要素の `Model` 属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="1336c-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="1336c-113">`InputText` コンポーネントの `@bind-Value` は次のようにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="1336c-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="1336c-114">`InputText` コンポーネントの `Value` プロパティへのモデルプロパティ (`_exampleModel.Name`)。</span><span class="sxs-lookup"><span data-stu-id="1336c-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="1336c-115">`InputText` コンポーネントの `ValueChanged` プロパティに対する変更イベントデリゲート。</span><span class="sxs-lookup"><span data-stu-id="1336c-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="1336c-116">`DataAnnotationsValidator` コンポーネントは、データ注釈を使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="1336c-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="1336c-117">`ValidationSummary` コンポーネントは、検証メッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="1336c-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="1336c-118">フォームが正常に送信されると (検証に合格)、`HandleValidSubmit` がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1336c-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="1336c-119">ユーザー入力の受信と検証には、一連の組み込みの入力コンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="1336c-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="1336c-120">入力は、変更されたときとフォームが送信されたときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="1336c-121">次の表に、使用できる入力コンポーネントを示します。</span><span class="sxs-lookup"><span data-stu-id="1336c-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="1336c-122">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1336c-122">Input component</span></span> | <span data-ttu-id="1336c-123">&hellip; としてレンダリング</span><span class="sxs-lookup"><span data-stu-id="1336c-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="1336c-124">すべての入力コンポーネント (`EditForm`を含む) では、任意の属性がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="1336c-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="1336c-125">コンポーネントパラメーターに一致しない属性は、表示される HTML 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="1336c-126">入力コンポーネントは、編集時に検証を行い、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。</span><span class="sxs-lookup"><span data-stu-id="1336c-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="1336c-127">一部のコンポーネントには、役に立つ解析ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1336c-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="1336c-128">たとえば、`InputDate` と `InputNumber` では、解析不能な値を検証エラーとして登録することによって適切に処理します。</span><span class="sxs-lookup"><span data-stu-id="1336c-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="1336c-129">Null 値を受け入れることができる型では、対象フィールドの null 値の許容もサポートされます (たとえば、`int?`)。</span><span class="sxs-lookup"><span data-stu-id="1336c-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="1336c-130">次の `Starship` 型では、以前の `ExampleModel`よりも大きなプロパティとデータ注釈を使用して検証ロジックを定義しています。</span><span class="sxs-lookup"><span data-stu-id="1336c-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="1336c-131">前の例では、データ注釈が存在しないため、`Description` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="1336c-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="1336c-132">次のフォームでは、`Starship` モデルで定義されている検証を使用してユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="1336c-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="1336c-133">`EditForm` は、変更されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters)として `EditContext` を作成します。</span><span class="sxs-lookup"><span data-stu-id="1336c-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="1336c-134">`EditForm` は、有効かつ無効な送信 (`OnValidSubmit`、`OnInvalidSubmit`) にも便利なイベントを提供します。</span><span class="sxs-lookup"><span data-stu-id="1336c-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="1336c-135">または、`OnSubmit` を使用して、カスタム検証コードで検証フィールドとチェックフィールドの値をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="1336c-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="1336c-136">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1336c-136">In the following example:</span></span>

* <span data-ttu-id="1336c-137">`HandleSubmit` メソッドは、 **[送信]** ボタンを選択したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="1336c-138">フォームは、フォームの `EditContext`を使用して検証されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="1336c-139">このフォームは、サーバー上で web API エンドポイントを呼び出す `ServerValidate` メソッドに `EditContext` を渡すことによってさらに検証されます (*表示されません*)。</span><span class="sxs-lookup"><span data-stu-id="1336c-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="1336c-140">`isValid`をチェックすることによって、クライアント側とサーバー側の検証の結果に応じて、追加のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="1336c-141">入力イベントに基づく InputText</span><span class="sxs-lookup"><span data-stu-id="1336c-141">InputText based on the input event</span></span>

<span data-ttu-id="1336c-142">`change` イベントではなく、`input` イベントを使用するカスタムコンポーネントを作成するには、`InputText` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="1336c-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="1336c-143">次のマークアップを使用してコンポーネントを作成し、`InputText` が使用される場合と同様に、コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="1336c-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="1336c-144">ラジオボタンの操作</span><span class="sxs-lookup"><span data-stu-id="1336c-144">Work with radio buttons</span></span>

<span data-ttu-id="1336c-145">フォームでオプションボタンを使用する場合、オプションボタンはグループとして評価されるため、データバインディングは他の要素とは異なる方法で処理されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="1336c-146">各オプションボタンの値は固定されていますが、ラジオボタングループの値は、選択したラジオボタンの値です。</span><span class="sxs-lookup"><span data-stu-id="1336c-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="1336c-147">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="1336c-147">The following example shows how to:</span></span>

* <span data-ttu-id="1336c-148">ラジオボタングループのデータバインディングを処理します。</span><span class="sxs-lookup"><span data-stu-id="1336c-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="1336c-149">カスタム `InputRadio` コンポーネントを使用した検証をサポートします。</span><span class="sxs-lookup"><span data-stu-id="1336c-149">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="1336c-150">次の `EditForm` では、前の `InputRadio` コンポーネントを使用して、ユーザーから評価を取得して検証します。</span><span class="sxs-lookup"><span data-stu-id="1336c-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="1336c-151">検証のサポート</span><span class="sxs-lookup"><span data-stu-id="1336c-151">Validation support</span></span>

<span data-ttu-id="1336c-152">`DataAnnotationsValidator` コンポーネントは、データ注釈を使用した検証サポートをカスケード `EditContext`にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="1336c-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="1336c-153">データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。</span><span class="sxs-lookup"><span data-stu-id="1336c-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="1336c-154">データ注釈とは異なる検証システムを使用するには、`DataAnnotationsValidator` をカスタム実装に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1336c-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="1336c-155">ASP.NET Core の実装は、参照ソース: [Data注釈](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)を検証するために、 [adddata注釈](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)を使用して/ます。</span><span class="sxs-lookup"><span data-stu-id="1336c-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="1336c-156"> は、次の2種類の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="1336c-156"> performs two types of validation:</span></span>

* <span data-ttu-id="1336c-157">フィールドの*検証*は、ユーザーがフィールドからタブを取り出したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="1336c-158">フィールドの検証中に、`DataAnnotationsValidator` コンポーネントによって、報告されたすべての検証結果がフィールドに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="1336c-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="1336c-159">*モデルの検証*は、ユーザーがフォームを送信したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="1336c-160">`DataAnnotationsValidator` コンポーネントは、モデルの検証中に、検証結果によって報告されたメンバー名に基づいてフィールドを決定しようとします。</span><span class="sxs-lookup"><span data-stu-id="1336c-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="1336c-161">個々のメンバーに関連付けられていない検証結果は、フィールドではなくモデルに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="1336c-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="1336c-162">検証の概要および検証メッセージコンポーネント</span><span class="sxs-lookup"><span data-stu-id="1336c-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="1336c-163">`ValidationSummary` コンポーネントは、検証[概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)に似たすべての検証メッセージを要約します。</span><span class="sxs-lookup"><span data-stu-id="1336c-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="1336c-164">`Model` パラメーターを使用して、特定のモデルの検証メッセージを出力します。</span><span class="sxs-lookup"><span data-stu-id="1336c-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="1336c-165">`ValidationMessage` コンポーネントには、[検証メッセージタグヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似た、特定のフィールドの検証メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="1336c-166">`For` 属性と、モデルプロパティに名前を付けるラムダ式を使用して、検証用のフィールドを指定します。</span><span class="sxs-lookup"><span data-stu-id="1336c-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="1336c-167">`ValidationMessage` コンポーネントと `ValidationSummary` コンポーネントでは、任意の属性がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1336c-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="1336c-168">コンポーネントパラメーターと一致しない属性は、生成された `<div>` 要素または `<ul>` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1336c-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="1336c-169">カスタム検証属性</span><span class="sxs-lookup"><span data-stu-id="1336c-169">Custom validation attributes</span></span>

<span data-ttu-id="1336c-170">[カスタム検証属性](xref:mvc/models/validation#custom-attributes)を使用するときに検証結果がフィールドに正しく関連付けられるようにするには、<xref:System.ComponentModel.DataAnnotations.ValidationResult>を作成するときに検証コンテキストの <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> を渡します。</span><span class="sxs-lookup"><span data-stu-id="1336c-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a><span data-ttu-id="1336c-171">データ注釈検証パッケージの Blazor</span><span class="sxs-lookup"><span data-stu-id="1336c-171">Blazor data annotations validation package</span></span>

<span data-ttu-id="1336c-172">[BlazorAspNetCore です。DataAnnotations。検証](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)は、`DataAnnotationsValidator` コンポーネントを使用して検証エクスペリエンスのギャップを埋めるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="1336c-172">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="1336c-173">パッケージは現在*試験段階*です。</span><span class="sxs-lookup"><span data-stu-id="1336c-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="1336c-174">[CompareProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="1336c-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="1336c-175"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> は、検証結果と特定のメンバーを関連付けないため、`DataAnnotationsValidator` コンポーネントでは正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="1336c-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="1336c-176">これにより、フィールドレベルの検証と、送信時にモデル全体が検証されるときの動作に一貫性がなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1336c-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="1336c-177">[BlazorAspNetCore です。DataAnnotations。検証](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)の*実験的*なパッケージでは、これらの制限を回避する追加の検証属性 `ComparePropertyAttribute`が導入されています。</span><span class="sxs-lookup"><span data-stu-id="1336c-177">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="1336c-178">Blazor アプリでは、`[CompareProperty]` は `[Compare]` 属性の直接置換です。</span><span class="sxs-lookup"><span data-stu-id="1336c-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="1336c-179">入れ子になったモデル、コレクション型、および複合型</span><span class="sxs-lookup"><span data-stu-id="1336c-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="1336c-180"> は、組み込みの `DataAnnotationsValidator`でデータ注釈を使用してフォーム入力を検証する機能をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="1336c-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="1336c-181">ただし、`DataAnnotationsValidator` は、コレクションまたは複合型のプロパティではないフォームにバインドされているモデルの最上位レベルのプロパティのみを検証します。</span><span class="sxs-lookup"><span data-stu-id="1336c-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="1336c-182">コレクションと複合型のプロパティを含む、バインドされたモデルのオブジェクトグラフ全体を検証するには、*実験的*な[BlazorAspNetCore によって提供される `ObjectGraphDataAnnotationsValidator` を使用します。DataAnnotations。検証](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)パッケージ:</span><span class="sxs-lookup"><span data-stu-id="1336c-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="1336c-183">`[ValidateComplexType]`でモデルのプロパティに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="1336c-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="1336c-184">次のモデルクラスでは、`ShipDescription` クラスに、モデルがフォームにバインドされたときに検証する追加のデータ注釈が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1336c-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="1336c-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="1336c-185">*Starship.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="1336c-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="1336c-186">*ShipDescription.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="1336c-187">フォームの検証に基づいて [送信] ボタンを有効にする</span><span class="sxs-lookup"><span data-stu-id="1336c-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="1336c-188">フォームの検証に基づいて [送信] ボタンを有効または無効にするには:</span><span class="sxs-lookup"><span data-stu-id="1336c-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="1336c-189">コンポーネントを初期化するときに、フォームの `EditContext` を使用してモデルを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1336c-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="1336c-190">コンテキストの `OnFieldChanged` コールバックでフォームを検証して、[送信] ボタンを有効または無効にします。</span><span class="sxs-lookup"><span data-stu-id="1336c-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>

```razor
<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);

        _editContext.OnFieldChanged += (_, __) =>
        {
            _formInvalid = !_editContext.Validate();
            StateHasChanged();
        };
    }
}
```

<span data-ttu-id="1336c-191">前の例では、次の場合に `_formInvalid` を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1336c-191">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="1336c-192">フォームには、有効な既定値が事前に読み込まれています。</span><span class="sxs-lookup"><span data-stu-id="1336c-192">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="1336c-193">フォームが読み込まれるときに [送信] ボタンを有効にします。</span><span class="sxs-lookup"><span data-stu-id="1336c-193">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="1336c-194">上記の方法の副作用として、ユーザーが1つのフィールドを操作した後に、`ValidationSummary` コンポーネントに無効なフィールドが設定されていることが挙げられます。</span><span class="sxs-lookup"><span data-stu-id="1336c-194">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="1336c-195">このシナリオは、次のいずれかの方法で対処できます。</span><span class="sxs-lookup"><span data-stu-id="1336c-195">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="1336c-196">フォームで `ValidationSummary` コンポーネントを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1336c-196">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="1336c-197">[送信] ボタンが選択されているとき (たとえば、`HandleValidSubmit` メソッドなど) に `ValidationSummary` コンポーネントを表示します。</span><span class="sxs-lookup"><span data-stu-id="1336c-197">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
