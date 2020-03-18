---
title: クラス ライブラリで ASP.NET Core API を使用する
author: scottaddie
description: クラス ライブラリで ASP.NET Core API を使用する方法を説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 72096fc2f03033dfe8325b5129e074913a2fbd1f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646688"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="e8db7-103">クラス ライブラリで ASP.NET Core API を使用する</span><span class="sxs-lookup"><span data-stu-id="e8db7-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="e8db7-104">作成者: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e8db7-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e8db7-105">このドキュメントでは、クラス ライブラリで ASP.NET Core API を使用するためのガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="e8db7-106">他のすべてのライブラリ ガイダンスについては、[オープン ソース ライブラリのガイダンス](/dotnet/standard/library-guidance/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8db7-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="e8db7-107">サポートする ASP.NET Core のバージョンを決定する</span><span class="sxs-lookup"><span data-stu-id="e8db7-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="e8db7-108">ASP.NET Core は、[.NET Core サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)に準拠しています。</span><span class="sxs-lookup"><span data-stu-id="e8db7-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="e8db7-109">ライブラリでサポートする ASP.NET Core のバージョンを決定する際は、このサポート ポリシーを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8db7-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="e8db7-110">ライブラリは次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-110">A library should:</span></span>

* <span data-ttu-id="e8db7-111">可能な限り、*長期サポート* (LTS) として分類されている ASP.NET Core バージョンをすべてサポートします。</span><span class="sxs-lookup"><span data-stu-id="e8db7-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="e8db7-112">*サポート終了* (EOL) として分類されている ASP.NET Core バージョンをサポートする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e8db7-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="e8db7-113">ASP.NET Core のプレビュー リリースが利用可能になると、破壊的変更が [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub リポジトリに投稿されます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="e8db7-114">ライブラリの互換性テストは、フレームワーク機能の開発中に実施できます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="e8db7-115">ASP.NET Core 共有フレームワークの使用</span><span class="sxs-lookup"><span data-stu-id="e8db7-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="e8db7-116">.NET Core 3.0 のリリースから、多数の ASP.NET Core アセンブリがパッケージとして NuGet に公開されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e8db7-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="e8db7-117">代わりに、アセンブリは `Microsoft.AspNetCore.App` 共有フレームワークに含まれ、.NET Core SDK およびランタイム インストーラーと共にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="e8db7-118">公開されなくなったパッケージの一覧については、「[古いパッケージ参照の削除](xref:migration/22-to-30#remove-obsolete-package-references)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8db7-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="e8db7-119">.NET Core 3.0 から、`Microsoft.NET.Sdk.Web` MSBuild SDK を使用するプロジェクトは、共有フレームワークを暗黙的に参照します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="e8db7-120">`Microsoft.NET.Sdk` SDK または `Microsoft.NET.Sdk.Razor` SDK を使用するプロジェクトで共有フレームワーク内の ASP.NET Core API を使用するには、ASP.NET Core を参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="e8db7-121">ASP.NET Core を参照するには、次の `<FrameworkReference>` 要素をプロジェクト ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="e8db7-122">ASP.NET Core を参照するためのこの方法は、.NET Core 3.x を対象とするプロジェクトでのみサポートされます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="e8db7-123">Blazor 拡張機能を組み込む</span><span class="sxs-lookup"><span data-stu-id="e8db7-123">Include Blazor extensibility</span></span>

<span data-ttu-id="e8db7-124">Blazor では、WebAssembly (WASM) とサーバーの[ホスティング モデル](xref:blazor/hosting-models)がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="e8db7-125">特別な理由がない限り、[Razor コンポーネント](xref:blazor/components)は両方のホスティング モデルをサポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="e8db7-126">Razor コンポーネント ライブラリは、[Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="e8db7-127">両方のホスティング モデルをサポートする</span><span class="sxs-lookup"><span data-stu-id="e8db7-127">Support both hosting models</span></span>

<span data-ttu-id="e8db7-128">[Blazor サーバー](xref:blazor/hosting-models#blazor-server) プロジェクトと [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) プロジェクトの両方で Razor コンポーネントの使用をサポートするには、エディターに応じて次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8db7-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8db7-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e8db7-130">**Razor クラス ライブラリ** プロジェクト テンプレートを使用します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="e8db7-131">このテンプレートの **[Support pages and views]\(ページとビューのサポート\)** チェックボックスの選択を解除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e8db7-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e8db7-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e8db7-133">統合ターミナルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e8db7-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e8db7-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e8db7-135">**Razor クラス ライブラリ** プロジェクト テンプレートを使用します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="e8db7-136">このテンプレートから生成されるプロジェクトは、次の操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="e8db7-137">.NET Standard 2.0 を対象とします。</span><span class="sxs-lookup"><span data-stu-id="e8db7-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="e8db7-138">`RazorLangVersion` プロパティを `3.0` に設定します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="e8db7-139">`3.0` は、.NET Core 3.x の既定値です。</span><span class="sxs-lookup"><span data-stu-id="e8db7-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="e8db7-140">次のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="e8db7-141">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="e8db7-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="e8db7-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="e8db7-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="e8db7-143">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="e8db7-144">特定のホスティング モデルをサポートする</span><span class="sxs-lookup"><span data-stu-id="e8db7-144">Support a specific hosting model</span></span>

<span data-ttu-id="e8db7-145">1 つの Blazor ホスティング モデルだけをサポートするのは、あまり一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="e8db7-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="e8db7-146">たとえば、[Blazor サーバー](xref:blazor/hosting-models#blazor-server) プロジェクトでのみ Razor コンポーネントの使用をサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e8db7-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="e8db7-147">.NET Core 3.x を対象とします。</span><span class="sxs-lookup"><span data-stu-id="e8db7-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="e8db7-148">共有フレームワークの `<FrameworkReference>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="e8db7-149">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="e8db7-150">Razor コンポーネントを含むライブラリの詳細については、「[ASP.NET Core Razor components class libraries](xref:blazor/class-libraries)」 (ASP.NET Core Razor コンポーネント クラス ライブラリ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8db7-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="e8db7-151">MVC 拡張機能を含める</span><span class="sxs-lookup"><span data-stu-id="e8db7-151">Include MVC extensibility</span></span>

<span data-ttu-id="e8db7-152">このセクションでは、次のものを含むライブラリの推奨事項について概要を説明します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="e8db7-153">Razor ビューまたは Razor ページ</span><span class="sxs-lookup"><span data-stu-id="e8db7-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="e8db7-154">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="e8db7-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="e8db7-155">ビューのコンポーネント</span><span class="sxs-lookup"><span data-stu-id="e8db7-155">View components</span></span>](#view-components)

<span data-ttu-id="e8db7-156">このセクションでは、MVC の複数のバージョンをサポートするマルチターゲット機能については説明しません。</span><span class="sxs-lookup"><span data-stu-id="e8db7-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="e8db7-157">複数の ASP.NET Core バージョンのサポートに関するガイダンスについては、「[複数のバージョンの ASP.NET Core をサポートする](#support-multiple-aspnet-core-versions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8db7-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="e8db7-158">Razor ビューまたは Razor ページ</span><span class="sxs-lookup"><span data-stu-id="e8db7-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="e8db7-159">[Razor ビュー](xref:mvc/views/overview)または [Razor ページ](xref:razor-pages/index)を含むプロジェクトでは、[Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk) を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="e8db7-160">プロジェクトが .NET Core 3.x を対象とする場合、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="e8db7-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="e8db7-161">`true` に設定された `AddRazorSupportForMvc` MSBuild プロパティ。</span><span class="sxs-lookup"><span data-stu-id="e8db7-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="e8db7-162">共有フレームワークの `<FrameworkReference>` 要素。</span><span class="sxs-lookup"><span data-stu-id="e8db7-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="e8db7-163">**Razor クラス ライブラリ** プロジェクト テンプレートは、.NET Core 3.x を対象とするプロジェクトについて、前述の要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="e8db7-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="e8db7-164">ご使用のエディターに応じて、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e8db7-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8db7-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e8db7-166">**Razor クラス ライブラリ** プロジェクト テンプレートを使用します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="e8db7-167">このテンプレートの **[Support pages and views]\(ページとビューのサポート\)** チェックボックスをオンにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e8db7-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e8db7-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e8db7-169">統合ターミナルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e8db7-170">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e8db7-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e8db7-171">現時点では、サポートされているプロジェクト テンプレートはありません。</span><span class="sxs-lookup"><span data-stu-id="e8db7-171">No project template support at this time.</span></span>

---

<span data-ttu-id="e8db7-172">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="e8db7-173">プロジェクトが代わりに NET Standard を対象とする場合、[Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) パッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="e8db7-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="e8db7-174">`Microsoft.AspNetCore.Mvc` パッケージは ASP.NET Core 3.0 で共有フレームワークに移動されたため、公開されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e8db7-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="e8db7-175">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="e8db7-176">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="e8db7-176">Tag Helpers</span></span>

<span data-ttu-id="e8db7-177">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を含むプロジェクトでは、`Microsoft.NET.Sdk` SDK を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="e8db7-178">.NET Core 3.x を対象とする場合、共有フレームワークの `<FrameworkReference>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="e8db7-179">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="e8db7-180">.NET Standard を対象とする場合 (ASP.NET Core 3.x より前のバージョンをサポートするため)、[Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="e8db7-181">`Microsoft.AspNetCore.Mvc.Razor` パッケージは共有フレームワークに移動されたため、公開されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e8db7-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="e8db7-182">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="e8db7-183">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="e8db7-183">View components</span></span>

<span data-ttu-id="e8db7-184">[ビュー コンポーネント](xref:mvc/views/view-components)を含むプロジェクトでは、`Microsoft.NET.Sdk` SDK を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="e8db7-185">.NET Core 3.x を対象とする場合、共有フレームワークの `<FrameworkReference>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="e8db7-186">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="e8db7-187">.NET Standard を対象とする場合 (ASP.NET Core 3.x より前のバージョンをサポートするため)、[Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="e8db7-188">`Microsoft.AspNetCore.Mvc.ViewFeatures` パッケージは共有フレームワークに移動されたため、公開されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e8db7-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="e8db7-189">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="e8db7-190">複数のバージョンの ASP.NET Core をサポートする</span><span class="sxs-lookup"><span data-stu-id="e8db7-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="e8db7-191">ASP.NET Core の複数のバリアントをサポートするライブラリを作成するには、マルチターゲット機能が必要です。</span><span class="sxs-lookup"><span data-stu-id="e8db7-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="e8db7-192">タグ ヘルパー ライブラリで ASP.NET Core の次のバリアントをサポートする必要があるシナリオについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="e8db7-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="e8db7-193">.NET Framework 4.6.1 を対象とする ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="e8db7-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="e8db7-194">.NET Core 2.x を対象とする ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8db7-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="e8db7-195">.NET Core 3.x を対象とする ASP.NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="e8db7-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="e8db7-196">次のプロジェクト ファイルでは、`TargetFrameworks` プロパティを使用してこれらのバリアントをサポートします。</span><span class="sxs-lookup"><span data-stu-id="e8db7-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="e8db7-197">上記のプロジェクト ファイルでは、次のことが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-197">With the preceding project file:</span></span>

* <span data-ttu-id="e8db7-198">すべてのコンシューマー向けに `Markdig` パッケージが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="e8db7-199">.NET Framework 4.6.1 以降または .NET Core 2.x を対象とするコンシューマー向けに [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) への参照が追加されます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="e8db7-200">パッケージのバージョン 2.1.0 は、下位互換性のために ASP.NET Core 2.2 で動作します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="e8db7-201">共有フレームワークは、.NET Core 3.x を対象とするコンシューマー向けに参照されます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="e8db7-202">`Microsoft.AspNetCore.Mvc.Razor` パッケージは、共有フレームワークに含まれます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="e8db7-203">また、.NET Core 2.1 と .NET Framework 4.6.1 の両方を対象とする場合、代わりに .NET Standard 2.0 を対象とすることもできます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="e8db7-204">前述のプロジェクト ファイルでは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="e8db7-205">ライブラリにはタグ ヘルパーのみが含まれているため、ASP.NET Core が実行される特定のプラットフォーム (.NET Core および .NET Framework) を対象とする方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="e8db7-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="e8db7-206">タグ ヘルパーは、他の .NET Standard 2.0 準拠のターゲット フレームワーク (Unity、UWP、Xamarin など) で使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="e8db7-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="e8db7-207">.NET Framework から .NET Standard 2.0 を使用する場合、いくつかの問題がありますが、.NET Framework 4.7.2 で対処されました。</span><span class="sxs-lookup"><span data-stu-id="e8db7-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="e8db7-208">.NET Framework 4.6.1 から 4.7.1 までを使用するコンシューマーの場合、.NET Framework 4.6.1 を対象とすることにより、エクスペリエンスを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="e8db7-209">ライブラリでプラットフォーム固有の API を呼び出す必要がある場合、.NET Standard の代わりに特定の .NET 実装を対象とします。</span><span class="sxs-lookup"><span data-stu-id="e8db7-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="e8db7-210">詳細については、「[マルチターゲット](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8db7-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="e8db7-211">変更されていない API を使用する</span><span class="sxs-lookup"><span data-stu-id="e8db7-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="e8db7-212">ミドルウェア ライブラリを .NET Core 2.2 から 3.0 にアップグレードするシナリオについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="e8db7-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="e8db7-213">ライブラリで使用される ASP.NET Core ミドルウェア API は、ASP.NET Core 2.2 と 3.0 間で変更されていません。</span><span class="sxs-lookup"><span data-stu-id="e8db7-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="e8db7-214">.NET Core 3.0 でミドルウェア ライブラリのサポートを継続するには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="e8db7-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="e8db7-215">[標準ライブラリのガイダンス](/dotnet/standard/library-guidance/)に従います。</span><span class="sxs-lookup"><span data-stu-id="e8db7-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="e8db7-216">対応するアセンブリが共有フレームワークに存在しない場合は、各 API の NuGet へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="e8db7-217">変更された API を使用する</span><span class="sxs-lookup"><span data-stu-id="e8db7-217">Use an API that changed</span></span>

<span data-ttu-id="e8db7-218">ライブラリを .NET Core 2.2 から 3.0 にアップグレードするシナリオについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="e8db7-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="e8db7-219">ライブラリで使用される ASP.NET Core API は、ASP.NET Core 3.0 で[破壊的変更](/dotnet/core/compatibility/breaking-changes)が行われています。</span><span class="sxs-lookup"><span data-stu-id="e8db7-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="e8db7-220">破壊された API をすべてのバージョンで使用しないようにライブラリを書き換えることができるかどうかを検討します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="e8db7-221">ライブラリを書き換えることができる場合は書き換えて、パッケージ参照を使用して以前のターゲット フレームワーク (たとえば、.NET Standard 2.0 や .NET Framework 4.6.1 など) を引き続き対象とします。</span><span class="sxs-lookup"><span data-stu-id="e8db7-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="e8db7-222">ライブラリを書き換えることができない場合は、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="e8db7-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="e8db7-223">.NET Core 3.0 のターゲットを追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="e8db7-224">共有フレームワークの `<FrameworkReference>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="e8db7-225">条件付きでコードをコンパイルするには、適切なターゲット フレームワーク シンボルを設定した [#if プリプロセッサ ディレクティブ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="e8db7-226">たとえば、ASP.NET Core 3.0 から、HTTP 要求および応答ストリームでの同期読み取りと同期書き込みは既定で無効になっています。</span><span class="sxs-lookup"><span data-stu-id="e8db7-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="e8db7-227">ASP.NET Core 2.2 では、この同期動作が既定でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e8db7-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="e8db7-228">IO の発生時に同期読み取りと同期書き込みを有効にする必要があるミドルウェア ライブラリについて検討します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-228">Consider a middleware library in which synchronous reads and writes should be enabled where IO is occurring.</span></span> <span data-ttu-id="e8db7-229">ライブラリで、同期機能を有効にするコードを適切なプリプロセッサ ディレクティブで囲む必要があります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="e8db7-230">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="e8db7-231">3\.0 で導入された API を使用する</span><span class="sxs-lookup"><span data-stu-id="e8db7-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="e8db7-232">ASP.NET Core 3.0 で導入された ASP.NET Core API を使用する必要がある場合について考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="e8db7-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="e8db7-233">以下の質問を検討します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-233">Consider the following questions:</span></span>

1. <span data-ttu-id="e8db7-234">ライブラリの機能上、新しい API は必要ですか?</span><span class="sxs-lookup"><span data-stu-id="e8db7-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="e8db7-235">ライブラリで、この機能を別の方法で実装できますか?</span><span class="sxs-lookup"><span data-stu-id="e8db7-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="e8db7-236">ライブラリの機能上、新しい API が必要であり、それをダウンレベルで実装する方法がない場合:</span><span class="sxs-lookup"><span data-stu-id="e8db7-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="e8db7-237">.NET Core 3.x のみを対象とします。</span><span class="sxs-lookup"><span data-stu-id="e8db7-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="e8db7-238">共有フレームワークの `<FrameworkReference>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="e8db7-239">ライブラリでこの機能を別の方法で実装できる場合:</span><span class="sxs-lookup"><span data-stu-id="e8db7-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="e8db7-240">NET Core 3.x をターゲット フレームワークとして追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="e8db7-241">共有フレームワークの `<FrameworkReference>` 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="e8db7-242">条件付きでコードをコンパイルするには、適切なターゲット フレームワーク シンボルを設定した [#if プリプロセッサ ディレクティブ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="e8db7-243">たとえば、次のタグ ヘルパーは、ASP.NET Core 3.0 で導入された <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> インターフェイスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="e8db7-244">.NET Core 3.0 を対象とするコンシューマーは、`NETCOREAPP3_0` ターゲット フレームワーク シンボルで定義されたコード パスを実行します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="e8db7-245">NET Core 2.1 および .NET Framework 4.6.1 のコンシューマーの場合、タグ ヘルパーのコンストラクター パラメーターの型が <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> に変更されます。</span><span class="sxs-lookup"><span data-stu-id="e8db7-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="e8db7-246">ASP.NET Core 3.0 で `IHostingEnvironment` が廃止としてマークされ、代わりに `IWebHostEnvironment` を推奨しているため、この変更が必要です。</span><span class="sxs-lookup"><span data-stu-id="e8db7-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="e8db7-247">次のマルチターゲット プロジェクト ファイルでは、このタグ ヘルパーのシナリオに対応しています。</span><span class="sxs-lookup"><span data-stu-id="e8db7-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="e8db7-248">共有フレームワークから削除された API を使用する</span><span class="sxs-lookup"><span data-stu-id="e8db7-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="e8db7-249">共有フレームワークから削除された ASP.NET Core アセンブリを使用するには、適切なパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e8db7-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="e8db7-250">ASP.NET Core 3.0 で共有フレームワークから削除されたパッケージの一覧については、「[古いパッケージ参照の削除](xref:migration/22-to-30#remove-obsolete-package-references)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e8db7-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="e8db7-251">たとえば、Web API クライアントを追加するプロジェクト ファイルは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e8db7-251">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="e8db7-252">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e8db7-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="e8db7-253">.NET 実装のサポート</span><span class="sxs-lookup"><span data-stu-id="e8db7-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="e8db7-254">.NET サポート ポリシー</span><span class="sxs-lookup"><span data-stu-id="e8db7-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
