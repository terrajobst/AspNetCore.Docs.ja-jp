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
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647678"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="af8c8-103">ASP.NET Core の Razor SDK</span><span class="sxs-lookup"><span data-stu-id="af8c8-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="af8c8-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="af8c8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="af8c8-105">概要</span><span class="sxs-lookup"><span data-stu-id="af8c8-105">Overview</span></span>

<span data-ttu-id="af8c8-106">[!INCLUDE[](~/includes/2.1-SDK.md)] には、`Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="af8c8-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="af8c8-107">Razor SDK とは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="af8c8-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="af8c8-108">ASP.NET Core MVC ベースまたは [Blazor](xref:blazor/index) プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ化、および発行を行うために必要です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="af8c8-109">Razor ( *.cshtml* または *.razor*) ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="af8c8-110">Razor SDK には、`Include` 属性が `**\*.cshtml` および `**\*.razor` glob パターンに設定された `Content` 項目が含まれています。</span><span class="sxs-lookup"><span data-stu-id="af8c8-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="af8c8-111">一致するファイルが発行されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="af8c8-112">ASP.NET Core MVC ベース プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ、および発行に関わる表示と操作を標準化できます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="af8c8-113">Razor ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットを含みます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="af8c8-114">Razor SDK には、`Include` 属性が `**\*.cshtml` glob パターンに設定された `Content` 項目が含まれています。</span><span class="sxs-lookup"><span data-stu-id="af8c8-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="af8c8-115">一致するファイルが発行されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="af8c8-116">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="af8c8-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="af8c8-117">Razor SDK を使用する</span><span class="sxs-lookup"><span data-stu-id="af8c8-117">Use the Razor SDK</span></span>

<span data-ttu-id="af8c8-118">ほとんどの Web アプリでは、明示的に Razor SDK を参照する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="af8c8-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af8c8-119">Razor SDK を使用して Razor ビューまたは Razor Pages を含むクラス ライブラリをビルドするには、Razor クラス ライブラリ (RCL) プロジェクト テンプレートを使用して開始することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="af8c8-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="af8c8-120">Blazor ( *.razor*) ファイルのビルドに使用する RCL では、少なくとも、[Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) パッケージへの参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="af8c8-121">Razor ビューまたは Razor ページのビルドに使用する RCL ( *.cshtml* ファイル) は、少なくとも `netcoreapp3.0` 以降をターゲットにする必要があり、そのプロジェクト ファイル内には [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) への `FrameworkReference` があります。</span><span class="sxs-lookup"><span data-stu-id="af8c8-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="af8c8-122">Razor SDK を使用して Razor ビューまたは Razor ページを含むクラス ライブラリを構築する方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="af8c8-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="af8c8-123">`Microsoft.NET.Sdk.Razor` の代わりに `Microsoft.NET.Sdk` を使用します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="af8c8-124">一般に、Razor Pages や Razor ビューをビルドしてコンパイルするために必要な追加の依存関係を取得するために `Microsoft.AspNetCore.Mvc` へのパッケージ参照が必要とされます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="af8c8-125">少なくとも、以下へのパッケージ参照をプロジェクトに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af8c8-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="af8c8-126">`Microsoft.AspNetCore.Razor.Design` パッケージでは、プロジェクトの Razor コンパイル タスクとターゲットが提供されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="af8c8-127">前述のパッケージは、`Microsoft.AspNetCore.Mvc` に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="af8c8-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="af8c8-128">次のマークアップは、Razor SDK を使用して ASP.NET Core Razor Pages アプリ用の Razor ファイルをビルドするプロジェクト ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="af8c8-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="af8c8-129">`Microsoft.AspNetCore.Razor.Design` パッケージと `Microsoft.AspNetCore.Mvc.Razor.Extensions` パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="af8c8-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="af8c8-130">ただし、バージョンのない `Microsoft.AspNetCore.App` パッケージのリファレンスでは、最新バージョンの `Microsoft.AspNetCore.Razor.Design` を含まないアプリへのメタパッケージが提供されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="af8c8-131">プロジェクトでは、ビルド時に最新の Razor 用修正プログラムが含まれるように、一貫したバージョンの `Microsoft.AspNetCore.Razor.Design` (または `Microsoft.AspNetCore.Mvc`) を参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af8c8-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="af8c8-132">詳細については、次を参照してください。[この GitHub の問題](https://github.com/aspnet/Razor/issues/2553)します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="af8c8-133">プロパティ</span><span class="sxs-lookup"><span data-stu-id="af8c8-133">Properties</span></span>

<span data-ttu-id="af8c8-134">Razor の SDK の動作は、プロジェクトをビルドする際に次のプロパティにより制御されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="af8c8-135">`RazorCompileOnBuild` &ndash; `true` の場合、プロジェクトをビルドする際に、Razor アセンブリがコンパイルおよび生成されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="af8c8-136">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-136">Defaults to `true`.</span></span>
* <span data-ttu-id="af8c8-137">`RazorCompileOnPublish` &ndash; `true` の場合、プロジェクトを発行する際に、Razor アセンブリがコンパイルおよび生成されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="af8c8-138">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-138">Defaults to `true`.</span></span>

<span data-ttu-id="af8c8-139">Razor SDK への入力および出力は、次の表のプロパティと項目を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="af8c8-140">ASP.NET Core 3.0 以降では、プロジェクト ファイルで `RazorCompileOnBuild` または `RazorCompileOnPublish` の MSBuild プロパティが無効になっている場合、MVC ビューまたは Razor Pages は既定では提供されません。</span><span class="sxs-lookup"><span data-stu-id="af8c8-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="af8c8-141">*.cshtml* ファイルを処理するためにアプリがランタイム コンパイルに依存して場合は、アプリに [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) パッケージへの明示的な参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af8c8-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="af8c8-142">項目</span><span class="sxs-lookup"><span data-stu-id="af8c8-142">Items</span></span> | <span data-ttu-id="af8c8-143">説明</span><span class="sxs-lookup"><span data-stu-id="af8c8-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="af8c8-144">コード生成への入力である項目要素 ( *.cshtml* ファイル) です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="af8c8-145">Razor コンポーネントのコード生成への入力である項目要素 ( *.razor* ファイル) です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="af8c8-146">Razor コンパイル対象への入力である項目要素 ( *.cs* ファイル) です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="af8c8-147">Razor アセンブリに追加でコンパイルするファイルを指定するには、この `ItemGroup` を使用します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="af8c8-148">Razor アセンブリ用の属性をコード生成するために使用する項目要素です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="af8c8-149">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="af8c8-150">生成される Razor アセンブリに埋め込みのリソースとして追加される項目要素です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="af8c8-151">プロパティ</span><span class="sxs-lookup"><span data-stu-id="af8c8-151">Property</span></span> | <span data-ttu-id="af8c8-152">説明</span><span class="sxs-lookup"><span data-stu-id="af8c8-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="af8c8-153">Razor によって生成されたアセンブリの (拡張子なしの) ファイル名です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-153">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="af8c8-154">Razor の出力ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="af8c8-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="af8c8-155">Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="af8c8-156">有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="af8c8-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="af8c8-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="af8c8-158">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-158">Default is `true`.</span></span> <span data-ttu-id="af8c8-159">`true` の場合、プロジェクトのコンテンツとして *web.config* ファイル、*json* ファイル、 *.cshtml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="af8c8-160">`Microsoft.NET.Sdk.Web` を介して参照する場合、*wwwroot* 下のファイルと構成ファイルも含まれます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="af8c8-161">`true` の場合、`RazorGenerate` 項目の `Content` 項目の *.cshtml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="af8c8-162">`true` の場合、`RazorAssemblyAttribute` で指定された属性を含む *.cs* ファイルが生成され、そのファイルがコンパイル出力に含められます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="af8c8-163">`true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="af8c8-164">`true` の場合、`RazorGenerate` 項目 ( *.cshtml*) ファイルが発行ディレクトリにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="af8c8-165">一般に、Razor ファイルがビルド時または発行時にコンパイルに含められる場合、発行済みアプリのために Razor ファイルは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="af8c8-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="af8c8-166">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="af8c8-167">`true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="af8c8-168">一般に、ビルド時または発行時に Razor のコンパイルが行われる場合、発行済みアプリのために参照アセンブリは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="af8c8-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="af8c8-169">発行済みアプリで実行時のコンパイルが必要な場合は、`true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="af8c8-170">たとえばアプリで、実行時に *.cshtml* ファイルを変更したり、埋め込みビューを使用したりする場合は、値を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="af8c8-171">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="af8c8-172">`true` の場合、Razor コンテンツ項目 ( *.cshtml* ファイル) はすべて、生成される NuGet パッケージに含めるようマーク付けされます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="af8c8-173">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="af8c8-174">`true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate ( *.cshtml*) 項目が追加されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="af8c8-175">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="af8c8-176">`true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="af8c8-177">既定値は、`UseSharedCompilation` の値です。</span><span class="sxs-lookup"><span data-stu-id="af8c8-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="af8c8-178">`true` の場合、アプリケーション パーツの検出を実行するために実行時に MVC によって使用される追加の属性が、SDK によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="af8c8-179">Web または Razor SDK をターゲットとするプロジェクトの `Content` 項目グループから除外する必要がある項目要素の glob パターン</span><span class="sxs-lookup"><span data-stu-id="af8c8-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="af8c8-180">`true` の場合、 *.config* ファイルと *.json* ファイルがビルド出力ディレクトリにコピーされません。</span><span class="sxs-lookup"><span data-stu-id="af8c8-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="af8c8-181">`true` の場合、MVC ビューまたは Razor Pages を含むアプリケーションを構築するときに必要な MVC 構成のサポートを追加するように、Razor SDK を構成します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-181">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="af8c8-182">このプロパティは、Web SDK をターゲットとする .NET Core 3.0 以降のプロジェクトには暗黙的に設定されます</span><span class="sxs-lookup"><span data-stu-id="af8c8-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="af8c8-183">ターゲットにする Razor 言語のバージョン。</span><span class="sxs-lookup"><span data-stu-id="af8c8-183">The version of the Razor Language to target.</span></span> |

<span data-ttu-id="af8c8-184">プロパティの詳細については、「[MSBuild プロパティ](/visualstudio/msbuild/msbuild-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af8c8-184">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="af8c8-185">ターゲット</span><span class="sxs-lookup"><span data-stu-id="af8c8-185">Targets</span></span>

<span data-ttu-id="af8c8-186">Razor SDK では、次の 2 つの主要なターゲットが定義されています。</span><span class="sxs-lookup"><span data-stu-id="af8c8-186">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="af8c8-187">`RazorGenerate` &ndash; コードにより、`RazorGenerate` 項目要素から *.cs* ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-187">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="af8c8-188">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorGenerateDependsOn` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-188">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="af8c8-189">`RazorCompile` &ndash; 生成された *.cs* ファイルを、Razor アセンブリにコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="af8c8-189">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="af8c8-190">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorCompileDependsOn` を使用します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-190">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="af8c8-191">`RazorComponentGenerate` &ndash; コードにより、`RazorComponent` 項目要素のための *.cs* ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-191">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="af8c8-192">このターゲットの前または後に実行できる追加のターゲットを指定するには、`RazorComponentGenerateDependsOn` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-192">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="af8c8-193">Razor ビューの実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="af8c8-193">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="af8c8-194">既定では、Razor SDK は、実行時のコンパイルを実行するために必要な参照アセンブリを公開しません。</span><span class="sxs-lookup"><span data-stu-id="af8c8-194">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="af8c8-195">この結果、アプリケーション モデルが実行時のコンパイルに依存している場合には、コンパイルが失敗します。たとえば、アプリが公開後に埋め込まれたビューを使用したり、ビューを変更したりする場合などです。</span><span class="sxs-lookup"><span data-stu-id="af8c8-195">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="af8c8-196">`CopyRefAssembliesToPublishDirectory` を `true` に設定して、参照アセンブリの公開を続行します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-196">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="af8c8-197">Web アプリの場合は、アプリが `Microsoft.NET.Sdk.Web` SDK をターゲットにしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-197">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="af8c8-198">Razor 言語バージョン</span><span class="sxs-lookup"><span data-stu-id="af8c8-198">Razor language version</span></span>

<span data-ttu-id="af8c8-199">`Microsoft.NET.Sdk.Web` SDK をターゲットとする場合、Razor 言語バージョンは、アプリのターゲット フレームワークのバージョンから推論されます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-199">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="af8c8-200">`Microsoft.NET.Sdk.Razor` SDK をターゲットとするプロジェクトの場合や、推論される値とは異なる Razor 言語バージョンを必要とするまれなアプリの場合は、アプリのプロジェクト ファイルで `<RazorLangVersion>` プロパティを設定することで、特定のバージョンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="af8c8-200">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="af8c8-201">Razor の言語バージョンは、そのバージョンのビルド対象であったランタイムのバージョンと緊密に統合されています。</span><span class="sxs-lookup"><span data-stu-id="af8c8-201">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="af8c8-202">そのランタイムに向けて設計されたのではない言語バージョンをターゲットにすることはサポートされておらず、おそらくビルド エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="af8c8-202">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af8c8-203">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="af8c8-203">Additional resources</span></span>

* [<span data-ttu-id="af8c8-204">.NET Core の csproj 形式に追加されたもの</span><span class="sxs-lookup"><span data-stu-id="af8c8-204">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="af8c8-205">MSBuild プロジェクトの共通項目</span><span class="sxs-lookup"><span data-stu-id="af8c8-205">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
