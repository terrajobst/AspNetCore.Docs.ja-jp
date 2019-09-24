---
title: Blazor レイアウトの ASP.NET Core
author: guardrex
description: Blazor アプリ用の再利用可能なレイアウトコンポーネントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/layouts
ms.openlocfilehash: 064ffafb8e7f3e76e5bd7bde0e06ef271fe92542
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211587"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="349d2-103">Blazor レイアウトの ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="349d2-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="349d2-104">By [Rainer Stropek](https://www.timecockpit.com)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="349d2-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="349d2-105">メニュー、著作権メッセージ、会社のロゴなどの一部のアプリ要素は、通常、アプリの全体的なレイアウトの一部であり、アプリのすべてのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="349d2-106">これらの要素のコードをアプリのすべてのコンポーネントにコピーすることは、要素&mdash;の1つが更新を必要とするたびに効率的な方法ではありません。すべてのコンポーネントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="349d2-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="349d2-107">このような重複を維持することは困難であり、時間の経過と共にコンテンツの一貫性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="349d2-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="349d2-108">*レイアウト*はこの問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="349d2-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="349d2-109">技術的には、レイアウトは別のコンポーネントにすぎません。</span><span class="sxs-lookup"><span data-stu-id="349d2-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="349d2-110">レイアウトは Razor テンプレートまたはC#コードで定義され、[データバインディング](xref:blazor/components#data-binding)、[依存関係の挿入](xref:blazor/dependency-injection)、およびその他のコンポーネントのシナリオを使用できます。</span><span class="sxs-lookup"><span data-stu-id="349d2-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="349d2-111">*コンポーネント*を*レイアウト*に変換するには、次のコンポーネントを実行します。</span><span class="sxs-lookup"><span data-stu-id="349d2-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="349d2-112">を`LayoutComponentBase`継承します。これ`Body`は、レイアウト内に表示されるコンテンツのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="349d2-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="349d2-113">Razor 構文`@Body`を使用して、コンテンツがレンダリングされるレイアウトマークアップ内の場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="349d2-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="349d2-114">次のコードサンプルは、レイアウトコンポーネントの Razor テンプレートである*Mainlayout*を示しています。</span><span class="sxs-lookup"><span data-stu-id="349d2-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="349d2-115">レイアウトは、 `LayoutComponentBase`ナビゲーションバーと`@Body`フッターの間のを継承して設定します。</span><span class="sxs-lookup"><span data-stu-id="349d2-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="349d2-116">Blazor アプリテンプレート`MainLayout`のいずれかに基づくアプリでは、コンポーネント (*mainlayout. razor*) はアプリの*共有*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="349d2-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="349d2-117">既定のレイアウト</span><span class="sxs-lookup"><span data-stu-id="349d2-117">Default layout</span></span>

<span data-ttu-id="349d2-118">アプリの*アプリケーションの razor*ファイルで`Router` 、コンポーネントの既定のアプリレイアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="349d2-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="349d2-119">既定の`Router` Blazor テンプレートによって提供される次のコンポーネントを使用`MainLayout`すると、既定のレイアウトがコンポーネントに設定されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="349d2-120">コンテンツの`NotFound`既定のレイアウトを指定するには、 `NotFound`コンテンツに対してを`LayoutView`指定します。</span><span class="sxs-lookup"><span data-stu-id="349d2-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="349d2-121">`Router`コンポーネントの詳細については、 <xref:blazor/routing>「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="349d2-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="349d2-122">ルーターでレイアウトを既定のレイアウトとして指定することは、コンポーネントごとまたはフォルダーごとにオーバーライドできるため、便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="349d2-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="349d2-123">最も一般的な方法であるため、ルーターを使用してアプリの既定のレイアウトを設定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="349d2-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="349d2-124">コンポーネントでのレイアウトの指定</span><span class="sxs-lookup"><span data-stu-id="349d2-124">Specify a layout in a component</span></span>

<span data-ttu-id="349d2-125">コンポーネントにレイアウトを`@layout`適用するには、Razor ディレクティブを使用します。</span><span class="sxs-lookup"><span data-stu-id="349d2-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="349d2-126">コンパイラは、 `@layout`を`LayoutAttribute`に変換します。これは、コンポーネントクラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="349d2-127">次`MasterList`のコンポーネントの内容は、 `MasterLayout`の`@Body`位置でに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="349d2-128">コンポーネントにレイアウトを直接指定すると、ルーターの*既定のレイアウト*セットや`@layout` 、*インポート*からインポートされたディレクティブはオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="349d2-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="349d2-129">一元的なレイアウト選択</span><span class="sxs-lookup"><span data-stu-id="349d2-129">Centralized layout selection</span></span>

<span data-ttu-id="349d2-130">アプリのすべてのフォルダーには、必要に応じて、*インポート*という名前のテンプレートファイルを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="349d2-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="349d2-131">コンパイラには、インポートファイルで指定されたディレクティブが同じフォルダー内のすべての Razor テンプレートに含まれ、そのすべてのサブフォルダーに再帰的に含まれています。</span><span class="sxs-lookup"><span data-stu-id="349d2-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="349d2-132">したがって、を含む`@layout MyCoolLayout`インポートの razor ファイルを使用すると、フォルダー内のすべてのコンポーネントでが使用`MyCoolLayout`されるようになります。</span><span class="sxs-lookup"><span data-stu-id="349d2-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="349d2-133">フォルダーおよびサブフォルダー内のすべて`@layout MyCoolLayout`の*razor*ファイルに繰り返し追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="349d2-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="349d2-134">`@using`ディレクティブは、同じ方法でコンポーネントにも適用されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="349d2-135">次のインポートを実行し*ます。 razor ファイルのインポート (_c)* :</span><span class="sxs-lookup"><span data-stu-id="349d2-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="349d2-136">`MyCoolLayout`。</span><span class="sxs-lookup"><span data-stu-id="349d2-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="349d2-137">同じフォルダーおよびサブフォルダー内のすべての Razor コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="349d2-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="349d2-138">`BlazorApp1.Data` 名前空間。</span><span class="sxs-lookup"><span data-stu-id="349d2-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="349d2-139">Razor*ファイルは*、razor の[ビューとページの _ViewImports ファイル](xref:mvc/views/layout#importing-shared-directives)に似ていますが、razor コンポーネントファイルに特に適用されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="349d2-140">インポートでレイアウトを指定すると、ルーターの*既定のレイアウト*として指定されたレイアウトが上書きされ*ます*。</span><span class="sxs-lookup"><span data-stu-id="349d2-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="349d2-141">入れ子になったレイアウト</span><span class="sxs-lookup"><span data-stu-id="349d2-141">Nested layouts</span></span>

<span data-ttu-id="349d2-142">アプリは、入れ子になったレイアウトで構成できます。</span><span class="sxs-lookup"><span data-stu-id="349d2-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="349d2-143">コンポーネントは、別のレイアウトを参照するレイアウトを参照できます。</span><span class="sxs-lookup"><span data-stu-id="349d2-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="349d2-144">たとえば、入れ子になったレイアウトは、複数レベルのメニュー構造を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="349d2-145">次の例は、入れ子になったレイアウトの使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="349d2-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="349d2-146">*EpisodesComponent*ファイルは、表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="349d2-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="349d2-147">コンポーネントはを参照`MasterListLayout`します。</span><span class="sxs-lookup"><span data-stu-id="349d2-147">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="349d2-148">*Masterlistlayout. razor*ファイルには、 `MasterListLayout`が用意されています。</span><span class="sxs-lookup"><span data-stu-id="349d2-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="349d2-149">レイアウトは、表示され`MasterLayout`ている別のレイアウト () を参照します。</span><span class="sxs-lookup"><span data-stu-id="349d2-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="349d2-150">`EpisodesComponent``@Body`が表示されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="349d2-151">最後に`MasterLayout` 、 *masterlayout*には、ヘッダー、メインメニュー、フッターなどの最上位レベルのレイアウト要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="349d2-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="349d2-152">`MasterListLayout`が表示`EpisodesComponent`される`@Body`と、が表示されます。</span><span class="sxs-lookup"><span data-stu-id="349d2-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="349d2-153">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="349d2-153">Additional resources</span></span>

* <xref:mvc/views/layout>
