---
title: ASP.NET Core のコンポーネントタグヘルパー
author: guardrex
ms.author: riande
description: ASP.NET Core コンポーネントタグヘルパーを使用して、ページおよびビューで Razor コンポーネントを表示する方法について説明します。
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 801ceb73de5bb4ef7500624e1fbddbf96d1ab89c
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226382"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core のコンポーネントタグヘルパー

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

ページまたはビューからコンポーネントを表示するには、[コンポーネントタグヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)を使用します。

次のコンポーネントタグヘルパーは、ページまたはビューで `Counter` コンポーネントをレンダリングします。

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

コンポーネントタグヘルパーは、コンポーネントにパラメーターを渡すこともできます。 チェックボックスのラベルの色とサイズを設定する次の `ColorfulCheckbox` コンポーネントについて考えてみます。

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

コンポーネントタグヘルパーでは、`Size` (`int`) および `Color` (`string`)[コンポーネントのパラメーター](xref:blazor/components#component-parameters)を設定できます。

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

ページまたはビューには、次の HTML が表示されます。

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

引用符で囲まれた文字列を渡すには、前の例の `param-Color` に示すように、[明示的な Razor 式](xref:mvc/views/razor#explicit-razor-expressions)が必要です。 `string` 型の値に対する Razor 解析動作は、属性が `object` 型であるため、`param-*` 属性には適用されません。

パラメーターは、JSON のシリアル化可能な型である必要があります。これは通常、その型に既定のコンストラクターと設定できるプロパティがある必要があることを意味します。 たとえば、前の例では `Size` と `Color` の値を指定できます。 `Size` と `Color` の型はプリミティブ型 (`int` および `string`) であり、JSON シリアライザーでサポートされています。

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、コンポーネントに対して以下の構成が行われます。

* ページに事前レンダリングするかどうか。
* ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。

| レンダリングモード | 説明 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server アプリのマーカーをレンダリングします。 コンポーネントからの出力は含められません。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | コンポーネントを静的 HTML にレンダリングします。 |

ページとビューはコンポーネントを使用できますが、逆のことはできません。 コンポーネントでは、ビューおよびページ固有の機能 (部分ビューやセクションなど) を使用できません。 コンポーネントの部分ビューのロジックを使用するには、部分ビューのロジックをコンポーネントにします。

静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。

## <a name="additional-resources"></a>その他のリソース

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
