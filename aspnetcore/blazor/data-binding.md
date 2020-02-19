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
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Core Blazor データバインディング

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

コンポーネントと DOM 要素の両方に対するデータバインディングは、 [`@bind`](xref:mvc/views/razor#bind)属性を使用して行われます。 次の例では、`CurrentValue` プロパティをテキストボックスの値にバインドします。

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

テキストボックスがフォーカスを失うと、プロパティの値が更新されます。

テキストボックスは、プロパティの値の変更に対する応答ではなく、コンポーネントがレンダリングされたときにのみ UI で更新されます。 イベントハンドラーのコードを実行した後にコンポーネントがレンダリングされるため、*通常*、イベントハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。

`CurrentValue` プロパティ (`<input @bind="CurrentValue" />`) で `@bind` を使用することは、基本的に次のようなものです。

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。 ユーザーがテキストボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。 実際には、型変換が実行されるケースが `@bind` によって処理されるため、コード生成はより複雑になります。 原則として、`@bind` は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。

`@bind` 構文を使用した `onchange` イベントの処理に加えて、`event` パラメーター ([`@bind-value:event`](xref:mvc/views/razor#bind)) を使用して[`@bind-value`](xref:mvc/views/razor#bind)属性を指定することで、プロパティまたはフィールドを他のイベントを使用してバインドできます。 次の例では、`oninput` イベントの `CurrentValue` プロパティをバインドします。

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキストボックスの値が変更されたときに `oninput` が発生します。

前の例の `@bind-value` は、次のようにバインドします。

* 要素の `value` 属性に対して指定された式 (`CurrentValue`)。
* `@bind-value:event`によって指定されたイベントへの変更イベントデリゲート。

### <a name="unparsable-values"></a>解析不可能値

データバインド要素に解析できない値を指定すると、バインドイベントがトリガーされたときに、解析されていない値が自動的に前の値に戻されます。

以下のシナリオについて考えてみます。

* `<input>` 要素は、初期値 `123`を持つ `int` 型にバインドされます。

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* ユーザーは、要素の値をページの `123.45` に更新し、要素のフォーカスを変更します。

前のシナリオでは、要素の値が `123`に戻されます。 `123`の元の値を優先して `123.45` 値が拒否された場合、ユーザーはその値が受け入れられていないことを認識します。

既定では、バインドは要素の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) に適用されます。 別のイベントを設定するには、`@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` を使用します。 `oninput` イベント (`@bind-value:event="oninput"`) の場合、解析できない値を導入するキーストロークの後に再設定が発生します。 `int`バインドされた型の `oninput` イベントを対象とする場合、ユーザーは `.` 文字を入力できません。 `.` 文字はすぐに削除されるので、ユーザーは、整数のみが許可されるというフィードバックをすぐに受け取ることができます。 `oninput` イベントの値を元に戻すことは、ユーザーが解析できない `<input>` 値のクリアを許可する必要がある場合など、理想的ではありません。 代替手段は次のとおりです。

* `oninput` イベントは使用しないでください。 既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"`) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。
* `int?` や `string`などの null 許容型にバインドし、無効なエントリを処理するカスタムロジックを提供します。
* `InputNumber` や `InputDate`などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。 フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。 フォーム検証コンポーネント:
  * ユーザーが無効な入力を提供し、関連付けられた `EditContext`で検証エラーを受信することを許可します。
  * 追加の web フォームデータを入力するユーザーに干渉することなく、UI に検証エラーを表示します。

### <a name="format-strings"></a>書式指定文字列

データバインディングは、 [`@bind:format`](xref:mvc/views/razor#bind)を使用して <xref:System.DateTime> 書式指定文字列で動作します。 通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

前のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text`に設定されています。 `@bind:format` は、次の .NET 型のバインドに対してサポートされています。

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 属性は、`<input>` 要素の `value` に適用する日付形式を指定します。 この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。

Blazor に日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。 推奨事項では、`date` フィールドの種類で形式が指定されている場合、バインドの `yyyy-MM-dd` 日付形式のみを使用することをお勧めします。

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a>コンポーネントパラメーターを使用した親子バインド

バインディングは、コンポーネントのパラメーターを認識します。 `@bind-{property}` は、親コンポーネントのプロパティ値を子コンポーネントにバインドできます。 子から親へのバインドについては、「[チェーンバインドを使用した子から親へのバインド](#child-to-parent-binding-with-chained-bind)」を参照してください。

次の子コンポーネント (`ChildComponent`) には、`Year` コンポーネントパラメーターと `YearChanged` コールバックがあります。

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

`EventCallback<T>` については <xref:blazor/event-handling#eventcallback>をご説明します。

次の親コンポーネントはを使用します。

* を `ChildComponent` し、親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。
* `onclick` イベントは、`ChangeTheYear` メソッドをトリガーするために使用されます。 詳細については、<xref:blazor/event-handling> を参照してください。

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

`ParentComponent` を読み込むと、次のマークアップが生成されます。

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentComponent`のボタンを選択して `ParentYear` プロパティの値が変更された場合は、`ChildComponent` の `Year` プロパティが更新されます。 `Year` の新しい値は、`ParentComponent` が再度実行されたときに UI に表示されます。

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` パラメーターは、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。

慣例により、`<ChildComponent @bind-Year="ParentYear" />` は基本的には書き込みと同じです。

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

一般に、プロパティは、`@bind-property:event` 属性を使用して、対応するイベントハンドラーにバインドできます。 たとえば、プロパティ `MyProp` は、次の2つの属性を使用して `MyEventHandler` にバインドできます。

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a>チェーンバインドを使用した親子バインド

一般的なシナリオでは、データバインドパラメーターをコンポーネントの出力のページ要素に連結します。 このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーンバインド*と呼ばれます。

チェーンバインドは、ページの要素で `@bind` 構文を使用して実装することはできません。 イベントハンドラーと値は、個別に指定する必要があります。 ただし、親コンポーネントでは、`@bind` 構文をコンポーネントのパラメーターと共に使用できます。

次の `PasswordField` コンポーネント (*Passwordfield*):

* `Password` プロパティに `<input>` 要素の値を設定します。
* [Eventcallback](xref:blazor/event-handling#eventcallback)を使用して、`Password` プロパティの変更を親コンポーネントに公開します。
* `onclick` イベントを使用して、`ToggleShowPassword` メソッドをトリガーします。 詳細については、<xref:blazor/event-handling> を参照してください。

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

`PasswordField` コンポーネントが別のコンポーネントで使用されています。

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

前の例で、パスワードの確認またはトラップエラーを実行するには、次の手順を実行します。

* `Password` のバッキングフィールドを作成します (次のコード例では`_password`)。
* `Password` setter で、チェックまたはトラップのエラーを実行します。

次の例では、パスワードの値にスペースが使用されている場合に、ユーザーにすぐにフィードバックを提供します。

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

### <a name="radio-buttons"></a>オプション ボタン

フォームのオプションボタンへのバインドの詳細については、「<xref:blazor/forms-validation#work-with-radio-buttons>」を参照してください。
