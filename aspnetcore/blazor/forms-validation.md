---
title: フォームと検証の Blazor の ASP.NET Core
author: guardrex
description: Blazorでフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/forms-validation
ms.openlocfilehash: a94a433f26e451bbadc73615e502e46d273f05c2
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828140"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="c8b8e-103">フォームと検証の Blazor の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8b8e-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="c8b8e-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c8b8e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c8b8e-105">[データ注釈](xref:mvc/models/validation)を使用した Blazor では、フォームおよび検証がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="c8b8e-106">次の `ExampleModel` 型は、データ注釈を使用して検証ロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="c8b8e-107">フォームは、`EditForm` コンポーネントを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="c8b8e-108">次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

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

* <span data-ttu-id="c8b8e-109">フォームは、`ExampleModel` の種類で定義されている検証を使用して、`name` フィールドのユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="c8b8e-110">モデルはコンポーネントの `@code` ブロック内に作成され、プライベートフィールド (`exampleModel`) に保持されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="c8b8e-111">フィールドは、`<EditForm>` 要素の `Model` 属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="c8b8e-112">`DataAnnotationsValidator` コンポーネントは、データ注釈を使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="c8b8e-113">`ValidationSummary` コンポーネントは、検証メッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="c8b8e-114">フォームが正常に送信されると (検証に合格)、`HandleValidSubmit` がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="c8b8e-115">ユーザー入力の受信と検証には、一連の組み込みの入力コンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="c8b8e-116">入力は、変更されたときとフォームが送信されたときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="c8b8e-117">次の表に、使用できる入力コンポーネントを示します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="c8b8e-118">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="c8b8e-118">Input component</span></span> | <span data-ttu-id="c8b8e-119">&hellip; としてレンダリング</span><span class="sxs-lookup"><span data-stu-id="c8b8e-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="c8b8e-120">すべての入力コンポーネント (`EditForm`を含む) では、任意の属性がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="c8b8e-121">コンポーネントパラメーターに一致しない属性は、表示される HTML 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="c8b8e-122">入力コンポーネントは、編集時に検証を行い、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="c8b8e-123">一部のコンポーネントには、役に立つ解析ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="c8b8e-124">たとえば、`InputDate` と `InputNumber` では、解析不能な値を検証エラーとして登録することによって適切に処理します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="c8b8e-125">Null 値を受け入れることができる型では、対象フィールドの null 値の許容もサポートされます (たとえば、`int?`)。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="c8b8e-126">次の `Starship` 型では、以前の `ExampleModel`よりも大きなプロパティとデータ注釈を使用して検証ロジックを定義しています。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="c8b8e-127">前の例では、データ注釈が存在しないため、`Description` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="c8b8e-128">次のフォームでは、`Starship` モデルで定義されている検証を使用してユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
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

<span data-ttu-id="c8b8e-129">`EditForm` は、変更されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters)として `EditContext` を作成します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="c8b8e-130">`EditForm` は、有効かつ無効な送信 (`OnValidSubmit`、`OnInvalidSubmit`) にも便利なイベントを提供します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="c8b8e-131">または、`OnSubmit` を使用して、カスタム検証コードで検証フィールドとチェックフィールドの値をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="c8b8e-132">入力イベントに基づく InputText</span><span class="sxs-lookup"><span data-stu-id="c8b8e-132">InputText based on the input event</span></span>

<span data-ttu-id="c8b8e-133">`change` イベントではなく、`input` イベントを使用するカスタムコンポーネントを作成するには、`InputText` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="c8b8e-134">次のマークアップを使用してコンポーネントを作成し、`InputText` が使用される場合と同様に、コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="c8b8e-135">検証のサポート</span><span class="sxs-lookup"><span data-stu-id="c8b8e-135">Validation support</span></span>

<span data-ttu-id="c8b8e-136">`DataAnnotationsValidator` コンポーネントは、データ注釈を使用した検証サポートをカスケード `EditContext`にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="c8b8e-137">データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="c8b8e-138">データ注釈とは異なる検証システムを使用するには、`DataAnnotationsValidator` をカスタム実装に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="c8b8e-139">ASP.NET Core の実装は、参照ソース: [Data注釈](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)を検証するために、 [adddata注釈](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)を使用して/ます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="c8b8e-140"> は、次の2種類の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-140"> performs two types of validation:</span></span>

* <span data-ttu-id="c8b8e-141">フィールドの*検証*は、ユーザーがフィールドからタブを取り出したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-141">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="c8b8e-142">フィールドの検証中に、`DataAnnotationsValidator` コンポーネントによって、報告されたすべての検証結果がフィールドに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-142">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="c8b8e-143">*モデルの検証*は、ユーザーがフォームを送信したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-143">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="c8b8e-144">`DataAnnotationsValidator` コンポーネントは、モデルの検証中に、検証結果によって報告されたメンバー名に基づいてフィールドを決定しようとします。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-144">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="c8b8e-145">個々のメンバーに関連付けられていない検証結果は、フィールドではなくモデルに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-145">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="c8b8e-146">検証の概要および検証メッセージコンポーネント</span><span class="sxs-lookup"><span data-stu-id="c8b8e-146">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="c8b8e-147">`ValidationSummary` コンポーネントは、検証[概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)に似たすべての検証メッセージを要約します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-147">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="c8b8e-148">`Model` パラメーターを使用して、特定のモデルの検証メッセージを出力します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-148">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="c8b8e-149">`ValidationMessage` コンポーネントには、[検証メッセージタグヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似た、特定のフィールドの検証メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-149">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="c8b8e-150">`For` 属性と、モデルプロパティに名前を付けるラムダ式を使用して、検証用のフィールドを指定します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-150">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="c8b8e-151">`ValidationMessage` コンポーネントと `ValidationSummary` コンポーネントでは、任意の属性がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-151">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="c8b8e-152">コンポーネントパラメーターと一致しない属性は、生成された `<div>` 要素または `<ul>` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-152">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="c8b8e-153">カスタム検証属性</span><span class="sxs-lookup"><span data-stu-id="c8b8e-153">Custom validation attributes</span></span>

<span data-ttu-id="c8b8e-154">[カスタム検証属性](xref:mvc/models/validation#custom-attributes)を使用するときに検証結果がフィールドに正しく関連付けられるようにするには、<xref:System.ComponentModel.DataAnnotations.ValidationResult>を作成するときに検証コンテキストの <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> を渡します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-154">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

::: moniker range=">= aspnetcore-3.1"

### <a name="opno-locblazor-data-annotations-validation-package"></a><span data-ttu-id="c8b8e-155">データ注釈検証パッケージの Blazor</span><span class="sxs-lookup"><span data-stu-id="c8b8e-155">Blazor data annotations validation package</span></span>

<span data-ttu-id="c8b8e-156">[Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)は、`DataAnnotationsValidator` コンポーネントを使用して検証エクスペリエンスのギャップを埋めるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-156">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="c8b8e-157">パッケージは現在*試験段階*です。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-157">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="c8b8e-158">[CompareProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="c8b8e-158">[CompareProperty] attribute</span></span>

<span data-ttu-id="c8b8e-159"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> は、`DataAnnotationsValidator` コンポーネントでは適切に機能しません。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-159">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="c8b8e-160">[BlazorAspNetCore です。DataAnnotations。検証](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)の*実験的*なパッケージでは、これらの制限を回避する追加の検証属性 `ComparePropertyAttribute`が導入されています。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-160">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="c8b8e-161">Blazor アプリでは、`[CompareProperty]` は `[Compare]` 属性の直接置換です。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-161">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span> <span data-ttu-id="c8b8e-162">詳細については、「 [OnValidSubmit EditForm によって無視される Compareattribute (dotnet/AspNetCore #10643)](https://github.com/dotnet/AspNetCore/issues/10643#issuecomment-543909748)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-162">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (dotnet/AspNetCore #10643)](https://github.com/dotnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="c8b8e-163">入れ子になったモデル、コレクション型、および複合型</span><span class="sxs-lookup"><span data-stu-id="c8b8e-163">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="c8b8e-164"> は、組み込みの `DataAnnotationsValidator`でデータ注釈を使用してフォーム入力を検証する機能をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-164"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="c8b8e-165">ただし、`DataAnnotationsValidator` は、コレクションまたは複合型のプロパティではないフォームにバインドされているモデルの最上位レベルのプロパティのみを検証します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-165">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="c8b8e-166">コレクションと複合型のプロパティを含む、バインドされたモデルのオブジェクトグラフ全体を検証するには、*実験的*な[BlazorAspNetCore によって提供される `ObjectGraphDataAnnotationsValidator` を使用します。DataAnnotations。検証](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)パッケージ:</span><span class="sxs-lookup"><span data-stu-id="c8b8e-166">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="c8b8e-167">`[ValidateComplexType]`でモデルのプロパティに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-167">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="c8b8e-168">次のモデルクラスでは、`ShipDescription` クラスに、モデルがフォームにバインドされたときに検証する追加のデータ注釈が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-168">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="c8b8e-169">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8b8e-169">*Starship.cs*:</span></span>

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

<span data-ttu-id="c8b8e-170">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8b8e-170">*ShipDescription.cs*:</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="c8b8e-171">複合型またはコレクション型のプロパティの検証</span><span class="sxs-lookup"><span data-stu-id="c8b8e-171">Validation of complex or collection type properties</span></span>

<span data-ttu-id="c8b8e-172">モデルのプロパティに適用される検証属性は、フォームが送信されるときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-172">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="c8b8e-173">ただし、モデルのコレクションまたは複合データ型のプロパティは、`DataAnnotationsValidator` コンポーネントによるフォームの送信時に検証されません。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-173">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="c8b8e-174">このシナリオで入れ子になった検証属性を使用するには、カスタム検証コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-174">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="c8b8e-175">例については、「 [Blazor の検証のサンプル (aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8b8e-175">For an example, see the [Blazor Validation sample (aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
