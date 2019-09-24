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
# <a name="aspnet-core-blazor-forms-and-validation"></a>Blazor フォームと検証の ASP.NET Core

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

[データ注釈](xref:mvc/models/validation)を使用して、Blazor でフォームおよび検証をサポートしています。

次`ExampleModel`の型は、データ注釈を使用して検証ロジックを定義します。

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

フォームは、 `EditForm`コンポーネントを使用して定義されます。 次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。

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

* フォームは、 `ExampleModel`型で定義さ`name`れた検証を使用して、フィールドのユーザー入力を検証します。 モデルはコンポーネントの`@code`ブロック内に作成され、プライベートフィールド (`exampleModel`) に保持されます。 フィールドは、 `Model` `<EditForm>`要素の属性に割り当てられます。
* コンポーネント`DataAnnotationsValidator`は、データ注釈を使用して検証サポートをアタッチします。
* コンポーネント`ValidationSummary`は、検証メッセージの概要を示します。
* `HandleValidSubmit`は、フォームが正常に送信された (検証に合格した) ときにトリガーされます。

ユーザー入力の受信と検証には、一連の組み込みの入力コンポーネントを使用できます。 入力は、変更されたときとフォームが送信されたときに検証されます。 次の表に、使用できる入力コンポーネントを示します。

| 入力コンポーネント | 表示形式&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

を含む`EditForm`すべての入力コンポーネントは、任意の属性をサポートします。 コンポーネントパラメーターに一致しない属性は、表示される HTML 要素に追加されます。

入力コンポーネントは、編集時に検証を行い、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。 一部のコンポーネントには、役に立つ解析ロジックが含まれています。 たとえば、と`InputDate` `InputNumber`は、検証エラーとして登録することによって、解析不能な値を適切に処理します。 Null 値を受け入れることができる型では、ターゲットフィールドの null 値の`int?`許容もサポートされます (たとえば、)。

次`Starship`の型では、以前`ExampleModel`よりも大きなプロパティとデータ注釈を使用して検証ロジックを定義しています。

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

前の例では`Description` 、データ注釈が存在しないため、は省略可能です。

次のフォームは、 `Starship`モデルで定義されている検証を使用してユーザー入力を検証します。

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

は`EditForm` 、変更`EditContext`されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters)としてを作成します。 また`EditForm` 、は、有効かつ無効な送信 (`OnValidSubmit`, `OnInvalidSubmit`) の便利なイベントも提供します。 または、 `OnSubmit`を使用して、検証フィールドとチェックフィールドの値をカスタム検証コードでトリガーします。

## <a name="inputtext-based-on-the-input-event"></a>入力イベントに基づく InputText

イベントで`InputText`はなく`input`イベントを使用するカスタムコンポーネントを作成するには、コンポーネントを使用します。 `change`

次のマークアップを使用してコンポーネントを作成し、その`InputText`コンポーネントを使用した場合と同じように使用します。

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a>検証のサポート

コンポーネント`DataAnnotationsValidator`は、データ注釈をカスケード`EditContext`に使用して検証サポートをアタッチします。 データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。 データ注釈とは異なる検証システムを使用するには`DataAnnotationsValidator` 、をカスタム実装に置き換えます。 ASP.NET Core の実装は、参照ソースの検査に使用できます。[DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs)。

コンポーネント`ValidationSummary`は、検証の[概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)に似たすべての検証メッセージを要約します。

コンポーネント`ValidationMessage`には、[検証メッセージタグヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似た、特定のフィールドの検証メッセージが表示されます。 `For`属性を使用した検証用のフィールドと、モデルプロパティに名前を付けるラムダ式を指定します。

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

コンポーネント`ValidationMessage` と`ValidationSummary`コンポーネントは、任意の属性をサポートします。 コンポーネントパラメーターに一致しない属性は、生成`<div>`される要素また`<ul>`は要素に追加されます。

### <a name="validation-of-complex-or-collection-type-properties"></a>複合型またはコレクション型のプロパティの検証

モデルのプロパティに適用される検証属性は、フォームが送信されるときに検証されます。 ただし、モデルのコレクションや複合データ型のプロパティは、フォームの送信時に検証されません。 このシナリオで入れ子になった検証属性を使用するには、カスタム検証コンポーネントを使用します。 例については、 [aspnet/Samples GitHub リポジトリの Blazor 検証サンプル](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation)を参照してください。
