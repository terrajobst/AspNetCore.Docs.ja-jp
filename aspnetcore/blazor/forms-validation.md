---
title: ASP.NET Core Blazor のフォームと検証
author: guardrex
description: Blazor でフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 5aad5a4d4303151ef5be82481dfae7367abeffbc
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083224"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor のフォームと検証

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

フォームと検証は、Blazor で[データ注釈](xref:mvc/models/validation)を使用してサポートされています。

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

フォームは、`EditForm` コンポーネントを使用して定義されます。 次のフォームでは、一般的な要素、コンポーネント、および Razor コードを示しています。

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

* フォームは、`ExampleModel` 型で定義されている検証を使用して、`name` フィールドのユーザー入力を検証します。 モデルはコンポーネントの `@code` ブロック内に作成され、プライベート フィールド (`_exampleModel`) に保持されます。 フィールドは、`<EditForm>` 要素の `Model` 属性に割り当てられます。
* `InputText` コンポーネントの `@bind-Value` は次のようにバインドします。
  * モデル プロパティ (`_exampleModel.Name`) を `InputText` コンポーネントの `Value` プロパティへ。
  * 変更イベント デリゲートを `InputText` コンポーネントの `ValueChanged` プロパティへ。
* `DataAnnotationsValidator` コンポーネントは、データ注釈を使用して検証サポートをアタッチします。
* `ValidationSummary` コンポーネントは、検証メッセージの概要を示します。
* `HandleValidSubmit` は、フォームが正常に送信される (検証に合格する) とトリガーされます。

一連の組み込みの入力コンポーネントを、ユーザー入力の受信と検証に使用できます。 入力は、それらが変更されたときとフォームが送信されたときに検証されます。 次の表に、使用できる入力コンポーネントを示しています。

| 入力コンポーネント | &hellip; とレンダリング       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

すべての入力コンポーネント (`EditForm` を含む) で、任意の属性がサポートされています。 コンポーネント パラメーターに一致しない属性は、レンダリングされる HTML 要素に追加されます。

入力コンポーネントは、編集時に検証し、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。 一部のコンポーネントには、便利な解析ロジックが含まれます。 たとえば、`InputDate` と `InputNumber` では、解析不能な値を検証エラーとして登録することによって、それらを適切に処理します。 NULL 値を受け入れることができる型では、ターゲット フィールドの NULL 値の許容もサポートしています (`int?` など)。

次の `Starship` 型では、以前の `ExampleModel` よりも大きなプロパティとデータ注釈のセットを使用して検証ロジックを定義しています。

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

`EditForm` では、変更されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters) として `EditContext` を作成します。 `EditForm` では、有効な送信と無効な送信用の便利なイベント (`OnValidSubmit`、`OnInvalidSubmit`) も提供しています。 または、`OnSubmit` を使用して、カスタム検証コードで検証をトリガーし、フィールド値をチェックします。

次に例を示します。

* **[送信]** ボタンが選択されると、`HandleSubmit` メソッドが実行されます。
* フォームの `EditContext` を使用して、フォームが検証されます。
* このフォームをさらに検証するには、サーバーで Web API エンドポイントを呼び出す `ServerValidate` メソッドに `EditContext` を渡します (*示されていません*)。
* `isValid` をチェックすることによって、クライアント側とサーバー側の検証の結果に応じて、追加のコードが実行されます。

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

`change` イベントではなく、`input` イベントを使用するカスタム コンポーネントを作成するには、`InputText` コンポーネントを使用します。

次のマークアップでコンポーネントを作成し、`InputText` を使用する場合と同様に、コンポーネントを使用します。

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>オプション ボタンの操作

フォームでオプション ボタンを使用する場合、オプション ボタンはグループとして評価されるため、データ バインディングが他の要素と異なる方法で処理されます。 各オプション ボタンの値は固定ですが、オプション ボタン グループの値は、選択されたオプション ボタンの値です。 以下の例では、次のことを行っています。

* オプション ボタン グループのデータバインディングを処理する。
* カスタム `InputRadio` コンポーネントを使用した検証をサポートする。

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

次の `EditForm` では、前の `InputRadio` コンポーネントを使用して、ユーザーから評価を取得して検証しています。

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

`DataAnnotationsValidator` コンポーネントは、データ注釈を使用した検証サポートをカスケードされた `EditContext` にアタッチします。 データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。 データ注釈と異なる検証システムを使用するには、`DataAnnotationsValidator` をカスタム実装に置き換えます。 参照ソースでの検査に、ASP.NET Core 実装を使用できます。[DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazor は 2 種類の検証を実行します。

* *フィールド検証* は、ユーザーがタブでフィールドを離れたときに実行されます。 フィールドの検証時に、`DataAnnotationsValidator` コンポーネントによって、報告されたすべての検証結果がフィールドに関連付けられます。
* *モデル検証*は、ユーザーがフォームを送信したときに実行されます。 モデルの検証時に、`DataAnnotationsValidator` コンポーネントは、検証結果で報告されたメンバー名に基づいてフィールドを判断しようとします。 個々のメンバーに関連付けられていない検証結果は、フィールドではなくモデルに関連付けられます。

### <a name="validation-summary-and-validation-message-components"></a>検証概要コンポーネントと検証メッセージ コンポーネント

`ValidationSummary` コンポーネントは、すべての検証メッセージを要約します。これは[検証概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)と似ています。

```razor
<ValidationSummary />
```

`Model` パラメーターを使用して、特定のモデルの検証メッセージを出力します。
  
```razor
<ValidationSummary Model="@_starship" />
```

`ValidationMessage` コンポーネントは、特定のフィールドの検証メッセージを表示します。これは、[検証メッセージ タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似ています。 `For` 属性と、モデル プロパティに名前を付けるラムダ式で、検証するフィールドを指定します。

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

`ValidationMessage` コンポーネントと `ValidationSummary` コンポーネントでは、任意の属性をサポートしています。 コンポーネント パラメーターに一致しない属性は、生成された `<div>` 要素または `<ul>` 要素に追加されます。

### <a name="custom-validation-attributes"></a>カスタム検証属性

[カスタム検証属性](xref:mvc/models/validation#custom-attributes)を使用するときに、検証結果がフィールドに正しく関連付けられるようにするには、<xref:System.ComponentModel.DataAnnotations.ValidationResult> の作成時に検証コンテキストの <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> を渡します。

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor データ注釈検証パッケージ

[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) は、`DataAnnotationsValidator` コンポーネントを使用して、検証エクスペリエンスのギャップを埋めるパッケージです。 パッケージは現在、*試験段階*です。

### <a name="compareproperty-attribute"></a>[CompareProperty] 属性

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> は、検証結果を特定のメンバーに関連付けないため、`DataAnnotationsValidator` コンポーネントで正しく機能しません。 これにより、フィールドレベルの検証と、送信時のモデル全体が検証されたときの動作に一貫性がなくなることがあります。 [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) の "*試験的*" パッケージでは、これらの制限を回避する追加の検証属性 `ComparePropertyAttribute` が導入されています。 Blazor アプリでは、`[CompareProperty]` は `[Compare]` 属性の直接の代わりとなるものです。

### <a name="nested-models-collection-types-and-complex-types"></a>入れ子になったモデル、コレクション型、および複合型

Blazor では、組み込みの `DataAnnotationsValidator` によるデータ注釈を使用したフォーム入力の検証をサポートしています。 ただし、`DataAnnotationsValidator`で は、コレクション型または複合型のプロパティではないフォームにバインドされているモデルの最上位レベルのプロパティのみが検証されます。

コレクション型と複合型のプロパティを含む、バインドされたモデルのオブジェクト グラフ全体を検証するには、"*試験的*" な [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) パッケージによって提供される `ObjectGraphDataAnnotationsValidator` を使用します。

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

`[ValidateComplexType]` でモデルのプロパティに注釈を付けます。 次のモデル クラスでは、`ShipDescription` クラスに、モデルがフォームにバインドされたときに検証する追加のデータ注釈が含まれています。

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>フォームの検証に基づいて送信ボタンを有効にする

フォームの検証に基づいて送信ボタンを有効または無効にするには:

* コンポーネントを初期化するときに、フォームの `EditContext` を使用してモデルを割り当てます。
* コンテキストの `OnFieldChanged` コールバックでフォームを検証して、送信ボタンを有効または無効にします。

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
* フォームが読み込まれるときに、送信ボタンを有効にしたいと考えます。

上記の方法の副作用として、ユーザーがいずれかのフィールドを操作した後に、`ValidationSummary` コンポーネントに無効なフィールドが設定されます。 このシナリオは、次のいずれかの方法で対処できます。

* フォームでは `ValidationSummary` コンポーネントを使用しないでください。
* 送信ボタンが選択された (たとえば、`HandleValidSubmit` メソッドで) ときに `ValidationSummary` コンポーネントを表示します。

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
