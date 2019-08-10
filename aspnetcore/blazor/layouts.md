---
title: Blazor レイアウトの ASP.NET Core
author: guardrex
description: Blazor アプリ用の再利用可能なレイアウトコンポーネントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/layouts
ms.openlocfilehash: 2d652e149381f0a93e3135da978ab5737d47c6f1
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "68948222"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="2eb04-103">Blazor レイアウトの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2eb04-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="2eb04-104">[Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="2eb04-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="2eb04-105">メニュー、著作権メッセージ、会社のロゴなどの一部のアプリ要素は、通常、アプリの全体的なレイアウトの一部であり、アプリのすべてのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="2eb04-106">これらの要素のコードをアプリのすべてのコンポーネントにコピーすることは、要素&mdash;の1つが更新を必要とするたびに効率的な方法ではありません。すべてのコンポーネントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2eb04-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="2eb04-107">このような重複を維持することは困難であり、時間の経過と共にコンテンツの一貫性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2eb04-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="2eb04-108">*レイアウト*はこの問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="2eb04-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="2eb04-109">技術的には、レイアウトは別のコンポーネントにすぎません。</span><span class="sxs-lookup"><span data-stu-id="2eb04-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="2eb04-110">レイアウトは Razor テンプレートまたはC#コードで定義され、[データバインディング](xref:blazor/components#data-binding)、[依存関係の挿入](xref:blazor/dependency-injection)、およびその他のコンポーネントのシナリオを使用できます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="2eb04-111">*コンポーネント*を*レイアウト*に変換するには、次のコンポーネントを実行します。</span><span class="sxs-lookup"><span data-stu-id="2eb04-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="2eb04-112">を`LayoutComponentBase`継承します。これ`Body`は、レイアウト内に表示されるコンテンツのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="2eb04-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="2eb04-113">Razor 構文`@Body`を使用して、コンテンツがレンダリングされるレイアウトマークアップ内の場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="2eb04-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="2eb04-114">次のコードサンプルは、レイアウトコンポーネントの Razor テンプレートである*Mainlayout*を示しています。</span><span class="sxs-lookup"><span data-stu-id="2eb04-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="2eb04-115">レイアウトは、 `LayoutComponentBase`ナビゲーションバーと`@Body`フッターの間のを継承して設定します。</span><span class="sxs-lookup"><span data-stu-id="2eb04-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="2eb04-116">コンポーネントでのレイアウトの指定</span><span class="sxs-lookup"><span data-stu-id="2eb04-116">Specify a layout in a component</span></span>

<span data-ttu-id="2eb04-117">コンポーネントにレイアウトを`@layout`適用するには、Razor ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="2eb04-117">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="2eb04-118">コンパイラは、 `@layout`を`LayoutAttribute`に変換します。これは、コンポーネントクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-118">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="2eb04-119">次のコンポーネントのコンテンツ ( *masterlist*) が、 `MainLayout`の`@Body`位置に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-119">The content of the following component, *MasterList.razor*, is inserted into the `MainLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a><span data-ttu-id="2eb04-120">一元的なレイアウト選択</span><span class="sxs-lookup"><span data-stu-id="2eb04-120">Centralized layout selection</span></span>

<span data-ttu-id="2eb04-121">アプリのすべてのフォルダーには、必要に応じて、*インポート*という名前のテンプレートファイルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-121">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="2eb04-122">コンパイラには、インポートファイルで指定されたディレクティブが同じフォルダー内のすべての Razor テンプレートに含まれ、そのすべてのサブフォルダーに再帰的に含まれています。</span><span class="sxs-lookup"><span data-stu-id="2eb04-122">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="2eb04-123">したがって、を含む`@layout MainLayout`インポートの razor ファイルを使用すると、フォルダー内のすべてのコンポーネントでが使用`MainLayout`されるようになります。</span><span class="sxs-lookup"><span data-stu-id="2eb04-123">Therefore, an *_Imports.razor* file containing `@layout MainLayout` ensures that all of the components in a folder use `MainLayout`.</span></span> <span data-ttu-id="2eb04-124">フォルダーおよびサブフォルダー内のすべて`@layout MainLayout`の*razor*ファイルに繰り返し追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2eb04-124">There's no need to repeatedly add `@layout MainLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="2eb04-125">`@using`ディレクティブは、同じ方法でコンポーネントにも適用されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-125">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="2eb04-126">次のインポートを実行し*ます。 razor ファイルのインポート (_c)* :</span><span class="sxs-lookup"><span data-stu-id="2eb04-126">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="2eb04-127">`MainLayout`。</span><span class="sxs-lookup"><span data-stu-id="2eb04-127">`MainLayout`.</span></span>
* <span data-ttu-id="2eb04-128">同じフォルダーおよびサブフォルダー内のすべての Razor コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="2eb04-128">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="2eb04-129">`BlazorApp1.Data` 名前空間。</span><span class="sxs-lookup"><span data-stu-id="2eb04-129">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="2eb04-130">Razorファイルは、razor の[ビューとページの _ViewImports ファイル](xref:mvc/views/layout#importing-shared-directives)に似ていますが、razor コンポーネントファイルに特に適用されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-130">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="2eb04-131">Blazor テンプレートでは、レイアウトを選択するために、 *razor*ファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-131">The Blazor templates use *_Imports.razor* files for layout selection.</span></span> <span data-ttu-id="2eb04-132">Blazor テンプレートから作成されたアプリには、プロジェクトのルートと*Pages*フォルダーにある*インポートの razor*ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2eb04-132">An app created from a Blazor template contains the *_Imports.razor* file in the root of the project and in the *Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="2eb04-133">入れ子になったレイアウト</span><span class="sxs-lookup"><span data-stu-id="2eb04-133">Nested layouts</span></span>

<span data-ttu-id="2eb04-134">アプリは、入れ子になったレイアウトで構成できます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-134">Apps can consist of nested layouts.</span></span> <span data-ttu-id="2eb04-135">コンポーネントは、別のレイアウトを参照するレイアウトを参照できます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-135">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="2eb04-136">たとえば、入れ子になったレイアウトは、複数レベルのメニュー構造を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-136">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="2eb04-137">次の例は、入れ子になったレイアウトの使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="2eb04-137">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="2eb04-138">*EpisodesComponent*ファイルは、表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="2eb04-138">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="2eb04-139">コンポーネントはを参照`MasterListLayout`します。</span><span class="sxs-lookup"><span data-stu-id="2eb04-139">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="2eb04-140">*Masterlistlayout. razor*ファイルには、 `MasterListLayout`が用意されています。</span><span class="sxs-lookup"><span data-stu-id="2eb04-140">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="2eb04-141">レイアウトは、表示され`MasterLayout`ている別のレイアウト () を参照します。</span><span class="sxs-lookup"><span data-stu-id="2eb04-141">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="2eb04-142">`EpisodesComponent``@Body`が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-142">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="2eb04-143">最後に`MasterLayout` 、 *masterlayout*には、ヘッダー、メインメニュー、フッターなどの最上位レベルのレイアウト要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2eb04-143">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="2eb04-144">`MasterListLayout`が`EpisodesComponent`表示される`@Body`と、が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2eb04-144">`MasterListLayout` with `EpisodesComponent` are rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="2eb04-145">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2eb04-145">Additional resources</span></span>

* <xref:mvc/views/layout>
