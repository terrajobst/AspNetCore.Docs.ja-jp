---
title: ASP.NET Core の Razor SDK
author: Rick-Anderson
description: ASP.NET Core の Razor ページを使用して、ページのコーディングに重点を置いたシナリオをより簡略化して、MVC を使用する場合よりも生産性を高める方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: 1dc001c7c5fe320629835e06fe6db7fadabff94d
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985390"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="0f313-103">ASP.NET Core の Razor SDK</span><span class="sxs-lookup"><span data-stu-id="0f313-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="0f313-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0f313-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="0f313-105">概要</span><span class="sxs-lookup"><span data-stu-id="0f313-105">Overview</span></span>

<span data-ttu-id="0f313-106">[!INCLUDE[](~/includes/2.1-SDK.md)]が含まれています、 `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK)。</span><span class="sxs-lookup"><span data-stu-id="0f313-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="0f313-107">Razor SDK とは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0f313-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
* <span data-ttu-id="0f313-108">ASP.NET Core MVC ベース プロジェクト用の [Razor](xref:mvc/views/razor) ファイルを含むプロジェクトのビルド、パッケージ、および発行に関わる表示と操作を標準化できます。</span><span class="sxs-lookup"><span data-stu-id="0f313-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="0f313-109">Razor ファイルのコンパイルのカスタマイズを可能にする事前定義されたターゲット、プロパティ、項目のセットを含みます。</span><span class="sxs-lookup"><span data-stu-id="0f313-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <span data-ttu-id="0f313-110">ASP.NET Core MVC ベースのプロジェクトまたは[Blazor](xref:blazor/index)プロジェクトの[Razor](xref:mvc/views/razor)ファイルを含むプロジェクトをビルド、パッケージ、および発行するために必要です</span><span class="sxs-lookup"><span data-stu-id="0f313-110">is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects, or [Blazor](xref:blazor/index) projects</span></span>
* <span data-ttu-id="0f313-111">には、Razor (cshtml または razor) ファイルのコンパイルをカスタマイズするための定義済みのターゲット、プロパティ、および項目のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0f313-111">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (.cshtml or .razor) files.</span></span>
::: moniker-end


::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="0f313-112">Razor SDK には、 `<Content>` `Include`属性が`**\*.cshtml`グロビングパターンに設定された要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0f313-112">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="0f313-113">一致するファイルがパブリッシュされます。</span><span class="sxs-lookup"><span data-stu-id="0f313-113">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f313-114">Razor SDK には`<Content>` 、 `**\*.cshtml`属性`Include`がおよび`**\*.razor`グロビングパターンに設定された要素が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0f313-114">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="0f313-115">一致するファイルがパブリッシュされます。</span><span class="sxs-lookup"><span data-stu-id="0f313-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="0f313-116">前提条件</span><span class="sxs-lookup"><span data-stu-id="0f313-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="0f313-117">Razor SDK を使用する</span><span class="sxs-lookup"><span data-stu-id="0f313-117">Use the Razor SDK</span></span>

<span data-ttu-id="0f313-118">ほとんどの web アプリは、Razor の SDK を明示的に参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0f313-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
<span data-ttu-id="0f313-119">Razor SDK を使用して Razor ビューまたは Razor ページを含むクラス ライブラリを構築する方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0f313-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="0f313-120">`Microsoft.NET.Sdk.Razor` の代わりに `Microsoft.NET.Sdk` を使用します。</span><span class="sxs-lookup"><span data-stu-id="0f313-120">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="0f313-121">通常、パッケージ参照を`Microsoft.AspNetCore.Mvc`ビルドし、Razor ページと Razor ビュー コンパイルに必要な追加の依存関係を受信するが必要です。</span><span class="sxs-lookup"><span data-stu-id="0f313-121">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="0f313-122">少なくとも、プロジェクトへのパッケージ参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f313-122">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="0f313-123">`Microsoft.AspNetCore.Razor.Design`パッケージがプロジェクトに、Razor コンパイル タスクとターゲットを提供します。</span><span class="sxs-lookup"><span data-stu-id="0f313-123">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="0f313-124">前述のパッケージは、`Microsoft.AspNetCore.Mvc` に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="0f313-124">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="0f313-125">次のマークアップは、Razor ファイルの ASP.NET Core Razor ページ アプリをビルドする Razor SDK を使用するプロジェクト ファイルを示します。</span><span class="sxs-lookup"><span data-stu-id="0f313-125">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="0f313-126">Razor SDK を使用して Razor ビューまたは Razor Pages を含むクラスライブラリをビルドするには、Razor クラスライブラリプロジェクトテンプレートから始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0f313-126">To use the Razor SDK to build class libraries containing Razor views or Razor Pages we recommend starting with the Razor Class Library project template.</span></span> <span data-ttu-id="0f313-127">Blazor (razor) ファイルのビルドに使用される razor クラスライブラリには、少なくとも`Microsoft.AspNetCore.Components`パッケージへの参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="0f313-127">A Razor class library that is used to build Blazor (.razor) files will at minimum require a reference to the `Microsoft.AspNetCore.Components` package.</span></span> <span data-ttu-id="0f313-128">Razor ビューまたはページ (cshtml ファイル) のビルドに使用される razor クラスライブラリは、 `netcoreapp3.0` `FrameworkReference`を対象としたものであり`Microsoft.AspNetCore.App`、を持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f313-128">A Razor class library that is used to build Razor views or pages (.cshtml files), will require targeting `netcoreapp3.0` or newer and have a `FrameworkReference` to `Microsoft.AspNetCore.App`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="0f313-129">`Microsoft.AspNetCore.Razor.Design`と`Microsoft.AspNetCore.Mvc.Razor.Extensions`でパッケージが含まれている、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。</span><span class="sxs-lookup"><span data-stu-id="0f313-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="0f313-130">ただし、バージョンのない`Microsoft.AspNetCore.App`パッケージ参照は、の最新バージョンが含まれていないアプリへのメタパッケージ`Microsoft.AspNetCore.Razor.Design`します。</span><span class="sxs-lookup"><span data-stu-id="0f313-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="0f313-131">プロジェクトは、一貫したバージョンを参照する必要があります`Microsoft.AspNetCore.Razor.Design`(または`Microsoft.AspNetCore.Mvc`) Razor の最新のビルド時の修正プログラムが含まれるようにします。</span><span class="sxs-lookup"><span data-stu-id="0f313-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="0f313-132">詳細については、[この GitHub の問題](https://github.com/aspnet/Razor/issues/2553)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0f313-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="0f313-133">プロパティ</span><span class="sxs-lookup"><span data-stu-id="0f313-133">Properties</span></span>

<span data-ttu-id="0f313-134">Razor の SDK の動作は、プロジェクトをビルドする際に次のプロパティにより制御されます。</span><span class="sxs-lookup"><span data-stu-id="0f313-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="0f313-135">`RazorCompileOnBuild` &ndash; ときに`true`、コンパイルし、プロジェクトのビルドの一部として Razor アセンブリを生成します。</span><span class="sxs-lookup"><span data-stu-id="0f313-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="0f313-136">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="0f313-136">Defaults to `true`.</span></span>
* <span data-ttu-id="0f313-137">`RazorCompileOnPublish` &ndash; ときに`true`、コンパイルし、プロジェクトの発行の一部として Razor アセンブリを生成します。</span><span class="sxs-lookup"><span data-stu-id="0f313-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="0f313-138">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="0f313-138">Defaults to `true`.</span></span>

<span data-ttu-id="0f313-139">プロパティと、次の表の項目は、入力と剃刀 SDK への出力の構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="0f313-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"
> [!WARNING]
<span data-ttu-id="0f313-140">ASP.NET Core 3.0 以降では、または`RazorCompileOnBuild` `RazorCompileOnPublish`が無効になっている場合、MVC ビューまたは Razor Pages は既定では提供されません。</span><span class="sxs-lookup"><span data-stu-id="0f313-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages will not be served by default if `RazorCompileOnBuild` or `RazorCompileOnPublish` is disabled.</span></span> <span data-ttu-id="0f313-141">アプリケーションでは、ランタイムコンパイルを`Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`使用して cshtml ファイルを処理する場合に、ランタイムコンパイルのサポートを追加するために、パッケージへの明示的な参照を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0f313-141">Applications must add an explicit reference to `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package to add support for runtime compilation if they rely on runtime compilation to process cshtml files.</span></span>
::: moniker-end


| <span data-ttu-id="0f313-142">項目</span><span class="sxs-lookup"><span data-stu-id="0f313-142">Items</span></span> | <span data-ttu-id="0f313-143">説明</span><span class="sxs-lookup"><span data-stu-id="0f313-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="0f313-144">コード生成の入力である項目要素 (*cshtml*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="0f313-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="0f313-145">コンポーネントコードの生成に入力する項目要素 (*razor*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="0f313-145">Item elements (*.razor* files) that are inputs to Component code generation.</span></span>
| `RazorCompile` | <span data-ttu-id="0f313-146">Razor コンパイルターゲットへの入力である項目要素 ( *.cs*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="0f313-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="0f313-147">Razor アセンブリにコンパイルする追加ファイルを指定する場合に使用します。`ItemGroup`</span><span class="sxs-lookup"><span data-stu-id="0f313-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="0f313-148">Razor アセンブリ用の属性をコード生成するために使用する項目要素です。</span><span class="sxs-lookup"><span data-stu-id="0f313-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="0f313-149">例:</span><span class="sxs-lookup"><span data-stu-id="0f313-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="0f313-150">項目の要素が生成された Razor アセンブリに埋め込みリソースとして追加します。</span><span class="sxs-lookup"><span data-stu-id="0f313-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="0f313-151">プロパティ</span><span class="sxs-lookup"><span data-stu-id="0f313-151">Property</span></span> | <span data-ttu-id="0f313-152">説明</span><span class="sxs-lookup"><span data-stu-id="0f313-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="0f313-153">Razor によって生成されたアセンブリの (拡張子なしの) ファイル名です。</span><span class="sxs-lookup"><span data-stu-id="0f313-153">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="0f313-154">Razor の出力ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="0f313-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="0f313-155">Razor アセンブリをビルドするために使用するツールセットを決定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="0f313-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="0f313-156">有効な値は `Implicit`、`RazorSDK`、`PrecompilationTool` です。</span><span class="sxs-lookup"><span data-stu-id="0f313-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="0f313-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="0f313-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="0f313-158">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="0f313-158">Default is `true`.</span></span> <span data-ttu-id="0f313-159">の`true`場合、web.config ファイル、 *json*ファイル、および*cshtml*ファイルがプロジェクトのコンテンツとして含まれます。</span><span class="sxs-lookup"><span data-stu-id="0f313-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="0f313-160">使用して参照されている場合`Microsoft.NET.Sdk.Web`、ファイル*wwwroot*し、構成ファイルも含まれています。</span><span class="sxs-lookup"><span data-stu-id="0f313-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="0f313-161">`true` の場合、`RazorGenerate` 項目の `Content` 項目の *.cshtml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0f313-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="0f313-162">ときに`true`、生成、 *.cs*ファイルで指定された属性を格納している`RazorAssemblyAttribute`コンパイル出力ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0f313-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="0f313-163">`true` の場合、`RazorAssemblyAttribute` にアセンブリ属性の既定のセットが追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f313-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="0f313-164">ときに`true`、コピー`RazorGenerate`項目 ( *.cshtml*) ファイルを発行ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="0f313-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="0f313-165">通常、Razor ファイルは必要ありません発行されたアプリの場合、それらのビルド時または発行時のコンパイルに参加します。</span><span class="sxs-lookup"><span data-stu-id="0f313-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="0f313-166">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="0f313-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="0f313-167">`true` の場合、発行ディレクトリに参照アセンブリ項目がコピーされます。</span><span class="sxs-lookup"><span data-stu-id="0f313-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="0f313-168">通常、参照アセンブリは必要ありません発行されたアプリのビルド時または発行時に Razor コンパイルが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="0f313-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="0f313-169">設定`true`発行されたアプリケーションがランタイムのコンパイルを必要とする場合。</span><span class="sxs-lookup"><span data-stu-id="0f313-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="0f313-170">などの値を設定`true`アプリを変更する場合 *.cshtml*実行時にファイルや埋め込みのビューを使用します。</span><span class="sxs-lookup"><span data-stu-id="0f313-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="0f313-171">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="0f313-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="0f313-172">ときに`true`、すべての Razor コンテンツ アイテム ( *.cshtml*ファイル)、生成された NuGet パッケージに含める対象としてマークされました。</span><span class="sxs-lookup"><span data-stu-id="0f313-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="0f313-173">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="0f313-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="0f313-174">`true` の場合、生成された Razor アセンブリに、埋め込みファイルとして RazorGenerate ( *.cshtml*) 項目が追加されます。</span><span class="sxs-lookup"><span data-stu-id="0f313-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="0f313-175">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="0f313-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="0f313-176">`true` の場合、コードの生成作業をオフロードするために、永続的なビルド サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="0f313-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="0f313-177">既定値は、`UseSharedCompilation` の値です。</span><span class="sxs-lookup"><span data-stu-id="0f313-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="0f313-178">の`true`場合、SDK は、アプリケーションパーツの検出を実行するために、MVC によって実行時に使用される追加の属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="0f313-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |

<span data-ttu-id="0f313-179">プロパティの詳細については、「[MSBuild プロパティ](/visualstudio/msbuild/msbuild-properties)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0f313-179">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="0f313-180">ターゲット</span><span class="sxs-lookup"><span data-stu-id="0f313-180">Targets</span></span>

<span data-ttu-id="0f313-181">Razor SDK では、次の 2 つの主要なターゲットが定義されています。</span><span class="sxs-lookup"><span data-stu-id="0f313-181">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="0f313-182">`RazorGenerate` &ndash; コード生成 *.cs*ファイルから`RazorGenerate`項目要素。</span><span class="sxs-lookup"><span data-stu-id="0f313-182">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="0f313-183">このターゲットの前後に実行する追加のターゲットを指定するには、`RazorGenerateDependsOn` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="0f313-183">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="0f313-184">`RazorCompile` &ndash; 生成されたコンパイル *.cs* Razor アセンブリへのファイルします。</span><span class="sxs-lookup"><span data-stu-id="0f313-184">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="0f313-185">このターゲットの前後に実行する追加のターゲットを指定するには、`RazorCompileDependsOn` を使用します。</span><span class="sxs-lookup"><span data-stu-id="0f313-185">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="0f313-186">`RazorComponentGenerate`コードによって、項目`RazorComponent`要素の .cs ファイルが生成されます。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="0f313-186">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="0f313-187">このターゲットの前後に実行する追加のターゲットを指定するには、`RazorComponentGenerateDependsOn` プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="0f313-187">Use `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="0f313-188">Razor ビューの実行時のコンパイル</span><span class="sxs-lookup"><span data-stu-id="0f313-188">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="0f313-189">既定では、Razor SDK は、実行時のコンパイルを実行するために必要な参照アセンブリを公開しません。</span><span class="sxs-lookup"><span data-stu-id="0f313-189">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="0f313-190">この結果、アプリケーション モデルが実行時のコンパイルに依存している場合には、コンパイルが失敗します。たとえば、アプリが公開後に埋め込まれたビューを使用したり、ビューを変更したりする場合などです。</span><span class="sxs-lookup"><span data-stu-id="0f313-190">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="0f313-191">`CopyRefAssembliesToPublishDirectory` を `true` に設定して、参照アセンブリの公開を続行します。</span><span class="sxs-lookup"><span data-stu-id="0f313-191">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="0f313-192">Web アプリの場合、アプリが対象とすることを確認して、 `Microsoft.NET.Sdk.Web` SDK。</span><span class="sxs-lookup"><span data-stu-id="0f313-192">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="0f313-193">Razor 言語バージョン</span><span class="sxs-lookup"><span data-stu-id="0f313-193">Razor language version</span></span>

<span data-ttu-id="0f313-194">`Microsoft.NET.Sdk.Web` SDK を対象とする場合、Razor 言語バージョンは、アプリのターゲットフレームワークのバージョンから推測されます。</span><span class="sxs-lookup"><span data-stu-id="0f313-194">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="0f313-195">SDK を`Microsoft.NET.Sdk.Razor`対象とするプロジェクトや、アプリが推定値とは異なる Razor 言語バージョンを必要とするまれな場合は、アプリのプロジェクトファイルで`<RazorLangVersion>`プロパティを設定することにより、バージョンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="0f313-195">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="0f313-196">Razor の言語バージョンは、ビルドされたランタイムのバージョンと密接に統合されています。</span><span class="sxs-lookup"><span data-stu-id="0f313-196">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="0f313-197">ランタイム向けに設計されていない言語バージョンをターゲットにすることはサポートされていないため、ビルドエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0f313-197">Targeting a language version that is not designed for the runtime is unsupported, and will likely produce build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0f313-198">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0f313-198">Additional resources</span></span>

* [<span data-ttu-id="0f313-199">.NET Core の csproj 形式に追加されたもの</span><span class="sxs-lookup"><span data-stu-id="0f313-199">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="0f313-200">MSBuild プロジェクトの共通項目</span><span class="sxs-lookup"><span data-stu-id="0f313-200">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
