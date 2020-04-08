---
title: ASP.NET Core の Razor SDK
author: Rick-Anderson
description: ASP.NET Core の Razor ページを使用して、ページのコーディングに重点を置いたシナリオをより簡略化して、MVC を使用する場合よりも生産性を高める方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80321008"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="1382e-103">ASP.NET Core の Razor SDK</span><span class="sxs-lookup"><span data-stu-id="1382e-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="1382e-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1382e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="1382e-105">概要</span><span class="sxs-lookup"><span data-stu-id="1382e-105">Overview</span></span>

<span data-ttu-id="1382e-106">[!INCLUDE[](~/includes/2.1-SDK.md)] には、`Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1382e-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="1382e-107">Razor SDK とは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1382e-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="1382e-108">ASP.NET Core MVC ベースまたは [Blazor](xref:mvc/views/razor) プロジェクト用の [Razor](xref:blazor/index) ファイルを含むプロジェクトのビルド、パッケージ化、および発行を行うために必要です。</span><span class="sxs-lookup"><span data-stu-id="1382e-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="1382e-109">Razor ( *.cshtml* または *.razor*) ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1382e-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="1382e-110">Razor SDK には、`Content` 属性が `Include` および `**\*.cshtml` glob パターンに設定された `**\*.razor` 項目が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1382e-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="1382e-111">一致するファイルが発行されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="1382e-112">ASP.NET Core MVC ベース プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ、および発行に関わる表示と操作を標準化できます。</span><span class="sxs-lookup"><span data-stu-id="1382e-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="1382e-113">Razor ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットを含みます。</span><span class="sxs-lookup"><span data-stu-id="1382e-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="1382e-114">Razor SDK には、`Content` 属性が `Include` glob パターンに設定された `**\*.cshtml` 項目が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1382e-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="1382e-115">一致するファイルが発行されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="1382e-116">前提条件</span><span class="sxs-lookup"><span data-stu-id="1382e-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="1382e-117">Razor SDK を使用する</span><span class="sxs-lookup"><span data-stu-id="1382e-117">Use the Razor SDK</span></span>

<span data-ttu-id="1382e-118">ほとんどの Web アプリでは、明示的に Razor SDK を参照する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="1382e-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1382e-119">Razor SDK を使用して Razor ビューまたは Razor Pages を含むクラス ライブラリをビルドするには、Razor クラス ライブラリ (RCL) プロジェクト テンプレートを使用して開始することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1382e-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="1382e-120">Blazor ( *.razor*) ファイルのビルドに使用する RCL では、少なくとも、[Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) パッケージへの参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="1382e-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="1382e-121">Razor ビューまたは Razor ページのビルドに使用する RCL ( *.cshtml* ファイル) は、少なくとも `netcoreapp3.0` 以降をターゲットにする必要があり、そのプロジェクト ファイル内には `FrameworkReference`Microsoft.AspNetCore.App メタパッケージ[ への ](xref:fundamentals/metapackage-app) があります。</span><span class="sxs-lookup"><span data-stu-id="1382e-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1382e-122">Razor SDK を使用して Razor ビューまたは Razor ページを含むクラス ライブラリを構築する方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1382e-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="1382e-123">`Microsoft.NET.Sdk.Razor` の代わりに `Microsoft.NET.Sdk` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1382e-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="1382e-124">一般に、Razor Pages や Razor ビューをビルドしてコンパイルするために必要な追加の依存関係を取得するために `Microsoft.AspNetCore.Mvc` へのパッケージ参照が必要とされます。</span><span class="sxs-lookup"><span data-stu-id="1382e-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="1382e-125">少なくとも、以下へのパッケージ参照をプロジェクトに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1382e-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="1382e-126">`Microsoft.AspNetCore.Razor.Design` パッケージでは、プロジェクトの Razor コンパイル タスクとターゲットが提供されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="1382e-127">前述のパッケージは、`Microsoft.AspNetCore.Mvc` に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="1382e-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="1382e-128">次のマークアップは、Razor SDK を使用して ASP.NET Core Razor Pages アプリ用の Razor ファイルをビルドするプロジェクト ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="1382e-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="1382e-129">`Microsoft.AspNetCore.Razor.Design` パッケージと `Microsoft.AspNetCore.Mvc.Razor.Extensions` パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="1382e-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="1382e-130">ただし、バージョンのない `Microsoft.AspNetCore.App` パッケージのリファレンスでは、最新バージョンの `Microsoft.AspNetCore.Razor.Design` を含まないアプリへのメタパッケージが提供されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="1382e-131">プロジェクトでは、ビルド時に最新の Razor 用修正プログラムが含まれるように、一貫したバージョンの `Microsoft.AspNetCore.Razor.Design` (または `Microsoft.AspNetCore.Mvc`) を参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1382e-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="1382e-132">詳細については、[こちらの GitHub の問題](https://github.com/aspnet/Razor/issues/2553)のページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1382e-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="1382e-133">プロパティ</span><span class="sxs-lookup"><span data-stu-id="1382e-133">Properties</span></span>

<span data-ttu-id="1382e-134">Razor の SDK の動作は、プロジェクトをビルドする際に次のプロパティにより制御されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="1382e-135">`RazorCompileOnBuild` &ndash; `true` の場合、プロジェクトをビルドする際に、Razor アセンブリがコンパイルおよび生成されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="1382e-136">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-136">Defaults to `true`.</span></span>
* <span data-ttu-id="1382e-137">`RazorCompileOnPublish` &ndash; `true` の場合、プロジェクトを発行する際に、Razor アセンブリがコンパイルおよび生成されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="1382e-138">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-138">Defaults to `true`.</span></span>

<span data-ttu-id="1382e-139">Razor SDK への入力および出力は、次の表のプロパティと項目を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="1382e-140">ASP.NET Core 3.0 以降では、プロジェクト ファイルで `RazorCompileOnBuild` または `RazorCompileOnPublish` の MSBuild プロパティが無効になっている場合、MVC ビューまたは Razor Pages は既定では提供されません。</span><span class="sxs-lookup"><span data-stu-id="1382e-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="1382e-141">[.cshtml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) ファイルを処理するためにアプリがランタイム コンパイルに依存して場合は、アプリに *Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation* パッケージへの明示的な参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1382e-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="1382e-142">項目</span><span class="sxs-lookup"><span data-stu-id="1382e-142">Items</span></span> | <span data-ttu-id="1382e-143">説明</span><span class="sxs-lookup"><span data-stu-id="1382e-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="1382e-144">コード生成への入力である項目要素 ( *.cshtml* ファイル) です。</span><span class="sxs-lookup"><span data-stu-id="1382e-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="1382e-145">Razor コンポーネントのコード生成への入力である項目要素 ( *.razor* ファイル) です。</span><span class="sxs-lookup"><span data-stu-id="1382e-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="1382e-146">Razor コンパイル対象への入力である項目要素 ( *.cs* ファイル) です。</span><span class="sxs-lookup"><span data-stu-id="1382e-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="1382e-147">Razor アセンブリに追加でコンパイルするファイルを指定するには、この `ItemGroup` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1382e-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="1382e-148">Razor アセンブリ用の属性をコード生成するために使用する項目要素です。</span><span class="sxs-lookup"><span data-stu-id="1382e-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="1382e-149">(例:</span><span class="sxs-lookup"><span data-stu-id="1382e-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="1382e-150">生成される Razor アセンブリに埋め込みのリソースとして追加される項目要素です。</span><span class="sxs-lookup"><span data-stu-id="1382e-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="1382e-151">property</span><span class="sxs-lookup"><span data-stu-id="1382e-151">Property</span></span> | <span data-ttu-id="1382e-152">説明</span><span class="sxs-lookup"><span data-stu-id="1382e-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="1382e-153">Razor によって生成されたアセンブリの (拡張子なしの) ファイル名です。</span><span class="sxs-lookup"><span data-stu-id="1382e-153">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="1382e-154">Razor の出力ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="1382e-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="1382e-155">Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="1382e-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="1382e-156">有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="1382e-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="1382e-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="1382e-158">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-158">Default is `true`.</span></span> <span data-ttu-id="1382e-159">`true` の場合、プロジェクトのコンテンツとして *web.config* ファイル、*json* ファイル、 *.cshtml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1382e-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="1382e-160">`Microsoft.NET.Sdk.Web` を介して参照する場合、*wwwroot* 下のファイルと構成ファイルも含まれます。</span><span class="sxs-lookup"><span data-stu-id="1382e-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="1382e-161">`true` の場合、*項目の* 項目の `Content`.cshtml`RazorGenerate` ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1382e-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="1382e-162">`true` の場合、*で指定された属性を含む*.cs`RazorAssemblyAttribute` ファイルが生成され、そのファイルがコンパイル出力に含められます。</span><span class="sxs-lookup"><span data-stu-id="1382e-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="1382e-163">`true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="1382e-164">`true` の場合、`RazorGenerate` 項目 ( *.cshtml*) ファイルが発行ディレクトリにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="1382e-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="1382e-165">一般に、Razor ファイルがビルド時または発行時にコンパイルに含められる場合、発行済みアプリのために Razor ファイルは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1382e-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="1382e-166">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-166">Defaults to `false`.</span></span> |
| `PreserveCompilationReferences` | <span data-ttu-id="1382e-167">`true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。</span><span class="sxs-lookup"><span data-stu-id="1382e-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="1382e-168">一般に、ビルド時または発行時に Razor のコンパイルが行われる場合、発行済みアプリのために参照アセンブリは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1382e-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="1382e-169">発行済みアプリで実行時のコンパイルが必要な場合は、`true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1382e-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="1382e-170">たとえばアプリで、実行時に `true`.cshtml*ファイルを変更したり、埋め込みビューを使用したりする場合は、値を* に設定します。</span><span class="sxs-lookup"><span data-stu-id="1382e-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="1382e-171">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="1382e-172">`true` の場合、Razor コンテンツ項目 ( *.cshtml* ファイル) はすべて、生成される NuGet パッケージに含めるようマーク付けされます。</span><span class="sxs-lookup"><span data-stu-id="1382e-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="1382e-173">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="1382e-174">`true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate ( *.cshtml*) 項目が追加されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="1382e-175">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="1382e-176">`true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="1382e-177">既定値は、`UseSharedCompilation` の値です。</span><span class="sxs-lookup"><span data-stu-id="1382e-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="1382e-178">`true` の場合、アプリケーション パーツの検出を実行するために実行時に MVC によって使用される追加の属性が、SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="1382e-179">Web または Razor SDK をターゲットとするプロジェクトの `Content` 項目グループから除外する必要がある項目要素の glob パターン</span><span class="sxs-lookup"><span data-stu-id="1382e-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="1382e-180">`true` の場合、 *.config* ファイルと *.json* ファイルがビルド出力ディレクトリにコピーされません。</span><span class="sxs-lookup"><span data-stu-id="1382e-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="1382e-181">`true` の場合、MVC ビューまたは Razor Pages を含むアプリケーションを構築するときに必要な MVC 構成のサポートを追加するように、Razor SDK を構成します。</span><span class="sxs-lookup"><span data-stu-id="1382e-181">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="1382e-182">このプロパティは、Web SDK をターゲットとする .NET Core 3.0 以降のプロジェクトには暗黙的に設定されます</span><span class="sxs-lookup"><span data-stu-id="1382e-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="1382e-183">ターゲットにする Razor 言語のバージョン。</span><span class="sxs-lookup"><span data-stu-id="1382e-183">The version of the Razor Language to target.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="1382e-184">property</span><span class="sxs-lookup"><span data-stu-id="1382e-184">Property</span></span> | <span data-ttu-id="1382e-185">説明</span><span class="sxs-lookup"><span data-stu-id="1382e-185">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="1382e-186">Razor によって生成されたアセンブリの (拡張子なしの) ファイル名です。</span><span class="sxs-lookup"><span data-stu-id="1382e-186">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="1382e-187">Razor の出力ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="1382e-187">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="1382e-188">Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="1382e-188">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="1382e-189">有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-189">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="1382e-190">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="1382e-190">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="1382e-191">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-191">Default is `true`.</span></span> <span data-ttu-id="1382e-192">`true` の場合、プロジェクトのコンテンツとして *web.config* ファイル、*json* ファイル、 *.cshtml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1382e-192">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="1382e-193">`Microsoft.NET.Sdk.Web` を介して参照する場合、*wwwroot* 下のファイルと構成ファイルも含まれます。</span><span class="sxs-lookup"><span data-stu-id="1382e-193">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="1382e-194">`true` の場合、*項目の* 項目の `Content`.cshtml`RazorGenerate` ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1382e-194">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="1382e-195">`true` の場合、*で指定された属性を含む*.cs`RazorAssemblyAttribute` ファイルが生成され、そのファイルがコンパイル出力に含められます。</span><span class="sxs-lookup"><span data-stu-id="1382e-195">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="1382e-196">`true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-196">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="1382e-197">`true` の場合、`RazorGenerate` 項目 ( *.cshtml*) ファイルが発行ディレクトリにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="1382e-197">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="1382e-198">一般に、Razor ファイルがビルド時または発行時にコンパイルに含められる場合、発行済みアプリのために Razor ファイルは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1382e-198">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="1382e-199">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-199">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="1382e-200">`true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。</span><span class="sxs-lookup"><span data-stu-id="1382e-200">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="1382e-201">一般に、ビルド時または発行時に Razor のコンパイルが行われる場合、発行済みアプリのために参照アセンブリは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1382e-201">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="1382e-202">発行済みアプリで実行時のコンパイルが必要な場合は、`true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1382e-202">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="1382e-203">たとえばアプリで、実行時に `true`.cshtml*ファイルを変更したり、埋め込みビューを使用したりする場合は、値を* に設定します。</span><span class="sxs-lookup"><span data-stu-id="1382e-203">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="1382e-204">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-204">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="1382e-205">`true` の場合、Razor コンテンツ項目 ( *.cshtml* ファイル) はすべて、生成される NuGet パッケージに含めるようマーク付けされます。</span><span class="sxs-lookup"><span data-stu-id="1382e-205">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="1382e-206">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-206">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="1382e-207">`true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate ( *.cshtml*) 項目が追加されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-207">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="1382e-208">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="1382e-208">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="1382e-209">`true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-209">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="1382e-210">既定値は、`UseSharedCompilation` の値です。</span><span class="sxs-lookup"><span data-stu-id="1382e-210">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="1382e-211">`true` の場合、アプリケーション パーツの検出を実行するために実行時に MVC によって使用される追加の属性が、SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-211">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="1382e-212">Web または Razor SDK をターゲットとするプロジェクトの `Content` 項目グループから除外する必要がある項目要素の glob パターン</span><span class="sxs-lookup"><span data-stu-id="1382e-212">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="1382e-213">`true` の場合、 *.config* ファイルと *.json* ファイルがビルド出力ディレクトリにコピーされません。</span><span class="sxs-lookup"><span data-stu-id="1382e-213">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="1382e-214">`true` の場合、MVC ビューまたは Razor Pages を含むアプリケーションを構築するときに必要な MVC 構成のサポートを追加するように、Razor SDK を構成します。</span><span class="sxs-lookup"><span data-stu-id="1382e-214">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="1382e-215">このプロパティは、Web SDK をターゲットとする .NET Core 3.0 以降のプロジェクトには暗黙的に設定されます</span><span class="sxs-lookup"><span data-stu-id="1382e-215">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="1382e-216">ターゲットにする Razor 言語のバージョン。</span><span class="sxs-lookup"><span data-stu-id="1382e-216">The version of the Razor Language to target.</span></span> |

::: moniker-end

<span data-ttu-id="1382e-217">プロパティの詳細については、「[MSBuild プロパティ](/visualstudio/msbuild/msbuild-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1382e-217">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="1382e-218">ターゲット</span><span class="sxs-lookup"><span data-stu-id="1382e-218">Targets</span></span>

<span data-ttu-id="1382e-219">Razor SDK では、次の 2 つの主要なターゲットが定義されています。</span><span class="sxs-lookup"><span data-stu-id="1382e-219">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="1382e-220">`RazorGenerate` &ndash; コードにより、*項目要素から*.cs`RazorGenerate` ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-220">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="1382e-221">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorGenerateDependsOn` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="1382e-221">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="1382e-222">`RazorCompile` &ndash; 生成された *.cs* ファイルを、Razor アセンブリにコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="1382e-222">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="1382e-223">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorCompileDependsOn` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1382e-223">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="1382e-224">`RazorComponentGenerate` &ndash; コードにより、*項目要素のための*.cs`RazorComponent` ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-224">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="1382e-225">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorComponentGenerateDependsOn` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="1382e-225">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="1382e-226">Razor ビューの実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="1382e-226">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="1382e-227">既定では、Razor SDK は、実行時のコンパイルを実行するために必要な参照アセンブリを公開しません。</span><span class="sxs-lookup"><span data-stu-id="1382e-227">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="1382e-228">この結果、アプリケーション モデルが実行時のコンパイルに依存している場合には、コンパイルが失敗します。たとえば、アプリが公開後に埋め込まれたビューを使用したり、ビューを変更したりする場合などです。</span><span class="sxs-lookup"><span data-stu-id="1382e-228">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="1382e-229">`CopyRefAssembliesToPublishDirectory` を `true` に設定して、参照アセンブリの公開を続行します。</span><span class="sxs-lookup"><span data-stu-id="1382e-229">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="1382e-230">Web アプリの場合は、アプリが `Microsoft.NET.Sdk.Web` SDK をターゲットにしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="1382e-230">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="1382e-231">Razor 言語バージョン</span><span class="sxs-lookup"><span data-stu-id="1382e-231">Razor language version</span></span>

<span data-ttu-id="1382e-232">`Microsoft.NET.Sdk.Web` SDK をターゲットとする場合、Razor 言語バージョンは、アプリのターゲット フレームワークのバージョンから推論されます。</span><span class="sxs-lookup"><span data-stu-id="1382e-232">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="1382e-233">`Microsoft.NET.Sdk.Razor` SDK をターゲットとするプロジェクトの場合や、推論される値とは異なる Razor 言語バージョンを必要とするまれなアプリの場合は、アプリのプロジェクト ファイルで `<RazorLangVersion>` プロパティを設定することで、特定のバージョンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="1382e-233">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="1382e-234">Razor の言語バージョンは、そのバージョンのビルド対象であったランタイムのバージョンと緊密に統合されています。</span><span class="sxs-lookup"><span data-stu-id="1382e-234">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="1382e-235">そのランタイムに向けて設計されたのではない言語バージョンをターゲットにすることはサポートされておらず、おそらくビルド エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="1382e-235">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1382e-236">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1382e-236">Additional resources</span></span>

* [<span data-ttu-id="1382e-237">.NET Core の csproj 形式に追加されたもの</span><span class="sxs-lookup"><span data-stu-id="1382e-237">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="1382e-238">MSBuild プロジェクトの共通項目</span><span class="sxs-lookup"><span data-stu-id="1382e-238">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
