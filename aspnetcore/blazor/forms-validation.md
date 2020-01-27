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
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a>フォームと検証の Blazor の ASP.NET Core

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

[データ注釈](xref:mvc/models/validation)を使用した Blazor では、フォームおよび検証がサポートされています。

次の `ExampleModel` 型は、データ注釈を使用して検証ロジックを定義します。

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

フォームは、`EditForm` コンポーネントを使用して定義されます。 次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。

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

前の例の場合:

* フォームは、`ExampleModel` の種類で定義されている検証を使用して、`name` フィールドのユーザー入力を検証します。 モデルはコンポーネントの `@code` ブロック内に作成され、プライベートフィールド (`_exampleModel`) に保持されます。 フィールドは、`<EditForm>` 要素の `Model` 属性に割り当てられます。
* `InputText` コンポーネントの `@bind-Value` は次のようにバインドされます。
  * `InputText` コンポーネントの `Value` プロパティへのモデルプロパティ (`_exampleModel.Name`)。
  * `InputText` コンポーネントの `ValueChanged` プロパティに対する変更イベントデリゲート。
* `DataAnnotationsValidator` コンポーネントは、データ注釈を使用して検証サポートをアタッチします。
* `ValidationSummary` コンポーネントは、検証メッセージの概要を示します。
* フォームが正常に送信されると (検証に合格)、`HandleValidSubmit` がトリガーされます。

ユーザー入力の受信と検証には、一連の組み込みの入力コンポーネントを使用できます。 入力は、変更されたときとフォームが送信されたときに検証されます。 次の表に、使用できる入力コンポーネントを示します。

| 入力コンポーネント | &hellip; としてレンダリング       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

すべての入力コンポーネント (`EditForm`を含む) では、任意の属性がサポートされます。 コンポーネントパラメーターに一致しない属性は、表示される HTML 要素に追加されます。

入力コンポーネントは、編集時に検証を行い、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。 一部のコンポーネントには、役に立つ解析ロジックが含まれています。 たとえば、`InputDate` と `InputNumber` では、解析不能な値を検証エラーとして登録することによって適切に処理します。 Null 値を受け入れることができる型では、対象フィールドの null 値の許容もサポートされます (たとえば、`int?`)。

次の `Starship` 型では、以前の `ExampleModel`よりも大きなプロパティとデータ注釈を使用して検証ロジックを定義しています。

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

前の例では、データ注釈が存在しないため、`Description` は省略可能です。

次のフォームでは、`Starship` モデルで定義されている検証を使用してユーザー入力を検証します。

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

`EditForm` は、変更されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters)として `EditContext` を作成します。 `EditForm` は、有効かつ無効な送信 (`OnValidSubmit`、`OnInvalidSubmit`) にも便利なイベントを提供します。 または、`OnSubmit` を使用して、カスタム検証コードで検証フィールドとチェックフィールドの値をトリガーします。

たとえば、行が次のように表示されているとします。

* `HandleSubmit` メソッドは、 **[送信]** ボタンを選択したときに実行されます。
* フォームは、フォームの `EditContext`を使用して検証されます。
* このフォームは、サーバー上で web API エンドポイントを呼び出す `ServerValidate` メソッドに `EditContext` を渡すことによってさらに検証されます (*表示されません*)。
* `isValid`をチェックすることによって、クライアント側とサーバー側の検証の結果に応じて、追加のコードが実行されます。

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

## <a name="inputtext-based-on-the-input-event"></a>入力イベントに基づく InputText

`change` イベントではなく、`input` イベントを使用するカスタムコンポーネントを作成するには、`InputText` コンポーネントを使用します。

次のマークアップを使用してコンポーネントを作成し、`InputText` が使用される場合と同様に、コンポーネントを使用します。

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>ラジオボタンの操作

フォームでオプションボタンを使用する場合、オプションボタンはグループとして評価されるため、データバインディングは他の要素とは異なる方法で処理されます。 各オプションボタンの値は固定されていますが、ラジオボタングループの値は、選択したラジオボタンの値です。 次の例は、その方法を示しています。

* ラジオボタングループのデータバインディングを処理します。
* カスタム `InputRadio` コンポーネントを使用した検証をサポートします。

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

次の `EditForm` では、前の `InputRadio` コンポーネントを使用して、ユーザーから評価を取得して検証します。

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

## <a name="validation-support"></a>検証のサポート

`DataAnnotationsValidator` コンポーネントは、データ注釈を使用した検証サポートをカスケード `EditContext`にアタッチします。 データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。 データ注釈とは異なる検証システムを使用するには、`DataAnnotationsValidator` をカスタム実装に置き換えます。 ASP.NET Core の実装は、参照ソース: [Data注釈](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)を検証するために、 [adddata注釈](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)を使用して/ます。

Blazor は、次の2種類の検証を実行します。

* フィールドの*検証*は、ユーザーがフィールドからタブを取り出したときに実行されます。 フィールドの検証中に、`DataAnnotationsValidator` コンポーネントによって、報告されたすべての検証結果がフィールドに関連付けられます。
* *モデルの検証*は、ユーザーがフォームを送信したときに実行されます。 `DataAnnotationsValidator` コンポーネントは、モデルの検証中に、検証結果によって報告されたメンバー名に基づいてフィールドを決定しようとします。 個々のメンバーに関連付けられていない検証結果は、フィールドではなくモデルに関連付けられます。

### <a name="validation-summary-and-validation-message-components"></a>検証の概要および検証メッセージコンポーネント

`ValidationSummary` コンポーネントは、検証[概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)に似たすべての検証メッセージを要約します。

```razor
<ValidationSummary />
```

`Model` パラメーターを使用して、特定のモデルの検証メッセージを出力します。
  
```razor
<ValidationSummary Model="@_starship" />
```

`ValidationMessage` コンポーネントには、[検証メッセージタグヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似た、特定のフィールドの検証メッセージが表示されます。 `For` 属性と、モデルプロパティに名前を付けるラムダ式を使用して、検証用のフィールドを指定します。

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

`ValidationMessage` コンポーネントと `ValidationSummary` コンポーネントでは、任意の属性がサポートされています。 コンポーネントパラメーターと一致しない属性は、生成された `<div>` 要素または `<ul>` 要素に追加されます。

### <a name="custom-validation-attributes"></a>カスタム検証属性

[カスタム検証属性](xref:mvc/models/validation#custom-attributes)を使用するときに検証結果がフィールドに正しく関連付けられるようにするには、<xref:System.ComponentModel.DataAnnotations.ValidationResult>を作成するときに検証コンテキストの <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> を渡します。

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>データ注釈検証パッケージの Blazor

[Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)は、`DataAnnotationsValidator` コンポーネントを使用して検証エクスペリエンスのギャップを埋めるパッケージです。 パッケージは現在*試験段階*です。

### <a name="compareproperty-attribute"></a>[CompareProperty] 属性

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> は、検証結果と特定のメンバーを関連付けないため、`DataAnnotationsValidator` コンポーネントでは正しく機能しません。 これにより、フィールドレベルの検証と、送信時にモデル全体が検証されるときの動作に一貫性がなくなる可能性があります。 [BlazorAspNetCore です。DataAnnotations。検証](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)の*実験的*なパッケージでは、これらの制限を回避する追加の検証属性 `ComparePropertyAttribute`が導入されています。 Blazor アプリでは、`[CompareProperty]` は `[Compare]` 属性の直接置換です。

### <a name="nested-models-collection-types-and-complex-types"></a>入れ子になったモデル、コレクション型、および複合型

Blazor は、組み込みの `DataAnnotationsValidator`でデータ注釈を使用してフォーム入力を検証する機能をサポートしています。 ただし、`DataAnnotationsValidator` は、コレクションまたは複合型のプロパティではないフォームにバインドされているモデルの最上位レベルのプロパティのみを検証します。

コレクションと複合型のプロパティを含む、バインドされたモデルのオブジェクトグラフ全体を検証するには、*実験的*な[BlazorAspNetCore によって提供される `ObjectGraphDataAnnotationsValidator` を使用します。DataAnnotations。検証](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation)パッケージ:

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

`[ValidateComplexType]`でモデルのプロパティに注釈を付けます。 次のモデルクラスでは、`ShipDescription` クラスに、モデルがフォームにバインドされたときに検証する追加のデータ注釈が含まれています。

*Starship.cs*:

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

*ShipDescription.cs*:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>フォームの検証に基づいて [送信] ボタンを有効にする

フォームの検証に基づいて [送信] ボタンを有効または無効にするには:

* コンポーネントを初期化するときに、フォームの `EditContext` を使用してモデルを割り当てます。
* コンテキストの `OnFieldChanged` コールバックでフォームを検証して、[送信] ボタンを有効または無効にします。

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

前の例では、次の場合に `_formInvalid` を `false` に設定します。

* フォームには、有効な既定値が事前に読み込まれています。
* フォームが読み込まれるときに [送信] ボタンを有効にします。

上記の方法の副作用として、ユーザーが1つのフィールドを操作した後に、`ValidationSummary` コンポーネントに無効なフィールドが設定されていることが挙げられます。 このシナリオは、次のいずれかの方法で対処できます。

* フォームで `ValidationSummary` コンポーネントを使用しないでください。
* [送信] ボタンが選択されているとき (たとえば、`HandleValidSubmit` メソッドなど) に `ValidationSummary` コンポーネントを表示します。

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
