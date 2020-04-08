---
title: ASP.NET Core Blazor レイアウト
author: guardrex
description: Blazor アプリの再利用可能なレイアウト コンポーネントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647924"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="dba3c-103">ASP.NET Core Blazor レイアウト</span><span class="sxs-lookup"><span data-stu-id="dba3c-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="dba3c-104">作成者: [Rainer Stropek](https://www.timecockpit.com)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dba3c-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dba3c-105">メニュー、著作権メッセージ、会社のロゴなどの一部のアプリ要素は、通常、アプリの全体のレイアウトの一部であり、アプリのすべてのコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="dba3c-106">これらの要素のコードをアプリのすべてのコンポーネントにコピーするのは、効率的な方法ではありません &mdash; 要素の 1 つで更新が必要になるたびに、すべてのコンポーネントを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dba3c-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="dba3c-107">このような複製を維持することは困難であり、時間の経過と共にコンテンツの一貫性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dba3c-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="dba3c-108">*レイアウト*によって、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="dba3c-109">技術的に、レイアウトはもう 1 つのコンポーネントにすぎません。</span><span class="sxs-lookup"><span data-stu-id="dba3c-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="dba3c-110">レイアウトは Razor テンプレートまたは C# コードで定義され、[データ バインディング](xref:blazor/data-binding)、[依存関係の挿入](xref:blazor/dependency-injection)、およびその他のコンポーネント シナリオを使用できます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="dba3c-111">*コンポーネント*を*レイアウト*に変えるには、コンポーネントが:</span><span class="sxs-lookup"><span data-stu-id="dba3c-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="dba3c-112">レイアウト内のレンダリングされるコンテンツの `LayoutComponentBase` プロパティを定義する `Body` から継承している。</span><span class="sxs-lookup"><span data-stu-id="dba3c-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="dba3c-113">Razor 構文 `@Body` を使用して、コンテンツがレンダリングされるレイアウト マークアップ内の場所を指定している。</span><span class="sxs-lookup"><span data-stu-id="dba3c-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="dba3c-114">次のコード サンプルに、レイアウト コンポーネント *MainLayout.razor* の Razor テンプレートを示します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="dba3c-115">レイアウトは `LayoutComponentBase` を継承し、ナビゲーション バーとフッターの間に `@Body` を設定します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="dba3c-116">Blazor アプリ テンプレートのいずれかに基づくアプリでは、`MainLayout` コンポーネント (*MainLayout.razor*) は、アプリの*共有*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="dba3c-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="dba3c-117">既定のレイアウト</span><span class="sxs-lookup"><span data-stu-id="dba3c-117">Default layout</span></span>

<span data-ttu-id="dba3c-118">アプリの `Router`App.razor*ファイル内の* コンポーネントに、既定のアプリ レイアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="dba3c-119">既定の `Router` テンプレートによって提供される次の Blazor コンポーネントは、既定のレイアウトを `MainLayout` コンポーネントに設定します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="dba3c-120">`NotFound` コンテンツの既定のレイアウトを指定するには、`LayoutView` コンテンツの `NotFound` を指定します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="dba3c-121">`Router` コンポーネントの詳細については、「<xref:blazor/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba3c-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="dba3c-122">ルーターでレイアウトを既定のレイアウトとして指定することは、コンポーネントごとまたはフォルダーごとにオーバーライドできるため、便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="dba3c-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="dba3c-123">ルーターを使用してアプリの既定のレイアウトを設定することは、最も一般的な技法であるため、お勧めします。</span><span class="sxs-lookup"><span data-stu-id="dba3c-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="dba3c-124">コンポーネントにレイアウトを指定する</span><span class="sxs-lookup"><span data-stu-id="dba3c-124">Specify a layout in a component</span></span>

<span data-ttu-id="dba3c-125">コンポーネントにレイアウトを適用するには、Razor ディレクティブ `@layout` を使用します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="dba3c-126">コンパイラでは `@layout` を `LayoutAttribute` に変換します。これはコンポーネント クラスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="dba3c-127">次の `MasterList` コンポーネントのコンテンツは、`MasterLayout` の位置にある `@Body` に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="dba3c-128">コンポーネントに直接レイアウトを指定すると、ルーターに設定された*既定のレイアウトまたは* _Imports.razor`@layout` からインポートされた  *ディレクティブ*がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="dba3c-129">一元的なレイアウトの選択</span><span class="sxs-lookup"><span data-stu-id="dba3c-129">Centralized layout selection</span></span>

<span data-ttu-id="dba3c-130">アプリのすべてのフォルダーには、必要に応じて、 *_Imports.razor* という名前のテンプレート ファイルを格納できます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="dba3c-131">コンパイラにより、インポート ファイルに指定されたディレクティブが、同じフォルダー内とそのすべてのサブフォルダー内で再帰的にすべての Razor テンプレートに含まれます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="dba3c-132">そのため、*を含む*_Imports.razor`@layout MyCoolLayout` ファイルにより、フォルダー内のすべてのコンポーネントで `MyCoolLayout` が確実に使用されます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="dba3c-133">フォルダーおよびサブフォルダー内のすべての `@layout MyCoolLayout`.razor*ファイルに* を繰り返し追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="dba3c-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="dba3c-134">`@using` ディレクティブは、同じようにコンポーネントにも適用されます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="dba3c-135">次の *_Imports.razor* ファイルでは、次のものをインポートします。</span><span class="sxs-lookup"><span data-stu-id="dba3c-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="dba3c-136">[https://login.microsoftonline.com/consumers/](`MyCoolLayout`)</span><span class="sxs-lookup"><span data-stu-id="dba3c-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="dba3c-137">同じフォルダーおよびサブフォルダー内のすべての Razor コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="dba3c-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="dba3c-138">`BlazorApp1.Data` 名前空間。</span><span class="sxs-lookup"><span data-stu-id="dba3c-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="dba3c-139">*_Imports.razor* ファイルは、[Razor ビューおよびページに対する _ViewImports.cshtml ファイル](xref:mvc/views/layout#importing-shared-directives)に似ていますが、Razor コンポーネント ファイルに限定して適用されます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="dba3c-140">*_Imports.razor* にレイアウトを指定すると、ルーターの*既定のレイアウト*として指定されたレイアウトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="dba3c-141">入れ子になったレイアウト</span><span class="sxs-lookup"><span data-stu-id="dba3c-141">Nested layouts</span></span>

<span data-ttu-id="dba3c-142">アプリは、入れ子になったレイアウトで構成できます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="dba3c-143">コンポーネントでは、別のレイアウトを参照するレイアウトを参照できます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="dba3c-144">たとえば、複数レベルのメニュー構造を作成するために、レイアウトの入れ子を使用します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="dba3c-145">次の例に、入れ子になったレイアウトの使用方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="dba3c-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="dba3c-146">*EpisodesComponent.razor* ファイルは、表示するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="dba3c-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="dba3c-147">コンポーネントは `MasterListLayout` を参照しています。</span><span class="sxs-lookup"><span data-stu-id="dba3c-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="dba3c-148">*MasterListLayout. razor* ファイルは `MasterListLayout` を提供します。</span><span class="sxs-lookup"><span data-stu-id="dba3c-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="dba3c-149">このレイアウトは、それがレンダリングされる別のレイアウト `MasterLayout` を参照しています。</span><span class="sxs-lookup"><span data-stu-id="dba3c-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="dba3c-150">`EpisodesComponent` は、`@Body` が表示される場所にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="dba3c-151">最後に、`MasterLayout`MasterLayout.razor*内の* に、ヘッダー、メイン メニュー、フッターなどの最上位レイアウト要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="dba3c-152">`MasterListLayout` を含む `EpisodesComponent` は、`@Body` が表示される場所にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="dba3c-153">統合コンポーネントと Razor Pages レイアウトを共有する</span><span class="sxs-lookup"><span data-stu-id="dba3c-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="dba3c-154">ルーティング可能なコンポーネントが Razor Pages アプリに統合されている場合、コンポーネントでアプリの共有レイアウトを使用できます。</span><span class="sxs-lookup"><span data-stu-id="dba3c-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="dba3c-155">詳細については、「<xref:blazor/integrate-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dba3c-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dba3c-156">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dba3c-156">Additional resources</span></span>

* <xref:mvc/views/layout>
