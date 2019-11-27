---
title: ASP.NET Core の Razor SDK
author: Rick-Anderson
description: ASP.NET Core の Razor ページを使用して、ページのコーディングに重点を置いたシナリオをより簡略化して、MVC を使用する場合よりも生産性を高める方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2fbdf95d02d7918236981c7fee8ebcbedf5c55e1
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963259"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="aa27e-103">ASP.NET Core の Razor SDK</span><span class="sxs-lookup"><span data-stu-id="aa27e-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="aa27e-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aa27e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="aa27e-105">概要</span><span class="sxs-lookup"><span data-stu-id="aa27e-105">Overview</span></span>

<span data-ttu-id="aa27e-106">[!INCLUDE[](~/includes/2.1-SDK.md)] には、`Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="aa27e-107">Razor SDK とは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="aa27e-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="aa27e-108">は、ASP.NET Core MVC ベースまたは[Blazor](xref:blazor/index)プロジェクトの[Razor](xref:mvc/views/razor)ファイルを含むプロジェクトをビルド、パッケージ、および発行するために必要です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="aa27e-109">には、Razor (*cshtml*または*razor*) ファイルのコンパイルをカスタマイズするための定義済みのターゲット、プロパティ、および項目のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="aa27e-110">Razor SDK には、`Include` 属性が `**\*.cshtml` および `**\*.razor` グロビングパターンに設定された `Content` 項目が含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="aa27e-111">一致するファイルがパブリッシュされます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="aa27e-112">ASP.NET Core MVC ベース プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ、および発行に関わる表示と操作を標準化できます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="aa27e-113">Razor ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットを含みます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="aa27e-114">Razor SDK には、`Include` 属性が `**\*.cshtml` グロビングパターンに設定された `Content` 項目が含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="aa27e-115">一致するファイルがパブリッシュされます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="aa27e-116">必要条件</span><span class="sxs-lookup"><span data-stu-id="aa27e-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="aa27e-117">Razor SDK を使用する</span><span class="sxs-lookup"><span data-stu-id="aa27e-117">Use the Razor SDK</span></span>

<span data-ttu-id="aa27e-118">ほとんどの web アプリは、Razor SDK を明示的に参照する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="aa27e-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa27e-119">Razor SDK を使用して Razor ビューまたは Razor Pages を含むクラスライブラリをビルドするには、Razor クラスライブラリ (RCL) プロジェクトテンプレートから始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="aa27e-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="aa27e-120">Blazor (*razor*) ファイルのビルドに使用される rcl では、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)パッケージへの参照が最低限必要です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="aa27e-121">Razor ビューまたはページ (*cshtml*ファイル) をビルドするために使用される rcl では、`netcoreapp3.0` 以降をターゲットにする必要があります。また、プロジェクトファイルの[AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に `FrameworkReference` があります。</span><span class="sxs-lookup"><span data-stu-id="aa27e-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aa27e-122">Razor SDK を使用して Razor ビューまたは Razor ページを含むクラス ライブラリを構築する方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="aa27e-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="aa27e-123">`Microsoft.NET.Sdk.Razor` の代わりに `Microsoft.NET.Sdk` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="aa27e-124">通常、Razor Pages および Razor ビューのビルドとコンパイルに必要な追加の依存関係を受け取るには、`Microsoft.AspNetCore.Mvc` へのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="aa27e-125">少なくとも、プロジェクトでパッケージ参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aa27e-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="aa27e-126">`Microsoft.AspNetCore.Razor.Design` パッケージは、プロジェクトの Razor コンパイルタスクとターゲットを提供します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="aa27e-127">前述のパッケージは、`Microsoft.AspNetCore.Mvc` に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="aa27e-128">次のマークアップは、Razor SDK を使用して ASP.NET Core Razor Pages アプリの Razor ファイルを作成するプロジェクトファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="aa27e-129">`Microsoft.AspNetCore.Razor.Design` パッケージと `Microsoft.AspNetCore.Mvc.Razor.Extensions` パッケージは、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="aa27e-130">ただし、バージョンが少ない `Microsoft.AspNetCore.App` のパッケージ参照では、`Microsoft.AspNetCore.Razor.Design` の最新バージョンを含まないアプリにメタパッケージが提供されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="aa27e-131">プロジェクトは、Razor の最新のビルド時修正プログラムが含まれるように、`Microsoft.AspNetCore.Razor.Design` (または `Microsoft.AspNetCore.Mvc`) の一貫したバージョンを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aa27e-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="aa27e-132">詳細については、次を参照してください。[この GitHub の問題](https://github.com/aspnet/Razor/issues/2553)します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="aa27e-133">プロパティ</span><span class="sxs-lookup"><span data-stu-id="aa27e-133">Properties</span></span>

<span data-ttu-id="aa27e-134">Razor の SDK の動作は、プロジェクトをビルドする際に次のプロパティにより制御されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="aa27e-135">`true`時に &ndash; `RazorCompileOnBuild`、プロジェクトのビルドの一部として Razor アセンブリをコンパイルして出力します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="aa27e-136">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-136">Defaults to `true`.</span></span>
* <span data-ttu-id="aa27e-137">`true`時に &ndash; `RazorCompileOnPublish`、プロジェクトの発行の一部として Razor アセンブリをコンパイルして出力します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="aa27e-138">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-138">Defaults to `true`.</span></span>

<span data-ttu-id="aa27e-139">次の表のプロパティと項目は、Razor SDK への入力と出力を構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="aa27e-140">ASP.NET Core 3.0 以降では、プロジェクトファイル内の `RazorCompileOnBuild` または `RazorCompileOnPublish` の MSBuild プロパティが無効になっている場合、MVC ビューまたは Razor Pages は既定では提供されません。</span><span class="sxs-lookup"><span data-stu-id="aa27e-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="aa27e-141">アプリケーションがランタイムコンパイルに依存して[Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation)ファイルを処理する場合は、RuntimeCompilation パッケージへの明示的な参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aa27e-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="aa27e-142">項目</span><span class="sxs-lookup"><span data-stu-id="aa27e-142">Items</span></span> | <span data-ttu-id="aa27e-143">説明</span><span class="sxs-lookup"><span data-stu-id="aa27e-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="aa27e-144">コード生成の入力である項目要素 (*cshtml*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="aa27e-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="aa27e-145">Razor コンポーネントのコード生成に入力する項目要素 (*razor*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="aa27e-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="aa27e-146">Razor コンパイルターゲットへの入力である項目要素 ( *.cs*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="aa27e-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="aa27e-147">Razor アセンブリにコンパイルする追加ファイルを指定するには、この `ItemGroup` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="aa27e-148">Razor アセンブリ用の属性をコード生成するために使用する項目要素です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="aa27e-149">(例:</span><span class="sxs-lookup"><span data-stu-id="aa27e-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="aa27e-150">生成された Razor アセンブリに埋め込みリソースとして追加された項目要素。</span><span class="sxs-lookup"><span data-stu-id="aa27e-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="aa27e-151">property</span><span class="sxs-lookup"><span data-stu-id="aa27e-151">Property</span></span> | <span data-ttu-id="aa27e-152">説明</span><span class="sxs-lookup"><span data-stu-id="aa27e-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="aa27e-153">Razor によって生成されたアセンブリの (拡張子なしの) ファイル名です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-153">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="aa27e-154">Razor の出力ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="aa27e-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="aa27e-155">Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="aa27e-156">有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="aa27e-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="aa27e-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="aa27e-158">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-158">Default is `true`.</span></span> <span data-ttu-id="aa27e-159">`true`すると、web.config ファイル、 *json*ファイル、および*cshtml*ファイルがプロジェクトのコンテンツとし*て含まれ*ます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="aa27e-160">`Microsoft.NET.Sdk.Web`によって参照される場合、 *wwwroot*ファイルと config ファイルのファイルも含まれます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="aa27e-161">`true` の場合、`RazorGenerate` 項目の `Content` 項目の *.cshtml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="aa27e-162">`true`すると、`RazorAssemblyAttribute` によって指定された属性を含む *.cs*ファイルを生成し、そのファイルをコンパイル出力に含めます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="aa27e-163">`true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="aa27e-164">`true`すると、`RazorGenerate` 項目 (*cshtml*) ファイルが発行ディレクトリにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="aa27e-165">通常、ビルド時または発行時にコンパイルに参加する場合、Razor ファイルは発行されたアプリには必要ありません。</span><span class="sxs-lookup"><span data-stu-id="aa27e-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="aa27e-166">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="aa27e-167">`true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="aa27e-168">通常、ビルド時または発行時に Razor コンパイルが発生した場合、公開されたアプリには参照アセンブリは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="aa27e-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="aa27e-169">発行されたアプリがランタイムコンパイルを必要とする場合は、`true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="aa27e-170">たとえば、アプリで実行時に、または埋め込みビューを使用する場合は、値を `true` に設定し*ます*。</span><span class="sxs-lookup"><span data-stu-id="aa27e-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="aa27e-171">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="aa27e-172">`true`すると、すべての Razor コンテンツ項目 (*cshtml*ファイル) が、生成される NuGet パッケージに含めるようにマークされます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="aa27e-173">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="aa27e-174">`true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate ( *.cshtml*) 項目が追加されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="aa27e-175">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="aa27e-176">`true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="aa27e-177">既定値は、`UseSharedCompilation` の値です。</span><span class="sxs-lookup"><span data-stu-id="aa27e-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="aa27e-178">`true`すると、アプリケーションパーツの検出を実行するために、SDK によって実行時に MVC によって使用される追加の属性が生成されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |

<span data-ttu-id="aa27e-179">プロパティの詳細については、「[MSBuild プロパティ](/visualstudio/msbuild/msbuild-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aa27e-179">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="aa27e-180">ターゲット</span><span class="sxs-lookup"><span data-stu-id="aa27e-180">Targets</span></span>

<span data-ttu-id="aa27e-181">Razor SDK では、次の 2 つの主要なターゲットが定義されています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-181">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="aa27e-182">`RazorGenerate` &ndash; コードは `RazorGenerate` 項目要素から .cs ファイルを生成*し*ます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-182">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="aa27e-183">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorGenerateDependsOn` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-183">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="aa27e-184">`RazorCompile` &ndash; は、生成された *.cs*ファイルを Razor アセンブリにコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="aa27e-184">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="aa27e-185">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorCompileDependsOn` を使用します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-185">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="aa27e-186">`RazorComponentGenerate` &ndash; コードによって `RazorComponent` 項目要素の *.cs*ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-186">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="aa27e-187">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorComponentGenerateDependsOn` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-187">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="aa27e-188">Razor ビューの実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="aa27e-188">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="aa27e-189">既定では、Razor SDK は、実行時のコンパイルを実行するために必要な参照アセンブリを公開しません。</span><span class="sxs-lookup"><span data-stu-id="aa27e-189">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="aa27e-190">この結果、アプリケーション モデルが実行時のコンパイルに依存している場合には、コンパイルが失敗します。たとえば、アプリが公開後に埋め込まれたビューを使用したり、ビューを変更したりする場合などです。</span><span class="sxs-lookup"><span data-stu-id="aa27e-190">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="aa27e-191">`CopyRefAssembliesToPublishDirectory` を `true` に設定して、参照アセンブリの公開を続行します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-191">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="aa27e-192">Web アプリの場合は、アプリが `Microsoft.NET.Sdk.Web` SDK を対象としていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="aa27e-192">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="aa27e-193">Razor 言語バージョン</span><span class="sxs-lookup"><span data-stu-id="aa27e-193">Razor language version</span></span>

<span data-ttu-id="aa27e-194">`Microsoft.NET.Sdk.Web` SDK を対象とする場合、Razor 言語バージョンはアプリのターゲットフレームワークのバージョンから推測されます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-194">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="aa27e-195">`Microsoft.NET.Sdk.Razor` SDK を対象とするプロジェクトの場合、またはまれに、アプリが推定値とは異なる Razor 言語バージョンを必要とする場合は、アプリのプロジェクトファイルの `<RazorLangVersion>` プロパティを設定してバージョンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="aa27e-195">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="aa27e-196">Razor の言語バージョンは、ビルドされたランタイムのバージョンと密接に統合されています。</span><span class="sxs-lookup"><span data-stu-id="aa27e-196">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="aa27e-197">ランタイム向けに設計されていない言語バージョンをターゲットにすることはサポートされていないため、ビルドエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="aa27e-197">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aa27e-198">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="aa27e-198">Additional resources</span></span>

* [<span data-ttu-id="aa27e-199">.NET Core の csproj 形式に追加されたもの</span><span class="sxs-lookup"><span data-stu-id="aa27e-199">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="aa27e-200">MSBuild プロジェクトの共通項目</span><span class="sxs-lookup"><span data-stu-id="aa27e-200">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
