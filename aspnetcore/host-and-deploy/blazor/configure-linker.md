---
title: ASP.NET Core Blazor 用のリンカーを構成する
author: guardrex
description: Blazor アプリを構築するときに、中間言語 (IL) リンカーを制御する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218662"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="4b24a-103">ASP.NET Core Blazor 用のリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="4b24a-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="4b24a-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4b24a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="4b24a-105">Blazor WebAssembly では、ビルド中に[中間言語 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) のリンクが実行されて、アプリの出力アセンブリから不要な IL がトリミングされます。</span><span class="sxs-lookup"><span data-stu-id="4b24a-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="4b24a-106">デバッグ構成でビルドすると、リンカーは無効になります。</span><span class="sxs-lookup"><span data-stu-id="4b24a-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="4b24a-107">リンカーを有効にするには、アプリをリリース構成でビルドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b24a-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="4b24a-108">Blazor WebAssembly アプリを配置する場合は、リリースでビルドすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4b24a-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="4b24a-109">アプリをリンクするとサイズが最適化されますが、悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4b24a-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="4b24a-110">リフレクションや関連する動的機能を使用するアプリは、トリミングされたときに中断する可能性があります。リンカーがこの動的な動作を認識せず、通常は実行時にリフレクションに必要な型を特定できないためです。</span><span class="sxs-lookup"><span data-stu-id="4b24a-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="4b24a-111">そのようなアプリをトリミングするには、コードと、アプリが依存しているパッケージまたはフレームワークのリフレクションで必要なすべての型を、リンカーに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b24a-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="4b24a-112">トリミングされたアプリが配置後に正しく動作するには、開発中にアプリのリリース ビルドを頻繁にテストすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="4b24a-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="4b24a-113">Blazor アプリのリンクは、次の MSBuild 機能を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="4b24a-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="4b24a-114">[MSBuild プロパティ](#control-linking-with-an-msbuild-property)を使ってリンクをグローバルに構成する。</span><span class="sxs-lookup"><span data-stu-id="4b24a-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="4b24a-115">[構成ファイル](#control-linking-with-a-configuration-file)を使ってアセンブリごとにリンクを制御する。</span><span class="sxs-lookup"><span data-stu-id="4b24a-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="4b24a-116">MSBuild プロパティを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="4b24a-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="4b24a-117">リンクは、アプリが `Release` 構成でビルドされると有効になります。</span><span class="sxs-lookup"><span data-stu-id="4b24a-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="4b24a-118">これを変更するには、プロジェクト ファイルで `BlazorWebAssemblyEnableLinking` の MSBuild プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="4b24a-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="4b24a-119">構成ファイルを使ってリンクを制御する</span><span class="sxs-lookup"><span data-stu-id="4b24a-119">Control linking with a configuration file</span></span>

<span data-ttu-id="4b24a-120">XML の構成ファイルを用意してそのファイルをプロジェクト ファイル内で MSBuild 項目として指定することで、アセンブリごとにリンクを制御します。</span><span class="sxs-lookup"><span data-stu-id="4b24a-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="4b24a-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="4b24a-121">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="4b24a-122">詳細については、[Link xml ファイルの例 (mono/リンカー GitHub リポジトリ)](https://github.com/mono/linker#link-xml-file-examples) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4b24a-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="4b24a-123">XML リンカー構成ファイルをライブラリに追加する</span><span class="sxs-lookup"><span data-stu-id="4b24a-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="4b24a-124">特定のライブラリ用にリンカーを構成するには、XML リンカー構成ファイルを埋め込みリソースとしてライブラリに追加します。</span><span class="sxs-lookup"><span data-stu-id="4b24a-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="4b24a-125">埋め込みリソースの名前は、アセンブリと同じにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b24a-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="4b24a-126">次の例では、*LinkerConfig.xml* ファイルが、ライブラリのアセンブリと同じ名前を持つ埋め込みリソースとして指定されています。</span><span class="sxs-lookup"><span data-stu-id="4b24a-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="4b24a-127">国際化用にリンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="4b24a-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="4b24a-128">既定では、WebAssembly アプリに対する Blazor のリンカー構成により、明示的に要求されたロケールを除き、国際化情報が除去されます。</span><span class="sxs-lookup"><span data-stu-id="4b24a-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="4b24a-129">これらのアセンブリを削除すると、アプリのサイズが最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="4b24a-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="4b24a-130">保持される I18N アセンブリを制御するには、プロジェクト ファイルで MSBuild のプロパティ `<MonoLinkerI18NAssemblies>` を設定します。</span><span class="sxs-lookup"><span data-stu-id="4b24a-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="4b24a-131">リージョンの値</span><span class="sxs-lookup"><span data-stu-id="4b24a-131">Region Value</span></span>     | <span data-ttu-id="4b24a-132">Mono のリージョン アセンブリ</span><span class="sxs-lookup"><span data-stu-id="4b24a-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="4b24a-133">すべてのアセンブリが含まれます</span><span class="sxs-lookup"><span data-stu-id="4b24a-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="4b24a-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="4b24a-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="4b24a-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="4b24a-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="4b24a-136">`none` (既定値)</span><span class="sxs-lookup"><span data-stu-id="4b24a-136">`none` (default)</span></span> | <span data-ttu-id="4b24a-137">None</span><span class="sxs-lookup"><span data-stu-id="4b24a-137">None</span></span>                    |
| `other`          | <span data-ttu-id="4b24a-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="4b24a-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="4b24a-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="4b24a-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="4b24a-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="4b24a-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="4b24a-141">複数の値を区切るにはコンマを使用します (例: `mideast,west`)。</span><span class="sxs-lookup"><span data-stu-id="4b24a-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="4b24a-142">詳しくは、「[I18N: Pnetlib 国際化フレームワーク ライブラリ (mono/mono GitHub リポジトリ)](https://github.com/mono/mono/tree/master/mcs/class/I18N)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4b24a-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
