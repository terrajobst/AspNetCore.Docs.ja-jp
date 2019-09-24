---
title: Blazor フォームと検証の ASP.NET Core
author: guardrex
description: Blazor でフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/forms-validation
ms.openlocfilehash: 6f6ace3deb7ed4262b643d897273bc767334b5e6
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207179"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="1aa0f-103">Blazor フォームと検証の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1aa0f-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="1aa0f-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1aa0f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1aa0f-105">[データ注釈](xref:mvc/models/validation)を使用して、Blazor でフォームおよび検証をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="1aa0f-106">次`ExampleModel`の型は、データ注釈を使用して検証ロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="1aa0f-107">フォームは、 `EditForm`コンポーネントを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="1aa0f-108">次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="1aa0f-109">フォームは、 `ExampleModel`型で定義さ`name`れた検証を使用して、フィールドのユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="1aa0f-110">モデルはコンポーネントの`@code`ブロック内に作成され、プライベートフィールド (`exampleModel`) に保持されます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="1aa0f-111">フィールドは、 `Model` `<EditForm>`要素の属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="1aa0f-112">コンポーネント`DataAnnotationsValidator`は、データ注釈を使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="1aa0f-113">コンポーネント`ValidationSummary`は、検証メッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="1aa0f-114">`HandleValidSubmit`は、フォームが正常に送信された (検証に合格した) ときにトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="1aa0f-115">ユーザー入力の受信と検証には、一連の組み込みの入力コンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="1aa0f-116">入力は、変更されたときとフォームが送信されたときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="1aa0f-117">次の表に、使用できる入力コンポーネントを示します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="1aa0f-118">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1aa0f-118">Input component</span></span> | <span data-ttu-id="1aa0f-119">表示形式&hellip;</span><span class="sxs-lookup"><span data-stu-id="1aa0f-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="1aa0f-120">を含む`EditForm`すべての入力コンポーネントは、任意の属性をサポートします。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="1aa0f-121">コンポーネントパラメーターに一致しない属性は、表示される HTML 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="1aa0f-122">入力コンポーネントは、編集時に検証を行い、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="1aa0f-123">一部のコンポーネントには、役に立つ解析ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="1aa0f-124">たとえば、と`InputDate` `InputNumber`は、検証エラーとして登録することによって、解析不能な値を適切に処理します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="1aa0f-125">Null 値を受け入れることができる型では、ターゲットフィールドの null 値の`int?`許容もサポートされます (たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="1aa0f-126">次`Starship`の型では、以前`ExampleModel`よりも大きなプロパティとデータ注釈を使用して検証ロジックを定義しています。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="1aa0f-127">前の例では`Description` 、データ注釈が存在しないため、は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="1aa0f-128">次のフォームは、 `Starship`モデルで定義されている検証を使用してユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="1aa0f-129">は`EditForm` 、変更`EditContext`されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters)としてを作成します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="1aa0f-130">また`EditForm` 、は、有効かつ無効な送信 (`OnValidSubmit`, `OnInvalidSubmit`) の便利なイベントも提供します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="1aa0f-131">または、 `OnSubmit`を使用して、検証フィールドとチェックフィールドの値をカスタム検証コードでトリガーします。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="1aa0f-132">入力イベントに基づく InputText</span><span class="sxs-lookup"><span data-stu-id="1aa0f-132">InputText based on the input event</span></span>

<span data-ttu-id="1aa0f-133">イベントで`InputText`はなく`input`イベントを使用するカスタムコンポーネントを作成するには、コンポーネントを使用します。 `change`</span><span class="sxs-lookup"><span data-stu-id="1aa0f-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="1aa0f-134">次のマークアップを使用してコンポーネントを作成し、その`InputText`コンポーネントを使用した場合と同じように使用します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="1aa0f-135">検証のサポート</span><span class="sxs-lookup"><span data-stu-id="1aa0f-135">Validation support</span></span>

<span data-ttu-id="1aa0f-136">コンポーネント`DataAnnotationsValidator`は、データ注釈をカスケード`EditContext`に使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="1aa0f-137">データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="1aa0f-138">データ注釈とは異なる検証システムを使用するには`DataAnnotationsValidator` 、をカスタム実装に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="1aa0f-139">ASP.NET Core の実装は、参照ソースの検査に使用できます。[DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="1aa0f-140">コンポーネント`ValidationSummary`は、検証の[概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)に似たすべての検証メッセージを要約します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-140">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="1aa0f-141">コンポーネント`ValidationMessage`には、[検証メッセージタグヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似た、特定のフィールドの検証メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-141">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="1aa0f-142">`For`属性を使用した検証用のフィールドと、モデルプロパティに名前を付けるラムダ式を指定します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-142">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="1aa0f-143">コンポーネント`ValidationMessage` と`ValidationSummary`コンポーネントは、任意の属性をサポートします。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-143">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="1aa0f-144">コンポーネントパラメーターに一致しない属性は、生成`<div>`される要素また`<ul>`は要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-144">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="1aa0f-145">複合型またはコレクション型のプロパティの検証</span><span class="sxs-lookup"><span data-stu-id="1aa0f-145">Validation of complex or collection type properties</span></span>

<span data-ttu-id="1aa0f-146">モデルのプロパティに適用される検証属性は、フォームが送信されるときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-146">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="1aa0f-147">ただし、モデルのコレクションや複合データ型のプロパティは、フォームの送信時に検証されません。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-147">However, the properties of collections or complex data types of a model aren't validated on form submission.</span></span> <span data-ttu-id="1aa0f-148">このシナリオで入れ子になった検証属性を使用するには、カスタム検証コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-148">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="1aa0f-149">例については、 [aspnet/Samples GitHub リポジトリの Blazor 検証サンプル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1aa0f-149">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>
