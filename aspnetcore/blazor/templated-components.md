---
title: ASP.NET Core Blazor テンプレート コンポーネント
author: guardrex
description: テンプレート コンポーネントで 1 つまたは複数の UI テンプレートをパラメーターとして受け取る方法について学習します。これは、コンポーネントのレンダリング ロジックの一部として使用できます。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989495"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="21fb5-103">ASP.NET Core Blazor テンプレート コンポーネント</span><span class="sxs-lookup"><span data-stu-id="21fb5-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="21fb5-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="21fb5-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="21fb5-105">テンプレート コンポーネントは、1 つまたは複数の UI テンプレートをパラメーターとして受け取るコンポーネントです。その後、コンポーネントのレンダリング ロジックの一部として使用できます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="21fb5-106">テンプレート コンポーネントを使用すると、通常のコンポーネントよりも再利用しやすい上位レベルのコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="21fb5-107">いくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="21fb5-107">A couple of examples include:</span></span>

* <span data-ttu-id="21fb5-108">テーブルのヘッダー、行、フッターのテンプレートをユーザーが指定できるようにするテーブル コンポーネント</span><span class="sxs-lookup"><span data-stu-id="21fb5-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="21fb5-109">リスト内の項目をレンダリングするためのテンプレートをユーザーが指定できるようにするリスト コンポーネント</span><span class="sxs-lookup"><span data-stu-id="21fb5-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="21fb5-110">テンプレート パラメーター</span><span class="sxs-lookup"><span data-stu-id="21fb5-110">Template parameters</span></span>

<span data-ttu-id="21fb5-111">`RenderFragment` または `RenderFragment<T>` の型の 1 つまたは複数のコンポーネント パラメーターを指定することで、テンプレート コンポーネントが定義されます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="21fb5-112">レンダー フラグメントは、レンダリングする UI のセグメントを表します。</span><span class="sxs-lookup"><span data-stu-id="21fb5-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="21fb5-113">`RenderFragment<T>` は、レンダー フラグメントが呼び出されたときに指定できる型パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="21fb5-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="21fb5-114">`TableTemplate` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="21fb5-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="21fb5-115">テンプレート コンポーネントを使用する場合、テンプレート パラメーターは、パラメーターの名前と一致する子要素を使用して指定できます (次の例では `TableHeader` と `RowTemplate`)。</span><span class="sxs-lookup"><span data-stu-id="21fb5-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

> [!NOTE]
> <span data-ttu-id="21fb5-116">ジェネリック型の制約は、将来のリリースでサポートされる予定です。</span><span class="sxs-lookup"><span data-stu-id="21fb5-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="21fb5-117">詳細については、[ジェネリック型の制約の許可 (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="21fb5-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="21fb5-118">テンプレート コンテキスト パラメーター</span><span class="sxs-lookup"><span data-stu-id="21fb5-118">Template context parameters</span></span>

<span data-ttu-id="21fb5-119">要素として渡される型 `RenderFragment<T>` のコンポーネント引数には、`context` という名前の暗黙的なパラメーターがありますが (たとえば、上記のコード サンプルの `@context.PetId`)、子要素の `Context` 属性を使用してパラメーター名を変更できます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="21fb5-120">次の例では、`RowTemplate` 要素の `Context` 属性で `pet` パラメーターを指定しています。</span><span class="sxs-lookup"><span data-stu-id="21fb5-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="21fb5-121">または、コンポーネント要素の `Context` 属性を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="21fb5-122">指定された `Context` 属性は、指定されたすべてのテンプレート パラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="21fb5-123">これは、(ラップする子要素を持たない) 暗黙的な子コンテンツのコンテンツ パラメーター名を指定する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="21fb5-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="21fb5-124">次の例では、`Context` 属性が `TableTemplate` 要素に表示され、すべてのテンプレート パラメーターに適用されます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="21fb5-125">ジェネリック型のコンポーネント</span><span class="sxs-lookup"><span data-stu-id="21fb5-125">Generic-typed components</span></span>

<span data-ttu-id="21fb5-126">多くの場合、テンプレート コンポーネントはジェネリック型です。</span><span class="sxs-lookup"><span data-stu-id="21fb5-126">Templated components are often generically typed.</span></span> <span data-ttu-id="21fb5-127">たとえば、ジェエリックの `ListViewTemplate` コンポーネントを使用して、`IEnumerable<T>` 値をレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="21fb5-128">ジェネリック コンポーネントを定義するには、[`@typeparam`](xref:mvc/views/razor#typeparam) ディレクティブを使用して、型パラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="21fb5-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="21fb5-129">ジェネリック型のコンポーネントを使用する場合、可能であれば型パラメーターは推論されます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="21fb5-130">それ以外の場合は、型パラメーターの名前と一致する属性を使用して、型パラメーターを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="21fb5-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="21fb5-131">次の例では、`TItem="Pet"` によって次の型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="21fb5-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
