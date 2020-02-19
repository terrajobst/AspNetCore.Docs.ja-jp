---
title: ASP.NET Core Blazor のテンプレートコンポーネント
author: guardrex
description: テンプレートコンポーネントが1つまたは複数の UI テンプレートをパラメーターとして受け取ることができるようにする方法について説明します。これは、コンポーネントのレンダリングロジックの一部として使用できます。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b64d6a731e540b13c50b2c6108f75efd0ac9290c
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453152"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="3f973-103">ASP.NET Core Blazor のテンプレートコンポーネント</span><span class="sxs-lookup"><span data-stu-id="3f973-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="3f973-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3f973-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3f973-105">テンプレートコンポーネントは、1つまたは複数の UI テンプレートをパラメーターとして受け取り、コンポーネントのレンダリングロジックの一部として使用できるコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="3f973-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="3f973-106">テンプレート化されたコンポーネントを使用すると、通常のコンポーネントよりも再利用しやすい上位レベルのコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="3f973-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="3f973-107">いくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="3f973-107">A couple of examples include:</span></span>

* <span data-ttu-id="3f973-108">テーブルのヘッダー、行、およびフッターのテンプレートをユーザーが指定できるようにするテーブルコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="3f973-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="3f973-109">リスト内の項目を表示するためのテンプレートをユーザーが指定できるようにするリストコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="3f973-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="3f973-110">Template parameters</span><span class="sxs-lookup"><span data-stu-id="3f973-110">Template parameters</span></span>

<span data-ttu-id="3f973-111">`RenderFragment` または `RenderFragment<T>`型の1つ以上のコンポーネントパラメーターを指定して、テンプレート化されたコンポーネントを定義します。</span><span class="sxs-lookup"><span data-stu-id="3f973-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="3f973-112">レンダーフラグメントは、レンダリングする UI のセグメントを表します。</span><span class="sxs-lookup"><span data-stu-id="3f973-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="3f973-113">`RenderFragment<T>` は、レンダリングフラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="3f973-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="3f973-114">`TableTemplate` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="3f973-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="3f973-115">テンプレート化されたコンポーネントを使用する場合、テンプレートパラメーターは、パラメーターの名前と一致する子要素を使用して指定できます (`TableHeader` と、次の例の `RowTemplate`)。</span><span class="sxs-lookup"><span data-stu-id="3f973-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="template-context-parameters"></a><span data-ttu-id="3f973-116">テンプレートコンテキストパラメーター</span><span class="sxs-lookup"><span data-stu-id="3f973-116">Template context parameters</span></span>

<span data-ttu-id="3f973-117">要素として渡される型 `RenderFragment<T>` のコンポーネント引数には、`context` という名前の暗黙的なパラメーターがあります (たとえば、上記のコードサンプルでは `@context.PetId`)。ただし、子要素の `Context` 属性を使用してパラメーター名を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="3f973-117">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="3f973-118">次の例では、`RowTemplate` 要素の `Context` 属性で `pet` パラメーターを指定しています。</span><span class="sxs-lookup"><span data-stu-id="3f973-118">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="3f973-119">または、コンポーネント要素の `Context` 属性を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="3f973-119">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="3f973-120">指定された `Context` 属性は、指定されたすべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="3f973-120">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="3f973-121">これは、暗黙的な子コンテンツ (ラップする子要素を持たない) のコンテンツパラメーター名を指定する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="3f973-121">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="3f973-122">次の例では、`Context` 属性が `TableTemplate` 要素に表示され、すべてのテンプレートパラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="3f973-122">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="3f973-123">汎用型のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="3f973-123">Generic-typed components</span></span>

<span data-ttu-id="3f973-124">多くの場合、テンプレート化されたコンポーネントは一般的に型指定されます</span><span class="sxs-lookup"><span data-stu-id="3f973-124">Templated components are often generically typed.</span></span> <span data-ttu-id="3f973-125">たとえば、汎用 `ListViewTemplate` コンポーネントを使用して `IEnumerable<T>` 値を表示できます。</span><span class="sxs-lookup"><span data-stu-id="3f973-125">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="3f973-126">ジェネリックコンポーネントを定義するには、 [`@typeparam`](xref:mvc/views/razor#typeparam)ディレクティブを使用して型パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="3f973-126">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="3f973-127">ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。</span><span class="sxs-lookup"><span data-stu-id="3f973-127">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="3f973-128">それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3f973-128">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="3f973-129">次の例では、`TItem="Pet"` 型を指定しています。</span><span class="sxs-lookup"><span data-stu-id="3f973-129">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
