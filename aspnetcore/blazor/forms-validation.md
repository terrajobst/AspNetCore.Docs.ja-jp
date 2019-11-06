---
title: Blazor フォームと検証の ASP.NET Core
author: guardrex
description: Blazor でフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: blazor/forms-validation
ms.openlocfilehash: 09281779e7f0b31e525e0e79c2d6d9ce9ca5b8ce
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659782"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="e0e30-103">Blazor フォームと検証の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0e30-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="e0e30-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0e30-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e0e30-105">[データ注釈](xref:mvc/models/validation)を使用して、Blazor でフォームおよび検証をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="e0e30-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="e0e30-106">次の `ExampleModel` 型は、データ注釈を使用して検証ロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="e0e30-107">フォームは、`EditForm` コンポーネントを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="e0e30-108">次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。</span><span class="sxs-lookup"><span data-stu-id="e0e30-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="e0e30-109">フォームは、`ExampleModel` の種類で定義されている検証を使用して、`name` フィールドのユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="e0e30-110">モデルはコンポーネントの `@code` ブロック内に作成され、プライベートフィールド (`exampleModel`) に保持されます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="e0e30-111">フィールドは、`<EditForm>` 要素の `Model` 属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="e0e30-112">`DataAnnotationsValidator` コンポーネントは、データ注釈を使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="e0e30-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="e0e30-113">`ValidationSummary` コンポーネントは、検証メッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="e0e30-114">フォームが正常に送信されると (検証に合格)、`HandleValidSubmit` がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="e0e30-115">ユーザー入力の受信と検証には、一連の組み込みの入力コンポーネントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="e0e30-116">入力は、変更されたときとフォームが送信されたときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="e0e30-117">次の表に、使用できる入力コンポーネントを示します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="e0e30-118">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="e0e30-118">Input component</span></span> | <span data-ttu-id="e0e30-119">&hellip; としてレンダリング</span><span class="sxs-lookup"><span data-stu-id="e0e30-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="e0e30-120">すべての入力コンポーネント (`EditForm`を含む) では、任意の属性がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="e0e30-121">コンポーネントパラメーターに一致しない属性は、表示される HTML 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="e0e30-122">入力コンポーネントは、編集時に検証を行い、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="e0e30-123">一部のコンポーネントには、役に立つ解析ロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e0e30-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="e0e30-124">たとえば、`InputDate` と `InputNumber` では、解析不能な値を検証エラーとして登録することによって適切に処理します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="e0e30-125">Null 値を受け入れることができる型では、対象フィールドの null 値の許容もサポートされます (たとえば、`int?`)。</span><span class="sxs-lookup"><span data-stu-id="e0e30-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="e0e30-126">次の `Starship` 型では、以前の `ExampleModel`よりも大きなプロパティとデータ注釈を使用して検証ロジックを定義しています。</span><span class="sxs-lookup"><span data-stu-id="e0e30-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="e0e30-127">前の例では、データ注釈が存在しないため、`Description` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="e0e30-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="e0e30-128">次のフォームでは、`Starship` モデルで定義されている検証を使用してユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="e0e30-129">`EditForm` は、変更されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters)として `EditContext` を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="e0e30-130">`EditForm` は、有効かつ無効な送信 (`OnValidSubmit`、`OnInvalidSubmit`) にも便利なイベントを提供します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="e0e30-131">または、`OnSubmit` を使用して、カスタム検証コードで検証フィールドとチェックフィールドの値をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="e0e30-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="e0e30-132">入力イベントに基づく InputText</span><span class="sxs-lookup"><span data-stu-id="e0e30-132">InputText based on the input event</span></span>

<span data-ttu-id="e0e30-133">`change` イベントではなく、`input` イベントを使用するカスタムコンポーネントを作成するには、`InputText` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="e0e30-134">次のマークアップを使用してコンポーネントを作成し、`InputText` が使用される場合と同様に、コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="e0e30-135">検証のサポート</span><span class="sxs-lookup"><span data-stu-id="e0e30-135">Validation support</span></span>

<span data-ttu-id="e0e30-136">`DataAnnotationsValidator` コンポーネントは、データ注釈を使用した検証サポートをカスケード `EditContext`にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="e0e30-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="e0e30-137">データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。</span><span class="sxs-lookup"><span data-stu-id="e0e30-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="e0e30-138">データ注釈とは異なる検証システムを使用するには、`DataAnnotationsValidator` をカスタム実装に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="e0e30-139">ASP.NET Core の実装は、参照ソース: [Data注釈](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)を検証するために、 [adddata注釈](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)を使用して/ます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="e0e30-140">`ValidationSummary` コンポーネントは、検証の[概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)に似たすべての検証メッセージを要約します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-140">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="e0e30-141">`ValidationMessage` コンポーネントには、[検証メッセージタグヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似た、特定のフィールドの検証メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-141">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="e0e30-142">`For` 属性と、モデルプロパティに名前を付けるラムダ式を使用して、検証用のフィールドを指定します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-142">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="e0e30-143">`ValidationMessage` コンポーネントと `ValidationSummary` コンポーネントでは、任意の属性がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e0e30-143">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="e0e30-144">コンポーネントパラメーターと一致しない属性は、生成された `<div>` 要素または `<ul>` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-144">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="e0e30-145">**Blazor パッケージ (AspNetCore の検証)**</span><span class="sxs-lookup"><span data-stu-id="e0e30-145">**Microsoft.AspNetCore.Blazor.DataAnnotations.Validation package**</span></span>

<span data-ttu-id="e0e30-146">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)は、`DataAnnotationsValidator` コンポーネントを使用して、検証エクスペリエンスのギャップを埋めるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="e0e30-146">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="e0e30-147">パッケージは現在*試験段階*です。今後のリリースでは、これらのシナリオを ASP.NET Core framework に追加する予定です。</span><span class="sxs-lookup"><span data-stu-id="e0e30-147">The package is currently *experimental*, and we plan to add these scenarios into the ASP.NET Core framework in a future release.</span></span>

<span data-ttu-id="e0e30-148">`DataAnnotationsValidator` コンポーネントでは、検証モデルの複合プロパティのサブプロパティは検証されません。</span><span class="sxs-lookup"><span data-stu-id="e0e30-148">The `DataAnnotationsValidator` component doesn't validate subproperties of complex properties on a validating model.</span></span> <span data-ttu-id="e0e30-149">コレクション型プロパティの項目は検証されません。</span><span class="sxs-lookup"><span data-stu-id="e0e30-149">Items of collection-type properties aren't validated.</span></span> <span data-ttu-id="e0e30-150">これらの型を検証するために、`Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` パッケージでは、`ObjectGraphDataAnnotationsValidator` コンポーネントと連動する `ValidateComplexType` 検証属性が導入されています。</span><span class="sxs-lookup"><span data-stu-id="e0e30-150">To validate these types, the `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces the `ValidateComplexType` validation attribute that works in tandem with the `ObjectGraphDataAnnotationsValidator` component.</span></span> <span data-ttu-id="e0e30-151">これらの型の使用例については、 [aspnet/Samples GitHub リポジトリの Blazor 検証サンプル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0e30-151">For an example of these types in use, see the [Blazor Validation sample in the aspnet/samples GitHub repository ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

<span data-ttu-id="e0e30-152"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> は、`DataAnnotationsValidator` コンポーネントでは適切に機能しません。</span><span class="sxs-lookup"><span data-stu-id="e0e30-152">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="e0e30-153">`Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` パッケージでは、これらの制限を回避する追加の検証属性 `ComparePropertyAttribute`が導入されています。</span><span class="sxs-lookup"><span data-stu-id="e0e30-153">The `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="e0e30-154">Blazor アプリでは、`ComparePropertyAttribute` は `CompareAttribute`の直接置換です。</span><span class="sxs-lookup"><span data-stu-id="e0e30-154">In a Blazor app, `ComparePropertyAttribute` is a direct replacement for the `CompareAttribute`.</span></span> <span data-ttu-id="e0e30-155">詳細については、「 [OnValidSubmit EditForm では Compareattribute が無視されました (aspnet/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0e30-155">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="e0e30-156">複合型またはコレクション型のプロパティの検証</span><span class="sxs-lookup"><span data-stu-id="e0e30-156">Validation of complex or collection type properties</span></span>

<span data-ttu-id="e0e30-157">モデルのプロパティに適用される検証属性は、フォームが送信されるときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="e0e30-157">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="e0e30-158">ただし、モデルのコレクションまたは複合データ型のプロパティは、`DataAnnotationsValidator` コンポーネントによるフォームの送信時に検証されません。</span><span class="sxs-lookup"><span data-stu-id="e0e30-158">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="e0e30-159">このシナリオで入れ子になった検証属性を使用するには、カスタム検証コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="e0e30-159">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="e0e30-160">例については、 [aspnet/Samples GitHub リポジトリの Blazor 検証サンプル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0e30-160">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
